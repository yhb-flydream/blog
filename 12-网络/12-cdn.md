<!--
 * @Author: yaohebin
 * @Date: 2021-02-01 17:40:53
 * @LastEditTime: 2022-06-16 08:50:02
 * @LastEditors: yaohebin
 * @Description: CDN
-->
# CDN

> **参考**
>
> [《CDN》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Glossary/CDN)
>
> [《CDN是什么？使用CDN有什么优势？》【来源：知乎】](https://www.zhihu.com/question/36514327)

CDN (内容分发网络) 指的是一组分布在各个地区的服务器。这些服务器存储着数据的副本，因此服务器可以根据哪些服务器与用户距离最近，来满足数据的请求。 CDNs 提供快速服务，较少受高流量影响。

CDNs 被广泛用于传输 stylesheets 和 JavaScript 等静态资源，像 Bootstrap，Jquery 等。对这些库文件使用 CDN 技术，有以下几点好处：

- 通过 CDN 向用户分发传输相关库的静态资源文件，可以降低我们自身服务器的请求压力。
- 大多数 CDN 在全球都有服务器，所以 CDNs 上的服务器在地理位置上可能比你自己的服务器更接近你的用户。 地理距离会按比例影响延迟。
- CDNs 已经配置了恰当的缓存设置。使用 CDN 节省了在你的服务器中对静态资源文件的配置。
