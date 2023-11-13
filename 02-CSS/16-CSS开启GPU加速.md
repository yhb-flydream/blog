# CSS 开启 GPU 加速

> **参考**
>
> [《will-change》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/CSS/will-change)
>
> [《Web 性能优化-CSS3 硬件加速(GPU 加速)》(作者：lizhen)【来源：https://lz5z.com/】](https://lz5z.com/Web%E6%80%A7%E8%83%BD%E4%BC%98%E5%8C%96-CSS3%E7%A1%AC%E4%BB%B6%E5%8A%A0%E9%80%9F/)
>
> [《CSS 动画之硬件加速》(作者：南北)【来源：w3cplus.com】](https://www.w3cplus.com/css3/introduction-to-hardware-acceleration-css-animations.html)
>
> [《使用 CSS3 will-change 提高页面滚动、动画等渲染性能》(作者：张鑫旭)【来源：http://www.zhangxinxu.com】](http://www.zhangxinxu.com/wordpress/?p=5025)
>
> [《脑洞大开：为啥帧率达到 60 fps 就流畅？》(作者：xiaosongluo)【来源：简书】](https://www.jianshu.com/p/71cba1711de0)
>
> [《深度剖析浏览器渲染性能原理，你到底知道多少？》(作者：齐修\_qixiuss)【来源：简书】](https://www.jianshu.com/p/a32b890c29b1)
>
> [《GPU 加速是什么》(作者：凹凸实验室)【来源：https://jelly.jd.com/】](https://jelly.jd.com/article/6006b1045b6c6a01506c87e2)

[TOC]

## 为什么要开启硬件加速

- 加速页面的呈现，GPU 加速可以减少浏览器回流（重排）、重绘，减少浏览器绘制页面的时间，优化用户体验。
- 开启 GPU 硬件加速可以减轻 CPU 的压力，使其可以处理更多的事情。

## 原理是什么

由 [浏览器渲染原理及流程](../04-浏览器/02-浏览器渲染原理及流程.md) 可知，浏览器需要将布局之后的渲染树进行绘制到屏幕上，在绘制的过程中需要将元素进行划分图层，有些图层则会经过 GPU 的处理形成渲染纹理，而某一些 CSS 的设置又会使 GPU 在处理图层的时候不触发浏览器的回流（重排）、重绘机制，从而减少了绘制时间。

## 怎么开启硬件加速

一些规则是可以让浏览器主动创建独立的渲染图层的：

- 3D 或者透视变换（perspective，transform） 的 CSS 属性。
- 使用加速视频解码的 video 元素。
- 拥有 3D（WebGL） 上下文或者加速 2D 上下文的 canvas 元素。
- 混合插件（Flash）。
- 对自己的 opacity 做 CSS 动画或使用一个动画 webkit 变换的元素。
- 拥有加速 CSS 过滤器的元素。
- 元素有一个包含复合层的后代节点(换句话说，就是一个元素拥有一个子元素，该子元素在自己的层里)。
- 元素有一个兄弟元素在复合图层渲染，并且该兄弟元素的 z-index 较小，那这个元素也会被应用到复合图层。

## CSS 开启硬件加速

### transform

```css
.exam1 {
  transform: translateZ(0);
}
.exam2 {
  transform: translate3d(0, 0, 0);
}
.exam3 {
  transform: rotateZ(360deg);
}
.exam4 {
  transform: scaleZ(0.3);
}
```

### opacity

### filter

### will-change

will-change 为 web 开发者提供了一种告知浏览器该元素会有哪些变化的方法，这样浏览器可以在元素属性真正发生变化之前提前做好对应的优化准备工作。这种优化可以将一部分复杂的计算工作提前准备好，使页面的反应更为快速灵敏。

- **不要将 will-change 应用到太多元素上：**
  - 浏览器已经尽力尝试去优化一切可以优化的东西了。有一些更强力的优化，如果与 will-change 结合在一起的话，有可能会消耗很多机器资源，如果过度使用的话，可能导致页面响应缓慢或者消耗非常多的资源。
- **不要过早应用 will-change 优化：**
  - 通常，当元素恢复到初始状态时，浏览器会丢弃掉之前做的优化工作。但是如果直接在样式表中显式声明了 will-change 属性，则表示目标元素可能会经常变化，浏览器会将优化工作保存得比之前更久。
  - **所以最佳实践是当元素变化之前和之后通过脚本来切换 will-change 的值。**
- **有节制地使用：**
  - 如果你的页面在性能方面没什么问题，则不要添加 will-change 属性来榨取一丁点的速度。
  - will-change 的设计初衷是作为最后的优化手段，用来尝试解决现有的性能问题。它不应该被用来预防性能问题。过度使用 will-change 会导致大量的内存占用，并会导致更复杂的渲染过程，因为浏览器会试图准备可能存在的变化过程。这会导致更严重的性能问题。
- **给它足够的工作时间：**
  - 这个属性是用来让页面开发者告知浏览器哪些属性可能会变化的。然后浏览器可以选择在变化发生前提前去做一些优化工作。所以给浏览器一点时间去真正做这些优化工作是非常重要的。使用时需要尝试去找到一些方法提前一定时间获知元素可能发生的变化，然后为它加上 will-change 属性。

不要这样直接写在默认状态中，因为 will-change 会一直挂着，可以在使用的时候挂在上去，触发完之后移除。

```css
/* 不要这样直接写在默认状态中，因为 will-change 会一直挂着 */
.will-change {
  will-change: transform;
  transition: transform 0.3s;
}
.will-change:hover {
  transform: scale(1.5);
}

/* 可以在使用的时候挂在上去，触发完之后移除 */
.will-change-parent:hover .will-change {
  will-change: transform;
}
.will-change {
  transition: transform 0.3s;
}
.will-change:hover {
  transform: scale(1.5);
}
```

## 注意事项

- 内存。如果 GPU 加载了大量的纹理，那么很容易就会发生内容问题，这一点在移动端浏览器上尤为明显，所以，一定要牢记不要让页面的每个元素都使用硬件加速。
- 使用 GPU 渲染会影响字体的抗锯齿效果。这是因为 GPU 和 CPU 具有不同的渲染机制。即使最终硬件加速停止了，文本还是会在动画期间显示得很模糊。
