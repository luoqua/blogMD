#JavaScript 中的事件捕获、事件冒泡、事件委托
DOM事件阶段一般分为三个阶段。  
当一个DOM事件被触发是，它不仅仅是单纯地在本身对象上触发一次，而是会经历三个不同的阶段。  
1、捕获阶段：先由文档的根节点document往目标节点触发事件对象，从外向内捕获事件对象。    
2、目标阶段：到达目标节点事件位置，触发事件。  
3、冒泡阶段：再从目标事件位置往document方向回溯，从内向外冒泡事件对象。 
下面结合一个具体实例更能让我们理解DOM的事件机制。 
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Document</title>
    <style type="text/css">
    #wrapDiv, #innerP, #textSpan{
        margin: 5px;padding: 5px;box-sizing: border-box;cursor: default;
    }
    #wrapDiv{
        width: 300px;height: 300px;border: indianred 3px solid;
    }
    #innerP{
        width: 200px;height: 200px;border: hotpink 3px solid;
    }
    #textSpan{
        display: block;width: 100px;height: 100px;border: orange 3px solid;
    }
    </style>
</head>
<body>
     <div id="wrapDiv">wrapDiv
        <p id="innerP">innerP
            <span id="textSpan">textSpan</span>
        </p>
    </div>
    <script>
    var wrapDiv = document.getElementById("wrapDiv");
    var innerP = document.getElementById("innerP");
    var textSpan = document.getElementById("textSpan");

    // 捕获阶段绑定事件
    window.addEventListener("click", function(e){
        console.log("window 捕获", e.target.nodeName, e.currentTarget.nodeName);
    }, true);

    document.addEventListener("click", function(e){
        console.log("document 捕获", e.target.nodeName, e.currentTarget.nodeName);
    }, true);

    document.documentElement.addEventListener("click", function(e){
        console.log("documentElement 捕获", e.target.nodeName, e.currentTarget.nodeName);
    }, true);

    document.body.addEventListener("click", function(e){
        console.log("body 捕获", e.target.nodeName, e.currentTarget.nodeName);
    }, true);

    wrapDiv.addEventListener("click", function(e){
        console.log("wrapDiv 捕获", e.target.nodeName, e.currentTarget.nodeName);
    }, true);

    innerP.addEventListener("click", function(e){
        console.log("innerP 捕获", e.target.nodeName, e.currentTarget.nodeName);
    }, true);

    textSpan.addEventListener("click", function(e){
        console.log("textSpan 捕获", e.target.nodeName, e.currentTarget.nodeName);
    }, true);

    // 冒泡阶段绑定的事件
    window.addEventListener("click", function(e){
        console.log("window 冒泡", e.target.nodeName, e.currentTarget.nodeName);
    }, false);

    document.addEventListener("click", function(e){
        console.log("document 冒泡", e.target.nodeName, e.currentTarget.nodeName);
    }, false);

    document.documentElement.addEventListener("click", function(e){
        console.log("documentElement 冒泡", e.target.nodeName, e.currentTarget.nodeName);
    }, false);

    document.body.addEventListener("click", function(e){
        console.log("body 冒泡", e.target.nodeName, e.currentTarget.nodeName);
    }, false);

    wrapDiv.addEventListener("click", function(e){
        console.log("wrapDiv 冒泡", e.target.nodeName, e.currentTarget.nodeName);
    }, false);

    innerP.addEventListener("click", function(e){
        console.log("innerP 冒泡", e.target.nodeName, e.currentTarget.nodeName);
    }, false);

    textSpan.addEventListener("click", function(e){
        console.log("textSpan 冒泡", e.target.nodeName, e.currentTarget.nodeName);
    }, false);
</script>
</body>
</html>
```
源码来源[事件冒泡、事件捕获和事件委托](https://www.cnblogs.com/Chen-XiaoJun/p/6210987.html)

4、事件委托  
```
    <ul id="ul">
        <li>111</li>
        <li>222</li>
        <li>333</li>
        <li>444</li>
    </ul>
    <script>
        window.onload = function(){
            　　var oUl = document.getElementById("ul1");
            　　oUl.onclick = function(ev){
                　　　　var ev = ev || window.event;
                　　　　var target = ev.target || ev.srcElement;
                　　　　if(target.nodeName.toLowerCase() == 'li'){
        　　　　　　　  alert(target.innerHTML);
                　　　　}
            　　}
        }
   
    </script>
```
* 事件委托的原理是根据浏览器的事件冒泡机制实现的，事件冒泡就是事件从最深的节点开始，然后逐步向上传播事件(包括click，mouseover,mousedown等)。
就如本例中的li标签被点击的时候，通过事件冒泡机制把click事件向上传播，以致触发了ul的点击事件。
在ul的点击事件中，我们用e.target获得事件目标的节点，也就是对应的事件源，这样就实现了我们想要的需求。
* 为什么要用事件委托：  
在JavaScript中，添加到页面上的事件处理程序数量将直接关系到页面的整体运行性能，因为需要不断的与dom节点进行交互，访问dom的次数越多，引起浏览器重绘与重排的次数也就越多，就会延长整个页面的交互就绪时间，这就是为什么性能优化的主要思想之一就是减少DOM操作的原因；如果要用事件委托，就会将所有的操作放到js程序里面，与dom的操作就只需要交互一次，这样就能大大的减少与dom的交互次数，提高性能；

参考：https://www.cnblogs.com/liugang-vip/p/5616484.html
      https://www.cnblogs.com/Chen-XiaoJun/p/6210987.html
      http://baijiahao.baidu.com/s?id=1572908033820978&wfr=spider&for=pc   
