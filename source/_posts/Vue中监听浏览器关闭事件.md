---
title: 监听浏览器关闭事件，并发送异步请求
date: 2020-06-11 15:42:31
tags:
---
## 项目需求

监听用户关闭浏览器事件，若关闭则发送异步请求给后端。

## 问题

问题1：浏览器关闭事件并不会触发vue实例的beforeDestroy和destroyed函数，所以无法在这两个钩子函数中处理浏览器关闭事件。

解决方式：注册unload事件，参见[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Events/unload)。

问题2：用户代理通常会忽略在unload事件处理器中的异步XMLHttpRequest。

解决方式：使用navigator.sendBeacon(url, data)，参见[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/sendBeacon)

## Vue中的具体实现

``` javascript
// 在created钩子函数中注册unload事件
window.addEventListener('unload', this.unloadEvent)

// 在methods中添加处理的函数
unloadEvent() {
    // 处理其他逻辑
    navigator.sendBeacon(`yoururl`, {})
}

// 在destroyed钩子函数中，记得注销掉unload事件
window.removeEventListener('unload', this.unloadEvent)
```
