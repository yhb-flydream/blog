<!--
 * @Author: yaohebin
 * @Date: 2021-02-02 07:53:39
 * @LastEditTime: 2022-10-06 09:19:03
 * @LastEditors: yaohebin
 * @Description: Object.defineProperty&Proxy
-->

# Object.defineProperty&Proxy

> **参考**
>
> [《Object.defineProperty()》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Object/defineProperty)
>
> [《Proxy》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Proxy)
>
> [《Proxy》(作者：阮一峰)【来源：ECMAScript 6 入门】](https://es6.ruanyifeng.com/#docs/proxy)

[TOC]

## Object.defineProperty

### Object.defineProperty 特点

- 修改一个对象的现有属性，或在一个对象上定义一个新属性
- 如果需要监听一个对象，需要遍历对象的所有属性，给对象的每一个属性就行设置
- 如果对象属性有引用类型的数据，则需要对该引用属性的值，在进行遍历设置
- 如果对象有新增属性，需要重新对该属性进行设置
- 不支持监听数组的变化
  - 重写了数组的一些方法
  - `push、pop、shift、unshift、splice、sort、reverse`

---

- Vue 关于对象数据：
  - 初始化传入 data 的数据可进行响应式
  - 如果初始化没定义，后添加的数据需要使用 this.$set(obj, key, value) 去设置才会有响应式
- Vue 关于数组数据：
  - 初始化传入 data 的数据可进行响应式
  - 直接替换原数组没问题
  - 直接修改数组中对象的属性也没问题
  - 使用数组方法操作也没问题，因为 vue 代理了数组原型上的方法
  - 使用索引操作不能响应式，如果想要有响应式需要使用 this.$set(arr, index, value) 去设置才会有响应式

## Proxy

### Proxy 特点

- 可以直接监听整个对象的变化
- 可以直接监听数组的变化
- Proxy 有多达 13 种拦截方法：不限于 get、set、has、deleteProperty、apply、ownKeys、construct 等等；除开 get 和 set 其他都是 defineProperty 不具备的
- Proxy 返回的是一个新对象，我们可以只操作新的对象达到目的；defineProperty 只能遍历对象属性直接修改
- Proxy 的存在浏览器兼容性问题，兼容性不如 Object.defineProperty
