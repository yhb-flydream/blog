<!--
 * @Author: yaohebin
 * @Date: 2022-06-17 08:35:55
 * @LastEditTime: 2022-06-23 08:05:40
 * @LastEditors: yaohebin
 * @Description: DOCTYPE(⽂档类型)
-->

# DOCTYPE(文档类型声明)

> **参考**
>
> [《文档类型声明》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Glossary/Doctype)
>
> [《深入理解DOCTYPE的作用》(作者：@Demi)【来源：CSDN】](https://blog.csdn.net/qq_38128179/article/details/80758192)

## 简介

在 HTML 中，文档类型 `doctype` 的声明是必要的。
在所有文档的头部，你都将会看到 `<!DOCTYPE html>` 的身影。这个声明的目的是防止浏览器在渲染文档时，切换到我们称为“怪异模式 (兼容模式)”的渲染模式。
`<!DOCTYPE html>` 确保浏览器按照最佳的相关规范进行渲染，而不是使用一个不符合规范的渲染模式。
