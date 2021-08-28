# defer&async

> **参考**
>
> [《defer 和 async 的区别》(作者：两仪)【来源：思否】](https://segmentfault.com/q/1010000000640869)
>
> [《浅谈 script 标签的 defer 和 async》(作者：景初)【来源：思否】](https://segmentfault.com/a/1190000006778717)
>
> [《详解 defer 和 async 的原理及应用》(作者：liuhe688)【来源：CSDN】](https://blog.csdn.net/liuhe688/article/details/51247484)
>
> [《图解 script 标签中的 async 和 defer 属性》(作者：乔珂力)【来源：掘金】](https://juejin.cn/post/6894629999215640583)

[TOC]

浏览器碰到 script 脚本的时候：

1. `<script src="script.js"></script>`
   - 没有 `defer` 或 `async`，浏览器会立即加载并执行指定的脚本，“立即”指的是在渲染该 script 标签之下的文档元素之前，也就是说不等待后续载入的文档元素，读到就加载并执行。
2. `<script async src="script.js"></script>`
   - 有 `async`，加载和渲染后续文档元素的过程将和 script.js 的加载并行进行（异步）。script.js 的执行会在 script.js 加载完之后立即进行，这会阻碍后续文档元素的加载和渲染。
3. `<script defer src="script.js"></script>`
   - 有 `defer`，加载后续文档元素的过程将和 script.js 的加载并行进行（异步），但是 script.js 的执行要在所有元素解析完成之后，`DOMContentLoaded` 事件触发之前完成。

![defer&async](https://image-static.segmentfault.com/28/4a/284aec5bb7f16b3ef4e7482110c5ddbb_fix732)

- `defer` 和 `async` 在网络读取（下载）这块儿是一样的，都是**异步**的（相较于 HTML 解析）
- 它俩的差别在于脚本下载完之后何时执行，显然 `defer` 是最接近我们对于应用脚本加载和执行的要求的
- 关于 `defer`，此图未尽之处在于**它是按照加载顺序执行脚本的**，这一点要善加利用
- `async` 则是一个乱序执行的主，反正对它来说脚本的加载和执行是紧紧挨着的，所以不管你声明的顺序如何，只要它加载完了就会立刻执行
- 仔细想想，`async` 对于应用脚本的用处不大，因为它完全不考虑依赖（哪怕是最低级的顺序执行），不过它对于那些可以不依赖任何脚本或不被任何脚本依赖的脚本来说却是非常合适的
