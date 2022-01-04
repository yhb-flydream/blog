# qiankun 实践问题

## Loading chunk 1 failed

**原因：**

有可能是路由组件采用了 require.context 方式自动懒加载收集路由组件

**解决：**

使用 `() => import('@/views/xxx')` 的形式手动添加

## 子应用路由失效

**描述：**

从基座应用进入子应用，然后切换到基座应用，再次进入子应用时，子应用路由失效，不走子应用的路由钩子函数

**原因：**

当子应用 unmount 的时候没有重置 router，导致重新进入子应用时 router.app 还是绑定旧的 Vue 实例

![子应用路由失效](../../_images/06-框架/微前端/微前端-001.png)

<!-- ![备用连接](https://i.loli.net/2021/10/29/bJfKPkgpLdN9hYx.png) -->

**解决：**

unmount 的时候重置一下，router.app 和 router.apps

```js
export async function unmount() {
  instance.$destroy()
  instance.$el.innerHTML = ''
  instance = null
  router.app = null
  router.apps = []
}
```
