# this

> **参考**
>
> [《this》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)
>
> [《JavaScript 深入之从 ECMAScript 规范解读 this》(作者：冴羽)【来源：掘金】](https://juejin.cn/post/6844903473872371725)

**在绝大多数情况下，函数的调用方式决定了 this 的值（运行时绑定）。**

this 不能在执行期间被赋值，并且在每次函数被调用时 this 的值也可能会不同。

ES5 引入了 bind 方法来设置函数的 this 值，而不用考虑函数如何被调用的。

ES2015 引入了箭头函数，箭头函数不提供自身的 this 绑定（this 的值将保持为闭合词法上下文的值）。

## 全局上下文

无论是否在严格模式下，在全局执行环境中（在任何函数体外部）this 都指向全局对象。

## 函数上下文

在函数内部，this 的值取决于函数被调用的方式。

## 类上下文

this 在 类 中的表现与在函数中类似，因为类本质上也是函数，但也有一些区别和注意事项。

在类的构造函数中，this 是一个常规对象。类中所有非静态的方法都会被添加到 this 的原型中：

```js
class Example {
  constructor() {
    const proto = Object.getPrototypeOf(this)
    console.log(Object.getOwnPropertyNames(proto))
  }
  first() {}
  second() {}
  static third() {}
}

new Example() // ['constructor', 'first', 'second']
```

> 注意：静态方法不是 this 的属性，它们只是类自身的属性。

## 派生类

不像基类的构造函数，派生类的构造函数没有初始的 this 绑定。在构造函数中调用 super() 会生成一个 this 绑定，并相当于执行如下代码，Base 为基类：

```js
this = new Base()
```

> 警告：在调用 super() 之前引用 this 会抛出错误。

派生类不能在调用 super() 之前返回，除非其构造函数返回的是一个对象，或者根本没有构造函数。

```js
class Base {}
class Good extends Base {}
class AlsoGood extends Base {
  constructor() {
    return { a: 5 }
  }
}
class Bad extends Base {
  constructor() {}
}

new Good()
new AlsoGood()
new Bad() // ReferenceError
```
