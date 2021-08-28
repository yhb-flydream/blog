# WebWorker

> **参考**
>
> [《Web Workers API》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API)
>
> [《使用 Web Workers》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/Web_Workers_API/Using_web_workers)

[TOC]

通过使用 Web Workers，Web 应用程序可以在独立于主线程的后台线程中，运行一个脚本操作。这样做的好处是可以在独立线程中执行费时的处理任务，从而允许主线程（通常是 UI 线程）不会因此被阻塞/放慢。

## Web Workers 概念与用法

**worker 将执行哪里的程序：**

使用构造函数（例如,Worker()）创建一个 worker 对象, 构造函数接受一个 JavaScript 文件 URL — 这个文件包含了将在 worker 线程中运行的代码。

**worker 的上下文：**

worker 将运行在与当前 window 不同的另一个全局上下文中，这个上下文由一个对象表示，标准情况下为 DedicatedWorkerGlobalScope （标准 workers 由单个脚本使用; 共享 workers 使用 SharedWorkerGlobalScope）。**一个专用 worker 仅仅能被首次生成它的脚本使用，而共享 worker 可以同时被多个脚本使用**。

因此，在 Worker 内通过 window 获取全局作用域 (而不是 self) 将返回错误。

**worker 里面不能操作的东西：**

你可以在 worker 线程中运行任意的代码，但注意存在一些例外：

- 直接在 worker 线程中**操纵 DOM 元素**；
- 或**使用 window 对象中的某些方法和属性**
- **大部分 window 对象的方法和属性是可以使用的**，包括 WebSockets，以及诸如 IndexedDB 和 FireFox OS 中独有的 Data Store API 这一类数据存储机制。

**worker 与主线程怎么通信：**

主线程和 worker 线程相互之间使用 `postMessage()` 方法来发送信息, 并且通过 `onmessage` 这个 event handler 来接收信息（传递的信息包含在 Message 这个事件的 data 属性内) 。_数据的交互方式为传递副本，而不是直接共享数据_。

**worker 生成新的 worker：**

worker 可以另外生成新的 worker，这些 worker 与它们父页面的宿主相同。此外，worker 可以通过 XMLHttpRequest 来访问网络，只不过 XMLHttpRequest 的 responseXML 和 channel 这两个属性的值将总是 null 。

**其他 worker：**

- **Shared Workers**
  - 可被不同的窗体的多个脚本运行，例如 IFrames 等，只要这些 workers 处于同一主域。
  - 共享 worker 比专用 worker 稍微复杂一点 — 脚本必须通过活动端口进行通讯。
- **Service Workers**
  - 一般作为 web 应用程序、浏览器和网络（如果可用）之间的代理服务。
  - 他们旨在（除开其他方面）创建有效的离线体验，拦截网络请求，以及根据网络是否可用采取合适的行动，更新驻留在服务器上的资源。
  - 他们还将允许访问推送通知和后台同步 API。
- **Chrome Workers**
  - 一种仅适用于 firefox 的 worker
- **音频 Workers**
  - 可以在网络 worker 上下文中直接完成脚本化音频处理

## 专用 worker

**一个专用 worker 仅仅能被生成它的脚本所使用。**

### worker 特性检测

为了更好的错误处理控制以及向下兼容，将你的 worker 运行代码包裹在以下代码中是一个很好的想法(main.js)：

```js
// main.js
if (window.Worker) {
  // ...
}
```

### 生成一个专用 worker

创建一个新的 worker 很简单。你需要做的是调用 Worker() 的构造器，指定一个脚本的 URI 来执行 worker 线程（main.js）：

```js
// main.js
var myWorker = new Worker('worker.js')
```

### 专用 worker 中消息的接收和发送

你可以通过 postMessage() 方法和 onmessage 事件处理函数触发 workers 的方法。当你想要向一个 worker 发送消息时，你只需要这样做（main.js）：

```js
// main.js
first.onchange = function () {
  myWorker.postMessage([first.value, second.value])
  console.log('first.onchange --- Message posted to worker')
}

second.onchange = function () {
  myWorker.postMessage([first.value, second.value])
  console.log('second.onchange --- Message posted to worker')
}
```

这段代码中变量 first 和 second 代表 2 个`<input>`元素；它们当中任意一个的值发生改变时，`myWorker.postMessage([first.value,second.value])`会将这 2 个值组成数组发送给 worker。你可以在消息中发送许多你想发送的东西。

在 worker 中接收到消息后，我们可以写这样一个事件处理函数代码作为响应（worker.js）：

```js
// worker.js
onmessage = function (e) {
  console.log('Message received from main script')
  var workerResult = 'Result: ' + e.data[0] * e.data[1]
  console.log('Posting message back to main script')
  postMessage(workerResult)
}
```

onmessage 处理函数允许我们在任何时刻，一旦接收到消息就可以执行一些代码，代码中消息本身作为事件的 data 属性进行使用。这里我们简单的对这 2 个数字作乘法处理并再次使用 postMessage()方法，将结果回传给主线程。

回到主线程，我们再次使用 onmessage 以响应 worker 回传的消息：

```js
// main.js
myWorker.onmessage = function (e) {
  result.textContent = e.data
  console.log('Message received from worker')
}
```

> **注意：** 在主线程中使用时，onmessage 和 postMessage() 必须挂在 worker 对象上，而在 worker 中使用时不用这样做。原因是，在 worker 内部，worker 是有效的全局作用域。
>
> **注意：** 当一个消息在主线程和 worker 之间传递时，它被复制或者转移了，而不是共享。

### 终止 worker

如果你需要从主线程中立刻终止一个运行中的 worker，可以调用 worker 的 terminate 方法：

```js
myWorker.terminate()
```

worker 线程会被立即杀死，不会有任何机会让它完成自己的操作或清理工作。

而在 worker 线程中，workers 也可以调用自己的 close 方法进行关闭：

```js
close()
```

### 处理错误

当 worker 出现运行中错误时，它的 onerror 事件处理函数会被调用。它会收到一个扩展了 ErrorEvent 接口的名为 error 的事件。

该事件不会冒泡并且可以被取消；为了防止触发默认动作，worker 可以调用错误事件的 preventDefault() 方法。

- `message` 可读性良好的错误消息。
- `filename` 发生错误的脚本文件名。
- `lineno` 发生错误时所在脚本文件的行号。

### 生成 subworker

如果需要的话 worker 能够生成更多的 worker。这就是所谓的 subworker，它们必须托管在同源的父页面内。

而且，subworker 解析 URI 时会相对于父 worker 的地址而不是自身页面的地址。这使得 worker 更容易记录它们之间的依赖关系。

### 引入脚本与库

Worker 线程能够访问一个全局函数 importScripts()来引入脚本，该函数接受 0 个或者多个 URI 作为参数来引入资源；

以下例子都是合法的：

```js
importScripts() /* 什么都不引入 */
importScripts('foo.js') /* 只引入 "foo.js" */
importScripts('foo.js', 'bar.js') /* 引入两个脚本 */
```

- 浏览器加载并运行每一个列出的脚本。
- 每个脚本中的全局对象都能够被 worker 使用。
- 如果脚本无法加载，将抛出 NETWORK_ERROR 异常，接下来的代码也无法执行。而之前执行的代码(包括使用 window.setTimeout() 异步执行的代码)依然能够运行。
- importScripts() 之后的函数声明依然会被保留，因为它们始终会在其他代码之前运行。

> **注意：** 脚本的下载顺序不固定，但执行时会按照传入 importScripts() 中的文件名顺序进行。这个过程是同步完成的；直到所有脚本都下载并运行完毕，importScripts() 才会返回。

## 共享 worker

**一个共享 worker 可以被多个脚本使用——即使这些脚本正在被不同的 window、iframe 或者 worker 访问。**

> **注意：** 如果共享 worker 可以被多个浏览上下文调用，所有这些浏览上下文必须属于同源（相同的协议，主机和端口号）。

### 生成一个共享 worker

生成一个新的共享 worker 与生成一个专用 worker 非常相似，只是构造器的名字不同——生成共享 worker 的代码如下：

```js
var myWorker = new SharedWorker('worker.js')
```

一个非常大的区别在于，**与一个共享 worker 通信必须通过端口对象——一个确切的打开的端口供脚本与 worker 通信（在专用 worker 中这一部分是隐式进行的）**。

在传递消息之前，端口连接必须被显式的打开，打开方式是使用 onmessage 事件处理函数或者 start()方法。start()方法的调用只在一种情况下需要，那就是消息事件被 addEventListener()方法使用。

在使用 start()方法打开端口连接时，如果父级线程和 worker 线程需要双向通信，那么它们都需要调用 start()方法。

```js
myWorker.port.start() // 父级线程中的调用
```

```js
port.start() // worker线程中的调用, 假设port变量代表一个端口
```

### 共享 worker 中消息的接收和发送

共享 worker 里面可以发送消息到 worker，但是使用 postMessage() 方法必须被端口对象调用。

```js
// shareWorker.js
squareNumber.onchange = function () {
  myWorker.port.postMessage([squareNumber.value, squareNumber.value])
  console.log('Message posted to worker')
}
```

```js
// worker.js
onconnect = function (e) {
  var port = e.ports[0]
  // 接收消息
  port.onmessage = function (e) {
    var workerResult = 'Result: ' + e.data[0] * e.data[1]
    // 发送消息
    port.postMessage(workerResult)
  }
}
```

首先，当一个端口连接被创建时（例如：在父级线程中，设置 onmessage 事件处理函数，或者显式调用 start()方法时），使用 onconnect 事件处理函数来执行代码。

使用事件的 `ports` 属性来获取端口并存储在变量中。

然后，为端口添加一个消息处理函数用来做运算并回传结果给主线程。在 worker 线程中设置此消息处理函数也会隐式的打开与主线程的端口连接，因此这里跟前文一样，对 port.start()的调用也是不必要的。

最后，回到主脚本，我们处理消息：

```js
myWorker.port.onmessage = function (e) {
  result2.textContent = e.data
  console.log('Message received from worker')
}
```
