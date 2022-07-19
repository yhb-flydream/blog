# mixins

> **参考**
>
> [《混入》【来源：Vue.js】](https://cn.vuejs.org/v2/guide/mixins.html)

[TOC]

混入 (mixin) 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。**当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。**

## 选项合并

当组件和混入对象含有同名选项时，这些选项将以恰当的方式进行“合并”。

数据对象：

- 在内部会进行递归合并，并在发生冲突时以**组件数据优先**。

```js
var mixin = {
  data: function () {
    return {
      message: 'hello',
      foo: 'abc',
    };
  },
};

new Vue({
  mixins: [mixin],
  data: function () {
    return {
      message: 'goodbye',
      bar: 'def',
    };
  },
  created: function () {
    console.log(this.$data);
    // => { message: "goodbye", foo: "abc", bar: "def" }
  },
});
```

同名钩子函数：

- 将合并为一个数组，因此都将被调用。
- 另外，**混入对象的钩子将在组件自身钩子之前调用。**

```js
var mixin = {
  created: function () {
    console.log('混入对象的钩子被调用');
  },
};

new Vue({
  mixins: [mixin],
  created: function () {
    console.log('组件钩子被调用');
  },
});

// => "混入对象的钩子被调用"
// => "组件钩子被调用"
```

值为对象的选项，例如 methods、components 和 directives：

- 将被合并为同一个对象。
- 两个对象键名冲突时，取组件对象的键值对。

```js
var mixin = {
  methods: {
    foo: function () {
      console.log('foo');
    },
    conflicting: function () {
      console.log('from mixin');
    },
  },
};

var vm = new Vue({
  mixins: [mixin],
  methods: {
    bar: function () {
      console.log('bar');
    },
    conflicting: function () {
      console.log('from self');
    },
  },
});

vm.foo(); // => "foo"
vm.bar(); // => "bar"
vm.conflicting(); // => "from self"
```

**注意：`Vue.extend()` 也使用同样的策略进行合并。**

## 全局混入

混入也可以进行全局注册。**使用时格外小心！一旦使用全局混入，它将影响每一个之后创建的 Vue 实例**。使用恰当时，这可以用来为自定义选项注入处理逻辑。

```js
// 为自定义的选项 'myOption' 注入一个处理器。
Vue.mixin({
  created: function () {
    var myOption = this.$options.myOption;
    if (myOption) {
      console.log(myOption);
    }
  },
});

new Vue({
  myOption: 'hello!',
});
// => "hello!"
```
