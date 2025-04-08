# Quill 编辑器架构

## 富文本编辑器的实现方式

可以看一下我之前总结的关于如何实现一个富文本编辑器的文章：[自己实现一个富文本编辑器](https://www.sixtyden.com/blog/articleDetail/34)

总结一下主要有以下两个方向：

1. 使用`document.execCommand`、`contenteditable`属性、Selection 对象、Rang 对象
2. 自己抽象`数据模型`和`文档模型`。（参考[1]）

## Quill 的基本结构

### Quill 的思路

quill 的实现主要是走的第二个方向，Selection 对象和 Rang 对象是自己封装了一下原生的。自己实现了 `Parchment` 来做文档模型，用来做 `DOM` 抽象层，可以简单理解为 `virtual dom` ，但实际上它是直接对应过去的，`Blot`是 Parchment 的基本组成部分。提供了几个最基本的实现：

- **Block Blot**：块类型格式化 Blot 的基本实现。默认格式的块级 Blot 会替代 Blot 的适当部分。

- **Inline Blot**：行级格式化 Blot 的基本实现。默认格式的行级 Blot 或者用一个 Blot 包裹自己，或者将它传递给合适的子节点。

- **Embed Blot**：非文本节点的基本实现，可以被格式化。其对应的额 DOM 节点通常是一个 [Void 元素](https://html.spec.whatwg.org/multipage/syntax.html#void-elements)，也可以是一个 [正常元素](https://html.spec.whatwg.org/multipage/syntax.html#normal-elements)。在这些情况下，Parchment 将不会操作或者感知到元素的子元素，正确的执行 Blot 的`index()`和`position()`方法对于正确的光标显示/选区是很重要的。

- **Scroll**：Parchment 文档的根节点。不能够被格式化。

- **Attributors**：Attributors 是一种轻量级的格式化方式。它们的 DOM 对应的是 [属性(Attribute)](https://html.spec.whatwg.org/multipage/syntax.html#attributes-0)。像 DOM 属性和节点的关系一样，Attributors 也属于 Blots。调用 [Inline](/docs/quill-translate/Parchment/parchment?id=inline-blot) 或者 [Block](docs/quill-translate/Parchment/parchment?id=block-blot) Blot 的`formats()`方法将会返回相应的 DOM 节点的格式（如果有的话）以及 DOM 节点属性表示的格式（如果有的话）。

quill 还实现了 `Dalta` 来做数据模型，将编辑器的产出相比其他编辑器多一种固定的数据结构 ：

```javascript
interface Op {
  // only one property out of {insert, delete, retain} will be present
  insert?: string | object;
  delete?: number;
  retain?: number;
  attributes?: AttributeMap;
}
ops: Op[]
```

在下文我们会详细介绍 `Parchment` 和 `Delta` 。

### Quill 目录结构

```javascript
src
├── assets          # icon和一些样式
├── blot            # 基于 Parchment 实现的一些blot结构
    ├── block       # 块级结构
    ├── break       # br的实现
    ├── container   # 容器结构
    ├── cursor      # 游标类型，非法字符的控制
    ├── embed       # 嵌入式结构
    ├── inline      # 行内结构
    ├── scroll      # quill根结构
    ├── text        # 文本结构
├── core            # 核心逻辑
    ├── editor      # 块级结构
    ├── emmiter     # 封装eventemitter3
    ├── instances   # 初始就是一个weakMap
    ├── logger      # 处理打印
    ├── module      # 定义模块的基类
    ├── quill       # 初始化所有功能点，提供核心api，事件、格式、模块、编辑器。。。
    ├── selection   # 处理光标，病监听光标事件
    ├── theme       # 定义主题的基本结构，theme/base.js会继承它
├── formats         # 继承blot而来的更加细粒度的格式
    ├── aligin      # 对齐方式
    ├── background  # 背景
    ├── blockquote  # 注解块
    ├── bold        # 加粗
    ├── code        # 代码块
    ├── color       # 颜色
    ├── direction   # 方向
    ├── font        # 字体
    ├── formula     # 公式
    ├── header      # 标题
    ├── image       # 图片
    ├── indent      # 缩进
    ├── italic      # 倾斜
    ├── link        # 链接
    ├── list        # 列表
    ├── script      # 脚标
    ├── size        # 字体大小
    ├── strike      # 文本删除线对应s标签
    ├── table       # 表格
    ├── underline   # 文本下划线
    ├── video       # 视频
├── module          # 核心功能模块
    ├── clipboard   # 剪切板，监听原生的copy、cut、paste事件并做处理
    ├── history     # 实现两个栈来粉笔诶存放redo、undo
    ├── keyboard    # 处理快捷键
    ├── syntax      # 处理代码语法，配合highlight.js提供高亮
    ├── table       # 表格模块，暂时不可用
    ├── toolbar     # 提供toolbar的基本事件监听，监听select和button两种元素，监听change和click事件
    ├── uploader    # 处理上传
├── theme           # 主题
    ├── base        # 主题的基类
    ├── snow        # snow主题
    ├── bubble      # bubble主题
```

用一个脑图来直观的看一下 quill 的结构：

![quill结构](https://lib.sixtyden.com/quill%E7%BB%93%E6%9E%84%20.png)

### Core（核心逻辑）

#### editor

主要实现编辑器底层的 api，包括`insert`、`delete`、`format` 以及 `update`等。这边的 api 产出都是基于 `Delta` 的。除了 `get` 类型的方法以外，每一个 api 都会去调用 `update()` 方法，并返回它产生的 `change（delta）`。

#### quill

根目录的`quill.js`和 `core.js`会引用`core/quill.js` ，并在`core/quill.js`中的构造函数执行后，注册 module、blot、formate 等最终返回`Quill`对象供使用。可以看出`core/quill.js` 就是整个编辑器的入口文件，它主要做了以下几件事：

- 初始化配置文件

- 创建编辑器根目录。

- 创建事件监听器。

- 初始化模块。

- 提供模块、 blot、format 等的导入导出功能。

- 实现底层 API 操作。

#### emmitor

处理编辑器内部的事件监听。Quill 的事件系统是使用了 `eventemitter3` 这个库，在`eventemitter3` 的基础之上，它自定了几种事件类型，这些事件就是我们在最外层能拿来做业务的事件。

此外，还在此直接监听了四种原生的事件，`selectionchange` 、`mousedown`、`mouseup`、`click`

emmitor 还实现了`handleDOM`和`listenDOM`，来专门监听和处理这几种原生事件。比如 `theme/base` 使用`listenDOM`来监听点击事件，实现了 toolber 的`tooltip`和`pickers`；`core/selection.js`就主要监听`selectionchange`,然后执行`update`，最终实现 `SELECTION_CHANGE` 的 `EDITOR_CHANGE`事件。

编辑器自定义的内部事件，会在下文的事件流中详细讲解。

#### logger

封装 `console.log`，跟中台的 Log 是差不多的东西。

#### module

比较小粒度并且跟 DOM 联系紧密的功能一般做在 blot 里面，但是比较独立的功能性逻辑，比如上传图片功能，就会被封装成模块。这里的 `module.js 主要提供模块的基类，自定义的模块都应该继承这个基类。

#### selection

封装原生的 `selection`对象 和 `Range`对象，维护编辑器的光标相关操作。

#### theme

处理主题相关逻辑，主题注册、主题自定义，实现主题的基类。

### quill 实现的基本 blots

quill 在 `Parchment` 提供的基本 blot 的基础之上实现了更多的 blot 类型。

- block - 块级结构
- break - br 的实现
- container - 容器结构
- cursor - 游标类型，非法字符的控制
- embed - 嵌入式结构
- inline - 行内结构
- scroll - quill 根结构
- text - 文本结构

### quill 的基本格式（format）

format 是在 blot 的基础之上实现出来的具体的**格式**，这些格式就相当于原生的`document.execCommand`执行的命令，比如加粗、倾斜等等。

format 实际上就是一种格式的定义，他会按照实际情况实现一些基础的方法，比如 `link` 超链接这种格式，他需要创建一个 `<a>` ，所以他需要实现一个 `create` 方法来创建这个 DOM ，他还需要实现 `format`方法用于执行格式化的动作，所有的 format 都是这样的结构，下面是超链接的实现：

```javascript
import Inline from '../blots/inline';

class Link extends Inline {
  public static blotName = 'link';
  public static tagName = 'A';
  public static SANITIZED_URL = 'about:blank';
  public static PROTOCOL_WHITELIST = ['http', 'https', 'mailto', 'tel'];

  public static create(value: any) {
    const node: any = super.create(value);
    node.setAttribute('href', this.sanitize(value));
    node.setAttribute('rel', 'noopener noreferrer');
    node.setAttribute('target', '_blank');
    return node;
  }

  public static formats(domNode: any) {
    return domNode.getAttribute('href');
  }

  public static sanitize(url: any) {
    return sanitize(url, this.PROTOCOL_WHITELIST) ? url : this.SANITIZED_URL;
  }

  public format(name: any, value: any) {
    if (name !== this.statics.blotName || !value) {
      super.format(name, value);
    } else {
      this.domNode.setAttribute('href', (this.constructor as any).sanitize(value));
    }
  }
}


function sanitize(url: any, protocols: any) {
  const anchor = document.createElement('a');
  anchor.href = url;
  const protocol = anchor.href.slice(0, anchor.href.indexOf(':'));
  return protocols.indexOf(protocol) > -1;
}

export { Link as default, sanitize };

```

### 模块（modules）

#### clipboard module

剪切板，监听原生的 copy、cut、paste 事件并做处理。

每次剪切、粘贴的时候都会触发对应的处理，他们会维护一个`matchers`，的数组，他只能解析出 quill 支持的类型。然后转化为 `Delta`, 然后使用 `updateContents()` 来粘贴内容。

关于粘贴图片，他在这边也做了处理，使得实现粘贴图片然后自定义处理方式变得简单。

#### history module

实现两个栈来粉笔诶存放 redo、undo。

这边主要是监听`TEXT_CHANGE`事件类型，来讲对应的`Delat`放到栈里面去。实现的比较简单，但是它在维护 redo、undo 栈的时候还做了一些合并操作。所以我们会有一个感觉就是回退可能不会很精细，这使得我们如果要历史记录功能变得蛋疼。

#### keyboard module

处理快捷键。嗯，就是处理快捷键，然后调用`core/quill` 里面的 API。

#### syntax module

处理代码语法，配合 highlight.js 提供高亮。

#### toolbar module

提供 toolbar 的基本事件监听，监听`select`和`button`两种元素，监听 change 和 click 事件。

由于 snow 主题的限制，这边的实现将操作限制在了`select`和`button`两种元素上。它提供创建这两种元素的 API，可以看到原生的 toolbar 上面只有按钮和下拉框，在创建按钮的时候为这些元素都加上了`ql-${format}`的 css 类名，所以这些按钮每个都对应了一种 format，点击后触发对应的事件，最终实现样式的改变。

我个人觉得这样设计限制比较大，修改起样式也太过于酸爽。实际的项目中可能 toolbar 会比较复杂，所以在在线文档项目中我就直接抛弃了原生的 toolbar，自己搞了一个。嗯，舒服多了...

#### uploader module

目前为止 quill 仅仅支持上传图片类型的文件，并且只支持 png 和 jpeg：

```javascript
mimetypes: ["image/png", "image/jpeg"];
```

然后处理图片也非常的简洁，就有一个 handleer，用 promise 返回了图片的 base64，根本没给自己上传的接口。所以我自己加一个上传的模块，但是并没有在源码里面实例化，而是在编辑器实例化 quill 的时候在配置文件中实例化了，因为这边要接上传服务，不能用业务代码污染编辑器里面。

### 主题（theme）

主题这边其实没啥好说的， 其实就是结合 `toolbar` 模块，做一些外观设计，然后去调用 `core/quill` 里面的 api 就好了。 他实现了一个`base.js` 基类倒是在自定义主题的时候有点用。

目前 quill 支持两种主题：bubble 和 snow，前者就是沉浸式的，没有 toolbar，根据 selection 来出现小浮框，后者就是传统的富文本编辑器都有的 toolbar。

## Quill 的 DOM 文档流

在这里我将讲述 quill 从根目录到第一个节点是怎么产生的。

首先，在 `core/quill.js` 会根据我们传入的一个 DOM 来作为编辑器的容器，然后会根据 `scroll`类型的 blot 来创建根 blot。`scroll` blot 会创建一个 `div`的节点，并使用 `contenteditable` 属性来控制当前环境是否可编辑。自此，一个可以编辑的根节点就创建完成了。

然后，以后出现的每一个动作都会使用 [MutationObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver) 这个 API 来完成。MutationObserver 接口提供了监视对 DOM 树所做更改的能力。[MutationObserver()](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver/MutationObserver)构造函数创建并返回一个新的 `MutationObserver` 它会在指定的 DOM 发生变化时被调用。

当`MutationObserver.observe()` 收到 DOM 的变化之后，便会触发回调。Parchment 中的 Scroll blot 做了 MutationObserver 的相关配置。他会在回调触发的时候，调用它的`update`方法，然后最终在`parent`中使用原生的`insertBefore()` 方法创建出 DOM。

一个 blot 的继承路径大概是这样（暂时没有事件画类图，我以后会补上去的），以 `scroll` blot 来举例子：

```javascript
Scroll -> parchment/ScrollBlot -> parchment/ParentBlot -> parchment/ShadowBlot -> ShadowBlot/Blot
```

一个 format 的继承路径大概是这样（暂时没有事件画类图，我以后会补上去的），以 `link` format 来举例子：

```javascript
Link -> quill/Inline -> parchment/Inline -> parchment/ParentBlot -> parchment/ShadowBlot -> ShadowBlot/Blot
```

## Quill 的事件流

在上面的文档流中我们提到的 [MutationObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver) ，整个 quill 内部的 dom 变化都是依赖这个 API 来实现，在`quill/blot/scroll`中的 `update()` 方法中会监听一个`SCROLL_UPDATE`, 这个事件代表，作为根 blot 的 scroll 发生了变化：

```javascript
public update(mutations?: any) {
  if (this.batch) {
    if (Array.isArray(mutations)) {
      this.batch = (this.batch as any[]).concat(mutations);
    }
    return;
  }
  let source = Emitter.sources.USER;
  if (typeof mutations === 'string') {
    source = mutations;
  }
  if (!Array.isArray(mutations)) {
    mutations = this.observer.takeRecords();
  }
  if (mutations.length > 0) {
    this.emitter.emit(Emitter.events.SCROLL_BEFORE_UPDATE, source, mutations);
  }
  super.update(mutations.concat([])); // pass copy
  if (mutations.length > 0) {
    this.emitter.emit(Emitter.events.SCROLL_UPDATE, source, mutations); // 这就是整个变化触发的地方
  }
}
```

监听这个事件的地方就是在 `core/quill` 的构造函数里面，一旦触发便会执行 `modify()` 方法，并在 `modify()` 的回调函数中执行 `update ()` 方法。

```javascript
this.emitter.on(Emitter.events.SCROLL_UPDATE, (source, mutations) => {
  const oldRange = this.selection.lastRange;
  const [newRange] = this.selection.getRange();
  const selectionInfo: any =
    oldRange && newRange ? { oldRange, newRange } : undefined;
  this.modify(() => this.editor.update(null, mutations, selectionInfo), source);
});
```

`update()`会返回一个 `change` 给 `modify()`，这个 change 就是 `Delta`。在`modify()` 中会触发`EDITOR_CHANGE`事件，根据情况会触发 `TEXT_CHANGE` 事件，这就是 quill 最外层的事件了，我们丛业务都是依赖的这些最外层的事件。

在 `core/quill` 中实现的所有涉及到 DOM 变化的操作都会调用这个`modify()`方法，以此来抛出事件。

## Parchment

关于 Parchment 的设计，我们可以看一下 [这里](/docs/quill-translate/Parchment/parchment)。

## Delta

关于 Delta 的设计，我们可以看一下 [这里](/docs/quill-translate/Documentation/5.delta) 和 [这里](/docs/quill-translate/Guides/6.designing-the-delta-format)。

## 相关链接

1. [假如自己实现一个富文本编辑器会发生什么](https://www.sixtyden.com/blog/articleDetail/34)
2. [Quill 中文文档（包含 parchment）](https://github.com/hzjswlgbsj/quill-document-chinese/blob/master/README.md)
3. [eventemitter3](https://github.com/primus/eventemitter3)
4. [MutationObserver](https://developer.mozilla.org/zh-CN/docs/Web/API/MutationObserver)
