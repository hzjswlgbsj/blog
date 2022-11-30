# Syntax Highlighter Module
语法模块通过自动检测和应用语法突出显示来增强代码块格式。优秀的[highlight.js](https://highlightjs.org/)库被用作解析和标记代码块的依赖项。

通常，您可以根据需要[configure](https://highlightjs.readthedocs.io/en/latest/api.html#configure-options)highlight.js。 但是，Quill期望并要求`useBR`选项为`false`。

## Example
```html
<!-- Include your favorite highlight.js stylesheet -->
<link href="highlight.js/monokai-sublime.min.css" rel="stylesheet">

<!-- Include the highlight.js library -->
<script href="highlight.js"></script>

<script>
hljs.configure({   // optionally configure hljs
  languages: ['javascript', 'ruby', 'python']
});

var quill = new Quill('#editor', {
  modules: {
    syntax: true,              // Include syntax module
    toolbar: [['code-block']]  // Include button in toolbar
  },
  theme: 'snow'
});
</script>
```