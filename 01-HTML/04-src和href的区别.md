<!--
 * @Author: yaohebin
 * @Date: 2022-06-17 08:35:07
 * @LastEditTime: 2022-06-23 07:49:59
 * @LastEditors: yaohebin
 * @Description: src和href的区别
-->

# src 和 href 的区别

> **参考**
>
> [《HTML 中 href、src 区别》(作者：午夜星光)【来源：CSDN】](https://blog.csdn.net/annsheshira23/article/details/51133709)
>
> [《003: src 和 href 的区别是什么？》(作者：三元同学)【来源：知乎】](https://zhuanlan.zhihu.com/p/91960069)

## href

href 是（Hypertext Reference）的简写，表示超文本引用，用来建立当前元素和文档之间的链接，指向网络资源所在位置。常用的有：link、a。

例如:

```js
<a href="http://www.baidu.com"></a>
<link type="text/css" rel="stylesheet" href="common.css" />
```

浏览器会识别该文档为 css 文档，并行下载该文档，并且不会停止对当前文档的处理。这也是建议使用 link，而不采用@import 加载 css 的原因。

## src

src 是（source）的简写，src 指向的内容会嵌入到文档中当前标签所在的位置。常用的有：img、script、iframe。

常见场景:

```html
<img src="img/girl.jpg">
<iframe src="top.html">
<script src="show.js">
```

当浏览器解析到 src ，会暂停其他资源的下载和处理，直到将该资源加载或执行完毕。(这也是 script 标签为什么放在底部而不是头部的原因)

## 对比

- href 用于在当前文档和引用资源之间确立联系
- src 用于替换当前内容
