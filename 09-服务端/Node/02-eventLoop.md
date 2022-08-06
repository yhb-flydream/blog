<!--
 * @Author: yaohebin
 * @Date: 2021-02-01 17:21:34
 * @LastEditTime: 2022-08-06 09:50:55
 * @LastEditors: yaohebin
 * @Description: eventLoop
-->

# eventLoop

> **参考**
>
> [《Node 指南》【来源：nodejs.org】](https://nodejs.org/zh-cn/docs/guides/)
>
> [《不要混淆 nodejs 和浏览器中的 event loop》(作者：光光同学)【来源：cnode】](https://cnodejs.org/topic/5a9108d78d6e16e56bb80882)
>
> [《一次弄懂 Event Loop（彻底解决此类面试问题） - NodeJS 的 Event Loop》(作者：光光同学)【来源：掘金】](https://juejin.cn/post/6844903764202094606#heading-25)
>
> [《正确理解 Node.js 的 Event loop》(作者：乃乎)【来源：知乎】](https://zhuanlan.zhihu.com/p/80458653)
>
> [《「多图详解」NodeJs 中 EventLoop 与浏览器下的差异性》(作者：19 组清风)【来源：掘金】](https://juejin.cn/post/7077122129107353636)
>
> [《又被 node 的 eventloop 坑了，这次是 node 的锅》(作者：zy445566)【来源：知乎】](https://zhuanlan.zhihu.com/p/54951550)

[TOC]

```text
   ┌───────────────────────────┐
┌─>│           timers          │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │
│  └─────────────┬─────────────┘      ┌───────────────┐
│  ┌─────────────┴─────────────┐      │   incoming:   │
│  │           poll            │<─────┤  connections, │
│  └─────────────┬─────────────┘      │   data, etc.  │
│  ┌─────────────┴─────────────┐      └───────────────┘
│  │           check           │
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │
   └───────────────────────────┘
```

_注意：每个框被称为事件循环机制的一个阶段。_

每个阶段都有一个 FIFO 队列来执行回调。

虽然每个阶段都是特殊的，但通常情况下，**当事件循环进入给定的阶段时，它将执行特定于该阶段的任何操作，然后执行该阶段队列中的回调，直到队列用尽或最大回调数已执行**。当该队列已用尽或达到回调限制，事件循环将移动到下一阶段，等等。这六个阶段都执行完毕称为一轮循环。

由于这些操作中的任何一个都可能调度 _更多的_ 操作和由内核排列在轮询阶段被处理的新事件，且在处理轮询中的事件时，轮询事件可以排队。因此，长时间运行的回调可以允许轮询阶段运行长于计时器的阈值时间。

## timers 定时器

> 本阶段执行已经被 setTimeout() 和 setInterval() 的调度回调函数。

在 timer 阶段其实使用一个最小堆而不是队列来保存所有元素（其实也可以理解，因为 timeout 的 callback 是按照超时时间的顺序来调用的，并不是先进先出的队列逻辑），然后循环取出所有到期的 callback 执行。

## pending callbacks 待定回调

> 执行从上一个循环延迟到当前循环的 I/O 回调。

根据 libuv 的文档，一些应该在上轮循环 poll 阶段执行的 callback，因为某些原因不能执行，就会被延迟到这一轮的循环的 I/O callbacks 阶段执行。换句话说这个阶段执行的 callbacks 是上轮残留的。

## idle, prepare

> 仅系统内部使用。

## poll 轮询

> 检索新的 I/O 事件;
> 执行与 I/O 相关的回调（几乎所有情况下，除了关闭的回调函数，那些由计时器和 setImmediate() 调度的之外），其余情况 node 将在适当的时候在此阻塞。

poll 阶段的任务就是阻塞等待监听的事件来临，然后执行对应的 callback，其中阻塞是带有超时时间的，以下几种情况都会使得超时时间为 0

- uv_run 处于 UV_RUN_NOWAIT 模式下
- uv_stop()被调用
- 没有活跃的 handles 和 request
- 有活跃的 idle handles
- 有等待关闭的 handles

如果上述都不符合，则超时时间为距离现在最近的 timer；如果没有 timer 则 poll 阶段会一直阻塞下去

## check 检测

> setImmediate() 回调函数在这里执行。

## close callbacks 关闭的回调函数

> 一些关闭的回调函数，如：`socket.on('close'[, fn])`、`http.server.on('close, fn)`。

循环关闭所有的 closing handles

## process.nextTick

文档中提到 process.nextTick() 不属于上面的任何一个 phase，但它在每个 phase 结束的时候都会运行。

process.nextTick 的实现在 next_tick.js 中

```js
function nextTick(callback) {
  if (typeof callback !== 'function') throw new errors.TypeError('ERR_INVALID_CALLBACK');

  if (process._exiting) return;

  var args;
  switch (arguments.length) {
    case 1:
      break;
    case 2:
      args = [arguments[1]];
      break;
    case 3:
      args = [arguments[1], arguments[2]];
      break;
    case 4:
      args = [arguments[1], arguments[2], arguments[3]];
      break;
    default:
      args = new Array(arguments.length - 1);
      for (var i = 1; i < arguments.length; i++) args[i - 1] = arguments[i];
  }

  push(new TickObject(callback, args, getDefaultTriggerAsyncId())); //将callback封装为一个对象放入队列中
}
```

它并没有什么魔法，也没有调用 C++提供的函数，只是简单地将所有回调封装为对象并放入队列。而 callback 的执行是在函数 `_tickCallback()`

```js
function _tickCallback() {
  let tock;
  do {
    while ((tock = shift())) {
      const asyncId = tock[async_id_symbol];
      emitBefore(asyncId, tock[trigger_async_id_symbol]);
      if (destroyHooksExist()) emitDestroy(asyncId);

      const callback = tock.callback;
      if (tock.args === undefined) callback(); //执行调用process.nextTick()时放置进来的callback
      else Reflect.apply(callback, undefined, tock.args); //执行调用process.nextTick()时放置进来的callback

      emitAfter(asyncId);
    }
    runMicrotasks(); //microtasks将会在此时执行，例如Promise
  } while (head.top !== head.bottom || emitPromiseRejectionWarnings());
  tickInfo[kHasPromiseRejections] = 0;
}
```

可以看到`_tickCallback()`会循环执行队列中所有 callback，值得注意的是 microtasks 的执行时机， 因此`_tickCallback()`的执行就意味着 process.nextTick()的回调的执行。

整理一下 `_tickCallback()`的转移并最终被调用的流程：

- 在 js 层面

```text
_tickCallback()//js中执行process.nextTick()的回调函数
        ↓
process._setupNextTick(_tickCallback) //c++和js的桥梁，将回调交给C++执行
```

- 此时 `_tickCallback()`被转移到在 C++层面，它首先被存储到 env 中

```text
env->set_tick_callback_function()//将_tickCallback存储到env中
        ↓
env->SetMethod(process, "_setupNextTick", SetupNextTick);//调用上者，js中process._setupNextTick的真身
```

- 被存储到 env 的 `_tickCallback()`被调用流程如下：

```text
env_->tick_callback_function()//取出_tickCallback执行
        ↓
InternalCallbackScope::Close()//调用前者
        ↓
InternalMakeCallback()//调用前者
        ↓
AsyncWrap::MakeCallback()//调用前者
        ↓
被多个封装异步操作的类继承并调用
        ↓
被uv_run()执行，从而实现在每个phase之后调用process.nextTick提供的回调
```

由以上源码分析可知，在 uv_run()执行后，从而实现在每个 phase 之后调用 process.nextTick 提供的回调
