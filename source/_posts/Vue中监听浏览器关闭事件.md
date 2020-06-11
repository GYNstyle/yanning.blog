---
title: 监听浏览器关闭事件，并发送异步请求
date: 2020-06-11 15:42:31
tags:
---
## 项目需求

监听用户关闭浏览器事件，若关闭则发送异步请求给后端。

## 问题

浏览器关闭事件并不会触发vue实例的beforeDestroy和destroyed函数，因而需要使用window.unload，参见[MDN](https://developer.mozilla.org/zh-CN/docs/Web/Events/unload)。但这两个事件并不支持异步请求。

## 解决方式

使用navigator.sendBeacon(url, data)，参见[MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/Navigator/sendBeacon)。其中，URL无需特殊处理，与你项目中发送异步请求的URL相同即可。

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
