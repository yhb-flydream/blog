<!--
 * @Author: yaohebin
 * @Date: 2021-02-01 17:47:02
 * @LastEditTime: 2022-07-29 14:33:24
 * @LastEditors: yaohebin
 * @Description: sourceMap
-->

# sourceMap

> **参考**
>
> [Webpack 中文网站 - devtool](https://www.webpackjs.com/configuration/devtool/)
>
> [Webpack 中文网站 - 使用 source map](https://www.webpackjs.com/guides/development/#%E4%BD%BF%E7%94%A8-source-map)
>
> [《JavaScript Source Map 详解》(作者：阮一峰)【来源：阮一峰的网络日志】](https://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html)
>
> [《Webpack 中的 sourcemap》(作者：axl234)【来源：博客园】](https://www.cnblogs.com/axl234/p/6500534.html)
>
> [《Source Map 的原理探究》(作者：Fundebug)【来源：Fundebug】](https://blog.fundebug.com/2018/10/12/understanding_frontend_source_map/)

[TOC]

## 配置使用

控制是否使用、怎么使用 sourceMap 的是 devtool 字段，在 webpack 中的配置是：

```js
module.exports = {
  entry: {},
  output: {},
  module: {},
  devtool: 'inline-source-map',
  plugins: [],
};
```

### devtool 的属性值有多个可选

| devtool                        | 构建速度 | 重新构建速度 | 生产环境 | 品质(quality)          |
| :----------------------------- | :------- | :----------- | :------- | :--------------------- |
| (none)                         | +++      | +++          | yes      | 打包后的代码           |
| eval                           | +++      | +++          | no       | 生成后的代码           |
| cheap-eval-source-map          | +        | ++           | no       | 转换过的代码（仅限行） |
| cheap-module-eval-source-map   | o        | ++           | no       | 原始源代码（仅限行）   |
| eval-source-map                | --       | +            | no       | 原始源代码             |
| cheap-source-map               | +        | o            | no       | 转换过的代码（仅限行） |
| cheap-module-source-map        | o        | -            | no       | 原始源代码（仅限行）   |
| inline-cheap-source-map        | +        | o            | no       | 转换过的代码（仅限行） |
| inline-cheap-module-source-map | o        | -            | no       | 原始源代码（仅限行）   |
| source-map                     | --       | --           | yes      | 原始源代码             |
| inline-source-map              | --       | --           | no       | 原始源代码             |
| hidden-source-map              | --       | --           | yes      | 原始源代码             |
| nosources-source-map           | --       | --           | yes      | 无源代码内容           |

> `+++` 非常快速, `++` 快速, `+` 比较快, `o` 中等, `-` 比较慢, `--` 慢

其中一些值适用于开发环境，一些适用于生产环境。对于开发环境，通常希望更快速的 source map，需要添加到 bundle 中以增加体积为代价，但是对于生产环境，则希望更精准的 source map，需要从 bundle 中分离并独立存在。

## 关于品质说明

- **打包后的代码**：将所有生成的代码视为一大块代码。你看不到相互分离的模块。
- **生成后的代码**：每个模块相互分离，并用模块名称进行注释。可以看到 webpack 生成的代码。
- **转换过的代码**：每个模块相互分离，并用模块名称进行注释。可以看到 webpack 转换前、loader 转译后的代码。
- **原始源代码**：每个模块相互分离，并用模块名称进行注释。你会看到转译之前的代码，正如编写它时。这取决于 loader 支持。
- **无源代码内容**：source map 中不包含源代码内容。浏览器通常会尝试从 web 服务器或文件系统加载源代码。
- **（仅限行）**：source map 被简化为每行一个映射。这通常意味着每个语句只有一个映射（假设你使用这种方式）。这会妨碍你在语句级别上调试执行，也会妨碍你在每行的一些列上设置断点。与压缩后的代码组合后，映射关系是不可能实现的，因为压缩工具通常只会输出一行。

### 对于开发环境的配置选择

以下选项非常适合开发环境：

- `eval`：每个模块都使用 `eval()` 执行，并且都有 `//@ sourceURL`。此选项会非常快地构建。主要缺点是，由于会映射到转换后的代码，而不是映射到原始代码（没有从 loader 中获取 source map），所以不能正确的显示行数。
- `eval-source-map`：每个模块使用 eval() 执行，并且 source map 转换为 DataUrl 后添加到 eval() 中。初始化 source map 时比较慢，但是会在重新构建时提供比较快的速度，并且生成实际的文件。行数能够正确映射，因为会映射到原始代码中。它会生成用于开发环境的最佳品质的 source map。
- `cheap-eval-source-map`：类似 eval-source-map，每个模块使用 eval() 执行。这是 "cheap(低开销)" 的 source map，因为它没有生成列映射(column mapping)，只是映射行数。它会忽略源自 loader 的 source map，并且仅显示转译后的代码，就像 eval devtool。
- `cheap-module-eval-source-map`：类似 cheap-eval-source-map，并且，在这种情况下，源自 loader 的 source map 会得到更好的处理结果。然而，loader source map 会被简化为每行一个映射(mapping)。

### 对于生产环境的配置选择

这些选项通常用于生产环境中：

- `(none)（省略 devtool 选项）`：不生成 source map。这是一个不错的选择。
- `source-map`：整个 source map 作为一个单独的文件生成。它为 bundle 添加了一个引用注释，以便开发工具知道在哪里可以找到它。
  - _你应该将你的服务器配置为，不允许普通用户访问 source map 文件！_
- `hidden-source-map`：与 source-map 相同，但不会为 bundle 添加引用注释。如果你只想 source map 映射那些源自错误报告的错误堆栈跟踪信息，但不想为浏览器开发工具暴露你的 source map，这个选项会很有用。
  - _你不应将 source map 文件部署到 web 服务器。而是只将其用于错误报告工具。_
- `nosources-source-map`：创建的 source map 不包含 sourcesContent(源代码内容)。它可以用来映射客户端上的堆栈跟踪，而无须暴露所有的源代码。你可以将 source map 文件部署到 web 服务器。
  - _这仍然会暴露反编译后的文件名和结构，但它不会暴露原始代码。_
