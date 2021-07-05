# link 与@import 的区别

> **参考**
>
> [《`<link>`：外部资源链接元素》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/link)
>
> [《@import》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/CSS/%40import)
>
> [《由 link 和@import 的区别引发的 CSS 渲染杂谈》(作者：奇拉米诺)【来源：博客园】](https://www.cnblogs.com/KilerMino/p/6115803.html)

[TOC]

## link

> HTML 外部资源链接元素 (`<link>`) 规定了当前文档与外部资源的关系。该元素最常用于链接样式表，此外也可以被用来创建站点图标(比如 PC 端的“favicon”图标和移动设备上用以显示在主屏幕的图标) 。

- `rel` 属性表示 **“关系 (relationship) ”** 它可能是`<link>`元素其中一个关键的特性——属性值表示`<link>`项的链接方式与包含它的文档之间的关系。
- `<link>`元素可以出现在`<head>`元素或者`<body>`元素中，具体取决于它是否有一个`body-ok`的链接类型。例如，stylesheet 链接类型是`body-ok`的，因此`<link rel="stylesheet">`允许出现在`body`中。然而，这不是一种好的可遵循的实践方式；更合理的方式是，将你的`<link>`元素从你的 body 内容中分离出来，将其放在`<head>`中。
- 当使用`<link>`为网站创建一个`favicon`时，你的网站使用内容安全策略 (Content Security Policy，CSP) 来增强它的安全性，这种策略适用于`favicon`。如果你遇到`favicon`未加载的问题，验证`Content-Security-Policy`头的`img-src` directive 没有在阻止对它的访问。
- HTML 和 XHTML 规范为`<link>`元素定义了一些事件处理器 (event handler) ，但是对于它们的使用方法不明确。
- 在 XHTML 1.0 下，例如`<link>`的空元素需要一个尾斜杠：`<link />`。
- WebTV 支持 rel 使用`next`值，用于在一个 document series 中预加载下一页。

### 属性

- `as`
  - 该属性仅在`<link>`元素设置了 `rel="preload"` 时才能使用。
  - 它规定了`<link>`元素加载的内容的类型，对于内容的优先级、请求匹配、正确的内容安全策略的选择以及正确的 `Accept` 请求头的设置，这个属性是必需的。
- `crossorigin`
  - 此枚举属性指定在加载相关资源时是否必须使用 `CORS`.
  - 启用 `CORS` 的图片 可以在 `<canvas>` 元素中重复使用, 并避免其被污染. 可取的值如下:
    - `anonymous` 会发起一个跨域请求(即包含 `Origin:` HTTP 头). 但不会发送任何认证信息 (即不发送 cookie, X.509 证书和 HTTP 基本认证信息). 如果服务器没有给出源站凭证 (不设置 `Access-Control-Allow-Origin:` HTTP 头), 资源就会被污染并限制使用.
    - `use-credentials` 会发起一个带有认证信息 (发送 cookie, X.509 证书和 HTTP 基本认证信息) 的跨域请求 (即包含 `Origin:` HTTP 头). 如果服务器没有给出源站凭证 (不设置 `Access-Control-Allow-Origin:` HTTP 头), 资源就会被污染并限制使用.
  - 当不设置此属性时, 资源将会不使用 `CORS` 加载 (即不发送 `Origin:` HTTP 头), 这将阻止其在 `<canvas>` 元素中进行使用. 若设置了非法的值, 则视为使用 `anonymous`.
- `disabled`
  - 仅对于 `rel="stylesheet"`
  - 值为 Boolean
  - **指示是否应加载所描述的样式表并将其应用于文档。**
  - 如果在加载 HTML 时在 HTML 中指定了 Disabled，则在页面加载期间不会加载样式表。相反，如果禁用属性更改为 false 或删除时，样式表将按需加载。
- `href`
  - 此属性指定被链接资源的 URL。 URL 可以是绝对的，也可以是相对的。
- `hreflang`
  - 此属性指明了被链接资源的语言.其意义仅供参考。仅当设置了 `href` 属性时才应设置该属性。
- `importance`
  - 指示资源的相对重要性。 优先级提示使用以下值委托：
    - `auto`: 表示**没有偏好**。浏览器可以使用其自己的启发式方法来确定资源的优先级。
    - `high`: 向浏览器指示资源具有高优先级。
    - `low`: 向浏览器指示资源的优先级较低。
    - **只有存在 `rel=“preload”` 或 `rel=“prefetch”` 时，`importance` 属性才能用于`<link>`元素。**
- `integrity`
  - 包含行内元数据，它是一个你用浏览器获取的资源文件的哈希值，以 base64 编码的方式加的密，这样用户能用它来验证一个获取到的资源,在传送时未被非法篡改.
- `media`
  - 这个属性规定了外部资源适用的媒体类型。它的值必须是"媒体查询"。这个属性使得用户代理能选择最适合设备运行的媒体类型。
- `referrerpolicy`
  - 一个字符串，指示在获取资源时使用哪个引荐来源网址:
    - `no-referrer` 表示 Referer 标头将不会发送。
    - `no-referrer-when-downgrade` 的原始位置时不会发送任何 Referer 标头。 如果未指定其他政策，这是用户代理的默认行为。
    - `origin` 意味着引荐来源网址将是页面的来源，大致是方案，主机和端口。
    - `origin-when-cross-origin` 这意味着导航到其他来源将仅限于方案，主机和端口，而在同一来源上导航将包括引荐来源网址的路径。
    - `unsafe-url` 意味着引荐来源网址将包含来源和路径（但不包括片段，密码或用户名）。这种情况是不安全的，因为它可能会将来源和路径从受 TLS 保护的资源泄漏到不安全的来源。
- `rel`
  - 此属性命名链接文档与当前文档的关系。 该属性必须是链接类型值的用空格分隔的列表。
- `sizes`
  - 这个属性定义了包含相应资源的可视化媒体中的 icons 的大小。它只有在 rel 包含 icon 的 link 类型值。
  - 它可能有如下的规则:
    - `any` 表示图标可以按矢量格式缩放到任意大小，例如 `image/svg+xml`。
    - 一个由空白符分隔的尺寸列表。每一个都以`<width in pixels>x<height in pixels>` 或 `<width in pixels>X<height in pixels>`给出。尺寸列表中的每一个尺寸都必须包含在资源里。
  - **大多数的 icon 格式只能存储一个 icon。因此绝大多数使用 sizes 时只包含一个值。微软的 ICO 格式和苹果的 ICNS 格式都是这样，ICO 使用得更加广泛，推荐你使用它。**
  - **苹果的 IOS 系统并不支持这个属性，于是苹果的 IPhone 以及 IPad 使用特殊的、非标准的 link 类型值去定义作为 Web Clip 或开始占位符：`apple-touch-icon` 和 `apple-touch-startup-icon`。**
- `title`
  - 属性在`<link>`元素上有特殊的语义。当用于`<link rel="stylesheet">`时，它定义了一个首选样式表或备用样式表。不正确地使用它可能会导致样式表被忽略。
- `type`
  - 这个属性被用于定义链接的内容的类型。这个属性的值应该是像 text/html，text/css 等 MIME 类型。这个属性常用的用法是定义链接的样式表，最常用的值是表明了 CSS 的 text/css。

## @import

> CSS@规则，用于从其他样式表导入样式规则。
> 这些规则必须先于所有其他类型的规则，@charset 规则除外; 因为它不是一个嵌套语句，@import 不能在条件组的规则中使用。

### 使用

```css
@import url;
@import url list-of-media-queries;
```

- `url`
  - 是一个表示要引入资源位置的 `<string>` 或者 `<uri>` 。 这个 URL 可以是绝对路径或者相对路径。 要注意的是这个 URL 不需要指明一个文件；可以只指明包名，然后合适的文件会被自动选择 (e.g. chrome://communicator/skin/).
- `list-of-media-queries`
  - 是一个逗号分隔的 **媒体查询** 条件列表，决定通过 URL 引入的 CSS 规则 在什么条件下应用。如果浏览器不支持列表中的任何一条媒体查询条件，就不会引入 URL 指明的 CSS 文件。

**示例：**

```css
@import url('fineprint.css') print;
@import url('bluish.css') projection, tv;
@import 'custom.css';
@import url('chrome://communicator/skin/');
@import 'common.css' screen, projection;
@import url('landscape.css') screen and (orientation: landscape);
```

## 区别

- `@import` 是 CSS 提供的语法规则，只有导入样式表的作用；`link` 是 HTML 提供的标签，不仅可以加载 CSS 文件，还可以定义 RSS、rel 连接属性等。
- 加载页面时，`link` 标签引入的 CSS 被同时加载；`@import` 引入的 CSS 将在页面加载完毕后被加载。
- `@import` 是 CSS2.1 才有的语法，故只可在 IE5+ 才能识别；`link` 标签作为 HTML 元素，不存在兼容性问题。
- 可以通过 JS 操作 DOM ，插入 `link` 标签来改变样式；由于 DOM 方法是基于文档的，无法使用 `@import` 的方式插入样式。
- **权重** 样式的渲染会按照 [优先级](./05-选择器优先级.md) 来进行渲染
