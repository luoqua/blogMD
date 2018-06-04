#vue.js 2.0 源码解析（一）

##new Vue({})的实现

1. 首先看 \\src\\core\\instance\\index.js文件
```
    import { initMixin } from './init'             //初始化模块
    import { stateMixin } from './state'            
    import { renderMixin } from './render'
    import { eventsMixin } from './events'
    import { lifecycleMixin } from './lifecycle'
    import { warn } from '../util/index'
    
    // 构造函数 Vue 
    function Vue (options) {
      if (process.env.NODE_ENV !== 'production' &&      //判断是否开发环境已以及是否已经 new Vue(实例化)
        !(this instanceof Vue)
      ) {
        warn('Vue is a constructor and should be called with the `new` keyword')
      }
      this._init(options)    //初始化 vue 实例。
    }

    initMixin(Vue)
    stateMixin(Vue)
    eventsMixin(Vue)
    lifecycleMixin(Vue)
    renderMixin(Vue)

    export default Vue
```
可以看到 index.js 文件，除了构造函数 Vue 外。导入了多个模块。我们一个个分析。

* init.js      //初始化模块
init.js中通过 prototype 原型链 为 Vue 添加了多个方法。
initMixin, initInternalComponents,resolveConstructorOptions。
1.initMixin
Vue 2.0中使用了大量的flow类型检查，所以会有以下这些写法。
    ```
    export function initMixin (Vue: Class<Component>) {}
    Vue.prototype._init = function (options?: Object) {}
    const vm: Component = this;
    ```
通过 Vue：Class 这种形式，flow会自动对当前对象进行类型检查，这里的写法 可以理解为，检查 Vue 是否是 class 类型。

istanbul 代码覆盖率工具 istanbul。
/* istanbul ignore if */ 注释代码含义为计算覆盖率的时候会被忽略
具体可移步 http://www.ruanyifeng.com/blog/2015/06/istanbul.html

三个if
1、config.performance //是否做性能检测，porfamance 是一个性能检测工具，其中mark是做用于做标记和清除标记，供用户自定义统计一些数据，比如某函数运行耗时等( ..//util//pref).
2、合并 options(// merge options)
3、是否做代理拦截(initProxy(vm))


    ```
    initLifecycle(vm)  //  初始化生命周期
    initEvents(vm)     //事件监听初始化；初始化事件系统
    initRender(vm)    //渲染页面
    callHook(vm, 'beforeCreate') //回调钩子beforeCreate;执行beforeCreate钩子
    initInjections(vm) // 初始化注入
    initState(vm)    初始化状态（包括prop/data/computed/method/watch）
    callHook(vm, 'created')    执行created钩子  
     ```

+ callHook (lifecycle.js) 生命周期钩子，会遍历vue的所有生命周期，从 beforeCreate -> created -> beforeMount -> mounted -> beforeUpdate -> updated 。
只要在 实例化时 options 中传入对应生命周期的钩子函数，就可以在 callhook中执行钩子。
    dep.js //观察者模式的实现，维护一个被观察的对象 dep,并且可以在 dep 上绑定多个指令。
    ```
    export function pushTarget (_target: ?Watcher) {
      if (Dep.target) targetStack.push(Dep.target)
      Dep.target = _target
    }
    ```
    其中 _target: ?Watcher 中的 “ :?Watcher ”是 flow 类型检查中的也许类型，代表的是类型可自定义，也就是值的部份除了定义的类型，也可以是null或undefined。
    另外：“?: string” 问号放前面的情况，代表的是属性为可自定义的(可有可无)，但一定是字符串类型。
    ```
    import type Watcher from './watcher'
    subs: Array<Watcher>;
    ```
    Flow具备有像TypeScript语言中，预先定义对象类型的作用。上面代码的都是这个组件中预先定义的类型，这些类型可以再套用到不同的代码文档之中。
    这里的 ``Array<Watcher>`` 使用了上面定义的 watcher 类型，代表 subs 是一个数组，并且里面的成员是 Watcher 类型，是个对象的结构。
+  initLifecycle(lifecycle.js) 初始化生命周期。
    ```
    //自下而上循环查找定位第一个非抽象的父实例或父组件
    let parent = options.parent     //父实例或父组件
    if (parent && !options.abstract) {
      while (parent.$options.abstract && parent.$parent) {
        parent = parent.$parent
      }
      parent.$children.push(vm)
    }
    
    ```
    
> <keep-alive> 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。和 <transition> 相似，<keep-alive> 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在父组件链中。

```
vm.$parent = parent
  vm.$root = parent ? parent.$root : vm

  vm.$children = []
  vm.$refs = {}

  vm._watcher = null
  vm._inactive = null
  vm._directInactive = false
  vm._isMounted = false
  vm._isDestroyed = false
  vm._isBeingDestroyed = false
```

<table>
  <thead>
    <tr>
        <th>名称</th>
        <th>说明</th>
    </tr>
  </thead>
  <tbody>
    <tr>
        <td>[$parent](https://cn.vuejs.org/v2/api/#parent)</td>
        <td>指定已创建的实例之父实例，在两者之间建立父子关系。子实例可以用 this.$parent 访问父实例，子实例被推入父实例的 $children 数组中。</td>
    </tr>
     <tr>
        <td>[$root](https://cn.vuejs.org/v2/api/#vm-root)</td>
        <td>当前组件树的根 Vue 实例。如果当前实例没有父实例，此实例将会是其自己。</td>
    </tr>
     <tr>
        <td>[$children](https://cn.vuejs.org/v2/api/#vm-children)</td>
        <td>当前实例的直接子组件。需要注意 $children 并不保证顺序，也不是响应式的。如果你发现自己正在尝试使用 $children 来进行数据绑定，考虑使用一个数组配合 v-for 来生成子组件，并且使用 Array 作为真正的来源。</td>
    </tr>
     <tr>
        <td>[$refs](https://cn.vuejs.org/v2/api/#vm-refs)</td>
        <td>一个对象，持有注册过 ref 特性 的所有 DOM 元素和组件实例。</td>
    </tr>
     <tr>
        <td>[_watcher](https://cn.vuejs.org/v2/guide/reactivity.html)</td>
        <td>组件实例相应的 watcher 实例对象。</td>
    </tr>
    <tr>
        <td>[_inactive]</td>
        <td>表示keep-alive中组件状态，如被激活，该值为false,反之为true</td>
    </tr>
    <tr>
        <td>[_directInactive]</td>
        <td>也是表示keep-alive中组件状态的属性。</td>
    </tr>
    <tr>
        <td>[_isMounted]</td>
        <td>当前实例是否完成挂载(对应生命周期图示中的mounted)。</td>
    </tr>
    <tr>
        <td>[_isDestroyed]</td>
        <td>当前实例是否已经被销毁(对应生命周期图示中的destroyed)。</td>
    </tr>
    <tr>
        <td>[_isBeingDestroyed]</td>
        <td>当前实例是否正在被销毁,还没有销毁完成(介于生命周期图示中deforeDestroy和destroyed之间)。</td>
    </tr>
  </tbody>  
</table>
<img src="https://cn.vuejs.org/images/lifecycle.png">

+ initEvents( events.js )
```
//eventsMixin(function)

//$on 监听当前实例上的自定义事件。事件可以由vm.$emit触发。回调函数会接收所有传入事件触发函数的额外参数。
(vm._events[event] || (vm._events[event] = [])).push(fn)
// optimize hook:event cost by using a boolean flag marked at registration
// instead of a hash lookup
if (hookRE.test(event)) {
  vm._hasHookEvent = true
}
```
> optimize hook:event cost by using a boolean flag marked at registration instead of a hash lookup
> 通过使用标记为注册而不是散列查找的布尔标记来优化钩子 hook: 事件成本。
如果 event 中包含 hook:, 修改 vm._hasHookEvent 的状态。如果 _hasHookEvent 为 true,
```
export function callHook (vm: Component, hook: string) {
  // #7573 disable dep collection when invoking lifecycle hooks
  //  beforeCreate:function(){
  //    this.$on("hook:created",function(){
  //      this.fetchData()
  //    })
  //  },
  const handlers = vm.$options[hook]
  if (handlers) {
    for (let i = 0, j = handlers.length; i < j; i++) {
      try {
        handlers[i].call(vm)
      } catch (e) {
        handleError(e, vm, `${hook} hook`)
      }
    }
  }
  if (vm._hasHookEvent) {
    vm.$emit('hook:' + hook)
  }
  ...
}

```
在 callHook 函数中 会对 绑定了生命周期的事件函数，做_hasHookEvent 状态判断，并且直接emit执行，这样在触发生命周期各钩子类的函数的时候，会触发如
hook:created 事件，从而优化了 通过散列查找钩子函数的 事件成本，减少开销。


* $off
    * 移除自定义事件监听器。

    * 如果没有提供参数，则移除所有的事件监听器；

    * 如果只提供了事件，则移除该事件所有的监听器；

    * 如果同时提供了事件与回调，则只移除这个回调的监听器。
    ```
    Vue.prototype.$off = function (event?: string | Array<string>, fn?: Function): Component {
        const vm: Component = this
        // all
        // 如果没有提供参数，移除所有事件监听器
        if (!arguments.length) {
          vm._events = Object.create(null)
          return vm
        }
        // array of events
        /*如果event是数组则递归注销事件*/
        if (Array.isArray(event)) {
          for (let i = 0, l = event.length; i < l; i++) {
            this.$off(event[i], fn)
          }
          return vm
        }
        // specific event
        const cbs = vm._events[event]
        //不存在事件直接返回
        if (!cbs) {
          return vm
        }
        //为提供回调，将事件的回调设为null
        if (!fn) {
          vm._events[event] = null
          return vm
        }
        //同时提供了事件与回调。只移除这个回调的监听器。
        if (fn) {
          // specific handler
          let cb
          let i = cbs.length
          while (i--) {
            cb = cbs[i]
            if (cb === fn || cb.fn === fn) {    //包含$on,$once
              cbs.splice(i, 1)
              break
            }
          }
        }
        return vm
      }
    ```

+ initRender(render.js)
  ```
    defineReactive(vm, '$attrs', parentData && parentData.attrs || emptyObject, () => {
      !isUpdatingChildComponent && warn(`$attrs is readonly.`, vm)
    }, true)
  ```
>$attrs { [key: string]: string }  
>包含了父作用域中不作为 prop 被识别 (且获取) 的特性绑定 (class 和 style 除外)。当一个组件没有声明任何 prop 时，这里会包含所有父作用域的绑定 (class 和 style 除外)，并且可以通过 v-bind="$attrs" 传入内部组件——在创建高级别的组件时非常有用。  
>vm.$listeners { [key: string]: Function | Array<Function> }  
>包含了父作用域中的 (不含 .native 修饰器的) v-on 事件监听器。它可以通过 v-on="$listeners" 传入内部组件——在创建更高层次的组件时非常有用。
  + defineReactive()函数( \\core\observer\\index.js)在对象上定义动态属性 

  由于 defineReactive 中用到了 Dep() 类，所以让我们先来看Dep是怎么实现的
  ......

## next-tick.js 源码阅读
```
/* @flow */
/* globals MessageChannel */

import { noop } from 'shared/util'
import { handleError } from './error'
import { isIOS, isNative } from './env'

const callbacks = []
let pending = false

// 处理callbacks的函数
function flushCallbacks () {
  pending = false     // 可以触发timeFunc
  const copies = callbacks.slice(0)     // 复制callback
  callbacks.length = 0                  // 清空callback
  for (let i = 0; i < copies.length; i++) {
    copies[i]()                         // 触发callback回调函数
  }
}

// Here we have async deferring wrappers using both microtasks and (macro) tasks.
// In < 2.4 we used microtasks everywhere, but there are some scenarios where
// microtasks have too high a priority and fire in between supposedly
// sequential events (e.g. #4521, #6690) or even between bubbling of the same
// event (#6566). However, using (macro) tasks everywhere also has subtle problems
// when state is changed right before repaint (e.g. #6813, out-in transitions).
// Here we use microtask by default, but expose a way to force (macro) task when
// needed (e.g. in event handlers attached by v-on).
// 在这里，我们使用了微任务(microtasks) 和宏任务((macro) tasks)的异步延迟包装器
// 在 version < 2.4中，我们全部用的是微任务，但是也有一些场景优先级太高，在顺序事件(例如 #4521, #6690)
// 或者在冒泡同一事件中出现这个问题。然而，全部使用（宏）任务也会有微妙的问题。所以，在重绘之前状态被正确改变的情况下
// 我们默认使用微任务，但是在需要宏任务的时候暴露一种方法来强制执行宏任务。（例如在事件处理程序中附加的v-on）
let microTimerFunc  // 定义微任务时间函数
let macroTimerFunc  // 定义宏任务时间函数
let useMacroTask = false          // 是否使用宏任务

// Determine (macro) task defer implementation.
// Technically setImmediate should be the ideal choice, but it's only available
// in IE. The only polyfill that consistently queues the callback after all DOM
// events triggered in the same loop is by using MessageChannel.
// 确定（宏）任务延迟执行。
// setImmediate技术也是一个理想的选择，但是它仅仅在Ie下可用。它唯一的polyfill就是在同一个事件循环(event loop)中触发的所有DOM事件之后
// 回调一致的队列
/* istanbul ignore if */
if (typeof setImmediate !== 'undefined' && isNative(setImmediate)) {
  macroTimerFunc = () => {
    setImmediate(flushCallbacks)
  }
} else if (typeof MessageChannel !== 'undefined' && (
  //  polyfill
  isNative(MessageChannel) ||
  // PhantomJS
  MessageChannel.toString() === '[object MessageChannelConstructor]'
)) {
  const channel = new MessageChannel()
  const port = channel.port2
  channel.port1.onmessage = flushCallbacks
  macroTimerFunc = () => {
    port.postMessage(1)
  }
} else {
  /* istanbul ignore next */
  macroTimerFunc = () => {
    setTimeout(flushCallbacks, 0)
  }
}

// Determine microtask defer implementation.
// 确定微任务延迟执行
/* istanbul ignore next, $flow-disable-line */
// 如果支持Promise，使用Promise实现
if (typeof Promise !== 'undefined' && isNative(Promise)) {
  const p = Promise.resolve()
  microTimerFunc = () => {
    p.then(flushCallbacks)
    // in problematic UIWebViews, Promise.then doesn't completely break, but
    // it can get stuck in a weird state where callbacks are pushed into the
    // microtask queue but the queue isn't being flushed, until the browser
    // needs to do some other work, e.g. handle a timer. Therefore we can
    // "force" the microtask queue to be flushed by adding an empty timer.
    // 在有问题的UIWebVIEW中，Promise.then并没有完全崩溃，但是它可能陷入一种怪异的状态，
    // 其中回调被推到微任务队列中，但是队列没有被刷新，直到浏览器需要做一些其他的工作，
    // 例如处理计时器。因此，我们可以通过添加一个空计时器来“强制”微任务队列被刷新。
    if (isIOS) setTimeout(noop)
  }
} else {
  // fallback to macro
  microTimerFunc = macroTimerFunc
}

/**
 * Wrap a function so that if any code inside triggers state change,
 * the changes are queued using a (macro) task instead of a microtask.
 * 包装一个函数，如果内部的任何代码触发状态改变，
 * 使用宏任务，而不是微任务去处理状态改变的队列。即是上面说的暴露的一个强制执行宏任务的方法。
 */
export function withMacroTask (fn: Function): Function {
  return fn._withTask || (fn._withTask = function () {
    useMacroTask = true
    const res = fn.apply(null, arguments)
    useMacroTask = false
    return res
  })
}

export function nextTick (cb?: Function, ctx?: Object) {
  let _resolve
  callbacks.push(() => {
    if (cb) {
      try {
        cb.call(ctx)
      } catch (e) {
        handleError(e, ctx, 'nextTick')
      }
    } else if (_resolve) {
      _resolve(ctx)
    }
  })
  if (!pending) {
    pending = true
    if (useMacroTask) {
      macroTimerFunc()
    } else {
      microTimerFunc()
    }
  }
  // $flow-disable-line
  if (!cb && typeof Promise !== 'undefined') {
    return new Promise(resolve => {
      _resolve = resolve
    })
  }
}

```