#Vue.js源码学习之vue文件的解析
##前言  
熟悉过vue-cli这一自动构建vue项目的工具的同学应该都知道，在项目搭建完成后会发现，项目目录下面有一种后缀名为.vue的文件，vue文件是一个vue自定义的文件类型，在vue文件中我们可以通过三个大标签script,template,style(分别对应的js、html、css三种常见标签),去编写一个页面。那么，vue是通过什么样的原理去将这样一个包含自定义标签的自定义文件，转化成浏览器可以运行的html语言呢。其实，通过vue源码我们可以发现，主要是src目录下面的sfc目录起得作用。这篇文章就是对于sfc目录的学习。

##SFC
SFC( single-file components )单文件组件(来自官网的翻译)。通过对官网对SFC的介绍我们可以知道。[单文件组件](https://cn.vuejs.org/v2/guide/single-file-components.html#%E4%BB%8B%E7%BB%8D)。vue中的单文件组件主要是为了解决普通的new vue({}) 容器构建vue 项目中会遇到的三个主要问题：  
1、css 不能模块化.即不能使用组件作用域的css。  
2、字符串模板不能高亮(在 HTML 有多行的时候，需要用到丑陋的 \\).  
3、无法进行预处理，比如Pug (formerly Jade) 和 Babel。前者是一个模板引擎，前身是jade。后者是一个javascript编译器，可以将es6、es7的语法转化成目前浏览器可以支持的es5。

##源码介绍( \\src\\sfc\\parser.js)

