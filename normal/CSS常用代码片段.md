> 本文不断更新...

### 1. 文本超出隐藏，并用三个点结尾

```css
// 单行
overflow: hidden;
text-overflow: ellipsis;
white-space: nowrap;

// 多行
width: 400px;
display: -webkit-box;
-webkit-line-clamp: 3;
-webkit-box-orient: vertical;
overflow: hidden;
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

### 4. 清除原生 input、textarea、button 的自带样式

```css
input,
button,
select,
textarea {
  outline: none; // 取消输入框聚焦时候的蓝色边框
  box-shadow: none; // 取消按钮点击后的蓝色框
  border: none // 取消所有边框
;
}
textarea {
  resize: none;
} // 禁止textarea可以拖放大小
```

### 5. 让[fontawesome](http://fontawesome.dashgame.com)字体图标变细

```css
<i style="-webkit-text-stroke: 1px gray; color: white; font-size: 2em">
```

**参考**：[一句 CSS 让 fontawesome 图标字体变细](https://www.chrisyue.com/how-to-make-fontawesome-font-lighter-with-one-line-css-code.html)

### 6. 垂直居中

**参考**：[16 种方法实现水平居中垂直居中](https://juejin.im/post/58f818bbb123db006233ab2a)

### 7.平滑定位

CSS Scroll Snap 是 CSS 中一个独立的模块，可以让**网页容器滚动停止的时候，自动平滑定位到指定元素的指定位置**，包含 `scroll-*` 以及 `scroll-snap-* `等诸多 CSS 属性。

**参考**：[16 种方法实现水平居中垂直居中](https://www.zhangxinxu.com/wordpress/2018/11/know-css-scroll-snap/)

### 8.css 动画

**无限循环旋转**

```html
<div id="loading">
  <div class="color1"></div>
  <div class="color1"></div>
  <div class="color1"></div>
  <div class="color1"></div>
  <div class="color1"></div>
  <div class="color2"></div>
  <div class="color2"></div>
  <div class="color2"></div>
  <div class="color2"></div>
  <div class="color2"></div>
</div>
```

```css
.color1 {
  background: #fde515;
}
.color2 {
  background: #00def2;
}
@-webkit-keyframes loadRotate {
  from {
    -webkit-transform: rotateZ(0deg);
  }
  to {
    -webkit-transform: rotateZ(360deg);
  }
}
@keyframes loadRotate {
  from {
    transform: rotateZ(0deg);
  }
  to {
    transform: rotateZ(360deg);
  }
}
#loading {
  width: 100px;
  height: 100px;
  position: absolute;
  left: 50%;
  top: 50%;
  margin-left: -50px;
  margin-top: -50px;
  -webkit-animation: loadRotate 3s linear infinite;
  -webkit-animation-fill-mode: both;
  animation: loadRotate 3s linear infinite;
  /*动画从头到尾的速度是相同的  liner*/
  /*指定动画应该播放无限次（永远）*/
  animation-fill-mode: both;
  /*动画结束前和结束后都应用该属性 animation-fill-mode:both; */
}
#loading div {
  width: 20px;
  height: 30px;
  position: absolute;
  left: 40px;
  top: 35px;
  -webkit-transform: rotateZ(0) translateX(60px);
  opacity: 1;
  border-radius: 50% 0;
}
#loading div:nth-child(2) {
  -webkit-transform: rotateZ(36deg) translateX(60px);
  opacity: 0.8;
}
#loading div:nth-child(3) {
  -webkit-transform: rotateZ(72deg) translateX(60px);
  opacity: 0.6;
}
#loading div:nth-child(4) {
  -webkit-transform: rotateZ(108deg) translateX(60px);
  opacity: 0.4;
}
#loading div:nth-child(5) {
  -webkit-transform: rotateZ(144deg) translateX(60px);
  opacity: 0.2;
}
#loading div:nth-child(6) {
  -webkit-transform: rotateZ(180deg) translateX(60px);
  opacity: 1;
}
#loading div:nth-child(7) {
  -webkit-transform: rotateZ(216deg) translateX(60px);
  opacity: 0.8;
}
#loading div:nth-child(8) {
  -webkit-transform: rotateZ(252deg) translateX(60px);
  opacity: 0.6;
}
#loading div:nth-child(9) {
  -webkit-transform: rotateZ(288deg) translateX(60px);
  opacity: 0.4;
}
#loading div:nth-child(10) {
  -webkit-transform: rotateZ(324deg) translateX(60px);
  opacity: 0.2;
}
```
