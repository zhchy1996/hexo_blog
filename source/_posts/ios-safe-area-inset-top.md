---
title: ios safe-area-inset-top存在延迟
date: 2021-06-08 18:14:13
description:
tags:
---

当在ios上使用安全距离的css变量时，如果立即获取元素高度会出现获取不准的情况，这是由于`safe-area-inset-top`生效有300ms的延迟，所以可以在300ms后获取。

[参考](https://www.gitmemory.com/issue/ionic-team/capacitor/2920/628468829)
