---
title: css-pseudo-class
date: 2020-11-20 16:28:02
description: css伪类选择器的坑 
tags:
---
## `:first-child / :last-child`
>CSS3手册中描述：
>* :first-child：选择属于其父元素的首个子元素的每个子元素
>* :last-child：选择属于其父元素的最后一个子元素的每个子元素
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <style>
    .p:first-child {
      border: 1px solid red;
    }
    .p:last-child {
      border: 1px solid blue;
    }
  </style>
</head>
<body>
  <div>
    <p class="p">这是第一个段落。</p>
    <p class="p">这是第二个段落。</p>
    <p class="p">这是第三个段落。</p>
  </div>
  <div>
    <p>这是一个干扰段落。</p>
    <p class="p">这是第一个段落。</p>
    <p class="p">这是第二个段落。</p>
    <!-- 这里无法选中 -->
    <p class="p">这是第三个段落。</p>
    <p>这是一个干扰段落。</p>
  </div>
</body>
</html>
```
## `:first-of-type / :last-of-type`
>CSS3手册中描述：
>:first-of-type：选择属于其父元素的首个指定类型子元素的每个子元素
>:last-of-type：选择属于其父元素的最后一个指定类型子元素的每个子元素
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <style>
    .p:first-of-type {
      border: 1px solid red;
    }
    .p:last-of-type {
      border: 1px solid blue;
    }
  </style>
</head>
<body>
  <div>
    <!-- 这里不受影响 -->
    <div>这是一个干扰段落。</div>
    <p class="p">这是第一个段落。</p>
    <p class="p">这是第二个段落。</p>
    <p class="p">这是第三个段落。</p>
      <!-- 这里不受影响 -->
    <div>这是一个干扰段落。</div>
  </div>
  <div>
    <p>这是一个干扰段落。</p>
    <p class="p">这是第一个段落。</p>
    <p class="p">这是第二个段落。</p>
      <!-- 这里受影响 -->
    <p class="p">这是第三个段落。</p>
    <p>这是一个干扰段落。</p>
  </div>
</body>
</html>
```
## `:nth-child(n) / :nth-last-child(n)`
>CSS3手册中描述：
>:nth-child(n)：选择属于其父元素的第n个子元素，不论元素的类型
>:nth-last-child(n)：选择属于其父元素的倒数第n个子元素，不论元素的类型
```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <style>
    .p:nth-child(2) {
      border: 1px solid red;
    }
    .p:nth-last-child(2) {
      border: 1px solid blue;
    }
  </style>
</head>
<body>
  <div>
    <p>这是一个干扰段落。</p>
    <!-- 这里不受影响 -->
    <p class="p">这是第一个段落。</p>
    <p class="p">这是第二个段落。</p>
    <!-- 这里不受影响 -->
    <p class="p">这是第三个段落。</p>
    <p>这是一个干扰段落。</p>
  </div>
</body>
</html>
```