# Keyboard Module
键盘模块为特定上下文中的键盘事件启用自定义行为。 Quill使用它来绑定格式化热键并防止不良的浏览器副作用。

## Key Bindings
键盘处理程序绑定到特定键和键修饰符。 `key`是JavaScript事件键代码，但字母数字键和一些常用键允许使用字符串短序。

键修饰符包括：`metaKey`，`ctrlKey`，`shiftKey` 和 `altKey`。 此外，`shortKey`是一个平台特定的修饰符，相当于Mac上的`metaKey` 和Linux和Windows上的`ctrlKey`。

处理程序被调用时，`this`会被绑定到键盘实例，同时还会注入当前的光标对象。

```javascript
quill.keyboard.addBinding({
  key: 'B',
  shortKey: true
}, function(range, context) {
  this.quill.formatText(range, 'bold', true);
});

// addBinding may also be called with one parameter,
// in the same form as in initialization
quill.keyboard.addBinding({
  key: 'B',
  shortKey: true,
  handler: function(range, context) {

  }
});
```

如果修饰键为`false`，则假定修饰符不活动。 您也可以传递`null`以表示修饰符的任何值。

```javascript
// Only b with no modifier will trigger
quill.keyboard.addBinding({ key: 'B' }, handler);

// Only shift+b will trigger
quill.keyboard.addBinding({ key: 'B', shiftKey: true }, handler);

// Either b or shift+b will trigger
quill.keyboard.addBinding({ key: 'B', shiftKey: null }, handler);
```

多个处理程序可以绑定到相同的键和修饰符组合。 处理程序将按照绑定的顺序同步调用。 默认情况下，处理程序停止传播到下一个处理程序，除非它显式返回`true`。

```javascript
quill.keyboard.addBinding({ key: 'tab' }, function(range) {
  // I will normally prevent handlers of the tab key
  // Return true to let later handlers be called
  return true;
});
```

注意：由于Quill的默认处理程序是在初始化时添加的，因此阻止它们的唯一方法是在[配置](https://quilljs.com/docs/modules/keyboard/#configuration)中添加你的处理程序。

## Context
上下文允许仅在特定场景中调用处理程序的进一步规范。无论是否指定了上下文，都将提供上下文对象作为所有处理程序的第二个参数。

```javascript
// If the user hits backspace at the beginning of list or blockquote,
// remove the format instead delete any text
quill.keyboard.addBinding({ key: Keyboard.keys.BACKSPACE }, {
  collapsed: true,
  format: ['blockquote', 'list'],
  offset: 0
}, function(range, context) {
  if (context.format.list) {
    this.quill.format('list', false);
  } else {
    this.quill.format('blockquote', false);
  }
});
```

### collapsed
如果为`true`，则仅在用户的选择被折叠时（即以光标形式）调用处理程序。 如果为`false`，则用户的选择长度必须为非零，例如当用户突出显示文本时。

### empty
如果为`true`，则仅在用户选择空行时调用，对于非空行调用`false`。 注意将empty设置为true意味着collapsed也为true且offset为0 - 否则用户的选择将不在空行上。

```javascript
// If the user hits enter on an empty list, remove the list instead
quill.keyboard.addBinding({ key: Keyboard.keys.ENTER }, {
  empty: true,    // implies collapsed: true and offset: 0
  format: ['list']
}, function(range, context) {
  this.quill.format('list', false);
});
```

### format
当一个Array，如果任何指定的格式处于活动状态，将调用处理程序。 当一个Object时，必须满足所有指定的格式条件。 在任何一种情况下，context参数的format属性都是所有当前活动格式的Object，与`quill.getFormat()`返回的格式相同。

```javascript
var context = {
  format: {
    list: true,       // must be on a list, but can be any value
    script: 'super',  // must be exactly 'super', 'sub' will not suffice
    link: false       // cannot be in any link
  }
};
```

### offset
只有当用户选择从行首开始`offset`字符时才会调用处理程序。 请注意，这是在应用可打印键之前。 这与其他上下文规范结合使用非常有用。

### prefix
正则表达式必须与用户选择的开始位置之前的文本匹配。 文本将不符合交叉格式边界。 提供的`context.prefix`值将是整个前一个文本，而不仅仅是正则表达式匹配。

```javascript
// When the user types space...
quill.keyboard.addBinding({ key: ' ' }, {
  collapsed: true,
  format: { list: false },  // ...on an line that's not already a list
  prefix: /^-$/,            // ...following a '-' character
  offset: 1,                // ...at the 1st position of the line,
                            // otherwise handler would trigger if the user
                            // typed hyphen+space mid sentence
}, function(range, context) {
  // the space character is consumed by this handler
  // so we only need to delete the hyphen
  this.quill.deleteText(range.index - 1, 1);
  // apply bullet formatting to the line
  this.quill.formatLine(range.index, 1, 'list', 'bullet');
  // restore selection
  this.quill.setSelection(range.index - 1);

  // console.log(context.prefix) would print '-'
});
```

### suffix
与上文的`prefix`相同，但紧跟用户选择结束位置后的匹配文本除外。

## Configuration
默认情况下，Quill附带了几个有用的键绑定，例如使用制表符缩进列表。 您可以在启动时添加自己的。

某些绑定对于防止危险的浏览器默认值（例如enter和backspace键）至关重要。您无法删除这些绑定以恢复到本机浏览器行为。但是，由于配置中指定的绑定将在Quill的默认值之前运行，因此您可以处理特殊情况并以其他方式传播到Quill。

使用`quill.keyboard.addBinding`添加绑定将不会在Quill之前运行，因为该点将添加默认绑定。

每个绑定配置必须包含`key`和`handler`选项，并且可以选择包含任何`context`选项。

```javascript
var bindings = {
  // This will overwrite the default binding also named 'tab'
  tab: {
    key: 9,
    handler: function() {
      // Handle tab
    }
  },

  // There is no default binding named 'custom'
  // so this will be added without overwriting anything
  custom: {
    key: 'B',
    shiftKey: true,
    handler: function(range, context) {
      // Handle shift+b
    }
  },

  list: {
    key: 'backspace',
    format: ['list'],
    handler: function(range, context) {
      if (context.offset === 0) {
        // When backspace on the first character of a list,
        // remove the list instead
        this.quill.format('list', false, Quill.sources.USER);
      } else {
        // Otherwise propogate to Quill's default
        return true;
      }
    }
  }
};

var quill = new Quill('#editor', {
  modules: {
    keyboard: {
      bindings: bindings
    }
  }
});
```

## Performance
与DOM事件一样，Quill键绑定是对每个匹配的阻塞调用，因此对于非常常见的键绑定而言，拥有一个非常昂贵的处理程序是一个坏主意。 应用与附加到常见阻塞DOM事件（如`scroll`或`mousemove`）时相同的性能最佳实践。