---
title: performance
date: 2021-02-20 15:02:29
description: 网页性能管理
tags:
---

参考[阮一峰的这篇文章](https://www.ruanyifeng.com/blog/2015/09/web-page-performance-in-depth.html)

# 重排与重绘
重新渲染，就需要重新生成布局和重新绘制。前者叫做"重排"（reflow），后者叫做"重绘"（repaint）。

---
# 对性能影响
一般来说，对下列属性的读操作会导致重新渲染
```
offsetTop/offsetLeft/offsetWidth/offsetHeight
scrollTop/scrollLeft/scrollWidth/scrollHeight
clientTop/clientLeft/clientWidth/clientHeight
getComputedStyle()
```

```js
// bad
div.style.left = div.offsetLeft + 10 + "px";
div.style.top = div.offsetTop + 10 + "px";

// good
var left = div.offsetLeft;
var top  = div.offsetTop;
div.style.left = left + 10 + "px";
div.style.top = top + 10 + "px";
```

一般规则：
* 样式表越简单，重排和重绘就越快。
* 重排和重绘的DOM元素层级越高，成本就越高。
* table元素的重排和重绘成本，要高于div元素

---
# 提高性能的九个技巧
有一些技巧，可以降低浏览器重新渲染的频率和成本。

第一条是上一节说到的，DOM 的多个读操作（或多个写操作），应该放在一起。不要两个读操作之间，加入一个写操作。

第二条，如果某个样式是通过重排得到的，那么最好缓存结果。避免下一次用到的时候，浏览器又要重排。

第三条，不要一条条地改变样式，而要通过改变class，或者csstext属性，一次性地改变样式。

```
// bad
var left = 10;
var top = 10;
el.style.left = left + "px";
el.style.top  = top  + "px";

// good 
el.className += " theclassname";

// good
el.style.cssText += "; left: " + left + "px; top: " + top + "px;";
```

第四条，尽量使用离线DOM，而不是真实的网面DOM，来改变元素样式。比如，操作Document Fragment对象，完成后再把这个对象加入DOM。再比如，使用 cloneNode() 方法，在克隆的节点上进行操作，然后再用克隆的节点替换原始节点。

第五条，先将元素设为display: none（需要1次重排和重绘），然后对这个节点进行100次操作，最后再恢复显示（需要1次重排和重绘）。这样一来，你就用两次重新渲染，取代了可能高达100次的重新渲染。

第六条，position属性为absolute或fixed的元素，重排的开销会比较小，因为不用考虑它对其他元素的影响。

第七条，只在必要的时候，才将元素的display属性为可见，因为不可见的元素不影响重排和重绘。另外，visibility : hidden的元素只对重绘有影响，不影响重排。

第八条，使用虚拟DOM的脚本库，比如React等。

第九条，使用 window.requestAnimationFrame()、window.requestIdleCallback() 这两个方法调节重新渲染（详见后文）。