---
title: vue-styleguidist
description: vue-styleguidist也是一个vue组件文档生成工具，它与之前记录过的`vuese`很像，不过更加强大，支持demo展示，热重载，并且可以实时更改代码并查看效果，不过它对注释的支持更加严格，需要嵌入在项目中，并且并不像`vuese`那样自由，所以需要根据需求自行取舍。
categories:
- 实用工具
tags:
- vue
- 工具
---


### 开始使用
[git地址](https://github.com/vue-styleguidist/vue-styleguidist)
#### 安装
首先你必须有webpack
`npm install --save-dev webpack`
然后安装vue-styleguidist
`npm install --save-dev vue-styleguidist`
- - - -
#### 设置style guide
如果使用`vue-cli3.X`可以跳过这步，直接安装`vue-cli-plugin-styleguidist`。如果没有使用就需要配置一下。配置在下文会具体提到。
- - - -
#### 将启动命令添加到npm script中
在`package.josn`添加如下配置：
```js
	{
	  "scripts": {
	+    "styleguide": "vue-styleguidist server",
	+    "styleguide:build": "vue-styleguidist build"
	  }
	}
```
- - - -
#### 启动style guide
* `npm run styleguide`可以启动开发服务
* `npm run styleguide:build`可以生成静态版本

- - - -
### 配置目录
#### 配置组件目录
默认情况下styleguidist会寻找根目录下`src/components/**/*.vue`的文件来生成文档
如果你的项目时这样的结构`components/Button/Button.vue`
```js
	module.exports = {
	  components: 'src/components/**/[A-Z]*.vue'
	}
```
styleguidist会无视`__tests__`文件夹
> 相对路径是基于配置文件位置  
> `ignore` 配置可以设置排除哪些文件  
> 使用`getComponentPathLine`配置可以改变组件名称下显示的路径  
  
- - - -
#### 加载并暴露组件
styleguidist会加载你的所有组件并且将他们全局暴露，这样你就可以在实例中随意使用它们
- - - -
#### 章节划分
将组件分组添加到章节活接添加额外的markdown文档到你的style guide中。  
每个章节都可以包含以下属性（所有属性都是可选的）
* `name` 章节标题
* `content` 包含概述内容的markdown文件的位置
* `components` 一个描述组件路径的字符串或者一个有路径组成的数组，也可以是一个返回组件路径的方法。与`component` 设置遵从同样的规则。
* `sections` 设置小节的数组（可以嵌套）
* `description` 章节描述
* `sectionDepth` 单页中小节的数目，只有`pagePersecion`开启时才可用
* `exampleMode` 示例代码tab的初始状态
	* `collapse`：默认情况下折叠选项卡
	* `hide`：隐藏选项卡并且在UI中不能打开
	* `expand`：默认打开选项卡
* `usageMode` props与methods tab的默认状态，值与上条相同
* `ignore` 一个设置不包含在章节中的路径数组或字符串
* `href` 一个导航网址，会替代导航到部分内容
* `external` 如果设置的话就会在一个新窗口打开连接
```js
	module.exports = {
	  sections: [
	    {
	      name: 'Introduction',
	      content: 'docs/introduction.md'
	    },
	    {
	      name: 'Documentation',
	      sections: [
	        {
	          name: 'Installation',
	          content: 'docs/installation.md',
	          description: 'The description for the installation section'
	        },
	        {
	          name: 'Configuration',
	          content: 'docs/configuration.md'
	        },
	        {
	          name: 'Live Demo',
	          external: true,
	          href: 'http://example.com'
	        }
	      ]
	    },
	    {
	      name: 'UI Components',
	      content: 'docs/ui.md',
	      components: 'lib/components/ui/*.vue'
	    }
	  ]
	}
```  

- - - -
### 配置webpack
`vue-styleguidist` 底层使用webpack，你需要配置如何加载工程文件
#### 复用你项目的webpack配置
默认情况下styleguideist会使用你项目根目录的下`webpack.config.js`文件作为配置文件
如果你的webpack配置文件在其他地方你需要在`styleguide.confi.js`中手动配置：
```js
	// ./styleguide.config.js
	module.exports = {
	  webpackConfig: require('./configs/webpack.js')
	};
```
或者你想把它和其他配置合并：
```js
// ./styleguide.config.js
module.exports = {
	  webpackConfig: Object.assign({},
	    require('./configs/webpack.js'),
	    {
	      /* Custom config options */
	    }
	  )
	};
```
> `entry`,`externals`,`outpu`,`watch`和`stats`配置不会生效。如果是作为production构建，`devtool`也不会生效。  
> `CommonsChunkPlugins`, `HtmlWebpackPlugin`, `UglifyJsPlugin`, `HotModuleReplacementPlugin`插件配置均会失效，因为styleguidist已经包含了一些插件，其中的某些插件会影响styleguidist。  
> 如果你的loader不能使用styleguidist尝试在`include`和`exclude`中使用绝对路径。  
> babelified webpack配置（比如`webpack.config.babel.js`）不会生效。我们推荐使用原生node代码改造你的配置-node6代码支持许多es6特性。  
> 使用`webpack-merge` 来完成更简单的配置合并。  

- - - -
#### 自定义webpack配置
在你的`styleguide.config.js`中添加一个`webpackConfig` 属性:
```js
// ./styleguide.config.js
	module.exports = {
	  webpackConfig: {
	    module: {
	      rules: [
	        // Vue loader
	        {
	          test: /\.vue$/,
	          exclude: /node_modules/,
	          loader: 'vue-loader'
	        },
	        // Babel loader, will use your project’s .babelrc
	        {
	          test: /\.js?$/,
	          exclude: /node_modules/,
	          loader: 'babel-loader'
	        },
	        // Other loaders that are needed for your components
	        {
	          test: /\.css$/,
	          loader: 'style-loader!css-loader'
	        }
	      ]
	    }
	  }
	};
```
> 注意此配置会使`webpack.config.js`中的配置失效  
> `entry`, `externals`, `output`, `watch`, 和`stats`配置会失效。对于production构建`devtool`也同样会失效。  
> `CommonsChunkPlugins`, `HtmlWebpackPlugin`, `UglifyJsPlugin`, `HotModuleReplacementPlugin`插件配置均会失效，因为styleguidist已经包含了一些插件，其中的某些插件会影响styleguidist。  

- - - -
#### 不工作情况
在非常少的情况下像使用老式或第三方的库，你可能需要改变那些styleguidist不允许你改变的`webpackconfig`配置，在这种情况下，你可以使用[dangerouslyUpdateWebpackConfig](https://github.com/vue-styleguidist/vue-styleguidist/blob/master/docs/Configuration.md#dangerouslyupdatewebpackconfig)配置。

- - - -
### 为组件生成文档
vue styleguidist基于你代码的声明注释和readme生成文档。  
> 这是可以生成文档的[示例](https://github.com/vue-styleguidist/vue-styleguidist/tree/master/examples/basic/src/components)  
  
#### 代码注释
vue styleguidist将会展示你组件的js注释块。
```js
	<template>
	  <div class="Button">
	    /* ... */
	  </div>
	</template>
	
	<script>
	
	/**
	 * The only true button.
	 */
	export default {
	  name: 'Button',
	  props: {
	    /**
	    * The color for the button.
	    */
	    color: {
	      type: String,
	      default: '#333'
	    },
	    /**
	    * The size of the button
	    * `small, normal, large`
	    */
	    size: {
	      type: String,
	      default: 'normal'
	    },
	    /**
	    * Gets called when the user clicks on the button
	    */
	    onClick: {
	      type:Function,
	      default: (event) => {
	        console.log('You have clicked me!', event.target);
	      }
	    }
	  },
	  /* ... */
	}
	</script>
```
如果你想创建一个自定义的v-model你需要在注释中添加`model`标签
```js
	<script>
	export default {
	  name: 'my-checkbox',
	  props: {
	    /**
	     * @model
	     */
	    value: String
	  }
	}
	</script>
```
对于事件这么定义
```js
	/**
	 * Success event.
	 *
	 * @event success
	 * @type {object}
	 */
	this.$emit('success', {
	  demo: 'example'
	})
```
> 你可以改变它的解析方式通过[propsParse](https://github.com/vue-styleguidist/vue-styleguidist/blob/master/docs/Configuration.md#propsparser)配置，默认使用[vue-docgen-api](https://github.com/vue-styleguidist/vue-docgen-api)。  
> `name` 属性是组件强制设置的，vue styleguidist会使用它作为示例的组件名称，如果你需要在文档生成时配置名字你需要像[示例](https://github.com/vue-styleguidist/buefy-styleguide-example/blob/master/styleguide.config.js#L43)这么做  
  
```js
  // 这是在propsParser的配置中
  // vueDocs是vue-docgen-api提供的一个方法
  // camelToKebab是一个解析名字的方法
	propsParser(file, source) {
		const doc = vueDocs.parse(file)
		doc.displayName = camelToKebab(doc.displayName)
		return doc
	},
```
#### slots文档
默认情况下，vue styleguidist不会生成slots文档，你需要在template中的slot之前使用`@slot` 来标记。
```js
  <template>
	  <div class="modal">
	    <div class="modal-container">
	      <div class="modal-head">
	        <!-- @slot Use this slot header -->
	        <slot name="head"></slot>
	      </div>
	      <div class="modal-body">
	        <!-- @slot Use this slot body -->
	        <slot name="body"></slot>
	      </div>
	    </div>
	  </div>
	</template>
```
- - - -
#### 包含Mixins和Extends
如果你引入了一个`mixin`或者`extends`，如果要生成文档你需要在头部添加`@mixin`标签。
Mixins：
```js
	// src/mixins/colorMixin.js
	
	/**
	 * @mixin
	 */
	module.exports = {
	  props: {
	    /**
	     * The color for the button example
	     */
	    color: {
	      type: String,
	      default: '#333'
	    }
	  }
	}
```
Extends：
```js
	// src/extends/Base.vue
	
	<template>
	  <div>
	    <h4>{{ color }}</h4>
	    <!--the appropriate input should go here-->
	  </div>
	</template>
	<script>
	/**
	 * @mixin
	 */
	export default {
	  props: {
	    /**
	     * The color for the button example
	     */
	    colorExtends: {
	      type: String,
	      default: '#333'
	    }
	  }
	}
	</script>
```
```js
	<template>
	<!-- -->
	</template>
	<script>
	// src/components/Button/Button.vue
	
	import colorMixin from '../../mixins/colorMixin';
	import Base from '../../extends/Base.vue';
	export default {
	  name: 'Button',
	  mixins: [colorMixin],
	  extends: Base,
	  props: {
	    /**
	    * The size of the button
	    * `small, normal, large`
	    */
	    size: {
	      default: 'normal'
	    },
	    /**
	    * Add custom click actions.
	    **/
	    onCustomClick: {
	      default: () => () => null,
	    },
	  },
	  /* ... */
	}
	</script>
```
- - - -
#### 示例和readme文件的使用
vue styleguidist会在组件的文件夹中寻找`Readme.md`文件和`ComponentName.md`文件并且展示他们。任何含有`vue`,`js`,`jsx`和`JavaScript`的代码块都会被渲染为一个交互式的相应的代码块。
```markdown
	Vue component example:
	
	```jsx
	    <Button size="large">Push Me</Button>
	```
	
	没有代码标记
	
	```
	<Button size="large">Push Me</Button>
	```
	
	你可以通过添加noeditor修饰语来禁用一个编辑
	
	```jsx noeditor
	<Button>Push Me</Button>
	```
	
	如果要给代码添加高亮效果可以添加static修饰语
	
	```jsx static
	<Button>Push Me</Button>
	```
	
  你也可以通过以下两个方式初始化vue来构建全面的例子
	
	1. 创建一个vue实例
	
	```js
	const names = require('dog-names').all;
	
	new Vue({
	  data(){
	    return {
	      list: names
	    }
	  },
	  template: `
	    <div>
	      <RandomButton :variants="list" />
	    </div>
	  `
	})
	```
	
  2.带有vue标签的单文件（支持< style scoped >）  
	
	```vue
	  <template>
	    <div class="wrapper">
	      <Button id="dog-name-button" @click.native="pushButton">Push Me</Button>
	      <hr />
	      <p class="text-name">Next Dog Name: {{ dogName }}</p>
	    </div>
	  </template>
	
	  <script>
	    const dogNames = require('dog-names').all;
	
	    // You can also use 'exports.default = {}' style module exports.
	    export default {
	      data() {
	        return { numClicks: 0, dogName: dogNames[0] };
	      },
	      methods: {
	        pushButton() {
	          this.numClicks += 1;
	          this.dogName = dogNames[this.numClicks];
	        }
	      }
	    }
	  </script>
	
	  <style scoped>
	    .wrapper {
	      background: blue;
	    }
	    .text-name {
	      color: red;
	    }
	  </style>
	```
	
  其他语言只会按照高亮源代码的形式渲染，不会解析成组件：
	
	```html
	<Button size="large">Push Me</Button>
	```
	
	Any [Markdown](http://daringfireball.net/projects/markdown/) is **allowed** _here_.
```
> 你可以通过[getExampleFilename](https://github.com/vue-styleguidist/vue-styleguidist/blob/master/docs/Configuration.md#getexamplefilename)选项配置示例文件的名字。  
你也可以在vue文件中添加[自定义代码块](https://vue-loader.vuejs.org/en/configurations/custom-blocks.html)`<docs></docs>`，这样vue styleguidist就可以构建readme了。参考这个[例子](https://github.com/vue-styleguidist/vue-styleguidist/blob/master/examples/basic/src/components/Button/Button.vue#L85)  

- - - -
#### 通过doclet标签添加额外的例子
使用`@example`doclet标签可以将额外的例子与组件联系起来
下面这个组件就会从`extra.examples.md`文件中加载一个例子
```js
	/**
	 * Component is described here.
	 *
	 * @example ./extra.examples.md
	 */
	export default {
	  name: 'Button'
	  // ...
	}
```
> 如果[skipComponentsWithoutExample](https://github.com/vue-styleguidist/vue-styleguidist/blob/master/docs/Configuration.md#skipcomponentswithoutexample)配置为true的话，你需要一个基本的示例文件（像`Readme.md`）  
  
- - - -
#### 公共方法
默认情况下你组件中包含的方法都是私有的不会暴露出来，如果有方法是公共的话你需要使用`@public` 来标记。
```js
	/**
	 * Insert text at cursor position.
	 *
	 * @param {string} text
	 * @public
	 */
	insertAtCursor(text) {
	  // ...
	}
```
- - - -
#### 忽略props
默认情况下，组件中的所有props都被识别成公共的，在一些少数情况中你可能希望从文档中删除一些prop但是源码中仍然保留，这种情况下你可以使用`@ignore`标签来将它从文档中删除。
```js
  props: {
    /**
    * @ignore
    */
    color: {
      type: String,
      default: '#333'
    }
```
- - - -
#### 使用JSDoc标签
你可以在components，props，methods中使用这些[JSDoc](http://usejsdoc.org/)标签
* [@deprecated](http://usejsdoc.org/tags-deprecated.html)
* [@see, @link](http://usejsdoc.org/tags-see.html)
* [@author](http://usejsdoc.org/tags-author.html)
* [@since](http://usejsdoc.org/tags-since.html)
* [@version](http://usejsdoc.org/tags-version.html)
methods可以使用这些
* [@param, @arg, @argument](http://usejsdoc.org/tags-param.html)
events
* [@event, @type](http://usejsdoc.org/tags-event.html)
v-model
* @model
```js
	<template>
	  <!-- -->
	</template>
	
	<script>
	
	/**
	* The only true button.
	* @version 1.0.1
	*/
	export default {
	  name: 'Button',
	  props: {
	    /**
	    * The color for the button.
	    * @see See [Wikipedia](https://en.wikipedia.org/wiki/Web_colors#HTML_color_names)
	    * @see See [MDN](https://developer.mozilla.org/en-US/docs/Web/CSS/color_value) for a list of color names
	    */
	    color: {
	      type: String,
	      default: '#333'
	    },
	    /**
	    * The size of the button
	    * `small, normal, large`
	    * @since Version 1.0.1
	    */
	    size: {
	      type: String,
	      default: 'normal'
	    },
	    /**
	    * Gets called when the user clicks on the button
	    */
	    onClick: {
	      type:Function,
	      default: (event) => {
	        console.log('You have clicked me!', event.target);
	      }
	    }
	  },
	  methods: {
	    /**
	     * Gets called when the user clicks on the button
	     *
	     * @param {SyntheticEvent} event The react `SyntheticEvent`
	     * @param {Number} num Numbers of examples
	     */
	    launch(event, num){
	      /* ... */
	    },
	    // ...
	    ignoreMethod(){
	      /**
	      * Success event.
	      *
	      * @event success
	      * @type {object}
	      */
	      this.$emit('success', {})
	    }
	  },
	  /* ... */
	}
	</script>
```

- - - -
### 其他
* [配置](https://github.com/vue-styleguidist/vue-styleguidist/blob/master/docs/Configuration.md)
* [构建命令和配置](https://github.com/vue-styleguidist/vue-styleguidist/blob/master/docs/CLI.md)
* [Node API](https://github.com/vue-styleguidist/vue-styleguidist/blob/master/docs/API.md)






#前端/VUE/工具