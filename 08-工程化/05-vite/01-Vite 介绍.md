<!--
 * @Author: yaohebin
 * @Date: 2023-02-14 10:13:12
 * @LastEditTime: 2023-02-27 15:35:00
 * @LastEditors: yaohebin
 * @Description: Vite 介绍
-->

# Vite 介绍

> **参考**
>
> [Vite 中文文档](https://cn.vitejs.dev/)

[TOC]

## 为什么是 Vite

Vite 旨在利用生态系统中的新进展解决上述问题：**浏览器开始原生支持 ES 模块，且越来越多 JavaScript 工具使用编译型语言编写。**

### 缓慢的服务器启动

Vite 通过在一开始将应用中的模块区分为 **依赖** 和 **源码** 两类，改进了开发服务器启动时间。

- 依赖
  - 大多为在开发时不会变动的纯 JavaScript。
  - 一些较大的依赖（例如有上百个模块的组件库）处理的代价也很高。依赖也通常会存在多种模块化格式（例如 ESM 或者 CommonJS）。
  - Vite 将会使用 esbuild 预构建依赖。esbuild 使用 Go 编写，并且比以 JavaScript 编写的打包器预构建依赖快 10-100 倍。
- 源码
  - 通常包含一些并非直接是 JavaScript 的文件，需要转换（例如 JSX，CSS 或者 Vue/Svelte 组件），时常会被编辑。同时，并不是所有的源码都需要同时被加载（例如基于路由拆分的代码模块）。

**Vite 以 原生 ESM 方式提供源码**。这实际上是让浏览器接管了打包程序的部分工作：**Vite 只需要在浏览器请求源码时进行转换并按需提供源码**。根据情景动态导入代码，即只在当前屏幕上实际使用时才会被处理。

### 缓慢的更新

基于打包器启动时，重建整个包的效率很低。原因显而易见：**因为这样更新速度会随着应用体积增长而直线下降。**

HMR

- 在 Vite 中，HMR 是在原生 ESM 上执行的。当编辑一个文件时，Vite 只需要精确地使已编辑的模块与其最近的 HMR 边界之间的链失活（大多数时候只是模块本身），使得无论应用大小如何，HMR 始终能保持快速更新。
- Vite 同时利用 HTTP 头来加速整个页面的重新加载（再次让浏览器为我们做更多事情）：源码模块的请求会根据 304 Not Modified 进行协商缓存，而依赖模块请求则会通过 Cache-Control: max-age=31536000,immutable 进行强缓存，因此一旦被缓存它们将不需要再次请求。

## 组成

- 开发服务器
  - 基于 `原生 ES 模块`，提供了丰富内建功能（如`模块热替换 HMR`）
- 构建指令
  - 使用 `rollup` 打包代码，已经预配置及优化，可直接输出可用于生产环境的静态资源

## 浏览器支持

默认支持 `原生 ESM 语法的 script 标签`、`原生 ESM 动态导入`和 `import.meta` 的浏览器。

传统浏览器可使用官方插件 `@vitejs/plugin-legacy` 支持

## 功能

### npm 依赖解析和预构建

- `预构建` 将导入的一些裸模块进行预构建，提高页面加载速度，并将 CommonJS / UMD 转换为 ESM 格式。
  - 预构建由 `esbuild` 执行
- 重写导入为合法的 URL（`/node_modules/.vite/deps/my-dep.js?v=f3sf2ebd`），以便浏览器可以正确导入。
- 依赖是强缓存
  - vite 通过 HTTP 头来缓存请求得到的依赖

### 模块热替换

### TypeScript

- 仅执行转译

### vue

- Vue 3 单文件组件支持：`@vitejs/plugin-vue`
- Vue 2.7 支持：`vitejs/vite-plugin-vue2`
- Vue <2.7 的支持：`underfin/vite-plugin-vue2`

### JSX

- Vue 3 JSX 支持：`@vitejs/plugin-vue-jsx`

### CSS

导入 `.css` 文件将会把内容插入到 `<style>` 标签中，同时也带有 HMR 支持。

#### PostCSS

如果项目包含有效的 PostCSS 配置（任何受 postcss-load-config 支持的格式，例如 postcss.config.js），将会自动应用于所有已导入的 CSS

#### CSS Modules

任何以 `.module.css` 为后缀的 CSS 文件都被认为是一个 CSS Modules 文件。导入这样的文件会返回一个相应的模块对象：

```css
/* example.module.css */
.red {
  color: red;
}
```

```js
import classes from './example.module.css';
document.getElementById('foo').className = classes.red;
```

如果 `css.modules.localsConvention` 设置开启了 `camelCase` 格式变量名转换（例如 `localsConvention: 'camelCaseOnly'`），你还可以使用按名导入。

```js
// .apply-color -> applyColor
import { applyColor } from './example.module.css';
document.getElementById('foo').className = applyColor;
```

#### CSS 预处理器

对 `.scss`, `.sass`, `.less`, `.styl`, `.stylus` 内置支持。没有必要为它们安装特定的 Vite 插件，**但必须安装相应的预处理器依赖**：

```bash
# .scss and .sass
npm add -D sass

# .less
npm add -D less

# .styl and .stylus
npm add -D stylus
```

- 如果使用的是单文件组件，可以通过 `<style lang="sass">`（或其他预处理器）自动开启。
- Vite 为 Sass 和 Less 改进了 @import 解析，以保证 Vite 别名（如 @）也能被使用。
  - 另外，`url()` 中的相对路径引用的，与根文件不同目录中的 Sass/Less 文件会自动变基以保证正确性。

#### 禁用 CSS 注入页面

自动注入 CSS 内容的行为可以通过 `?inline` 参数来关闭。在关闭时，被处理过的 CSS 字符串将会作为该模块的默认导出，但样式并没有被注入到页面中。

```js
import styles from './foo.css'; // 样式将会注入页面
import otherStyles from './bar.css?inline'; // 样式不会注入页面
```

### 静态资源处理

导入一个静态资源会返回解析后的 URL：

```js
import imgUrl from './img.png';
document.getElementById('hero-img').src = imgUrl;
```

添加一些特殊的查询参数可以更改资源被引入的方式：

```js
// 显式加载资源为一个 URL
import assetAsURL from './asset.js?url';

// 以字符串形式加载资源
import assetAsString from './shader.glsl?raw';

// 加载为 Web Worker
import Worker from './worker.js?worker';

// 在构建时 Web Worker 内联为 base64 字符串
import InlineWorker from './worker.js?worker&inline';
```

### JSON

JSON 可以被直接导入 —— 同样支持具名导入（**对一个根字段使用具名导入 —— 有效帮助 treeshaking！**）：

```js
// 导入整个对象
import json from './example.json';
// 对一个根字段使用具名导入 —— 有效帮助 treeshaking！
import { field } from './example.json';
```

### Glob 导入

Vite 支持使用特殊的 `import.meta.glob` 函数从文件系统导入多个模块：

```js
const modules = import.meta.glob('./dir/*.js');
```

以上将会被转译为下面的样子：

```js
// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js'),
  './dir/bar.js': () => import('./dir/bar.js'),
};
```

你可以遍历 modules 对象的 key 值来访问相应的模块：

```js
for (const path in modules) {
  modules[path]().then((mod) => {
    console.log(path, mod);
  });
}
```

匹配到的文件默认是懒加载的，通过动态导入实现，并会在构建时分离为独立的 chunk。

如果倾向于引入所有的模块（例如依赖于这些模块中的副作用首先被应用），可以传入 `{ eager: true }` 作为第二个参数：

```js
const modules = import.meta.glob('./dir/*.js', { eager: true });
```

以上会被转译为下面的样子：

```js
// vite 生成的代码
import * as __glob__0_0 from './dir/foo.js';
import * as __glob__0_1 from './dir/bar.js';
const modules = {
  './dir/foo.js': __glob__0_0,
  './dir/bar.js': __glob__0_1,
};
```

#### Glob 导入形式

`import.meta.glob` 都支持以字符串形式导入文件，类似于 以字符串形式导入资源。在这里，我们使用了 `Import Reflection` 语法对导入进行断言：

```js
const modules = import.meta.glob('./dir/*.js', { as: 'raw' });
// { as: 'url' } 还支持将资源作为 URL 加载。
```

上面的代码会被转换为下面这样：

```js
// code produced by vite（代码由 vite 输出）
const modules = {
  './dir/foo.js': 'export default "foo"\n',
  './dir/bar.js': 'export default "bar"\n',
};
```

#### 多个匹配模式

第一个参数可以是一个 glob 数组，例如：

```js
const modules = import.meta.glob(['./dir/*.js', './another/*.js']);
```

#### 反面匹配模式

同样也支持反面 glob 匹配模式（以 `!` 作为前缀）。若要忽略结果中的一些文件，你可以添加“排除匹配模式”作为第一个参数：

```js
const modules = import.meta.glob(['./dir/*.js', '!**/bar.js']);

// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js'),
};
```

- 具名导入

也可能你只想要导入模块中的部分内容，那么可以利用 import 选项。

```js
const modules = import.meta.glob('./dir/*.js', { import: 'setup' });

// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js').then((m) => m.setup),
  './dir/bar.js': () => import('./dir/bar.js').then((m) => m.setup),
};
```

当与 `eager` 一同存在时，甚至可以对这些模块进行 tree-shaking。

```js
const modules = import.meta.glob('./dir/*.js', { import: 'setup', eager: true });

// vite 生成的代码
import { setup as __glob__0_0 } from './dir/foo.js';
import { setup as __glob__0_1 } from './dir/bar.js';
const modules = {
  './dir/foo.js': __glob__0_0,
  './dir/bar.js': __glob__0_1,
};
```

设置 import 为 default 可以加载默认导出。

```js
const modules = import.meta.glob('./dir/*.js', {
  import: 'default',
  eager: true,
});

// vite 生成的代码
import __glob__0_0 from './dir/foo.js';
import __glob__0_1 from './dir/bar.js';
const modules = {
  './dir/foo.js': __glob__0_0,
  './dir/bar.js': __glob__0_1,
};
```

- 自定义查询

你也可以使用 query 选项来提供对导入的自定义查询，以供其他插件使用。

```js
const modules = import.meta.glob('./dir/*.js', {
  query: { foo: 'bar', bar: true },
});

// vite 生成的代码
const modules = {
  './dir/foo.js': () => import('./dir/foo.js?foo=bar&bar=true'),
  './dir/bar.js': () => import('./dir/bar.js?foo=bar&bar=true'),
};
```

#### Glob 导入注意事项

- 这只是一个 Vite 独有的功能而不是一个 Web 或 ES 标准
- 该 Glob 模式会被当成导入标识符：必须是相对路径（以 `./` 开头）或绝对路径（以 `/` 开头，相对于项目根目录解析）或一个`别名路径`（请看 `resolve.alias` 选项)。
- Glob 匹配是使用 fast-glob 来实现的 —— 阅读它的文档来查阅 支持的 Glob 模式。
- 你还需注意，所有 `import.meta.glob` 的参数都必须以字面量传入。你 **不** 可以在其中使用变量或表达式。

### 动态导入

和 glob 导入 类似，Vite 也支持带变量的动态导入。

```js
const module = await import(`./dir/${file}.js`);
```

**注意变量仅代表一层深的文件名**。如果 file 是 `foo/bar`，导入将会失败。_对于更进阶的使用详情，你可以使用 `glob 导入` 功能。_

### WebAssembly

### Web Workers

一个 Web Worker 可以使用 `new Worker()` 和 `new SharedWorker()` 导入。与 worker 后缀相比，这种语法更接近于标准，是创建 worker 的 推荐 方式。

```js
const worker = new Worker(new URL('./worker.js', import.meta.url));
```

worker 构造函数会接受可以用来创建 “模块” worker 的选项：

```js
const worker = new Worker(new URL('./worker.js', import.meta.url), {
  type: 'module',
});
```

#### 带有查询后缀的导入

你可以在导入请求上添加 `?worker` 或 `?sharedworker` 查询参数来直接导入一个 web worker 脚本。默认导出会是一个自定义 worker 的构造函数：

```js
import MyWorker from './worker?worker';

const worker = new MyWorker();
```

Worker 脚本也可以使用 import 语句来替代 importScripts() —— 注意，在开发过程中，这依赖于浏览器原生支持，目前只在 Chrome 中适用，而在生产版本中，它已经被编译掉了。

默认情况下，worker 脚本将在生产构建中编译成单独的 chunk。如果你想将 worker 内联为 base64 字符串，请添加 inline 查询参数：

```js
import MyWorker from './worker?worker&inline';
```

如果你想要以一个 URL 的形式读取该 worker，请添加 url 这个 query：

```js
import MyWorker from './worker?worker&url';
```

### 构建优化

> 下面所罗列的功能会自动应用为构建过程的一部分，除非你想禁用它们，否则没有必要显式配置。

#### CSS 代码分割

Vite 会自动地将一个异步 chunk 模块中使用到的 CSS 代码抽取出来并为其生成一个单独的文件。这个 CSS 文件将在该异步 chunk 加载完成时自动通过一个 `<link>` 标签载入，该异步 chunk 会保证只在 CSS 加载完毕后再执行，避免发生 FOUC 。

[什么是 FOUC？如何避免 FOUC？](https://www.cnblogs.com/xianyulaodi/p/5198603.html)

如果你更倾向于将所有的 CSS 抽取到一个文件中，你可以通过设置 `build.cssCodeSplit` 为 false 来禁用 CSS 代码分割。

#### 预加载指令生成

Vite 会为入口 chunk 和它们在打包出的 HTML 中的直接引入自动生成 `<link rel="modulepreload">` 指令。

#### 异步 Chunk 加载优化
