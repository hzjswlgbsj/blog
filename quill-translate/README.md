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

#### removeFormat

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

#### getBounds

检索给定位置处的像素位置（相对于编辑器容器）和选择的尺寸。 用户当前的选择不需要在该索引处。 用于计算工具提示的放置位置。

**Methods**

```javascript
getBounds(index: Number, length: Number = 0):
{ left: Number, top: Number, height: Number, width: Number }
```

**Examples**

```javascript
quill.setText("Hello\nWorld\n");
quill.getBounds(7); // Returns { height: 15, width: 0, left: 27, top: 31 }
```

#### getSelection

检索用户的选择范围，可选择首先聚焦编辑器。 否则，如果编辑器没有焦点，则可能返回`null`。

**Methods**

```javascript
getSelection(focus = false): { index: Number, length: Number }
```

**Examples**

```javascript
var range = quill.getSelection();
if (range) {
  if (range.length == 0) {
    console.log("User cursor is at index", range.index);
  } else {
    var text = quill.getText(range.index, range.length);
    console.log("User has highlighted: ", text);
  }
} else {
  console.log("User cursor is not in editor");
}
```

#### setSelection

将用户选择设置为给定范围，这也将使编辑器聚焦。 提供 null 作为选择范围将使编辑器失去焦点。[来源](https://quilljs.com/docs/api/#events)可以是`“user”`，`“api”`或`“silent”`。

**Methods**

```javascript
setSelection(index: Number, length: Number = 0, source: String = 'api')
setSelection(range: { index: Number, length: Number }, source: String = 'api')
```

**Examples**

```javascript
quill.setSelection(0, 5);
```

### [编辑器](/docs/quill-translate/Documentation/API/4.editor)

#### blur

从编辑器中删除焦点。

**Methods**

```javascript
blur();
```

**Examples**

```javascript
quill.blur();
```

#### enable

通过鼠标或键盘等输入设备设置用户编辑的能力。 当源为`“api”`或`“silent”`时，不影响 API 调用的功能。

**Methods**

```javascript
enable(((enabled: boolean) = true));
```

**Examples**

```javascript
quill.enable();
quill.enable(false); // Disables user input
```

#### disable

`enable(false)`的简写

#### focus

让编辑器获得焦点，并将光标至于最后。

**Methods**

```javascript
focus();
```

**Examples**

```javascript
quill.focus();
```

#### hasFocus

检查编辑器是否处于获取焦点状态。 注意注意工具栏，工具提示，不算作编辑器。

**Methods**

```javascript
hasFocus(): Boolean
```

**Examples**

```javascript
quill.hasFocus();
```

#### update

如果发生更改，请同步检查编辑器以获取用户更新并触发事件。 在解决冲突期间需要最新的最新状态的协作用例非常有用。[来源](https://quilljs.com/docs/api/#events) 可以是`“user”`，`“api”`或`“silent”`。

**Methods**

```javascript
update(((source: String) = "user"));
```

**Examples**

```javascript
quill.update();
```

### [事件](/docs/quill-translate/Documentation/API/5.events)

#### text-change

当 Quill 的内容发生变化时发出。 提供了更改的详细信息，更改前编辑器内容的表示以及更改的来源。 如果源来自用户，则源将是`“user”`。 例如：

- 用户输入编辑器

- 用户使用工具栏格式化文本

- 用户使用热键撤消

- 用户使用操作系统拼写纠正

可能通过 API 发生更改，但只要它们来自用户，所提供的源仍应为`“user”`。 例如，当用户单击工具栏时，技术上工具栏模块会调用 Quill API 来实现更改。 但是源仍然是`“user”`，因为更改的起源是用户的点击。

导致文本更改的 API 也可以使用`“silent”`源调用，在这种情况下，`text-change`不会被触发。 建议不要这样做，因为它可能会破坏撤消堆栈以及依赖于完整文本更改记录的其他功能。

对文本的更改可能会导致更改选择（例如，键入前进光标），但是在`text-change`处理程序期间，光标（selection）尚未更新，并且本机浏览器行为可能会将其置于不一致状态。 使用下文的`selection-change`或`editor-change`来获得可靠的选择更新。

**Callback Signature**

```javascript
handler(delta: Delta, oldContents: Delta, source: String)
```

**Examples**

```javascript
quill.on("text-change", function (delta, oldDelta, source) {
  if (source == "api") {
    console.log("An API call triggered this change.");
  } else if (source == "user") {
    console.log("A user action triggered this change.");
  }
});
```

#### selection-change

当用户或 API 导致选择更改时发出，其中范围表示选择边界。 空范围表示选择丢失（通常由编辑器失去焦点引起）。 您还可以通过检查发出的范围是否为空来将此事件用作焦点更改事件。

导致光标（selection）改变的 API 也可以用`“silent”`源调用，在这种情况下，不会触发`selection-change`。 如果`selection-change`是副作用，这将非常有用。 例如，键入会导致选择发生更改，但如果同时对每个字符都触发`selection-change`事件，则会产生非常大的噪声（副作用？低性能？）。

**Callback Signature**

```javascript
handler(range: { index: Number, length: Number },
        oldRange: { index: Number, length: Number },
        source: String)
```

**Examples**

```javascript
quill.on("selection-change", function (range, oldRange, source) {
  if (range) {
    if (range.length == 0) {
      console.log("User cursor is on", range.index);
    } else {
      var text = quill.getText(range.index, range.length);
      console.log("User has highlighted", text);
    }
  } else {
    console.log("Cursor not in the editor");
  }
});
```

#### editor-change

当事件`text-change`或`selection-change`被触发时，`editor-change`都会被触发，即使源是`“silent”`。第一个参数是`text-change`或`selection-change`的事件名，后跟通常传递给相应处理程序的参数。

**Callback Signature**

```javascript
handler(name: String, ...args)
```

**Examples**

```javascript
quill.on("editor-change", function (eventName, ...args) {
  if (eventName === "text-change") {
    // args[0] will be delta
  } else if (eventName === "selection-change") {
    // args[0] will be old range
  }
});
```

#### on

添加事件处理程序。 有关事件本身的更多详细信息，请参阅上文的`text-change`或`selection-change`。

**Methods**

```javascript
on(name: String, handler: Function): Quill
```

**Examples**

```javascript
quill.on("text-change", function () {
  console.log("Text change!");
});
```

#### once

添加一个只触发一次的事件处理程序。 有关事件本身的更多详细信息，请参阅上文的`text-change`或`selection-change`。

**Methods**

```javascript
once(name: String, handler: Function): Quill
```

**Examples**

```javascript
quill.once("text-change", function () {
  console.log("First text change!");
});
```

#### off

删除事件处理程序。

**Methods**

```javascript
off(name: String, handler: Function): Quill
```

**Examples**

```javascript
function handler() {
  console.log("Hello!");
}

quill.on("text-change", handler);
quill.off("text-change", handler);
```

### [模型](/docs/quill-translate/Documentation/API/6.model)

> 注意：本节的 api 都是实验性的。

#### find

静态方法返回给定 DOM 节点的 Quill 或 [Blot](https://github.com/quilljs/parchment) 实例。 在后一种情况下，为`bubble`参数传入 true 将搜索给定 DOM 的祖先，直到找到相应的 [Blot](https://github.com/quilljs/parchment)。

**Methods**

```javascript
Quill.find(domNode: Node, bubble: boolean = false): Blot | Quill
```

**Examples**

```javascript
var container = document.querySelector("#container");
var quill = new Quill(container);
console.log(Quill.find(container) === quill); // Should be true

quill.insertText(0, "Hello", "link", "https://world.com");
var linkNode = document.querySelector("#container a");
var linkBlot = Quill.find(linkNode);
```

#### getIndex

返回文档开头与给定 [Blot](https://github.com/quilljs/parchment) 出现之间的距离。

**Methods**

```javascript
getIndex(blot: Blot): Number
```

**Examples**

```javascript
let [line, offset] = quill.getLine(10);
let index = quill.getIndex(line); // index + offset should == 10
```

#### getLeaf

返回文档中指定索引处的叶 [Blot](https://github.com/quilljs/parchment)。

**Methods**

```javascript
getLeaf(index: Number): Blot
```

**Examples**

```javascript
quill.setText("Hello Good World!");
quill.formatText(6, 4, "bold", true);

let [leaf, offset] = quill.getLeaf(7);
// leaf should be a Text Blot with value "Good"
// offset should be 1, since the returned leaf started at index 6
```

#### getLine

返回文档中指定索引处的 Blot[Blot](https://github.com/quilljs/parchment)行。

**Methods**

```javascript
getLine(index: Number): [Blot, Number]
```

**Examples**

```javascript
quill.setText("Hello\nWorld!");

let [line, offset] = quill.getLine(7);
// line should be a Block Blot representing the 2nd "World!" line
// offset should be 1, since the returned line started at index 6
```

#### getLines

返回指定位置中包含的行。

**Methods**

```javascript
getLines(index: Number = 0, length: Number = remaining): Blot[]
getLines(range: Range): Blot[]
```

**Examples**

```javascript
quill.setText("Hello\nGood\nWorld!");
quill.formatLine(1, 1, "list", "bullet");

let lines = quill.getLines(2, 5);
// array with a ListItem and Block Blot,
// representing the first two lines
```

### [扩展](/docs/quill-translate/Documentation/API/7.extension)

#### debug

允许在给定级别记录消息的静态方法：`'error'`，`'warn'`，`'log'`或`'info'`。 传递 true 相当于传递`'log'`。 传递`false`会禁用所有消息。

**Methods**

```javascript
Quill.debug(level: String | Boolean)
```

**Examples**

```javascript
Quill.debug("info");
```

#### import

返回 Quill 库，格式，模块或主题的静态方法。 通常，路径应该精确映射到 Quill 源代码目录结构。 除非另有说明，否则对返回实体的修改可能会破坏所需的 Quill 功能，因此强烈建议不要这样做。

**Methods**

```javascript
Quill.import(path): any
```

**Examples**

```javascript
var Parchment = Quill.import("parchment");
var Delta = Quill.import("delta");

var Toolbar = Quill.import("modules/toolbar");
var Link = Quill.import("formats/link");
// Similar to ES6 syntax `import Link from 'quill/formats/link';`
```

#### register

注册模块，主题或格式，使其可以添加到编辑器中。以后可以使用`Quill.import`检索。使用`'formats/'`，`'modules/'`或`'themes/'`的路径前缀分别注册格式，模块或主题。对于特定格式，缩写为直接传递格式，路径将自动生成。 将使用相同的路径覆盖现有定义。

**Methods**

```javascript
Quill.register(format: Attributor | BlotDefinintion, supressWarning: Boolean = false)
Quill.register(path: String, def: any, supressWarning: Boolean = false)
Quill.register(defs: { [String]: any }, supressWarning: Boolean = false)
```

**Examples**

```javascript
var Module = Quill.import("core/module");

class CustomModule extends Module {}

Quill.register("modules/custom-module", CustomModule);
```

```javascript
Quill.register({
  "formats/custom-format": CustomFormat,
  "modules/custom-module-a": CustomModuleA,
  "modules/custom-module-b": CustomModuleB,
});

Quill.register(CustomFormat);
// You cannot do Quill.register(CustomModuleA); as CustomModuleA is not a format
```

#### addContainer

在 Quill 容器中添加并返回一个容器元素，同步到编辑器本身。按照惯例，Quill 模块应该有一个前缀为`ql-`的类名。可选地包括一个 refNode，其中应该插入容器。

**Methods**

```javascript
addContainer(className: String, refNode?: Node): Element
addContainer(domNode: Node, refNode?: Node): Element
```

**Examples**

```javascript
var container = quill.addContainer("ql-custom");
```

#### getModule

检索已添加到编辑器的模块。

**Methods**

```javascript
getModule(name: String): any
```

**Examples**

```javascript
var toolbar = quill.getModule("toolbar");
```

#### enable

通过鼠标或键盘等输入设备设置用户编辑的能力。 当源为`“api”`或`“silent”`时，不影响 API 调用的功能。

**Methods**

```javascript
enable(((enabled: boolean) = true));
```

**Examples**

```javascript
quill.enable();
quill.enable(false); // Disables user input
```

#### disable

`enable(false)`的简写

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
