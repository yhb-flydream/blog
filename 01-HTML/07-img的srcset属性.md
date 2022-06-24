<!--
 * @Author: yaohebin
 * @Date: 2022-06-17 08:37:31
 * @LastEditTime: 2022-06-24 08:30:44
 * @LastEditors: yaohebin
 * @Description: img的srcset属性
-->

# img 的 srcset 属性

> **参考**
>
> [《`<img>`：图像嵌入元素 srcset》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/img#attr-srcset)
>
> [《响应式图片srcset全新释义sizes属性w描述符》(作者：张鑫旭)【来源：http://www.zhangxinxu.com】](https://www.zhangxinxu.com/wordpress/2014/10/responsive-images-srcset-size-w-descriptor/)

以逗号分隔的一个或多个字符串列表表明一系列用户代理使用的可能的图像。

每一个字符串由以下组成：

- 指向图像的 URL。
- 可选地，再加一个空格之后，附加以下的其一：
  - 一个宽度描述符，这是一个正整数，后面紧跟 'w' 符号。该整数宽度除以 sizes 属性给出的资源（source）大小来计算得到有效的像素密度，即换算成和 x 描述符等价的值。
  - 一个像素密度描述符，这是一个正浮点数，后面紧跟 'x' 符号。

如果没有指定源描述符，那它会被指定为默认的 1x。

在相同的 srcset 属性中混合使用宽度描述符和像素密度描述符时，会导致该值无效。重复的描述符（比如，两个源在相同的 srcset 两个源都是 2x）也是无效的。

在支持 srcset 的用户代理中，src 属性中的图片被作为 1x 候选项。

```html
<img src="favicon72.png"
    alt="MDN logo"
    srcset="favicon144.png 2x">
```

在支持 srcset 的用户代理中，当使用 w 描述符时，src 属性会被忽略。当匹配了媒体条件 (max-width: 600px) 时，图像将宽 200px，否则宽 50vw（视图宽度的 50%）。

```html
<img src="clock-demo-200px.png"
    alt="Clock"
    srcset="clock-demo-200px.png 200w, clock-demo-400px.png 400w"
    sizes="(max-width: 600px) 200px, 50vw">
```
