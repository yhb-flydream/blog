# new 运算符的执行过程

> **参考**
>
> [《new 运算符》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)
>
> [《new 一个对象的过程发生了什么》(作者：寻梦丶 2015)【来源：CSDN】](https://blog.csdn.net/w770583069/article/details/77322537)
>
> [《JavaScript 中的 new 操作符的原理解析》(作者：码飞\_CC)【来源：CSDN】](https://blog.csdn.net/w770583069/article/details/77322537)

[TOC]

new 运算符：**创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。**

```js
function objFactory() {
  var obj = Object.create(null)
  constructor = [].shift.call(arguments)[0]
  obj.__proto__ = constructor.prototype
  var ret = constructor.apply(obj, [].shift.call(arguments).splice(1))
  return typeof ret === 'object' || typeof ret === 'function' ? ret : obj
}
```

1. 创建一个空对象 `{}`
2. 设置原型链，将 空对象 的 `__proto__` 成员指向了构造函数的 `prototype`
3. 将 空对象 作为 构造函数的 的上下文调用
4. 如果构造函数 return 了一个新的“对象”，那么这个对象就会取代整个 new 出来的结果。如果构造函数没有 return 对象，那么就会返回步骤 1 所创建的对象，即隐式返回 this

## 实现 new 操作符

```js
function myNew(constrc, ...args) {
  // 1,2 创建一个对象obj，将obj的[[prototype]]属性指向构造函数的原型对象
  // 即实现：obj.__proto__ === constructor.prototype
  const obj = Object.create(constrc.prototype)
  // 3.将constrc内部的this（即执行上下文）指向obj，并执行
  const result = constrc.apply(obj, args)
  // 4. 如果构造函数返回的是对象，则使用构造函数执行的结果。否则，返回新创建的对象
  return result instanceof Object ? result : obj
}
```

```js
// 使用的例子：
function Person(name, age) {
  this.name = name
  this.age = age
}
const person1 = myNew(Person, 'Tom', 20)
console.log(person1) // Person {name: "Tom", age: 20}
```
