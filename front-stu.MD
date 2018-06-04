#常见基本面试问题和解答
1.下面的代码将输出到控制台的是什么，为什么？
    ```
    (function(){
        var a = b = 3;
    })();
    console.log("a defined? " + (typeof a !== 'undefined'));
    console.log("b defined? " + (typeof b !== 'undefined'));
    ```
答案：a defined? false  
      b defined? true  
解析：立即执行函数内代码，实际上可以写为。   ```b = 3; var a = 2; ```  
     
由于在非严格模式下，b=3 定义的b是全局变量。而 var a = 2 定义的a是局部变量。
所以 在函数体外，b 依然有作用域，而 a 没有。

2.下面的代码将输出到控制台的是什么？，为什么？
    ```
    var myObject = {
        foo: "bar",
        func: function() {
            var self = this;
            console.log("outer func: this.foo = " + this.foo);
            console.log("outer func: self.foo = " + self.foo);
            (function() {
                console.log("inner func: this.foo = " + this.foo);
                console.log("inner func: self.foo = " + self.foo);
            }());
        }
    };
    myObject.func();
    ```
    结果：outer func ：this.foo = bar
          outer func : self.too = bar
          inner func : this.foo = undefined
          inner func : selt.too = bar
    解析：
**this 对象是运行时基于函数的执行环境绑定的；在全局函数中,this等于window,而当函数被作为某个对象的方法调用时，this等于那个对象。《Javascript高级程序设计》**  
**每个函数被调用时，其活动对象都会自动取得两个特殊变量：this和arguments。内部函数在搜索这两个变量时，只会搜索到其活动对象为止，因此永远不可能直接访问外部函数中的这两个变量。  《Javascript高级程序设计》**
       
根据这两句话可知，func 内的 this 是指向 myObject 的。但是由于 func 内部还有一个函数体，此函数体内部的 this 是指向 widow的 所以结果是  undefined。

3.在JavaScript源文件的开头包含'use strict'的意义和有什么好处？  
**'use strict' 是一种在运行时自动执行更严格的JavaScript代码解析和错误处理的方法。如果代码错误被忽略或失败，将会产生错误或抛出异常。**  

* **使调试更容易。**  
*  **防止意外全局**。非严格模式下，将值赋给未生明的变量会自动创建一个具有该名称的全局变量。在严格模式下，会报错。  
* **消除隐藏威胁。** 非严格模式下，对null或undefined的这个值的引用会自动强制到全局。在严格模式下，引用null或undefined的这个值会引发错误。  
* **不允许重复的参数值。**严格模式在检测到重复的函数参数时会报错。  
* **使eval()更安全。** 正常模式下，Javascript语言有两种变量作用域（scope）：全局作用域和函数作用域。严格模式创设了第三种作用域：eval作用域。正常模式下，eval语句的作用域，取决于它处于全局作用域，还是处于函数作用域。严格模式下，eval语句本身就是一个作用域，不再能够生成全局变量了，它所生成的变量只能用于eval内部。  
* **禁止this关键字指向全局对象。**  
* **抛出无效的使用错误的删除符。**删除操作符（用于从对象中删除属性）不能用于对象的不可配置属性。当试图删除一个不可配置的属性时，非严格代码将自动失败，而在这种情况下，严格模式会引发错误。  

4.下面的代码将输出到控制台的是什么？，为什么？
    ```
    var arr1 = "john".split('');
    var arr2 = arr1.reverse();
    var arr3 = "jones".split('');
    arr2.push(arr3);
    console.log("array 1: length=" + arr1.length + " last=" + arr1.slice(-1));
    console.log("array 2: length=" + arr2.length + " last=" + arr2.slice(-1));
    ```
结果：
"array 1: length=5 last=j,o,n,e,s"  
"array 2: length=5 last=j,o,n,e,s"  
解析：arr2 和 arr1 指向的是同一个对象，所以 改变 arr2的值的同时，arr1 也会改变。

* 调用数组对象的 reverse() 方法不仅以相反的顺序返回数组，它还颠倒了数组本身的顺序。
* reverse()方法返回对数组本身的引用。因此，arr2仅仅是对arr1的引用，因此，当对arr2做任何事情是，arr1也会受到影响，因为arr1和arr2只是对同一个对象的引用。
   
5.写一个sum方法，当使用下面的语法调用时它将正常工作。
```
console.log(sum(2,3)); // Outputs 5
console.log(sum(2)(3)); // Outputs 5
```
