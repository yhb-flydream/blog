# instanceof

```js
function myInstanceof(left, right) {
  let leftProto = Object.getPrototypeOf(left)
  prototype = right.prototype
  while (true) {
    if (!leftProto) return false
    if (leftProto === prototype) return true
    leftProto = Object.getPrototypeOf(leftProto)
  }
}
```
