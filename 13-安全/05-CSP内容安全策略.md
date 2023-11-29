<!--
 * @Author: yaohebin
 * @Date: 2021-02-01 17:19:57
 * @LastEditTime: 2023-11-29 17:51:04
 * @LastEditors: yaohebin
 * @Description: CSP 内容安全策略
-->

# CSP 内容安全策略

> **参考**
>
> [《内容安全策略( CSP )》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/web/http/csp)
>
> [《内容安全策略（CSP）详解》(作者：我最犀利蛮鬼)【来源：CSDN】](https://blog.csdn.net/qq_37943295/article/details/79978761)
>
> [《Web 安全之内容安全策略（Content-Security-Policy,CSP）详解 》(作者：斑林鸽的代码世界)【来源：博客园】](https://www.cnblogs.com/mutudou/p/14373644.html)

[TOC]

内容安全策略 (CSP) 是一个额外的安全层，用于检测并削弱某些特定类型的攻击，包括**跨站脚本 (XSS (en-US))** 和**数据注入攻击**等。无论是数据盗取、网站内容污染还是散发恶意软件，这些攻击都是主要的手段。

## 威胁

### 跨站脚本攻击

CSP 的主要目标是减少和报告 XSS 攻击 ，XSS 攻击利用了浏览器对于从服务器所获取的内容的信任。恶意脚本在受害者的浏览器中得以运行，因为浏览器信任其内容来源，即使有的时候这些脚本并非来自于它本该来的地方。

CSP 通过指定有效域——即浏览器认可的可执行脚本的有效来源——使服务器管理者有能力减少或消除 XSS 攻击所依赖的载体。一个 CSP 兼容的浏览器将会仅执行从白名单域获取到的脚本文件，忽略所有的其他脚本 (包括内联脚本和 HTML 的事件处理属性)。

**作为一种终极防护形式，始终不允许执行脚本的站点可以选择全面禁止脚本执行。**

### 数据包嗅探攻击

除限制可以加载内容的域，服务器还可指明哪种协议允许使用；比如 (从理想化的安全角度来说)，服务器可指定所有内容必须通过 HTTPS 加载。一个完整的数据安全传输策略不仅强制使用 HTTPS 进行数据传输，也为所有的 cookie 标记安全标识 cookies with the secure flag，并且提供自动的重定向使得 HTTP 页面导向 HTTPS 版本。网站也可以使用 Strict-Transport-Security HTTP 头部确保连接它的浏览器只使用加密通道。

## 使用 CSP

### 制定策略

使用 `Content-Security-Policy` HTTP 头部 来指定你的策略，像这样:

```text
Content-Security-Policy: policy
```

policy 参数是一个包含了各种描述你的 CSP 策略指令的字符串。

### 描述策略

一个策略由一系列策略指令所组成，每个策略指令都描述了一个针对某个特定类型资源以及生效范围的策略。

你的策略应当包含一个 `default-src` 策略指令，在其他资源类型没有符合自己的策略时应用该策略。

一个策略可以包含 `default-src` 或者 `script-src (en-US)` 指令来防止内联脚本运行, 并杜绝`eval()`的使用。

一个策略也可包含一个 `default-src` 或 `style-src (en-US)` 指令去限制来自一个 `<style>` 元素或者 style 属性的內联样式。
