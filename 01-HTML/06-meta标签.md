<!--
 * @Author: yaohebin
 * @Date: 2022-06-17 08:36:20
 * @LastEditTime: 2022-06-23 10:02:04
 * @LastEditors: yaohebin
 * @Description: meta标签
-->

# meta 标签

> **参考**
>
> [《`<meta>`：文档级元数据元素》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/HTML/Element/meta)
>
> [《HTML `<meta>` 标签》【来源：MDN】](https://www.w3school.com.cn/tags/tag_meta.asp)
>
> [《HTML 中 meta 大全》【来源：php 中文网】](https://www.php.cn/div-tutorial-455933.html)

`<meta>` 元素可提供有关页面的**元信息**（meta-information），比如针对搜索引擎和更新频度的描述和关键词。

`<meta>` 标签位于文档的头部，不包含任何内容。`<meta>` 标签的属性定义了与文档相关联的名称/值对。

- `<meta>` 标签永远位于 head 元素内部。
- 元数据总是以名称/值的形式被成对传递的。

## 属性

| 属性       | 值                                                                                       | 描述                                         |
| ---------- | ---------------------------------------------------------------------------------------- | -------------------------------------------- |
| charset    | character_set                                                                            | 规定 HTML 文档的字符编码。                   |
| content    | text                                                                                     | 定义与 http-equiv 或 name 属性相关的元信息。 |
| http-equiv | content-security-policy<br />content-type<br />default-style<br />refresh                | 把 content 属性关联到 HTTP 头部。            |
| name       | application-name<br />author<br />description<br />generator<br />keywords<br />viewport | 把 content 属性关联到一个名称。              |
| scheme     | some_text                                                                                | 定义用于翻译 content 属性值的格式。          |

### charset

定义文档的字符编码。如 utf-8，gbk，gb2312。

```html
<meta charset="utf-8" />
```

### name

name 属性提供了 名称/值 对中的**名称**（**值**由 content 提供）。

HTML 和 XHTML 标签都没有指定任何预先定义的 `<meta>` 名称。通常情况下，可以自由使用对自己和源文档的读者来说富有意义的名称。

```html
<!-- 页面关键词 -->
<meta name="keywords" content="" />

<!-- 页面描述 -->
<meta name="description" content="不超过150个字符" />

<!-- 网页作者 -->
<meta name="author" content="name, email@gmail.com" />

<!-- 强制Chromium内核，作用于360浏览器、QQ浏览器等国产双核浏览器 -->
<meta name="renderer" content="webkit" />

<!-- 强制Chromium内核，作用于其他双核浏览器 -->
<meta name="force-rendering" content="webkit" />

<!-- 
  设置视窗大小
  
  width    设置layout viewport  的宽度，为一个正整数，或字符串"width-device"
  initial-scale    设置页面的初始缩放值，为一个数字，可以带小数
  minimum-scale    允许用户的最小缩放值，为一个数字，可以带小数
  maximum-scale    允许用户的最大缩放值，为一个数字，可以带小数
  shrink-to-fit=no IOS9中要想前面的属性起作用需要加上这个
  height    设置layout viewport  的高度，这个属性对我们并不重要，很少使用
  user-scalable    是否允许用户进行缩放，值为"no"或"yes", no 代表不允许，yes代表允许
-->
<meta name="viewport" content="width=device-width, initial-scale=1.0, shrink-to-fit=no, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no" />

<!-- 
  搜索引擎抓取
  
  all：文件将被检索，且页面上的链接可以被查询； 
  none：文件将不被检索，且页面上的链接不可以被查询；
  index：文件将被检索； 
  follow：页面上的链接可以被查询； 
  noindex：文件将不被检索； 
  nofollow：页面上的链接不可以被查询。 
-->
<meta name="robots" content="index,follow" />

<!-- 忽略页面中的数字识别为电话，忽略email识别-->
<meta name="format-detection" content="telphone=no, email=no" />

<!-- IOS begin -->

<!-- 添加到主屏后的标题（iOS 6 新增） -->
<meta name="apple-mobile-web-app-title" content="标题" />

<!-- 当网站添加到主屏幕快速启动方式，可隐藏地址栏，仅针对ios的safari (ios7.0版本以后，safari上已看不到效果) -->
<meta name="apple-mobile-web-app-capable" content="yes" />

<!-- 是否启用 WebApp 全屏模式，删除苹果默认的工具栏和菜单栏 -->
<meta name="apple-touch-fullscreen" content="yes" />

<!-- 添加智能 App 广告条 Smart App Banner（iOS 6+ Safari） -->
<meta name="apple-itunes-app" content="app-id=myAppStoreID, affiliate-data=myAffiliateData, app-argument=myURL" />

<!-- 设置苹果工具栏颜色:默认值为 default（白色），可以定为 black（黑色）和 black-translucent（灰色半透明） -->
<meta name="apple-mobile-web-app-status-bar-style" content="black" />

<!-- 针对手持设备优化，主要是针对一些老的不识别viewport的浏览器，比如黑莓 -->
<meta name="HandheldFriendly" content="true" />

<!-- 微软的老式浏览器 -->
<meta name="MobileOptimized" content="320" />

<!-- uc强制竖屏 -->
<meta name="screen-orientation" content="portrait" />

<!-- QQ强制竖屏 -->
<meta name="x5-orientation" content="portrait" />

<!-- UC强制全屏 -->
<meta name="full-screen" content="yes" />

<!-- QQ强制全屏 -->
<meta name="x5-fullscreen" content="true" />

<!-- UC应用模式 -->
<meta name="browsermode" content="application" />

<!-- QQ应用模式 -->
<meta name="x5-page-mode" content="app" />

<!-- windows phone 点击无高光 -->
<meta name="msapplication-tap-highlight" content="no" />

<!-- IOS end -->

<!-- Windows 8 磁贴颜色 -->
<meta name="msapplication-TileColor" content="#000" />

<!-- Windows 8 磁贴图标 -->
<meta name="msapplication-TileImage" content="icon.png" />

<!-- 标注网站的版权信息。 -->
<meta name="copyright" content="">
```

```html
<!-- 
  iOS 图标 begin 
  网站添加至ios桌面时的图标
-->

<!-- iPhone 和 iTouch，默认 57x57 像素，必须有 -->
<link rel="apple-touch-icon-precomposed" sizes="57x57" href="table_ico57.png" />

<!-- Retina iPhone 和 Retina iTouch，114x114 像素，可以没有，但推荐有 -->
<link rel="apple-touch-icon-precomposed" sizes="72x72" href="table_ico72.png" />
<link rel="apple-touch-icon-precomposed" sizes="114x114" href="table_ico114.png" />

<!-- Retina iPad，144x144 像素，可以没有，但推荐有 -->
<link rel="apple-touch-icon-precomposed" sizes="144x144" href="table_ico144.png" />

<!-- iOS 启动画面 begin -->

<!-- iPad 竖屏 768 x 1004（标准分辨率） -->
<link rel="apple-touch-startup-image" sizes="768x1004" href="/splash-screen-768x1004.png" />

<!-- iPad 横屏 1024x748（标准分辨率） -->
<link rel="apple-touch-startup-image" sizes="1024x748" href="/Default-Portrait-1024x748.png" />

<!-- iPad 竖屏 1536x2008（Retina） -->
<link rel="apple-touch-startup-image" sizes="1536x2008" href="/splash-screen-1536x2008.png" />

<!-- iPad 横屏 2048x1496（Retina） -->
<link rel="apple-touch-startup-image" sizes="2048x1496" href="/splash-screen-2048x1496.png" />

<!-- iPhone/iPod Touch 竖屏 320x480 (标准分辨率) -->
<link rel="apple-touch-startup-image" href="/splash-screen-320x480.png" />

<!-- iPhone/iPod Touch 竖屏 640x960 (Retina) -->
<link rel="apple-touch-startup-image" sizes="640x960" href="/splash-screen-640x960.png" />

<!-- iPhone 5/iPod Touch 5 竖屏 640x1136 (Retina) -->
<link rel="apple-touch-startup-image" sizes="640x1136" href="/splash-screen-640x1136.png" />

<!-- 添加 RSS 订阅 -->
<link rel="alternate" type="application/rss+xml" title="RSS" href="/rss.xml" />
```

```html
<!-- sns 社交标签 begin -->

<!-- 参考微博API -->
<meta property="og:type" content="类型" />

<meta property="og:url" content="URL地址" />

<meta property="og:title" content="标题" />

<meta property="og:image" content="图片" />

<meta property="og:description" content="描述" />

<!-- sns 社交标签 end -->
```

### http-equiv

http-equiv 属性为 名称/值 对提供了 **名称**（**值**由 content 提供）。

并指示服务器在发送实际的文档之前先在要传送给浏览器的 MIME 文档头部包含名称/值对。

当服务器向浏览器发送文档时，会先发送许多名称/值对。虽然有些服务器会发送许多这种名称/值对，但是所有服务器都至少要发送一个：`content-type:text/html`。这将告诉浏览器准备接受一个 HTML 文档。

```html
<!-- 如果有安装 Google Chrome Frame 插件则强制为Chromium内核，否则强制本机支持的最高版本IE内核，作用于IE浏览器 -->
<meta http-equiv="X-UA-Compatible" content="IE=Edge,chrome=1" />

<!-- 如果网页过期，那么存盘的cookie将被删除。必须使用GMT的时间格式。 -->
<meta http-equiv="Set-Cookie" content="cookievalue=xxx; expires=Friday,12-Jan-2001 18:18:18 GMT; path=/" />

<!-- 用于设定网页的到期时间。一旦网页过期，必须到服务器上重新传输。 -->
<meta http-equiv="expires" content="时间" />

<!-- 告诉浏览器在【数字】秒后跳转到【一个网址】 -->
<meta http-equiv="Refresh" content="5;URL=https://www.baidu.com/" />

<!-- 设定页面使用的字符集。 -->
<meta http-equiv="content-Type" content="text/html; charset=utf-8" />

<!-- 禁止浏览器从本地计算机的缓存中访问页面内容。访问者将无法脱机浏览。 -->
<meta http-equiv="Pragma" content="no-cache" />

<!-- 用来防止别人在iframe(框架)里调用自己的页面，这也算是一个非常实用的属性。 -->
<meta http-equiv="Window-target" content="_top" />

<!-- 
  指导浏览器如何缓存某个响应以及缓存多长时间

  no-cache: 先发送请求，与服务器确认该资源是否被更改，如果未被更改，则使用缓存。
  no-store: 不允许缓存，每次都要去服务器上，下载完整的响应。（安全措施）
  public : 缓存所有响应，但并非必须。因为max-age也可以做到相同效果
  private : 只为单个用户缓存，因此不允许任何中继进行缓存。（比如说CDN就不允许缓存private的响应）
  maxage : 表示当前请求开始，该响应在多久内能被缓存和重用，而不去服务器重新请求。例如：max-age=60表示响应可以再缓存和重用 60 秒。
-->
<meta http-equiv="cache-control" content="no-cache">

<!-- 不让百度转码 -->
<meta http-equiv="Cache-Control" content="no-siteapp" />
```

### content

content 属性提供了 名称/值 对中的 **值**。该值可以是任何有效的字符串。

content 属性始终要和 name 属性或 http-equiv 属性一起使用。

### scheme

scheme 属性用于指定要用来翻译属性值的方案。此方案应该在由 `<head>` 标签的 profile 属性指定的概况文件中进行了定义。
