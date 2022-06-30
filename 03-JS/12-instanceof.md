<!--
 * @Author: yaohebin
 * @Date: 2021-02-01 17:00:29
 * @LastEditTime: 2022-06-30 16:55:04
 * @LastEditors: yaohebin
 * @Description: instanceof
-->
# instanceof

> **参考**
>
> [《instanceof》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/instanceof)
>
> [《JavaScript 深入之从 ECMAScript 规范解读 this》(作者：冴羽)【来源：掘金】](https://juejin.cn/post/6844903473872371725)

[TOC]

instanceof 运算符：用于检测构造函数的 prototype (constructor.prototype) 属性是否出现在某个实例对象(object)的原型链上。

## 语法

```text
object instanceof constructor
```

```js
function Car(make, model, year) {
  this.make = make
  this.model = model
  this.year = year
}
const auto = new Car('Honda', 'Accord', 1998)

console.log(auto instanceof Car)
// expected output: true

console.log(auto instanceof Object)
// expected output: true
```

## 实现 instanceof

```js
function myInstanceof(left, right) {
  let leftProto = Object.getPrototypeOf(left),
    prototypeObj = right.prototype
  while (true) {
    if (!leftProto) return false
    if (leftProto === prototypeObj) return true
    leftProto = Object.getPrototypeOf(leftProto)
  }
}
```
