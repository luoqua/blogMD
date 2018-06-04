#Vue.js入坑之路
+ vue.js 中的插件和组件的区别  
    组件一般用 Vue.component() 方法注册  
    插件一般可能包含多个组件，并且需要通过 Vue.use() 全局引入  
    Vue插件的使用 Vue.use(components);其中，Vue.use对于含有install对象属性的插件来说，是直接调用install方法  
```
    //examples
    //目录结构
    //|-components
　　//|-loading
　　//　　|-index.js  导出组件，并且install
　　//　　|-loading.vue   定义Loading组件，这里面基本的style 　//,script中之前讲的methods,data方法都可以使用
    //index.js中的代码：
    import LoadingComponent from '../loading.vue'
    const Loading = {
        install:function(Vue){ //核心部分，在我们使用Vue.use()时，自动调用的是install，而install导出的必须是的组件
　　　　Vue.component('loading',LoadingComponent);
　　  }
    }
    export default Loading
    //main.js 中的代码
    import loading from './components/loading'
　　Vue.use(loading); //调用的是install里面的组件

```
    Vue.use()对于插件是函数的情况来说，是将该函数当成install方法去调用。在调用该方法时会讲vue当成参数传入。  
```
    //examples
    const plugins = vue => vue.component(plugins.name,plugins) 
    Vue.use(plugins)
    //附Vue.use()源码
    Vue.use = function (plugin: Function | Object) {
    const installedPlugins = (this._installedPlugins || (this._installedPlugins = []))
    if (installedPlugins.indexOf(plugin) > -1) {
      return this
    }

    // additional parameters
    const args = toArray(arguments, 1)
    args.unshift(this)
    if (typeof plugin.install === 'function') {
      plugin.install.apply(plugin, args)
    } else if (typeof plugin === 'function') {
      plugin.apply(null, args)
    }
    installedPlugins.push(plugin)
    return this
  }
```