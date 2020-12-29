---
title: webpack热更新原理
date: 2020-12-29 11:23:40
description: webpack开发服务器热更新初解 HMR
tags: webpack
---

热更新需要借助`webpack-dev-server`并且配合`HotModuleReplacementPlugin`一起使用，相比于 webpack 自带的 watch 它的优势是将输出的问题放在内存中，所以没有磁盘 IO ，在构建速度上会有更大优势

## 构成
`Webpack Compile`：将 JS 源代码编译成 bundle.js
`HMR Server`：用来将热更新的文件输出给 HMR Runtime
`Bundle Server`：提供文件在浏览器的访问，以服务的方式访问
`HMR Runtime`：会被注入到浏览器，更新文件的变化
`bundle.js`：构建输出的文件

`HMR Runtime`和`HMR Server`会建立起一条连接，通常是 websocket，就可以实时更新文件变化
![](/images/HMR-1.png)

##  过程
* 启动阶段
首先我们在文件系统便写完代码之后，`Webpack Compile`将源代码和`HMR Runtime`一起编译成`bundle`文件，传输给`Bundle Server`，`Bundle Server`是一个服务器，这样在浏览器里就可以以服务的方式访问文件。
* 更新阶段
当我们在文件系统更新文件之后，还是会经过`Webpack Compile`的编译，`Webpack Compile`会将编译后的结果传递给`HMR Server`，`HMR Server`会比较哪些文件发生了变化，因为服务端的`HMR Server`会和客户端的`HMR Runtime`建立起一条`websocket`链接，所以`HMR Server`会以 json 的形式通知给`HMR Runtime`文件做出了哪些变化。

## 实际更新过程
1. 当某一个文件或者模块发生变化时，webpack 监听到文件变化对文件重新编译打包，编译会生成一个 hash 值用作下次热更新标识，同时还会生成一些文件， 其中一个为`[hash].hot-update.json`包含了需要变化的内容，其他为`chunk.js`
2. 由于服务端的`HMR Server`和客户端的`HMR Runtime`已经建立起一条 websocket 连接，当有文件更新时会将生成的 hash 推送给浏览器，**浏览器会记录下这个 hash 并用上一次收到的 hash 来请求包含更新内容的 json 文件**。
3. 获得 json 后浏览器会根据获得的信息来请求获取发生变化的`chuk.js`，并发起请求来请求重新构建的 js ,并且通过 eval 去执行 render 从而达到热替换的效果。

本文只是很基础的讲解了 HMR 的原理，想深入了解还需要知道`Webpack Complile`如何工作，`Bundle Server`何种方式提供访问，`bundle.js`如何构建，以及如何触发 render 过程。