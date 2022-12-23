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

工具栏模块允许用户轻松格式化 Quill 的内容。

它可以配置自定义容器和处理程序。

```javascript
var quill = new Quill("#editor", {
  modules: {
    toolbar: {
      container: "#toolbar", // Selector for toolbar container
      handlers: {
        bold: customBoldHandler,
      },
    },
  },
});
```

由于`container`选项很常见，因此也允许使用顶级简写。

```javascript
var quill = new Quill("#editor", {
  modules: {
    // Equivalent to { toolbar: { container: '#toolbar' }}
    toolbar: "#toolbar",
  },
});
```

#### Container

工具栏控件可以由简单的格式名称数组或自定义 HTML 容器指定。

从更简单的数组选项开始:

```javascript
var toolbarOptions = ["bold", "italic", "underline", "strike"];

var quill = new Quill("#editor", {
  modules: {
    toolbar: toolbarOptions,
  },
});
```

控件也可以按嵌套数组的一个级别进行分组。 这将使用类名称`ql-formats`将控件包装在`<span>`中，从而为要使用的主题提供结构。 例如，[Snow](https://quilljs.com/docs/themes/#snow/)在控件组之间添加了额外的间距。

```javascript
var toolbarOptions = [
  ["bold", "italic"],
  ["link", "image"],
];
```

可以使用 Object 指定具有自定义值的按钮，并将格式名称作为其唯一键。

```javascript
var toolbarOptions = [{ header: "3" }];
```

下拉列表类似地由 Object 指定，但具有可能值的数组。 CSS 用于控制下拉选项的可视标签。

```javascript
// 注意false，而不是'normal'，是正确的值quill.format（'size'，false）删除格式，允许默认样式工作
var toolbarOptions = [{ size: ["small", false, "large", "huge"] }];
```

注意 [Themes](https://quilljs.com/docs/themes/)还可以指定下拉列表的默认值。 例如，如果设置为空数组，则 [Snow](https://quilljs.com/docs/themes/#snow/)会为`color`和`background`格式提供 35 种颜色的默认列表。

```javascript
var toolbarOptions = [
  ["bold", "italic", "underline", "strike"], // toggled buttons
  ["blockquote", "code-block"],

  [{ header: 1 }, { header: 2 }], // custom button values
  [{ list: "ordered" }, { list: "bullet" }],
  [{ script: "sub" }, { script: "super" }], // superscript/subscript
  [{ indent: "-1" }, { indent: "+1" }], // outdent/indent
  [{ direction: "rtl" }], // text direction

  [{ size: ["small", false, "large", "huge"] }], // custom dropdown
  [{ header: [1, 2, 3, 4, 5, 6, false] }],

  [{ color: [] }, { background: [] }], // dropdown with defaults from theme
  [{ font: [] }],
  [{ align: [] }],

  ["clean"], // remove formatting button
];

var quill = new Quill("#editor", {
  modules: {
    toolbar: toolbarOptions,
  },
  theme: "snow",
});
```

对于需要更多自定义的用例，您可以在 HTML 中手动创建工具栏，并将 DOM 元素或选择器传递给 Quill。`ql-toolbar`类将添加到工具栏容器中，Quill 将适当的处理程序附加到`<button>`和`<select>`元素，其类名称为`ql-${format}`。Buttons 元素可以选择具有自定义`value`属性。

```javascript
<!-- Create toolbar container -->
<div id="toolbar">
  <!-- Add font size dropdown -->
  <select class="ql-size">
    <option value="small"></option>
    <!-- Note a missing, thus falsy value, is used to reset to default -->
    <option selected></option>
    <option value="large"></option>
    <option value="huge"></option>
  </select>
  <!-- Add a bold button -->
  <button class="ql-bold"></button>
  <!-- Add subscript and superscript buttons -->
  <button class="ql-script" value="sub"></button>
  <button class="ql-script" value="super"></button>
</div>
<div id="editor"></div>

<!-- Initialize editor with toolbar -->
<script>
  var quill = new Quill('#editor', {
    modules: {
      toolbar: '#toolbar'
    }
  });
</script>
```

请注意，通过提供您自己的 HTML 元素，Quill 会搜索特定的输入元素，但您自己的输入与 Quill 无关，仍然可以添加和设置样式并共存。

```javascript
<div id="toolbar">
  <!-- Add buttons as you would before -->
  <button class="ql-bold"></button>
  <button class="ql-italic"></button>

  <!-- But you can also add your own -->
  <button id="custom-button"></button>
</div>
<div id="editor"></div>

<script>
var quill = new Quill('#editor', {
  modules: {
    toolbar: '#toolbar'
  }
});

var customButton = document.querySelector('#custom-button');
customButton.addEventListener('click', function() {
  console.log('Clicked!');
});
</script>
```

#### Handlers

默认情况下，工具栏控件应用并删除格式，但您也可以使用自定义处理程序覆盖它，例如为了显示外部 UI。

处理程序函数将绑定到工具栏（因此使用`this`将引用工具栏实例）并在相应格式处于非活动状态时传递输入的`value`属性，否则传递`false`。 添加自定义处理程序将覆盖默认工具栏和主题行为。

```javascript
var toolbarOptions = {
  handlers: {
    // handlers object will be merged with default handlers object
    link: function (value) {
      if (value) {
        var href = prompt("Enter the URL");
        this.quill.format("link", href);
      } else {
        this.quill.format("link", false);
      }
    },
  },
};

var quill = new Quill("#editor", {
  modules: {
    toolbar: toolbarOptions,
  },
});

// Handlers can also be added post initialization
var toolbar = quill.getModule("toolbar");
toolbar.addHandler("image", showImageUI);
```

### [键盘](/docs/quill-translate/Documentation/MODULES/2.keyboard)

键盘模块为特定上下文中的键盘事件启用自定义行为。 Quill 使用它来绑定格式化热键并防止不良的浏览器副作用。

#### Key Bindings

键盘处理程序绑定到特定键和键修饰符。 `key`是 JavaScript 事件键代码，但字母数字键和一些常用键允许使用字符串短序。

键修饰符包括：`metaKey`，`ctrlKey`，`shiftKey` 和 `altKey`。 此外，`shortKey`是一个平台特定的修饰符，相当于 Mac 上的`metaKey` 和 Linux 和 Windows 上的`ctrlKey`。

处理程序被调用时，`this`会被绑定到键盘实例，同时还会注入当前的光标对象。

```javascript
quill.keyboard.addBinding(
  {
    key: "B",
    shortKey: true,
  },
  function (range, context) {
    this.quill.formatText(range, "bold", true);
  }
);

// addBinding may also be called with one parameter,
// in the same form as in initialization
quill.keyboard.addBinding({
  key: "B",
  shortKey: true,
  handler: function (range, context) {},
});
```

如果修饰键为`false`，则假定修饰符不活动。 您也可以传递`null`以表示修饰符的任何值。

```javascript
// Only b with no modifier will trigger
quill.keyboard.addBinding({ key: "B" }, handler);

// Only shift+b will trigger
quill.keyboard.addBinding({ key: "B", shiftKey: true }, handler);

// Either b or shift+b will trigger
quill.keyboard.addBinding({ key: "B", shiftKey: null }, handler);
```

多个处理程序可以绑定到相同的键和修饰符组合。 处理程序将按照绑定的顺序同步调用。 默认情况下，处理程序停止传播到下一个处理程序，除非它显式返回`true`。

```javascript
quill.keyboard.addBinding({ key: "tab" }, function (range) {
  // I will normally prevent handlers of the tab key
  // Return true to let later handlers be called
  return true;
});
```

注意：由于 Quill 的默认处理程序是在初始化时添加的，因此阻止它们的唯一方法是在 [配置](https://quilljs.com/docs/modules/keyboard/#configuration) 中添加你的处理程序。

#### Context

上下文允许仅在特定场景中调用处理程序的进一步规范。无论是否指定了上下文，都将提供上下文对象作为所有处理程序的第二个参数。

```javascript
// If the user hits backspace at the beginning of list or blockquote,
// remove the format instead delete any text
quill.keyboard.addBinding(
  { key: Keyboard.keys.BACKSPACE },
  {
    collapsed: true,
    format: ["blockquote", "list"],
    offset: 0,
  },
  function (range, context) {
    if (context.format.list) {
      this.quill.format("list", false);
    } else {
      this.quill.format("blockquote", false);
    }
  }
);
```

**collapsed**

如果为`true`，则仅在用户的选择被折叠时（即以光标形式）调用处理程序。 如果为`false`，则用户的选择长度必须为非零，例如当用户突出显示文本时。

**empty**

如果为`true`，则仅在用户选择空行时调用，对于非空行调用`false`。 注意将 empty 设置为 true 意味着 collapsed 也为 true 且 offset 为 0 - 否则用户的选择将不在空行上。

```javascript
// If the user hits enter on an empty list, remove the list instead
quill.keyboard.addBinding(
  { key: Keyboard.keys.ENTER },
  {
    empty: true, // implies collapsed: true and offset: 0
    format: ["list"],
  },
  function (range, context) {
    this.quill.format("list", false);
  }
);
```

**format**

当一个 Array，如果任何指定的格式处于活动状态，将调用处理程序。 当一个 Object 时，必须满足所有指定的格式条件。 在任何一种情况下，context 参数的 format 属性都是所有当前活动格式的 Object，与`quill.getFormat()`返回的格式相同。

```javascript
var context = {
  format: {
    list: true, // must be on a list, but can be any value
    script: "super", // must be exactly 'super', 'sub' will not suffice
    link: false, // cannot be in any link
  },
};
```

**offset**

只有当用户选择从行首开始`offset`字符时才会调用处理程序。 请注意，这是在应用可打印键之前。 这与其他上下文规范结合使用非常有用。

**prefix**

正则表达式必须与用户选择的开始位置之前的文本匹配。 文本将不符合交叉格式边界。 提供的`context.prefix`值将是整个前一个文本，而不仅仅是正则表达式匹配。

```javascript
// When the user types space...
quill.keyboard.addBinding(
  { key: " " },
  {
    collapsed: true,
    format: { list: false }, // ...on an line that's not already a list
    prefix: /^-$/, // ...following a '-' character
    offset: 1, // ...at the 1st position of the line,
    // otherwise handler would trigger if the user
    // typed hyphen+space mid sentence
  },
  function (range, context) {
    // the space character is consumed by this handler
    // so we only need to delete the hyphen
    this.quill.deleteText(range.index - 1, 1);
    // apply bullet formatting to the line
    this.quill.formatLine(range.index, 1, "list", "bullet");
    // restore selection
    this.quill.setSelection(range.index - 1);

    // console.log(context.prefix) would print '-'
  }
);
```

**suffix**

与上文的`prefix`相同，但紧跟用户选择结束位置后的匹配文本除外。

#### Configuration

默认情况下，Quill 附带了几个有用的键绑定，例如使用制表符缩进列表。 您可以在启动时添加自己的。

某些绑定对于防止危险的浏览器默认值（例如 enter 和 backspace 键）至关重要。您无法删除这些绑定以恢复到本机浏览器行为。但是，由于配置中指定的绑定将在 Quill 的默认值之前运行，因此您可以处理特殊情况并以其他方式传播到 Quill。

使用`quill.keyboard.addBinding`添加绑定将不会在 Quill 之前运行，因为该点将添加默认绑定。

每个绑定配置必须包含`key`和`handler`选项，并且可以选择包含任何`context`选项。

```javascript
var bindings = {
  // This will overwrite the default binding also named 'tab'
  tab: {
    key: 9,
    handler: function () {
      // Handle tab
    },
  },

  // There is no default binding named 'custom'
  // so this will be added without overwriting anything
  custom: {
    key: "B",
    shiftKey: true,
    handler: function (range, context) {
      // Handle shift+b
    },
  },

  list: {
    key: "backspace",
    format: ["list"],
    handler: function (range, context) {
      if (context.offset === 0) {
        // When backspace on the first character of a list,
        // remove the list instead
        this.quill.format("list", false, Quill.sources.USER);
      } else {
        // Otherwise propogate to Quill's default
        return true;
      }
    },
  },
};

var quill = new Quill("#editor", {
  modules: {
    keyboard: {
      bindings: bindings,
    },
  },
});
```

#### Performance

与 DOM 事件一样，Quill 键绑定是对每个匹配的阻塞调用，因此对于非常常见的键绑定而言，拥有一个非常昂贵的处理程序是一个坏主意。 应用与附加到常见阻塞 DOM 事件（如`scroll`或`mousemove`）时相同的性能最佳实践。

### [历史](/docs/quill-translate/Documentation/MODULES/3.history)

历史记录模块负责处理 Quill 的撤消和重做。 它可以配置以下选项：

#### Configuration

**delay**

- Default: `1000`

在延迟毫秒数内发生的更改将合并为单个更改。

例如，将延迟设置为`0`，几乎每个字符都记录为一次更改，因此撤消将一次撤消一个字符。 延迟设置为`1000`后，撤消将撤消在过去 1000 毫秒内发生的所有更改。

**maxStack**

- Default: `100`

历史记录的撤消/重做堆栈的最大大小。 具有`delay`选项的合并更改计为单个更改。

**userOnly**

- Default: `false`

默认情况下，所有更改（无论是源自用户输入还是通过 API 以编程方式）都将被视为相同，并且更改将由历史记录模块撤消或重做。 如果`userOnly`设置为`true`，则只撤消或重做用户更改。

Example

```javascript
var quill = new Quill("#editor", {
  modules: {
    history: {
      delay: 2000,
      maxStack: 500,
      userOnly: true,
    },
  },
  theme: "snow",
});
```

#### API

**clear**

清除历史堆栈。

Methods

```javascript
clear();
```

Examples

```javascript
quill.history.clear();
```

**cutoff(该 API 在实验中)**

通常，短时间连续发生的更改（由`delay`配置）将合并为单个更改，因此触发撤消将撤消多个更改。使用`cutoff()`将重置合并窗口，以便调用`cutoff()`之前和之后的更改将不会合并。

Methods

```javascript
cutoff();
```

Examples

```javascript
quill.history.cutoff();
```

**cutoff**

撤消上次更改。

Methods

```javascript
undo();
```

Examples

```javascript
quill.history.undo();
```

**redo**

如果上次更改是撤消操作，请重做此撤消操作。 否则什么都不做。

Methods

```javascript
redo();
```

Examples

```javascript
quill.history.redo();
```

### [剪切板](/docs/quill-translate/Documentation/MODULES/4.clipboard)

剪贴板处理 Quill 和外部应用程序之间的复制，剪切和粘贴。 存在一组默认值以提供对粘贴内容的合理解释，并具有通过匹配器进一步自定义的能力。

剪贴板通过在 [后序遍历](https://zh.wikipedia.org/wiki/%E6%A0%91%E7%9A%84%E9%81%8D%E5%8E%86) 相应的 DOM 树来解释粘贴的 HTML，从而构建所有子树的 [Delta](https://quilljs.com/docs/delta/) 表示。在每个后代节点，到目前为止，使用 DOM 节点和 Delta 解释调用匹配器函数，允许匹配器返回修改的 Delta 解释。

为了有效地使用匹配器，[Deltas](https://quilljs.com/docs/delta)的熟悉和舒适是必要的。

#### API

**addMatcher**

将自定义匹配器添加到剪贴板。使用`nodeType`的匹配器按照添加的顺序首先被调用，然后是使用 CSS`selector`的匹配器，也按照添加的顺序。 [nodeType](https://developer.mozilla.org/en-US/docs/Web/API/Node/nodeType)可以是`Node.ELEMENT_NODE`或`Node.TEXT_NODE`。

Methods

```javascript
addMatcher(selector: String, (node: Node, delta: Delta) => Delta)
addMatcher(nodeType: Number, (node: Node, delta: Delta) => Delta)
```

Examples

```javascript
quill.clipboard.addMatcher(Node.TEXT_NODE, function (node, delta) {
  return new Delta().insert(node.data);
});

// Interpret a <b> tag as bold
quill.clipboard.addMatcher("B", function (node, delta) {
  return delta.compose(new Delta().retain(delta.length(), { bold: true }));
});
```

**dangerouslyPasteHTML**

将 HTML 代码段表示的内容插入到给定索引的编辑器中。该代码段由剪贴板 [匹配器](https://quilljs.com/docs/modules/clipboard/#addMatcher) 解释，它可能不会生成准确的输入 HTML。如果没有提供插入索引，则会覆盖整个编辑器内容。[源](https://quilljs.com/docs/api/#events)可以是`“user”`，`“api”`或`“silent”`。

不正确的 HTML 处理可能导致跨站点脚本（XSS）和无法正确清理，这是众所周知的容易出错和导致 Web 漏洞的主要原因。此方法遵循 React 的示例，并且恰当地命名以确保开发人员已采取必要的预防措施。

Methods

```javascript
dangerouslyPasteHTML(html: String, source: String = 'api')
dangerouslyPasteHTML(index: Number, html: String, source: String = 'api')
```

Examples

```javascript
quill.setText("Hello!");

quill.clipboard.dangerouslyPasteHTML(5, "&nbsp;<b>World</b>");
// Editor is now '<p>Hello&nbsp;<strong>World</strong>!</p>';
```

#### Configuration

**matchers**

可以将一系列匹配器传递到 Clipboard 的配置选项中。 这些将在 Quill 自己的默认匹配器后附加。

```javascript
var quill = new Quill("#editor", {
  modules: {
    clipboard: {
      matchers: [
        ["B", customMatcherA],
        [Node.TEXT_NODE, customMatcherB],
      ],
    },
  },
});
```

### [语法](/docs/quill-translate/Documentation/MODULES/5.syntax)

语法模块通过自动检测和应用语法突出显示来增强代码块格式。优秀的 [highlight.js](https://highlightjs.org/) 库被用作解析和标记代码块的依赖项。

通常，您可以根据需要 [configure](https://highlightjs.readthedocs.io/en/latest/api.html#configure-options) highlight.js。 但是，Quill 期望并要求`useBR`选项为`false`。

**Example**

```html
<!-- Include your favorite highlight.js stylesheet -->
<link href="highlight.js/monokai-sublime.min.css" rel="stylesheet" />

<!-- Include the highlight.js library -->
<script href="highlight.js"></script>

<script>
  hljs.configure({
    // optionally configure hljs
    languages: ["javascript", "ruby", "python"],
  });

  var quill = new Quill("#editor", {
    modules: {
      syntax: true, // Include syntax module
      toolbar: [["code-block"]], // Include button in toolbar
    },
    theme: "snow",
  });
</script>
```

## [快速开始](/docs/quill-translate/Documentation/1.quick-start)

最好的入门方法是尝试一个简单的例子。 使用 DOM 元素初始化 Quill 以包含编辑器。 该元素的内容将成为 Quill 的初始内容。

```javascript
<!-- Include stylesheet -->
<link href="https://cdn.quilljs.com/1.3.6/quill.snow.css" rel="stylesheet">

<!-- Create the editor container -->
<div id="editor">
  <p>Hello World!</p>
  <p>Some initial <strong>bold</strong> text</p>
  <p><br></p>
</div>

<!-- Include the Quill library -->
<script src="https://cdn.quilljs.com/1.3.6/quill.js"></script>

<!-- Initialize Quill editor -->
<script>
  var quill = new Quill('#editor', {
    theme: 'snow'
  });
</script>
```

这就是它的全部！

### 下一步

Quill 真正的神奇之处在于其灵活性和可扩展性。 您可以通过在整个站点中播放演示或直接访问 [Interactive Playground](https://quilljs.com/playground/)来了解可能的内容。 有关深入演练，请参阅 [如何自定义 Quill](/docs/quill-translate/Guides/2.how-to-customize-quill)。

## [下载](/docs/quill-translate/Documentation/2.download)

Quill 准备好以几种方便的形式使用。

### CDN

提供全球分布式可用 CDN，由 [Render](https://render.com/) 提供支持。

```javascript
<!-- Main Quill library -->
<script src="//cdn.quilljs.com/1.3.6/quill.js"></script>
<script src="//cdn.quilljs.com/1.3.6/quill.min.js"></script>

<!-- Theme included stylesheets -->
<link href="//cdn.quilljs.com/1.3.6/quill.snow.css" rel="stylesheet">
<link href="//cdn.quilljs.com/1.3.6/quill.bubble.css" rel="stylesheet">

<!-- Core build with no theme, formatting, non-essential modules -->
<link href="//cdn.quilljs.com/1.3.6/quill.core.css" rel="stylesheet">
<script src="//cdn.quilljs.com/1.3.6/quill.core.js"></script>
```

### NPM

将 Quill 添加为 [NPM](https://www.npmjs.com/) 依赖项并将其添加到您自己的构建工作流中，或使用包含的构建选项。 `dist/`文件夹中也包含已编译的样式表。

```javascript
npm install quill@1.3.6
```

### 直接下载

Quill 的各个版本也提供直接 [下载](https://github.com/quilljs/quill/releases/tag/v1.3.6)。

### 源码

当然，完整的源代码总是可以在 [Github](https://github.com/quilljs/quill)上获得。

```javascript
git clone git@github.com:quilljs/quill.git
```

## [配置](/docs/quill-translate/Documentation/3.configuration)

Quill 允许多种方式来定制它以满足您的需求。 本节致力于调整现有功能。 有关添加新功能的信息，请参阅 [模块](/docs/quill-translate/Documentation/MODULES) 部分;有关样式，请参阅 [主题](/docs/quill-translate/Documentation/6.themes) 部分。

### 容器

Quill 需要一个容器，其中将附加编辑器。 您可以传入 CSS 选择器或 DOM 对象。

```javascript
var editor = new Quill(".editor"); // First matching element will be used
```

```javascript
var container = document.getElementById("editor");
var editor = new Quill(container);
```

```javascript
var container = $(".editor").get(0);
var editor = new Quill(container);
```

### 可选项

要配置 Quill，请传入一个 options 对象:

```javascript
var options = {
  debug: "info",
  modules: {
    toolbar: "#toolbar",
  },
  placeholder: "Compose an epic...",
  readOnly: true,
  theme: "snow",
};
var editor = new Quill("#editor", options);
```

识别以下键：

#### bounds

默认: `document.body`

DOM 元素或 DOM 元素的 CSS 选择器，其中应限制编辑器的 ui 元素（即工具提示等）。 目前，它只考虑左右边界。

#### debug

默认: `warn`

[调试](/docs/quill-translate/Documentation/API/7.extension?id=debug)的快捷方式。 注意 debug 是一种静态方法，会影响页面上其他 Quill 编辑器的实例。 默认情况下仅启用警告和错误消息。

#### formats

默认: All formats

在编辑器中允许[格式](/docs/quill-translate/Documentation/4.formats?id=的白名单。 有关完整列表，请参阅格式。

#### modules

包含模块的集合和各自的选项。 有关更多信息，请参阅 [模块](/docs/quill-translate/Documentation/modules/MODULES).

#### placeholder

默认: None

占位符文本，以显示编辑器何时为空。

#### readOnly

默认: `false`

是否将编辑器实例化为只读模式。

#### scrollingContainer

默认: `null`

DOM 元素或 DOM 元素的 CSS 选择器，指定哪个容器具有滚动条（即`overflow-y：auto`），if 是否已使用自定义 CSS 从默认`ql-editor`更改。 当 Quill 设置为 [自动增长](https://quilljs.com/playground/#autogrow)其高度时，必须修复滚动跳跃错误，并且滚动时负责另一个祖先容器。

#### theme

要使用的主题的名称。 内置选项是“泡沫”或“雪”。 无效或有价值的值将加载默认的最小主题。 请注意，主题的特定样式表仍需要手动包含。 有关更多信息，请参阅 [主题](/docs/quill-translate/Documentation/6.themes)。

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
