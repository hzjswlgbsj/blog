## 介绍

---

### Nodejs 是什么？

- Nodejs 是一个基于 Chrome V8 引擎的 JavaScript 运行环境
- Nodejs 使用了一个事件驱动、非阻塞式 I/O 的模型

### Nodejs 系统架构

![image.png](https://lib.sixtyden.com/nodejs%20system.png)

图中左侧为 **Chrome V8 引擎的 JavaScript 运行环境**，右侧是 **非阻塞式 I/O 的模型**
APPLICATION：你写的 nodejs 代码
V8：你的代码被 V8 引擎解析，同时 V8 提供与操作系统交互的能力。而这些能力是通过 Nodejs 的内置模块实现的。

### Nodejs 可以用来做什么

- Web 服务，比如（腾讯视频网页版），Nodejs 来做服务端渲染
- 构件工作流（gulp、webpack 等）
- 开发工具（Visual Studio Code）
- 游戏
- 客户端应用（twitch.tv-以 electron 实现）

### Nodejs 的 Commonjs 模块规范

在浏览器运行环境我们可以用 `script`  来加载 js，但是 Node 环境没有 HTML 该如何加载 JavaScript 呢？commonjs 就是为此而生。

- 它由 JavaScript 社区发起，在 Nodejs 上应用并推广
- 后续也影响到了浏览器端的模块化

## Commonjs 规范

---

### 1.简介

在浏览器环境中，我们最为常见加载 JavaScript 代码的方式就是使用 `<scrpit>`  标签，无论是一段 JavaScript 代码还是一个文件都可以使用。

但是我们如果要加载更多的外部库，使用 `<script>`  标签来加载就会出现一些问题：

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

### 2.Module 对象

每一个 `.js`  文件都是一个模块，每个模块都有一个 `module` 对象，他们都是 `Module`  构造函数的实例。`module` 对象有以下属性：

```javascript
module.id 模块的识别符，通常是带有绝对路径的模块文件名。
module.filename 模块的文件名，带有绝对路径。
module.loaded 返回一个布尔值，表示模块是否已经完成加载。
module.parent 返回一个对象，表示调用该模块的模块。
module.children 返回一个数组，表示该模块要用到的其他模块。
module.exports 表示模块对外输出的值。
```

#### 2.1 module.exports 属性

`module.exports` 属性表示当前模块对外输出的接口，其他文件加载该模块，实际上就是读取 `module.exports` 变量。

a.js

```javascript
module.exports = function add() {
  console.log("add");
};
```

b.js

```javascript
var lib = require("./lib");
console.log("lib", lib); // [Function: add]
```

#### 2.2 exports 属性

为了方便，Node 会为每个模块注入一个 **exports** 变量，指向 **module.exports**。
**exports** 变量如果被赋值就会切断它与 **module.exports** 的引用关系，然后被修改后，其他文件引用的时候你会发现你改的代码没有改变。

建议不要使用 `exports`  只使用 `module.exports` 。

### 3. require 命令

#### 3.1 基本概念

`require`  命令用于，读取并执行一个 js 文件，返回返回该文件的 exports 对象，由于 exports 返回的是一个对象，所以如果要返回一个函数需要使用 `module.exports` 。
a.js

```javascript
module.exports = function () {
  console.log("hello world");
};
```

b.js

```javascript
require("./a.js")();
```

`module.exports` 返回的是函数，所以能直接执行。

#### 3.2 缓存

第一次加载某个模块时，Node 会缓存该模块。以后再加载该模块，就直接从缓存取出该模块的 `module.exports` 属性。

```javascript
require("./example.js");
require("./example.js").message = "hello";
require("./example.js").message;
// "hello"
```

只会打印一次。

所有缓存的模块保存在 `require.cache` 中，如果想删除模块的缓存，可以像下面这样写。

```javascript
// 删除指定模块的缓存
delete require.cache[moduleName];

// 删除所有模块的缓存
Object.keys(require.cache).forEach(function (key) {
  delete require.cache[key];
});
```

### 4. 模块加载机制

CommonJS 模块的加载机制是，输入的是被输出的值的深拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。

#### 4.1 require 的内部处理流程

```javascript
Module._load = function (request, parent, isMain) {
  // 1. 检查 Module._cache，是否缓存之中有指定模块
  // 2. 如果缓存之中没有，就创建一个新的Module实例
  // 3. 将它保存到缓存
  // 4. 使用 module.load() 加载指定的模块文件，
  //    读取文件内容之后，使用 module.compile() 执行文件代码
  // 5. 如果加载/解析过程报错，就从缓存删除该模块
  // 6. 返回该模块的 module.exports
};
```

上面的第 4 步，采用 `module.compile()` 执行指定模块的脚本，逻辑如下。

```javascript
Module.prototype._compile = function (content, filename) {
  // 1. 生成一个require函数，指向module.require
  // 2. 加载其他辅助方法到require
  // 3. 将文件内容放到一个函数之中，该函数可调用 require
  // 4. 执行该函数
};
```

上面的第 1 步和第 2 步，`require`函数及其辅助方法主要如下。

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

`Module._compile` 方法是同步执行的，所以 `Module._load` 要等它执行完成，才会向用户返回 `module.exports` 的值。

### 参考

1. [阮一峰讲的简洁直接读一遍便可扫盲](https://javascript.ruanyifeng.com/nodejs/module.html)

## Nodejs 异步

---

先放上 Nodejs 的系统架构图
![image.png](https://lib.sixtyden.com/nodejs%20system.png)

### 非阻塞 I/O

- I/O 即 Input/Output，一个系统的输入输出
- 阻塞 I/O 和 非阻塞 I/O 的区别在于**系统接收输入再到输出期间，能不能接收其他输入**

有如下代码：

```javascript
/**
 * 用glob包来做文件结构获取，以此解释非阻塞i/o
 */

const glob = require("glob");
let res = null;

// 同步方式
// console.time('glob')
// res = glob.sync(__dirname + '/**/*')
// console.timeEnd('glob')
// console.log(res)

// 异步方式
console.time("glob");
glob(__dirname + "/**/*", (err, result) => {
  res = result;
  // console.log(res)
  console.log("get result!");
});
console.timeEnd("glob");
```

执行同步操作就相当于阻塞 I/O，得到的结果如下
![image.png](https://cdn.nlark.com/yuque/0/2021/png/160765/1626604236877-5f85afd1-ef4a-43b1-8276-aab45b4960a1.png#averageHue=%232c2b2a&crop=0&crop=0&crop=1&crop=1&height=87&id=Dw3a0&margin=%5Bobject%20Object%5D&name=image.png&originHeight=96&originWidth=688&originalType=binary&ratio=1&rotation=0&showTitle=false&size=14236&status=done&style=none&title=&width=624)

执行异步操作就相当于非阻塞 I/O，得到的结果如下

![image.png](https://cdn.nlark.com/yuque/0/2021/png/160765/1626604288664-d8a9d17b-cea1-48c5-a8a4-eb5500dcf9b9.png#averageHue=%232c2a2a&crop=0&crop=0&crop=1&crop=1&height=117&id=uM972&margin=%5Bobject%20Object%5D&name=image.png&originHeight=128&originWidth=682&originalType=binary&ratio=1&rotation=0&showTitle=false&size=16544&status=done&style=none&title=&width=624)

### 事件循环

事件循环是 Nodejs 实现非阻塞 I/O 的基础，他们都是属于 LIBUV 这个 c++ 库。
一下代码简单理解时间循环（因为我已经理解浏览器运行环境的事件循环机制，所以这边不详细记录）

```javascript
const eventloop = {
  queue: [],
  loop() {
    while (this.queue.length) {
      let callback = this.queue.shift();
      callback();
    }

    setTimeout(this.loop.bind(this), 50);
  },
  add(callback) {
    this.queue.push(callback);
  },
};

eventloop.loop();

setTimeout(() => {
  eventloop.add(() => {
    console.log(1);
  });
}, 500);

setTimeout(() => {
  eventloop.add(() => {
    console.log(2);
  });
}, 800);

// 结果
// 1
// 2
```

### Promise

- 当前事件循环的不到的结果（给你的是一个「承诺」），但是未来的事件循环会给到你结果（兑现「承诺」）
- promise 是一个状态机
  - pending
  - fulfilled/resolve
  - rejected

简单使用

```javascript
const promise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve(3);
  }, 300);

  // 这个是无效的，promise的状态是不可扭转的
  setTimeout(() => {
    reject(new Error());
  }, 500);
})
  .then((res) => {
    // resolve 状态会立即执行then
    console.log(res);
  })
  .catch((err) => {
    console.log(err);
  });

console.log(promise);

setTimeout(() => {
  console.log(promise);
}, 800);
```

- .then 和 .catch

  - resolved 状态的 Promise 会回调后面的第一个 .then
  - resolved 状态的 Promise 会回调后面的第一个 .catch
  - 任何一个 rejected 状态且后面没有 .catch 的 Promise，都会造成浏览器/node 环境的全局错误

- 执行 then 和 catch 会返回一个新的 Promise，该 Promise 最终状态根据 then 和 catch 的回调函数的执行结果决定
  - 如果回调函数最终是 throw，该 Promise 是 rejected 状态
  - 如果回调函数最终是 return，该 Promise 是 resolved 状态
  - 但如果回调函数最终 return 了一个 Promise，该 Promise 会和回调函数 return 的 Promise 状态保持一致

以上总结可由一下代码来理解，假设我们去面试，面试一共有 3 轮，3 轮全部通过你开心的笑了。某一轮失败你就哭了，并且 hr 通知你到底是在第几面挂掉的。

```javascript
(function () {
  const promise = interview(1)
    .then(() => {
      return interview(2);
    })
    .then(() => {
      return interview(3);
    })
    .then(() => {
      console.log("smile");
    })
    .catch((err) => {
      console.log("cry at " + err.round + " round");
    });
  function interview(round) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        if (Math.random() > 0.5) {
          resolve("success");
        } else {
          const error = new Error("fail");
          error.round = round;
          reject(error);
        }
      }, 500);
    });
  }
})();
```

### async/await

定义两个函数，分别为 async 函数 和 返回 Promise 的普通函数，并且去执行他们，会发现他们返回值是一样的 Promise

```javascript
const asyncFun = async function () {
  return 1;
};

const promiseFun = function () {
  return new Promise((resolve) => {
    resolve(1);
  });
};

console.log(asyncFun());
console.log(promiseFun());

// Promise { 1 }
// Promise { 1 }
```

- async function 是 Promise 的语法糖封装
- 异步编程的终极解决方案 - 以同步的方式写异步代码
  - await 关键字可以「暂停」async function 的执行
  - await 关键字可以以同步的写法获取 Promise 的执行结果
  - try-catch 可以获取 await 所得到的的结果
- 他是一个可以穿越事件循环机制的 function

下面，我们来改造一下上面的 3 次面试的代码，你看看代码量。

```javascript
(async function () {
  try {
    // await interview(1)
    // await interview(2)
    // await interview(3)
    await Promise.all([interview(1), interview(2), interview(3)]);
    console.log("smile");
  } catch (error) {
    return console.log(`cry at ${error.round}`);
  }
})();

function interview(round) {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      if (Math.random() > 0.5) {
        resolve("success");
      } else {
        const error = new Error("fail");
        error.round = round;
        reject(error);
      }
    }, 500);
  });
}
```

## 什么是 HTTP 服务

|-------------|
| 5.应用层 |
|-------------|
| 4.传输层 |
|-------------|
| 3.网络层 |
|-------------|
| 2.数据链路层 |
|-------------|
| 1.物理层 |
|-------------|

- 一个网页请求，包含两次 HTTP 包交换
  - 浏览器向 HTTP 服务器发送请求 HTTP 包
  - HTTP 服务器想浏览器返回 HTTP 包
- HTTP 服务套做什么事
  - 解析进来的 HTTP 请求报文
  - 返回对应的 HTTP 返回报文、

### 简单实现一个 HTTP 服务

Nodejs 的 http 模块

```javascript
const http = require("http");

http
  .createServer((req, res) => {
    res.writeHead(200);
    res.end("helle");
  })
  .listen(3000);

// 在浏览器中访问 localhost:3000
```

下面是一个石头剪刀布游戏

// game.js

```javascript
module.exports = function (playAction) {
  if (["rock", "scissor", "paper"].indexOf === -1) {
    throw new Error("invalid palyAction");
  }
  // 计算机电脑出的东西
  let computerAction;
  var random = Math.random() * 3;

  if (random < 1) {
    computerAction = "rock";
  } else if (random > 2) {
    computerAction = "scissor";
  } else {
    computerAction = "paper";
  }

  if (computerAction === playAction) {
    return 0;
  } else if (
    (computerAction === "rock" && playAction === "paper") ||
    (computerAction === "scissor" && playAction === "rock") ||
    (computerAction === "paper" && playAction === "scissor")
  ) {
    return -1;
  } else {
    return 1;
  }
};
```

// index.js

```javascript
const http = require("http");
const url = require("url");
const fs = require("fs");
const querystring = require("querystring");
const game = require("./game");
let playerWon = 0;
let playerLastAction = null;
let sameCount = 0;

http
  .createServer((request, response) => {
    const parsedUrl = url.parse(request.url);
    if (parsedUrl.pathname === "/favicon.ico") {
      response.writeHead(200);
      response.end();
      return;
    }

    if (parsedUrl.pathname === "/game") {
      const query = querystring.parse(parsedUrl.query);
      const playerAction = query.action;
      const gameResult = game(playerAction);
      playerLastAction = playerAction;
      if (playerWon >= 3 || sameCount === 9) {
        response.writeHead(500);
        response.end("我再也不和你玩了！");
        return;
      }

      if (playerAction && playerAction === playerLastAction) {
        sameCount++;
      } else {
        sameCount = 0;
      }

      if (sameCount > 3) {
        response.writeHead(400);
        response.end("你作弊！");
        sameCount = 9;
        return;
      }

      response.writeHead(200);
      if (gameResult === 0) {
        response.end("平局！");
      } else if (gameResult === 1) {
        response.end("你赢了！");
        playerWon++;
      } else {
        response.end("你输了！");
      }
    }

    if (parsedUrl.pathname === "/") {
      fs.createReadStream(__dirname + "/index.html").pipe(response);
    }
  })
  .listen(3000);
```

// index.html

```javascript
<!DOCTYPE html utf-8>
<html>
  <head>
    <meta charset="UTF-8">
  </head>
  <body>
    <div
      id='output'
      style='width: 600px; height: 400px;background-color: #eee; overflow: auto;'
    ></div>
    <button id='rock'>石头</button>
    <button id='scissor'>剪刀</button>
    <button id='paper'>布</button>
  </body>
  <script>
    const $button = {
      rock: document.getElementById('rock'),
      scissor: document.getElementById('scissor'),
      paper: document.getElementById('paper'),
    }

    const $output = document.getElementById('output')
    Object.keys($button).forEach(key => {
      $button[key].addEventListener('click', () => {
        fetch(`http://${window.location.host}/game?action=${key}`)
        .then((res => res.text()))
        .then(text => $output.innerHTML += text + '
')
      })
    })
  </script>
</html>
```
