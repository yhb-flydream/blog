# xhr

> **参考**
>
> [《XMLHttpRequest》【来源：MDN】](https://developer.mozilla.org/zh-CN/docs/Web/API/XMLHttpRequest)
>
> [《XMLHttpRequest Level 2 使用指南》(作者：阮一峰)【来源：阮一峰的网络日志】](http://www.ruanyifeng.com/blog/2012/09/xmlhttprequest_level_2.html)

[TOC]

```js
var xhr = new XMLHttpRequest()
xhr.open('GET', 'example.php')
xhr.onreadystatechange = function (response) {
  if (xhr.readyState === 4 && xhr.status === 200) {
    console.log('xhr.responseText', xhr.responseText)
  } else {
    console.log('xhr.statusText', xhr.statusText)
  }
}
xhr.send()
```

## 构造函数

### XMLHttpRequest()

> 该构造函数用于初始化一个 XMLHttpRequest 实例对象。
> 在调用下列任何其他方法之前，必须先调用该构造函数，或通过其他方式，得到一个实例对象。

## 属性

### onreadystatechange

> 在 XMLHttpRequest 的 readyState 属性发生改变时触发 readystatechange 事件的时候被调用。

### readyState

> 返回一个 XMLHttpRequest 代理当前所处的状态。

一个 XHR 代理总是处于下列状态中的一个：

- 0
  - UNSENT
  - 代理被创建，但尚未调用 open() 方法。
- 1
  - OPENED
  - open() 方法已经被调用。
- 2
  - HEADERS_RECEIVED
  - send() 方法已经被调用，并且头部和状态已经可获得。
- 3
  - LOADING
  - 下载中；responseText 属性已经包含部分数据。
- 4
  - DONE
  - 下载操作已完成。

```js
var xhr = new XMLHttpRequest()
console.log('UNSENT', xhr.readyState) // readyState 为 0

xhr.open('GET', '/api', true)
console.log('OPENED', xhr.readyState) // readyState 为 1

xhr.onprogress = function () {
  console.log('LOADING', xhr.readyState) // readyState 为 3
}

xhr.onload = function () {
  console.log('DONE', xhr.readyState) // readyState 为 4
}

xhr.send(null)
```

> xhr.readyState：XMLHttpRequest 对象的状态，等于 4 表示数据已经接收完毕。
> xhr.status：服务器返回的状态码，等于 200 表示一切正常。
> xhr.responseText：服务器返回的文本数据。
> xhr.responseXML：服务器返回的 XML 格式的数据。
> xhr.statusText：服务器返回的状态文本。
