# Vite 使用插件

> **参考**
>
> [Vite 中文文档 - 使用插件](https://cn.vitejs.dev/guide/using-plugins.html)

## 添加一个插件

若要使用一个插件，需要将它添加到项目的 `devDependencies` 并在 `vite.config.js` 配置文件中的 `plugins` 数组中引入它。

例如，要想为传统浏览器提供支持，可以按下面这样使用官方插件 `@vitejs/plugin-legacy`：

```bash
npm add -D @vitejs/plugin-legacy
```

```js
// vite.config.js
import legacy from '@vitejs/plugin-legacy';
import { defineConfig } from 'vite';

export default defineConfig({
  plugins: [
    legacy({
      targets: ['defaults', 'not IE 11'],
    }),
  ],
});
```

## 查找插件

> Vite 旨在为常见的 Web 开发范式提供开箱即用的支持。在寻找一个 Vite 或兼容的 Rollup 插件之前，请先查看 功能指引。大量在 Rollup 项目中需要使用插件的用例在 Vite 中已经覆盖到了。
> Vite 是一个基于 Rollup 的 Cli 工具，所以预设了一些基础配置优化，所以不需要再添加插件额外配置，但 Vite 不能包括所有功能，有一些还是需要插件来完成。

- [官方插件](https://cn.vitejs.dev/plugins/#official-plugins)
- [社区插件](https://github.com/vitejs/awesome-vite#plugins)
- [Rollup 插件](https://cn.vitejs.dev/guide/api-plugin.html#rollup-plugin-compatibility)

## 强制插件排序

为了与某些 Rollup 插件兼容，可能需要强制修改插件的执行顺序，或者只在构建时使用。这应该是 Vite 插件的实现细节。

可以使用 enforce 修饰符来强制插件的位置:

- `pre`：在 Vite 核心插件之前调用该插件
- 默认：在 Vite 核心插件之后调用该插件
- `post`：在 Vite 构建插件之后调用该插件

```js
// vite.config.js
import image from '@rollup/plugin-image';
import { defineConfig } from 'vite';

export default defineConfig({
  plugins: [
    {
      ...image(),
      enforce: 'pre',
    },
  ],
});
```

## 按需应用

默认情况下插件在开发 (serve) 和生产 (build) 模式中都会调用。

如果插件在服务或构建期间按需使用，请使用 apply 属性指明它们仅在 `'build'` 或 `'serve'` 模式时调用：

```js
// vite.config.js
import typescript2 from 'rollup-plugin-typescript2';
import { defineConfig } from 'vite';

export default defineConfig({
  plugins: [
    {
      ...typescript2(),
      apply: 'build',
    },
  ],
});
```
