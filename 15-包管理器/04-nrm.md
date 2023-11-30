<!--
 * @Author: yaohebin
 * @Date: 2021-06-11 09:56:48
 * @LastEditTime: 2023-11-30 18:39:00
 * @LastEditors: yaohebin
 * @Description: nrm
-->

# nrm

> **参考**
>
> [《nrm 使用详解》（作者：越前君）【来源：掘金】](https://juejin.cn/post/6931597891182002183)
>
> [《nrm 的使用（安装、配置、查看、切换、增加、删除、测试）》（作者：小阮 2018）【来源：CSDN】](https://blog.csdn.net/qq_38872934/article/details/105706101)
>
> [《用 nrm 一键切换 npm 源》（作者：wangmeijian）【来源：博客园】](https://www.cnblogs.com/wangmeijian/p/7072053.html)

npm 的镜像管理工具。

## `npm config list` (查看 `npm` 源地址)

可以看到 `npm` 的源 `metrics-registry = "https://registry.npmjs.org/"`

国内常用的镜像地址如淘宝 npm：`https://registry.npm.taobao.org/`

- 修改 registry，在终端输入：

```bash
npm set registry https://registry.npm.taobao.org/
```

- 删除 registry

```bash
npm config rm registry
```

反复添加删除切换源很不方便，所以有了 [`nrm`](https://github.com/Pana/nrm)，用来专门管理切换 registry

## 安装及使用

- 安装

```bash
npm install -g nrm
```

- 列出所有可用源

```bash
nrm ls

* npm ---- https://registry.npmjs.org/
  cnpm --- http://r.cnpmjs.org/
  taobao - https://registry.npm.taobao.org/
  nj ----- https://registry.nodejitsu.com/
  npmMirror  https://skimdb.npmjs.com/registry/
  edunpm - http://registry.enpmjs.org/
```

`nrm ls` 可以列出所有可用的源名及地址，`*` 标志为当前所用源

- 查看当前使用源

```bash
nrm current

npm
```

`nrm current` 输出 `npm` 为当前所用源的名称

- 切换源

```bash
nrm use cnpm
```

`nrm use cnpm` 输出 `Registry has been set to: http://r.cnpmjs.org/` 表示当前所用源已切换为 `cnpm`

- 添加源

```bash
nrm add xxxx https://registry.npm.xxxx.org
```

可以添加一个名为 `xxxx` 的源

- 测试源的速度

```bash
nrm test [源名]
```

- 删除源

```bash
nrm del xxxx
```
