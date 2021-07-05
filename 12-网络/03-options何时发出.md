# options 何时发出

> **参考**
>
> [《跨源资源共享（CORS）》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/CORS)
>
> [《OPTIONS》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/OPTIONS)
>
> [《什么时候会发送 options 请求》(作者：熊也抱抱)【来源：掘金】](https://juejin.cn/post/6844903821634699277)

[TOC]

**OPTIONS 方法对服务器发起请求，可以检测服务器支持哪些 HTTP 方法。**

## 怎么触发的 OPTIONS 请求呢？

MDN 的 CORS 一文中提到：

规范要求，对那些可能对服务器数据产生副作用的 HTTP 请求方法（特别是 GET 以外的 HTTP 请求，或者搭配某些 MIME 类型的 POST 请求），浏览器必须首先使用 OPTIONS 方法发起一个预检请求（preflight request），从而获知服务端是否允许该跨域请求。

所以这个跨域请求触发了浏览器自动发起 OPTIONS 请求，看看此次跨域请求具体触发了哪些条件。

1. 使用了下面任一 HTTP 方法：`PUT/DELETE/CONNECT/OPTIONS/TRACE/PATCH`
2. 人为设置了以下集合之外首部字段：`Accept/Accept-Language/Content-Language/Content-Type/DPR/Downlink/Save-Data/Viewport-Width/Width`
3. Content-Type 的值不属于下列之一：`application/x-www-form-urlencoded、multipart/form-data、text/plain`

## 如何避免和优化 OPTIONS

`Access-Control-Max-Age` 这个响应首部表示 preflight request （预检请求）的返回结果（即 `Access-Control-Allow-Methods` 和 `Access-Control-Allow-Headers` 提供的信息） 可以被缓存的最长时间，单位是秒。(MDN)

如果值为 -1，则表示禁用缓存，每一次请求都需要提供预检请求，即用 OPTIONS 请求进行检测。

OPTIONS 请求即预检请求，可用于检测服务器允许的 http 方法。当发起跨域请求时，由于安全原因，触发一定条件时浏览器会在正式请求之前自动先发起 OPTIONS 请求，即 CORS 预检请求，服务器若接受该跨域请求，浏览器才继续发起正式请求。
