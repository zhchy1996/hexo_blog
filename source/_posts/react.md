---
title: react文档记录
description: 仅记录在阅读react文档过程中需要注意的点
date: 2020-11-17 14:18:48
tags: react
---
[react 官方文档](https://zh-hans.reactjs.org/docs/)
# 核心概念
## Component
* 组件名首字母必须大写
* 组件支持函数式组件和`class`组件，如果不需要`state`使用函数式组件即可
* 组件接受`props`作为传入参数，并且不要在组件中改变`props`，如果需要要使用`state`
* `class`组件由`constructor`接收`props`并且需要调用`super(props)`来调用父组件构造函数
---

## State & 生命周期
* 必须调用`this.setState`来更新数据
* 因为`this.props`和`this.state`可能会异步更新，所以不要依赖他们的值来更新下一个状态,可以传入一个函数来获取正确状态
```js
// Wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
// Correct
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
```
* `this.setState`会合并`this.state`并且是浅合并
* 数据的传递是从父组件到子组件的
---

## 事件绑定
* 如果需要使用`this`需要绑定`this`到函数上
```js
this.handleClick = this.handleClick.bind(this);
```
* 如果觉着上面那种写法麻烦可以用下面两种写法
```js
// 实验性语法
handleClick = () => {
    console.log('this is:', this);
}
// 此语法问题在于每次渲染 LoggingButton 时都会创建不同的回调函数。在大多数情况下，这没什么问题，但如果该回调函数作为 prop 传入子组件时，这些组件可能会进行额外的重新渲染。
<button onClick={() => this.handleClick()}>
    Click me
</button>
```
* 传递额外参数可以使用这两种写法
```js
// react事件对象e必须显式传递
<button onClick={(e) => this.deleteRow(id, e)}>Delete Row</button>
// 事件对象将隐式传递
<button onClick={this.deleteRow.bind(this, id)}>Delete Row</button>
```
---

## 条件渲染
* 可以使用变量存储元素
* `this.state`的改变会触发`render`函数，所以可以在`render`中进行相关的条件判断
* 可以用与运算符`&&` 来简化写法，如果左式返回`false`则不会渲染任何内容，需要注意的是如果返回`0`则会渲染出数字0，不过`null`和`undefined`则不会出现这种情况
* 三目运算符也可以使用
* 不想渲染任何内容则可以让`render`函数返回`null`
---

## list & key
* 返回一个元素数组即可实现
* 每个元素也需要一个`key`,`key`最好是在列表中独一无二的字符串,可以[参考这个文章](https://zh-hans.reactjs.org/docs/reconciliation.html#recursing-on-children)
* `key`最好是数据的`id`,也可以使用索引，使用数据顺序可能发生变化则不建议使用索引，会导致性能变差，可参考[这篇文章](https://medium.com/@robinpokorny/index-as-a-key-is-an-anti-pattern-e0349aece318)
* 元素的`key`应该放在就近的数组上下文中
```js
function ListItem(props) {
  const value = props.value;
  return (
    // 错误！你不需要在这里指定 key：
    <li key={value.toString()}>
      {value}
    </li>
  );
}

function NumberList(props) {
  const numbers = props.numbers;
  const listItems = numbers.map((number) =>
    // 错误！元素的 key 应该在这里指定：
    <ListItem value={number} />
  );
  return (
    <ul>
      {listItems}
    </ul>
  );
}
```
* `key`在兄弟节点中保持唯一即可
* 用户组件不会接收到key
---

## 表单
* 可以创建受控组件-使用`state`配合`onChange` 来接管表单数据
* 同时操控多个`input`可以使用同一个方法通过`name`区分`target.name`
---

## 状态提升  
对于多个子组件共用的数据可以在父组件中维护，通过`props`向子组件传递数据，并且由父组件提供通过`props`传入的方法实现数据变更

---
## 组合 VS 继承
react 中是没有像 vue 那样的插槽概念的，因为在 react 中，元素可以作为参数传递
* 可以通过`props.childred`来将子组件渲染进结果中
```js
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {props.children}
    </div>
  );
}

function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```
* 如果需要多个"洞"的话可以利用`props`
```js
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      } />
  );
}
```
* props 可以满足绝大多数需求，所以不需要`extend`继承

---
## react 哲学
1. 根据 UI 划分组件层级
2. 创建一个静态版本
当应用简单时可以采取自上而下的方式，当项目复杂时可以采取自下而上的方式
3. 确定 UI state 的最小（且完整）表示
只保留应用所需的可变 state 的最小集合，其他数据均由它们计算产生,判断是否是 state 可以通过如下三个步骤：
   1. 该数据是否是由父组件通过 props 传递而来的？如果是，那它应该不是 state。
   2. 该数据是否随时间的推移而保持不变？如果是，那它应该也不是 state。
   3. 你能否根据其他 state 或 props 计算出该数据的值？如果是，那它也不是 state。
4. 确定 state 放置位置
    * 找到根据这个 state 进行渲染的所有组件。
    * 找到他们的共同所有者（common owner）组件（在组件层级上高于所有需要该 state 的组件）。
    * 该共同所有者组件或者比它层级更高的组件应该拥有该 state。
    * 如果你找不到一个合适的位置来存放该 state，就可以直接创建一个新的组件来存放该 state，并将这一新组件置于高于共同所有者组件层级的位置。
5. 添加反向数据流

---
# 高阶指引
## 无障碍

---
## 代码分割
* `import()`语法可以实现代码分割，即按需加载
* 使用`React.lazy`可以动态引入组件，在 ssr 中可以使用[Loadable Components](https://github.com/gregberge/loadable-components)
* 应在`Suspense`中渲染 lazy 组件
```js
import React, { Suspense } from 'react';

const OtherComponent = React.lazy(() => import('./OtherComponent'));

function MyComponent() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <OtherComponent />
      </Suspense>
    </div>
  );
}
```
* 可通过异常捕获边界来处理加载失败
* `React.lazy`只支持默认导出，如果希望使用命名导出可以通过中间模块导出

---
## Context
* Context 提供了一个无需为每层组件手动添加 props，就能在组件树间进行数据传递的方法
```js
// Context 可以让我们无须明确地传遍每一个组件，就能将值深入传递进组件树。
// 为当前的 theme 创建一个 context（“light”为默认值）。
const ThemeContext = React.createContext('light');
class App extends React.Component {
  render() {
    // 使用一个 Provider 来将当前的 theme 传递给以下的组件树。
    // 无论多深，任何组件都能读取这个值。
    // 在这个例子中，我们将 “dark” 作为当前的值传递下去。
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// 中间的组件再也不必指明往下传递 theme 了。
function Toolbar() {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

class ThemedButton extends React.Component {
  // 指定 contextType 读取当前的 theme context。
  // React 会往上找到最近的 theme Provider，然后使用它的值。
  // 在这个例子中，当前的 theme 值为 “dark”。
  // 这里必须是contextType
  static contextType = ThemeContext;
  render() {
      // 这里必须是context
    return <div>{this.context}</div>;
  }
}
```
* 适用于很多不同层级的组件需要访问同样的一些数据，如果滥用会导致组件复用性变差，如果只是从顶层向下传递数据可以考虑直接把子组件作为参数传递
```js
function Page(props) {
  const user = props.user;
  const userLink = (
    <Link href={user.permalink}>
      <Avatar user={user} size={props.avatarSize} />
    </Link>
  );
  return <PageLayout userLink={userLink} />;
}

// 现在，我们有这样的组件：
<Page user={user} avatarSize={avatarSize} />
// ... 渲染出 ...
<PageLayout userLink={...} />
// ... 渲染出 ...
<NavigationBar userLink={...} />
// ... 渲染出 ...
{props.userLink}
```
* API
    * `const MyContext = React.createContext(defaultValue);`用于创建一个 context 对象
    * `<MyContext.Provider value={/* 某个值 */}>`给消费组件提供值，当`value`值变化时，内部所有消费组件会重新渲染。Provider 及其内部 consumer 组件都不受制于 shouldComponentUpdate 函数，因此当 consumer 组件在其祖先组件退出更新的情况下也能更新。传递对象给`value`时，检测变化的方式会导致一些问题。
    * `Class.contextType`挂载在 class 上的`contextType`属性会被重赋值为一个由`React.createContext()`创建的 Context 对象。
    ```js
    class MyClass extends React.Component {
    componentDidMount() {
        let value = this.context;
        /* 在组件挂载完成后，使用 MyContext 组件的值来执行一些有副作用的操作 */
    }
    componentDidUpdate() {
        let value = this.context;
        /* ... */
    }
    componentWillUnmount() {
        let value = this.context;
        /* ... */
    }
    render() {
        let value = this.context;
        /* 基于 MyContext 组件的值进行渲染 */
    }
    }
    MyClass.contextType = MyContext;

    // 实验性的语法
    class MyClass extends React.Component {
        static contextType = MyContext;
        render() {
            let value = this.context;
            /* 基于这个值进行渲染工作 */
        }
    }
    ```
    * `Context.Consumer`可以在函数式组件中订阅 context
    ```js
    <MyContext.Consumer>
    {value => /* 基于 context 值进行渲染*/}
    </MyContext.Consumer>
    ```
    * `Context.displayName`用于在 React DevTools 中显示 context 名称
* 可以在 context 中提供更改 context 的方法供消费组件使用
* 消费多个 context
```js
// Theme context，默认的 theme 是 “light” 值
const ThemeContext = React.createContext('light');

// 用户登录 context
const UserContext = React.createContext({
  name: 'Guest',
});

class App extends React.Component {
  render() {
    const {signedInUser, theme} = this.props;

    // 提供初始 context 值的 App 组件
    return (
      <ThemeContext.Provider value={theme}>
        <UserContext.Provider value={signedInUser}>
          <Layout />
        </UserContext.Provider>
      </ThemeContext.Provider>
    );
  }
}

function Layout() {
  return (
    <div>
      <Sidebar />
      <Content />
    </div>
  );
}

// 一个组件可能会消费多个 context
function Content() {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <UserContext.Consumer>
          {user => (
            <ProfilePage user={user} theme={theme} />
          )}
        </UserContext.Consumer>
      )}
    </ThemeContext.Consumer>
  );
}
```
* provider 父组件进行重新渲染时，可能会导致 consumers 组件重新渲染，因为`value`总是被赋值为新的对象
```js
class App extends React.Component {
  render() {
    return (
      <MyContext.Provider value={{something: 'something'}}>
        <Toolbar />
      </MyContext.Provider>
    );
  }
}

// 可以将value状态提升到父节点的 state 中
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: {something: 'something'},
    };
  }

  render() {
    return (
      <Provider value={this.state.value}>
        <Toolbar />
      </Provider>
    );
  }
}
```

---
## 错误边界
* 错误边界是一种 React 组件，这种组件可以捕获并打印发生在其子组件树任何位置的 JavaScript 错误，并且，它会渲染出备用 UI
* 任何未被错误边界获取的错误将会导致整个 React 组件树被卸载
* 无法捕获以下错误
    * 时间处理
    * 异步代码(如`setTimeout`或`requestAnimationFrame`回调函数)
    * 服务端渲染
    * 自身抛出的错误
* 如果一个 class 组件中定义了`static getDerivedStateFromError()`或`componentDidCatch()`这两个生命周期方法中的任意一个（或两个）时，那么它就变成一个错误边界，前者用于渲染备用UI，后者用于打印错误信息
```js
class Inner extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    }
    this.handleClick = this.handleClick.bind(this);
  }

  handleClick () {
    this.setState(state => state.count += 1)
  }

  render() {
    if (this.state.count === 5) throw new Error('count is 5')
    return <div onClick={this.handleClick}>{this.state.count}</div>
  }
}

class ErrorDemo extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      hasError: false
    }
  }

  static getDerivedStateFromError(error) {
    console.log(error)
    return {
      hasError: true
    };
  }

  componentDidCatch(error, errorInfo) {
    console.log('catch', error, errorInfo);
  }

  render() {
    return (
      this.state.hasError
      ? <div>error</div>
      : this.props.children
    )
  }
}

ReactDOM.render(
  <ErrorDemo>
    <Inner></Inner>
  </ErrorDemo>,
  document.getElementById('root')
);
```
* 如果直接抛出一个错误页面依然会报错
* 无法捕捉事件处理器内部的错误，可以使用`try/catch`

---
## Refs & DOM
提供了在经典数据流之外强制修改子组件或DOM的方式。
* 何时使用 Refs
    * 管理焦点，文本选择或媒体播放
    * 触发强制动画
    * 集成第三方 DOM 库
* 使用
    * 创建 Refs
    ```js
    class MyComponent extends React.Component {
        constructor(props) {
            super(props);
            this.myRef = React.createRef();
        }
        render() {
            return <div ref={this.myRef} />;
        }
    }
    ```
    * 访问 Refs `const node = this.myRef.current;`
    * ref 用于 HTML 元素时`ref.current`为底层 DOM,用于自定义 class 组件时,current 为挂载实例
    * 不能在函数组件上使用`ref`属性，因为没有实例, 可以使用`forwardRef`(可与`useImperativeHandle`结合使用)，在函数组件内部使用 ref 不受影响
* 可以给`ref`传入函数，函数会在挂载时执行，并且在卸载时传入null

---
## Refs 转发
* Ref 转发是一个可选特性，其允许某些组件接收 ref，并将其向下传递（换句话说，“转发”它）给子组件，这样就可以在外部的获取到组件内部的 ref。
```js
const FancyButton = React.forwardRef((props, ref) => (
  <button ref={ref} className="FancyButton">
    {props.children}
  </button>
));

// 你可以直接获取 DOM button 的 ref：
const ref = React.createRef();
<FancyButton ref={ref}>Click me!</FancyButton>;
```
* 高阶组件中转发(看完高阶组件再看)

---
## Fragments
* React 中的一个常见模式是一个组件返回多个元素。Fragments 允许你将子列表分组，而无需向 DOM 添加额外节点
```js
render() {
  return (
    <React.Fragment>
      <ChildA />
      <ChildB />
      <ChildC />
    </React.Fragment>
  );
}
```
* 支持短语法`<> </>`，这种写法不支持`key`

---
## 高阶组件 HOC
* 高阶组件是参数为组件，返回值为新组件的函数，`const EnhancedComponent = higherOrderComponent(WrappedComponent);`
* 用 HOC 替换了之前的 mixins，至于为什么要放弃 mixins 可以参考[这个](https://zh-hans.reactjs.org/blog/2016/07/13/mixins-considered-harmful.html)
> react是函数式风格，而mixins更偏向于面向对象  
> mixins使代码变的复杂并且可维护性变差
> 会有许多隐式依赖
> 会导致命名冲突，并难以重构
> mixins 的互相依赖和拓展会让代码逐渐复杂化







































