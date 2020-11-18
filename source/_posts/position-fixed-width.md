---
title: position fixed的宽度问题
date: 2020-11-18 16:25:25
description: 偶然遇到的css当postion为fixed的问题
tags: bug
---
发现一个吸底按钮不居中的bug，其父元素有padding，而底部footer左边距正常，右边距超出

```css
.parent {
    padding: 10px;
}
.footer {
    position: fixed;
    bottom: 0;
    width: 100%;

    .button {
        margin: 5px 20px 15px;
    }
}
```
实际上footer元素设为`position: fixed`后，设置`width: 100%`其宽度为视口宽度，并没有像预想中为parent的宽度，所以导致视觉上没有居中，参考了[stackoverflow 上的一个问题](https://stackoverflow.com/questions/22159588/position-fixed-with-width-100-is-ignoring-body-padding/22159685)后把`width: 100%`改为`width: calc(100% - 20px);`即可。
个人不建议使用这种实现方式，应该在footer内部添加`padding`最好，而不在父元素全局添加`padding`，而且考虑到margin塌陷等问题，也不建议使用margin来限制元素的大小，应该使用padding来实现。
