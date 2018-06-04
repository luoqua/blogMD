#es6入坑之路
+ Promise的学习
    + Promise.resolve()
        有时需要将现有对象转为 Promise 对象，Promise.resolve方法就起到这个作用。Promise.resolve等价于下面的写法。  
        ```
        Promise.resolve('foo')
        //等价于
        new Promise(resolve => resolve('foo'))
        Promise.resolve('foo').then( res => console.log(res) ) // 'foo'
        ```
        1、参数是一个实例时，不做任何修改，直接返回实例
        2、参数是一个 thenable 对象
            thenable对象是指具有then方法的对象，比如下面这个对象
            ```
            let thenable = {
                then:function(resolve,reject){
                    resolve(42);
                }
            }
            ```
            path.resolve 方法会将这个对象转为Promise对象，然后就立即执行thenable对象的then方法.
            ```
            let p1 = Promise.resolve(thenable);

            //等价于
            let p1 = new Promise(function(resolve,reject){
                thenable.then(resolve,reject);
            })

            p1.then(function(value){
                console.log(value);   //42
            })
            ```
        3、参数不是具有then方法的对象，或根本就不是对象  
        如果参数是一个原始值，或者是一个不具有then方法的对象，则Promise.resolve方法返回一个新的 Promise 对象，状态为resolved。  
        ```
        const p = Promise.resolve('Hello');

        p.then(function (s){
          console.log(s)
        });
        // Hello
        ```
        4、不带有任何参数  
        Promise.resolve方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。所以，如果希望得到一个 Promise 对象，比较方便的方法就是直接调用Promise.resolve方法。  
        ```
        const p = Promise.resolve();
        //等价于
        const p = new Promise(function(resolve,reject){

            })
        p.then(function () {
          // ...
        });
        ```
        *注意：立即resolve的Promise对象，是在本轮"事件循环（event loop）"的结束时，而不是在下一轮"事件循环"的开始时执行,而setTimeout(fn, 0)是在下一轮“事件循环”开始时执行，*
        ```
        setTimeout( function() {
            console.log('three');
        },0)

        Promise.resolve().then(function(){
            console.log('two');
        })
        console.log('one');

        //one
        //two
        //three


        ```
+ Symbol的学习  
    1. Symbol是es6新增的数据原始类型，与undefined,boolean,String,null,number,Object一样，是js的第七种数据类型。并且通过这种数据类型声明的变量，都是独一无二的。Symbol 类型通过 Symbol 函数生成。
    ```
    let s = Symbol()

    typeof s
    //"symbol"
    ```
    Symbol函数不能用new实例化，因为Symbol值不是对象，所以不能添加对象。Symbol函数可以接受一个字符串作为参数，表示对Symbol实例的描述，主要是为了在控制台显示，或者转为字符串时，比较容易区分。
    ```
    let s1 = Symbol('foo');
    let s2 = Symbol('bar');

    s1 // Symbol(foo)
    s2 // Symbol(bar)

    s1.toString() // "Symbol(foo)"
    s2.toString() // "Symbol(bar)"
    ```