<!--
 * @Author: yaohebin
 * @Date: 2021-10-20 11:14:32
 * @LastEditTime: 2022-07-16 17:33:56
 * @LastEditors: yaohebin
 * @Description: virtualDom
-->
# virtualDom

> **参考**
>
> [《深入剖析：Vue 核心之虚拟 DOM》(作者：我是你的超级英雄)【来源：掘金】](https://juejin.cn/post/6844903895467032589)
>
> [《从了解到深入虚拟DOM和实现diff算法》(作者：小浪努力学前端)【来源：掘金】](https://juejin.cn/post/6990582632270528525)
>
> [《Vue原理解析之Virtual Dom》(作者：JoeRay61)【来源：掘金】](https://segmentfault.com/a/1190000008291645)

[TOC]

## 虚拟 DOM 出现的原因

1. 操作大量的真实 DOM 的代价很大，每次操作 DOM 都可能引起浏览器的回流，浪费性能，影响体验
2. 跨平台

## 虚拟 DOM 的好处

虚拟 DOM 就是为了解决浏览器性能问题而被设计出来的。如前，若一次操作中有 10 次更新 DOM 的动作，虚拟 DOM 不会立即操作 DOM，而是将这 10 次更新的 diff 内容保存到本地一个 JS 对象中，最终将这个 JS 对象一次性 attch 到 DOM 树上，再进行后续操作，避免大量无谓的计算量。

## 解析虚拟 DOM

### 把真实 DOM 解析成用对象来表示的结构树

### diff 算法比较新旧虚拟 DOM 的差异

### 将两个虚拟 DOM 对象的差异应用到真正的 DOM 树
