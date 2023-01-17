# 博客搭建之 Docsify 美化篇

上一篇文章我们介绍了如何使用 Docsify+Github 搭建博客，还没搭好的 [赶紧去搭一个](https://www.sixtyden.com/#/docs/normal/Centos+Docker+Nginx+Doscify+Github%E6%90%AD%E5%BB%BA%E5%8D%9A%E5%AE%A2%E5%85%A8%E6%95%99%E7%A8%8B)，接下来是博客美化。

Docsify 默认的主题都是极简风，是 vuepress 主题的，需要对布局和样式进行调整，增加一些插件，本文介绍的是我自己喜欢的风格，也是极简风，只是修改了一些样式。

## 增加 Cover

cover 页还是要定制一下

```javascript
<script>
  window.$docsify = {
    coverpage: true // 展示封面，开启后会自动加载_coverpage.md
  }
</script>
```

先看效果
![image.png](https://lib.sixtyden.com/docsify_coverpage.jpg)
首先在根目录新建 `_coverpage.md`，内容如下（注意，这里是可以写 HTML 的 Docsify 会解析）
**\_coverpage.md**

```html
<div class="main-layout-index-container">
  <div class="main-layout-index-content-container">
    <div class="main-layout-index-content">
      <div>
        <img
          class="main-layout-avatar"
          src="https://lib.sixtyden.com/avatar_shuimen.pic.jpg"
        />
      </div>
      <span class="main-layout-desc">Sixty's Den</span>
      <p class="main-layout-motto">活到老，学到老</p>
      <div class="main-layout-line">
        <div class="line-container">
          <div class="line-style line-style-width1"></div>
        </div>
      </div>
      <p class="main-layout-self-intro">
        嗨，我是刘林
        (@Sixty)，一名来自天府之国的web开发者，现居南京，就职于育儿网，正在前端的浑水里摸爬打滚。
      </p>
      <div class="main-layout-line">
        <div class="line-container">
          <div class="line-style line-style-width2"></div>
        </div>
      </div>
      <p class="main-layout-self-desc">
        生活不止眼前的代码，还有诗和远方的田野。
      </p>
      <div class="main-layout-module" v-if="menuList && menuList.length > 0">
        <span class="main-layout-module-name">
          <a href="https://github.com/hzjswlgbsj/blog" target="_blank"
            >Github</a
          >
        </span>
        <span class="main-layout-module-name">
          <a href="#README">开始阅读</a>
        </span>
      </div>
    </div>
  </div>
  <div class="main-layout-index-cover cover-blue"></div>
</div>
```

增加样式文件 `coverpage.css`，然后在 `index.html` 中引入
**coverpage.css**

```css
.main-layout-index-container {
  background-color: #046d78;
  opacity: 0.95;
  font-size: 12px;
  position: relative;
  text-align: center;
  color: #fefefe;
  width: 100%;
  height: 100%;
  background-size: cover;
  background-repeat: no-repeat;
  background-image: url(https://lib.sixtyden.com/hy_xiaoying.jpg);
}

.main-layout-avatar {
  border-radius: 50%;
  width: 80px;
  height: 80px;
  cursor: pointer;
}

.main-layout-line {
  margin: 20px 0;
}

.line-container {
  display: flex;
  justify-content: center;
}

.line-style {
  background-color: #4b4f65;
  height: 1px;
}

.line-style-width1 {
  width: 300px;
}

.line-style-width2 {
  width: 100px;
}

.main-layout-index-content-container {
  display: flex;
  align-items: center;
  height: 100%;
  position: relative;
  padding: 0 60px;
  text-align: center;
  text-shadow: 0 1px 1px rgba(0, 0, 0, 0.4);
}

.main-layout-index-content {
  max-width: 620px;
  margin: 0 auto;
}

.main-layout-desc {
  margin: 0 0 5px 0;
  font-size: 3em;
  letter-spacing: 4px;
  /*此属性减少或增加字符之间的间隔*/
}

.main-layout-motto {
  color: #cccccc;
  font-size: 1.5em;
}

.main-layout-line {
  margin: 20px 0;
}

.main-layout-self-intro,
.main-layout-self-desc {
  font-size: 1.2em;
}

.main-layout-module {
  margin: 50px 0;
}

.main-layout-module-name {
  padding: 10px 20px;
  border: 1px solid #ffffff;
  border-radius: 20px;
  margin-right: 10px;
}

.main-layout-module-name:hover {
  cursor: pointer;
  background-color: #42b983;
}

.cover-blue {
  background-color: #046d78;
  opacity: 0.6;
  z-index: 10;
}

.main-layout-index-cover {
  display: block;
  position: absolute;
  z-index: -1;
  top: 0;
  right: 0;
  bottom: 0;
  left: 0;
}
```

还有一些全局样式我放到了 `index.css`，也一并引入到 `index.html`
**index.css**

```css
section.cover .cover-main {
  margin: 0;
  height: 100%;
}

.content {
  padding-top: 0;
}

.markdown-section > :first-child {
  margin-top: 70px !important;
}

#docsify-darklight-theme {
  top: 70px;
}

.app-name-link {
  display: flex;
  align-items: center;
  margin-left: 23px;
}

.title-logo {
  width: 28px;
  height: 28px;
  margin-right: 12px;
}

.sidebar > h1 {
  font-size: 1.2rem !important;
  color: #42b983 !important;
  font-weight: 400 !important;
}

/*加重描述strong*/
.markdown-section strong {
  color: rgb(239, 112, 96);
}

.app-nav {
  position: fixed;
  margin: 0;
  padding: 10px 60px 10px 0;
  width: calc(100% - 300px);
  background-color: #fff;
  height: 55px;
  border-bottom: 1px solid #eee;
}

.github-corner {
  position: absolute;
  z-index: 99;
}

.github-corner svg {
  height: 55px;
  width: 55px;
}

.markdown-section code {
  border-radius: 2px;
  font-family: "Helvetica Neue", Helvetica, "Hiragino Sans GB",
    "Microsoft YaHei", Arial, sans-serif;
  font-size: 16px !important;
  margin: 0 2px;
  padding: 3px 5px;
  white-space: nowrap;
  /*border: 1px solid #282c34;*/
  /*color: rgb(184, 101, 208);*/
}

.markdown-section > div > img,
.markdown-section pre {
  box-shadow: 2px 2px 20px 6px rgb(255, 255, 255) !important;
}

.markdown-section a:not(:hover) {
  text-decoration: none;
}

/*侧边栏*/
.sidebar {
  padding-top: 6px;
}

aside.sidebar ul li {
  margin: 0;
  position: relative;
}

aside.sidebar ul li ul {
  margin: 6px 0;
}

aside.sidebar ul li p {
  padding-left: 22px;
  font-size: 18px;
  font-weight: normal;
}

aside.sidebar ul li a {
  line-height: 35px;
  font-size: 14px;
  padding: 3px 0 3px 22px;
}

aside.sidebar ul li.active > a {
  font-size: 16px !important;
}

aside.sidebar ul li.active > a:before {
  content: "" !important;
  position: absolute !important;
  margin: 0 !important;
  width: 10px !important;
  height: 10px !important;
  top: 15px !important;
  left: 0px !important;
  border-radius: 50% !important;
  background-color: #fed24a !important;
  box-shadow: 0 0 0 3px rgba(254, 210, 74, 0.4) !important;
}

/*以上调侧边栏*/

/*h1 span{
  display:inline-block;
  background: rgb(66, 185, 131);
  color:#ffffff;
  padding:  10px  16px;
  border-radius:5px;
  box-shadow: 1px 1px 3px black;
}*/
#main h2 span {
  color: #18b566 !important;
}

#main h3 span {
  color: #089acc !important;
}

#main h4 span {
  color: #ff9700 !important;
}

/*代码块背景*/
p code {
  background-color: rgb(255, 255, 255) !important;
}

.markdown-section p.tip,
.markdown-section tr:nth-child(1n) {
  font-size: 16px !important;
}

img,
pre {
  border-radius: 5px;
}

/*添加代码块复制按钮样式*/
.docsify-copy-code-button {
  background: #00a1d6 !important;
  color: #ffffff !important;
  font-size: 13px !important;
}

::after {
  color: #9da2fd !important;
  font-size: 13px !important;
}

.markdown-section > p {
  font-size: 16px !important;
}

/*代码块头部图标 start*/
.markdown-section pre:before {
  content: "";
  display: block;
  background: url(https://gitee.com/wugenqiang/PictureBed/raw/master/images01/20200716201955.png);
  height: 30px;
  background-size: 40px;
  background-repeat: no-repeat;
  background-color: #1c1c1c;
  background-position: 10px 10px;
}

/*代码块头部图标 end*/

.markdown-section pre > code {
  color: #c0c3c1 !important;
  font-family: "Inconsolata", consolas, "PingFang SC", "Microsoft YaHei",
    monospace !important;
  background-color: #212121 !important; //#accfff  #282c34
  font-size: 15px !important;
  white-space: pre !important;
  line-height: 1.5 !important;
  -moz-tab-size: 4 !important;
  -o-tab-size: 4 !important;
  tab-size: 4 !important;
  /*border-radius: .3em;*/
}

ol,
ul,
li {
  line-height: 27px !important;
  font-size: 16px !important;
}

@media (min-width: 600px) {
  .markdown-section pre > code {
    font-size: 15px !important;
    letter-spacing: 1.1px !important;
  }
}

@media (max-width: 600px) {
  .markdown-section pre > code {
    padding-top: 5px;
    padding-bottom: 5px;
    padding-left: 16px !important;
  }

  pre:after {
    content: "" !important;
  }
}

section.cover h1 {
  margin: 0;
}

pre {
  background-color: #212121 !important;
}

@media (min-width: 600px) {
  pre code {
    /*box-shadow: 2px 1px 20px 2px #aaa;*/
    /*border-radius: 10px !important;*/
    padding-left: 20px !important;
  }
}

@media (max-width: 600px) {
  pre {
    padding-left: 3px !important;
    padding-right: 3px !important;
    margin-left: -20px !important;
    margin-right: -20px !important;
    box-shadow: 0px 0px 20px 0px #f7f7f7 !important;
  }

  /*代码块复制按钮默认隐藏*/
  .docsify-copy-code-button {
    display: none;
  }

  .advertisement {
    display: none;
  }
}

.markdown-section pre {
  padding-left: 0 !important;
  padding-right: 0px !important;
}

.markdown-section {
  margin: 0 3.2% !important;
}

/*修改代码块代码颜色显示*/
.token.directive.keyword {
  color: #4faee2 !important;
}

.token.keyword {
  color: #c678dd !important;
}

.token.comment {
  color: #737c8b !important;
}

.token.tag {
  color: #a589ad !important;
}

.token.attr-name {
  color: #de916c !important;
}

.token.attr-value {
  color: #4faee2 !important;
}

.token.macro.property {
  color: #4faee2 !important;
}

.token.function {
  color: #e6b456 !important;
}

.token.string {
  color: #98b755 !important;
}

.token.punctuation {
  color: #c0c3c1 !important;
}

.token.number {
  color: #c0c3c1 !important;
}

a.section-link {
  font-size: 0.9rem !important;
}

.advertisement {
  position: fixed;
  right: 20px;
  top: 100px;
  width: 110px;
  box-shadow: -1px 0 2px 0px #c5ebda;
  padding: 10px;
  z-index: 99;
  background-color: #fff;
  text-align: center;
}

.advertisement p,
h4 {
  margin: 0;
  padding: 0;
}

.advertisement .Tencent_code h4 {
  font-size: 15px;
  color: #25a46a;
  margin-bottom: 10px;
}
```

现在给你目前的 `index.html`
**index.html**

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <title>Sixty's blog</title>
    <link
      rel="shortcut icon"
      href="https://lib.sixtyden.com/avatar_shuimen.pic.jpg"
      type="image/x-icon"
    />
    <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1" />
    <meta name="description" content="Description" />
    <meta
      name="viewport"
      content="width=device-width, initial-scale=1.0, minimum-scale=1.0"
    />
    <!-- vue主题样式 -->
    <link
      rel="stylesheet"
      href="//cdn.jsdelivr.net/npm/docsify@4/lib/themes/vue.css"
    />
    <!-- 暗黑主题插件样式 -->
    <link
      rel="stylesheet"
      href="//cdn.jsdelivr.net/npm/docsify-darklight-theme@latest/dist/style.min.css"
      title="docsify-darklight-theme"
      type="text/css"
    />
    <!-- 自定义的样式 -->
    <link rel="stylesheet" href="./index.css" type="text/css" />
    <link rel="stylesheet" href="./coverpage.css" type="text/css" />
  </head>

  <body>
    <div id="app">loading...</div>
    <script>
      window.$docsify = {
        basePath: "/", // 资源相对路径
        name: "<div class='app-name-link'><img class='title-logo' src='https://lib.sixtyden.com/avatar_shuimen.pic.jpg' /><div>活到老，学到老</div></div>", // 文档标题，会显示在侧边栏顶部
        nameLink: "/", // 点击文档标题后的跳转地址
        // logo: 'https://lib.sixtyden.com/avatar_shuimen.pic.jpg',
        repo: "hzjswlgbsj/blog", // 有repo属性则右上角会展示github图标，点击可进入
        loadSidebar: true, // 加载侧边栏
        externalLinkTarget: "_blank", // 外部链接的打开方式，默认_blank
        cornerExternalLinkTarget: "_blank", // 右上角链接的打开方式。默认为 _blank
        routerMode: "hash", // 路由方式 默认hash
        coverpage: true, // 展示封面
        onlyCover: true, // 只在访问主页时加载封面
        // topMargin: 90,
        notFoundPage: true, // 在找不到指定页面时加载_404.md
        auto2top: true, // 切换页面后是否自动跳转到页面顶部
        loadNavbar: true, // _navbar.md如果为真，则从_navbar.md文件加载navbar ，否则从指定的路径加载
        mergeNavbar: true,
        formatUpdated: "{MM}/{DD} {HH}:{mm}",
        search: {
          // 搜索插件配置
          paths: "auto",
          placeholder: "🔍 搜索",
          noData: "😞 没有结果!",
        },
        count: {
          // 字数统计插件配置
          countable: true,
          fontsize: "0.9em",
          color: "rgb(90,90,90)",
          language: "chinese",
        },
        pagination: {
          // 分页插件配置
          previousText: "上一章节",
          nextText: "下一章节",
          crossChapter: true,
        },
        footer: {
          copy: "生活不止眼前的代码，还有诗和远方的田野。 —— <a href='https://www.sixtyden.com' target='_blank'>Sixty's blog</a><br/><br/>",
          pre: "<hr/>",
          style: "text-align: left;",
        },
        plugins: [
          function (hook, vm) {
            hook.mounted(function () {
              // 主页判断是否显示topbar
              var appNavEl = document.querySelector(".app-nav");
              window.addEventListener(
                "hashchange",
                () => {
                  if (window.location.hash === "#/") {
                    appNavEl.style.display = "none";
                  } else {
                    appNavEl.style.display = "block";
                  }
                },
                false
              );
            });
            hook.doneEach(function () {
              function changeStyle() {
                var savedTheme = localStorage.getItem("DARK_LIGHT_THEME");
                console.log("现在的主题是", savedTheme, appNavEl);
                if (savedTheme === "dark") {
                  appNavEl.style.backgroundColor = "#091a28";
                  appNavEl.style.borderColor = "#0d2538";
                } else {
                  appNavEl.style.backgroundColor = "#ffffff";
                  appNavEl.style.borderColor = "#eee";
                }
              }
              var appNavEl = document.querySelector(".app-nav");
              changeStyle();
              document
                .getElementById("docsify-darklight-theme")
                .addEventListener("click", changeStyle);
            });
          },
        ],
        themeColor: "#42b983", // 暗黑模式主题色
        darklightTheme: {
          siteFont: "PT Sans",
          defaultTheme: "dark",
          codeFontFamily: "Roboto Mono, Monaco, courier, monospace",
          bodyFontSize: "17px",
          dark: {
            accent: "#42b983",
            toogleBackground: "#ffffff",
            background: "#091a28",
            textColor: "#b4b4b4",
            codeTextColor: "#ffffff",
            codeBackgroudColor: "#0e2233",
            borderColor: "#0d2538",
            blockQuoteColour: "#858585",
            highlightColor: "#e96900",
            sidebarSublink: "#b4b4b4",
            codeTypeColor: "#ffffff",
            coverBackground:
              "linear-gradient(to left bottom, hsl(118, 100%, 85%) 0%,hsl(181, 100%, 85%) 100%)",
            toogleImage:
              "url(https://cdn.jsdelivr.net/npm/docsify-darklight-theme@latest/icons/sun.svg)",
          },
          light: {
            accent: "#42b983",
            toogleBackground: "#091a28",
            background: "#ffffff",
            textColor: "#34495e",
            codeTextColor: "#525252",
            codeBackgroudColor: "#f8f8f8",
            borderColor: "rgba(0, 0, 0, 0.07)",
            blockQuoteColor: "#858585",
            highlightColor: "#e96900",
            sidebarSublink: "#b4b4b4",
            codeTypeColor: "#091a28",
            coverBackground:
              "linear-gradient(to left bottom, hsl(118, 100%, 85%) 0%,hsl(181, 100%, 85%) 100%)",
            toogleImage:
              "url(https://cdn.jsdelivr.net/npm/docsify-darklight-theme@latest/icons/moon.svg)",
          },
        },
      };
    </script>
  </body>

  <script src="//cdn.jsdelivr.net/npm/docsify@4"></script>
  <!--全局搜索插件-->
  <script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/search.min.js"></script>
  <!--字数统计插件-->
  <script src="//unpkg.com/docsify-count/dist/countable.js"></script>
  <!--代码段复制插件-->
  <script src="//cdn.jsdelivr.net/npm/docsify-copy-code"></script>
  <!--图片缩放插件-->
  <script src="//cdn.jsdelivr.net/npm/docsify/lib/plugins/zoom-image.min.js"></script>
  <!-- 阅读进度条插件 -->
  <script src="//unpkg.com/docsify-pagination/dist/docsify-pagination.min.js"></script>
  <!-- 暗黑主题切换插件 -->
  <script src="//cdn.jsdelivr.net/npm/docsify-darklight-theme@latest/dist/index.min.js"></script>
  <!-- 添加页脚 -->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/docsify-footer-enh.min.js"></script>
  <!-- 回到顶部功能 -->
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/jquery.js"></script>
  <script src="https://cdn.jsdelivr.net/gh/wugenqiang/NoteBook@master/plugin/jquery.goup.js"></script>
  <script type="text/javascript">
    $(document).ready(function () {
      $.goup({
        trigger: 100,
        bottomOffset: 52,
        locationOffset: 25,
        //title: 'TOP',
        titleAsText: true,
      });
    });
  </script>
</html>
```

## 增加 Topbar

首先在配置里开启 topbar，在上面的 `index.html` 中已经有相关代码

```javascript
<script>
  window.$docsify = {
    loadNavbar: true, // _navbar.md如果为真，则从_navbar.md文件加载navbar ，否则从指定的路径加载
  }
</script>
```

**\_navbar.md**

```markdown
- [<span class="iconfont icon-book3"></span> 首页](https://www.sixtyden.com)
- [<span class="iconfont icon-icon_fabu"></span> 导航](README.md)
  - [JavaScript 恶补基础](README?id=JavaScript_deep_learn)
  - [在线文档](README?id=document_online)
  - [技术杂文](README?id=blog)
  - [翻译](README?id=translation)
  - [Quill 文档翻译](README?id=quill_translate)
  - [算法](README?id=algorithm)
  - [设计模式](README?id=design_patterns)
  - [面试题](README?id=interview_questions)
- [<span class="iconfont icon-wodeguanzhu"></span> 关于本站](ABOUT.md)
```

## 改造 Sidebar

有两种方式，一种是自定义 sidebar 一种是直接使用根目录的 `README.md` 来做层级。

如果是自定义的话会占用侧边栏的「公共位置」，导致在阅读其他文章的时候目录跟住目录混合在一起，虽说支持多层级，但是看了效果后我不太喜欢。

如果是使用根目录的 `README.md` 的话，会自动解析 Markdown 文件中的标题层级，自动在侧边栏出现，并且在阅读其他文章的时候侧边栏会重新读取当前文章的标题结构重新生成。

这里演示两种

### 自定义侧边栏

首先在配置里开启自定义 sidebar，在上面的 `index.html` 中已经有相关代码

```javascript
<script>
  window.$docsify = {
    loadSidebar: true, // 如果为真，则从_sidebar.md文件加载侧边栏 ，否则从指定的路径加载
  }
</script>
```

**\_sidebar**

```markdown
- 恶补 JavaScript 基础
  - [理解 JavaScript 对象](/docs/javascript-base-learn/理解JavaScript对象)
  - [执行上下文](/docs/javascript-base-learn/执行上下文)
  - [变量对象](/docs/javascript-base-learn/变量对象)
  - [作用域链](/docs/javascript-base-learn/作用域链)
  - [this 关键字](/docs/javascript-base-learn/this关键字)
  - [理解闭包](/docs/javascript-base-learn/理解闭包)
  - [原型与原型链](/docs/javascript-base-learn/原型与原型链)
  - [创建对象的多种方式及其优缺点](/docs/javascript-base-learn/创建对象的多种方式及其优缺点)
  - [继承的多种方式及其优缺点](/docs/javascript-base-learn/继承的多种方式及其优缺点)
  - [事件循环机制](/docs/javascript-base-learn/事件循环机制)
  - [call 和 apply](/docs/javascript-base-learn/call和apply)
  - [bind 执行时发生了什么](/docs/javascript-base-learn/bind执行时发生了什么)
  - [new 执行时发生了什么](/docs/javascript-base-learn/new执行时发生了什么)
- [在线文档](docs/document-online/README)
  - [如何设计在线协同文档](/docs/document-online/如何设计在线协同文档)
```

### README 标题层级

**README.md**

```markdown
## 🌾 JavaScript 恶补基础

- [理解 JavaScript 对象](/docs/javascript-base-learn/理解JavaScript对象)
- [执行上下文](/docs/javascript-base-learn/执行上下文)
- [变量对象](/docs/javascript-base-learn/变量对象)
- [作用域链](/docs/javascript-base-learn/作用域链)
- [this 关键字](/docs/javascript-base-learn/this关键字)
- [理解闭包](/docs/javascript-base-learn/理解闭包)
- [原型与原型链](/docs/javascript-base-learn/原型与原型链)
- [创建对象的多种方式及其优缺点](/docs/javascript-base-learn/创建对象的多种方式及其优缺点)
- [继承的多种方式及其优缺点](/docs/javascript-base-learn/继承的多种方式及其优缺点)
- [事件循环机制](/docs/javascript-base-learn/事件循环机制)
- [call 和 apply](/docs/javascript-base-learn/call和apply)
- [bind 执行时发生了什么](/docs/javascript-base-learn/bind执行时发生了什么)
- [new 执行时发生了什么](/docs/javascript-base-learn/new执行时发生了什么)

## 🌿 在线文档

- [如何设计在线协同文档](/docs/document-online/如何设计在线协同文档)

## 🍀 技术杂文

- [使用七牛云来做 typora 的图床](docs/normal/使用七牛云来做typora的图床)
- [ssh 免密码登陆服务器](docs/normal/ssh免密码登陆服务器)

## 🍁 Quill 文档翻译

### Documentation

- [API](/docs/quill-translate/Documentation/API/README)
  - [content](/docs/quill-translate/Documentation/API/1.content)
  - [formatting](/docs/quill-translate/Documentation/API/2.formatting)
  - [selection](/docs/quill-translate/Documentation/API/3.selection)
  - [editor](/docs/quill-translate/Documentation/API/4.editor)
  - [events](/docs/quill-translate/Documentation/API/5.events)
  - [model](/docs/quill-translate/Documentation/API/6.model)
  - [extension](/docs/quill-translate/Documentation/API/7.extension)
- [MODULES](/docs/quill-translate/Documentation/MODULES/README)
  - [toolbar](/docs/quill-translate/Documentation/MODULES/1.toolbar)
  - [keyboard](/docs/quill-translate/Documentation/MODULES/2.keyboard)
  - [history](/docs/quill-translate/Documentation/MODULES/3.history)
  - [clipboard](/docs/quill-translate/Documentation/MODULES/4.clipboard)
  - [syntax](/docs/quill-translate/Documentation/MODULES/5.syntax)
- [quick-start](/docs/quill-translate/Documentation/1.quick-start)
- [download](/docs/quill-translate/Documentation/2.download)
- [configuration](/docs/quill-translate/Documentation/3.configuration)
- [formats](/docs/quill-translate/Documentation/4.formats)
- [delta](/docs/quill-translate/Documentation/5.delta)
- [themes](/docs/quill-translate/Documentation/6.themes)

### Guides

- [why-quill](/docs/quill-translate/Guides/1.why-quill)
- [how-to-customize-quill](/docs/quill-translate/Guides/2.how-to-customize-quill)
- [adding-quill-to-your-build-pipeline](/docs/quill-translate/Guides/3.adding-quill-to-your-build-pipeline)
- [build-a-custom-module](/docs/quill-translate/Guides/4.build-a-custom-module)
- [cloning-medium-with-parchment](/docs/quill-translate/Guides/5.cloning-medium-with-parchment)
- [designing-the-delta-format](/docs/quill-translate/Guides/6.designing-the-delta-format)
- [comparison-with-other-rich-text-editors](/docs/quill-translate/Guides/7.comparison-with-other-rich-text-editors)
- [upgrading-to-1.0](/docs/quill-translate/Guides/8.upgrading-to-1.0)
- [upgrading-to-2.0](/docs/quill-translate/Guides/9.upgrading-to-2.0)

### Parchment

- [parchment](/docs/quill-translate/Parchment/parchment)

## 🌾 翻译

## 🍃 算法

## 🍂 设计模式

## 🌴 面试题
```

如果确保我前面的配置和引用了完整的样式文件和插件的话现在应该是有点样子了。
