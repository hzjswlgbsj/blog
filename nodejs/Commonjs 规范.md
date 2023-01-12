## 1.简介
在浏览器环境中，我们最为常见加载 JavaScript 代码的方式就是使用 `<scrpit>` 标签，无论是一段 JavaScript 代码还是一个文件都可以使用。

但是我们如果要加载更多的外部库，使用 `<script>` 标签来加载就会出现一些问题：

- 需要手动管理加载顺序
- 不同脚本之间如果要通信，需要全局变量作为「桥梁」
- 那如果没有 HTML 怎么加载 JavaScript 呢

Commonjs 就是 Nodejs 加载和管理 JavaScript 文件的一种方式

- 它由 JavaScript 社区发起，在 Nodejs 上应用并推广
- 后续也影响到了浏览器端的模块化

**Commonjs 有如下特点**：

- 所有代码都运行在模块作用域，不会污染全局作用域。
- 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
- 模块加载的顺序，按照其在代码中出现的顺序

## 2.Module 对象
每一个 `.js` 文件都是一个模块，每个模块都有一个 `module` 对象，他们都是 `Module` 构造函数的实例。`module` 对象有以下属性：
```javascript
module.id 模块的识别符，通常是带有绝对路径的模块文件名。
module.filename 模块的文件名，带有绝对路径。
module.loaded 返回一个布尔值，表示模块是否已经完成加载。
module.parent 返回一个对象，表示调用该模块的模块。
module.children 返回一个数组，表示该模块要用到的其他模块。
module.exports 表示模块对外输出的值。
```

### 2.1 module.exports 属性
`module.exports` 属性表示当前模块对外输出的接口，其他文件加载该模块，实际上就是读取 `module.exports` 变量。

a.js
```javascript
module.exports = function add () {
  console.log('add')
}
```
b.js
```javascript
var lib = require('./lib')
console.log('lib', lib) // [Function: add]
```



### 2.2 exports 属性
为了方便，Node 会为每个模块注入一个 **exports** 变量，指向 **module.exports**。
**exports** 变量如果被赋值就会切断它与 **module.exports** 的引用关系，然后被修改后，其他文件引用的时候你会发现你改的代码没有改变。

建议不要使用 `exports` 只使用 `module.exports` 。

## 3. require 命令
### 3.1 基本概念
`require` 命令用于，读取并执行一个 js 文件，返回返回该文件的 exports 对象，由于 exports 返回的是一个对象，所以如果要返回一个函数需要使用 `module.exports` 。
a.js
```javascript
module.exports = function () {
  console.log("hello world")
}
```
b.js
```javascript
require('./a.js')()
```
`module.exports` 返回的是函数，所以能直接执行。

### 3.2 缓存
第一次加载某个模块时，Node会缓存该模块。以后再加载该模块，就直接从缓存取出该模块的 `module.exports` 属性。
```javascript
require('./example.js');
require('./example.js').message = "hello";
require('./example.js').message
// "hello"
```
只会打印一次。

所有缓存的模块保存在 `require.cache` 中，如果想删除模块的缓存，可以像下面这样写。
```javascript
// 删除指定模块的缓存
delete require.cache[moduleName];

// 删除所有模块的缓存
Object.keys(require.cache).forEach(function(key) {
  delete require.cache[key];
})
```
## 4. 模块加载机制
CommonJS 模块的加载机制是，输入的是被输出的值的深拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。
### 4.1 require 的内部处理流程
```javascript
Module._load = function(request, parent, isMain) {
  // 1. 检查 Module._cache，是否缓存之中有指定模块
  // 2. 如果缓存之中没有，就创建一个新的Module实例
  // 3. 将它保存到缓存
  // 4. 使用 module.load() 加载指定的模块文件，
  //    读取文件内容之后，使用 module.compile() 执行文件代码
  // 5. 如果加载/解析过程报错，就从缓存删除该模块
  // 6. 返回该模块的 module.exports
};
```
上面的第4步，采用 `module.compile()` 执行指定模块的脚本，逻辑如下。
```javascript
Module.prototype._compile = function(content, filename) {
  // 1. 生成一个require函数，指向module.require
  // 2. 加载其他辅助方法到require
  // 3. 将文件内容放到一个函数之中，该函数可调用 require
  // 4. 执行该函数
};
```
上面的第1步和第2步，`require`函数及其辅助方法主要如下。
```javascript
require(): 加载外部模块
require.resolve()：将模块名解析到一个绝对路径
require.main：指向主模块
require.cache：指向所有缓存的模块
require.extensions：根据文件的后缀名，调用不同的执行函数
```
一旦 `require` 函数准备完毕，整个所要加载的脚本内容，就被放到一个新的函数之中，这样可以避免污染全局环境。该函数的参数包括 `require`、`module`、`exports`，以及其他一些参数。
```javascript
(function (exports, require, module, __filename, __dirname) {
  // YOUR CODE INJECTED HERE!
});
```
`Module._compile` 方法是同步执行的，所以 `Module._load` 要等它执行完成，才会向用户返回 `module.exports`    的值。
## 参考

1. [阮一峰讲的简洁直接读一遍便可扫盲](https://javascript.ruanyifeng.com/nodejs/module.html)
