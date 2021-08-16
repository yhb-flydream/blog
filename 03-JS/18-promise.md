# promise

> **参考**
>
> [《Promise》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise)
>
> [《使用 Promise》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Guide/Using_promises)
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

[TOC]

```js
const PENDING = 'pending'
const FULFILLED = 'fulfilled'
const REJECTED = 'rejected'

class myPromise {
  constructor(executor) {
    this.status = PENDING
    this.value = undefined
    this.reason = undefined
    this.onResolvedCallbacks = []
    this.onRejectedCallbacks = []

    let resolve = (value) => {
      if (this.status === PENDING) {
        this.status = FULFILLED
        this.value = value
        this.onResolvedCallbacks.forEach((fn) => fn())
      }
    }

    let reject = (reason) => {
      if (this.status === PENDING) {
        this.status = REJECTED
        this.reason = reason
        this.onRejectedCallbacks.forEach((fn) => fn())
      }
    }

    try {
      executor(resolve, reject)
    } catch (error) {
      reject(error)
    }
  }

  then(onFulfilled, onRejected) {
    onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : (v) => v
    onRejected =
      typeof onRejected === 'function'
        ? onFulfilled
        : (err) => {
            throw err
          }
    let promise2 = new myPromise((resolve, reject) => {
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

const resolvePromise = (promise2, x, resolve, reject) => {
  if (promise2 === x) {
    return reject(new TypeError("Chaining cycle detected for promise #<Promise>"));)
  }
  let called
  if ((typeof x === 'object' && x != null) || typeof x === 'function') {
    try {
      let then = x.then
      if (typeof then === 'function') {
        then.call(x, (y) => {
          if (called) return
          called = true
          resolvePromise(promise2, y, resolve, reject)
        }, (r) => {
          if (called) return
          called = true
          reject(r)
        })
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

myPromise.resolve = value => {
  if (value instanceof myPromise) {
    return value
  }
  return new myPromise(resolve => resolve(value))
}

myPromise.reject = reason => {
  return new myPromise(resolve => reject(reason))
}

myPromise.all = function(promiseArr) {
  let index = 0, result = []
  return new myPromise((resolve, reject) => {
    promiseArr.forEach((p, i) => {
      myPromise.resolve(p).then(val => {
        index++
        result[i] = val
        if(index === promiseArr.length) {
          resolve(result)
        }
      }, err => {
        reject(err)
      })
    })
  })
}

myPromise.race = function(promiseArr) {
  return new myPromise((resolve, reject) => {
    promiseArr.forEach((p) => {
      myPromise.resolve(p).then(val => {
        resolve(val)
      }, err => {
        reject(err)
      })
    })
  })
}
```
