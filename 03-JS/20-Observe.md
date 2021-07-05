# Observe

> **参考**
>
> [《MutationObserver》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver)
>
> [《Class 的基本语法》(作者：阮一峰)【来源：ECMAScript 6 入门】](https://es6.ruanyifeng.com/#docs/class)
>
> [《ES6 系列之箭头函数》(作者：冴羽)【来源：掘金】](https://juejin.cn/post/6844903616231260174)

[TOC]

MutationObserver 接口提供了监视对 DOM 树所做更改的能力。它被设计为旧的 `Mutation Events` 功能的替代品，该功能是 DOM3 Events 规范的一部分。

**`MutationObserver()` 创建并返回一个新的 `MutationObserver` 它会在指定的 DOM 发生变化时被调用。**

## 方法

### mutationObserver.disconnect()

**阻止 MutationObserver 实例(观察者)继续接收的通知，直到再次调用其 observe()方法，该观察者对象包含的回调函数都不会再被调用。**

无参数，返回 undefined。

**注意: 所有已经检测到但是尚未向观察者报告的变动都会被丢弃。**

**如果被观察的元素被从 DOM 中移除，然后被浏览器的垃圾回收机制释放，此 MutationObserver 将同样被删除。**

```js
// 创建了一个观察者，接着与之断开连接，让它可以重复使用。
var targetNode = document.querySelector('#someElement')
var observerOptions = {
  childList: true,
  attributes: true,
}

var callback = function () {
  // ...
}

var observer = new MutationObserver(callback)
observer.observe(targetNode, observerOptions)

/* some time later... */

observer.disconnect()
```

## mutationObserver.observe()

**配置了 MutationObserver 对象的回调方法以开始接收与给定选项匹配的 DOM 变化的通知。**

**根据配置，观察者会观察 DOM 树中的单个 Node，也可能会观察被指定节点的部分或者所有的子孙节点。**

`mutationObserver.observe(target[, options])`

- target
  - DOM 树中的一个要观察变化的 DOM Node (可能是一个 Element) , 或者是被观察的子节点树的根节点。
- options(可选)
  - 一个可选的 MutationObserverInit 对象，此对象的配置项描述了 DOM 的哪些变化应该提供给当前观察者的 callback。
- 返回值
  - undefined
- 异常
  - 配置选项使得实际上不会监视任何内容（例如，如果 `MutationObserverInit.childList`，`MutationObserverInit.attributes` 和 `MutationObserverInit.characterData` 都为 false）。
  - `attributes` 选项为 false（表示不监视属性更改）但是 `attributeOldValue` 为 true 并且/或者 `attributeFilter` 配置存在。
  - `characterDataOldValue` 选项为 true 但是 `MutationObserverInit.characterData` 为 false（表示不跟踪字符更改）。

```js
// 得到要观察的元素
var elementToObserve = document.querySelector('#targetElementId')

// 创建一个叫 `observer` 的新 `MutationObserver` 实例，
// 并将回调函数传给它
var observer = new MutationObserver(function () {
  console.log('callback that runs when observer is triggered')
})

// 在 MutationObserver 实例上调用 `observe` 方法，
// 并将要观察的元素与选项传给此方法
observer.observe(elementToObserve, { subtree: true, childList: true })
```

## [MutationObserverInit](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserverInit)

MutationObserverInit 字典描述了 MutationObserver 的配置。因此，它主要被用作 MutationObserver.observe() 方法的参数类型。
