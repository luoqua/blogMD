#js基础对象属性方法
## Object.keys()  
  Object.keys()方法会返回一个由一个给定对象的自身可枚举属性组成的数组，数组中属性名的排列顺序和使用 for...in 循环遍历改对象时返回的顺序一致(两者的主要区别是一个for-in 循环还会枚举器原型链上的属性)
```
//examples
// simple array
var arr = ['a', 'b', 'c'];
console.log(Object.keys(arr)); // console: ['0', '1', '2']
// array like object
var obj = { 0: 'a', 1: 'b', 2: 'c' };
console.log(Object.keys(obj)); // console: ['0', '1', '2']
``` 
## require.context
你可以用require.context()来创建自己的上下文。这个函数接受三个参数：
+ directory：说明需要检索的目录
+ useSubdirectories：是否检索子目录
+ regExp: 匹配文件的正则表达式  
    该函数返回一个require函数，包含了3个参数： resolve, keys, id.
+ resolve 一个返回模块请求的id的函数
+ keys 一个以数组的形式返回所有模块请求的函数，可以结合 map, foreach, 函数进行批量require();  
如果你想引入文件目录下所有符合匹配模式的文件，那么这个方法是很有用的.
    ```
    // examples: 
    function importAll (r) {
      r.keys().forEach(r);
    }

    importAll(require.context('../components/', true, /\.js$/));
    ```

## for...of
es6新加的特效，可以循环遍历数组元素;  
与 for...in 和Array.forEach()的区别：
1、for...in 是对普通对象的遍历，不仅会遍历数组元素，甚至还会遍历其自定义属性或者原型链上面的属性。
2、forEach() 方法，不能使用 break, 中断循环，也不能用return 语句返回到外层函数。
for...in 可以完美解决上面两个方法的问题。


## Object.entries()
该方法返回一个给定对象自身可枚举属性的键值对数组，其排列与使用for...in 循环遍历该对象时返回的顺序一致(区别在于for-in 循环也枚举原型链中的属性)
    ```
    //examples:  
    const obj = { foo: 'bar', baz: 42 };
    console.log(Object.entries(obj)); // [ ['foo', 'bar'], ['baz', 42] ]

    // array like object
    const obj = { 0: 'a', 1: 'b', 2: 'c' };
    console.log(Object.entries(obj)); // [ ['0', 'a'], ['1', 'b'], ['2', 'c'] ]

    // array like object with random key ordering
    const anObj = { 100: 'a', 2: 'b', 7: 'c' };
    console.log(Object.entries(anObj)); // [ ['2', 'b'], ['7', 'c'], ['100', 'a'] ]

    // getFoo is property which isn't enumerable
    const myObj = Object.create({}, { getFoo: { value() { return this.foo; } } });
    myObj.foo = 'bar';
    console.log(Object.entries(myObj)); // [ ['foo', 'bar'] ]

    // non-object argument will be coerced to an object
    console.log(Object.entries('foo')); // [ ['0', 'f'], ['1', 'o'], ['2', 'o'] ]

    // iterate through key-value gracefully
    const obj = { a: 5, b: 7, c: 9 };
    for (const [key, value] of Object.entries(obj)) {
      console.log(`${key} ${value}`); // "a 5", "b 7", "c 9"
    }
    ```

将Object转换为Map
new Map() 构造函数接受一个可迭代的entries。借助Object.entries方法你可以很容易的将Object转换为Map:
    ```
    var obj = { foo: "bar", baz: 42 }; 
    var map = new Map(Object.entries(obj));
    console.log(map); // Map { foo: "bar", baz: 42 }
    ```
    Polyfill
你可以使用下面列出的简易 polyfill。
    ```
    if (!Object.entries)
      Object.entries = function( obj ){
        var ownProps = Object.keys( obj ),
            i = ownProps.length,
            resArray = new Array(i); // preallocate the Array
        while (i--)
          resArray[i] = [ownProps[i], obj[ownProps[i]]];
        
        return resArray;
      };
    ```
转载：https://segmentfault.com/a/1190000011616239#articleHeader5
 
## unshift() 方法  
    unshift() 方法可向数组的开头添加一个或更多元素，并返回新的长度。

## location.replace()  
replace() 方法可用一个新文档取代当前文档。可用于页面跳转。和location.href的区别在于，replace() 方法不会在 History 对象中生成一个新的记录。当使用该方法时，新的 URL 将覆盖 History 对象中的当前记录。 而后者会在浏览器的历史浏览记录添加一条记录。

## location.pathname  
pathname 属性是一个可读可写的字符串，可设置或返回当前 URL 的路径部分
    ```
    //examples
    //假设当前的 URL 是: http://example.com:1234/test/test.htm#part2：
    document.write(location.pathname);  ///test/test.htm
    ```

## location.hash  
hash 属性是一个可读可写的字符串，该字符串是 URL 的锚部分（从 # 号开始的部分）。
    ```
    //examples
    //假设当前的 URL 是: http://example.com:1234/test.htm#part2：
    document.write(location.hash);//#part2
    ```

## window.top  
该属性返回对一个顶级窗口的只读引用。如果窗口本身就是一个顶级窗口，top 属性存放对窗口自身的引用。如果窗口是一个框架，那么 top 属性引用包含框架的顶层窗口。跳出当前框架

## in 运算符  
如果指定的属性在指定的对象或其原型链中，则in 运算符返回true。
    ```
    //examples
    // 数组
    var trees = new Array("redwood", "bay", "cedar", "oak", "maple");
    0 in trees        // 返回true
    3 in trees        // 返回true
    6 in trees        // 返回false
    "bay" in trees    // 返回false (必须使用索引号,而不是数组元素的值)
    ```
## charAt() 方法  
charAt() 方法可返回指定位置的字符。字符串中第一个字符的下标是 0。如果参数 index 不在 0 与 string.length 之间，该方法将返回一个空字符串。

## slice(start,end) 方法  
slice()方法可从已有的数组中返回选定的元素
参数：start 规定从何处开始选，如果是负数，则从数组尾部开始算位置
  end 规定从何处结束所取。该参数是数组片段结束处的数组下标。如果没有指定该参数，则包含从start开始到数组结束的所有元素。
返回值:返回一个新的数组，包含从 start 到 end (部包括该元素)的原数组中的元素。  
*注意：该方法不会修改数组，而是返回一个子数组。如果想删除数组中的一段元素，应该使用Array.splice()*

## Document.documentElement  
会返回文档对象（document）的根元素的只读属性(如Html文档的<html>元素)    
与document.body的区别
document.body 会返回 Html 文档中的body节点。即body
注意：在获取浏览器滚动器距离时、窗口大小时，两者在不同浏览器下会有区别。
兼容解决方案。

## Js 中双感叹号的作用
!!一般用于将后面的表达式强制转化为布尔类型。js是弱类型语言，所以可以通过双感叹号，将其转化为boolean类型。  
布尔类型的转化，js约定的规则为：
1、false、undefined、null、0、""均为false
2、true、1、"string"、[Object] 为true

## js substring(start, end)的用法
substring() 方法用于提取字符串中介于两个指定下标之间的字符。substring() 方法返回的子串包括 start 处的字符，但不包括 stop 处的字符。  
重要事项：与 slice() 和 substr() 方法不同的是，substring() 不接受负的参数。

## js substr()的 用法
substr(start, length)方法可在字符串中抽取从start下标开始的指定数目的字符。
其中length必须是数值，如果省略该参数，那么返回从字符串的开始位置到结尾的字串。

## js random() 方法
Math.random() 方法可返回介于 0 ~ 1 之间的一个随机数。

## document.createElement(nodename)
参数：元素节点的名称
返回：新建的元素节点

## document.innerHTML = Node
向元素节点中插入节点。注意HTML要大写。  
与document.innerText的区别：
1、innerHTML指的是从对象的起始位置到终止位置的全部内容，包括Html标签
2、innerText 指的是从起始位置到终止位置的内容，但它去除html标签。
同时，innerHTML 是所有浏览器都支持的，innerText 是IE浏览器和chrome浏览器支持的，Firefox浏览器不支持。所以尽量多用innerHTML。少用innerText

## transform 不同浏览器厂商前缀，js驼峰写法
chrome:webkitTransform  => css: -webkit-transform  
o:OTransform => css:-o-transform  
ms:msTransform => css:-ms-transform  
Moz:MozTransform => css:-Mozva-transform  

## 常见获取dom对象的方法总结和区别(document.getElementById...)
document.getElementById、document.getElementsByTagName、document.getElementsByClassName、querySelector()、querySelectorAll()等选择器的使用和区别

##ES6-对象的扩展-Object.keys()，Object.values()，Object.entries()
https://blog.csdn.net/ww430430/article/details/78716817

## JavaScript indexOf() 方法
stringObject.indexOf(searchvalue,fromindex)方法可返回某个指定的字符串值在字符串中首次出现的位置。  
searchvalue:检索的字符串;必需
fromindex：可选的整数参数。规定在字符串中开始检索的位置。它的合法取值是 0 到 stringObject.length - 1。
注意：如果要检索的字符串值没有出现，则该方法返回 -1。