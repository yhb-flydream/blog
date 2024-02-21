<!--
 * @Author: yaohebin
 * @Date: 2022-06-17 08:35:07
 * @LastEditTime: 2024-02-21 11:35:10
 * @LastEditors: yaohebin
 * @Description: src和href的区别
-->

# src 和 href 的区别

> **参考**
>
> [《HTML 中 href、src 区别》(作者：午夜星光)【来源：CSDN】](https://blog.csdn.net/annsheshira23/article/details/51133709)
>
> [《003: src 和 href 的区别是什么？》(作者：三元同学)【来源：知乎】](https://zhuanlan.zhihu.com/p/91960069)
>
> [《简单介绍一下 url、href、src 到底是什么？可能好多人不太明白》(作者：rocling)【来源：CSDN】](https://blog.csdn.net/rocling/article/details/82954538)

## href

href 是（Hypertext Reference）的简写，表示**超文本引用**。

**用来建立当前元素和文档之间的链接，指向网络资源所在位置。**

常用的有：link、a。

例如:

```js
<a href="http://www.baidu.com"></a>
<link type="text/css" rel="stylesheet" href="common.css" />
```

## src

src 是（source）的简写，表示**来源**。

**指向的内容会嵌入到文档中当前标签所在的位置。**

常用的有：img、script、iframe。

例如:

```html
<img src="img/girl.jpg">
<iframe src="top.html">
<script src="show.js">
```

## 对比

1. 请求资源类型不同
   - href 指向网络资源所在位置，建立和当前元素（锚点）或当前文档（链接）之间的联系
   - src 指向资源会将其下载并应用到文档中，比如 JavaScript 脚本，img 图片
2. 作用结果不同
   - href 用于在当前文档和引用资源之间确立联系
   - src 用于替换当前内容
3. 浏览器解析方式不同
   - href 若在文档中添加 ，浏览器会识别该文档为 CSS 文件，就会并行下载资源并且不会停止对当前文档的处理。这也是为什么建议使用 link 方式加载 CSS，而不是使用 @import 方式
   - src 当浏览器解析到，会暂停其他资源的下载和处理，直到将该资源加载、编译、执行完毕，图片和框架等也如此，类似于将所指向资源应用到当前内容。这也是为什么建议把 js 脚本放在底部而不是头部的原因
