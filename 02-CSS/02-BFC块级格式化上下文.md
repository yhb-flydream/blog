# BFC 块级格式化上下文

> **参考**
>
> [《块格式化上下文》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)
>
> [《外边距重叠》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Box_Model/Mastering_margin_collapsing)
>
> [《理解CSS中BFC》(作者：一半一半)【来源：w3cplus】](https://www.w3cplus.com/css/understanding-block-formatting-contexts-in-css.html)

[TOC]

块格式化上下文（Block Formatting Context，BFC） 是 Web 页面的可视 CSS 渲染的一部分，是块盒子的布局过程发生的区域，也是浮动元素与其他元素交互的区域。

## 会创建块格式化上下文的方式

- 根元素（`<html>`）
- 浮动元素（元素的 float 不是 none）
- 绝对定位元素（元素的 position 为 absolute 或 fixed）
- 行内块元素（元素的 display 为 inline-block）
- 表格单元格（元素的 display 为 table-cell，HTML 表格单元格默认为该值）
- 表格标题（元素的 display 为 table-caption，HTML 表格标题默认为该值）
- 匿名表格单元格元素（元素的 display 为 table、table-row、 table-row-group、table-header-group、table-footer-group（分别是 HTML table、row、tbody、thead、tfoot 的默认属性）或 inline-table）
- overflow 计算值(Computed)不为 visible 的块元素
- display 值为 flow-root 的元素
- contain 值为 layout、content 或 paint 的元素
- 弹性元素（display 为 flex 或 inline-flex 元素的直接子元素）
- 网格元素（display 为 grid 或 inline-grid 元素的直接子元素）
- 多列容器（元素的 column-count 或 column-width 不为 auto，包括 column-count 为 1）
- column-span 为 all 的元素始终会创建一个新的 BFC，即使该元素没有包裹在一个多列容器中（标准变更，Chrome bug）。

**块格式化上下文包含创建它的元素内部的所有内容.**

块格式化上下文对浮动定位都很重要。**浮动定位和清除浮动时只会应用于同一个 BFC 内的元素。** 浮动不会影响其它 BFC 中元素的布局，而清除浮动只能清除同一 BFC 中在它前面的元素的浮动。

**外边距折叠（Margin collapsing）也只会发生在属于同一 BFC 的块级元素之间。**

## 解决的问题：外边距折叠（外边距塌陷）

块的上外边距(margin-top)和下外边距(margin-bottom)有时合并(折叠)为单个边距，其大小为单个边距的最大值(或如果它们相等，则仅为其中一个)，这种行为称为边距折叠。

> 有设定 `float` 和 `position: absolute` 的元素不会产生外边距重叠行为。

**有三种情况会形成外边距重叠：**

- 1、同一层相邻元素之间

```html
<style>
  p:nth-child(1) {
    margin-bottom: 13px;
  }
  p:nth-child(2) {
    margin-top: 87px;
  }
</style>

<p>下边界范围会...</p>
<p>...会跟这个元素的上边界范围重叠。</p>
```

上例中两个`<p>`元素会发生边界折叠，只会挑选最大边界范围留下，所以这个例子的边界范围其实是 87px。

- 2、没有内容将父元素和后代元素分开

如果没有边框 `border`，`内边距 padding`，`行内内容`，也没有创建`块级格式上下文`或`清除浮动`来分开**一个块级元素的上边界 margin-top 与其内一个或多个后代块级元素的上边界 margin-top**；

或没有`边框`，`内边距`，`行内内容`，`高度 height`，`最小高度 min-height` 或 `最大高度 max-height` 来分开**一个块级元素的下边界 margin-bottom 与其内的一个或多个后代后代块元素的下边界 margin-bottom**，则就会出现父块元素和其内后代块元素外边界重叠，重叠部分最终会溢出到父级块元素外面。

```html
<style type="text/css">
  section {
    margin-top: 13px;
    margin-bottom: 87px;
  }

  header {
    margin-top: 87px;
  }

  footer {
    margin-bottom: 13px;
  }
</style>

<section>
  <header>上边界重叠 87</header>
  <main></main>
  <footer>下边界重叠 87 不能再高了</footer>
</section>
```

- 3、空的块级元素

当**一个块元素上边界 margin-top 直接贴到元素下边界 margin-bottom** 时也会发生边界折叠。

这种情况会发生在**一个块元素完全没有设定`边框 border`、`内边距 paddng`、`高度 height`、`最小高度 min-height`、`最大高度 max-height`、`内容设定为 inline` 或是加上 `clear-fix`** 的时候。

```html
<style>
  ​​​​​​​​​​​​p {
    margin: 0;
  }
  div {
    margin-top: 13px;
    margin-bottom: 87px;
  }
</style>

<p>上边界范围是 87 ...</p>
<div></div>
<p>... 上边界范围是 87</p>
```

**一些需要注意的地方：**

- 上述情况的组合会产生更复杂的外边距折叠。
- 即使某一外边距为0，这些规则仍然适用。因此就算父元素的外边距是0，第一个或最后一个子元素的外边距仍然会“溢出”到父元素的外面。
- 如果参与折叠的外边距中包含负值，折叠后的外边距的值为最大的正边距与最小的负边距（即绝对值最大的负边距）的和,；也就是说如果有-13px 8px 100px叠在一起，边界范围的技术就是 100px -13px的87px。
- 如果所有参与折叠的外边距都为负，折叠后的外边距的值为最小的负边距的值。这一规则适用于相邻元素和嵌套元素。
