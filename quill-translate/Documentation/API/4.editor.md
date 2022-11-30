# Editor
## blur
从编辑器中删除焦点。

### Methods
```javascript
blur()
```

### Examples
```javascript
quill.blur();
```

## enable
通过鼠标或键盘等输入设备设置用户编辑的能力。 当源为`“api”`或`“silent”`时，不影响API调用的功能。

### Methods
```javascript
enable(enabled: boolean = true)
```

### Examples
```javascript
quill.enable();
quill.enable(false);   // Disables user input
```

## disable
`enable(false)`的简写

## focus
让编辑器获得焦点，并将光标至于最后。

### Methods
```javascript
focus()
```

### Examples
```javascript
quill.focus();
```

## hasFocus
检查编辑器是否处于获取焦点状态。 注意注意工具栏，工具提示，不算作编辑器。

### Methods
```javascript
hasFocus(): Boolean
```

### Examples
```javascript
quill.hasFocus();
```

## update
如果发生更改，请同步检查编辑器以获取用户更新并触发事件。 在解决冲突期间需要最新的最新状态的协作用例非常有用。[来源](https://quilljs.com/docs/api/#events)可以是`“user”`，`“api”`或`“silent”`。

### Methods
```javascript
update(source: String = 'user')
```

### Examples
```javascript
quill.update();
```