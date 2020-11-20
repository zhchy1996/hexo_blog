---
title: keyboard-pop
date: 2020-11-20 16:04:33
description: H5键盘弹出
tags:
---

### 键盘弹出
* IOS：IOS系统 的键盘处在窗口的最上层，当键盘弹起时，webview 的高度 height 并没有改变，只是 scrollTop 发生变化，页面可以滚动。且页面可以滚动的最大限度为弹出的键盘的高度，而只有键盘弹出时页面恰好也滚动到最底部时，scrollTop 的变化值为键盘的高度，其他情况下则无法获取。这就导致在 IOS 情况下难以获取键盘的真实高度。
* Android: 在Android系统中，键盘也是处在窗口的最上层，键盘弹起时，如果输入框在靠近底部的话，就会被键盘挡住，只有你输入的时候输入框才会滚动到可视化区域。

### 键盘收起
* IOS：触发键盘上的按钮收起键盘或者输入框以外的页面区域时，输入框会失去焦点，因此会触发输入框的 blur 事件；当键盘收起时，页面底部会出现一个空白区域，页面会被顶起。
* Android: 触发键盘上的按钮收起键盘时，输入框并不会失去焦点，因此不会触发页面的 blur 事件；触发输入框以外的区域时，输入框会失去焦点，触发输入框的 blur 事件。

### 键盘状况判断
* 键盘弹出：输入框获取焦点时会自动触发键盘的弹起动作，因此，我们可以监听 focusin 事件，在里面实现键盘弹出后所需的页面逻辑。
* 键盘收起：当触发其他页面区域收起键盘时，我们可以监听 focusout 事件，在里面实现键盘收起后所需的页面逻辑。而在通过键盘按钮收起键盘时在 ios 与 android 端存在差异化表现，下面具体分析：
    * IOS：触发了 focusout 事件，仍然通过该办法监听。
    * Android：没有触发 focusout 事件。在 android 中，键盘的状态切换（弹出、收起）不仅和输入框关联，同时还会影响到 webview 高度的变化，那我们就可以通过监听 webview height 的变化来判断键盘是否收起。

### 系统判断
```js
const ua = window.navigator.userAgent.toLocaleLowerCase();
const isIOS = /iphone|ipad|ipod/.test(ua);
const isAndroid = /android/.test(ua);
```

### IOS
```js
let isReset = true; //是否归位

this.focusinHandler = () => {
  isReset = false; //聚焦时键盘弹出，焦点在输入框之间切换时，会先触发上一个输入框的失焦事件，再触发下一个输入框的聚焦事件
};

this.focusoutHandler = () => {
  isReset = true;
  setTimeout(() => {
    //当焦点在弹出层的输入框之间切换时先不归位
    if (isReset) {
        window.scroll(0, 0); //确定延时后没有聚焦下一元素，是由收起键盘引起的失焦，则强制让页面归位
    }
  }, 30);
};

document.body.addEventListener('focusin', this.focusinHandler);
document.body.addEventListener('focusout', this.focusoutHandler);
```

### Android
```js
const originHeight = document.documentElement.clientHeight || document.body.clientHeight;

this.resizeHandler = () => {
  const resizeHeight = document.documentElement.clientHeight || document.body.clientHeight;
  const activeElement = document.activeElement;
  if (resizeHeight < originHeight) {
    // 键盘弹起后逻辑
    if (activeElement && (activeElement.tagName === "INPUT" || activeElement.tagName === "TEXTAREA")) {
      setTimeout(()=>{
          // 使元素可见的方法
        activeElement.scrollIntoView({ block: 'center' });//焦点元素滚到可视区域的问题
      },0)
    }
  } else {
    // 键盘收起后逻辑
  }
};

window.addEventListener('resize', this.resizeHandler);
```


