---
title: vue3
date: 2021-02-19 16:56:00
description: Vue3文档阅读笔记，主要记录新特性应用以及与2.x的差异
tags:
---

# 新特性
## 组合式API
组合式API可以提供更好的复用性，与`mixin`相比其带来的影响更小，mixin 的问题可以参考[ react 文档中总结的 mixin 危害](https://zh-hans.reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)，其与 vue 中的 mixin 危害是大致相同的。

### 组合式 API 基础
与新的组合式 API 相对应，vue的文档中称过去的这种模式为选项式 API
```js
// src/components/UserRepositories.vue

export default {
  components: { RepositoriesFilters, RepositoriesSortBy, RepositoriesList },
  props: {
    user: { type: String }
  },
  data () {
    return {
      repositories: [], // 1
      filters: { ... }, // 3
      searchQuery: '' // 2
    }
  },
  computed: {
    filteredRepositories () { ... }, // 3
    repositoriesMatchingSearchQuery () { ... }, // 2
  },
  watch: {
    user: 'getUserRepositories' // 1
  },
  methods: {
    getUserRepositories () {
      // 使用 `this.user` 获取用户仓库
    }, // 1
    updateFilters () { ... }, // 3
  },
  mounted () {
    this.getUserRepositories() // 1
  }
}
```
而组合式 API 主要依靠`setup`组件选项实现  
新的 setup 组件选项在创建组件之前执行，一旦 props 被解析，并充当合成 API 的入口点, **注意，setup 中无法访问到 this，也就是说我们只能访问到到组件实例的 props**。

在 setup 中我们可以使用以下api：  
* `ref` 只能用于原始值类似 data 用于创建响应式的变量，需要注意的是其中 `value` 才是变量真实的值，其将原始值也变为引用值方便实现响应式
* `reactive` 用于引用值，返回对象的响应式副本，基于 proxy 实现，返回的代理**不**等于原始对象
* `watch` 同选项式 API 的 watch
* `computed` 同选项式 API 的 computed
* 生命周期钩子需要加 `on` ，例如 `mounted` -> `onMounted`

--- 
### Setup
接受两个参数
* props 组件的props，是响应式的，不能使用解构赋值
* contenxt 提供三个组件的 property : `attrs`, `slots`, `emit` 可以使用解构赋值
attrs 和 slots 是有状态的对象，它们总是会随组件本身的更新而更新。这意味着你应该避免对它们进行解构，并始终以 attrs.x 或 slots.x 的方式引用 property。请注意，与 props 不同，attrs 和 slots 是非响应式的。如果你打算根据 attrs 或 slots 更改应用副作用，那么应该在 onUpdated 生命周期钩子中执行此操作。

在非模板中访问 Setup 中返回的经过 ref 处理的变量需要使用 `*.value`，在模板中直接访问即可  

`setup`还可以返回一个渲染函数，可以使用同一作用域中声明的响应式状态
```js
// MyBook.vue

import { h, ref, reactive } from 'vue'

export default {
  setup() {
    const readersNumber = ref(0)
    const book = reactive({ title: 'Vue 3 Guide' })
    // Please note that we need to explicitly expose ref value here
    return () => h('div', [readersNumber.value, book.title])
  }
}
```

---
### 生命周期钩子
选项式 API | setup
-|-
`beforeMount`|`onBeforeMount`
`mounted`|`onMounted`
`beforeUpdate`|`onBeforeUpdate`
`updated`|`onUpdated`
`beforeUnmount`|`onBeforeUnmount`
`unmounted`|`onUnmounted`
`errorCaptured`|`onErrorCaptured`
`renderTracked`|`onRenderTracked`
`renderTriggered`|`onRenderTriggered`

由于 setup 是围绕 beforeCreate 和 created 运行的，所以不用再 setup 中定义它们。

```js
export default {
  setup() {
    // mounted
    onMounted(() => {
      console.log('Component is mounted!')
    })
  }
}
```

---
### 提供 / 注入


## 提供 / 注入
```js
const app = Vue.createApp({})

app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide: {
    user: 'John Doe'
  },
  template: `
    <div>
      {{ todos.length }}
      <!-- 模板的其余部分 -->
    </div>
  `
})

app.component('todo-list-statistics', {
  inject: ['user'],
  created() {
    console.log(`Injected property: ${this.user}`) // > 注入 property: John Doe
  }
})
```

```js
// 这样写不生效
app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide: {
    todoLength: this.todos.length // 将会导致错误 'Cannot read property 'length' of undefined`
  },
  template: `
    ...
  `
})
// 这样写才可以
app.component('todo-list', {
  data() {
    return {
      todos: ['Feed a cat', 'Buy tickets']
    }
  },
  provide() {
    return {
      todoLength: this.todos.length
    }
  },
  template: `
    ...
  `
})
```

这种形式的注入传递的变量和对象是非响应式的，如果需要可以传递 ref 和 reactive 变量

```js
app.component('todo-list', {
  // ...
  provide() {
    return {
      todoLength: Vue.computed(() => this.todos.length)
    }
  }
})
```



