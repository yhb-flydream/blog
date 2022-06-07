# vue-router

> **参考**
>
> [《Location》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/Location)
>
> [《History》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/History)
>
> [《Window: hashchange event》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/hashchange_event)
>
> [《popstate》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/Window/popstate_event)
>
> [《深度剖析：前端路由原理》(作者：我是你的超级英雄)【来源：掘金】](https://juejin.cn/post/6844903906024095751)

[TOC]

## 编程式的导航

### router.push()

类似于 `window.history.pushState()`

这个方法会向 history 栈添加一个新的记录，所以，当用户点击浏览器后退按钮时，则回到之前的 URL。

### router.replace()

类似于 `window.history.replaceState()`

跟 router.push 很像，唯一的不同就是，它**不会向 history 添加新记录，而是跟它的方法名一样 —— 替换掉当前的 history 记录**。

### router.go()

类似于 `window.history.go(n)`

这个方法的参数是一个**整数**，意思是在 history 记录中向前或者后退多少步。

### router.back()

### router.forward()

## 路由钩子函数

### 全局守卫

#### 全局前置守卫 router.beforeEach

当一个导航触发时，全局前置守卫按照创建顺序调用。守卫是异步解析执行，此时导航在所有守卫 resolve 完之前一直处于 等待中。

#### 全局解析守卫 router.beforeResolve

在 2.5.0+ 你可以用 router.beforeResolve 注册一个全局守卫。这和 router.beforeEach 类似，区别是在导航被确认之前，**同时在所有组件内守卫和异步路由组件被解析之后，解析守卫就被调用**。

#### 全局后置守卫 router.afterEach

钩子不会接受 next 函数也不会改变导航本身

### 路由独享守卫

与全局前置守卫的方法参数是一样的

```js
const router = new VueRouter({
  routes: [
    {
      path: '/foo',
      component: Foo,
      beforeEnter: (to, from, next) => {
        // ...
      },
    },
  ],
})
```

### 组件内守卫

#### beforeRouteEnter

- 在渲染该组件的对应路由被 confirm 前调用
- **不！能！获取组件实例 `this`**
- 因为当守卫执行前，组件实例还没被创建
- 不过，可以通过传一个回调给 `next` 来访问组件实例。在导航被确认的时候执行回调，并且把组件实例作为回调方法的参数。
- **beforeRouteEnter 是支持给 next 传递回调的唯一守卫。**

```js
beforeRouteEnter (to, from, next) {
  next(vm => {
    // 通过 `vm` 访问组件实例
  })
}
```

#### beforeRouteUpdate

- 在当前路由改变，但是该组件被复用时调用
- 举例来说，对于一个带有动态参数的路径 `/foo/:id`，在 `/foo/1` 和 `/foo/2` 之间跳转的时候，
- 由于会渲染同样的 Foo 组件，因此组件实例会被复用。而这个钩子就会在这个情况下被调用。
- 可以访问组件实例 `this`

#### beforeRouteLeave

- 导航离开该组件的对应路由时调用
- 可以访问组件实例 `this`
- 离开守卫通常用来禁止用户在还未保存修改前突然离开。
- 该导航可以通过 `next(false)` 来取消。

### 完整的导航解析流程

1. 导航被触发。
2. 在失活的组件里调用 `beforeRouteLeave` 守卫。
3. 调用全局的 `beforeEach` 守卫。
4. 在重用的组件里调用 `beforeRouteUpdate` 守卫 (2.2+)。
5. 在路由配置里调用 `beforeEnter`。
6. 解析异步路由组件。
7. 在被激活的组件里调用 `beforeRouteEnter`。
8. 调用全局的 `beforeResolve` 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 `afterEach` 钩子。
11. 触发 DOM 更新。
12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入。

## hash 模式和 history 模式

### hash 模式

- URL 中的 hash 值只是客户端的一种状态，也就是说当向服务器发出请求时，hash 部分不会被发送
- hash 值的改变都会在浏览器的访问历史中增加一个记录，因此我们能通过浏览器的回退、前进控制 hash 的切换
- 可以通过 a 标签设置 href 属性，当用户点击后，URL 的 hash 会改变，或者通过使用 JavaScript 来对 `location.hash` 进行赋值，改变 URL 的 hash 值
- 可以使用 `hashchange` 事件来监听 hash 值的改变，从而进行页面的跳转

### history 模式

- HTML5 提供了 History API 来实现 URL 的变化，其中主要的 API 有以下两个：
  - `history.pushState()` 可以增加一个历史记录
  - `history.replaceState()` 替换当前的记录
- 可以使用 `popstate` 事件来监听 URL 的变化，对页面进行跳转
- history.pushState() 和 history.replaceState() 不会触发 `popstate` 事件，需要手动触发页面跳转
- history 模式需要后端正确配置路由指向映射，确保页面都可以访问到
