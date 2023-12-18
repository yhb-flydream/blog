<!--
 * @Author: yaohebin
 * @Date: 2023-12-14 16:18:21
 * @LastEditTime: 2023-12-18 21:32:04
 * @LastEditors: yaohebin
 * @Description: hooks 原理
-->

# hooks 原理

> **参考**
>
> [《vue2 @hook 的解析与妙用》(作者：可缺不可滥)【来源：CSND】](https://blog.csdn.net/glorydx/article/details/129259977)
>
> [《Vue 官方文档里没告诉你的神秘钩子——@hook》(作者：tuisemo)【来源：掘金】](https://juejin.cn/post/7006616545119961101)

## 原理

```ts
export function callHook(vm: Component, hook: string) {
  // #7573 disable dep collection when invoking lifecycle hooks
  pushTarget()
  const handlers = vm.$options[hook]
  const info = `${hook} hook`
  if (handlers) {
    for (let i = 0, j = handlers.length; i < j; i++) {
      invokeWithErrorHandling(handlers[i], vm, null, vm, info)
    }
  }
  if (vm._hasHookEvent) {
    vm.$emit('hook:' + hook)
  }
  popTarget()
}
```

在源码中，每个生命周期钩子函数都会调用 callHook 函数，执行完钩子函数后，会有一个判断 `vm._hasHookEvent`，这里就是判断是否有 hook 钩子函数被添加到组件上，如果有添加，则执行这个 hooks 函数

## 使用场景

- 在需要监听的子组件上使用

```vue
<template>
  <Child @hook:mounted="onChildMounted" />
</template>
```

- 优化代码结构

```js
// 优化前
export default {
  data() {
    return {
      timer:null
    }
  }
  mounted () {
    this.timer = setInterval(() => { }, 1000);
  }
  beforeDestroy () {
    clearInterval(this.timer)
  }
}

// 优化后
export default {
  mounted () {
    const timer = setInterval(() => { }, 1000);
    this.$once('hook:beforeDestroy', function () {
      clearInterval(timer)
    })
  }
}
```
