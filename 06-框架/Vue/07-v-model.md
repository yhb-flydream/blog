<!--
 * @Author: yaohebin
 * @Date: 2021-02-02 07:55:51
 * @LastEditTime: 2022-07-16 09:04:04
 * @LastEditors: yaohebin
 * @Description: v-model
-->

# v-model

> **参考**
>
> [《表单输入绑定》【来源：Vue.js】](https://cn.vuejs.org/v2/guide/forms.html)
>
> [《自定义组件的 v-model》【来源：Vue.js】](https://cn.vuejs.org/v2/guide/components-custom-events.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E7%BB%84%E4%BB%B6%E7%9A%84-v-model)
>
> [《v-model》【来源：Vue.js 技术揭秘】](https://ustbhuangyi.github.io/vue-analysis/v2/extend/v-model.html)

[TOC]

**v-model 本质上是一个语法糖。**

## 基础用法

可以用 v-model 指令在表单 `<input>`、`<textarea>` 及 `<select>` 元素上创建双向数据绑定。

> v-model 会忽略所有表单元素的 **value、checked、selected attribute 的初始值，而总是将 Vue 实例的数据作为数据来源**。
> 你应该通过 JavaScript 在组件的 data 选项中声明初始值。

v-model 在内部为不同的输入元素使用不同的 property 并抛出不同的事件：

- text 和 textarea 元素使用 `value` property 和 `input` 事件；
- checkbox 和 radio 使用 `checked` property 和 `change` 事件；
- select 字段将 `value` 作为 prop 并将 `change` 作为事件。

## 自定义组件的 v-model

一个组件上的 v-model 默认会利用名为 `value` 的 prop 和名为 `input` 的事件，但是像 checkbox、radio 和 select 等类型的输入控件可能会将 `value` attribute 用于不同的目的（如上）。model 选项可以用来避免这样的冲突：

```js
Vue.component('base-checkbox', {
  model: {
    prop: 'checked',
    event: 'change',
  },
  props: {
    checked: Boolean,
  },
  template: `
    <input
      type="checkbox"
      v-bind:checked="checked"
      v-on:change="$emit('change', $event.target.checked)"
    >
  `,
});
```

**注意仍然需要在组件的 props 选项里声明 `checked` 这个 prop。**

## 将原生事件绑定到组件

你可能有很多次想要在一个组件的根元素上直接监听一个原生事件。这时，你可以使用 v-on 的 `.native` 修饰符：

```html
<base-input v-on:focus.native="onFocus"></base-input>
```

由于在组件上添加的原生事件，会被添加到根元素上，若组件的根元素不是类似于 `<input>` 的特定的想要监听的元素时，则添加的原生事件将会失败。

为了解决这个问题，Vue 提供了一个 $listeners property，它是一个对象，里面包含了作用在这个组件上的所有监听器。例如：

```js
{
  focus: function (event) { /* ... */ }
  input: function (value) { /* ... */ },
}
```

有了这个 `$listeners` property，你就可以配合 `v-on="$listeners"` 将所有的事件监听器指向这个组件的某个特定的子元素。对于类似 `<input>` 的你希望它也可以配合 v-model 工作的组件来说，为这些监听器创建一个类似下述 `inputListeners` 的计算属性通常是非常有用的：

```js
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  computed: {
    inputListeners: function () {
      var vm = this;
      // `Object.assign` 将所有的对象合并为一个新对象
      return Object.assign(
        {},
        // 我们从父级添加所有的监听器
        this.$listeners,
        // 然后我们添加自定义监听器，
        // 或覆写一些监听器的行为
        {
          // 这里确保组件配合 `v-model` 的工作
          input: function (event) {
            vm.$emit('input', event.target.value);
          },
        }
      );
    },
  },
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"
        v-bind:value="value"
        v-on="inputListeners"
      >
    </label>
  `,
});
```

## `.sync` 修饰符

在父页面使用子组件：

```html
<text-document v-bind:title.sync="doc.title"></text-document>
```

子组件里触发更改：

```js
this.$emit('update:title', newTitle);
```

> 注意带有 `.sync` 修饰符的 `v-bind` 不能和表达式一起使用 (例如 `v-bind:title.sync="doc.title + '!'"` 是无效的)。
> 取而代之的是，你只能提供你想要绑定的 property 名，类似 v-model。

当我们用一个对象同时设置多个 prop 的时候，也可以将这个 `.sync` 修饰符和 `v-bind` 配合使用：

```html
<text-document v-bind.sync="doc"></text-document>
```

这样会把 doc 对象中的每一个 property (如 title) 都作为一个独立的 prop 传进去，然后各自添加用于更新的 v-on 监听器。

> 将 v-bind.sync 用在一个字面量的对象上，例如 `v-bind.sync="{ title: doc.title }"`，是无法正常工作的，因为在解析一个像这样的复杂表达式的时候，有很多边缘情况需要考虑。
