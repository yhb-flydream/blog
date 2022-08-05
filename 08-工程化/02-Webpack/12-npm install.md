<!--
 * @Author: yaohebin
 * @Date: 2022-04-10 10:29:08
 * @LastEditTime: 2022-08-05 08:22:09
 * @LastEditors: yaohebin
 * @Description: npm install
-->

# npm install

> **参考**
>
> [《npm/yarn lock 真香》(作者：wuwhs)【来源：思否】](https://segmentfault.com/a/1190000039684460)
>
> [《npm 模块安装机制简介》(作者：阮一峰)【来源：阮一峰的网络日志】](https://www.ruanyifeng.com/blog/2016/01/npm-install.html)
>
> [《npm install 原理分析》(作者：ConardLi)【来源：code 秘密花园】](https://mp.weixin.qq.com/s/oyWoihH31MZObY83JlH2pw)
>
> [《npm install 之后发生了什么？》(作者：凌轩)【来源：知乎】](https://zhuanlan.zhihu.com/p/37285173)
>
> [《npm 缓存现在是怎么做的？》(作者：张立理)【来源：知乎】](https://www.zhihu.com/question/305539244/answer/551386426)
>
> [《探索 JavaScript 中的依赖管理及循环依赖》(作者：前端新能源)【来源：掘金】](https://juejin.cn/post/6844903552012255245)

[TOC]

## package.json

package.json 在前端工程化中主要用来记录依赖包名称、版本、运行指令等信息字段。

其中，`dependencies` 字段指定了项目运行所依赖的模块，`devDependencies` 指定项目开发所需要的模块。它们都指向一个对象。该对象的各个成员，分别由模块名和对应的版本要求组成，表示依赖的模块及其版本范围。对应的版本可以加上各种限定，主要有以下几种：

- **指定版本**：比如 1.2.2 ，遵循 **“大版本.次要版本.小版本”** 的格式规定，安装时**只安装指定版本**。
- **~指定版本**：比如 ~1.2.2 ，表示安装 1.2.x 的最新版本（不低于 1.2.2），但是不安装 1.3.x，也就是说安装时**不改变大版本号和次要版本号**。
- **^指定版本**：比如 ˆ1.2.2，表示安装 1.x.x 的最新版本（不低于 1.2.2），但是不安装 2.x.x，也就是说安装时**不改变大版本号**。
  - 需要注意的是，如果大版本号为 0，则插入号的行为与波浪号相同，这是因为此时处于开发阶段，即使是次要版本号变动，也可能带来程序的不兼容。
- **latest**：安装最新版本。

使用比如 `npm install package -save` 安装一个依赖包时，版本是**插入号形式(^)**。这样每次重新安装依赖包 npm install 时”次要版本“和“小版本”是会拉取最新的。

## package-lock.json

在 npm5 版本后，当我们运行 `npm install` 发现会生成一个新文件 `package-lock.json`，它是 package.json 中列出的每个依赖项的大型列表，应安装的特定版本，模块的位置（URI），验证模块完整性的哈希，它需要的包列表，以及依赖项列表，其作用是**锁定安装时的包的版本号**。

## npm install 安装规则

### 1.x.x

此版本的安装方式是递归模式，npm install 时，会严格按照 package.json 结构以及子依赖包的 package.json 结构将依赖安装到他们各自的 node_modules 中，直到有子依赖包不在依赖其他模块，是严格的嵌套结构。

### 3.x.x

此版本的安装方式改为了扁平结构，即：

**安装模块时，不管其是直接依赖还是子依赖的依赖，优先将其安装在 node_modules 根目录。**

如果后安装的依赖包又依赖了已经安装过的依赖包时，判断已安装的模块版本是否符合新模块的版本范围，如果符合则跳过，使用已经安装过的依赖包，不符合则在当前模块的 node_modules 下安装该模块。

对应的，如果我们在项目代码中引用了一个模块，模块查找流程如下：

- 在当前模块路径下搜索
- 在当前模块 node_modules 路径下搜素
- 在上级模块的 node_modules 路径下搜索
- 直到搜索到全局路径中的 node_modules

但是还有一个新问题**安装依赖的不确定性**，由于安装依赖包顺序不同，按照以上所说的安装方式，从而可能导致 node_modules 结构的不确定性，依赖结构的不确定性可能会给程序带来不可预知的问题。

**5.x.x 之后的版本，安装方式还是采用了扁平结构，另外新增了 package-lock.json 文件。**

下面来介绍一下 5.x.x 之后的几个版本不同的安装策略：

### 5.0.x

不管 package.json 中依赖是否有更新，npm install 都会根据 package-lock.json 下载。针对这种安装策略，有人提出了问题，然后就演变成了 5.1.0 版本后的规则。

### 5.1.0

当 package.json 中的依赖项有新版本时，npm install 会无视 package-lock.json 去下载新版本的依赖项并且更新 package-lock.json。针对这种安装策略，又有人提出了问题，得出 5.4.2 版本后的规则。

### 5.4.2

- 如果只有一个 package.json 文件，运行 npm install 会根据它生成一个 package-lock.json 文件，这个文件相当于本次 install 的一个快照，它不仅记录了 package.json 指明的直接依赖的版本，也记录了间接依赖的版本。
- 如果 package.json 的 semver-range version 和 package-lock.json 中版本兼容(package-lock.json 版本在 package.json 指定的版本范围内)，即使此时 package.json 中有新的版本，执行 npm install 也还是会根据 package-lock.json 下载。
- 如果手动修改了 package.json 的 version ranges，且和 package-lock.json 中版本不兼容，那么执行 npm install 时 package-lock.json 将会更新到兼容 package.json 的版本。

## 整体流程

1. 检查 `.npmrc` 文件：
   - 优先级为：项目级的 .npmrc 文件 > 用户级的 .npmrc 文件> 全局级的 .npmrc 文件 > npm 内置的 .npmrc 文件
2. 检查项目中有无 lock 文件
   1. 无 lock 文件：
      - 从 npm 远程仓库获取包信息
      - 根据 package.json 构建依赖树，构建过程：
        - 构建依赖树时，不管其是直接依赖还是子依赖的依赖，优先将其放置在 node_modules 根目录。
        - 当遇到相同模块时，判断已放置在依赖树的模块版本是否符合新模块的版本范围，如果符合则跳过，不符合则在当前模块的 node_modules 下放置该模块。
        - 注意这一步只是确定逻辑上的依赖树，并非真正的安装，后面会根据这个依赖结构去下载或拿到缓存中的依赖包
      - 在缓存中依次查找依赖树中的每个包
        - 不存在缓存：
          - 从 npm 远程仓库下载包
          - 校验包的完整性
            - 校验不通过：重新下载
            - 校验通过：
              - 将下载的包复制到 npm 缓存目录
              - 将下载的包按照依赖结构解压到 node_modules
        - 存在缓存：将缓存按照依赖结构解压到 node_modules
      - 将包解压到 node_modules
      - 生成 lock 文件
   2. 有 lock 文件：
      - 检查 package.json 中的依赖版本是否和 package-lock.json 中的依赖有冲突。
      - 如果没有冲突，直接跳过获取包信息、构建依赖树过程，开始在缓存中查找包信息，后续过程相同
