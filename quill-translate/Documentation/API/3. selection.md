# Selection
## getBounds
检索给定位置处的像素位置（相对于编辑器容器）和选择的尺寸。 用户当前的选择不需要在该索引处。 用于计算工具提示的放置位置。

### Methods
```javascript
getBounds(index: Number, length: Number = 0):
{ left: Number, top: Number, height: Number, width: Number }
```

### Examples
```javascript
quill.setText('Hello\nWorld\n');
quill.getBounds(7);  // Returns { height: 15, width: 0, left: 27, top: 31 }
```

## getSelection
检索用户的选择范围，可选择首先聚焦编辑器。 否则，如果编辑器没有焦点，则可能返回`null`。

### Methods
```javascript
getSelection(focus = false): { index: Number, length: Number }
```

### Examples
```javascript
var range = quill.getSelection();
if (range) {
  if (range.length == 0) {
    console.log('User cursor is at index', range.index);
  } else {
    var text = quill.getText(range.index, range.length);
    console.log('User has highlighted: ', text);
  }
} else {
  console.log('User cursor is not in editor');
}
```

## setSelection
将用户选择设置为给定范围，这也将使编辑器聚焦。 提供null作为选择范围将使编辑器失去焦点。[来源](https://quilljs.com/docs/api/#events)可以是`“user”`，`“api”`或`“silent”`。

### Methods
```javascript
setSelection(index: Number, length: Number = 0, source: String = 'api')
setSelection(range: { index: Number, length: Number }, source: String = 'api')
```

### Examples
```javascript
quill.setSelection(0, 5);
```