# plugin 如何写

- 一个插件就是一个类。
- 在插件类上定义一个 apply 方法。
- 在 apply 方法中监听一个 webpack 自身的事件钩子。
- 处理 webpack 内部实例的特定数据。
- 功能完成后调用 webpack 提供的回调。
