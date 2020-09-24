---
title: Vue 源码
description: Vue源码笔记
categories:
  - 框架
tags:
  - Vue
date: 2020-09-21
---

# 源码目录结构
<pre>
├── benchmarks
├── examples
├── flow  vue使用flow进行类型检查
├── packages
├── scripts  启动构建相关脚本
├── src  主要源码
    ├── compiler
    ├── core  核心代码
    ├── platforms  多平台兼容代码
    ├── server
    ├── sfc
    └── shared
├── test
└── types
</pre>

# core
<pre>
├── components
├── config.js
├── global-api
├── index.js
├── instance  vue实例的核心代码
├── observer
├── util
└── vdom
</pre>

## `index.js`
这个文件主要用于初始化
### `initGlobalAPI(Vue)`

#### `Vue`
import Vue from [./instance/index](#instance-index-js)
从`instance`引入了`Vue`类
先看一下`instance`中都做了什么

## `instance`
<pre>
├── events.js
├── index.js
├── init.js
├── inject.js
├── lifecycle.js
├── proxy.js
├── render-helpers
├── render.js
└── state.js
</pre>

### `instance/index.js`
```js
import { initMixin } from './init'
import { stateMixin } from './state'
import { renderMixin } from './render'
import { eventsMixin } from './events'
import { lifecycleMixin } from './lifecycle'
import { warn } from '../util/index'

// 定义了Vue的实例
function Vue (options) {
    // 如果不是生产环境并且直接调用Vue会报warn
  if (process.env.NODE_ENV !== 'production' &&
    // 这种写法可以判断构造函数是否被作为构造函数使用
    !(this instanceof Vue)
  ) {
    warn('Vue is a constructor and should be called with the `new` keyword')
  }
  // 这个方法是由initMixin挂载到Vue.prototype上的
  this._init(options)
}

initMixin(Vue)
stateMixin(Vue)
eventsMixin(Vue)
lifecycleMixin(Vue)
renderMixin(Vue)

export default Vue
```
声明`Vue`构造函数的代码很简单，其实 Vue 的功能都是由下面的各种 Mixin 实现的，首先来看一下 [`initMixin`](#init-js-initMixin)做了什么

### init.js/initMixin
```js
let uid = 0
export function initMixin (Vue: Class<Component>) {
  Vue.prototype._init = function (options?: Object) {
    const vm: Component = this
    // a uid
    vm._uid = uid++

    let startTag, endTag
    // config.performance 默认是false,用于标记是否记录性能情况
    if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
      startTag = `vue-perf-start:${vm._uid}`
      endTag = `vue-perf-end:${vm._uid}`
      // mark为引用的工具方法用于判断是否为生产环境并且在浏览器中调用window.performance.mark用于记录性能信息
      mark(startTag)
    }

    // a flag to avoid this being observed
    vm._isVue = true
    // merge options
    if (options && options._isComponent) {
      // optimize internal component instantiation
      // 优化内部组件实例化
      // since dynamic options merging is pretty slow, and none of the
      // internal component options needs special treatment.
      // 这个方法见下方,貌似是处理内部组件的，之后遇到再看
      initInternalComponent(vm, options)
    } else {
        // 合并配置，并赋值给实例的$options
      vm.$options = mergeOptions(
          // 不知道这种情况处理了那些配置
        resolveConstructorOptions(vm.constructor),
        options || {},
        vm
      )
    }
    /* istanbul ignore else */
    if (process.env.NODE_ENV !== 'production') {
      initProxy(vm)
    } else {
      vm._renderProxy = vm
    }
    // expose real self
    vm._self = vm
    // 初始化了一系列状态
    initLifecycle(vm)
    initEvents(vm)
    initRender(vm)
    callHook(vm, 'beforeCreate')
    initInjections(vm) // resolve injections before data/props
    initState(vm)
    initProvide(vm) // resolve provide after data/props
    callHook(vm, 'created')

    /* istanbul ignore if */
    if (process.env.NODE_ENV !== 'production' && config.performance && mark) {
      vm._name = formatComponentName(vm, false)
      mark(endTag)
      measure(`vue ${vm._name} init`, startTag, endTag)
    }

    if (vm.$options.el) {
      vm.$mount(vm.$options.el)
    }
  }
}
```

#### proxy/initProxy方法
```js
initProxy = function initProxy (vm) {
    // 这里判断Proxy是否可用
    if (hasProxy) {
      // determine which proxy handler to use
      const options = vm.$options
      // 暂时不清楚这个render标记有什么用
      const handlers = options.render && options.render._withStripped
        ? getHandler
        : hasHandler
        // 就是由于这个proxy当我们访问不存在的data时会有一个vue warn的报错，类似这种
        // [Vue warn]: Property or method "b" is not defined on the instance but referenced during render. Make sure that this property is reactive, either in the data option, or for class-based components, by initializing the property. See: https://vuejs.org/v2/guide/reactivity.html#Declaring-Reactive-Properties.
      vm._renderProxy = new Proxy(vm, handlers)
    } else {
      vm._renderProxy = vm
    }
  }
```

#### lifecycle/initLifecycle方法
```js
export function initLifecycle (vm: Component) {
  const options = vm.$options

  // locate first non-abstract parent
  let parent = options.parent
  if (parent && !options.abstract) {
    while (parent.$options.abstract && parent.$parent) {
      parent = parent.$parent
    }
    // 像第一个parent实例的$children中添加此实例
    parent.$children.push(vm)
  }

  // 添加父实例
  vm.$parent = parent
  // 判断是否为根节点
  vm.$root = parent ? parent.$root : vm

  // 初始化一系列状态
  vm.$children = []
  vm.$refs = {}

  vm._watcher = null
  vm._inactive = null
  vm._directInactive = false
  vm._isMounted = false
  vm._isDestroyed = false
  vm._isBeingDestroyed = false
}
```

#### event/initEvents
```js
export function initEvents (vm: Component) {
    // 创建了记录event的对象
  vm._events = Object.create(null)
  vm._hasHookEvent = false
  // init parent attached events
  // 获取父组件的事件监听？
  const listeners = vm.$options._parentListeners
  if (listeners) {
      // 如果父组件有_parentListeners则吧他们也挂载到子组件
    updateComponentListeners(vm, listeners)
  }
}
```

# 小技巧
* arr.slice()
可以实现数组的浅克隆
* 







