---
title: JavaScript忍者秘籍(1)
description: JavaScript忍者秘籍读书笔记
categories:
- JavaScript
tags:
- 笔记
- JavaScript
---

### 第一章
#### 版本支持
可以通过以下网址查看浏览器对各个版本js的支持情况:  
1. https://kangax.github.io/compat-table/es6/
2. http://kangax. github. io/compat-table/es2016plus/ 
3. https://kangax.github.io/compat-table/esnext/

#### 性能分析
可以通过`console.time`和`console.timeEnd`来展示一段代码执行的事件

#### 跨平台开发能力
* 桌面应用：
  * [NW.js](http://nwjs.io)
  * [Electron](http://electron.atom.io)
* 移动应用：
  * [apache Cordova](https://cordova.apache.org)
* Node.js

### 第二章：运行时的页面构建过程 
#### 输入url发生了什么
![](/img/JavaScript忍者秘籍/2-1.jpg)
- - - -
##### 页面构建阶段
![](/img/JavaScript忍者秘籍/2-2.jpg)
![](/img/JavaScript忍者秘籍/2-3.jpg)
> dom与html并不相同，html更像是dom的蓝图，浏览器根据html构建dom，如果html有错误浏览器会修复它发现的问题
> 在构建时如果遇到脚本元素浏览器会停止构建dom执行script代码

当所有的html元素和js代码都执行完毕后就进入生命周期第二部分：事件处理。
- - - -
##### 事件处理
![](/img/JavaScript忍者秘籍/2-4.jpg)

事件有两种注册方式
  * 通过把函数赋给某个特殊属性(如onload)
  * 通过使用内置addEventListener方法
- - - -
### 第三章：理解函数
#### 函数作为对象的乐趣
可以像给对象添加属性一样给函数添加属性
```js
var fn = function () {};
fn.a = 'a';
```
这样函数就拥有了自我记忆的能力
```js
function isPrime(value) {
　if (!isPrime.answers) {
　　isPrime.answers = {};
　}　　←---　创建缓存
　if (isPrime.answers[value] !== undefined) {
　　return isPrime.answers[value];
　}　　←---　检查缓存的值
　var prime = value !== 0 && value !== 1; // 1 is not a prime
　for (var i = 2; i < value; i++) {
　　 if (value % i === 0) {
　　　　prime = false;
　　　　break;
　　 }
　}
　return isPrime.answers[value] = prime; 　　←---　存储计算的值
}

assert(isPrime(5), "5 is prime!");
assert(isPrime.answers[5], "The answer was cached!"); 　　←---　测试该函数是否正常工作
```
> 这样做有两个优点：
> 1. 由于函数调用时会寻找之前调用所得到的值，所以用户最终会乐于看到所获得的性能收益。
> 2. 它几乎是无缝地发生在后台，最终用户和页面作者都不需要执行任何特殊请求，也不需要做任何额外初始化，就能顺利进行工作。

> 但是它也有以下缺点：
> 1. 任何类型的缓存都必然会为性能牺牲内存。
> 2. 纯粹主义者会认为缓存逻辑不应该和业务逻辑混合，函数或方法只需要把一件事做好。但不必担心，在第8章你会了解到如何解决这类问题。
> 3. 对于这类问题很难做负载测试或估计算法复杂度，因为结果依赖于函数之前的输入。

- - - -
#### 函数声明和函数表达式
函数声明
```js
function a() {}
```
函数表达式
```js
var a = function() {};
myFunction(function() {});
```
> 这种总是其他表达式一部分的函数(作为复制表达式的右值，或者作为其他函数的参数)叫做函数表达式。
> 对于函数声明来说，函数名是强制性的，而对于函数表达式来说，函数名则完全是可选的。
立即函数是一种函数表达式

```js
(function() {})();
+function(){}();
-function(){}();
!function(){}();
~function(){}();
// 少见的写法
(function() {}());
```
> 之所以用括号包裹是为了告诉js解释器这条语句是函数表达式，否则它会被当做函数声明

- - - -
#### 剩余参数
```js
function multiMax(first, ...other) {
  // 多余的参数会存在other数组中
  console.log(other)
}
```
> 需要注意的是只有最后一个形参可以是剩余参数

- - - -
#### 默认参数
```js
function (a = 'b') {
  console.log(a) // b
}
```
- - - -
### 第四章：函数进阶，理解函数调用
#### arguments
其为一个类数组，包含了所有传入函数的实参,其不可调用数组方法。

改变arguments中的值也会改变函数对应实参的值,**但是在严格模式中改变arguments中的值并不会改变函数实参**

- - - -
#### this
#### 函数调用
有四种方式调用一个函数
* fun()
* obj.fun()
* new fun()
* fun.apply(obj)

##### 直接调用
此时this有两种可能
1. 在非严格模式下，this为window
2. 在严格模式下，this为undefined

##### 作为方法被调用
此时的this为该方法的宿主对象

##### 构造函数
使用new关键字会触发以下几个动作：
1. 创建一个新的空对象
2. 将该对象作为this传给构造函数，作为函数上下文
3. 新构造的对象作为new运算符的返回值

如果构造函数返回一个对象则将这个对象作为整个表达式的值返回，this被抛弃  
如果构造函数返回的是非对象类型，则忽略返回值，返回新创建的对象

##### 使用apply和call方法调用
当为一个dom元素绑定方法时this会指向dom本身

apply和call可以指定上下文

- - - -
#### 解决函数上下文的问题

##### 使用箭头函数
箭头函数的this与生命所在的上下文this相同，箭头函数执行时不会传入this而是继承所在上下文的this

##### 使用bind方法
所有函数均可访问bind方法，可以创建并返回一个新函数，并绑定在传入的对象上。

- - - -
### 第五章：精通函数，闭包和作用域

#### 理解闭包
闭包允许函数访问并操作函数外部的变量。只要变量或函数存在于生命函数时的作用域内，闭包即可使函数能够访问这些变量或函数。
```js
var outerValue = 'ninja';
function outerFunction() {
  outerValue === 'ninja';
}
outerFunction();
```
> 外部变量outerValue和外部函数outerFunction都是在全局作用域中声明的，该作用域（实际上就是一个闭包）从未消失（只要应用处于运行状态）。这也不足为奇，该函数可以访问到外部变量，因为它仍然在作用域内并且是可见的。

```js
var outerValue = "samurai";
var later; //　　←---　声明一个空变量，稍后在后面的代码中使用

function outerFunction() {
　var innerValue = "ninja";　//←---　在函数内部声明一个值，该值在作用域局限于函数内部，在函数外部不允许访问

　　function innerFunction() {
　　　assert(outerValue === "samurai", "I can see the samurai.");
　　　assert(innerValue === "ninja", "I can see the ninja.")
　　}　　←---　在outerFunction函数中声明一个内部函数，声明该内部函数时，innerValue是在内部函数的作用域内的

　　later = innerFunction; //　←---　将内部函数innerFunction的引用存储在变量later上，因为later在全局作用域内，所以我们可以对它进行调用
}

outerFunction();　//←---　调用outerFunction函数，创建内部函数innerFunction，并将内部函数赋值给变量later

later();//　←---　通过later调用内部函数。我们不能直接调用内部函数，因为它的作用域（和innerValue一起）被限制在外部函数outerFunction之内
```

这就是闭包。闭包创建了被定义时的作用域内的变量和函数的安全气泡，因此函数获得了执行时所需的内容。该气泡与函数本身一起包含了函数和变量。

虽然这些结构不容易看见（没有包含这么多信息的闭包对象可以进行观察），存储和引用这些信息会直接影响性能。谨记每一个通过闭包访问变量的函数都具有一个作用域链，作用域链包含闭包的全部信息，这一点非常重要。因此，虽然闭包是非常有用的，但不能过度使用。使用闭包时，所有的信息都会存储在内存中，直到JavaScript引擎确保这些信息不再使用（可以安全地进行垃圾回收）或页面卸载时，才会清理这些信息。

- - - -
#### 使用闭包
##### 封装私有变量
```js
function Ninja() {　//←---　定义nijia构造函数
  　var feints = 0; 　//←---　在构造函数内部声明一个变量，因为所声明的变量的作用域局限于构造函数的内部，所以它是一个“私有”变量。我们使用该变量统计ninja佯攻的次数
  　this.getFeints = function() {
  　　 return feints; 　//←---　创建用于访问计数变量feints的方法。由于在构造函数外部的代码是无法访问feints变量的，这是通过办读形式访问该变量的常用方法
  　};
  　this.feint = function() {
  　　 feints++;
  　};　　//←---　为feints变量声明一个累加方法。由于feints就私有变量，在外部是无法累加的，累加过程则被限制在我们提供的方法中
  }
  
  var ninja1 = new Ninja();　//←---　现在开始测试，首先创建一个ninja的实例
  ninja1.feint();　//←---　调用feint方法，通过该方法增加ninja的佯攻次数
  
  assert(ninja1.feints === undefined,//　←---　验证我们无法直接获取该变量值
  　　　 "And the private data is inaccessible to us.");
  assert(ninja1.getFeints() === 1, 
  　　　 "We're able to access the internal feint count."); 　//←---　虽然我们无法直接对feints变量赋值，但是我们仍然能够通过getFeints方法操作该变量的值
  
  var ninja2 = new Ninja();
  assert(ninja2.getFeints() === 0, 
  　　　 "The second ninja object gets its own feints variable."); //　←---　当我们通过ninja构造函数创建一个新的ninja2实例时，ninja2对象则具有自己私有的feints变量

```
> 在本例中我们可通过闭包内部方法获取私有变量的值，但是不能直接访问。

![](/img/JavaScript忍者秘籍/5-1.png)

##### 回调函数
```html
<div id="box1">First Box</div>　//←---　创建用于展示动画的DOM元素
<script>
　function animateIt(elementId) {
　　 var elem = document.getElementById(elementId); //　←---　在动画函数animatelt内部，获取DOM元素的引用
　　 var tick = 0; 　　←---　创建一个计时器用于记录动画执行的次数
　　 var timer = setInterval(function() {//　←---　创建并启动一个JavaScript内置的计时器，传入一个回调函数
　　　 if (tick < 100) {
　　　　 elem.style.left = elem.style.top = tick + "px";
　　　　 tick++;
　　　 }　　←---　每隔10毫秒调用一次计时器的回调函数，调整元素的位置100次
　　　 else {
　　　　 clearInterval(timer);
　　　　 assert(tick === 100, 　//←---　执行了100次之后，停止计时器，并验证我们还可以看到与执行动画有关的变量
　　　　　　　　　"Tick accessed via a closure.");
　　　　 assert(elem, 
　　　　　　　　　"Element also accessed via a closure.");
　　　　 assert(timer, 
　　　　　　　　　"Timer reference also obtained via a closure.");
　　 }
　　}, 10);　//←---　 setInterval函数的持续时间为10毫秒，也就是说回调函数每隔10秒调用一次
　}
　animateIt("box1");　//←---　全部都设置完成之后，我们可以执行动画函数并查看动画效果
</script>
```
> 每次计时器中回调函数执行都可以读取到特定信息

上述示例说明闭包内的函数不仅可以在创建的时刻访问这些变量，而且当闭包内部的函数执行时，还可以更新这些变量的值。闭包不是在创建的那一时刻的状态的快照，而是一个真实的状态封装，只要闭包存在，就可以对变量进行修改。

- - - -
#### 通过执行上下文来跟踪代码
JavaScript代码有两种类型，一种是全局代码，在所有函数外部定义；一种是函数代码，位于函数内部。

既然具有两种类型的代码，那么就有两种执行上下文：全局执行上下文和函数执行上下文。二者最重要的差别是：全局执行上下文只有一个，当JavaScript程序开始执行时就已经创建了全局上下文；而函数执行上下文是在每次调用函数时，就会创建一个新的。

> 第4章介绍了当调用函数时可通过关键字访问函数上下文。函数执行上下文，虽然也称为上下文，但完全是不一样的概念。执行上下文是内部的JavaScript概念，JavaScript引擎使用执行上下文来跟踪函数的执行。

JavaScript基于单线程的执行模型：在某个特定的时刻只能执行特定的代码。一旦发生函数调用，当前的执行上下文必须停止执行，并创建新的函数执行上下文来执行函数。当函数执行完成后，将函数执行上下文销毁，并重新回到发生调用时的执行上下文中。所以需要跟踪执行上下文——正在执行的上下文以及正在等待的上下文。最简单的跟踪方法是使用执行上下文栈（或称为调用栈）。

![](/img/JavaScript忍者秘籍/5-2.png)
> 1. 每个JavaScript程序只创建一个全局执行上下文，并从全局执行上下文开始执行（在单页应用中每个页面只有一个全局执行上下文）。当执行全局代码时，全局执行上下文处于活跃状态。
2. 首先在全局代码中定义两个函数：skulk和report，然后调用skulk("Kuma")。由于在同一个特定时刻只能执行特定代码，所以JavaScript引擎停止执行全局代码，开始执行带有Kuma参数的skulk函数。创建新的函数执行上下文，并置入执行上下文栈的顶部。
3. skulk函数进而调用report函数。又一次因为在同一个特定时刻只能执行特定代码，所以，暂停skulk执行上下文，创建新的Kuma作为参数的report函数的执行上下文，并置入执行上下文栈的顶部。
4. report通过内置函数console.log（详见附录B）打印出消息后，report函数执行完成，代码又回到了skulk函数。report执行上下文从执行上下文栈顶部弹出，skulk函数执行上下文重新激活，skulk函数继续执行。
5. skulk函数执行完成后也发生类似的事情：skulk函数执行上下文从栈顶端弹出，重新激活一直在等待的全局执行上下文并恢复执行。JavaScript的全局代码恢复执行。

虽然执行上下文栈（execution context stack）是JavaScript内部概念，但仍然可以通过JavaScript调试器中查看，在JavaScript调试器中可以看到对应的调用栈（call stack）。

![](/img/JavaScript忍者秘籍/5-3.png)

- - - -
#### 使用词法环境跟踪变量的作用域
词法环境（lexical environment）是JavaScript引擎内部用来跟踪标识符与特定变量之间的映射关系。
通常来说，词法环境与特定的JavaScript代码结构关联，既可以是一个函数、一段代码片段，也可以是try-catch语句。这些代码结构（函数、代码片段、try-catch）可以具有独立的标识符映射表。

##### 代码嵌套
![](/img/JavaScript忍者秘籍/5-4.png)

在作用域范围内，每次执行代码时，代码结构都获得与之关联的词法环境。例如，每次调用skulk函数，都将创建新的函数词法环境。
此外，需要着重强调的是，内部代码结构可以访问外部代码结构中定义的变量。例如，for循环可以访问report函数、skulk函数以及全局代码中的变量；report函数可以访问skulk函数及全局代码中的变量；skulk函数可以访问的额外变量但仅是全局代码中的变量。

##### 代码嵌套与词法环境
![](/img/JavaScript忍者秘籍/5-5.png)

- - - -
#### 理解JavaScript的变量类型
在JavaScript中可以通过三个关键字定义变量`var`，`let`，`const`，它们有两点不同：可变性，与词法环境的关系；

##### 变量可变性
const不可变，不过如果是引用值的话可以改变其中内容但是不可以改变引用地址

##### 定义变量的关键字与词法环境
当使用关键字var时，该变量是在距离最近的函数内部或是在全局词法环境中定义的。（注意：忽略块级作用域）这是JavaScript由来已久的特性。

```js
var globalNinja = "Yoshi";　//←---　使用关键字var定义全局变量

function reportActivity() {
　 var functionActivity = "jumping";　//←---　使用关键字var定义函数内部的局部变量

　　for (var i = 1; i < 3; i++) {
　　　　　var forMessage = globalNinja + " " + functionActivity; //　←---　使用关键字var在for循环中定义两个变量
　　　　　assert(forMessage === "Yoshi jumping",
　　　　　　　　　"Yoshi is jumping within the for block");//　←---　在for循环中可以访问块级变量，函数内的局部变量以及全局变量
　　　　　assert(i, "Current loop counter:" + i);
　　}

　　assert(i === 3 && forMessage === "Yoshi jumping",
　　　　　　"Loop variables accessible outside of the loop");//　←---　但是在for循环外部，仍然能访问for循环中定义的变量
　　}

reportActivity();
assert(typeof functionActivity === "undefined"
　　 && typeof i === "undefined" && typeof forMessage === "undefined",
　　 "We cannot see function variables outside of a function");//　←---　函数外部无法访问函数内部的局部变量

```
> 变量globalNinja和functionActivity能访问是在预料之中的，可以for循环中的变量依然能在外部访问

这源于通过var声明的变量实际上总是在距离最近的函数内或全局词法环境中注册的，不关注块级作用域。图5.11描述了这一现象，图中展示了reportActivity函数内的for循环执行后的词法环境。
![](/img/JavaScript忍者秘籍/5-6.jpg)

