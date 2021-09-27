# definedProperty&Proxy

## definedProperty

**数据劫持**结合**发布者-订阅者模式**。`Object.defineProperty()`

## Proxy

Vue3.x 改用 `Proxy` 替代 `Object.defineProperty` 实现数据监听。

但 Proxy 只会代理对象的第一层，所以需要判断 `Reflect.get` 的返回值是否为 Object 再通过 `reactive` 方法做代理，这样就实现了深度观测。

监测数组的时候可能触发多次 `get/set`，可以判断 key 是否为当前被代理对象 target 自身属性，也可以判断旧值与新值是否相等，只有满足以上两个条件之一时，才有可能执行 trigger。

## 优势

### Proxy 可以直接监听对象；defineProperty 需要遍历对象属性进行监听

### Proxy 可以直接监听对象新增的属性；defineProperty 只能劫持一开始就存在的属性，新增属性需要手动 Observer

### Proxy 可以直接监听数组的变化；defineProperty 无法监听数组的变化

### Proxy 有多达 13 种拦截方法：不限于 get、set、has、deleteProperty、apply、ownKeys、construct 等等；除开 get 和 set 其他都是 defineProperty 不具备的

### Proxy 返回的是一个新对象，我们可以只操作新的对象达到目的；defineProperty 只能遍历对象属性直接修改

## 劣势

### Proxy 的存在浏览器兼容性问题
