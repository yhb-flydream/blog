# Promise

> **参考**
>
> [《Promise》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)
>
> [《使用 Promise》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises)
>
> [《【翻译】Promises/A+规范》(作者：于明昊)【来源：ituring】](https://www.ituring.com.cn/article/66566)
>
> [《JavaScript Promise 迷你书（中文版）》(作者：azu / 翻译：liubin、kaku、honnkyou)【来源：liubin.org】](http://liubin.org/promises-book/)
>
> [《JavaScript Promise：去而复返》(作者：司徒正美)【来源：博客园】](https://www.cnblogs.com/rubylouvre/p/3495286.html)
>
> [《了解 JavaScript Promise》(作者：nodejs.cn)【来源：nodejs.cn】](http://nodejs.cn/learn/understanding-javascript-promises)
>
> [《Promise 对象》(作者：阮一峰)【来源：JavaScript 标准参考教程（alpha）】](https://javascript.ruanyifeng.com/advanced/promise.html)
>
> [《Promise 对象》(作者：阮一峰)【来源：ECMAScript 6 入门】](https://es6.ruanyifeng.com/#docs/promise)
>
> [《Promise》(作者：Ilya Kantor)【来源：现代 JavaScript 教程】](https://zh.javascript.info/promise-basics)
>
> [《理解 JavaScript Promise》(作者：苍微 q)【来源：知乎】](https://zhuanlan.zhihu.com/p/26523836)
>
> [《从一道让我失眠的 Promise 面试题开始，深入分析 Promise 实现细节》(作者：ITEM)【来源：掘金】](https://juejin.cn/post/6945319439772434469)
>
> [《深度揭秘 Promise 微任务注册和执行过程》(作者：程序 me)【来源：掘金】](https://juejin.cn/post/6844903987183894535)
>
> [《史上最易读懂的 Promise/A+ 完全实现》(作者：谢然)【来源：知乎】](https://zhuanlan.zhihu.com/p/21834559)
>
> [《死磕 36 个 JS 手写题（搞懂后，提升真的大）》(作者：布兰)【来源：大海我来了】](https://mp.weixin.qq.com/s/sDZudDS2jn8PZrSAtkicTg)
>
> [《深入 Promise(一)——Promise 实现详解》(作者：nswbmw)【来源：知乎】](https://zhuanlan.zhihu.com/p/25178630)
>
> [《Promise 的队列与 setTimeout 的队列有何关联？》【来源：知乎】](https://www.zhihu.com/question/36972010)
>
> [《手写一个 Promise/A+,完美通过官方 872 个测试用例》(作者：\_蒋鹏飞)【来源：CSDN】](https://blog.csdn.net/dennis_jiang/article/details/105389519)
>
> [《按照 Promise/A+ 规范逐行注释并实现 Promise》(作者：Asheng)【来源：思否】](https://segmentfault.com/a/1190000041640722)
>
> [《看了就会，手写 Promise 原理，最通俗易懂的版本！！！》(作者：Sunshine_Lin)【来源：掘金】](https://juejin.cn/post/6994594642280857630)

[TOC]

```js
// 定义状态值
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

class myPromise {
  constructor(executor) {
    // 初始化数据
    this.status = PENDING
    this.value = undefined
    this.reason = undefined
    this.onResolvedCallbacks = []
    this.onRejectedCallbacks = []

    // 定义 resolve 函数
    let resolve = value => {
      if (this.status === PENDING) {
        this.status = FULFILLED
        this.value = value
        this.onResolvedCallbacks.forEach(fn => fn())
      }
    }

    // 定义 reject 函数
    let reject = reason => {
      if (this.status === PENDING) {
        this.status = REJECTED
        this.reason = reason
        this.onRejectedCallbacks.forEach(fn => fn())
      }
    }

    // 初始化调用传入的参数
    try {
      executor(resolve, reject)
    } catch (error) {
      reject(error)
    }
  }

  // 定义 then 方法
  then(onFulfilled, onRejected) {
    // 对 then 方法传入的参数做判断兼容
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v
    onRejected =
      typeof onRejected === 'function'
        ? onRejected
        : err => {
            throw err
          }

    // then 函数调用之后会返回一个新的 promise
    let promise2 = new myPromise((resolve, reject) => {
      // 判断 then 之前调用的 promise 的状态
      // 根据 resolve 或 reject 状态执行执行对应逻辑
      if (this.status === FULFILLED) {
        setTimeout(() => {
          try {
            let x = onFulfilled(this.value)
            resolvePromise(promise2, x, resolve, reject)
          } catch (e) {
            reject(e)
          }
        }, 0)
      }

      if (this.status === REJECTED) {
        setTimeout(() => {
          try {
            let x = onRejected(this.reason)
            resolvePromise(promise2, x, resolve, reject)
          } catch (e) {
            reject(e)
          }
        }, 0)
      }

      // 如果还是 pending 状态，则把任务先添加到 resolve 和 reject 处理队列中
      if (this.status === PENDING) {
        this.onResolvedCallbacks.push(() => {
          setTimeout(() => {
            try {
              let x = onFulfilled(this.value)
              resolvePromise(promise2, x, resolve, reject)
            } catch (e) {
              reject(e)
            }
          }, 0)
        })

        this.onRejectedCallbacks.push(() => {
          setTimeout(() => {
            try {
              let x = onRejected(this.reason)
              resolvePromise(promise2, x, resolve, reject)
            } catch (e) {
              reject(e)
            }
          }, 0)
        })
      }
    })

    return promise2
  }
}

// 根据 then 参数函数执行的结果，判断时候需要进行递归处理
const resolvePromise = (promise2, x, resolve, reject) => {
  // 什么时候 then 参数函数执行的结果 x === promise2 ？
  if (promise2 === x) {
    return reject(new TypeError('Chaining cycle detected for promise #<Promise>'))
  }
  // 加锁，避免重复调用
  let called
  // 如果 then 参数函数执行的结果 x 是 object 或是 function，如果不是，直接调用 resolve 处理
  if ((typeof x === 'object' && x != null) || typeof x === 'function') {
    try {
      // 判断 是否带有 then 方法
      let then = x.then
      // 如果有 then 方法，继续递归调用
      if (typeof then === 'function') {
        then.call(
          x,
          y => {
            if (called) return
            called = true
            resolvePromise(promise2, y, resolve, reject)
          },
          e => {
            if (called) return
            called = true
            reject(e)
          }
        )
      } else {
        resolve(x)
      }
    } catch (e) {
      if (called) return
      called = true
      reject(e)
    }
  } else {
    resolve(x)
  }
}

// Promise 的 resolve 方法
// 如果参数是 Promise 的实例，直接返回
// 如果参数是 object 或 function 则返回 new Promise 的调用，并在 Promise 里执行 then 方法
// 如果参数是普通值，直接返回 new Promise 的调用
myPromise.resolve = value => {
  if (value instanceof myPromise) {
    return value
  }
  if (typeof value === 'object' && value !== null && typeof value.then === 'function') {
    return new myPromise((resolve, reject) => {
      value.then.call(
        value,
        v => resolve(v),
        e => reject(e)
      )
    })
  }
  return new myPromise(resolve => resolve(value))
}

// Promise 的 reject 方法
// 直接返回 new Promise 的调用
myPromise.reject = reason => {
  return new myPromise((resolve, reject) => reject(reason))
}

// Promise 的 all 方法
// 返回所有 Promise，参数 promises 的执行结果可以通过返回值的 then 拿到
myPromise.all = function (promiseArr) {
  let index = 0,
    result = []
  return new myPromise((resolve, reject) => {
    promiseArr.forEach((p, i) => {
      myPromise.resolve(p).then(
        val => {
          index++
          result[i] = val
          if (index === promiseArr.length) {
            resolve(result)
          }
        },
        err => {
          reject(err)
        }
      )
    })
  })
}

// Promise 的 race 方法
// 参数 promises 中，有一个 resolve 即返回
myPromise.race = function (promiseArr) {
  return new myPromise((resolve, reject) => {
    promiseArr.forEach(p => {
      myPromise.resolve(p).then(
        val => {
          resolve(val)
        },
        err => {
          reject(err)
        }
      )
    })
  })
}

myPromise.catch = onRejected => {
  return this.then(null, onRejected)
}
```

```js
function Promise(executor) {
  const self = this

  self.status = 'pending'
  self.onResolvedCallback = []
  self.onRejectedCallback = []

  function resolve(value) {
    if (value instanceof Promise) {
      return value.then(resolve, reject)
    }

    setTimeout(function () {
      if (self.status === 'pending') {
        self.status === 'resolved'
        self.data = value
        for (let i = 0; i < self.onResolvedCallback.length; i++) {
          self.onResolvedCallback[i](value)
        }
      }
    })
  }

  function reject(reason) {
    setTimeout(function () {
      if (self.status === 'pending') {
        self.status = 'rejected'
        self.data = reason
        for (let i = 0; i < self.onRejectedCallback.length; i++) {
          self.onRejectedCallback[i](reason)
        }
      }
    })
  }

  try {
    executor(resolve, reject)
  } catch (reason) {
    reject(reason)
  }
}

Promise.prototype.then = function (onResolved, onRejected) {
  const self = this
  let promise2

  onResolved =
    typeof onResolved === 'function'
      ? onResolved
      : function (v) {
          return v
        }
  onRejected =
    typeof onRejected === 'function'
      ? onRejected
      : function (err) {
          throw err
        }

  if (self.status === 'resolved') {
    return (promise2 = new Promise(function (resolve, reject) {
      setTimeout(function () {
        try {
          let x = onResolved(self.data)
          resolvePromise(promise2, x, resolve, reject)
        } catch (reason) {
          reject(reason)
        }
      })
    }))
  }

  if (self.status === 'rejected') {
    return (promise2 = new Promise(function (resolve, reject) {
      setTimeout(function () {
        try {
          let x = onRejected(self.data)
          resolvePromise(promise2, x, resolve, reject)
        } catch (reason) {
          reject(reason)
        }
      })
    }))
  }

  if (self.status === 'pending') {
    return (promise2 = new Promise(function (resolve, reject) {
      self.onResolvedCallback.push(function (value) {
        try {
          let x = onResolved(value)
          resolvePromise(promise2, x, resolve, reject)
        } catch (reason) {
          reject(reason)
        }
      })

      self.onRejectedCallback.push(function (reason) {
        try {
          let x = onRejected(reason)
          resolvePromise(promise2, x, resolve, reject)
        } catch (reason) {
          reject(reason)
        }
      })
    }))
  }

  // return (promise2 = new Promise(function (resolve, reject) {
  //   if (self.status === 'resolved') {
  //     setTimeout(function () {
  //       try {
  //         let x = onResolved(self.data)
  //         resolvePromise(promise2, x, resolve, reject)
  //       } catch (reason) {
  //         reject(reason)
  //       }
  //     })
  //   }
  //   if (self.status === 'rejected') {
  //     setTimeout(function () {
  //       try {
  //         let x = onRejected(self.data)
  //         resolvePromise(promise2, x, resolve, reject)
  //       } catch (reason) {
  //         reject(reason)
  //       }
  //     })
  //   }
  //   if (self.status === 'pending') {
  //     self.onResolvedCallback.push(function (value) {
  //       try {
  //         let x = onResolved(value)
  //         resolvePromise(promise2, x, resolve, reject)
  //       } catch (reason) {
  //         reject(reason)
  //       }
  //     })
  //     self.onRejectedCallback.push(function (value) {
  //       try {
  //         let x = onRejected(value)
  //         resolvePromise(promise2, x, resolve, reject)
  //       } catch (reason) {
  //         reject(reason)
  //       }
  //     })
  //   }
  // }))
}

function resolvePromise(promise, x, resolve, reject) {
  let then
  let thenCalledOrThrow = false

  if (promise2 === x) {
    return reject(new TypeError('Chaining cycle detected for promise!'))
  }

  if (x instanceof Promise) {
    if (x.status === 'pending') {
      x.then(function (v) {
        resolvePromise(promise2, x, resolve, reject)
      }, reject)
    } else {
      x.then(resolve, reject)
    }
    return
  }

  if (x !== null && (typeof x === 'object' || typeof x === 'function')) {
    try {
      then = x.then
      if (typeof then === 'function') {
        then.call(
          x,
          function rs(y) {
            if (thenCalledOrThrow) return
            thenCalledOrThrow = true
            return resolvePromise(promise2, y, resolve, reject)
          },
          function rj(r) {
            if (thenCalledOrThrow) return
            thenCalledOrThrow = true
            return reject(r)
          }
        )
      } else {
        resolve(x)
      }
    } catch (e) {
      if (thenCalledOrThrow) return
      thenCalledOrThrow = true
      return reject(e)
    }
  } else {
    resolve(x)
  }
}

Promise.prototype.catch = function (onRejected) {
  return this.then(null, onRejected)
}

Promise.deferred = Promise.defer = function () {
  let dfd = {}
  dfd.promise = new Promise(function (resolve, reject) {
    dfd.resolve = resolve
    dfd.reject = reject
  })
  return dfd
}
```
