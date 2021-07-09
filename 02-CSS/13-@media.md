# @media

> **参考**
>
> [《使用媒体查询》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/CSS/Media_Queries/Using_media_queries)
>
> [《@media》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/CSS/@media)

[TOC]

## 媒体查询

媒体查询常被用于以下目的：

- 有条件的通过 @media 和 @import at-rules 用 CSS 装饰样式。
- 用 `media=` 属性为`<style>`, `<link>`, `<source>`和其他 HTML 元素指定特定的媒体类型。如:

```html
<link rel="stylesheet" src="styles.css" media="screen" /> <link rel="stylesheet" src="styles.css" media="print" />
```

- 使用 `Window.matchMedia()` 和 `MediaQueryList.addListener()` 方法来测试和监控媒体状态。

每条媒体查询语句都由**一个可选的媒体类型**和**任意数量的媒体特性**表达式构成。_可以使用多种逻辑操作符合并多条媒体查询语句。_ 媒体查询语句不区分大小写。

当媒体类型（如果指定）与在其上显示文档的设备匹配并且所有媒体功能表达式都计算为 true 时，媒体查询将计算为 true。涉及未知媒体类型的查询始终为 false。

### 媒体类型

媒体类型（Media types）描述设备的一般类别。除非使用 not 或 only 逻辑操作符，媒体类型是可选的，并且会（隐式地）应用 all 类型。

- all 适用于所有设备
- print 适用于在打印预览模式下在屏幕上查看的分页材料和文档。
- screen 主要用于屏幕
- speech 主要用于语音合成

### 逻辑操作符

逻辑操作符（logical operators） `not`, `and`, 和 `only` 可用于联合构造复杂的媒体查询，您还可以通过用逗号分隔多个媒体查询，将它们组合为一个规则。

- and
  - 用于将多个媒体查询规则组合成单条媒体查询，当每个查询规则都为真时则该条媒体查询为真，它还用于将媒体功能与媒体类型结合在一起。
- not
  - 用于否定媒体查询，如果不满足这个条件则返回 true，否则返回 false。
  - 如果出现在以逗号分隔的查询列表中，它将仅否定应用了该查询的特定查询。
  - 如果使用 not 运算符，则还必须指定媒体类型。
- only
  - 仅在整个查询匹配时才用于应用样式，并且对于防止较早的浏览器应用所选样式很有用。
  - 当不使用 only 时，旧版本的浏览器会将 `screen and (max-width: 500px)` 简单地解释为 screen，忽略查询的其余部分，并将其样式应用于所有屏幕。
  - 如果使用 only 运算符，则还必须指定媒体类型。
- ,
  - 逗号用于将多个媒体查询合并为一个规则。
  - 逗号分隔列表中的每个查询都与其他查询分开处理。
  - 因此，如果列表中的任何查询为 true，则整个 media 语句均返回 true。
  - 换句话说，列表的行为类似于逻辑或 or 运算符。

### 定位媒体类型

媒体类型描述了给定设备的一般类别。尽管通常在设计网站时会考虑屏幕，但您可能希望创建针对特殊设备（例如打印机或基于音频的屏幕阅读器）的样式。

例如，此 CSS 针对打印机：

```css
@media print {
}
```

您还可以定位多个设备。 例如，此@media 规则使用两个媒体查询来同时定位屏幕和打印设备

```css
@media screen, print {
}
```

## @media 介绍

@media CSS @规则 可用于基于一个或多个媒体查询的结果来应用样式表的一部分。使用它，您可以指定一个媒体查询和一个 CSS 块，当且仅当该媒体查询与正在使用其内容的设备匹配时，该 CSS 块才能应用于该文档。

> 注： 在 JavaScript 中，可以使用 `CSSMediaRuleCSS` 对象模型接口访问使用 @media 创建的规则。

@media 规则可置于您代码的顶层或位于其它任何 @条件规则组 内。

```css
@media screen and (min-width: 900px) {
  article {
    padding: 1rem 3rem;
  }
}

@supports (display: flex) {
  @media screen and (min-wdith: 900px) {
    article {
      display: flex;
    }
  }
}

@media print {
  body {
    font-size: 10pt;
  }
}

@media screen {
  body {
    font-size: 13px;
  }
}

@media screen, print {
  body {
    line-height: 1.2;
  }
}

@media only screen and (min-width: 320px) and (max-width: 480px) and (resolution: 150dpi) {
  body {
    line-height: 1.4;
  }
}

@media (height > 600px) {
  body {
    line-height: 1.4;
  }
}

@media (400px <= width <= 700px) {
  body {
    line-height: 1.4;
  }
}
```
