<!--
 * @Author: yaohebin
 * @Date: 2021-02-01 17:36:46
 * @LastEditTime: 2022-06-16 08:48:36
 * @LastEditors: yaohebin
 * @Description: GET&POST
-->
# GET&POST

> **参考**
>
> [《GET》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/GET)
>
> [《POST》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST)
>
> [《OPTIONS》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/OPTIONS)
>
> [《GET 和 POST 两种基本请求方法的区别》(作者：在途中#)【来源：博客园】](https://www.cnblogs.com/logsharing/p/8448446.html)
>
> [《GET 和 POST 到底有什么区别？》【来源：知乎】](https://www.zhihu.com/question/28586791)

[TOC]

- GET 参数通过 URL 传递，POST 放在 Request body 中。
- GET 在浏览器回退时是无害的，而 POST 会再次提交请求。
- GET 产生的 URL 地址可以被 Bookmark，而 POST 不可以。
- GET 请求会被浏览器主动 cache，而 POST 不会，除非手动设置。
- GET 请求只能进行 url 编码，而 POST 支持多种编码方式。
- GET 请求参数会被完整保留在浏览器历史记录里，而 POST 中的参数不会被保留。
- GET 请求在 URL 中传送的参数是有长度限制的，而 POST 没有。
- 对参数的数据类型，GET 只接受 ASCII 字符，而 POST 没有限制。
- GET 比 POST 更不安全，因为参数直接暴露在 URL 上，所以不能用来传递敏感信息。

GET 和 POST 本质上就是 TCP 链接，并无差别。但是由于 HTTP 的规定和浏览器/服务器的限制，导致他们在应用过程中体现出一些不同。

GET 产生一个 TCP 数据包；POST 产生两个 TCP 数据包。

POST 请求之前会有一次预请求，校验安全性。
