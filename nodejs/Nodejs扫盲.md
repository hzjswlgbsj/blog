> 本文是 Nodejs 的简单扫盲，快速阅读可以大致了解 Nodejs ，属于笔记类不是教程。

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

|   五层结构   |
| :----------: |
|   5.应用层   |
|   4.传输层   |
|   3.网络层   |
| 2.数据链路层 |
|   1.物理层   |

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

**game.js**

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

**index.js**

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

**index.html**

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

## RPC 调用

RPC（Remote Procedure Call，远程过程调用），可用 Ajax 来类比理解。

### 相同点

- 都是两个计算机之间的网络通信
- 需要双方约定一个数据格式

### 不同点

- Ajax 使用 DNS 作为寻址服务，而 RPC 不一定
- Ajax 应用层协议使用 HTTP，而 RPC 不一定使用，一般会使用**二进制协议**来取代 HTTP
- Ajax 基于 HTTP、TCP，RPC 基于 TCP 或者 UDP

### Node.js 的 buffer 模块

**Buffer** 对象用于表示固定长度的字节序列。 许多 Node.js API 都支持 **Buffer**。

**Buffer** 类是 JavaScript **Uint8Array** 类的子类，并使用涵盖额外用例的方法对其进行扩展。 Node.js API 在支持 **Buffer** 的地方也接受普通的 **Uint8Array**。

虽然 **Buffer** 类在全局范围内可用，但仍然**建议通过 import 或 require 语句显式地引用它**。

#### 创建缓冲区

```javascript
const { Buffer } = require("buffer");
// form 方法创建一个buffer
const buffer1 = Buffer.from("sixty");

// 创建包含字节 [1, 2, 3] 的缓冲区。
const buffer2 = Buffer.from([1, 2, 3]);

// 创建长度为 10 的以零填充的缓冲区。
const buffer3 = Buffer.alloc(10);

console.log(buffer1); // <Buffer 73 69 78 74 79>
console.log(buffer2); // <Buffer 01 02 03>
console.log(buffer3); // <Buffer 00 00 00 00 00 00 00 00 00 00>
```

#### 获取缓冲区值

下面是读取用户命令行输入的例子

```javascript
var fs = require("fs");
var buffer = new Buffer(1024);

var readSize = fs.readSync(fs.openSync("/dev/tty", "r"), buffer, 0, bufferSize);
var chunk = buffer.toString("utf8", 0, readSize);

console.log("INPUT: " + chunk);

// 输入任意内容，然后按回车键
// foo
// INPUT: foo
```

#### protocol-buffers

[protool-buffers](https://github.com/mafintosh/protocol-buffers) 是一个专门为 nodejs 提供的 buffer 库。protool-buffers 可以将结构化的数据文件转化为一个 buffer，并有相关 api 在二进制和结构化数据时间相互转化，类似于 JSON。下面是 protool-buffers 的实例

test.proto

```javascript
message Column {
  required int32 id = 1;
  required string name = 2;
  required float price = 3;
}
```

buffer.js

```javascript
const fs = require("fs");
const protobuf = require("protocol-buffers");
const schema = protobuf(fs.readFileSync(__dirname + "/test.proto", "utf-8"));
console.log(schema);
// 输出
// Messages {
//   Column: {
//     type: 2,
//     message: true,
//     name: 'Column',
//     buffer: true,
//     encode: [Function: encode] { bytes: 0 },
//     decode: [Function: decode] { bytes: 0 },
//     encodingLength: [Function: encodingLength],
//     dependencies: [ [Object], [Object], [Object] ]
//   }
// }

const buffer = schema.Column.encode({
  id: 1,
  name: "Node.js",
  price: 80.4,
});
console.log(schema.Column.decode(buffer));
// 输出
// { id: 1, name: 'Node.js', price: 80.4000015258789 }
```

nodejs 做 RPC 通信的时候就可以把这个结构化的数据，编码成二进制的数据，然后发给另一个服务器，然后服务器再解出来。

### 多路复用的 RPC

#### 建立简单的通讯

直接看代码
client.js

```javascript
const net = require("net");
const socket = new net.Socket({});

socket.connect({
  host: "127.0.0.1",
  port: 4000,
});

socket.write("hello sixty");
```

server.js

```javascript
const net = require("net");

const server = net.createServer((socket) => {
  socket.on("data", (buffer) => {
    console.log(buffer, buffer.toString());
  });
});

server.listen(4000);
```

执行 `node server.js`  开启服务器。然后执行 `node client.js`  想服务发消息，在终端能够看到我们发送的字符串，这样简单的客户端和服务端的通讯就完成了。

一个半双工通讯获取数据的例子：
client.js

```javascript
const net = require("net");

const socket = new net.Socket({});

socket.connect({
  host: "127.0.0.1",
  port: 4000,
});

const lessonid = ["1", "2", "3", "4", "5", "6", "7", "8"];
let id = Math.floor(Math.random() * lessonid.length);
socket.write(encode(id));

socket.on("data", (buffer) => {
  console.log(buffer.toString());

  // 收到服务器的回复后继续发
  id = Math.floor(Math.random() * lessonid.length);
  socket.write(encode(id));
});

function encode(id) {
  buffer = Buffer.alloc(4);
  buffer.writeInt32BE(lessonid[id]);
  return buffer;
}
```

server.js

```javascript
const net = require("net");

const server = net.createServer((socket) => {
  socket.on("data", (buffer) => {
    const lessonid = buffer.readInt32BE();
    setTimeout(() => {
      socket.write(Buffer.from(data[lessonid]));
    }, 500);
  });
});

server.listen(4000);

const data = {
  1: "01 | 课程介绍",
  2: "02 | 内容综述",
  3: "03 | nodejs是什么",
  4: "04 | nodejs可以用来做啥",
  5: "05 | 课程实战项目介绍",
  6: "06 | 什么是技术预研",
  7: "07 | nodejs环境安装",
  8: "08 | 第一个nodejs程序",
};
```

可以看出半双工通信就是客户端服务端只能同时存在一个请求。为什么不能客户端直接不断的发请求？这里面涉及到一个时序问题：服务端接收到客户端快速发不了的并发请求，并不能保证按照时间先后给到客户端返回。

**如何做到全双工通讯？**

1. 我们得为每一个客户端发送的数据包加上 seq 包序号
2. 避免掉 TCP 的「粘包」机制（TCP 底层优化机制会导致「粘包」，会将很多个并发请求合并为一个，最终只返回一次）

增加 seq 解决问题 1，并将半双工代码改为双工通信
client.js

```javascript
const net = require("net");

const socket = new net.Socket({});

socket.connect({
  host: "127.0.0.1",
  port: 4000,
});

const lessonid = ["1", "2", "3", "4", "5", "6", "7", "8"];
let seq = 0;
let id = Math.floor(Math.random() * lessonid.length);

socket.on("data", (buffer) => {
  const seqBuffer = buffer.slice(0, 2);
  const titleBuffer = buffer.slice(2);
  console.log(seqBuffer.readInt16BE(), titleBuffer.toString());
});

function encode(id) {
  buffer = Buffer.alloc(6);
  buffer.writeInt16BE(seq++); // 在数据buffer前面两位加上seq
  buffer.writeInt32BE(lessonid[id], 2);
  return buffer;
}

// 每50毫秒就通讯一次
setInterval(() => {
  id = Math.floor(Math.random() * lessonid.length);
  socket.write(encode(id));
}, 50);
```

server.js

```javascript
const net = require("net");

const server = net.createServer((socket) => {
  socket.on("data", (buffer) => {
    const seqBuffer = buffer.slice(0, 2);
    const lessonid = buffer.readInt32BE(2);
    setTimeout(() => {
      const buffer = Buffer.concat([seqBuffer, Buffer.from(data[lessonid])]);
      socket.write(buffer);
    }, 10 + Math.random() * 1000); // 随机一秒内返回，我们测试在包乱序返回的时候客户端是否还能将请求包和返回包对应上
  });
});

server.listen(4000);

const data = {
  1: "01 | 课程介绍",
  2: "02 | 内容综述",
  3: "03 | nodejs是什么",
  4: "04 | nodejs可以用来做啥",
  5: "05 | 课程实战项目介绍",
  6: "06 | 什么是技术预研",
  7: "07 | nodejs环境安装",
  8: "08 | 第一个nodejs程序",
};
```

这里我们客户端是妹 50 毫秒发一次，如果直接并发发过去的话，我们会发现服务端只接收到一个 buffer。原因是 tcp 底层会自动帮我们拼接并发的包，通常称为「粘包」是一种优化，但是不符合我们实现双工通讯的需求。

关于粘包问题这里没办法详细说，RPC 本身是一门比较高深的内容，社区也有很多成熟的 RPC 框架，这里我们学习 Nodejs 不需要了解的太深入，我们下一篇直接进入实战。

### 总结

关于 Buffer 模块，刚接触 nodejs 的小伙伴可能会觉得很抽象难懂，我就是这样的人，我建议可以阅读 [这篇文章](https://juejin.cn/post/6844903897438371847) 来帮助理解。

## Nodejs 多进程优化

### 1. 进程与线程优化

#### 进程、线程与协程

首先理解一下三者的概念。

<iframe
  height=850 
  width=90% 
  src="https://www.processon.com/embed/614867c107912943c05abd7b"  
  frameborder=0  
  allowfullscreen
/>

进程、线程、协程脑图

<iframe
  height=850 
  width=90% 
  src="https://www.processon.com/embed/614868ba637689589eda04fd"  
  frameborder=0  
  allowfullscreen
/>

> J**avaScript 是单线程的，但是可以有多个进程，一个进程或者子进程里面只有一个线程，而 web worker 与 Nodejs 的子线程一样没有改变单线程的本质，子线程完全收到主线程控制，并且能力会收到一些限制，比如子线程不能操作 DOM 等。**

#### Nodejs 的事件循环

- 主线程运行 v8 与 JavaScript
- 多个子线程通过事件循环被调度

事件循环的过程我们做一个类比：Nodejs 有一个进程，他有个主线程去运行 v8 引擎与 JavaScript。Nodejs 就是一个公司，老板就相当于**主线程**，员工就相当于**子线程**，事件循环就相当于老板在做公司一线事务（跑 JavaScript 和 v8 引擎） ，一旦发现有可以分配给员工做的任务（比如异步任务），老板就会将这些异步任务分配给员工，员工做完后他就报告结果（通过一个事件回调）给老板，老板会不断处理员工做的事情。

<iframe
  height=850 
  width=90% 
  src="https://www.processon.com/embed/614853421e085315dc4d864f"  
  frameborder=0  
  allowfullscreen
/>

当主线程任务过多的时候是处理不完的，但是 JavaScript 只有一个线程，这会导致无法充分利用硬件资源。所以 Nodejs 提供了子线程和子进程的能力，让我们可以利用子进程和子线程在别的 cpu 跑一个 JavaScript 的环境。这样 Nodejs 就好比是个集团了，它里面有很多个子公司，子公司的一个老板就复杂跑一个 JavaScript 的任务，我们就可以充分利用 cpu 资源。

<iframe
  height=850 
  width=90% 
  src="https://www.processon.com/embed/6148544a0e3e74524c9403ac"  
  frameborder=0  
  allowfullscreen
/>

**master.js**

```typescript
/**
 * 主进程
 */

const cp = require("child_process"); // 引入子进程模块
const childProcess = cp.fork(__dirname + "/child.js"); // 以child.js为子进程入口，创建一个子进程
childProcess.send("hello child!"); // 给子进程发消息

// 接受子进程发来的消息
childProcess.on("message", (message) => {
  console.log("Message form child", message);
});
```

**child.js**

```typescript
/**
 * 子进程，process是个全局变量
 */

// 接收来自主进程的消息
process.on("message", (message) => {
  console.log("Message form master", message);
  process.send("hello master!");
});
```

可以看出主进程与子进程之间的通讯类似于 RPC 调用中的服务端与客户端的通讯。进程之间的通讯传递的参数需要 Buffer 序列化或者字符串序列化，这样的话主进程可以分发任务给子进程，子进程进行计算后将结果返回给主进程，最大限度利用 cpu。

如果你需要做一些 CPU 敏感的任务的话，Nodejs 也提供了 **Worker Threads** 来创建一个子线程，而不是 子进程来处理。**Worker Threads **是 v10 版本后才提供的，他对于 I/O 敏感的操作并不会有太大的帮助，因为 Nodejs 现有的非阻塞 I/O 已经比较强大有效了，而且 **child_process** 和 **Cluster** 已经足够利用其他的 CPU 了。

### 2.多核能力的网络程序

上一节了解了如何利用子进程去使用多核 CPU 的能力来执行 CPU 敏感的程序，那我们有没有办法将网络请求任务也分发出去呢？假设我们主进程把多个 http 请求分发给多个子进程，然后子进程处理完后把结果返回给主进程是不是就可以了，答案是肯定的，但是如果我们自己做主进程与子进程这个通讯过程处理的话那岂不是相当的麻烦？所以 Nodejs 提供的 Cluster 模块。

<iframe
  height=850 
  width=90% 
  src="https://www.processon.com/embed/61485df75653bb35253038d1"  
  frameborder=0  
  allowfullscreen
/>

启动与当前机器核心数相同的子进程来处理 http 请求

```typescript
const cluster = require("cluster");
const os = require("os");

// 如果是主线程的话分发出当前机器的cpu核心数的子进程
if (cluster.isMaster) {
  for (let i = 0; i < os.cpus().length; i++) {
    cluster.fork();
  }
} else {
  require("./http.js"); // 如果是子进程的话直接处理http请求
}
```

但是不要直接 fork 当前机器的核心数数量的子进程，因为 JavaScript 的一些事件循环里面的任务本身会分发给多个 CPU 去处理，如果 CPU 都被占用的话反而会影响执行速度，而且 fork 一个 进程的同时 Nodejs 内存也会被复制，成倍的内存被消耗却得不到成倍的 CPU 性能优化。

### 3. 进程守护与管理

由于 JavaScript 是一门解释型的语言，很多变量引用上的错误，比如你使用某个空的值，你把这个空的值当做对象来使用，并获取它的某个 key，这时候就会出错。但是很遗憾，在代码运行前这是不能被检测到的，你只有运行到这行代码的时候才能发现这个错误。

这种错误在开发的过程中其实是很容易犯的，但是你又不能在统一上线之前检测到被解决，这就导致这种错误很容易被发布到线上去。而且 JavaScript 本身就含有很多的异步代码，异步代码会在「未来」的某个事件循环中，在一个新的调用栈里面回调回来，那如果在这个新的调用栈中运行的代码中包含上面提到的这种错误，而开发者又没有使用 try-catch 来包裹他的代码，那么就不单单是报错了，很可能会引起程序崩溃。

所以，我们就可以使用进程守护能力，在主进程对服务器进程进行监控，这样可以大大提升服务器进程的健康程度，从而提高 Nodejs 程序的稳定性。

### 参考

1. [进程、线程与协程](https://zhuanlan.zhihu.com/p/70256971)
2. [Nodejs 线程与进程](https://www.nodejs.red/#/nodejs/process-threads?id=interview1)

## Express 和 Koa

要了解一个框架最好的方法是：

- 了解他的关键功能
- 推导出他要解决的问题是什么

核心功能

- 路由
- request/response 简化
  - request: pathname、query 等
  - response: send()、json()、jsonp()等

### **Express**

用 express 改造石头剪刀布游戏
express.js，代替之前的 index.js

```javascript
const fs = require("fs");
const game = require("./game");
const express = require("express");

let playerWinCount = 0; // 玩家胜利次数
let playerLastAction = null; // 玩家上一次动作
let sameCount = 0; // 玩家连续出同一个动作的次数

const app = express();

app.get("/favicon.ico", (request, response) => {
  response.status(200);
  return;
});

app.get(
  "/game",
  (request, response, next) => {
    if (playerWinCount >= 3 || sameCount === 9) {
      response.status(500);
      response.send("我再也不和你玩了！");
      return;
    }

    next();

    if (response.playerWon) {
      playerWinCount++;
    }
  },
  (request, response, next) => {
    const query = request.query;
    const playerAction = query.action;

    if (playerAction && playerAction === playerLastAction) {
      sameCount++;
    } else {
      sameCount = 0;
    }

    if (sameCount > 3) {
      response.status(400);
      response.send("你作弊！我再也不跟你玩了");
      sameCount = 9;
      return;
    }

    playerLastAction = playerAction;
    response.playerAction = playerAction;
    next();
  },
  (request, response, next) => {
    const playerAction = response.playerAction;
    const gameResult = game(playerAction);
    response.status(200);
    if (gameResult === 0) {
      response.send("平局！");
    } else if (gameResult === 1) {
      response.send("你赢了！");
      response.playerWon = true;
    } else {
      response.send("你输了！");
    }
  }
);

app.get("/", (request, response) => {
  response
    .status(200)
    .send(fs.readFileSync(__dirname + "/index.html").toString());
});

app.listen(3000);
```

**Express** 是个**洋葱模型**，我们可以在 get 或者其他类型的请求中以中间件的形式执行逻辑，如代码中，可以传入无限多个函数，这些函数可以称为中间件，可以抽到外层的 js 文件管理，也可使用第三方的中间件处理逻辑。这些函数中的 response 是同一个，可以实现中间件的数据交互。

但是 **Express** 的洋葱模型 是有问题的，如果某个中间件出现了异步操作，比如加一个像 settimeout 这种事务，那么其他中间件会比他先执行，导致数据交互出问题，而 **Koa** 能解决这个问题，可以说 Koa 就是 **Express** 的升级版。

### Koa

- koa 支持 await 可以等待某个操作执行完成，这就能解决 express 的问题
- koa 没有了路由的概念，它将这个能力给到了中间件。所以就有了开源的的 koa 路由中间件，就像前端一样。

下面用 Koa 来改造石头剪刀布游戏

```javascript
const fs = require("fs");
const game = require("./game");
const koa = require("koa");
const mount = require("koa-mount");

let playerWinCount = 0; // 玩家胜利次数
let playerLastAction = null; // 玩家上一次动作
let sameCount = 0; // 玩家连续出同一个动作的次数
const app = new koa();
const gameKoa = new koa(); // 新的koa实例

// 中间件1
gameKoa.use(async (ctx, next) => {
  if (playerWinCount >= 3) {
    ctx.status = 500;
    ctx.body = "你开挂，我不会再玩了";
    return;
  }

  await next();

  if (ctx.playerWon) {
    playerWinCount++;
  }
});

// 中间件2
gameKoa.use(async (ctx, next) => {
  const query = ctx.query;
  const playerAction = query.action;

  if (!playerAction) {
    ctx.status = 400;
    return;
  }

  if (sameCount === 9) {
    ctx.status = 500;
    ctx.body = "我再也不和你玩了！";
  }

  if (playerAction === playerLastAction) {
    sameCount++;
    if (sameCount >= 3) {
      ctx.status = 400;
      ctx.body = "你作弊！我再也不跟你玩了";
      sameCount = 9;
      return;
    }
  } else {
    sameCount = 0;
  }

  playerLastAction = playerAction;
  ctx.playerAction = playerAction;
  await next();
});

// 中间件3
gameKoa.use(async (ctx) => {
  const playerAction = ctx.playerAction;
  const gameResult = game(playerAction);
  await new Promise((resolve) => {
    setTimeout(() => {
      ctx.status = 200;
      if (gameResult === 0) {
        ctx.body = "平局！";
      } else if (gameResult === 1) {
        ctx.body = "你赢了！";
        ctx.playerWon = true;
      } else {
        ctx.body = "你输了！";
      }

      resolve();
    }, 500);
  });
});
// 游戏路由
app.use(mount("/game", gameKoa));

app.use(
  mount("/favicon.ico", (ctx) => {
    ctx.status = 200;
  })
);

// 根路由
app.use(
  mount("/", (ctx) => {
    ctx.body = fs.readFileSync(__dirname + "/index.html", "utf-8");
  })
);
app.listen(3000);
```

Koa 核心功能

- 比 Express 更极致的 request/response 简化
  - ctx.status = 200
  - ctx.body = 'hello world'
- 使用 async function 实现的中间件
  - 有「暂停执行」的能力
  - 在一部的情况下也符合洋葱模型

### Express vs Koa

- Express 门槛更低，Koa 更强大优雅
- Express 封装更多东西，开发更快速，Koa 可定制化成都更高
