# Extension
## debug
允许在给定级别记录消息的静态方法：`'error'`，`'warn'`，`'log'`或`'info'`。 传递true相当于传递`'log'`。 传递`false`会禁用所有消息。

### Methods
```javascript
Quill.debug(level: String | Boolean)
```

### Examples
```javascript
Quill.debug('info');
```

## import
返回Quill库，格式，模块或主题的静态方法。 通常，路径应该精确映射到Quill源代码目录结构。 除非另有说明，否则对返回实体的修改可能会破坏所需的Quill功能，因此强烈建议不要这样做。

### Methods
```javascript
Quill.import(path): any
```

### Examples
```javascript
var Parchment = Quill.import('parchment');
var Delta = Quill.import('delta');

var Toolbar = Quill.import('modules/toolbar');
var Link = Quill.import('formats/link');
// Similar to ES6 syntax `import Link from 'quill/formats/link';`
```

## register
注册模块，主题或格式，使其可以添加到编辑器中。以后可以使用`Quill.import`检索。使用`'formats/'`，`'modules/'`或`'themes/'`的路径前缀分别注册格式，模块或主题。对于特定格式，缩写为直接传递格式，路径将自动生成。 将使用相同的路径覆盖现有定义。

### Methods
```javascript
Quill.register(format: Attributor | BlotDefinintion, supressWarning: Boolean = false)
Quill.register(path: String, def: any, supressWarning: Boolean = false)
Quill.register(defs: { [String]: any }, supressWarning: Boolean = false)
```

### Examples
```javascript
var Module = Quill.import('core/module');

class CustomModule extends Module {}

Quill.register('modules/custom-module', CustomModule);
```

```javascript
Quill.register({
  'formats/custom-format': CustomFormat,
  'modules/custom-module-a': CustomModuleA,
  'modules/custom-module-b': CustomModuleB,
});

Quill.register(CustomFormat);
// You cannot do Quill.register(CustomModuleA); as CustomModuleA is not a format
```

## addContainer
在Quill容器中添加并返回一个容器元素，同步到编辑器本身。按照惯例，Quill模块应该有一个前缀为`ql-`的类名。可选地包括一个refNode，其中应该插入容器。

### Methods
```javascript
addContainer(className: String, refNode?: Node): Element
addContainer(domNode: Node, refNode?: Node): Element
```

### Examples
```javascript
var container = quill.addContainer('ql-custom');
```

## getModule
检索已添加到编辑器的模块。

### Methods
```javascript
getModule(name: String): any
```

### Examples
```javascript
var toolbar = quill.getModule('toolbar');
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