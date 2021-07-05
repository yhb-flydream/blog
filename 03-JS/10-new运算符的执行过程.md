# new 运算符的执行过程

> **参考**
>
> [【new 运算符】【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/new)
>
> [【new 一个对象的过程发生了什么】(作者：寻梦丶 2015)【来源：CSDN】](https://blog.csdn.net/w770583069/article/details/77322537)

```js
function objFactory() {
  var obj = Object.create(null)
  constructor = [].shift.call(arguments)
  obj.__proto__ = constructor.prototype
  var ret = constructor.apply(obj, arguments)
  return typeof ret === 'object' || typeof ret === 'function' ? ret : obj
}
```

1. 创建一个空对象 `{}`
2. 设置原型链，将 空对象 的 `__proto__` 成员指向了构造函数的 `prototype`
3. 将步骤 1 新创建的对象作为 this 的上下文
4. 如果构造函数 return 了一个新的“对象”，那么这个对象就会取代整个 new 出来的结果。如果构造函数没有 return 对象，那么就会返回步骤 1 所创建的对象，即隐式返回 this
