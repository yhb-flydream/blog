# definedProperty&Proxy

## definedProperty

**数据劫持**结合**发布者-订阅者模式**。`Object.defineProperty()`

## Proxy

Vue3.x 改用 `Proxy` 替代 `Object.defineProperty` 实现数据监听。

但 Proxy 只会代理对象的第一层，所以需要判断 `Reflect.get` 的返回值是否为 Object 再通过 `reactive` 方法做代理，这样就实现了深度观测。

监测数组的时候可能触发多次 `get/set`，可以判断 key 是否为当前被代理对象 target 自身属性，也可以判断旧值与新值是否相等，只有满足以上两个条件之一时，才有可能执行 trigger。
