# CSRF

> **参考**
>
> [《前端安全系列之二：如何防止 CSRF 攻击？》(作者：美团技术团队)【来源：掘金】](https://juejin.cn/post/6844903689702866952)

[TOC]

## 什么是 CSRF

> CSRF（Cross-site request forgery）跨站请求伪造：**攻击者诱导受害者进入第三方网站，在第三方网站中，向被攻击网站发送跨站请求。利用受害者在被攻击网站已经获取的注册凭证，绕过后台的用户验证，达到冒充用户对被攻击的网站执行某项操作的目的。**

一个典型的 CSRF 攻击有着如下的流程：

- 受害者登录 a.com，并保留了登录凭证（Cookie）。
- 攻击者引诱受害者访问了 b.com。
- b.com 向 a.com 发送了一个请求：a.com/act=xx。浏览器会…
- a.com 接收到请求后，对请求进行验证，并确认是受害者的凭证，误以为是受害者自己发送的请求。
- a.com 以受害者的名义执行了 act=xx。
- 攻击完成，攻击者在受害者不知情的情况下，冒充受害者，让 a.com 执行了自己定义的操作。

## 几种常见的攻击类型

### GET 类型

GET 类型的 CSRF 利用非常简单，只需要一个 HTTP 请求，一般会这样利用：

```html
<img src="http://bank.example/withdraw?amount=10000&for=hacker" />
```

在受害者访问含有这个 img 的页面后，浏览器会自动向`http://bank.example/withdraw?account=xiaoming&amount=10000&for=hacker`发出一次 HTTP 请求。bank.example 就会收到包含受害者登录信息的一次跨域请求。

### POST 类型

这种类型的 CSRF 利用起来通常使用的是一个自动提交的表单，如：

```html
<form action="http://bank.example/withdraw" method="POST">
  <input type="hidden" name="account" value="xiaoming" />
  <input type="hidden" name="amount" value="10000" />
  <input type="hidden" name="amount" value="10000" />
  <input type="hidden" name="for" value="hacker" />
</form>
<script>
  document.forms[0].submit()
</script>
```

访问该页面后，表单会自动提交，相当于模拟用户完成了一次 POST 操作。

**POST 类型的攻击通常比 GET 要求更加严格一点，但仍并不复杂。任何个人网站、博客，被黑客上传页面的网站都有可能是发起攻击的来源，后端接口不能将安全寄托在仅允许 POST 上面。**

### 链接类型

链接类型的 CSRF 并不常见，比起其他两种用户打开页面就中招的情况，这种需要用户点击链接才会触发。

这种类型通常是*在论坛中发布的图片中嵌入恶意链接*，或者*以广告的形式诱导用户中招*，攻击者通常会以比较夸张的词语诱骗用户点击，例如：

```html
<a href="http://test.com/csrf/withdraw.php?amount=1000&for=hacker" taget="_blank"> 重磅消息！！</a>
```

由于之前用户登录了信任的网站 A，并且保存登录状态，只要用户主动访问上面的这个 PHP 页面，则表示攻击成功。

## CSRF 的特点

- 攻击一般发起在第三方网站，而不是被攻击的网站。被攻击的网站无法防止攻击发生。
- 攻击利用受害者在被攻击网站的登录凭证，冒充受害者提交操作；而不是直接窃取数据。
- 整个过程攻击者并不能获取到受害者的登录凭证，仅仅是“冒用”。
- 跨站请求可以用各种方式：图片 URL、超链接、CORS、Form 提交等等。部分请求方式可以直接嵌入在第三方论坛、文章中，难以进行追踪。

CSRF 通常是跨域的，因为外域通常更容易被攻击者掌控。但是如果本域下有容易被利用的功能，比如可以发图和链接的论坛和评论区，攻击可以直接在本域下进行，而且这种攻击更加危险。

## 防护策略

CSRF 通常从第三方网站发起，被攻击的网站无法防止攻击发生，只能通过增强自己网站针对 CSRF 的防护能力来提升安全性。

上文中讲了 CSRF 的两个特点：

- CSRF（通常）发生在第三方域名。
- CSRF 攻击者不能获取到 Cookie 等信息，只是使用。

针对这两点，我们可以专门制定防护策略，如下：

- 阻止不明外域的访问
  - 同源检测
  - Samesite Cookie
- 提交时要求附加本域才能获取的信息
  - CSRF Token
  - 双重 Cookie 验证

### 同源检测

既然 CSRF 大多来自第三方网站，那么我们就直接禁止外域（或者不受信任的域名）对我们发起请求。

那么问题来了，我们如何判断请求是否来自外域呢？

在 HTTP 协议中，每一个异步请求都会携带两个 Header，用于标记来源域名：

- Origin Header
- Referer Header

这两个 Header 在浏览器发起请求时，大多数情况会自动带上，并且不能由前端自定义内容。 服务器可以通过解析这两个 Header 中的域名，确定请求的来源域。

**使用 Origin Header 确定来源域名：**

在部分与 CSRF 有关的请求中，请求的 Header 中会携带 Origin 字段。字段内包含请求的域名（不包含 path 及 query）。

如果 Origin 存在，那么直接使用 Origin 中的字段确认来源域名就可以。

但是 Origin 在以下两种情况下并不存在：

- **IE11 同源策略：** IE 11 不会在跨站 CORS 请求上添加 Origin 标头，Referer 头将仍然是唯一的标识。最根本原因是因为 IE 11 对同源的定义和其他浏览器有不同，有两个主要的区别，可以参考[MDN Same-origin_policy#IE_Exceptions](https://developer.mozilla.org/en-US/docs/Web/Security/Same-origin_policy#IE_Exceptions)
- **302 重定向：** 在 302 重定向之后 Origin 不包含在重定向的请求中，因为 Origin 可能会被认为是其他来源的敏感信息。对于 302 重定向的情况来说都是定向到新的服务器上的 URL，因此浏览器不想将 Origin 泄漏到新的服务器上。

**使用 Referer Header 确定来源域名：**

根据 HTTP 协议，在 HTTP 头中有一个字段叫 Referer，记录了该 HTTP 请求的*来源地址*。
对于 Ajax 请求，图片和 script 等资源请求，Referer 为*发起请求的页面地址*。
对于页面跳转，Referer 为*打开页面历史记录的前一个页面地址*。
因此我们使用 Referer 中链接的 Origin 部分可以得知请求的来源域名。

这种方法并非万无一失，Referer 的值是由浏览器提供的，虽然 HTTP 协议上有明确的要求，但是每个浏览器对于 Referer 的具体实现可能有差别，并不能保证浏览器自身没有安全漏洞。

使用验证 Referer 值的方法，就是把安全性都依赖于第三方（即浏览器）来保障，从理论上来讲，这样并不是很安全。在部分情况下，攻击者可以隐藏，甚至修改自己请求的 Referer。

2014 年，W3C 的 Web 应用安全工作组发布了 Referrer Policy 草案，对浏览器该如何发送 Referer 做了详细的规定。截止现在新版浏览器大部分已经支持了这份草案，我们终于可以灵活地控制自己网站的 Referer 策略了。新版的 Referrer Policy 规定了五种 Referer 策略：No Referrer、No Referrer When Downgrade、Origin Only、Origin When Cross-origin、和 Unsafe URL。之前就存在的三种策略：never、default 和 always，在新标准里换了个名称。他们的对应关系如下：

| 策略名称                   | 属性值（新）                     | 属性值（旧） |
| -------------------------- | -------------------------------- | ------------ |
| No Referrer                | no-Referrer                      | never        |
| No Referrer When Downgrade | no-Referrer-when-downgrade       | default      |
| Origin Only                | (same or strict) origin          | origin       |
| Origin When Cross Origin   | (strict) origin-when-crossorigin | -            |
| Unsafe URL                 | unsafe-url                       | always       |

根据上面的表格因此需要把 Referrer Policy 的策略设置成 same-origin，对于同源的链接和引用，会发送 Referer，referer 值为 Host 不带 Path；跨域访问则不携带 Referer。例如：aaa.com 引用 bbb.com 的资源，不会发送 Referer。

设置 Referrer Policy 的方法有三种：

1. 在 CSP 设置
2. 页面头部增加 meta 标签
3. a 标签增加 referrerpolicy 属性

上面说的这些比较多，但我们可以知道一个问题：攻击者可以在自己的请求中隐藏 Referer。如果攻击者将自己的请求这样填写：

```html
<img src="http://bank.example/withdraw?amount=10000&for=hacker" referrerpolicy="no-referrer" />
```

那么这个请求发起的攻击将不携带 Referer。

另外在以下情况下 Referer 没有或者不可信：

1. IE6、7 下使用 window.location.href=url 进行界面的跳转，会丢失 Referer。
2. IE6、7 下使用 window.open，也会缺失 Referer。
3. HTTPS 页面跳转到 HTTP 页面，所有浏览器 Referer 都丢失。
4. 点击 Flash 上到达另外一个网站的时候，Referer 的情况就比较杂乱，不太可信。

**无法确认来源域名情况：**

当 Origin 和 Referer 头文件不存在时该怎么办？如果 Origin 和 Referer 都不存在，建议直接进行阻止，特别是如果您没有使用随机 CSRF Token（参考下方）作为第二次检查。

**如何阻止外域请求：**

通过 Header 的验证，我们可以知道发起请求的来源域名，这些来源域名可能是网站本域，或者子域名，或者有授权的第三方域名，又或者来自不可信的未知域名。

我们已经知道了请求域名是否是来自不可信的域名，我们直接阻止掉这些的请求，就能防御 CSRF 攻击了吗？

且慢！当一个请求是页面请求（比如网站的主页），而来源是搜索引擎的链接（例如百度的搜索结果），也会被当成疑似 CSRF 攻击。所以在判断的时候需要过滤掉页面请求情况，通常 Header 符合以下情况：

```txt
Accept: text/html
Method: GET
```

但相应的，页面请求就暴露在了 CSRF 的攻击范围之中。如果你的网站中，在页面的 GET 请求中对当前用户做了什么操作的话，防范就失效了。

例如，下面的页面请求：

```txt
GET https://example.com/addComment?comment=XXX&dest=orderId
```

注：这种严格来说并不一定存在 CSRF 攻击的风险，但仍然有很多网站经常把主文档 GET 请求挂上参数来实现产品功能，但是这样做对于自身来说是存在安全风险的。

另外，前面说过，CSRF 大多数情况下来自第三方域名，但并不能排除本域发起。如果攻击者有权限在本域发布评论（含链接、图片等，统称 UGC），那么它可以直接在本域发起攻击，这种情况下同源策略无法达到防护的作用。

**综上所述：同源验证是一个相对简单的防范方法，能够防范绝大多数的 CSRF 攻击。但这并不是万无一失的，对于安全性要求较高，或者有较多用户输入内容的网站，我们就要对关键的接口做额外的防护措施。**

### CSRF Token

前面讲到 CSRF 的另一个特征是，攻击者无法直接窃取到用户的信息（Cookie，Header，网站内容等），仅仅是冒用 Cookie 中的信息。

而 CSRF 攻击之所以能够成功，是因为服务器误把攻击者发送的请求当成了用户自己的请求。那么我们可以要求所有的用户请求都携带一个 CSRF 攻击者无法获取到的 Token。服务器通过校验请求是否携带正确的 Token，来把正常的请求和攻击的请求区分开，也可以防范 CSRF 的攻击。

### 分布式校验

### 双重 Cookie 验证

双重 Cookie 采用以下流程：

- 在用户访问网站页面时，向请求域名注入一个 Cookie，内容为随机字符串（例如 `csrfcookie=v8g9e4ksfhw`）。
- 在前端向后端发起请求时，取出 Cookie，并添加到 URL 的参数中（接上例 `POST https://www.a.com/comment?csrfcookie=v8g9e4ksfhw`）。
- 后端接口验证 Cookie 中的字段与 URL 参数中的字段是否一致，不一致则拒绝。

此方法相对于 CSRF Token 就简单了许多。可以直接通过前后端拦截的的方法自动化实现。后端校验也更加方便，只需进行请求中字段的对比，而不需要再进行查询和存储 Token。

### Samesite Cookie 属性

防止 CSRF 攻击的办法已经有上面的预防措施。为了从源头上解决这个问题，Google 起草了一份草案来改进 HTTP 协议，那就是为 Set-Cookie 响应头新增 Samesite 属性，它用来标明这个 Cookie 是个“同站 Cookie”，同站 Cookie 只能作为第一方 Cookie，不能作为第三方 Cookie，Samesite 有两个属性值，分别是 Strict 和 Lax。

## 防止网站被利用

前面所说的，都是被攻击的网站如何做好防护。而非防止攻击的发生，CSRF 的攻击可以来自：

- 攻击者自己的网站。
- 有文件上传漏洞的网站。
- 第三方论坛等用户内容。
- 被攻击网站自己的评论功能等。

对于来自黑客自己的网站，我们无法防护。但对其他情况，那么如何防止自己的网站被利用成为攻击的源头呢？

- 严格管理所有的上传接口，防止任何预期之外的上传内容（例如HTML）。
- 添加 `Header X-Content-Type-Options: nosniff` 防止黑客上传HTML内容的资源（例如图片）被解析为网页。
- 对于用户上传的图片，进行转存或者校验。不要直接使用用户填写的图片链接。
- 当前用户打开其他用户填写的链接时，需告知风险（这也是很多论坛不允许直接在内容中发布外域链接的原因之一，不仅仅是为了用户留存，也有安全考虑）。

## CSRF其他防范措施

### CSRF测试

### CSRF监控
