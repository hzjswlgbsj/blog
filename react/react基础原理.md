# react 的基础知识

## vue 的基础知识

react 数据更新后 -> setState -> 计算虚拟 DOM -> diff 算法 -> 更新 DOM
但是 react 不分组件的，直接从 root 开始计算的的，所以可能会卡，所以需要 fiber

## react 的数据更新

**react 如何解决这个「可能会卡」问题？**

来看一个示例代码，这个代码是模拟一个动画效果，然后我们想办法在动画前面做其他的运算，导致卡顿，然后我们怎么解决卡顿

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite + TS</title>
    <style>
      #app {
        width: 100px;
        height: 100px;
        background-color: red;
        position: relative;
        left: 0px;
        right: 0px;
      }
    </style>
  </head>

  <body>
    <div id="app"></div>
    <div id="demo"></div>
    <script type="module" src="./src/reactDemo/index.ts"></script>
    <script>
      /**
       * 时间切片概念，将巨大运算任务拆分成小任务，在浏览器空闲的时候执行 requestIdleCallback 就是这样一个API
       * requestIdleCallback 这个 API 的用法是，浏览器空闲的时候会执行这个回调，这个回调会在下一帧执行（react 现在已经不使用这个 api 了）
       *
       * 注意，这个 diff1 其实没有做什么性能优化，仅仅是利用浏览器空闲的时候来做计算，让动画看起来不卡了
       *
       *
       * 多聊一句，大文件上传，2g 视频。如何做断点续传，如何保证不卡，计算断点续传的 hash 值的时候肯定会卡
       * 1.web worker
       * 2.time slice (requestIdleCallback)
       */
      let cur = 0;
      let num = 0;
      function diff1(deadline) {
        // 当前浏览器还有空闲时间
        while (deadline.timeRemaining() > 1) {
          // 每次浏览器有空闲了，我们都算 200 次
          let i = 200;
          while (i > 0) {
            num += 2 ** Math.random();
            i--;
          }

          cur += 100;
        }

        // 还没算完，但是浏览器有任务了，那么我们再启动一下
        if (cur < 100000000) {
          window.requestIdleCallback(diff1);
        } else {
          document.querySelector("#demo").innerHTML = num;
          cur = 0;
        }
      }

      setInterval(() => {
        // diff()
        // diff1()
        window.requestIdleCallback(diff1);
      }, 2000);

      /** 然后在前面加一个计算任务重的逻辑，可以看到下面那个 start 的动画效果会卡顿，怎么办呢？ */
      function diff() {
        let i = 0;
        let num = 0;
        // 模拟大计算量
        while (i < 100000000) {
          num += 2 ** Math.random();
          i++;
        }

        document.querySelector("#demo").innerHTML = num;
      }

      /** 模拟一个动画，来作为演示react 渲染 */
      function start() {
        let i = 1;
        let direction = "right";
        const div = document.querySelector("#app");
        setInterval(() => {
          div.style.left = i + "px";
          if (i == 500 || i == 0) {
            direction = direction === "left" ? "right" : "left";
          }
          i = direction === "right" ? i + 1 : i - 1;
        }, 30);
      }

      start();
    </script>
  </body>
</html>
```

react 中的 VNode 可能是这种树形结构

```js
const vnode = {
  type: "div",
  children: [
    {
      type: "div",
      children: [
        {
          type: "div",
        },
      ],
    },
    // ...
  ],
};
```

那 `setState` 的时候就会类似于：

```js
let newVnode = {};
diff(oldVnode, newVnode);
```

那 Fiber 的时候就会从树形结构变成链表结构

```js
let vdom = {
  type: "div",
  children: [],
};

// 第一个子节点
vdom.child = {
  type: "div",
  children: [],
};

// 下一个兄弟节点
vdom.nextSibling = {
  type: "div",
  children: [],
};

// 父节点，react 中叫 return 属性
vdom.return = {
  type: "div",
  children: [],
};
```

然后 react 更新的伪代码差不多是这样

```js
vdom.forEach((child, i) => {
  if (i == 0) {
    vdom.child = child;
  } else {
    vdom.children[i - 1].nextSibling = child;
  }
});

child.return = vdom;
```

为什么 react 要设计 fiber 呢，为什么要从树形结构变成链表结构呢？

因为树形结构遍历需要递归，很难做好 「可中断」逻辑，而链表遍历不需要递归，可以中断，所以性能会好很多

![react 的 diff 图示](https://lib.sixtyden.com/react%20%E4%B8%AD%E7%9A%84%20diff%20%E5%9B%BE%E7%A4%BA.png)

在浏览器的视角，requestIdleCallback 的运行是这样的:

![requestIdleCallback 运行](https://lib.sixtyden.com/%E6%B5%8F%E8%A7%88%E5%99%A8%E8%A7%86%E8%A7%92%20requestIdleCallback.png)

## 编译

我们平常写的 jsx 或者 vue template 一般是这样

```html
<div>
  <div>
    <div></div>
  </div>
</div>
```

那么编译的过程就是把这个转化为上面我们说的 vdom，然后才能优化渲染

## vite 的基础知识

为什么在 vite 中不能使用 require 而在 webpack 中可以？

在 html 的 script 标签中增加 type="module" 就可以使用 import 了。
