# Adding Quill to Your Build Pipeline
每个版本的Quill都可以使用各种来源，包括[NPM](https://www.npmjs.com/package/quill)或其[CDN](https://quilljs.com/docs/download/)。 但是，可能存在一些用例，您希望从源代码构建Quill，作为应用程序构建管道的一部分。 如果你没有想到这种欲望，就不要大费周章啦！ 使用预建版本是使用Quill的最简单方法。

Quill是使用[Webpack](https://webpack.js.org/concepts/)构建的，本指南主要针对Webpack用户。 但是，某些原则可能会转换为其他构建环境。

在[quill-webpack-example](https://github.com/quilljs/webpack-example/)中可以找到本指南中涵盖的所有内容的一个最小的工作示例。

## Webpack
您需要将Webpack和适当的加载器作为开发依赖项添加到您的应用程序中。 如果你想从源代码构建Parchment，那么Typescript编译器是必需的。
- Quill源代码 - [babel-core](https://www.npmjs.com/package/babel-core)，[babel-loader](https://www.npmjs.com/package/babel-loader)，[babel-preset-es2015](https://www.npmjs.com/package/babel-preset-es2015)

- Parchment源代码 - [ts-loader](https://www.npmjs.com/package/ts-loader)，[typescript](https://www.npmjs.com/package/typescript)

- SVG图标 - [html-loader](https://www.npmjs.com/package/html-loader)

您的Webpack配置文件还需要将Quill和Parchment别名指向其各自的条目源文件。 如果没有这个，Webpack将使用NPM中包含的预构建文件，而不是从源代码构建。

## Entry
Quill被发布会构建`quill.js`和`quill.core.js`。用于构建的条目文件的目的，[quill.js](https://github.com/quilljs/quill/blob/master/quill.js)和[core](https://github.com/quilljs/quill/blob/master/core.js)，是导入和注册必要的依赖项。您可能希望应用程序中有一个类似的入口点，该入口点只包含您使用的格式、模块或主题。
```javascript
import Quill from 'quill/core';

import Toolbar from 'quill/modules/toolbar';
import Snow from 'quill/themes/snow';

import Bold from 'quill/formats/bold';
import Italic from 'quill/formats/italic';
import Header from 'quill/formats/header';


Quill.register({
  'modules/toolbar': Toolbar,
  'themes/snow': Snow,
  'formats/bold': Bold,
  'formats/italic': Italic,
  'formats/header': Header
});


export default Quill;
```

## Stylesheets
在样式表领域从源代码构建中没有多少好处，因为规则很容易被覆盖。 但是，[css-loader](https://www.npmjs.com/package/css-loader)的波形符前缀可能有助于在应用程序css文件中包含Quill样式。

```javascript
@import "~quill/dist/quill.core.css"

// Rest of your application CSS
```

## Example
看一下[quill-webpack-example](https://github.com/quilljs/webpack-example)以获得最小的工作示例。