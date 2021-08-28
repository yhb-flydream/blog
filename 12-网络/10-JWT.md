# JWT

> **参考**
>
> [《JSON Web Token 入门教程》(作者：阮一峰)【来源：阮一峰的网络日志】](http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html)
>
> [《五分钟带你了解啥是 JWT》(作者：kaipython)【来源：知乎】](https://zhuanlan.zhihu.com/p/86937325)
>
> [《什么是 JWT -- JSON WEB TOKEN》(作者：Dearmadman)【来源：简书】](https://www.jianshu.com/p/576dbf44b2ae)

[TOC]

## JSON Web Token是什么

JSON Web Token (JWT)是一个开放标准(RFC 7519)，它定义了一种紧凑的、自包含的方式，用于作为JSON对象在各方之间安全地传输信息。该信息可以被验证和信任，因为它是数字签名的。

## 什么时候你应该用JSON Web Token

- **Authorization (授权)**: 这是使用JWT的最常见场景。一旦用户登录，后续每个请求都将包含JWT，允许用户访问该令牌允许的路由、服务和资源。单点登录是现在广泛使用的JWT的一个特性，因为它的开销很小，并且可以轻松地跨域使用。
- **Information Exchange (信息交换)**: 对于安全的在各方之间传输信息而言，JSON Web Tokens无疑是一种很好的方式。因为JWT可以被签名，例如，用公钥/私钥对，你可以确定发送人就是它们所说的那个人。另外，由于签名是使用头和有效负载计算的，您还可以验证内容没有被篡改。

## JSON Web Token的结构是什么样的

JSON Web Token由三部分组成，它们之间用圆点(`.`)连接。这三部分分别是：

- Header
- Payload
- Signature

## JSON Web Tokens是如何工作的

在认证的时候，当用户用他们的凭证成功登录以后，一个JSON Web Token将会被返回。此后，token就是用户凭证了，你必须非常小心以防止出现安全问题。一般而言，你保存令牌的时候不应该超过你所需要它的时间。

无论何时用户想要访问受保护的路由或者资源的时候，用户代理（通常是浏览器）都应该带上JWT，典型的，通常放在Authorization header中，用Bearer schema。

header应该看起来是这样的：

> Authorization: Bearer

服务器上的受保护的路由将会检查Authorization header中的JWT是否有效，如果有效，则用户可以访问受保护的资源。如果JWT包含足够多的必需的数据，那么就可以减少对某些操作的数据库查询的需要，尽管可能并不总是如此。

如果token是在授权头（Authorization header）中发送的，那么跨源资源共享(CORS)将不会成为问题，因为它不使用cookie。
