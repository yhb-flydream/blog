# XSS

> **参考**
>
> [《前端安全系列（一）：如何防止 XSS 攻击？》(作者：美团技术团队)【来源：CSDN】](https://blog.csdn.net/MeituanTech/article/details/82877451)
>
> [《XSS(跨站脚本攻击)详解》(作者：墨鱼菜鸡)【来源：博客园】](https://www.cnblogs.com/csnd/p/11807592.html)
>
> [《跨站脚本漏洞(XSS)基础讲解》(作者：Jewel591)【来源：简书】](https://www.jianshu.com/p/4fcb4b411a66)

[TOC]

## 什么是 XSS 攻击

Cross Site Scripting（跨站脚本攻击）简称 XSS，为了和 CSS 区分，这里把攻击的第一个字母改成了 X，于是叫做 XSS，是一种**代码注入攻击**。

XSS 的本质是：**恶意代码未经过滤，与网站正常的代码混在一起；浏览器无法分辨哪些脚本是可信的，导致恶意脚本被执行。**

攻击者通过在目标网站上注入恶意脚本，使之在用户的浏览器上运行。利用这些恶意脚本，攻击者可获取用户的敏感信息如 Cookie、SessionID 等，进而危害数据安全。

而由于直接在用户的终端执行，恶意代码能够直接获取用户的信息，或者利用这些信息冒充用户向网站发起攻击者定义的请求。

在部分情况下，由于输入的限制，注入的恶意脚本比较短。但可以通过引入外部的脚本，并由浏览器执行，来完成比较复杂的攻击策略。

这里有一个问题：用户是通过哪种方法“注入”恶意脚本的呢？

不仅仅是业务上的“用户创造内容”可以进行注入，包括 URL 上的参数等都可以是攻击的来源。在处理输入时，以下内容都不可信：

- 来自用户创造的内容
- 来自第三方的链接
- URL 参数
- POST 参数
- Referrer （可能来自不可信的来源）
- Cookie （可能来自其他子域注入）

## XSS 分类

根据攻击的来源，XSS 攻击可分为：

- 存储型
- 反射型
- DOM 型

| 类型       |                  存储区 |          插入点 |
| ---------- | ----------------------: | --------------: |
| 存储型 XSS |              后端数据库 |            HTML |
| 反射型 XSS |                     URL |            HTML |
| DOM 型 XSS | 后端数据库/前端存储/URL | 前端 JavaScript |

> 存储区：恶意代码存放的位置。
> 插入点：由谁取得恶意代码，并插入到网页上。

### 存储型

存储型 XSS 的攻击步骤：

1. 攻击者将恶意代码提交到目标网站的数据库中。
2. 用户打开目标网站时，网站服务端将恶意代码从数据库取出，拼接在 HTML 中返回给浏览器。
3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

**常见于带有用户保存数据的网站功能，如论坛发帖、商品评论、用户私信等。**

### 反射型

反射型 XSS 的攻击步骤：

1. 攻击者构造出特殊的 URL，其中包含恶意代码。
2. 用户打开带有恶意代码的 URL 时，网站服务端将恶意代码从 URL 中取出，拼接在 HTML 中返回给浏览器。
3. 用户浏览器接收到响应后解析执行，混在其中的恶意代码也被执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

**反射型 XSS 漏洞常见于通过 URL 传递参数的功能，如网站搜索、跳转等。**

反射型 XSS 跟存储型 XSS 的区别是：**存储型 XSS 的恶意代码存在数据库里，反射型 XSS 的恶意代码存在 URL 里。**

由于需要用户主动打开恶意的 URL 才能生效，攻击者往往会结合多种手段诱导用户点击。

### DOM 型

DOM 型 XSS 的攻击步骤：

1. 攻击者构造出特殊的 URL，其中包含恶意代码。
2. 用户打开带有恶意代码的 URL。
3. 用户浏览器接收到响应后解析执行，前端 JavaScript 取出 URL 中的恶意代码并执行。
4. 恶意代码窃取用户数据并发送到攻击者的网站，或者冒充用户的行为，调用目标网站接口执行攻击者指定的操作。

DOM 型 XSS 跟前两种 XSS 的区别：**DOM 型 XSS 攻击中，取出和执行恶意代码由浏览器端完成，属于前端 JavaScript 自身的安全漏洞，而其他两种 XSS 都属于服务端的安全漏洞。**

## XSS 攻击的预防

XSS 攻击有两大要素：

- 攻击者提交恶意代码。
- 浏览器执行恶意代码。

### 输入过滤

输入侧过滤能够在某些情况下解决特定的 XSS 问题，但会引入很大的不确定性和乱码问题。**在防范 XSS 攻击时应避免此类方法。**

当然，对于明确的输入类型，例如数字、URL、电话号码、邮件地址等等内容，进行输入过滤还是必要的。

既然输入过滤并非完全可靠，我们就要通过“防止浏览器执行恶意代码”来防范 XSS。这部分分为两类：

- 防止 HTML 中出现注入。
- 防止 JavaScript 执行时，执行恶意代码。

### 预防存储型和反射性 XSS 攻击

存储型和反射型 XSS 都是在服务端取出恶意代码后，插入到响应 HTML 里的，攻击者刻意编写的“数据”被内嵌到“代码”中，被浏览器所执行。

- 改成纯前端渲染，把代码和数据分开
- 对 HTML 做充分转义

#### 纯前端渲染

纯前端渲染过程：

1. 浏览器先加载一个静态 HTML，此 HTML 中不包含任何跟业务先关的数据
2. 然后浏览器执行 HTML 中的 JavaScript
3. JavaScript 通过 ajax 加载业务数据，调用 DOM API 更新到页面上

#### 转义 HTML

如果拼接 HTML 是必要的，就需要采用合适的转义库，对 HTML 模板各处插入点进行充分的转义。

### 预防 DOM 型 XSS 攻击

DOM 型 XSS 攻击，实际上就是网站前端 JavaScript 代码本身不够严谨，把不可信的数据当作代码执行了。

在使用 `.innerHTML`、`.outerHTML`、`document.write()` 时要特别小心，不要把不可信的数据作为 HTML 插到页面上，而应尽量使用 `.textContent`、`.setAttribute()` 等。

如果用 Vue/React 技术栈，并且不使用 `v-html/dangerouslySetInnerHTML` 功能，就在前端 render 阶段避免 `innerHTML`、`outerHTML` 的 XSS 隐患。

DOM 中的内联事件监听器，如 `location`、`onclick`、`onerror`、`onload`、`onmouseover` 等，`<a>` 标签的 `href` 属性，JavaScript 的 `eval()`、`setTimeout()`、`setInterval()` 等，都能把字符串作为代码运行。如果不可信的数据拼接到字符串中传递给这些 API，很容易产生安全隐患，请务必避免。

```html
<!-- 内联事件监听器中包含恶意代码 -->
<img onclick="UNTRUSTED" onerror="UNTRUSTED" src="data:image/png," />

<!-- 链接内包含恶意代码 -->
<a href="UNTRUSTED">1</a>

<script>
  // setTimeout()/setInterval() 中调用恶意代码
  setTimeout('UNTRUSTED');
  setInterval('UNTRUSTED');

  // location 调用恶意代码
  location.href = 'UNTRUSTED';

  // eval() 中调用恶意代码
  eval('UNTRUSTED');
</script>
```

### 其他 XSS 防范措施

虽然在渲染页面和执行 JavaScript 时，通过谨慎的转义可以防止 XSS 的发生，但完全依靠开发的谨慎仍然是不够的。以下介绍一些通用的方案，可以降低 XSS 带来的风险和后果。

#### Content Security Policy

严格的 CSP 在 XSS 的防范中可以起到以下的作用：

- 禁止加载外域代码，防止复杂的攻击逻辑。
- 禁止外域提交，网站被攻击后，用户的数据不会泄露到外域。
- 禁止内联脚本执行（规则较严格，目前发现 GitHub 使用）。
- 禁止未授权的脚本执行（新特性，Google Map 移动版在使用）。
- 合理使用上报可以及时发现 XSS，利于尽快修复问题。

#### 输入内容长度控制

对于不受信任的输入，都应该限定一个合理的长度。虽然无法完全防止 XSS 发生，但可以增加 XSS 攻击的难度。

#### 其他安全措施

- HTTP-only Cookie: 禁止 JavaScript 读取某些敏感 Cookie，攻击者完成 XSS 注入后也无法窃取此 Cookie。
- 验证码：防止脚本冒充用户提交危险操作。

### XSS 的检测

1. 使用通用 XSS 攻击字符串手动检测 XSS 漏洞。
2. 使用扫描工具自动检测 XSS 漏洞。

```text
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

它能够检测到存在于 HTML 属性、HTML 文字内容、HTML 注释、跳转链接、内联 JavaScript 字符串、内联 CSS 样式表等多种上下文中的 XSS 漏洞，也能检测 eval()、setTimeout()、setInterval()、Function()、innerHTML、document.write() 等 DOM 型 XSS 漏洞，并且能绕过一些 XSS 过滤器。

只要在网站的各输入框中提交这个字符串，或者把它拼接到 URL 参数上，就可以进行检测了。

```text
http://xxx/search?keyword=jaVasCript%3A%2F*-%2F*%60%2F*%60%2F*%27%2F*%22%2F**%2F(%2F*%20*%2FoNcliCk%3Dalert()%20)%2F%2F%250D%250A%250d%250a%2F%2F%3C%2FstYle%2F%3C%2FtitLe%2F%3C%2FteXtarEa%2F%3C%2FscRipt%2F--!%3E%3CsVg%2F%3CsVg%2FoNloAd%3Dalert()%2F%2F%3E%3E
```

除了手动检测之外，还可以使用自动扫描工具寻找 XSS 漏洞
