#如何在vue-dev环境下进行代码断点调试。
1、首先在github上将vue2.0的源码拷到本地，进入源码目录。  
2、改变\\scripts\\config.js ，在 genfig 函数中 config对象中添加sourceMap属性.  
**随着代码增多，我们需要对代码进行压缩。代码压缩后进行调bug定位将非常困难，于是引入sourcemap记录压缩前后的位置信息记录，当产生错误时直接定位到未压缩前的位置，将大大的方便我们调试。**
具体可以移步 http://www.ruanyifeng.com/blog/2013/01/javascript_source_map.html
```
function genConfig (name) {
  const opts = builds[name]
  const config = {
    input: opts.entry,
    external: opts.external,
    sourceMap:true,
```
3.重新npm run dev.。在 examples 目录下面，新建页面引用 vue.js 文件，即可进行代码未rollup前的断点调试。

Rollup 是一个 JavaScript 模块打包器，可以将小块代码编译成大块复杂的代码。

http://www.rollupjs.com/