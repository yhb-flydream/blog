<!--
 * @Author: yaohebin
 * @Date: 2021-02-01 17:45:29
 * @LastEditTime: 2022-06-09 09:53:59
 * @LastEditors: yaohebin
 * @Description: webpack 构建流程
-->

# webpack 构建流程

> **参考**
>
> [Webpack 中文网站](https://www.webpackjs.com/)
>
> [《1-5 使用 Plugin》(作者：吴浩麟)【来源：吴浩麟】](https://webpack.wuhaolin.cn/1%E5%85%A5%E9%97%A8/1-5%E4%BD%BF%E7%94%A8Plugin.html)
>
> [《webpack 构建流程分析》(作者：Rap_Jin)【来源：掘金】](https://juejin.cn/post/6844904000169607175)
>
> [《[万字总结] 一文吃透 Webpack 核心原理》(作者：范文杰)【来源：知乎】](https://zhuanlan.zhihu.com/p/363928061)
>
> [《[源码解读] Webpack 插件架构深度讲解》(作者：范文杰)【来源：知乎】](https://zhuanlan.zhihu.com/p/367931462)
>
> [《webpack 系列--浅析 webpack 的原理》(作者：saucxs)【来源：博客园】](https://www.cnblogs.com/chengxs/p/11022842.html)
>
> [《webpack 打包原理 ? 看完这篇你就懂了 !》(作者：null 仔)【来源：掘金】](https://juejin.cn/post/6844904038543130637)

[TOC]

## 基本概念

- `Entry`：入口，Webpack 执行构建的第一步将从 Entry 开始，可抽象成输入。
- `Module`：模块，在 Webpack 里一切皆模块，一个模块对应着一个文件。Webpack 会从配置的 Entry 开始递归找出所有依赖的模块。
- `Chunk`：代码块，一个 Chunk 由多个模块组合而成，用于代码合并与分割。
- `Loader`：模块转换器，用于把模块原内容按照需求转换成新内容。
- `Plugin`：扩展插件，在 Webpack 构建流程中的特定时机会广播出对应的事件，插件可以监听这些事件的发生，在特定时机做对应的事情。

## 流程概括

**Webpack 的运行流程是一个串行的过程**，从启动到结束会依次执行以下流程：

1. **初始化参数**：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数；
2. **开始编译**：用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译；
3. **确定入口**：根据配置中的 entry 找出所有的入口文件；
4. **编译模块**：从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理；
5. **完成模块编译**：在经过第 4 步使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系；
6. **输出资源**：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；
7. **输出完成**：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

在以上过程中，Webpack 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 API 改变 Webpack 的运行结果。
