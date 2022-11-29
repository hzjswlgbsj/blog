# Model
> 注意：本节的api都是实验性的。

## find
静态方法返回给定DOM节点的Quill或[Blot](https://github.com/quilljs/parchment)实例。 在后一种情况下，为`bubble`参数传入true将搜索给定DOM的祖先，直到找到相应的[Blot](https://github.com/quilljs/parchment)。

### Methods
```javascript
Quill.find(domNode: Node, bubble: boolean = false): Blot | Quill
```

### Examples
```javascript
var container = document.querySelector("#container");
var quill = new Quill(container);
console.log(Quill.find(container) === quill);   // Should be true

quill.insertText(0, 'Hello', 'link', 'https://world.com');
var linkNode = document.querySelector('#container a');
var linkBlot = Quill.find(linkNode);
```

## getIndex
返回文档开头与给定[Blot](https://github.com/quilljs/parchment)出现之间的距离。

### Methods
```javascript
getIndex(blot: Blot): Number
```

### Examples
```javascript
let [line, offset] = quill.getLine(10);
let index = quill.getIndex(line);   // index + offset should == 10
```

## getLeaf
返回文档中指定索引处的叶[Blot](https://github.com/quilljs/parchment)。

### Methods
```javascript
getLeaf(index: Number): Blot
```

### Examples
```javascript
quill.setText('Hello Good World!');
quill.formatText(6, 4, "bold", true);

let [leaf, offset] = quill.getLeaf(7);
// leaf should be a Text Blot with value "Good"
// offset should be 1, since the returned leaf started at index 6
```

## getLine
返回文档中指定索引处的Blot[Blot](https://github.com/quilljs/parchment)行。

### Methods
```javascript
getLine(index: Number): [Blot, Number]
```

### Examples
```javascript
quill.setText('Hello\nWorld!');

let [line, offset] = quill.getLine(7);
// line should be a Block Blot representing the 2nd "World!" line
// offset should be 1, since the returned line started at index 6
```

## getLines
返回指定位置中包含的行。

### Methods
```javascript
getLines(index: Number = 0, length: Number = remaining): Blot[]
getLines(range: Range): Blot[]
```

### Examples
```javascript
quill.setText('Hello\nGood\nWorld!');
quill.formatLine(1, 1, 'list', 'bullet');

let lines = quill.getLines(2, 5);
// array with a ListItem and Block Blot,
// representing the first two lines
```