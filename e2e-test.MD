#关于vue-cli脚手架自动构建的项目的e2e测试尝试
1. e2e测试介绍  
    之前接触过前端的单元测试，e2e测试是第一次玩，了解过以后，才发现用起来真的很棒。
    e2e测试又叫模拟用户行为的测试，或者端到端的测试。简单来说，e2e测试，是把我们的程序当成一个黑盒子，站在客户的角度，我不需要懂我提的这些功能具体是怎么实现的，我只要在浏览器中看到我想要的功能和效果。e2e测试，可以自动打开浏览器，运行我们要测试的程序，并且收集测试记录。
2. 前期准备  
    通过下载官方的vue-cli，自动构建了webpack+vue的项目，在安装过程中，会有提示是否安装e2e测试的选项，选择yes。这样在项目构建完成以后就能在项目下面找到test目录下的e2e目录。
3. 开始  
    简单看下e2e的目录结构，nightwatch.conf.js和runner.js分别是配置和运行文件。具体编写的测试逻辑写在specs当中， 
    //specs//test.js
    ```
    const devServer = browser.globals.devServerURL
     browser
      .url(devServer)                               //浏览器打开连接
      .waitForElementVisible('#app', 5000)          //等待id="app"的元素加载
      .assert.elementPresent('.hello')              //断言 class="hello" 的元素存在
      .assert.containsText('h1', 'Welcome to Your Vue.js App')  //断言<h1>中的text内为Welcome to Your Vue.js App
      .assert.elementCount('img', 1)                //断言 图片的数量是1
      .end()                                        //结束
    ```