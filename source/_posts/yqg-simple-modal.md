---
title: yqg simple modal
description: yqg simple modal介绍
categories:
  - 工具
tags:
  - 工具
date: 2020-10-23 19:16:00
---
### 注册方法
`created`时在Vue的原型上添加了`$modal`，并且添加了`open`和`clearModal`方法，同时还在`created`中添加了`beforeEach`路由钩子用于清除弹窗

由于一个app中只能存在一个modal组件（组件会渲染多个弹窗）所以通过count字段去控制，在`beforeCreate`时判断count数值，并+1`beforeDestroy`时-1
### 方法实现
#### open
* 使用promise，便于外部调用处理确认和取消逻辑
* 提供了close和dismiss方法，并通过v-bind传入，这也是使用modal的组件都需要引用 `modal`mixin的原因，并且提供的close和dismiss方法都调用了 `closeModal`方法，并且提供了更为彻底的 `afterClose`方法，不过它并没有在 `modal`的mixin中
* 提供了对背景水印的支持处理
* 向modalMap中添加了一个对象用于渲染modal，内部自己创建了key并且包含传入的组件和props，传入的options会作为 `dialogProps`
#### closeModal
将对应key的弹窗的visible设为false
#### clearModal
将对应key的弹窗数据从 `modalMap`中移除，会销毁掉dom，并且会在弹窗
#### clearModals
清除掉所有弹窗，这个放在在Vue原型上暴露，并在路由 `beforeEach`调用

### template
#### antd组件
默认附带参数有
* vsible 控制是否展示
* afterClose 完全关闭后的回调，这里传入的是 `clearModal`方法
* destoryOnClose 关闭时销毁 Modal 里的子元素
* footer 当组件中有onlyClose时会在footer中展示一个关闭按钮，否则使用组件提供的按钮（通常是yqg-simple-form）提供
* width 宽度
* style overflowX: hidden;overflowY: auto;
* bodyStyle 有添加水印相关逻辑
* getContainer 指定 Modal 挂载的 HTML 节点，这里提供的是 `this.$el`就是调用的组件 HTML 节点
* 后续提供了自己添加参数的入口，可以是调用 `open`时的 `props`，也可以在传入 `modal`组件中的 `dialogPorps` 中指定
* cancel 传入了`dismiss`用于关闭弹窗

