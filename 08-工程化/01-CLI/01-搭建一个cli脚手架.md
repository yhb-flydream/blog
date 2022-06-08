<!--
 * @Author: yaohebin
 * @Date: 2021-09-28 16:56:54
 * @LastEditTime: 2022-06-08 11:21:13
 * @LastEditors: yaohebin
 * @Description: 搭建一个 cli 脚手架
-->
# 搭建一个 cli 脚手架

> **参考**
>
> [《从 0 构建自己的脚手架/CLI 知识体系（万字） 🛠》(作者：ITEM)【来源：掘金】](https://juejin.cn/post/6966119324478079007)
>
> [《手把手教你写一个脚手架》(作者：谭光志)【来源：掘金】](https://juejin.cn/post/6932610749906812935)
>
> [《手把手教你写一个脚手架（二）》(作者：谭光志)【来源：掘金】](https://juejin.cn/post/6965684757921431583)
>
> [《chalk不支持nodejs中require引入，以及颜色不变的解决方法》(作者：打倒OKR)【来源：掘金】](https://juejin.cn/post/7094161450947575844)

## 使用到的工具

- 命令行自定义指令
  - [commander](https://github.com/tj/commander.js/blob/master/Readme_zh-CN.md)
- 命令行询问用户问题，记录回答结果
  - [inquirer](https://github.com/SBoudrias/Inquirer.js/)
- 控制台输出内容样式美化
  - [chalk](https://www.npmjs.com/package/chalk)
- 控制台 loading 样式
  - [ora](https://www.npmjs.com/package/ora)
- 控制台打印 logo
  - [figlet](https://www.npmjs.com/package/figlet)
- 控制台输出表格
  - [easy-table](https://www.npmjs.com/package/easy-table)
- 下载远程模版
  - [download-git-repo](https://www.npmjs.com/package/download-git-repo)
- 系统fs模块的扩展，提供了更多便利的 API，并继承了fs模块的 API
  - [fs-extra](https://www.npmjs.com/package/fs-extra)
- 支持跨平台调用系统上的命令
  - [cross-spawn](https://www.npmjs.com/package/cross-spawn)

## 问题

运行报错时，注意可以安装对应的低版本的包再进行尝试
