<!--
 * @Author: yaohebin
 * @Date: 2021-02-02 07:57:17
 * @LastEditTime: 2023-11-27 13:36:18
 * @LastEditors: yaohebin
 * @Description: diff
-->
# diff

> **参考**
>
> [《深入剖析：Vue 核心之虚拟 DOM》(作者：我是你的超级英雄)【来源：掘金】](https://juejin.cn/post/6844903895467032589)
>
> [《深入 Vue2.x 的虚拟 DOM diff 原理》(作者：小时光茶社)【来源：CSDN】](https://blog.csdn.net/M6i37JK/article/details/78140159)
>
> [《vue 虚拟 dom 和 diff 算法详解》(作者：ら陈佚晨)【来源：CSDN】](https://blog.csdn.net/weixin_42707287/article/details/113994483)
>
> [《15 张图，20 分钟吃透 Diff 算法核心原理，我说的！！！》(作者：Sunshine_Lin)【来源：掘金】](https://juejin.cn/post/6994959998283907102)
>
> [《DIff 算法看不懂就一起来砍我(带图)》(作者：渣渣 xiong)【来源：掘金】](https://juejin.cn/post/7000266544181674014)

Vue 的 Diff 算法只会同层级比较。因为在大部分情况下，用户跨层级的移动操作特别少，可以忽略不计。

1. 逐层对比【因为同层元素移动比较常见】

   - 如果都是静态节点可以直接跳过。
   - 都是文本节点就直接替换内容（如果内容不相等）。
   - 如果只有新节点，就新增节点。只有只有老节点，就移除。
   - 如果新旧节点都有子节点，就继续比较子节点。循环新的节点，每一个节点都去老的同层节点里遍历，找到了就在进行子元素的比较，继续重复上述流程。如果没有找到，就生成新元素。

2. 算法优化【因为每次全部循环太浪费性能】

   - 静态节点可以直接跳过，例如内容里只有文本。因为不会产生变化。
   - 双端对比（快捷检索），在同级移动的情况下（移除、移动、修改某节点），大多数节点的前后节点都是不变的。所以可以假设同坐标的元素是不变的。所以可以进行 4 种双端比较：
     - 新前和旧前（比如只是在列表后新增了元素，那么前面不变的就会在这里返回，直接复用）
     - 新后和旧后（比如只是在列表前新增了元素，上一条没通过，就会一直尾元素比较，直接复用）
     - 新后和旧前
     - 新前和旧后
   - key 优化
     - 将没有通过快捷检索的所有旧元素节点，上的 key 放到数组 oldIndexs 中。（移动后指针开始和结尾中为处理的元素）
     - 如果新的元素上面有 key 值，就去数组中 oldIndexs 查找。找到了就继续对比 2 个元素子节点。（记得移动位置）
     - 如果新的元素上面没有 key 值，就去未出里旧元素中看是否相同的节点，有的话就返回索引 index。
     - 通过 index，复用元素就继续对比 2 个元素子节点。（记得移动位置）

3. 删除多余元素

   - 新的数组和老的数组如果其中 1 个循环完，就退出循环。
   - 如果新的 Vnode 循环完 oldStartIdx > oldEndIdx，那就移除 oldVnode 中未处理的节点。
   - 如果旧的 Vnode 循环完 newStartIdx > newEndIdx，说明 newVode 还有剩余。创建新节点，插入 Dom 中。

## 优点

- **跨平台**：Virtual DOM 是以 JavaScript 对象为基础而不依赖真实平台环境，所以使它具有了跨平台的能力，比如说浏览器平台、Weex、Node 等。
- **提高 DOM 操作效率**：把大量的 DOM 操作搬运到 Javascript 中，运用 patching 算法来计算出真正需要更新的节点，最大限度地减少 DOM 操作（开发者角度，开发者自己可能操作不当，写出低性能的代码），从而提高性能。
- **提升渲染性能**：在大量、频繁的数据更新下，依托 diff 算法，能够对视图进行合理、高效的更新。
