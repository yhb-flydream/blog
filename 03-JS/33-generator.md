<!--
 * @Author: yaohebin
 * @Date: 2022-06-15 08:55:45
 * @LastEditTime: 2022-07-09 18:19:56
 * @LastEditors: yaohebin
 * @Description: generator
-->

# generator

> **参考**
>
> [《function\*》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/function*)
>
> [《Generator》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Generator)

[TOC]

`function*` 这种声明方式 (function 关键字后跟一个星号）会定义一个生成器函数 (generator function)，它返回一个 `Generator` 对象。

```js
function* generator(i) {
  yield i;
  yield i + 10;
}

const gen = generator(10);

console.log(gen.next().value);
// expected output: 10

console.log(gen.next().value);
// expected output: 20
```

**生成器函数在执行时能暂停，后面又能从暂停处继续执行。**

调用一个生成器函数并不会马上执行它里面的语句，而是返回一个这个生成器的 迭代器 （ iterator ）对象。

当这个迭代器的 `next()` 方法被首次（后续）调用时，其内的语句会执行到第一个（后续）出现 `yield` 的位置为止，**`yield` 后紧跟迭代器要返回的值**。

或者如果用的是 `yield*`（多了个星号），则表示将执行权移交给另一个生成器函数（当前生成器暂停执行）。

```js
function* anotherGenerator(i) {
  yield i + 1;
  yield i + 2;
  yield i + 3;
}

function* generator(i) {
  yield i;
  yield* anotherGenerator(i); // 移交执行权
  yield i + 10;
}

var gen = generator(10);

console.log(gen.next().value); // 10
console.log(gen.next().value); // 11
console.log(gen.next().value); // 12
console.log(gen.next().value); // 13
console.log(gen.next().value); // 20
```

`next()` 方法返回一个对象，这个对象包含两个属性：

- value 表示本次 yield 表达式的返回值，当 done 的值为 true 时可以忽略该值。
- done 属性为布尔类型，表示生成器后续是否还有 yield 语句，即生成器函数是否已经执行完毕并返回

调用 `next()` 方法时，如果传入了参数，那么这个参数会传给上一条执行的 yield 语句左边的变量，例如下面例子中的 x ：

```js
function* gen() {
  yield 10;
  x = yield 'foo';
  yield x;
}

var gen_obj = gen();
console.log(gen_obj.next()); // 执行 yield 10，返回 10
console.log(gen_obj.next()); // 执行 yield 'foo'，返回 'foo'
console.log(gen_obj.next(100)); // 将 100 赋给上一条 yield 'foo' 的左值，即执行 x=100，返回 100
console.log(gen_obj.next()); // 执行完毕，value 为 undefined，done 为 true
```

```js
function* createIterator() {
  let first = yield 1;
  let second = yield first + 2; // 4 + 2  // first = 4 是 next(4) 将参数赋给上一条的
  yield second + 3; // 5 + 3
}

let iterator = createIterator();

console.log(iterator.next()); // "{ value: 1, done: false }"
console.log(iterator.next(4)); // "{ value: 6, done: false }"
console.log(iterator.next(5)); // "{ value: 8, done: false }"
console.log(iterator.next()); // "{ value: undefined, done: true }"
```

当在生成器函数中显式 return 时，会导致生成器立即变为完成状态，即调用 next() 方法返回的对象的 done 为 true。如果 return 后面跟了一个值，那么这个值会作为当前调用 next() 方法返回的 value 值。

```js
function* yieldAndReturn() {
  yield 'Y';
  return 'R'; //显式返回处，可以观察到 done 也立即变为了 true
  yield 'unreachable'; // 不会被执行了
}

var gen = yieldAndReturn();
console.log(gen.next()); // { value: "Y", done: false }
console.log(gen.next()); // { value: "R", done: true }
console.log(gen.next()); // { value: undefined, done: true }
```

**生成器函数不能当构造器使用。**

```js
function* f() {}
var obj = new f(); // throws "TypeError: f is not a constructor"
```
