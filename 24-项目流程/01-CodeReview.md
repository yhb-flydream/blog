<!--
 * @Author: yaohebin
 * @Date: 2023-02-22 15:10:20
 * @LastEditTime: 2023-12-05 10:29:19
 * @LastEditors: yaohebin
 * @Description: CodeReview
-->

# CodeReview

> **参考**
>
> [《如何有效地进行代码 Review？》(作者：腾讯技术工程)【来源：知乎】](https://zhuanlan.zhihu.com/p/268749337)
>
> [《Commit message 和 Change log 编写指南》(作者：阮一峰)【来源：阮一峰的网络日志】](http://www.ruanyifeng.com/blog/2016/01/commit_message_change_log.html)

## Why

- 统一格式
- 提高代码复用性（组件、方法）
- 提高代码质量
- 提高项目健壮性
- 个人快速成长

## what

- 什么需求需要 review
  - 根据当次提交需求的复杂度（涉及到复杂逻辑及比较多需求更新）

## When

- 测试
- CI
- PR

## Where（Who）

- 会议室一起讨论

## How

### 写代码时对以下几点讨论或者告知

- 涉及到项目整体架构的改动（基础配置）
- 涉及到项目公共部分的改动（api、components、router、scss、store、utils）
- 涉及到修改原有项目（可以互相问看谁比较熟悉）
- 涉及到开发复杂逻辑

### 提交代码时

- 一次的提交尽可能的只涉及一个问题或需求
- 提交规范可参考如下
- vscode 插件（Commit Message Editor、git-commit-plugin）

| Git 提交规范 |                              |
| ------------ | ---------------------------- |
| Feat         | 增加新功能                   |
| Fix          | 修复问题/BUG                 |
| Style        | 代码风格相关无影响运行结果的 |
| Perf         | 优化/性能提升                |
| revert       | 撤销修改                     |
| Test         | 测试相关                     |
| Docs         | 文档/注释                    |
| Chore        | 依赖更新/脚手架配置更改等    |
| workflow     | 工作流改进                   |
| Ci           | 持续集成                     |
| Mod          | 不确定分类的修改             |
| Wip          | 开发中                       |
| types        | 类型修改                     |

```text
feat(home): add home page
fix(home): fix home page link
```

### PR 时

- 项目整体架构的改动（基础配置）
- 项目公共部分的改动（api、components、router、scss、store、utils）
- 代码规范、格式（变量名和函数名，行的长度，缩进，格式和注释）
- 代码影响范围
- 代码优化（多余的或是重复的代码，模块化）
- 代码安全（参数类型，容错机制，错误捕获）

### 工具
