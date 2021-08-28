# Symbol

> **参考**
>
> [《Symbol》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol)
>
> [《Symbol》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Glossary/Symbol)

[TOC]

symbol 是一种基本数据类型。

Symbol()函数会返回 symbol 类型的值，该类型具有静态属性和静态方法。

它的静态属性会暴露几个内建的成员对象；它的静态方法会暴露全局的 symbol 注册，且类似于内建对象类，但作为构造函数来说它并不完整，因为它不支持语法："new Symbol()"。

每个从 `Symbol()` 返回的 symbol 值都是唯一的。

一个 symbol 值能作为对象属性的标识符；这是该数据类型仅有的目的。

`Symbol([description])`

- description (可选)
  - 可选的，字符串类型。对 symbol 的描述，可用于调试但不是访问 symbol 本身。

注意，Symbol("foo") 不会强制将字符串 “foo” 转换成 symbol 类型。它每次都会创建一个新的 symbol 类型：

```js
var sym1 = Symbol('foo')
var sym2 = Symbol('foo')

sym1 === sym2 // false
```

**带有 new 运算符的语法将抛出 TypeError 错误。**

```js
var sym = new Symbol() // TypeError
```
