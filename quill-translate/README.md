[Quill](https://quilljs.com/) 编辑器官方文档的翻译，还包含 [Parchment](https://github.com/quilljs/parchment) 和 [Delta](https://github.com/quilljs/delta)

# 文档

## API

### [内容](/docs/quill-translate/Documentation/API/1.content)

#### deleteText

从编辑器中删除文本，返回表示更改的 [Delta](/docs/quill-translate/Guides/6.designing-the-delta-format)。 [来源](/docs/quill-translate/Documentation/API/5.events.md)可以是`“user”`，`“api”`或`“silent”`。 [禁用](/docs/quill-translate/Documentation/API/5.events.md?id=disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

**Methods**

```javascript
deleteText(index: Number, length: Number, source: String = 'api'): Delta
```

**Examples**

```javascript
quill.deleteText(6, 4);
```

#### getContents

检索编辑器的内容，以及由 [Delta](/docs/quill-translate/Guides/6.designing-the-delta-format) 对象表示的格式化数据。

**Methods**

```javascript
getContents(index: Number = 0, length: Number = remaining): Delta
```

**Examples**

```javascript
var delta = quill.getContents();
```

#### getLength

检索编辑器内容的长度。 请注意，即使 Quill 为空，仍有一个由'\n'表示的空行，因此`getLength`将返回 1。

**Methods**

```javascript
getLength(): Number
```

**Examples**

```javascript
var length = quill.getLength();
```

#### insertEmbed

将 embedded 的内容插入编辑器，返回表示更改的 [Delta](/docs/quill-translate/Guides/6.designing-the-delta-format)。 [来源](/docs/quill-translate/Documentation/API/5.events.md)可以是`“user”`，`“api”`或`“silent”`。 [禁用](/docs/quill-translate/Documentation/API/5.events.md?id=disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

**Methods**

```javascript
insertEmbed(index: Number, type: String, value: any, source: String = 'api'): Delta
```

**Examples**

```javascript
quill.insertEmbed(10, "image", "https://quilljs.com/images/cloud.png");
```

#### insertText

将文本插入编辑器，可选择使用指定格式或多种格式。返回表示更改的[Delta](/docs/quill-translate/Guides/6.designing-the-delta-format)。 [来源](/docs/quill-translate/Documentation/API/5.events.md)可以是`“user”`，`“api”`或`“silent”`。 [禁用](/docs/quill-translate/Documentation/API/5.events.md?id=disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

**Methods**

```javascript
insertText(index: Number, text: String, source: String = 'api'): Delta
insertText(index: Number, text: String, format: String, value: any,
           source: String = 'api'): Delta
insertText(index: Number, text: String, formats: { [String]: any },
           source: String = 'api'): Delta
```

**Examples**

```javascript
quill.insertText(0, "Hello", "bold", true);

quill.insertText(5, "Quill", {
  color: "#ffff00",
  italic: true,
});
```

#### setContents

用给定内容覆盖编辑器。 内容应以 [换行符](https://quilljs.com/docs/delta/#line-formatting) 结尾。 返回表示更改的 [Delta](/docs/quill-translate/Guides/6.designing-the-delta-format)。 如果 Delta 没有无效操作，这将与 Delta 传入相同。 [来源](/docs/quill-translate/Documentation/API/5.events.md)可以是`“user”`，`“api”`或`“silent”`。 [禁用](/docs/quill-translate/Documentation/API/5.events.md?id=disable) 编辑器时，`“source”`为`“user”`的调用将被忽略。

**Methods**

```javascript
setContents(delta: Delta, source: String = 'api'): Delta
```

**Examples**

```javascript
quill.setContents([
  { insert: "Hello " },
  { insert: "World!", attributes: { bold: true } },
  { insert: "\n" },
]);
```

#### setText

用给定文本设置编辑器的内容，返回表示更改的 [Delta](/docs/quill-translate/Guides/6.designing-the-delta-format)。 注意 Quill 文档必须以换行符结尾，因此如果省略，将添加一个换行符。 [来源](/docs/quill-translate/Documentation/API/5.events.md)可以是`“user”`，`“api”`或`“silent”`。 [禁用](/docs/quill-translate/Documentation/API/5.events.md?id=disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

**Methods**

```javascript
setText(text: String, source: String = 'api'): Delta
```

**Examples**

```javascript
quill.setText("Hello\n");
```

#### updateContents

将 Delta 应用于编辑器内容，返回表示更改的 [Delta](/docs/quill-translate/Guides/6.designing-the-delta-format)。如果 Delta 传入没有无效操作，这些 Deltas 将是相同的。[来源](/docs/quill-translate/Documentation/API/5.events.md)可以是`“user”`，`“api”`或`“silent”`。 [禁用](/docs/quill-translate/Documentation/API/5.events.md?id=disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

**Methods**

```javascript
updateContents(delta: Delta, source: String = 'api'): Delta
```

**Examples**

```javascript
// Assuming editor currently contains [{ insert: 'Hello World!' }]
quill.updateContents(
  new Delta()
    .retain(6) // Keep 'Hello '
    .delete(5) // 'World' is deleted
    .insert("Quill")
    .retain(1, { bold: true }) // Apply bold to exclamation mark
);
// Editor should now be [
//  { insert: 'Hello Quill' },
//  { insert: '!', attributes: { bold: true} }
// ]
```

### [格式化](/docs/quill-translate/Documentation/API/2.formatting)

#### format

格式化文本在用户当前选择，返回表示更改的 [Delta](/docs/quill-translate/Guides/6.designing-the-delta-format)。 如果用户的选择长度为 0，即它是光标，则格式将被设置为活动，因此用户键入的下一个字符将具有该格式。[来源](/docs/quill-translate/Documentation/API/5.events.md)可以是`“user”`，`“api”`或`“silent”`。 [禁用](/docs/quill-translate/Documentation/API/5.events.md?id=disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

**Methods**

```javascript
format(name: String, value: any, source: String = 'api'): Delta
```

**Examples**

```javascript
quill.format("color", "red");
quill.format("align", "right");
```

#### formatLine

格式化给定范围内的所有行，返回表示更改的 [Delta](/docs/quill-translate/Guides/6.designing-the-delta-format)。请参阅[可用格式列表](https://quilljs.com/docs/formats/)的格式。 使用内联格式调用时无效。 要删除格式，请为 value 参数传递`false`。 用户的选择可能不会被保留。[来源](/docs/quill-translate/Documentation/API/5.events.md)可以是`“user”`，`“api”`或`“silent”`。[禁用](/docs/quill-translate/Documentation/API/5.events.md?id=disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

**Methods**

```javascript
formatLine(index: Number, length: Number, source: String = 'api'): Delta
formatLine(index: Number, length: Number, format: String, value: any, source: String = 'api'): Delta
formatLine(index: Number, length: Number, formats: { [String]: any }, source: String = 'api'): Delta
```

**Examples**

```javascript
quill.setText("Hello\nWorld!\n");
quill.formatLine(1, 2, "align", "right"); // right aligns the first line
quill.formatLine(4, 4, "align", "center"); // center aligns both lines
```

#### formatText

在编辑器中格式化文本，返回表示更改的 [Delta](/docs/quill-translate/Guides/6.designing-the-delta-format)。对于行级格式（如文本对齐），请定位换行符或使用上面的`formatLine`帮助完成。请参阅 [可用格式列表](https://quilljs.com/docs/formats/) 的格式。要删除格式，请为 value 参数传递`false`。 用户的选择可能不会被保留。[来源](/docs/quill-translate/Documentation/API/5.events.md)可以是`“user”`，`“api”`或`“silent”`。 [禁用](/docs/quill-translate/Documentation/API/5.events.md?id=disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

**Methods**

```javascript
formatText(index: Number, length: Number, source: String = 'api'): Delta
formatText(index: Number, length: Number, format: String, value: any, source: String = 'api'): Delta
formatText(index: Number, length: Number, formats: { [String]: any }, source: String = 'api'): Delta
```

**Examples**

```javascript
quill.setText("Hello\nWorld!\n");
quill.formatText(0, 5, "bold", true); // bolds 'hello'
quill.formatText(0, 5, {
  // unbolds 'hello' and set its color to blue
  bold: false,
  color: "rgb(0, 0, 255)",
});

quill.formatText(5, 1, "align", "right"); // right aligns the 'hello' line
```

#### getFormat

检索给定范围内文本的常见格式。 对于要报告的格式，范围内的所有文本都必须具有真值。 如果存在不同的真值，则将报告具有所有真值的数组。 如果未提供范围，则使用用户的当前选择范围。 可用于显示光标上已设置的格式。 如果不带参数调用，则将使用用户的当前选择范围。

**Methods**

```javascript
getFormat(range: Range = current): { [String]: any }
getFormat(index: Number, length: Number = 0): { [String]: any }

```

**Examples**

```javascript
quill.setText("Hello World!");
quill.formatText(0, 2, "bold", true);
quill.formatText(1, 2, "italic", true);
quill.getFormat(0, 2); // { bold: true }
quill.getFormat(1, 1); // { bold: true, italic: true }

quill.formatText(0, 2, "color", "red");
quill.formatText(2, 1, "color", "blue");
quill.getFormat(0, 3); // { color: ['red', 'blue'] }

quill.setSelection(3);
quill.getFormat(); // { italic: true, color: 'blue' }

quill.format("strike", true);
quill.getFormat(); // { italic: true, color: 'blue', strike: true }

quill.formatLine(0, 1, "align", "right");
quill.getFormat(); // { italic: true, color: 'blue', strike: true, align: 'right' }
```

## removeFormat

删除给定范围内的所有格式和嵌入，返回表示更改的 [Delta](/docs/quill-translate/Guides/6.designing-the-delta-format)。如果行的任何部分包含在范围内，则将删除行格式。 用户的选择可能不会被保留。[来源](/docs/quill-translate/Documentation/API/5.events.md)可以是`“user”`，`“api”`或`“silent”`。 [禁用](/docs/quill-translate/Documentation/API/5.events.md?id=disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

**Methods**

```javascript
removeFormat(index: Number, length: Number, source: String = 'api'): Delta
```

**Examples**

```javascript
quill.setContents([
  { insert: 'Hello', { bold: true } },
  { insert: '\n', { align: 'center' } },
  { insert: { formula: 'x^2' } },
  { insert: '\n', { align: 'center' } },
  { insert: 'World', { italic: true }},
  { insert: '\n', { align: 'center' } }
]);

quill.removeFormat(3, 7);
// Editor contents are now
// [
//   { insert: 'Hel', { bold: true } },
//   { insert: 'lo\n\nWo' },
//   { insert: 'rld', { italic: true }},
//   { insert: '\n', { align: 'center' } }
// ]
```

### [光标选区](/docs/quill-translate/Documentation/API/3.selection)

- [selection](/docs/quill-translate/Documentation/API/3.selection)

### [编辑器](/docs/quill-translate/Documentation/API/4.editor)

- [editor](/docs/quill-translate/Documentation/API/4.editor)

### [事件](/docs/quill-translate/Documentation/API/5.events)

- [events](/docs/quill-translate/Documentation/API/5.events)

### [模型](/docs/quill-translate/Documentation/API/6.model)

- [model](/docs/quill-translate/Documentation/API/6.model)

### [扩展](/docs/quill-translate/Documentation/API/7.extension)

- [extension](/docs/quill-translate/Documentation/API/7.extension)

## 模块

### [工具栏](/docs/quill-translate/Documentation/MODULES/1.toolbar)

- [toolbar](/docs/quill-translate/Documentation/MODULES/1.toolbar)

### [键盘](/docs/quill-translate/Documentation/MODULES/2.keyboard)

- [keyboard](/docs/quill-translate/Documentation/MODULES/2.keyboard)

### [历史](/docs/quill-translate/Documentation/MODULES/3.history)

- [history](/docs/quill-translate/Documentation/MODULES/3.history)

### [剪切板](/docs/quill-translate/Documentation/MODULES/4.clipboard)

- [clipboard](/docs/quill-translate/Documentation/MODULES/4.clipboard)

### [语法](/docs/quill-translate/Documentation/MODULES/5.syntax)

- [syntax](/docs/quill-translate/Documentation/MODULES/5.syntax)

## [快速开始](/docs/quill-translate/Documentation/1.quick-start)

- [quick-start](/docs/quill-translate/Documentation/1.quick-start)

## [下载](/docs/quill-translate/Documentation/2.download)

- [download](/docs/quill-translate/Documentation/2.download)

## [配置](/docs/quill-translate/Documentation/3.configuration)

- [configuration](/docs/quill-translate/Documentation/3.configuration)

## [格式](/docs/quill-translate/Documentation/4.formats)

- [formats](/docs/quill-translate/Documentation/4.formats)

## [delta](/docs/quill-translate/Documentation/5.delta)

- [delta](/docs/quill-translate/Documentation/5.delta)

## [主题](/docs/quill-translate/Documentation/6.themes)

- [themes](/docs/quill-translate/Documentation/6.themes)

# 指南

## [为什么是 quill](/docs/quill-translate/Guides/1.why-quill)

- [why-quill](/docs/quill-translate/Guides/1.why-quill)

## [如何定制化 quill](/docs/quill-translate/Guides/2.how-to-customize-quill)

- [how-to-customize-quill](/docs/quill-translate/Guides/2.how-to-customize-quill)

## [添加 quill 到应用](/docs/quill-translate/Guides/3.adding-quill-to-your-build-pipeline)

- [adding-quill-to-your-build-pipeline](/docs/quill-translate/Guides/3.adding-quill-to-your-build-pipeline)

## [构建自定义模块](/docs/quill-translate/Guides/4.build-a-custom-module)

- [build-a-custom-module](/docs/quill-translate/Guides/4.build-a-custom-module)

## [用 parchment 克隆 medium](/docs/quill-translate/Guides/5.cloning-medium-with-parchment)

- [cloning-medium-with-parchment](/docs/quill-translate/Guides/5.cloning-medium-with-parchment)

## [设计 Delta 的 format](/docs/quill-translate/Guides/6.designing-the-delta-format)

- [designing-the-delta-format](/docs/quill-translate/Guides/6.designing-the-delta-format)

## [对比其他编辑器](/docs/quill-translate/Guides/7.comparison-with-other-rich-text-editors)

- [comparison-with-other-rich-text-editors](/docs/quill-translate/Guides/7.comparison-with-other-rich-text-editors)

## [升级 1.0](/docs/quill-translate/Guides/8.upgrading-to-1.0)

- [upgrading-to-1.0](/docs/quill-translate/Guides/8.upgrading-to-1.0)

## [升级 2.0](/docs/quill-translate/Guides/9.upgrading-to-2.0)

- [upgrading-to-2.0](/docs/quill-translate/Guides/9.upgrading-to-2.0)

# Parchment

- [parchment](/docs/quill-translate/Parchment/parchment)
