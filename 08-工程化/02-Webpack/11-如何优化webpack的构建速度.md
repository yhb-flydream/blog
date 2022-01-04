# 如何优化 webpack 的构建速度

> **参考**
>
> [《性能优化篇---Webpack 构建速度优化》(作者：keywords)【来源：思否】](https://segmentfault.com/a/1190000018493260)

- 使用 插件分析 webpack 打包慢的原因（文件体积、文件打包速度）
- 优化 loader 配置 使用 缓存（cache-loader），配置文检查找路径、文件格式，减少文件查找时间
- 代码分割，减少单个打包文件的体积
- 使用 DllPlugin 分开打包依赖的第三方模块
- 使用 HappyPack 将 loader 文件转换的耗时操作拆分多个进程同时处理
- 使用 ParallelUglifyPlugin 对 js 进行多线程并行处理
