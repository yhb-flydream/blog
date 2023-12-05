# call&apply&bind

> **参考**
>
> [《Function.prototype.call()》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/call)
>
> [《Function.prototype.apply()》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/apply)
>
> [《JavaScript 深入之 call 和 apply 的模拟实现》(作者：冴羽)【来源：掘金】](https://juejin.cn/post/6844903476477034510)
>
> [《JavaScript 深入之 bind 的模拟实现》(作者：mqyqingfeng)【来源：github】](https://github.com/mqyqingfeng/Blog/issues/12)
>
> [《js 手动实现 bind 方法，超详细思路分析！》(作者：听风是风)【来源：博客园】](https://www.cnblogs.com/echolun/p/12178655.html)

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

原生调用方式：

```js
Object.prototype.toString.call(obj, arg1, arg2, ...)
```

目的是将 toString 方法里面的 this 指向，换成 obj

1. 获取 obj 或者 window
2. 获取参数
3. 获取 toString 方法，赋值给一个 临时方法
   - 可以通过 this 访问到，因为 toString.call 的 call 被 toString 调用，则 this 指向调用者，即 toString
4. 执行 临时方法，并传入参数
5. 删除 临时方法
6. 返回执行结果

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

function myCall() {
  // 解构传入的参数
  // context 第一个参数是：需要绑定的对象，如果传 null 则绑定 window
  // args 收集剩余参数
  let [context, ...args] = [...arguments]
  context = context || window
  // 声明一个 临时变量，变量用来复制 需要改变 this 指向的方法，然后把这个临时变量添加到 需要绑定的对象 上，随后再调用这个 临时变量 存放的方法时，需要改变 this 指向的方法的 this 的指向就变成了 需要绑定的对象
  // 因为调用 call 的方式是 fn.call() 所以在 call 方法里可以通过 this 访问到 需要改变 this 指向的方法
  let sym = Symbol()
  context[sym] = this
  // 执行这个 需要绑定的对象 上新添加的的方法，并把调用 call 时的参数传给这个新添加的的方法
  // 得出的结果就是 需要改变 this 指向的方法 改变 this 指向之后调用的结果
  let result = context[sym](...args)
  // 把 需要绑定的对象 上临时的方法删除，不污染原对象
  delete context[sym]
  // 最后返回结果
  return result
}
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

原生调用方式：

```js
Object.prototype.toString.apply(obj, args)
```

目的是将 toString 方法里面的 this 指向，换成 obj

1. 获取 obj 或者 window
2. 获取参数
3. 获取 toString 方法，赋值给一个 临时方法
   - 可以通过 this 访问到，因为 toString.call 的 call 被 toString 调用，则 this 指向调用者，即 toString
4. 执行 临时方法，并传入参数
5. 删除 临时方法
6. 返回执行结果

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
//   let result context[sym](args)
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

原生调用方式：

```js
const toStringFn = Object.prototype.toString.bing(obj, args)
```

目的是将 toString 方法里面的 this 指向，换成 obj，并返回一个可执行函数

1. 获取 obj 或者 window
2. 获取参数
3. 获取 toString 方法的 this
4. 声明一个新函数
5. 收集新函数的参数
6. 新函数使用 apply 函数修正自己的 this 指向
   - bind 返回的函数即可以直接调用，又可以当做构造函数使用
   - 当直接调用的时候，新函数的 this 需指向 obj 或者 window
   - 当被用作构造函数使用的时候，新函数的 this 需指向自身的 this
7. 修改 新函数的 prototype 指向 obj 或者 window 的 prototype，用以判断执行是以什么方式执行返回的新函数
8. 在新函数中使用 apply 函数执行，传入修正后的 this，并传入参数
9. 返回新函数的执行结果
10. 返回新函数

```js
Function.prototype.bind2 = function (context) {
  if (typeof this !== 'function') {
    throw new Error('Function.prototype.bind - what is trying to be bound is not callable')
  }

  var self = this
  var args = Array.prototype.slice.call(arguments, 1)

  var fNOP = function () {}

  var fBound = function () {
    var bindArgs = Array.prototype.slice.call(arguments)
    // 当作为构造函数时，this 指向实例，self 指向绑定函数，因为下面一句 `fBound.prototype = this.prototype;`，已经修改了 fBound.prototype 为 绑定函数的 prototype，此时结果为 true，当结果为 true 的时候，this 指向实例。
    // 当作为普通函数时，this 指向 window，self 指向绑定函数，此时结果为 false，当结果为 false 的时候，this 指向绑定的 context。
    return self.apply(this.constructor === fNOP ? this : context, args.concat(bindArgs))
  }
  // 修改返回函数的 prototype 为绑定函数的 prototype，实例就可以继承函数的原型中的值
  // 使用一个空函数进行中转
  if (this.prototype) {
    fNOP.prototype = this.prototype
  }
  fBound.prototype = new fNOP()
  return fBound
}

// function myBind() {
//   let fn = this
//   let [context, ...args] = arguments
//   let fnFlag = function () {}
//   let newFn = function () {
//     return fn.apply(this instanceof fnFlag ? this : context, [...args, ...arguments])
//   }
//   if (fn.prototype) {
//     fnFlag.prototype = fn.prototype
//   }
//   newFn.prototype = new fnFlag()
//   return newFn
// }
```
