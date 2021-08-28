# loader

> **参考**
>
> [《loader》(作者：https://www.webpackjs.com/)【来源：https://www.webpackjs.com/】](https://www.webpackjs.com/concepts/loaders/)
>
> [《webpack 之 loader 和 plugin 简介》(作者：赵鹏|美团点评点餐)【来源：掘金】](https://juejin.cn/post/6844903489458405390)
>
> [《1-4 使用 Loader》(作者：吴浩麟)【来源：吴浩麟】](https://webpack.wuhaolin.cn/1%E5%85%A5%E9%97%A8/1-4%E4%BD%BF%E7%94%A8Loader.html)
> [《分享12个Webpack中常用的Loader》(作者：蛙人)【来源：前端娱乐圈】](https://mp.weixin.qq.com/s/uITo5FLHeu4BQ6psfGYddg)

[TOC]

## loader 是什么

**loader 类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。**

## loader 有什么用

- 用于对模块的源代码进行转换。
- 可以使你在 import 或"加载"模块时预处理文件。
- 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。
- 甚至允许你直接在 JavaScript 模块中 import CSS 文件！
- 让 webpack 能够去处理那些非 JavaScript 文件（webpack 自身只理解 JavaScript）。
- 可以将所有类型的文件转换为 webpack 能够处理的有效模块，然后你就可以利用 webpack 的打包能力，对它们进行处理。

**本质上，webpack loader 将所有类型的文件，转换为应用程序的依赖图（和最终的 bundle）可以直接引用的模块。**

## loader 怎么用

在更高层面，在 webpack 的配置中 loader 有两个目标：

- `test` 属性，用于标识出应该被对应的 loader 进行转换的某个或某些文件。
- `use` 属性，表示进行转换时，应该使用哪个 loader。

首先安装相对应的 loader：

```bash
npm install --save-dev css-loader
npm install --save-dev ts-loader
```

```js
// webpack.config.js
module.exports = {
  module: {
    rules: [
      { test: /\.css$/, use: 'css-loader' },
      { test: /\.ts$/, use: 'ts-loader' },
    ],
  },
}
```

### 使用 loader 的方式

- 配置（推荐）：在 webpack.config.js 文件中指定 loader。
- 内联：在每个 import 语句中显式指定 loader。
- CLI：在 shell 命令中指定它们。

#### 配置

module.rules 允许你在 webpack 配置中指定多个 loader。 这是展示 loader 的一种简明方式，并且有助于使代码变得简洁。同时让你对各个 loader 有个全局概览：

```js
module: {
  rules: [
    {
      test: /\.css$/,
      use: [
        { loader: 'style-loader' },
        {
          loader: 'css-loader',
          options: {
            modules: true,
          },
        },
      ],
    },
  ]
}
```

#### 内联

可以在 import 语句或任何等效于 "import" 的方式中指定 loader。使用 `!` 将资源中的 loader 分开。分开的每个部分都相对于当前目录解析。

```js
import Styles from 'style-loader!css-loader?modules!./styles.css'
```

通过前置所有规则及使用 `!`，可以对应覆盖到配置中的任意 loader。

选项可以传递查询参数，例如 `?key=value&foo=bar`，或者一个 JSON 对象，例如 `?{"key":"value","foo":"bar"}`。

#### CLI

```bash
webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'
```

这会对 .jade 文件使用 jade-loader，对 .css 文件使用 style-loader 和 css-loader。

## loader 特性

- loader 支持链式传递。
  - 能够对资源使用流水线(pipeline)。
  - **一组链式的 loader 将按照相反的顺序执行。**
  - loader 链中的第一个 loader 返回值给下一个 loader。在最后一个 loader，返回 webpack 所预期的 JavaScript。
- loader 可以是同步的，也可以是异步的。
- loader 运行在 Node.js 中，并且能够执行任何可能的操作。
- loader 接收查询参数。用于对 loader 传递配置。
- loader 也能够使用 options 对象进行配置。
- 除了使用 package.json 常见的 main 属性，还可以将普通的 npm 模块导出为 loader，做法是在 package.json 里定义一个 loader 字段。
- 插件(plugin)可以为 loader 带来更多特性。
- loader 能够产生额外的任意文件。

## 常用 loader

- 样式：style-loader、css-loader、less-loader、sass-loader、postcss-loader 等
- 文件：raw-loader、file-loader 、url-loader 等
- 编译：babel-loader、coffee-loader 、ts-loader 等
- 校验测试：mocha-loader、jshint-loader 、eslint-loader 等

### style-loader、css-loader、less-loader、sass-loader

> 用于将 css 编译完成的样式，挂载到页面 style 标签上。
> 需要注意 loader 执行顺序，style-loader 放到第一位，因为 loader 都是从后往前执行，最后全部编译完成挂载到 style 上

安装：

```bash
npm i css-loader style-loader sass-loader node-sass less-loader -D
```

在 `webpack.config.js` 配置：

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader'],
        // use: ['style-loader', 'css-loader', 'less-loader'],
        include: /src/,
      },
    ],
  },
}
```

### postcss-loader

> 用于补充 css 样式各种浏览器内核前缀

安装：

```bash
npm i postcss-loader autoprefixer -D
```

在 `postcss.config.js` 配置：

```js
module.exports = {
  plugins: [
    require('autoprefixer')({
      overrideBrowserslist: ['> 1%', 'last 3 versions', 'ie 8'],
    }),
  ],
}
```

在 `webpack.config.js` 配置：

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.scss$/,
        use: ['style-loader', 'css-loader', 'sass-loader', 'postcss-loader'],
        include: /src/,
      },
    ],
  },
}
```

### babel-loader

> 将 Es6+ 语法转换为 Es5 语法

安装：

```bash
npm i babel-loader @babel/core @babel/preset-env -D
```

- babel-loader 这是使 babel 和 webpack 协同工作的模块
- @bable/core 这是 babel 编译器核心模块
- @babel/preset-env 这是 babel 官方推荐的预置器，可根据用户的环境自动添加所需的插件和补丁来编译 Es6 代码

在 `webpack.config.js` 配置：

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.js$/,
        use: {
          loader: 'babel-loader',
          options: {
            presets: [['@babel/preset-env', { targets: 'defaults' }]],
          },
        },
      },
    ],
  },
}
```

在 `babel.config.js` 配置：

```js
module.exports = {
  presets: [
    // https://github.com/vuejs/vue-cli/tree/master/packages/@vue/babel-preset-app
    '@vue/cli-plugin-babel/preset',
  ],
  env: {
    development: {
      // babel-plugin-dynamic-import-node plugin only does one thing by converting all import() to require().
      // This plugin can significantly increase the speed of hot updates, when you have a large number of pages.
      // https://panjiachen.github.io/vue-element-admin-site/guide/advanced/lazy-loading.html
      plugins: ['dynamic-import-node'],
    },
  },
}
```

### html-loader

> 我们有时候想引入一个 html 页面代码片段赋值给 DOM 元素内容使用，这时就用到 html-loader

安装：

```bash
npm i html-loader -D
```

在 `webpack.config.js` 配置：

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.html$/,
        use: 'html-loader',
      },
    ],
  },
}
```

使用：

```js
import Content from '../template.html'

document.body.innerHTML = Content
```

### file-loader

> 用于处理文件类型资源，如 jpg，png 等图片。返回值为 publicPath 为准。

安装：

```bash
npm i file-loader -D
```

在 `webpack.config.js` 配置：

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|jpeg)$/,
        use: [
          {
            loader: 'file-loader',
            options: {
              name: '[name]_[hash:8].[ext]',
              publicPath: 'https://www.baidu.com',
            },
          },
        ],
      },
    ],
  },
}
```

使用：

```js
import img from './pic.png'
console.log(img) // https://www.baidu.com/pic_600eca23.png
```

### url-loader

> url-loader 也是处理图片类型资源，只不过它与 file-loader 有一点不同，url-loader 可以设置一个根据图片大小进行不同的操作，如果该图片大小大于指定的大小，则将图片进行打包资源，否则将图片转换为 base64 字符串合并到 js 文件里

安装：

```bash
npm i url-loader -D
```

在 `webpack.config.js` 配置：

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|jpeg)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              name: '[name]_[hash:8].[ext]',
              limit: 10240, // 这里单位为(b) 10240 => 10kb
              // 这里如果小于10kb则转换为base64打包进js文件，如果大于10kb则打包到dist目录
            },
          },
        ],
      },
    ],
  },
}
```

使用：

```js
import img from './pic.png'
```

### html-withimg-loader

> 我们在编译图片时，都是使用 file-loader 和 url-loader，这两个 loader 都是查找 js 文件里的相关图片资源，但是 html 里面的文件不会查找所以我们 html 里的图片也想打包进去，这时使用 html-withimg-loader

安装：

```bash
npm i html-withimg-loader -D
```

在 `webpack.config.js` 配置：

如果打包出现 img 的 src 路径为[Object Module]，解决方案有

- 将 file-loader 降级到 4.2.0
- 修改 options 参数 esModule 为 false

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.(png|jpg|jpeg)$/,
        use: {
          loader: 'file-loader',
          options: {
            name: '[name]_[hash:8].[ext]',
            publicPath: 'http://www.baidu.com',
            esModule: false,
          },
        },
      },
      {
        test: /\.(png|jpeg|jpg)/,
        use: 'html-withimg-loader',
      },
    ],
  },
}
```

使用：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>首页</title>
  </head>
  <body>
    <h4>我蛙人</h4>
    <img src="./src/img/pic.jpg" alt="" />
  </body>
</html>
```

### vue-loader

> 用于编译.vue 文件

安装：

```bash
npm i vue-loader vue vue-template-compiler -D
```

- vue-loader 用于识别.vue 文件
- vue 识别支持 vue 语法
- vue-template-compiler 语法模板渲染引擎 {{}} template、 script、 style

**注意：vue 要和 vue-template-compiler 版本一致，不然会出现打包报错。**

在 `webpack.config.js` 配置：

如果打包出现 img 的 src 路径为[Object Module]，解决方案有

- 将 file-loader 降级到 4.2.0
- 修改 options 参数 esModule 为 false

```js
const VueLoaderPlugin = require('vue-loader/lib/plugin')
module.exports = {
  entry: './src/main.js',
  output: {
    path: __dirname + '/dist',
    filename: 'index.js',
  },
  module: {
    rules: [
      {
        test: /\.vue$/,
        use: 'vue-loader',
      },
    ],
  },
  plugins: [new VueLoaderPlugin()],
}
```

使用：

- 在 `main.js`

```js
import App from './index.vue'
import Vue from 'Vue'
new Vue({
  el: '#app',
  render: (h) => h(App),
})
```

- 在 `index.vue`

```js
<template>
  <div class="index">
    {{ msg }}
  </div>
</template>

<script>
export default {
 name: 'index',
  data() {
    return {
        msg: "hello 蛙人"
    }
  },
  created() {},
  components: {},
  watch: {},
  methods: {}
}
</script>
<style scoped>

</style>
```

### eslint-loader

> 用于检查代码是否符合规范，是否存在语法错误。

安装：

```bash
npm i eslint-loader eslint -D
```

在 `.eslintrc.js` 配置：

```js
module.exports = {
  root: true,
  env: {
    browser: true,
  },
  rules: {
    'no-alert': 0, // 禁止使用alert
    indent: [2, 4], // 缩进风格
    'no-unused-vars': 'error', // 变量声明必须使用
  },
}
```

在 `webpack.config.js` 配置：

```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.ts$/,
        use: ['eslint-loader', 'ts-loader'],
        enforce: 'pre',
        exclude: /node_modules/,
      },
      {
        test: /\.ts$/,
        use: 'ts-loader',
        exclude: /node_modules/,
      },
    ],
  },
}
```

使用：

在 .js 文件中书写时，会自动检测写法是否符合所设置的 eslint 规则并提示
