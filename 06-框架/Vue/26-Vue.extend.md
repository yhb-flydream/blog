# Vue.extend

> **参考**
>
> [《Vue.extend( options )》【来源：vuejs】](https://cn.vuejs.org/v2/api/#Vue-extend)
>
> [《vue.extend, vue.component 区别》【来源：思否】](https://segmentfault.com/q/1010000007312426)

[TOC]

> 使用基础 Vue 构造器，创建一个“子类”。参数是一个包含组件选项的对象。

**data 选项是特例，需要注意 - 在 Vue.extend() 中它必须是函数。**

```html
<template>
  <div id="mount-point"></div>
</template>

<script>
  // 创建构造器
  var Profile = Vue.extend({
    template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
    data: function () {
      return {
        firstName: 'Walter',
        lastName: 'White',
        alias: 'Heisenberg',
      }
    },
  })
  // 创建 Profile 实例，并挂载到一个元素上。
  new Profile().$mount('#mount-point')
</script>
```

```html
<p>Walter White aka Heisenberg</p>
```

---

```js
//  src/global-api/initExtend.js
import { mergeOptions } from '../util/index'
export default function initExtend(Vue) {
  let cid = 0 //组件的唯一标识
  // 创建子类继承Vue父类 便于属性扩展
  Vue.extend = function (extendOptions) {
    // 创建子类的构造函数 并且调用初始化方法
    const Sub = function VueComponent(options) {
      this._init(options) //调用Vue初始化方法
    }
    Sub.cid = cid++
    Sub.prototype = Object.create(this.prototype) // 子类原型指向父类
    Sub.prototype.constructor = Sub //constructor指向自己
    Sub.options = mergeOptions(this.options, extendOptions) //合并自己的options和父类的options
    return Sub
  }
}
```

## vue.extend, vue.component 区别

- Vue.extend 返回的是一个“扩展实例构造器”，也就是一个预设了部分选项的 Vue 实例构造器
- Vue.component 是用来全局注册组件的方法
  - 其作用是将通过 Vue.extend 生成的扩展实例构造器注册（命名）为一个组件
  - 可以简单理解为当在模板中遇到该组件名称作为标签的自定义元素时，会自动调用类似于 new myVue 这样的构造函数来生成组件实例，并挂载到自定义元素上，当然实际情况要比这复杂得多，还需要处理 props 数据传递、slot 内容分发、自定义事件、组件生命周期……事宜。

```js
var myVue = Vue.extend({
  // 预设选项
}) // 返回一个“扩展实例构造器”

// 然后就可以这样来使用
var vm = new myVue({
  // 其他选项
})
```
