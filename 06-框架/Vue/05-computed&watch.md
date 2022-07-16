<!--
 * @Author: yaohebin
 * @Date: 2021-02-02 07:54:46
 * @LastEditTime: 2022-07-15 15:07:10
 * @LastEditors: yaohebin
 * @Description: 
-->
# computed&watch

> **参考**
>
> [《【Vue】谈 Vue 的依赖追踪系统 ——搞懂 methods watch 和 compute 的区别和联系》(作者：外婆的)【来源：博客园】](https://www.cnblogs.com/penghuwan/p/7194133.html)

watch 和 computed 都是以 Vue 的依赖追踪机制为基础的。

## computed

- 依赖其他属性，由其他属性计算而来
- computed 带有缓存功能，只有依赖型数据发生改变，computed 才会重新计算
- 都有一个 get 和一个 set 方法，当数据变化时，调用 set 方法进行其他操作

## watch

- 监听 Vue 实例上的属性变化，或某些特定数据的变化，然后执行某些具体的业务逻辑操作
- 当需要在数据变化时执行异步或开销较大的操作时，watch 方式是最有用的。所以 watch 一定是支持异步的。
