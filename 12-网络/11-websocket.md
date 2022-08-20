# WebSocket

> **参考**
>
> [《WebSocket》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/WebSocket)
>
> [《WebSocket 教程》(作者：阮一峰)【来源：阮一峰的网络日志】](http://www.ruanyifeng.com/blog/2017/05/websocket.html)
>
> [《WebSocket 其实没那么难》(作者：阮一峰)【来源：知乎】](https://zhuanlan.zhihu.com/p/74326818)
>
> [《WebSocket》(作者：廖雪峰)【来源：廖雪峰的官方网站】](https://www.liaoxuefeng.com/wiki/1022910821149312/1103303693824096)
>
> [《WebSocket 加入心跳包防止自动断开连接》(作者：一行代码一首诗)【来源：简书】](https://www.jianshu.com/p/1141dcf6de3e)
>
> [《WebSocket 使用及优化（心跳机制与断线重连）》(作者：DieHunter1024)【来源：掘金】](https://juejin.cn/post/6945057379834675230)

## 简介

- WebSocket 是一种在单个 TCP 连接上进行全双工通信的协议。WebSocket 使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。
- 在 WebSocket API 中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接， 并进行双向数据传输。（维基百科）
- WebSocket 本质上一种计算机网络应用层的协议，用来弥补 http 协议在持久通信能力上的不足。
- **它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。**

其它特点：

- 建立在 TCP 协议之上，服务器端的实现比较容易。
- 与 HTTP 协议有着良好的兼容性。默认端口也是 80 和 443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
- 数据格式比较轻量，性能开销小，通信高效。
- 可以发送文本，也可以发送二进制数据。
- 没有同源限制，客户端可以与任意服务器通信。
- 协议标识符是 ws（如果加密，则为 wss），服务器网址就是 URL。

## 为什么需要 WebSocket

HTTP 协议有一个缺陷：**通信只能由客户端发起，不具备服务器推送能力。**

要实现实时获取服务端最新的数据只能使用 **轮询** 的方式：每隔一段时候，就发出一个询问，了解服务器有没有新的信息。

在 WebSocket 协议出现以前，创建一个和服务端进双通道通信的 web 应用，需要依赖 HTTP 协议，进行不停的轮询，这会导致一些问题：

- 服务端被迫维持来自每个客户端的大量不同的连接
- 大量的轮询请求会造成高开销，比如会带上多余的 header，造成了无用的数据传输。

## WebSocket 与 HTTP 的区别

- 都是一样基于 TCP 的，都是可靠性传输协议。都是应用层协议。
- WebSocket 在建立握手时，数据是通过 HTTP 传输的。但是建立之后，在真正传输时候是不需要 HTTP 协议的。

## WebSocket 协议的原理

HTTP 请求头中多了几个关于 WebSocket 的字段

- Upgrade: websocket
- Connection: Upgrade

告诉服务器要开启 websocket 连接了

- Sec-WebSocket-Key: x3JJHMbDL1EzLkh9GBhXDw==
  - 一个 Base64 encode 的值，这个是浏览器随机生成的，验证服务器的真伪
- Sec-WebSocket-Protocol: chat, superchat
  - 一个用户定义的字符串，用来区分同 URL 下，不同的服务所需要的协议
- Sec-WebSocket-Version: 13
  - 告诉服务器所使用的 WebSocket Draft （协议版本）

HTTP 响应：

```text
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: HSmrc0sMlYUkAGmm5OPpG2HaGWk=
Sec-WebSocket-Protocol: chat
```

Sec-WebSocket-Accept 这个则是经过服务器确认，并且加密过后的 Sec-WebSocket-Key，Sec-WebSocket-Protocol 则是表示最终使用的协议。

WebSocket 连接的过程是：

- 首先，客户端发起 http 请求，经过 3 次握手后，建立起 TCP 连接；http 请求里存放 WebSocket 支持的版本号等信息，如：Upgrade、Connection、WebSocket-Version 等；
- 然后，服务器收到客户端的握手请求后，同样采用 HTTP 协议回馈数据；
- 最后，客户端收到连接成功的消息后，开始借助于 TCP 传输信道进行全双工通信。

## Websocket 的优缺点

优点：

- 一旦建议后，互相沟通所消耗的请求头是很小的
- 服务器可以向客户端推送消息了

缺点：

- 少部分浏览器不支持，浏览器支持的程度与方式有区别（IE10）

## 应用场景

- 即时聊天通信
- 多玩家游戏
- 在线协同编辑
- 实时数据流的拉取与推送
- 新闻类实况直播
- 实时地理位置
