# 设备像素比（devicePixelRatio）

> **参考**
>
> [《设备像素比（devicePixelRatio）》(作者：xueli_2017)【来源：CSDN】](https://blog.csdn.net/xueli_2017/article/details/91492971)
>
> [《像素、设备像素比、PPI、Viewport》(作者：小马的日常琐碎)【来源：博客园】](https://www.cnblogs.com/maqingbo/p/7528861.html)

[TOC]

> `window.devicePixelRatio` 是**设备物理像素**和**设备独立像素**（device-independent pixels，dips）之间的比率。
> `window.devicePixelRatio` = **设备物理像素** / **设备独立像素**

## 设备物理像素

> 是一个物理概念，比如设备的分辨率

## 设备独立像素

> 是一个抽象像素，用于向 CSS 中的宽、高、媒体查询和 meta 的 viewport 中的 device-width 提供的信息。

## CSS 像素

> 是 web 编程的概念，指的是 CSS 中使用的逻辑像素。在 CSS 规范中，长度单位可以分为两类，绝对(absolute)单位以及相对(relative)单位。px 是一个相对单位，相对的是设备物理像素。

## 每英寸像素(pixel per inch，ppi)

> 表示每英寸所拥有的像素(pixel)数目，数值越高，代表显示屏能够以越高的密度显示图像。
