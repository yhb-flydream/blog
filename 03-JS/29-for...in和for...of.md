# for...in 和 for...of

> **参考**
>
> [《for...in》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...in)
>
> [《for...of》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...of)
>
> [《for...of 与 for...in 的区别》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...of#for...of%E4%B8%8Efor...in%E7%9A%84%E5%8C%BA%E5%88%AB)

[TOC]

## for...in

> 以任意顺序遍历一个对象的除 `Symbol` 以外的**可枚举**属性，包括**继承的可枚举**属性。

- `for...in` 一般用来对遍历一个**非数组（Array）的对象**（如：`{a: 1, b: 2}`）
- 如果只考虑对象本身的属性进行遍历，不包括原型上的属性，可以使用 `getOwnPropertyNames()` 或 `hasOwnProperty()` 来过滤只遍历对象自身的属性
- 它最常用的地方应该是用于调试，可以更方便的去检查对象属性，在处理有`key-value`数据（比如属性用作“键”），需要检查其中的任何键是否为某值的情况时，推荐用 `for ... in`。

```js
var obj = { a: 1, b: 2, c: 3 }

for (var prop in obj) {
  console.log('obj.' + prop + ' = ' + obj[prop])
}

// Output:
// "obj.a = 1"
// "obj.b = 2"
// "obj.c = 3"

var triangle = { a: 1, b: 2, c: 3 }

function ColoredTriangle() {
  this.color = 'red'
}

ColoredTriangle.prototype = triangle

var obj = new ColoredTriangle()

for (var prop in obj) {
  if (obj.hasOwnProperty(prop)) {
    console.log(`obj.${prop} = ${obj[prop]}`)
  }
}

// Output:
// "obj.color = red"
```

## for...of

> 在**可迭代对象**（包括 Array，Map，Set，String，TypedArray，arguments 对象等等）上创建一个迭代循环，调用自定义迭代钩子，并为每个不同属性的值执行语句

```js
const array1 = ['a', 'b', 'c']

// 如果你不想修改语句块中的变量 , 也可以使用 const 而非 let
for (const element of array1) {
  console.log(element)
}

// expected output: "a"
// expected output: "b"
// expected output: "c"

/* string */
let iterable = 'boo'

for (let value of iterable) {
  console.log(value)
}
// "b"
// "o"
// "o"

/* Map */
let iterable = new Map([
  ['a', 1],
  ['b', 2],
  ['c', 3],
])

for (let entry of iterable) {
  console.log(entry)
}
// ["a", 1]
// ["b", 2]
// ["c", 3]

for (let [key, value] of iterable) {
  console.log(value)
}
// 1
// 2
// 3

/* arguments */
;(function () {
  for (let argument of arguments) {
    console.log(argument)
  }
})(1, 2, 3)

// 1
// 2
// 3

/* DOM 集合 */
//注意：这只能在实现了NodeList.prototype[Symbol.iterator]的平台上运行
let articleParagraphs = document.querySelectorAll('article > p')

for (let paragraph of articleParagraphs) {
  paragraph.classList.add('read')
}
```

### 关闭迭代器

对于 `for...of` 的循环，可以由 `break`, `throw continue` 或 `return` 终止。在这些情况下，迭代器关闭。

```js
function* foo() {
  yield 1
  yield 2
  yield 3
}

for (let o of foo()) {
  console.log(o)
  break // closes iterator, triggers return
}
```

### 迭代生成器

```js
function* fibonacci() {
  // 一个生成器函数
  let [prev, curr] = [0, 1]
  for (;;) {
    // while (true) {
    ;[prev, curr] = [curr, prev + curr]
    yield curr
  }
}

for (let n of fibonacci()) {
  console.log(n)
  // 当n大于1000时跳出循环
  if (n >= 1000) break
}
```

### 不要重用生成器

生成器不应该重用，即使 `for...of` 循环的提前终止，例如通过 `break` 关键字。在退出循环后，生成器关闭，并尝试再次迭代，不会产生任何进一步的结果。

```js
var gen = (function* () {
  yield 1
  yield 2
  yield 3
})()

for (let o of gen) {
  console.log(o)
  break //关闭生成器
}

//生成器不应该重用，以下没有意义！
for (let o of gen) {
  console.log(o)
}
```

### 迭代其他可迭代对象

迭代显式实现**可迭代**协议的对象：

```js
var iterable = {
  [Symbol.iterator] {
    return {
      i: 0,
      next() {
        if (this.i < 3) {
          return { value: this.i++, done: false }
        }
        return { value: undefined, done: true }
      },
    }
  },
}

for (var value of iterable) {
  console.log(value)
}
// 0
// 1
// 2
```

## `for...of` 与 `for...in` 的区别

无论是 for...in 还是 for...of 语句都是迭代一些东西。

它们之间的主要区别在于它们的迭代方式。

- for...in 语句**以任意顺序迭代对象的可枚举属性**。
- for...of 语句**遍历可迭代对象定义要迭代的数据**。
