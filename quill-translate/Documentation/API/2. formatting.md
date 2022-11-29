# Formatting
## format
格式化文本在用户当前选择，返回表示更改的[Delta](https://quilljs.com/guides/designing-the-delta-format/)。 如果用户的选择长度为0，即它是光标，则格式将被设置为活动，因此用户键入的下一个字符将具有该格式。[来源](https://quilljs.com/docs/api/#events)可以是`“user”`，`“api”`或`“silent”`。 [禁用](https://quilljs.com/docs/api/#disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

### Methods
```javascript
format(name: String, value: any, source: String = 'api'): Delta
```

### Examples
```javascript
quill.format('color', 'red');
quill.format('align', 'right');
```

## formatLine
格式化给定范围内的所有行，返回表示更改的[Delta](https://quilljs.com/guides/designing-the-delta-format/)。请参阅[可用格式列表](https://quilljs.com/docs/formats/)的格式。 使用内联格式调用时无效。 要删除格式，请为value参数传递`false`。 用户的选择可能不会被保留。[来源](https://quilljs.com/docs/api/#events)可以是`“user”`，`“api”`或`“silent”`。[禁用](https://quilljs.com/docs/api/#disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

### Methods
```javascript
formatLine(index: Number, length: Number, source: String = 'api'): Delta
formatLine(index: Number, length: Number, format: String, value: any,
           source: String = 'api'): Delta
formatLine(index: Number, length: Number, formats: { [String]: any },
           source: String = 'api'): Delta
```

### Examples
```javascript
quill.setText('Hello\nWorld!\n');

quill.formatLine(1, 2, 'align', 'right');   // right aligns the first line
quill.formatLine(4, 4, 'align', 'center');  // center aligns both lines
```

## formatText
在编辑器中格式化文本，返回表示更改的[Delta](https://quilljs.com/guides/designing-the-delta-format/)。对于行级格式（如文本对齐），请定位换行符或使用上面的`formatLine`帮助完成。请参阅[可用格式列表](https://quilljs.com/docs/formats/)的格式。要删除格式，请为value参数传递`false`。 用户的选择可能不会被保留。[来源](https://quilljs.com/docs/api/#events)可以是`“user”`，`“api”`或`“silent”`。 [禁用](https://quilljs.com/docs/api/#disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

### Methods
```javascript
formatText(index: Number, length: Number, source: String = 'api'): Delta
formatText(index: Number, length: Number, format: String, value: any,
           source: String = 'api'): Delta
formatText(index: Number, length: Number, formats: { [String]: any },
           source: String = 'api'): Delta
```

### Examples
```javascript
quill.setText('Hello\nWorld!\n');

quill.formatText(0, 5, 'bold', true);      // bolds 'hello'

quill.formatText(0, 5, {                   // unbolds 'hello' and set its color to blue
  'bold': false,
  'color': 'rgb(0, 0, 255)'
});

quill.formatText(5, 1, 'align', 'right');  // right aligns the 'hello' line
```

## getFormat
检索给定范围内文本的常见格式。 对于要报告的格式，范围内的所有文本都必须具有真值。 如果存在不同的真值，则将报告具有所有真值的数组。 如果未提供范围，则使用用户的当前选择范围。 可用于显示光标上已设置的格式。 如果不带参数调用，则将使用用户的当前选择范围。

### Methods
```javascript
getFormat(range: Range = current): { [String]: any }
getFormat(index: Number, length: Number = 0): { [String]: any }

```

### Examples
```javascript
quill.setText('Hello World!');
quill.formatText(0, 2, 'bold', true);
quill.formatText(1, 2, 'italic', true);
quill.getFormat(0, 2);   // { bold: true }
quill.getFormat(1, 1);   // { bold: true, italic: true }

quill.formatText(0, 2, 'color', 'red');
quill.formatText(2, 1, 'color', 'blue');
quill.getFormat(0, 3);   // { color: ['red', 'blue'] }

quill.setSelection(3);
quill.getFormat();       // { italic: true, color: 'blue' }

quill.format('strike', true);
quill.getFormat();       // { italic: true, color: 'blue', strike: true }

quill.formatLine(0, 1, 'align', 'right');
quill.getFormat();       // { italic: true, color: 'blue', strike: true, align: 'right' }
```

## removeFormat
删除给定范围内的所有格式和嵌入，返回表示更改的[Delta](https://quilljs.com/guides/designing-the-delta-format/)。如果行的任何部分包含在范围内，则将删除行格式。 用户的选择可能不会被保留。[来源](https://quilljs.com/docs/api/#events)可以是`“user”`，`“api”`或`“silent”`。 [禁用](https://quilljs.com/docs/api/#disable)编辑器时，`“source”`为`“user”`的调用将被忽略。

### Methods
```javascript
removeFormat(index: Number, length: Number, source: String = 'api'): Delta
```

### Examples
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