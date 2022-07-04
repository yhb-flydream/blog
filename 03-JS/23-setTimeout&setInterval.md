<!--
 * @Author: yaohebin
 * @Date: 2021-02-01 17:10:45
 * @LastEditTime: 2022-07-04 20:36:44
 * @LastEditors: yaohebin
 * @Description: setTimeout&setInterval
-->

# setTimeout&setInterval

> **参考**
>
> [《setInterval()》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/setInterval)
>
> [《window.setTimeout》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/setTimeout)
>
> [《关于"this"的问题》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/setTimeout#%E5%85%B3%E4%BA%8Ethis%E7%9A%84%E9%97%AE%E9%A2%98)
>
> [《实际延时比设定值更久的原因：最小延迟时间》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/setTimeout#%E5%AE%9E%E9%99%85%E5%BB%B6%E6%97%B6%E6%AF%94%E8%AE%BE%E5%AE%9A%E5%80%BC%E6%9B%B4%E4%B9%85%E7%9A%84%E5%8E%9F%E5%9B%A0%EF%BC%9A%E6%9C%80%E5%B0%8F%E5%BB%B6%E8%BF%9F%E6%97%B6%E9%97%B4)
>
> [《详解 setTimeout 实现机制与原理，手写一个实现》(作者：前端瓶子君)【来源：程序员客栈】](https://jishuin.proginn.com/p/763bfbd5438e)
>
> [《4 种方案详解如何实现准时的 setTimeout》(作者：前端开发博客)【来源：CSDN】](https://blog.csdn.net/lgno2/article/details/116574487)
>
> [《阿里前端面试题：requestAnimationFrame 实现类似 setInterval 的计时器》(作者：Nnnina)【来源：思否】](https://segmentfault.com/q/1010000013909430)
>
> [《[Javascript] 实现 setInterval 函数》(作者：seasideX)【来源：CSDN】](https://blog.csdn.net/seasidexin/article/details/109100809)
>
> [《Symbol.for()》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Symbol/for)

[TOC]

## setTimeout

Window 和 Worker 接口提供的 `setTimeout()` 方法设置一个定时器，该定时器在定时器到期后执行一个函数或指定的一段代码。

它返回一个 `timeoutID` 正整数，表示定时器的编号。这个值可以传递给 clearTimeout() 来取消该定时器。

**语法：**

```js
var timeoutID = setTimeout(function[, delay, arg1, arg2, ...]);
var timeoutID = setTimeout(function[, delay]);
var timeoutID = setTimeout(code[, delay]);
```

**参数：**

- function
  - 在到期时间 (delay 毫秒) 之后调用的函数。
- code
  - 这个语法是可选的，你可以传递一个字符串来代替一个函数对象，你传递的字符串会被编译然后经过 delay 毫秒执行。这个语法因为与 `eval()` 存在相同的安全风险所以*不推荐*使用。
- delay
  - 延迟的毫秒数 (一秒等于 1000 毫秒)，函数的调用会在该延迟之后发生。如果省略该参数，delay 取默认值 0，意味着“马上”执行，或者尽快执行。不管是哪种情况，实际的延迟时间可能会比期待的 (delay 毫秒数) 值长。
- arg1, ..., argN
  - 当定时器过期的时候，将被传递给 `function` 函数的附加参数。

**返回值：**

返回值 timeoutID 是一个非零数值，用来标识通过 setTimeout() 创建的定时器，这个值可以用来作为 clearTimeout() 的参数来清除对应的定时器。

值得注意的是，**`setInterval()` 和 `setTimeout()` 共享同一个 ID 池，并且 clearInterval() 和 clearTimeout() 在技术上是可互换使用的**。但是，我们应该匹配使用 `clearInterval()` 和 `clearTimeout()`，以避免代码杂乱无章，并增强代码的可维护性。

> 备注：参数 delay 会被转换成一个有符号 32 位整数。这将 delay 限制到了 2147483647 毫秒(大约 24.8 天)以内，因为它在 IDL 中被指定为一个有符号整数。

**实现 setTimeout：**

思路：使用 `requestAnimationFrame` 来做循环，检查设定时间，在设定时间调用方法

1. 按照上方叙述，实现的函数需要有三个及多个参数
   1. fn 执行函数
   2. timeout 计时时间
   3. args 使用剩余参数收集第三个及多个参数
2. 记录函数被调用时的时间 startTime，以便于与计时时间 timeout 作对比，在指定时间时属性 fn
3. 声明一个函数 loop 供 `requestAnimationFrame` 循环调用
4. loop 内记录调用循环函数的当前时间 now，与 startTime 作对比
5. 判断 now - startTime 是否大于 timeout
   1. 大于等于 timeout 则已到设定的定时时间，调用传入的方法，并携带入参执行
   2. 否则再次执行 `requestAnimationFrame` 方法，传入 loop 函数，并返回一个 timer，以供到设定的时间时清除（`cancelAnimationFrame`）`requestAnimationFrame`

```js
const setTimeout = (fn, timeout, ...args) => {
  let setTimeoutSym = window[Symbol.for('setTimeoutStack')];
  if (!setTimeoutSym) window[Symbol.for('setTimeoutStack')] = setTimeoutSym = {};

  timeout = timeout >= 10 ? timeout : 10;

  let startTime = (timeId = Date.now());
  setTimeoutSym[timeId] = true;
  let now;
  const loop = () => {
    if (!setTimeoutSym[timeId]) return;
    now = Date.now();
    if (now - startTime >= timeout) {
      startTime = now;
      fn.apply(this, args);
      clearTimeout(timeId);
      return;
    }
    window.requestAnimationFrame(loop);
  };
  loop();
  return timeId;
};

// 使用
// 使用方式跟原生 setTimeout 类似
let timer = setTimeout(() => {
  console.log('setTimeout');
}, 3000);

function clearTimeout(timeId) {
  let setTimeoutSym = window[Symbol.for('setTimeoutStack')];
  if (setTimeoutSym && setTimeoutSym[timeId]) delete setTimeoutSym[timeId];
}

clearTimeout(timer);
```

## setInterval

Window 和 Worker 接口提供的 `setInterval()` 方法重复调用一个函数或执行一个代码片段，在每次调用之间具有固定的时间间隔。

它返回一个 `intervalID`，该 ID 唯一地标识时间间隔，因此你可以稍后通过调用 `clearInterval()` 来移除定时器。

**语法：**

```js
var intervalID = setInterval(function[, delay, arg1, arg2, ...]);
var intervalID = setInterval(function[, delay]);
var intervalID = setInterval(code[, delay]);
```

**参数：**

- function
  - 要重复调用的函数，每经过指定 delay 毫秒后执行一次。第一次调用发生在 delay 毫秒之后。
- code
  - 这个语法是可选的，你可以传递一个字符串来代替一个函数对象，你传递的字符串会被编译然后每经过 delay 毫秒执行一次。这个语法因为与 `eval()` 存在相同的安全风险所以*不推荐*使用。
- delay
  - 是每次延迟的毫秒数（一秒等于 1000 毫秒），函数的每次调用会在该延迟之后发生。如果未指定，则其默认值为 0。
- arg1, ..., argN
  - 当定时器过期的时候，将被传递给 `function` 函数的附加参数。

**返回值：**

返回值 intervalID 是一个非零数值，用来标识通过 setInterval() 创建的定时器，这个值可以用来作为 clearInterval() 的参数来清除对应的定时器。

值得注意的是，**`setInterval()` 和 `setTimeout()` 共享同一个 ID 池，并且 clearInterval() 和 clearTimeout() 在技术上是可互换使用的**。但是，我们应该匹配使用 `clearInterval()` 和 `clearTimeout()`，以避免代码杂乱无章，并增强代码的可维护性。

> 备注：参数 delay 会被转换成一个有符号 32 位整数。这将 delay 限制到了 2147483647 毫秒以内，因为它在 IDL 中被指定为一个有符号整数。

```js
function setInterval(fn, timeout, ...args) {
  let setIntervalSym = window[Symbol.for('setIntervalStack')];
  if (!setIntervalSym) window[Symbol.for('setIntervalStack')] = setIntervalSym = {};

  timeout = timeout >= 10 ? timeout : 10;

  let startTime = (timeId = Date.now());
  setIntervalSym[timeId] = true;
  let now;
  const loop = () => {
    if (!setTimeoutSym[timeId]) return;
    now = Date.now();
    if (now - startTime >= timeout) {
      startTime = now;
      fn.apply(this, args);
    }
    window.requestAnimationFrame(loop);
  };
  loop();
  return timeId;
}

// 使用
// 使用方式跟原生 setTimeout 类似
let timer = setInterval(() => {
  console.log('setInterval');
}, 3000);

function clearInterval(timeId) {
  let setIntervalSym = window[Symbol.for('setIntervalStack')];
  if (setIntervalSym && setIntervalSym[timeId]) delete setIntervalSym[timeId];
}

clearInterval(timer);
```

## this 问题

由 setTimeout() 或 setInterval() 调用的代码运行在与所在函数完全分离的执行环境上。这会导致，这些代码中包含的 this 关键字在非严格模式会指向 window (或全局) 对象，严格模式下为 undefined，这和所期望的 this 的值是不一样的。

> 备注：即使是在严格模式下，setTimeout() 的回调函数里面的 this 仍然默认指向 window 对象， 并不是 undefined。

```js
let myArray = ['zero', 'one', 'two'];
myArray.myMethod = function (sProperty) {
  alert(arguments.length > 0 ? this[sProperty] : this);
};

/* 正常调用没问题 */
myArray.myMethod(); // prints "zero,one,two"
myArray.myMethod(1); // prints "one"

/* 在 setTimeout、setInterval 调用时，this 指向 window */
setTimeout(myArray.myMethod, 1000); // prints "[object Window]" after 1 second
setTimeout(myArray.myMethod, 1500, '1'); // prints "undefined" after 1.5 seconds

/* 使用 call */
setTimeout.call(myArray, myArray.myMethod, 2000); // error: "NS_ERROR_XPC_BAD_OP_ON_WN_PROTO: Illegal operation on WrappedNative prototype object"
setTimeout.call(myArray, myArray.myMethod, 2500, 2); // same error
```

### 可能的解决办法

- 1、一个通用的方法是用包装函数来设置 this：

```js
setTimeout(function () {
  myArray.myMethod();
}, 2000); // prints "zero,one,two" after 2 seconds
setTimeout(function () {
  myArray.myMethod('1');
}, 2500); // prints "one" after 2.5 seconds
```

- 2、箭头函数也可以：

```js
setTimeout(() => {
  myArray.myMethod();
}, 2000); // prints "zero,one,two" after 2 seconds
setTimeout(() => {
  myArray.myMethod('1');
}, 2500); // prints "one" after 2.5 seconds
```

- 3、使用两个非原生的 setTimeout() 和 setInterval() 全局函数代替原生的

```js
// Enable the passage of the 'this' object through the JavaScript timers

var __nativeST__ = window.setTimeout,
  __nativeSI__ = window.setInterval;

window.setTimeout = function (vCallback, nDelay /*, argumentToPass1, argumentToPass2, etc. */) {
  var oThis = this,
    aArgs = Array.prototype.slice.call(arguments, 2);
  return __nativeST__(
    vCallback instanceof Function
      ? function () {
          vCallback.apply(oThis, aArgs);
        }
      : vCallback,
    nDelay
  );
};

window.setInterval = function (vCallback, nDelay /*, argumentToPass1, argumentToPass2, etc. */) {
  var oThis = this,
    aArgs = Array.prototype.slice.call(arguments, 2);
  return __nativeSI__(
    vCallback instanceof Function
      ? function () {
          vCallback.apply(oThis, aArgs);
        }
      : vCallback,
    nDelay
  );
};

/*  */
let myArray = ['zero', 'one', 'two'];
myArray.myMethod = function (sProperty) {
  alert(arguments.length > 0 ? this[sProperty] : this);
};

setTimeout(alert, 1500, 'Hello world!'); // the standard use of setTimeout and setInterval is preserved, but...
setTimeout.call(myArray, myArray.myMethod, 2000); // prints "zero,one,two" after 2 seconds
setTimeout.call(myArray, myArray.myMethod, 2500, 2); // prints "two" after 2,5 seconds
```

- 4、使用 bind()的例子：

```js
let myArray = ['zero', 'one', 'two'];
myBoundMethod = function (sProperty) {
  console.log(arguments.length > 0 ? this[sProperty] : this);
}.bind(myArray);

myBoundMethod(); // prints "zero,one,two" because 'this' is bound to myArray in the function
myBoundMethod(1); // prints "one"
setTimeout(myBoundMethod, 1000); // still prints "zero,one,two" after 1 second because of the binding
setTimeout(myBoundMethod, 1500, '1'); // prints "one" after 1.5 seconds
```

## 实际延时比设定值更久的原因：最小延迟时间

在浏览器中，setTimeout()/setInterval() 的每调用一次定时器的最小间隔是 4ms，这通常是由于**函数嵌套导致（嵌套层级达到一定深度），或者是由于已经执行的 setInterval 的回调函数阻塞导致的**。例如：

```js
function cb() {
  f();
  setTimeout(cb, 0);
}
setTimeout(cb, 0);

setInterval(f, 0);
```

在 Chrome 和 Firefox 中， 定时器的第 5 次调用被阻塞了；在 Safari 是在第 6 次；Edge 是在第 3 次。Gecko 从这个版本 version 56 开始对 setInterval() 开始采用这样的机制（setTimeout()已经实现，具体请参考以下内容)。

一直以来，不同浏览器中出现这种最小延迟的情况有所不同（例如 Firefox）- 从其他地方调用了 setInterval()，或者在嵌套函数调用 setTimeout() 时（嵌套级别达到特定深度时），都会出现超时延迟。
