# Clipboard Module
剪贴板处理Quill和外部应用程序之间的复制，剪切和粘贴。 存在一组默认值以提供对粘贴内容的合理解释，并具有通过匹配器进一步自定义的能力。

剪贴板通过在[后序遍历](https://zh.wikipedia.org/wiki/%E6%A0%91%E7%9A%84%E9%81%8D%E5%8E%86)相应的DOM树来解释粘贴的HTML，从而构建所有子树的[Delta](https://quilljs.com/docs/delta/)表示。在每个后代节点，到目前为止，使用DOM节点和Delta解释调用匹配器函数，允许匹配器返回修改的Delta解释。

为了有效地使用匹配器，[Deltas](https://quilljs.com/docs/delta)的熟悉和舒适是必要的。

## API
### addMatcher
将自定义匹配器添加到剪贴板。使用`nodeType`的匹配器按照添加的顺序首先被调用，然后是使用CSS`selector`的匹配器，也按照添加的顺序。 [nodeType](https://developer.mozilla.org/en-US/docs/Web/API/Node/nodeType)可以是`Node.ELEMENT_NODE`或`Node.TEXT_NODE`。

### Methods
```javascript
addMatcher(selector: String, (node: Node, delta: Delta) => Delta)
addMatcher(nodeType: Number, (node: Node, delta: Delta) => Delta)
```

### Examples
```javascript
quill.clipboard.addMatcher(Node.TEXT_NODE, function(node, delta) {
  return new Delta().insert(node.data);
});

// Interpret a <b> tag as bold
quill.clipboard.addMatcher('B', function(node, delta) {
  return delta.compose(new Delta().retain(delta.length(), { bold: true }));
});
```

### dangerouslyPasteHTML
将HTML代码段表示的内容插入到给定索引的编辑器中。该代码段由剪贴板[匹配器](https://quilljs.com/docs/modules/clipboard/#addMatcher)解释，它可能不会生成准确的输入HTML。如果没有提供插入索引，则会覆盖整个编辑器内容。[源](https://quilljs.com/docs/api/#events)可以是`“user”`，`“api”`或`“silent”`。

不正确的HTML处理可能导致跨站点脚本（XSS）和无法正确清理，这是众所周知的容易出错和导致Web漏洞的主要原因。此方法遵循React的示例，并且恰当地命名以确保开发人员已采取必要的预防措施。

### Methods
```javascript
dangerouslyPasteHTML(html: String, source: String = 'api')
dangerouslyPasteHTML(index: Number, html: String, source: String = 'api')
```

### Examples
```javascript
quill.setText('Hello!');

quill.clipboard.dangerouslyPasteHTML(5, '&nbsp;<b>World</b>');
// Editor is now '<p>Hello&nbsp;<strong>World</strong>!</p>';
```

## Configuration
### matchers
可以将一系列匹配器传递到Clipboard的配置选项中。 这些将在Quill自己的默认匹配器后附加。
```javascript
var quill = new Quill('#editor', {
  modules: {
    clipboard: {
      matchers: [
        ['B', customMatcherA],
        [Node.TEXT_NODE, customMatcherB]
      ]
    }
  }
});
```