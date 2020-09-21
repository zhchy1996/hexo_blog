---
title: Node REPL
description: Node.js权威指南第二章笔记
categories:
 - Node.js
tags:
 - Node.js
date: 2018-01-11
---
### REPL运行环境概述
* REPL是一个交互式运行环境
* 可以使用`_` 来访问最近的表达式，使用的是上个表达式返回的值
```js
a = 3
>3
_+1
>4
a
>3
// 可以看到我们使用_操作的并不是a，而是其返回的3
```
- - - -
### REPL运行环境中的上下文对象
* 在模块文件中可以使用`start`方法开启一个REPL运行环境，可以传入参数，也为其制定一个上下文对象
```js
var repl = require("repl");
var con = repl.start().context;
con.msg =" 示例消息";
con.testFunction= function(){ console.log( con.msg);};
// 在打开的repl中使用testFunction方法可以打印‘实例消息’
```
- - - -
### REPL运行环境中的基础命令
* `.break`放弃多行函数的书写，返回命令提示符起点处，可用ctrl+c替代
* `.clear`清除REPL运行环境上下文对象中保存的所有变量和函数
* `.exit`退出REPL运行环境，可用ctrl+d替代
* `.help`显示所有基础命令
* `.save`将在REPL环境输入的表达式存到一个文件中，可以指定路径
* `.load`把某个文件保存的所有表达式一次加载到REPL运行环境中，可以指定路径











#node