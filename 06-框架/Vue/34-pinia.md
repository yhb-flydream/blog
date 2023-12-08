<!--
 * @Author: yaohebin
 * @Date: 2022-07-21 15:20:36
 * @LastEditTime: 2023-12-08 11:45:43
 * @LastEditors: yaohebin
 * @Description: pinia
-->

# pinia

> **参考**
>
> [Pinia](https://pinia.vuejs.org/)
>
> [Pinia 中文文档](https://pinia.web3doc.top/)
>
> [《大菠萝？Pinia 已经来了，再不学你就 out 了》(作者：mapengfei)【来源：掘金】](https://juejin.cn/post/7078281612013764616)
>
> [《新一代状态管理工具，Pinia.js 上手指南》(作者：小学生 study)【来源：掘金】](https://juejin.cn/post/7049196967770980389)
>
> [pinia-plugin-persist](https://seb-l.github.io/pinia-plugin-persist/)
>
> [《Pinia 持久化存储插件 pinia-plugin-persist 介绍及源码解读》(作者：ZTStory)【来源：掘金】](https://juejin.cn/post/7139002338554937352)
>
> [pinia-plugin-persistedstate](https://prazdevs.github.io/pinia-plugin-persistedstate/)
>
> [《Pinia 的使用以及数据持久化》(作者：天神下凡\_)【来源：掘金】](https://juejin.cn/post/7101657189428756516)
>
> [pinia-plugin-persist-uni](https://allen-1998.github.io/pinia-plugin-persist-uni/)
>
> [《pinia-plugin-persist-uni 在 uni-app 中持久化存储 pinia》(作者：allen_1998)【来源：掘金】](https://juejin.cn/post/7081275565008748552)

## 特点

- Devtools 支持
  - 追踪 actions、mutations 的时间线
  - 在组件中展示它们所用到的 Store
  - 让调试更容易的 Time travel
- 热更新
  - 不必重载页面即可修改 Store
  - 开发时可保持当前的 State
- 插件：可通过插件扩展 Pinia 功能
- 为 JS 开发者提供适当的 TypeScript 支持以及自动补全功能
- 支持服务端渲染
- mutation 已被弃用。它们经常被认为是极其冗余的。它们初衷是带来 devtools 的集成方案，但这已不再是一个问题了
- 无需要创建自定义的复杂包装器来支持 TypeScript，一切都可标注类型，API 的设计方式是尽可能地利用 TS 类型推理
- 无过多的魔法字符串注入，只需要导入函数并调用它们，然后享受自动补全的乐趣就好
- 无需要动态添加 Store，它们默认都是动态的，甚至你可能都不会注意到这点。注意，你仍然可以在任何时候手动使用一个 Store 来注册它，但因为它是自动的，所以你不需要担心它
- 不再有嵌套结构的模块。你仍然可以通过导入和使用另一个 Store 来隐含地嵌套 stores 空间。虽然 Pinia 从设计上提供的是一个扁平的结构，但仍然能够在 Store 之间进行交叉组合。你甚至可以让 Stores 有循环依赖关系
- 不再有可命名的模块。考虑到 Store 的扁平架构，Store 的命名取决于它们的定义方式，你甚至可以说所有 Store 都应该命名

## 持久化

- pinia-plugin-persist
- pinia-plugin-persistedstate
- pinia-plugin-persist-uni
