# History Module
历史记录模块负责处理Quill的撤消和重做。 它可以配置以下选项：

## Configuration
### delay
- Default: `1000`

在延迟毫秒数内发生的更改将合并为单个更改。

例如，将延迟设置为`0`，几乎每个字符都记录为一次更改，因此撤消将一次撤消一个字符。 延迟设置为`1000`后，撤消将撤消在过去1000毫秒内发生的所有更改。

### maxStack
- Default: `100`

历史记录的撤消/重做堆栈的最大大小。 具有`delay`选项的合并更改计为单个更改。

### userOnly
- Default: `false`

默认情况下，所有更改（无论是源自用户输入还是通过API以编程方式）都将被视为相同，并且更改将由历史记录模块撤消或重做。 如果`userOnly`设置为`true`，则只撤消或重做用户更改。

## Example
```javascript
var quill = new Quill('#editor', {
  modules: {
    history: {
      delay: 2000,
      maxStack: 500,
      userOnly: true
    }
  },
  theme: 'snow'
});
```

## API
### clear
清除历史堆栈。

#### Methods
```javascript
clear()
```
#### Examples
```javascript
quill.history.clear();
```

### cutoff(该API在实验中)
通常，短时间连续发生的更改（由`delay`配置）将合并为单个更改，因此触发撤消将撤消多个更改。使用`cutoff()`将重置合并窗口，以便调用`cutoff()`之前和之后的更改将不会合并。

#### Methods
```javascript
cutoff()
```
#### Examples
```javascript
quill.history.cutoff();
```

### cutoff
撤消上次更改。

#### Methods
```javascript
undo()
```
#### Examples
```javascript
quill.history.undo();
```

### redo
如果上次更改是撤消操作，请重做此撤消操作。 否则什么都不做。

#### Methods
```javascript
redo()
```
#### Examples
```javascript
quill.history.redo();
```