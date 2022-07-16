# nextTick

> **参考**
>
> [《Vue 源码详解之 nextTick：MutationObserver 只是浮云，microtask 才是核心！》(作者：Ma63d)【来源：github】](https://github.com/Ma63d/vue-analysis/issues/6)
>
> [《关于 vue 中 next 和 Tick(nextTick)的一点理解》(作者：fantasy525)【来源：思否】](https://segmentfault.com/a/1190000015632392)
>
> [《Vue 中$nextTick 源码解析》(作者：谢小飞)【来源：掘金】](https://juejin.cn/post/6844904147804749832)

1. 把回调函数放入 callbacks 等待执行
2. 将执行函数放到微任务或者宏任务中
3. 事件循环到了微任务或者宏任务，执行函数依次执行 callbacks 中的回调

```js
/* @flow */
/* globals MutationObserver */

// import { noop } from 'shared/util'
// import { handleError } from './error'
// import { isIE, isIOS, isNative } from './env'

const inBrowser = typeof window !== 'undefined';
const inWeex = typeof WXEnvironment !== 'undefined' && !!WXEnvironment.platform;
const weexPlatform = inWeex && WXEnvironment.platform.toLowerCase();
const UA = inBrowser && window.navigator.userAgent.toLowerCase();
const isIE = UA && /msie|trident/.test(UA);
const isIOS = (UA && /iphone|ipad|ipod|ios/.test(UA)) || weexPlatform === 'ios';

function isNative(Ctor) {
  return typeof Ctor === 'function' && /native code/.test(Ctor.toString());
}

let isUsingMicroTask = false;

const callbacks = [];
let pending = false;

function flushCallbacks() {
  pending = false;
  const copies = callbacks.slice(0);
  console.log('[ copies ] >', copies);
  callbacks.length = 0;
  for (let i = 0; i < copies.length; i++) {
    copies[i]();
  }
}

// Here we have async deferring wrappers using microtasks.
// In 2.5 we used (macro) tasks (in combination with microtasks).
// However, it has subtle problems when state is changed right before repaint
// (e.g. #6813, out-in transitions).
// Also, using (macro) tasks in event handler would cause some weird behaviors
// that cannot be circumvented (e.g. #7109, #7153, #7546, #7834, #8109).
// So we now use microtasks everywhere, again.
// A major drawback of this tradeoff is that there are some scenarios
// where microtasks have too high a priority and fire in between supposedly
// sequential events (e.g. #4521, #6690, which have workarounds)
// or even between bubbling of the same event (#6566).
let timerFunc;

// The nextTick behavior leverages the microtask queue, which can be accessed
// via either native Promise.then or MutationObserver.
// MutationObserver has wider support, however it is seriously bugged in
// UIWebView in iOS >= 9.3.3 when triggered in touch event handlers. It
// completely stops working after triggering a few times... so, if native
// Promise is available, we will use it:
/* istanbul ignore next, $flow-disable-line */
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve();
  console.log('[ Promise ] >', 'Promise');
  timerFunc = () => {
    console.log('[ p.then ] >', 'p.then');
    p.then(flushCallbacks);
    // In problematic UIWebViews, Promise.then doesn't completely break, but
    // it can get stuck in a weird state where callbacks are pushed into the
    // microtask queue but the queue isn't being flushed, until the browser
    // needs to do some other work, e.g. handle a timer. Therefore we can
    // "force" the microtask queue to be flushed by adding an empty timer.
    // if (isIOS) setTimeout(noop)
  };
  isUsingMicroTask = true;
} else if (
  !isIE &&
  typeof MutationObserver !== 'undefined' &&
  (isNative(MutationObserver) ||
    // PhantomJS and iOS 7.x
    MutationObserver.toString() === '[object MutationObserverConstructor]')
) {
  // Use MutationObserver where native Promise is not available,
  // e.g. PhantomJS, iOS7, Android 4.4
  // (#6466 MutationObserver is unreliable in IE11)
  console.log('[ MutationObserver ] >', 'MutationObserver');
  let counter = 1;
  const observer = new MutationObserver(flushCallbacks);
  const textNode = document.createTextNode(String(counter));
  observer.observe(textNode, {
    characterData: true,
  });
  timerFunc = () => {
    console.log('[ MutationObserver ] >', 'MutationObserver()');
    counter = (counter + 1) % 2;
    textNode.data = String(counter);
  };
  isUsingMicroTask = true;
} else if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  // Fallback to setImmediate.
  // Technically it leverages the (macro) task queue,
  // but it is still a better choice than setTimeout.
  console.log('[ setImmediate ] >', 'setImmediate');
  timerFunc = () => {
    console.log('[ setImmediate ] >', 'setImmediate()');
    setImmediate(flushCallbacks);
  };
} else {
  // Fallback to setTimeout.
  console.log('[ setTimeout ] >', 'setTimeout');
  timerFunc = () => {
    console.log('[ setTimeout ] >', 'setTimeout()');
    setTimeout(flushCallbacks, 0);
  };
}

function nextTick(cb, ctx) {
  let _resolve;
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx);
      } catch (e) {
        handleError(e, ctx, 'nextTick');
      }
    } else if (_resolve) {
      _resolve(ctx);
    }
  });
  console.log('[ pending ] >', pending);
  if (!pending) {
    pending = true;
    timerFunc();
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise((resolve) => {
      _resolve = resolve;
    });
  }
}
function a() {
  console.log('[ 1 ] >', 1);
}
function b() {
  console.log('[ 2 ] >', 2);
}

nextTick(a);
console.log('[ a done ] >', 'a done');
nextTick(b);
console.log('[ b done ] >', 'b done');
```

总结下：

1. nextTick 中维护了一个 callbacks 队列，一个 pending 锁，一个 timerFunc 微任务或者宏任务，一个 flushCallbacks 微任务或者宏任务的回调函数。
2. 这个 timerFunc 就是根据浏览器环境判断得出的一个能够产生微任务或降级为宏任务的 api 调用，比如 promise.then。
3. callbacks 队列的作用是收集当前正在执行的宏任务中所有的 nextTick 回调，等当前宏任务执行完之后好一次性 for 循环执行完。
4. 试想如果没有 callback 队列的话，每次调用 nextTick 都去创建一个 timerFunc 微任务（假设支持），那么也就不需要 pending 锁了。
5. 现在有了 callbacks 队列的情况下就只需要创建一个 timerFunc 微任务或者宏任务，那问题是什么时候创建该微任务呢？
6. 这里就要讲到 pending 了，在 pending 为 false 的时候表示第一次添加 cb 到 callbacks 中，这时候创建一个 timerFunc 微任务，并加锁。
7. 后面调用 nextTick 就只是往 callbacks 添加回调。
8. 等当前宏任务之后完之后，就会去执行 flushCallbacks 清空 callbacks 队列，并设置 pending 为 false，一切归零
