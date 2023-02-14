
> 本文不断更新...


### 1. 文本超出隐藏，并用三个点结尾

```css
overflow: hidden;
text-overflow: ellipsis;
white-space: nowrap;
```

### 2. 浏览器中的各种高度、宽度

```css
document.body.clientWidth ==> BODY对象宽度  
document.body.clientHeight ==> BODY对象高度  
document.documentElement.clientWidth ==> 可见区域宽度  
document.documentElement.clientHeight ==> 可见区域高度  
  
网页可见区域宽： document.body.clientWidth  
网页可见区域高： document.body.clientHeight  
网页可见区域宽： document.body.offsetWidth (包括边线的宽)  
网页可见区域高： document.body.offsetHeight (包括边线的高)  
网页正文全文宽： document.body.scrollWidth  
网页正文全文高： document.body.scrollHeight  
网页被卷去的高： document.body.scrollTop  
网页被卷去的左： document.body.scrollLeft  
网页正文部分上： window.screenTop  
网页正文部分左： window.screenLeft  
屏幕分辨率的高： window.screen.height  
屏幕分辨率的宽： window.screen.width  
屏幕可用工作区高度： window.screen.availHeight  
屏幕可用工作区宽度： window.screen.availWidth  
  
// 部分jQuery函数  
$(window).height() 　//浏览器时下窗口可视区域高度   
$(document).height()　　　　//浏览器时下窗口文档的高度   
$(document.body).height()　　　　　　//浏览器时下窗口文档body的高度   
$(document.body).outerHeight(true)　//浏览器时下窗口文档body的总高度 包括border padding margin   
$(window).width() 　//浏览器时下窗口可视区域宽度   
$(document).width()//浏览器时下窗口文档对于象宽度   
$(document.body).width()　　　　　　//浏览器时下窗口文档body的高度   
$(document.body).outerWidth(true)　//浏览器时下窗口文档body的总宽度 包括border padding  
  
HTML精确定位:scrollLeft,scrollWidth,clientWidth,offsetWidth   
scrollHeight: 获取对象的滚动高度。   
scrollLeft:设置或获取位于对象左边界和窗口中目前可见内容的最左端之间的距离   
scrollTop:设置或获取位于对象最顶端和窗口中可见内容的最顶端之间的距离   
scrollWidth:获取对象的滚动宽度   
offsetHeight:获取对象相对于版面或由父坐标 offsetParent 属性指定的父坐标的高度   
offsetLeft:获取对象相对于版面或由 offsetParent 属性指定的父坐标的计算左侧位置   
offsetTop:获取对象相对于版面或由 offsetTop 属性指定的父坐标的计算顶端位置   
event.clientX 相对文档的水平座标   
event.clientY 相对文档的垂直座标   
event.offsetX 相对容器的水平坐标   
event.offsetY 相对容器的垂直坐标   
document.documentElement.scrollTop 垂直方向滚动的值   
event.clientX+document.documentElement.scrollTop 相对文档的水平座标+垂直方向滚动的量
```

### 3. 禁用某个元素的悬浮效果，包括事件

```css
pointer-events: none;
```

### 4. 清除原生input、textarea、button的自带样式

```css
input,button,select,textarea{
    outline:none; // 取消输入框聚焦时候的蓝色边框
    box-shadow: none; // 取消按钮点击后的蓝色框
    border: none  // 取消所有边框
} 
textarea{resize:none} // 禁止textarea可以拖放大小
```

### 5. 让[fontawesome](http://fontawesome.dashgame.com)字体图标变细

```css
<i style="-webkit-text-stroke: 1px gray; color: white; font-size: 2em">
```

**参考**：[一句 CSS 让 fontawesome 图标字体变细](https://www.chrisyue.com/how-to-make-fontawesome-font-lighter-with-one-line-css-code.html)

### 6. 垂直居中

**参考**：[16种方法实现水平居中垂直居中](https://juejin.im/post/58f818bbb123db006233ab2a)


