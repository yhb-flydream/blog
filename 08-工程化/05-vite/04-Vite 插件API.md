# Vite 插件 API

> **参考**
>
> [Vite 中文文档 - 插件 API](https://cn.vitejs.dev/guide/api-plugin.html)
>
> [Rollup 插件文档](https://rollupjs.org/plugin-development/)

## 插件规范

## 插件配置

用户会将插件添加到项目的 `devDependencies` 中并使用数组形式的 `plugins` 选项配置它们。

```js
// vite.config.js
import vitePlugin from 'vite-plugin-feature';
import rollupPlugin from 'rollup-plugin-feature';

export default defineConfig({
  plugins: [vitePlugin(), rollupPlugin()],
});
```
