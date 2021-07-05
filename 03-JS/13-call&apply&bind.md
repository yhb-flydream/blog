# call&apply&bind

> **参考**
>
> [【JavaScript 深入之 call 和 apply 的模拟实现】(作者：冴羽)【来源：掘金】](https://juejin.cn/post/6844903476477034510)
>
> [【JavaScript 深入之 bind 的模拟实现】(作者：冴羽)【来源：掘金】](https://juejin.cn/post/6844903476623835149)

[TOC]

## call

```js
Function.prototype.call2 = function (context) {
  context = context || window
  context.fn = this

  var args = []
  for (var i = 1; i < arguments.length; i++) {
    args.push('arguments[' + i + ']')
  }

  var result = eval('context.fn(' + args + ')')

  delete context.fn
  return result
}
// function myCall(context) {
//   let [context = window, ...args] = [...arguments]
//   context = context || window
//   let sym = new Symbol()
//   context[sym] = this
//   let result = context[sym](...args)
//   delete context[sym]
//   return result
// }
```

## apply

```js
Function.prototype.apply2 = function (context, arr) {
  context = Object(context) || window
  context.fn = this

  var result
  if (!arr) {
    result = context.fn()
  } else {
    var args = []
    for (var i = 0, len = arr.length; i < len; i++) {
      args.push('arr[' + i + ']')
    }
    result = eval('context.fn(' + args + ')')
  }
  delete context.fn
  return result
}
function myApply(context) {
  let [context, args = []] = arguments
  context = context || window
  let sym = new Symbol()
  context[sym] = this
  let result constext[sym](args)
  delete context[sym]
  return result
}
```

## bind

> **bind() 方法会创建一个新函数。**
> 当这个新函数被调用时，bind() 的第一个参数将作为它运行时的 this，之后的一序列参数将会在传递的实参前传入作为它的参数。

```js
Function.prototype.bind2 = function (context) {
  var self = this
  var args = Array.prototype.slice.call(arguments, 1)

  return function () {
    var bindArgs = Array.prototype.slice.call(arguments)
    self.apply(context, args.concat(bindArgs))
  }
}
```

```js
Function.prototype.bind2 = function (context) {
  var self = this
  var args = Array.prototype.slice.call(arguments, 1)

  var fbound = function () {
    var bindArgs = Array.prototype.slice.call(arguments)
    // 当作为构造函数时，this 指向实例，self 指向绑定函数，因为下面一句 `fbound.prototype = this.prototype;`，已经修改了 fbound.prototype 为 绑定函数的 prototype，此时结果为 true，当结果为 true 的时候，this 指向实例。
    // 当作为普通函数时，this 指向 window，self 指向绑定函数，此时结果为 false，当结果为 false 的时候，this 指向绑定的 context。
    return self.apply(this instanceof self ? this : context, args.concat(bindArgs))
  }
  // 修改返回函数的 prototype 为绑定函数的 prototype，实例就可以继承函数的原型中的值
  fbound.prototype = this.prototype
  // 使用一个空函数进行中转
  // var fNOP = function () {};
  // fNOP.prototype = this.prototype
  // fbound.prototype = new fNOP()
  return fbound
}

// function myBind(context) {
//   let self = this
//   let [context, ...args] = arguments
//   let fn = function () {
//     let args2 = [...arguments]
//     return self.apply(this instanceof self ? this : context, [...args, ...args2])
//   }
//   fn.prototype = this.prototype
//   return fn
// }
function myBind(context) {
  let fn = this
  let [context, ...args] = arguments
  let newFn = function () {
    let args2 = [...arguments]
    return fn.apply(this instanceof fn ? this : context, [...args, ...args2])
  }
  return newFn
}
```
