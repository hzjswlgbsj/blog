# Events
## text-change
当Quill的内容发生变化时发出。 提供了更改的详细信息，更改前编辑器内容的表示以及更改的来源。 如果源来自用户，则源将是`“user”`。 例如：

- 用户输入编辑器

- 用户使用工具栏格式化文本

- 用户使用热键撤消

- 用户使用操作系统拼写纠正

可能通过API发生更改，但只要它们来自用户，所提供的源仍应为`“user”`。 例如，当用户单击工具栏时，技术上工具栏模块会调用Quill API来实现更改。 但是源仍然是`“user”`，因为更改的起源是用户的点击。

导致文本更改的API也可以使用`“silent”`源调用，在这种情况下，`text-change`不会被触发。 建议不要这样做，因为它可能会破坏撤消堆栈以及依赖于完整文本更改记录的其他功能。

对文本的更改可能会导致更改选择（例如，键入前进光标），但是在`text-change`处理程序期间，光标（selection）尚未更新，并且本机浏览器行为可能会将其置于不一致状态。 使用下文的`selection-change`或`editor-change`来获得可靠的选择更新。

### Callback Signature
```javascript
handler(delta: Delta, oldContents: Delta, source: String)
```

### Examples
```javascript
quill.on('text-change', function(delta, oldDelta, source) {
  if (source == 'api') {
    console.log("An API call triggered this change.");
  } else if (source == 'user') {
    console.log("A user action triggered this change.");
  }
});
```

## selection-change
当用户或API导致选择更改时发出，其中范围表示选择边界。 空范围表示选择丢失（通常由编辑器失去焦点引起）。 您还可以通过检查发出的范围是否为空来将此事件用作焦点更改事件。

导致光标（selection）改变的API也可以用`“silent”`源调用，在这种情况下，不会触发`selection-change`。 如果`selection-change`是副作用，这将非常有用。 例如，键入会导致选择发生更改，但如果同时对每个字符都触发`selection-change`事件，则会产生非常大的噪声（副作用？低性能？）。

### Callback Signature
```javascript
handler(range: { index: Number, length: Number },
        oldRange: { index: Number, length: Number },
        source: String)
```

### Examples
```javascript
quill.on('selection-change', function(range, oldRange, source) {
  if (range) {
    if (range.length == 0) {
      console.log('User cursor is on', range.index);
    } else {
      var text = quill.getText(range.index, range.length);
      console.log('User has highlighted', text);
    }
  } else {
    console.log('Cursor not in the editor');
  }
});
```

## editor-change
当事件`text-change`或`selection-change`被触发时，`editor-change`都会被触发，即使源是`“silent”`。第一个参数是`text-change`或`selection-change`的事件名，后跟通常传递给相应处理程序的参数。

### Callback Signature
```javascript
handler(name: String, ...args)
```

### Examples
```javascript
quill.on('editor-change', function(eventName, ...args) {
  if (eventName === 'text-change') {
    // args[0] will be delta
  } else if (eventName === 'selection-change') {
    // args[0] will be old range
  }
});
```

## on
添加事件处理程序。 有关事件本身的更多详细信息，请参阅上文的`text-change`或`selection-change`。

### Methods
```javascript
on(name: String, handler: Function): Quill
```

### Examples
```javascript
quill.on('text-change', function() {
  console.log('Text change!');
});
```

## once
添加一个只触发一次的事件处理程序。 有关事件本身的更多详细信息，请参阅上文的`text-change`或`selection-change`。

### Methods
```javascript
once(name: String, handler: Function): Quill
```

### Examples
```javascript
quill.once('text-change', function() {
  console.log('First text change!');
});
```

## off
删除事件处理程序。

### Methods
```javascript
off(name: String, handler: Function): Quill
```

### Examples
```javascript
function handler() {
  console.log('Hello!');
}

quill.on('text-change', handler);
quill.off('text-change', handler);
```