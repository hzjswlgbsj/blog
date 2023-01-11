## 介绍

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
