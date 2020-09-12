title: JavaScript设计模式
description: 常用JavaScript设计模式
categories:
  - JavaScript
tags:
  - JavaScript
date: 2020-09-10 19:32:00
---
# 设计模式
## 道
### SOLID设计原则
* **单一功能原则（Single Responsibility Principle**
* **开放封闭原则（Opened Closed Principle**
* 里式替换原则（Liskov Substitution Principle）
* 接口隔离原则（Interface Segregation Principle）
* 依赖反转原则（Dependency Inversion Principle）

> 开放封闭原则：对拓展开放，对修改封闭。说得更准确点，软件实体（类、模块、函数）可以扩展，但是不可修改

### 设计模式核心思想-封装变化
将变与不变分离，确保变化的部分灵活、不变的部分稳定。

---
## 术
![](https://user-gold-cdn.xitu.io/2019/4/6/169f16406d230ffe?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

---
# 简单工厂模式
## 构造器模式
```js
function User(name , age, career) {
    this.name = name
    this.age = age
    this.career = career 
}

const user = new User(name, age, career)
```
构造器模式可以很好的区分共性与个性即**变与不变**


## 简单工厂模式
```js
function User(name , age, career, work) {
    this.name = name
    this.age = age
    this.career = career 
    this.work = work
}

function Factory(name, age, career) {
    let work
    switch(career) {
        case 'coder':
            work =  ['写代码','写系分', '修Bug'] 
            break
        case 'product manager':
            work = ['订会议室', '写PRD', '催更']
            break
        case 'boss':
            work = ['喝茶', '看报', '见客户']
        case 'xxx':
            // 其它工种的职责分配
            ...
            
    return new User(name, age, career, work)
}
```
工厂模式其实就是**将创建对象的过程单独封装**,其目的就是为了实现**无脑传参**


## 小结
将创建对象的过程单独封装，这样的操作就是工厂模式  
有构造函数的地方，我们就应该想到简单工厂  
构造器解决的是多个对象实例的问题，简单工厂解决的是多个类的问题

---
# 抽象工厂模式
创建抽象工厂
```js
class MobilePhoneFactory {
    // 提供操作系统的接口
    createOS(){
        throw new Error("抽象工厂方法不允许直接调用，你需要将我重写！");
    }
    // 提供硬件的接口
    createHardWare(){
        throw new Error("抽象工厂方法不允许直接调用，你需要将我重写！");
    }
}
```
抽象工厂不创建实例而是给具体工厂继承  

具体工厂
```js
// 具体工厂继承自抽象工厂
class FakeStarFactory extends MobilePhoneFactory {
    createOS() {
        // 提供安卓系统实例
        return new AndroidOS()
    }
    createHardWare() {
        // 提供高通硬件实例
        return new QualcommHardWare()
    }
}
```

抽象产品
```js
// 定义操作系统这类产品的抽象产品类
class OS {
    controlHardWare() {
        throw new Error('抽象产品方法不允许直接调用，你需要将我重写！');
    }
}

// 定义具体操作系统的具体产品类
class AndroidOS extends OS {
    controlHardWare() {
        console.log('我会用安卓的方式去操作硬件')
    }
}

class AppleOS extends OS {
    controlHardWare() {
        console.log('我会用🍎的方式去操作硬件')
    }
}


// 定义手机硬件这类产品的抽象产品类
class HardWare {
    // 手机硬件的共性方法，这里提取了“根据命令运转”这个共性
    operateByOrder() {
        throw new Error('抽象产品方法不允许直接调用，你需要将我重写！');
    }
}

// 定义具体硬件的具体产品类
class QualcommHardWare extends HardWare {
    operateByOrder() {
        console.log('我会用高通的方式去运转')
    }
}

class MiWare extends HardWare {
    operateByOrder() {
        console.log('我会用小米的方式去运转')
    }
}
```

当我们使用的时候只需要这样：
```js
// 这是我的手机
const myPhone = new FakeStarFactory()
// 让它拥有操作系统
const myOS = myPhone.createOS()
// 让它拥有硬件
const myHardWare = myPhone.createHardWare()
// 启动操作系统(输出‘我会用安卓的方式去操作硬件’)
myOS.controlHardWare()
// 唤醒硬件(输出‘我会用高通的方式去运转’)
myHardWare.operateByOrder()
```

如果不需要`FakeStarFactory`，而是创建其他工厂就可以**不对抽象工厂`MobilePhoneFactory`做任何修改，只需要拓展它的种类：
```js
class newStarFactory extends MobilePhoneFactory {
    createOS() {
        // 操作系统实现代码
    }
    createHardWare() {
        // 硬件实现代码
    }
}
```
这样就符合**对原有的系统不会造成任何潜在影响**的“对拓展开放，对修改封闭”的原则

## 总结
* 抽象工厂（抽象类，它不能被用于生成具体实例）： 用于声明最终目标产品的共性。在一个系统里，抽象工厂可以有多个（大家可以想象我们的手机厂后来被一个更大的厂收购了，这个厂里除了手机抽象类，还有平板、游戏机抽象类等等），每一个抽象工厂对应的这一类的产品，被称为“产品族”。
* 具体工厂（用于生成产品族里的一个具体的产品）： 继承自抽象工厂、实现了抽象工厂里声明的那些方法，用于创建具体的产品的类。
* 抽象产品（抽象类，它不能被用于生成具体实例）： 上面我们看到，具体工厂里实现的接口，会依赖一些类，这些类对应到各种各样的具体的细粒度产品（比如操作系统、硬件等），这些具体产品类的共性各自抽离，便对应到了各自的抽象产品类。
* 具体产品（用于生成产品族里的一个具体的产品所依赖的更细粒度的产品）： 比如我们上文中具体的一种操作系统、或具体的一种硬件等。

---
# 单例模式
保证一个类仅有一个实例，并提供一个访问它的全局访问点
## 单例模式的实现
```js
class SingleDog {
    show() {
        console.log('我是一个单例对象')
    }
    static getInstance() {
        // 判断是否已经new过1个实例
        if (!SingleDog.instance) {
            // 若这个唯一的实例不存在，那么先创建它
            SingleDog.instance = new SingleDog()
        }
        // 如果这个唯一的实例已经存在，则直接返回
        return SingleDog.instance
    }
}

const s1 = SingleDog.getInstance()
const s2 = SingleDog.getInstance()

// true
s1 === s2
```
还可以用闭包实现
```js
SingleDog.getInstance = (function() {
    // 定义自由变量instance，模拟私有变量
    let instance = null
    return function() {
        // 判断自由变量是否为null
        if(!instance) {
            // 如果为null则new出唯一实例
            instance = new SingleDog()
        }
        return instance
    }
})()
```

## Vuex中的单例模式
```js
let Vue // 这个Vue的作用和楼上的instance作用一样
...

export function install (_Vue) {
  // 判断传入的Vue实例对象是否已经被install过Vuex插件（是否有了唯一的state）
  if (Vue && _Vue === Vue) {
    if (process.env.NODE_ENV !== 'production') {
      console.error(
        '[vuex] already installed. Vue.use(Vuex) should be called only once.'
      )
    }
    return
  }
  // 若没有，则为这个Vue实例对象install一个唯一的Vuex
  Vue = _Vue
  // 将Vuex的初始化逻辑写进Vue的钩子函数里
  applyMixin(Vue)
}
```

## 实现一个 Storage
实现Storage，使得该对象为单例，基于 localStorage 进行封装。实现方法 setItem(key,value) 和 getItem(key)。  

静态方法
```js
// 定义Storage
class Storage {
    static getInstance() {
        // 判断是否已经new过1个实例
        if (!Storage.instance) {
            // 若这个唯一的实例不存在，那么先创建它
            Storage.instance = new Storage()
        }
        // 如果这个唯一的实例已经存在，则直接返回
        return Storage.instance
    }
    getItem (key) {
        return localStorage.getItem(key)
    }
    setItem (key, value) {
        return localStorage.setItem(key, value)
    }
}

const storage1 = Storage.getInstance()
const storage2 = Storage.getInstance()

storage1.setItem('name', '李雷')
// 李雷
storage1.getItem('name')
// 也是李雷
storage2.getItem('name')

// 返回true
storage1 === storage2
```

闭包版
```js
// 先实现一个基础的StorageBase类，把getItem和setItem方法放在它的原型链上
function StorageBase () {}
StorageBase.prototype.getItem = function (key){
    return localStorage.getItem(key)
}
StorageBase.prototype.setItem = function (key, value) {
    return localStorage.setItem(key, value)
}

// 以闭包的形式创建一个引用自由变量的构造函数
const Storage = (function(){
    let instance = null
    return function(){
        // 判断自由变量是否为null
        if(!instance) {
            // 如果为null则new出唯一实例
            instance = new StorageBase()
        }
        return instance
    }
})()

// 这里其实不用 new Storage 的形式调用，直接 Storage() 也会有一样的效果 
const storage1 = new Storage()
const storage2 = new Storage()

storage1.setItem('name', '李雷')
// 李雷
storage1.getItem('name')
// 也是李雷
storage2.getItem('name')

// 返回true
storage1 === storage2
```

## 实现一个全局的模拟框
实现一个全局唯一的Modal弹框
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>单例模式弹框</title>
</head>
<style>
    #modal {
        height: 200px;
        width: 200px;
        line-height: 200px;
        position: fixed;
        left: 50%;
        top: 50%;
        transform: translate(-50%, -50%);
        border: 1px solid black;
        text-align: center;
    }
</style>
<body>
	<button id='open'>打开弹框</button>
	<button id='close'>关闭弹框</button>
</body>
<script>
    // 核心逻辑，这里采用了闭包思路来实现单例模式
    const Modal = (function() {
    	let modal = null
    	return function() {
            if(!modal) {
            	modal = document.createElement('div')
            	modal.innerHTML = '我是一个全局唯一的Modal'
            	modal.id = 'modal'
            	modal.style.display = 'none'
            	document.body.appendChild(modal)
            }
            return modal
    	}
    })()
    
    // 点击打开按钮展示模态框
    document.getElementById('open').addEventListener('click', function() {
        // 未点击则不创建modal实例，避免不必要的内存占用;此处不用 new Modal 的形式调用也可以，和 Storage 同理
    	const modal = new Modal()
    	modal.style.display = 'block'
    })
    
    // 点击关闭按钮隐藏模态框
    document.getElementById('close').addEventListener('click', function() {
    	const modal = new Modal()
    	if(modal) {
    	    modal.style.display = 'none'
    	}
    })
</script>
</html>
```

---
# 原型模式
原型模式不仅是一种设计模式，它还是一种编程范式（programming paradigm），是 JavaScript 面向对象系统实现的根基。

## 以类为中心的语言和以原型为中心的语言
### JAVA中的类
JAVA 中，类才是它面向对象系统的根本。所以说在 JAVA 中，我们可以选择不使用原型模式 —— 这样一来，所有的实例都必须要从类中来，当我们希望创建两个一模一样的实例时，就只能这样做（假设实例从 Dog 类中来,必传参数为姓名、性别、年龄和品种）：
```js
Dog dog = new Dog('旺财', 'male', 3, '柴犬')

Dog dog_copy = new Dog('旺财', 'male', 3, '柴犬')
```
没错，我们不得不把一模一样的参数传两遍，非常麻烦。而原型模式允许我们通过调用克隆方法的方式达到同样的目的，比较方便，所以 Java 专门针对原型模式设计了一套接口和方法，在必要的场景下会通过原型方法来应用原型模式。当然，在更多的情况下，Java 仍以“实例化类”这种方式来创建对象。

### JavaScript中的类
JS 的类基于prototype,ES6 的class为语法糖
> ECMAScript 2015 中引入的 JavaScript 类实质上是 JavaScript 现有的基于原型的继承的语法糖。类语法不会为 JavaScript 引入新的面向对象的继承模型。 ——MDN

---
## 原型范式
了解原型与原型链
![](https://user-gold-cdn.xitu.io/2019/3/11/1696bfe41aa0a184?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)
![](https://user-gold-cdn.xitu.io/2019/3/11/1696bfd959ce30b3?imageView2/0/w/1280/h/960/format/webp/ignore-error/1)

需要了解深拷贝
> [深入了解深拷贝](https://segmentfault.com/a/1190000016672263)

---
# 装饰器模式
在不改变原对象的基础上，通过对其进行包装拓展，使原有对象可以满足用户的更复杂需求  
## 给点击按钮弹窗添加逻辑
```js
// 定义打开按钮
class OpenButton {
    // 点击后展示弹框（旧逻辑）
    onClick() {
        const modal = new Modal()
    	modal.style.display = 'block'
    }
}

// 定义按钮对应的装饰器
class Decorator {
    // 将按钮实例传入
    constructor(open_button) {
        this.open_button = open_button
    }
    
    onClick() {
        this.open_button.onClick()
        // “包装”了一层新逻辑
        this.changeButtonStatus()
    }
    
    changeButtonStatus() {
        this.changeButtonText()
        this.disableButton()
    }
    
    disableButton() {
        const btn =  document.getElementById('open')
        btn.setAttribute("disabled", true)
    }
    
    changeButtonText() {
        const btn = document.getElementById('open')
        btn.innerText = '快去登录'
    }
}

const openButton = new OpenButton()
const decorator = new Decorator(openButton)

document.getElementById('open').addEventListener('click', function() {
    // openButton.onClick()
    // 此处可以分别尝试两个实例的onClick方法，验证装饰器是否生效
    decorator.onClick()
})
```
我们把实例传给了`Decorator`这样方便未来的拓展

---
## 单一职责原则
文本修改&按钮置灰这两个变化，被封装在了两个不同的方法里,这是一种单一职责的体现,在日常开发中要首先有**尝试拆分**的敏感，其次要有**该不该拆**的判断，如果逻辑颗粒度过小，盲目拆分会导致项目中有过多零碎的小方法。

---
## ES7中的装饰器
ES7 中我们可以通过@语法糖给类添加装饰器
```js
// 装饰器函数，它的第一个参数是目标类
function classDecorator(target) {
    target.hasDecorator = true
  	return target
}

// 将装饰器“安装”到Button类上
@classDecorator
class Button {
    // Button类的相关逻辑
}

// 验证装饰器是否生效
console.log('Button 是否被装饰了：', Button.hasDecorator)
```
也可是使用它来装饰类里面的方法
```js
// 具体的参数意义，在下个小节，这里大家先感知一下操作
function funcDecorator(target, name, descriptor) {
    let originalMethod = descriptor.value
    descriptor.value = function() {
    console.log('我是Func的装饰器逻辑')
    return originalMethod.apply(this, arguments)
  }
  return descriptor
}

class Button {
    @funcDecorator
    onClick() { 
        console.log('我是Func的原有逻辑')
    }
}

// 验证装饰器是否生效
const button = new Button()
button.onClick()
```

---
## 装饰器语法糖背后的故事
### 函数传参 & 调用
```js
function classDecorator(target) {
    target.hasDecorator = true
  	return target
}

// 将装饰器“安装”到Button类上
@classDecorator
class Button {
    // Button类的相关逻辑
}
```
给类添加装饰器时，`target`是类本身  

```js
function funcDecorator(target, name, descriptor) {
    let originalMethod = descriptor.value
    descriptor.value = function() {
        console.log('我是Func的装饰器逻辑')
        return originalMethod.apply(this, arguments)
    }
    return descriptor
}

class Button {
    @funcDecorator
    onClick() { 
        console.log('我是Func的原有逻辑')
    }
}   
```
修饰方法时`target`是`Button.prototype`,因为**onClick 方法总是要依附其实例存在的，修饰 onClik 其实是修饰它的实例。但我们的装饰器函数执行的时候，Button 实例还并不存在。为了确保实例生成后可以顺利调用被装饰好的方法，装饰器只能去修饰 Button 类的原型对象。**  

### 将“属性描述对象”交到你手里
```js
function funcDecorator(target, name, descriptor) {
    let originalMethod = descriptor.value
    descriptor.value = function() {
    console.log('我是Func的装饰器逻辑')
    return originalMethod.apply(this, arguments)
  }
  return descriptor
}
```
`target`是原型，`name`是要修饰的属性，`descriptor`就是`Object.defineProperty(obj, prop, descriptor)`中的`descriptor`

---
## 生产实践
### React中的装饰器:HOC
> 高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件。  

```js
// 高阶组件
import React, { Component } from 'react'

const BorderHoc = WrappedComponent => class extends Component {
  render() {
    return <div style={{ border: 'solid 1px red' }}>
      <WrappedComponent />
    </div>
  }
}
export default borderHoc

// 业务组件
import React, { Component } from 'react'
import BorderHoc from './BorderHoc'

// 用BorderHoc装饰目标组件
@BorderHoc 
class TargetComponent extends React.Component {
  render() {
    // 目标组件具体的业务逻辑
  }
}

// export出去的其实是一个被包裹后的组件
export default TargetComponent
```

### 使用装饰器改写 Redux connect
```js
import React, { Component } from 'react'
import { connect } from 'react-redux'
import { bindActionCreators } from 'redux'
import action from './action.js'

class App extends Component {
  render() {
    // App的业务逻辑
  }
}

function mapStateToProps(state) {
  // 假设App的状态对应状态树上的app节点
  return state.app
}

function mapDispatchToProps(dispatch) {
  // 这段看不懂也没关系，下面会有解释。重点理解connect的调用即可
  return bindActionCreators(action, dispatch)
}

// 把App组件与Redux绑在一起
export default connect(mapStateToProps, mapDispatchToProps)(App)
```

```js
import { connect } from 'react-redux'
import { bindActionCreators } from 'redux'
import action from './action.js'

function mapStateToProps(state) {
  return state.app
}

function mapDispatchToProps(dispatch) {
  return bindActionCreators(action, dispatch)
}

// 将connect调用后的结果作为一个装饰器导出
export default connect(mapStateToProps, mapDispatchToProps)
// 在组件文件里引入connect：

import React, { Component } from 'react'
import connect from './connect.js'   

@connect
export default class App extends Component {
  render() {
    // App的业务逻辑
  }
}
```
> [core-decorators](https://github.com/jayphelps/core-decorators)

---
# 适配器模式
适配器模式通过把一个类的接口变换成客户端所期待的另一种接口，可以帮我们解决不兼容的问题。

## axios中的适配器
在 [axios 的核心逻辑](https://github.com/axios/axios/blob/master/lib/core/Axios.js)中，我们可以注意到实际上派发请求的是 [dispatchRequest 方法](https://github.com/axios/axios/blob/master/lib/core/dispatchRequest.js)。该方法内部其实主要做了两件事：
1. 数据转换，转换请求体/响应体，可以理解为数据层面的适配；
2. 调用适配器。

调用如下适配器：
```js
function getDefaultAdapter() {
  var adapter;
  // 判断当前是否是node环境
  if (typeof process !== 'undefined' && Object.prototype.toString.call(process) === '[object process]') {
    // 如果是node环境，调用node专属的http适配器
    adapter = require('./adapters/http');
  } else if (typeof XMLHttpRequest !== 'undefined') {
    // 如果是浏览器环境，调用基于xhr的适配器
    adapter = require('./adapters/xhr');
  }
  return adapter;
}
```

http适配器
```js
module.exports = function httpAdapter(config) {
  return new Promise(function dispatchHttpRequest(resolvePromise, rejectPromise) {
    // 具体逻辑
  }
}
```

xhr适配器
```js
module.exports = function xhrAdapter(config) {
  return new Promise(function dispatchXhrRequest(resolve, reject) {
    // 具体逻辑
  }
}
```

---
# 代理模式
## ES6 的Proxy
```js
const proxy = new Proxy(obj, handler)
```
每次访问obj都需要通过handler

getter拦截
```js
// 普通私密信息
const baseInfo = ['age', 'career']
// 最私密信息
const privateInfo = ['avatar', 'phone']

// 用户（同事A）对象实例
const user = {
    ...(一些必要的个人信息)
    isValidated: true,
    isVIP: false,
}

// 掘金婚介所登场了
const JuejinLovers = new Proxy(girl, {
  get: function(girl, key) {
      if(baseInfo.indexOf(key)!==-1 && !user.isValidated) {
          alert('您还没有完成验证哦')
          return
      }
      
      //...(此处省略其它有的没的各种校验逻辑)
    
      // 此处我们认为只有验证过的用户才可以购买VIP
      if(user.isValidated && privateInfo.indexOf(key) && !user.isVIP) {
          alert('只有VIP才可以查看该信息哦')
          return
      }
  }
})
```

setter拦截
```js
// 规定礼物的数据结构由type和value组成
const present = {
    type: '巧克力',
    value: 60,
}

// 为用户增开presents字段存储礼物
const girl = {
  // 姓名
  name: '小美',
  // 自我介绍
  aboutMe: '...'（大家自行脑补吧）
  // 年龄
  age: 24,
  // 职业
  career: 'teacher',
  // 假头像
  fakeAvatar: 'xxxx'(新垣结衣的图片地址）
  // 真实头像
  avatar: 'xxxx'(自己的照片地址),
  // 手机号
  phone: 123456,
  // 礼物数组
  presents: [],
  // 拒收50块以下的礼物
  bottomValue: 50,
  // 记录最近一次收到的礼物
  lastPresent: present,
}

// 掘金婚介所推出了小礼物功能
const JuejinLovers = new Proxy(girl, {
  get: function(girl, key) {
    if(baseInfo.indexOf(key)!==-1 && !user.isValidated) {
        alert('您还没有完成验证哦')
        return
    }
    
    //...(此处省略其它有的没的各种校验逻辑)
  
    // 此处我们认为只有验证过的用户才可以购买VIP
    if(user.isValidated && privateInfo.indexOf(key)!==-1 && !user.isVIP) {
        alert('只有VIP才可以查看该信息哦')
        return
    }
  }
  
  set: function(girl, key, val) {
 
    // 最近一次送来的礼物会尝试赋值给lastPresent字段
    if(key === 'lastPresent') {
      if(val.value < girl.bottomValue) {
          alert('sorry，您的礼物被拒收了')
          return
      }
    
      // 如果没有拒收，则赋值成功，同时并入presents数组
      girl.lastPresent = val
      girl.presents = [...girl.presents, val]
    }
  }
 
})
```

## 常见四种代理模式
### 事件代理
```js
// 获取父元素
const father = document.getElementById('father')

// 给父元素安装一次监听函数
father.addEventListener('click', function(e) {
    // 识别是否是目标子元素
    if(e.target.tagName === 'A') {
        // 以下是监听函数的函数体
        e.preventDefault()
        alert(`我是${e.target.innerText}`)
    }
} )
```

### 虚拟代理
图片预加载

错误的做法
```js
class PreLoadImage {
    // 占位图的url地址
    static LOADING_URL = 'xxxxxx'
    
    constructor(imgNode) {
        // 获取该实例对应的DOM节点
        this.imgNode = imgNode
    }
    
    // 该方法用于设置真实的图片地址
    setSrc(targetUrl) {
        // img节点初始化时展示的是一个占位图
        this.imgNode.src = PreLoadImage.LOADING_URL
        // 创建一个帮我们加载图片的Image实例
        const image = new Image()
        // 监听目标图片加载的情况，完成时再将DOM上的img节点的src属性设置为目标图片的url
        image.onload = () => {
            this.imgNode.src = targetUrl
        }
        // 设置src属性，Image实例开始加载图片
        image.src = targetUrl
    }
}
```

正确的做法
```js
class PreLoadImage {
    constructor(imgNode) {
        // 获取真实的DOM节点
        this.imgNode = imgNode
    }
     
    // 操作img节点的src属性
    setSrc(imgUrl) {
        this.imgNode.src = imgUrl
    }
}

class ProxyImage {
    // 占位图的url地址
    static LOADING_URL = 'xxxxxx'

    constructor(targetImage) {
        // 目标Image，即PreLoadImage实例
        this.targetImage = targetImage
    }
    
    // 该方法主要操作虚拟Image，完成加载
    setSrc(targetUrl) {
       // 真实img节点初始化时展示的是一个占位图
        this.targetImage.setSrc(ProxyImage.LOADING_URL)
        // 创建一个帮我们加载图片的虚拟Image实例
        const virtualImage = new Image()
        // 监听目标图片加载的情况，完成时再将DOM上的真实img节点的src属性设置为目标图片的url
        virtualImage.onload = () => {
            this.targetImage.setSrc(targetUrl)
        }
        // 设置src属性，虚拟Image实例开始加载图片
        virtualImage.src = targetUrl
    }
}
```
ProxyImage 帮我们调度了预加载相关的工作，我们可以通过 ProxyImage 这个代理，实现对真实 img 节点的间接访问，并得到我们想要的效果。

在这个实例中，virtualImage 这个对象是一个“幕后英雄”，它始终存在于 JavaScript 世界中、代替真实 DOM 发起了图片加载请求、完成了图片加载工作，却从未在渲染层面抛头露面。因此这种模式被称为“虚拟代理”模式。

### 缓存代理
对计算结果缓存
```js
// addAll方法会对你传入的所有参数做求和操作
const addAll = function() {
    console.log('进行了一次新计算')
    let result = 0
    const len = arguments.length
    for(let i = 0; i < len; i++) {
        result += arguments[i]
    }
    return result
}

// 为求和方法创建代理
const proxyAddAll = (function(){
    // 求和结果的缓存池
    const resultCache = {}
    return function() {
        // 将入参转化为一个唯一的入参字符串
        const args = Array.prototype.join.call(arguments, ',')
        
        // 检查本次入参是否有对应的计算结果
        if(args in resultCache) {
            // 如果有，则返回缓存池里现成的结果
            return resultCache[args]
        }
        return resultCache[args] = addAll(...arguments)
    }
})()
```

### 保护代理
上面拦截getter和setter就是一种保护代理

## 小结
代理模式十分多样，可以是为了加强控制、拓展功能、提高性能，也可以仅仅是为了优化我们的代码结构、实现功能的解耦。

---
# test
## test1