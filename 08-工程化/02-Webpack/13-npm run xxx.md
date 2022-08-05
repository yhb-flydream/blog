<!--
 * @Author: yaohebin
 * @Date: 2022-06-06 08:26:53
 * @LastEditTime: 2022-08-05 08:34:00
 * @LastEditors: yaohebin
 * @Description: npm run xxx
-->

# npm run xxx

> **参考**
>
> [《前端项目中运行 npm run xxx 的时候发生了什么？》(作者：mb5ff58fc86bda8)【来源：51cto 博客】](https://blog.51cto.com/u_15077533/4531157)
>
> [《三面面试官：运行 npm run xxx 的时候发生了什么？》(作者：阳光是 sunny)【来源：掘金】](https://juejin.cn/post/7078924628525056007)

[TOC]

## 查找 package.json 中的 scripts，执行对应的命令

执行 `npm run xxxx` 的时候会首先去查找 package.json 中的 scripts 中定义的各种命令（如 `npm run dev` 会去找 scripts 中定义的 dev 的命令），然后执行对应的命令（`"dev": "vue-cli-service serve"`，则执行 `vue-cli-service serve`）

```json
{
  "scripts": {
    "dev": "vue-cli-service serve"
  }
}
```

## 为什么不直接执行 `vue-cli-service serve` 而要执行 `npm run dev`

因为可能没有全局的 `vue-cli-service` 命令

## 既然没有全局的 `vue-cli-service serve`，那么执行 `npm run dev` 最终也还要执行 `vue-cli-service serve`，为什么不会报错

因为在通过 `npm i xxxx` 的时候，会在 `node_modules/.bin` 目录下创建好对应的可执行文件，执行 `npm run dev` 的时候执行 `vue-cli-service serve`，npm 会到 `node_modules/.bin` 查找可执行文件脚本，当于执行了 `./node_modules/.bin/vue-cli-service.cmd serve`

## `.bin` 目录下的软连接是从哪里来的，又是怎么知道软连接执行的哪里

比如在使用 `vue-cli-service` 时，在项目的 `package-lock.json` 文件中，可以找到 `vue-cli-service`，bin 字段标出了其 `bin/vue-cli-service.js` 所在的位置。可知当执行 `npm i` 安装项目依赖的时候，npm 将 `bin/vue-cli-service.js` 作为 bin 的声明，npm 在读取到该配置后，会将该指向的文件链接到 `./node_modules/.bin` 目录下，而 npm 还会自动把 `node_modules/.bin` 加入 `$PATH`，这样就可以直接作为命令运行依赖，而不用全局安装

在使用全局安装 `npm i xxxx -g` 时，那么 npm 会将全局的 bin 文件加入到全局的 `$PATH`，这样就可以在全局执行命令

## `./node_modules/.bin` 目录下，三个同名 `vue-cli-service` 文件的作用

- vue-cli-service
  - unix 系默认的可执行文件，必须输入完整文件名
- vue-cli-service.cmd
  - windows cmd 中默认的可执行文件，当我们不添加后缀名时，自动根据 pathext 查找文件
- vue-cli-service.ps1
  - Windows PowerShell 中可执行文件，可以跨平台
