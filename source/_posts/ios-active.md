---
title: ios 伪类失效
date: 2021-06-08 18:02:05
description:
tags:
---

偶然遇到了ios中button `:active`失效的问题，经查需要绑定触摸事件
```js
document.addEventListener("touchstart", function() {}, false);
```
而这种问题出现的原因是由于在iOS上，鼠标事件的发送速度非常快，从而不会收到向下或活动状态。因此，:active只有在HTML元素上设置了触摸事件时，才会触发伪状态 .[参考链接](https://developer.apple.com/library/archive/documentation/AppleApplications/Reference/SafariWebContent/AdjustingtheTextSize/AdjustingtheTextSize.html)

[参考](https://stackoverflow.com/questions/3885018/active-pseudo-class-doesnt-work-in-mobile-safari)