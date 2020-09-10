---
title: koa-views
description: koa-views文档
categories:
 - Node.js
tags:
 - node
 - koa
 - utils
---


### 介绍
npm地址：[koa-views  -  npm](https://www.npmjs.com/package/koa-views)
koa渲染模板中间件
* 示例  
  ```js
  var views = require('koa-views');

  // Must be used before any router is used
  app.use(views(__dirname + '/views', {
    map: {
      html: 'underscore'
    }
  }));

  app.use(async function (ctx, next) {
    ctx.state = {
      session: this.session,
      title: 'app'
    };

    await ctx.render('user', {
      user: 'John'
    });
  });
  ```  


- - - -


### 简单中间件
可以理解为将render方法单独拿出，使用简单
```js
  var render = require('koa-views-render');
  
  // ...
  
  app.use(render('home', { title : 'Home Page' });
```
- - - -
### API
#### view(root,opts)
##### `root`
传入渲染模板的绝对路径
##### `opts` 
配置
* opts.extension  
  配置模板的扩展名,配置后就无需在每个文件都加入扩展名  
```js
  app.use(async function (ctx) {
  await ctx.render('user.pug')
  })
  // 如果使用opts.extension就可以这样写
  app.use(views(__dirname, {extension: 'pug'))

  app.use(async (ctx) => {
    await ctx.render('user')
  })
```
* opts.map
可以使用设置的引擎去渲染模板（[nunjucks模板引擎](https://www.liaoxuefeng.com/wiki/001434446689867b27157e896e74d51a89c25cc8b43bdb3000/0014713964925087c29166d8c344a949364e40e2f28dc09000)）  
```js
  app.use(views(__dirname, { map: {html: 'nunjucks' }}))

  // 每个html文件都会使用nunjucks引擎渲染
  app.use(async function (ctx) {
    await ctx.render('user.html')
  })
```
* opts.engineSource
可以指定相应扩展名的文件使用指定的扩展名替换  
```js
  //foo后缀的文件都将
  app.use(views(__dirname, { engineSource: {foo: () => Promise.resolve('bar')}}))

  app.use(async function (ctx) {
    await ctx.render('index.foo')
  })
```
* opts.options
将配置传递给模板引擎
```js
const app = new Koa()
  .use(views(__dirname, {
    map: { hbs: 'handlebars' },
    options: {
      helpers: {
        uppercase: (str) => str.toUpperCase()
      },
 
      partials: {
        subTitle: './my-partial' // requires ./my-partial.hbs
      }
    }
  }))
  .use(function (ctx) {
    ctx.state = { title: 'my title', author: 'queckezz' }
    return ctx.render('./my-view.hbs')
  })
```







#node/koa