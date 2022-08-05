<!--
 * @Author: yaohebin
 * @Date: 2021-02-01 17:52:46
 * @LastEditTime: 2022-08-05 10:20:35
 * @LastEditors: yaohebin
 * @Description: webpack、grunt、gulp
-->

# webpack、grunt、gulp

> **参考**
>
> [《如何看待 Grunt、Gulp 和 Webpack》(作者：果汁凉茶丶)【来源：思否】](https://www.jianshu.com/p/fe96491ccf56)
>
> [《gulp 与 webpack 的区别》(作者：海角在眼前)【来源：博客园】](https://www.cnblogs.com/lovesong/p/6413546.html)
>
> [《Webpack 与 Gulp、Grunt 区别》(作者：珍妮的小罐君)【来源：CSDN】](https://blog.csdn.net/qq_36671474/article/details/82227369)
>
> [《Grunt、Gulp 和 Webpack 区别 》(作者：Ann&)【来源：博客园】](https://www.cnblogs.com/Ann-web-1/p/11444675.html)

[TOC]

## Grunt、Gulp

流管理工具，通过一个个 task 配置执行用户需要的功能，如格式检验，代码压缩等，值得一提的是，经过这两者处理的代码只是局部变量名被替换简化，整体并没有发生改变，还是你的代码。

## webpack

进行了更彻底的打包处理，更加偏向对模块语法规则进行转换。主要任务是突破浏览器的鸿沟，将原本浏览器不能识别的规范和各种各样的静态文件进行分析，压缩，合并，打包，最后生成浏览器支持的代码。最终处理过的代码可读性已经非常差了。
