# DOM 事件流

> **参考**
>
> [《搞懂 js 事件、事件流(捕获冒泡)、事件委托》(作者：广州蓝景实训部)【来源：知乎】](https://zhuanlan.zhihu.com/p/321432610)
>
> [《JavaScript 事件之事件冒泡与时间捕获（总结分享）》(作者：长期闲置)【来源：php 中文网】](https://www.php.cn/js-tutorial-486454.html)

[TOC]

## DOM 事件流阶段

### 捕获阶段

> Netscape 提出的事件流为事件捕获，事件捕获和事件冒泡是刚好相反的，事件捕获是指不太具体的节点应该更早的接收到事件，而最具体的节点应该最后接收到事件

### 目标阶段

### 冒泡阶段

> IE 的事件流称为事件冒泡，事件开始由最具体的元素接收，然后逐级向上传播到较为不具体的节点（文档）

- 那么冒泡的终点是哪里呢？
  - 最终可以冒泡到 window 上，即使是有 iframe 的话，也是不影响的
  - 比如，我们把这个页面嵌入到另外一个页面中， 最终也是会冒泡到这个页面的 window，即使是在 iframe 上添加一个 click 事件，也是不会冒泡到这个 iframe 上的，即事件的冒泡是相互独立的。

## DOM 级事件处理

### DOM0 级事件

通过 DOM0 级方法指定事件处理程序方法很简单，首先取得一个要操作元素的引用，然后直接将一个函数赋值给该元素的对应事件处理程序属性。

每个元素包括 window 和 document 都拥有自己的事件处理程序属性。**注意，这种方法添加的事件处理程序将在事件流的冒泡阶段被处理。**

- 事件处理程序属性全部小写，以”on”开头，后面跟事件类型：
  - onclick
  - onload
  - oninput
  - onfocus
  - onblur
- 每个元素如 img、a、input、form 包括 window 和 document 都拥有自己的事件处理程序属性。
  - `document.getElementById("btn1").onclick`
  - `document.getElementById("img1").onclick`
  - `document.getElementById("img1").onerror`

**注意：如果以上代码处于文档的底部，在页面刚刚加载时，我们将鼠标移动到 img1 上面。有可能由于代码尚未执行，不会弹出我们设定的对话框！如今，这个延迟已经十分短暂。**

### DOM2 级事件

#### `addEventListener`、`removeEventListener`

参数：

1. 要处理事件名(不含 on)
2. 事件处理函数
3. 布尔变量
   - false（默认）在冒泡阶段处理事件函数
   - true 在捕获阶段处理事件函数

与 DOM0 级事件区别：

1. DOM2 不依赖事件处理程序属性
2. 可以同时对对象的同一事件注册多个处理程序，它们按照注册顺序依次执行。

我们可以用 `removeEventListener` 方法来删除我们刚才指定的事件处理程序，注意参数要保持一致：

```js
btn1.removeEventListener('click', handle2, false)
```

**要特别注意的是：如果我们使用匿名函数指定事件处理程序，便无法使用 `removeEventListener()` 方法删除事件处理程序：**

```js
btn1.addEventListener(
  'click',
  function () {
    alert('click!')
  },
  false
)

btn1.removeEventListener(
  'click',
  function () {
    alert('click!')
  },
  false
) //无法取消！
```

**这样是无法取消以上指定的事件处理程序的！因为上面 `addEventListener` 和 `removeEventListener` 中的 2 个事件处理函数虽然代码相同，`实质上是 2 个不同的函数引用`。**

## IE 事件处理程序及跨浏览器支持

### `attachEvent` 与 `detachEvent`

这两个方法只接收 2 个参数：

1. 事件名称
2. 事件处理函数

由于 IE8 及更早版本只支持事件冒泡，这两个方法添加的事件处理程序会在事件冒泡阶段被执行。

和 DOM2 不同的是：

1. IE 事件处理方法运行作用域为全局作用域，this 指代 window；
2. 第一个参数事件名以 on 为前缀；
3. 当为同一对象的相同事件指定多个处理程序时，**执行顺序和 DOM2 相反，IE 中以添加它们的相反顺序执行**。

### 跨浏览器支持

```js
var EventUtil = {
  addEventHandler: function (element, eventType, handler) {
    if (element.addEventListener) {
      element.addEventListener(eventType, handler, flase)
    } else if (element.attachEvent) {
      element.attachEvent('on' + eventType, handler)
    } else {
      element['on' + eventType] = handler
    }
  },

  removeEventHandler: function (element, eventType, handler) {
    if (element.removeEventListener) {
      element.removeEventListener(eventType, handler, flase)
    } else if (element.detachEvent) {
      element.detachEvent('on' + eventType, handler)
    } else {
      element['on' + eventType] = null
    }
  },
}
```

## 事件对象

在触发某个事件时，会产生一个 `event` 对象。该对象中包含与事件有关的信息。例如触发事件的元素、事件的类型、与特定事件相关的如鼠标位置信息等。

### DOM 事件对象

`event` 对象中包含有大量的有关事件的属性和方法（例如 `event.stopPropagation` 方法可用于停止事件在捕获或者冒泡阶段的继续传播，`event.preventDefault` 方法会取消阻止事件的行）

| 属性/方法       | 值类型   | 读写     | 描述                                                                 |
| --------------- | -------- | -------- | -------------------------------------------------------------------- |
| currentTarget   | Element  | readonly | 事件处理程序当前正在处理的元素                                       |
| target          | Element  | readonly | 事件的目标                                                           |
| type            | String   | readonly | 触发事件的类型                                                       |
| preventDefault  | Function | readonly | 取消事件默认行为，如链接的默认行为就是被单击时跳转到 href 指定的 url |
| stopPropagation | Function | readonly | 取消事件进一步冒泡或捕获                                             |

### IE 事件对象与跨浏览器事件对象

#### IE 事件对象

在 IE 中，当使用 DOM0 级指定事件处理程序时，`event` 对象被认为是 window 的一个属性，例如获取鼠标点击坐标的代码：

```js
var mouseLoc = function () {
  var loc = 'x: ' + window.event.screenX + '\n' + 'y: ' + window.event.screenY
  alert(loc)
}
```

当使用 `attachEvent` 方法指定事件处理程序时，`event` 对象会被作为参数传入事件处理程序，我们将以上的代码重写：

```js
var mouseLoc = function (event) {
  var loc = 'x: ' + event.screenX + '\n' + 'y: ' + event.screenY
  alert(loc)
}
btn1.attachEvent('onclick', mouseLoc)
```

| 属性/方法    | 值类型  | 读写       | 描述                                                                 |
| ------------ | ------- | ---------- | -------------------------------------------------------------------- |
| cancelBubble | Boolean | read/write | 默认为 false，置为 true 时取消事件冒泡（同 DOM 中 stopPropagation）  |
| returnValue  | Boolean | read/write | 默认为 true，设为 false 取消事件默认行为（同 DOM 中 preventDefault） |
| srcElement   | Element | readonly   | 事件目标                                                             |
| type         | String  | readonly   | 事件目标                                                             |

#### 跨浏览器事件对象

```js
var EventUtil = {
  addEventHandler: function (element, eventType, handler) {
    if (element.addEventListener) {
      element.addEventListener(eventType, handler, flase)
    } else if (element.attachEvent) {
      element.attachEvent('on' + eventType, handler)
    } else {
      element['on' + eventType] = handler
    }
  },

  removeEventHandler: function (element, eventType, handler) {
    if (element.removeEventListener) {
      element.removeEventListener(eventType, handler, flase)
    } else if (element.detachEvent) {
      element.detachEvent('on' + eventType, handler)
    } else {
      element['on' + eventType] = null
    }
  },

  getEvent: function (event) {
    return event ? event : window.event
  },

  getTarget: function (event) {
    return event.target || event.srcElement
  },

  preventDefault: function (event) {
    if (event.preventDefault) {
      event.preventDefault()
    } else {
      event.returnValue = false
    }
  },

  stopPropagation: function (event) {
    if (event.stopPropagation) {
      event.stopPropagation()
    } else {
      event.cancelBubbles = true
    }
  },

  getRelatedTarget: function (event) {
    if (event.relatedTarget) {
      return event.relatedTarget
    } else if (event.toElement) {
      return event.toElement
    } else if (event.fromElement) {
      return event.fromElement
    } else {
      return null
    }
  },
}
```
