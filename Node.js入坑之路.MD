#Node.js踩坑之路
+ path.resolve()
    该方法将一系列路径或路径段解析为绝对路径。  
    *给定的路径的序列是从右往左被处理的，后面每个 path 被依次解析，直到构造完成一个绝对路径。*
语法：  
```
path.resolve([...paths])
```
例子：
```
path.resolve('/foo/bar', './baz');
// 返回: '/foo/bar/baz'

path.resolve('/foo/bar', '/tmp/file/');
// 返回: '/tmp/file'

path.resolve('wwwroot', 'static_files/png/', '../gif/image.gif');
// 如果当前工作目录为 /home/myself/node，
// 则返回 '/home/myself/node/wwwroot/static_files/gif/image.gif'
```

+ __dirname
    当前模块的文件夹名称。等同于 __filename 的 path.dirname()的值
```
//示例 运行位于 /Users/mjr目录下的example.js文件：node example.js
console.log(__dirname);
// Prints: /Users/mjr
console.log(path.dirname(__filename));
// Prints: /Users/mjr
```

+ fs.readdirSync()
    方法说明：
    同步版本的 fs.readdir() 。  
    方法将返回一个包含“指定目录下所有文件名称”的数组对象。

+ fs-extra 
    fs模块的扩展，提供了更多便利的API

+ fast-glob( https://github.com/mrmlnc/fast-glob 一个更快的 glob 模块)
    node的glob模块允许你使用 *等符号, 来写一个glob规则,像在shell里一样,获取匹配对应规则的文件.( https://www.cnblogs.com/liulangmao/p/4552339.html )

+ path.join([...paths])
    path.join() 方法使用平台特定的分隔符把全部给定的 path 片段连接到一起，并规范化生成的路径.如果任一路径片段不是一个字符串，则抛出 TypeError。
```
//例子
path.join('/foo', 'bar', 'baz/asdf', 'quux', '..');
// 返回: '/foo/bar/baz/asdf'

path.join('foo', {}, 'bar');
// 抛出 'TypeError: Path must be a string. Received {}'
```
与 path.resolve()的区别
1、path.resolve() 在碰到 以 / 的路径片段时。会丢弃剩余路径片段.而path.join()会将路径片段连接到一起。
2、path.relove()会自动补全当前的绝对路径。

+ UpperCamelCase
    node.js模块。将路径转化为驼峰命名。
    ```
    const upperCamelCase = require('uppercamelcase');
    upperCamelCase('foo-bar');
    //=> FooBar
    upperCamelCase('foo_bar');
    //=> FooBar
    upperCamelCase('Foo-Bar');
    //=> FooBar
    upperCamelCase('--foo.bar');
    //=> FooBar
    upperCamelCase('__foo__bar__');
    //=> FooBar
    upperCamelCase('foo bar');
    //=> FooBar
    console.log(process.argv[3]);
    //=> --foo-bar
    upperCamelCase(process.argv[3]);
    //=> FooBar
    upperCamelCase('foo', 'bar');
    //=> 'FooBar'
    upperCamelCase('__foo__', '--bar');
    //=> 'FooBar'
    ```

+ 关于 js 中 !~的用法 主要用的是二进制里面的原码、反码、补码等知识
```
const intallList = exportList.filter(name => !~uninstallComponents.indexOf(uppercamelize(name)));
console.log(!~5);           //false
console.log(!~-1);          //true
可用于 filter中的 数据过滤
```
