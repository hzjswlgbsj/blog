# Toolbar Module
工具栏模块允许用户轻松格式化Quill的内容。

它可以配置自定义容器和处理程序。

```javascript
var quill = new Quill('#editor', {
  modules: {
    toolbar: {
      container: '#toolbar',  // Selector for toolbar container
      handlers: {
        'bold': customBoldHandler
      }
    }
  }
});
```

由于`container`选项很常见，因此也允许使用顶级简写。

```javascript
var quill = new Quill('#editor', {
  modules: {
    // Equivalent to { toolbar: { container: '#toolbar' }}
    toolbar: '#toolbar'
  }
});
```

## Container
工具栏控件可以由简单的格式名称数组或自定义HTML容器指定。

从更简单的数组选项开始:

```javascript
var toolbarOptions = ['bold', 'italic', 'underline', 'strike'];

var quill = new Quill('#editor', {
  modules: {
    toolbar: toolbarOptions
  }
});
```

控件也可以按嵌套数组的一个级别进行分组。 这将使用类名称`ql-formats`将控件包装在`<span>`中，从而为要使用的主题提供结构。 例如，[Snow](https://quilljs.com/docs/themes/#snow/)在控件组之间添加了额外的间距。

```javascript
var toolbarOptions = [['bold', 'italic'], ['link', 'image']];
```

可以使用Object指定具有自定义值的按钮，并将格式名称作为其唯一键。

```javascript
var toolbarOptions = [{ 'header': '3' }];
```

下拉列表类似地由Object指定，但具有可能值的数组。 CSS用于控制下拉选项的可视标签。

```javascript
// 注意false，而不是'normal'，是正确的值quill.format（'size'，false）删除格式，允许默认样式工作
var toolbarOptions = [
  { size: [ 'small', false, 'large', 'huge' ]}
];
```

注意[Themes](https://quilljs.com/docs/themes/)还可以指定下拉列表的默认值。 例如，如果设置为空数组，则[Snow](https://quilljs.com/docs/themes/#snow/)会为`color`和`background`格式提供35种颜色的默认列表。

```javascript
var toolbarOptions = [
  ['bold', 'italic', 'underline', 'strike'],        // toggled buttons
  ['blockquote', 'code-block'],

  [{ 'header': 1 }, { 'header': 2 }],               // custom button values
  [{ 'list': 'ordered'}, { 'list': 'bullet' }],
  [{ 'script': 'sub'}, { 'script': 'super' }],      // superscript/subscript
  [{ 'indent': '-1'}, { 'indent': '+1' }],          // outdent/indent
  [{ 'direction': 'rtl' }],                         // text direction

  [{ 'size': ['small', false, 'large', 'huge'] }],  // custom dropdown
  [{ 'header': [1, 2, 3, 4, 5, 6, false] }],

  [{ 'color': [] }, { 'background': [] }],          // dropdown with defaults from theme
  [{ 'font': [] }],
  [{ 'align': [] }],

  ['clean']                                         // remove formatting button
];

var quill = new Quill('#editor', {
  modules: {
    toolbar: toolbarOptions
  },
  theme: 'snow'
});
```

对于需要更多自定义的用例，您可以在HTML中手动创建工具栏，并将DOM元素或选择器传递给Quill。`ql-toolbar`类将添加到工具栏容器中，Quill将适当的处理程序附加到`<button>`和`<select>`元素，其类名称为`ql-${format}`。Buttons元素可以选择具有自定义`value`属性。

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

请注意，通过提供您自己的HTML元素，Quill会搜索特定的输入元素，但您自己的输入与Quill无关，仍然可以添加和设置样式并共存。

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

## Handlers
默认情况下，工具栏控件应用并删除格式，但您也可以使用自定义处理程序覆盖它，例如为了显示外部UI。

处理程序函数将绑定到工具栏（因此使用`this`将引用工具栏实例）并在相应格式处于非活动状态时传递输入的`value`属性，否则传递`false`。 添加自定义处理程序将覆盖默认工具栏和主题行为。

```javascript
var toolbarOptions = {
  handlers: {
    // handlers object will be merged with default handlers object
    'link': function(value) {
      if (value) {
        var href = prompt('Enter the URL');
        this.quill.format('link', href);
      } else {
        this.quill.format('link', false);
      }
    }
  }
}

var quill = new Quill('#editor', {
  modules: {
    toolbar: toolbarOptions
  }
});

// Handlers can also be added post initialization
var toolbar = quill.getModule('toolbar');
toolbar.addHandler('image', showImageUI);
```