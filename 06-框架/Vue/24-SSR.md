<!--
 * @Author: yaohebin
 * @Date: 2021-06-24 11:43:01
 * @LastEditTime: 2022-07-21 11:10:36
 * @LastEditors: yaohebin
 * @Description: SSR
-->

# SSR

> **参考**
>
> [Vue SSR 指南](https://v2.ssr.vuejs.org/zh/)
>
> [开箱即用的 SSR 框架](https://doc.ssr-fc.com/)
>
> [《带你五步学会 Vue SSR》(作者：leocoder)【来源：思否】](https://segmentfault.com/a/1190000016637877)

[TOC]

## 什么是服务器端渲染 (SSR)？

将同一个组件渲染为服务器端的 HTML 字符串，将它们直接发送到浏览器，最后将这些静态标记"激活"为客户端上完全可交互的应用程序。

服务器渲染的 Vue.js 应用程序也可以被认为是"同构"或"通用"，因为应用程序的大部分代码都可以在服务器和客户端上运行。

## SSR 优势

1. 更好的 SEO
2. 更快的内容到达时间 (time-to-content)

## SSR 限制

1. 开发条件所限。
   - 浏览器特定的代码，只能在某些生命周期钩子函数 (lifecycle hook) 中使用；
   - 一些外部扩展库 (external library) 可能需要特殊处理，才能在服务器渲染应用程序中运行。
2. 涉及构建设置和部署的更多要求。
   - 与可以部署在任何静态文件服务器上的完全静态单页面应用程序 (SPA) 不同，服务器渲染应用程序，需要处于 Node.js server 运行环境。
3. 更多的服务器端负载。
   - 在 Node.js 中渲染完整的应用程序，显然会比仅仅提供静态文件的 server 更加大量占用 CPU 资源 (CPU-intensive - CPU 密集)，因此如果你预料在高流量环境 (high traffic) 下使用，请准备相应的服务器负载，并明智地采用缓存策略。
