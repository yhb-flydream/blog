# call&apply&bind

> **参考**
>
> [《Function.prototype.call()》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
>
> [《Function.prototype.apply()》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
>
> [《JavaScript 深入之 call 和 apply 的模拟实现》(作者：冴羽)【来源：掘金】](https://juejin.cn/post/6844903476477034510)
>
> [《JavaScript 深入之 bind 的模拟实现》(作者：冴羽)【来源：掘金】](https://juejin.cn/post/6844903476623835149)

[TOC]

## call

使用一个指定的 this 值和单独给出的一个或多个参数来调用一个函数。

### call 语法

```js
function.call(thisArg, arg1, arg2, ...)
```

- thisArg
  - 可选的。
  - 在 function 函数运行时使用的 this 值。
  - 请注意，this 可能不是该方法看到的实际值：如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动替换为指向全局对象，原始值会被包装。
- arg1, arg2, ...
  - 指定的参数列表。

使用调用者提供的 this 值和参数调用该函数的返回值。若该方法没有返回值，则返回 undefined。

### 实现 call

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
//   let sym = Symbol()
//   context[sym] = this
//   let result = context[sym](...args)
//   delete context[sym]
//   return result
// }
```

## apply

调用一个具有给定 this 值的函数，以及以一个数组（或类数组对象）的形式提供的参数。

### apply 语法

```js
function.apply(thisArg, [argsArray])
```

- thisArg
  - 可选的。
  - 在 function 函数运行时使用的 this 值。
  - 请注意，this 可能不是该方法看到的实际值：如果这个函数处于非严格模式下，则指定为 null 或 undefined 时会自动替换为指向全局对象，原始值会被包装。
- argsArray
  - 可选的。
  - 一个数组或者类数组对象，其中的数组元素将作为单独的参数传给 function 函数。
  - 如果该参数的值为 null 或 undefined，则表示不需要传入任何参数。
  - 从 ECMAScript 5 开始可以使用类数组对象。

使用调用者提供的 this 值和参数调用该函数的返回值。若该方法没有返回值，则返回 undefined。

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
// function myApply(context) {
//   let [context, args = []] = arguments
//   context = context || window
//   let sym = Symbol()
//   context[sym] = this
//   let result constext[sym](args)
//   delete context[sym]
//   return result
// }
```

## bind

bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数，供调用时使用。

### bind 语法

```js
function.bind(thisArg[, arg1[, arg2[, ...]]])
```

- thisArg
  - 调用绑定函数时作为 this 参数传递给目标函数的值。
  - 如果使用 new 运算符构造绑定函数，则忽略该值。
  - 当使用 bind 在 setTimeout 中创建一个函数（作为回调提供）时，作为 thisArg 传递的任何原始值都将转换为 object。
  - 如果 bind 函数的参数列表为空，或者 thisArg 是 null 或 undefined，执行作用域的 this 将被视为新函数的 thisArg。
- arg1, arg2, ...
  - 当目标函数被调用时，被预置入绑定函数的参数列表中的参数。

返回一个原函数的拷贝，并拥有指定的 this 值和初始参数。

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

// function myBind() {
//   let fn = this
//   let [context, ...args] = arguments
//   let newFn = function () {
//     return fn.apply(this instanceof fn ? this : context, [...args, ...arguments])
//   }
//   newFn.prototype = fn.prototype
//   return newFn
// }
```
