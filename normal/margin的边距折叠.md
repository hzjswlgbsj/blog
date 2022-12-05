块级元素的上外边距和下外边距有时会合并（或折叠）为一个外边距，其大小取其中的最大者，这种行为称为`外边距折叠（margin collapsing）`，有时也翻译为`外边距合并`。注意浮动元素和绝对定位元素的外边距不会折叠。

margin 的`边距折叠`主要有以下三种情况：

### 1.相邻元素之间

毗邻的两个元素之间的外边距会折叠（除非后一个元素需要清除之前的浮动）。

### 2.父元素与其第一个或最后一个子元素之间

如果在父元素与其第一个子元素之间不存在边框、内边距、行内内容，也没有创建块格式化上下文、或者清除浮动将两者的 margin-top 分开；或者在父元素与其最后一个子元素之间不存在边框、内边距、行内内容、height、min-height、max-height将两者的 margin-bottom 分开，那么这两对外边距之间会产生折叠。此时子元素的外边距会“溢出”到父元素的外面。

### 3.空的块级元素

如果一个块级元素中不包含任何内容，并且在其 margin-top 与 margin-bottom 之间没有边框、内边距、行内内容、height、min-height 将两者分开，则该元素的上下外边距会折叠。

下面我们详细分析一下：

### 相邻元素之间垂直方向外边距合并

```javascript
<template>
  <div class="parent">
    <div class="first-child">first-child</div>
    <div class="second-child">second-child</div>
  </div>
</template>

<script>
export default {
  name: 'CssMargin'
}
</script>

<style scoped>
.parent {
  background-color: #cccccc;
  color: #ffffff
}
.first-child {
  background-color: red;
  width: 100px;
  height: 100px;
  margin-bottom: 10px;
}
.second-child {
  background-color: blue;
  width: 100px;
  height: 100px;
  margin-top: 20px;
}
</style>
```

效果如下：

![image-20200828114501588](https://lib.sixtyden.com/margin1.jpg)

**两个竖直方向的盒子相遇时，其竖直方向的距离等于外边距中值较大的一个。**

解决方法：
将上面一个div用一个触发 **BFC（Block Formatting Context）** 的盒子包裹，让下面的 div 与上面的 div 互不影响。

```javascript
<template>
  <div class="parent">
    <div class="container">
      <div class="first-child">first-child</div>
    </div>
    <div class="second-child">second-child</div>
    <!-- <div class="third-child">third-child</div> -->
  </div>
</template>

<script>
export default {
  name: 'CssMargin'
}
</script>

<style scoped>
.parent {
  background-color: #cccccc;
  color: #ffffff;
  width: 400px;
  height: 400px;
}
.container {
  overflow: hidden;
  /* height: 100px; // 这里不能设置容器的高度，不然超出的就被隐藏了 */
}
.first-child {
  background-color: red;
  width: 100px;
  height: 100px;
  margin-bottom: 10px;
}

.second-child {
  background-color: blue;
  width: 100px;
  height: 100px;
  margin-top: 20px;
}
.third-child {
  background-color: blue;
  width: 100px;
  height: 100px;
}
</style>
```

效果如下：

![image-20200828114601382](https://lib.sixtyden.com/margin2.jpg)

### 父元素和子元素之间

```javascript
<template>
  <div class="parent">
    <div class="first-child"></div>
    <!-- <div class="second-child">second-child</div> -->
    <!-- <div class="third-child">third-child</div> -->
  </div>
</template>

<script>
export default {
  name: 'CssMargin'
}
</script>

<style scoped>
.parent {
  background-color: #cccccc;
  color: #ffffff;
  width: 200px;
  height: 200px;
}
.first-child {
  background-color: red;
  margin-top: 50px;
}
.second-child {
  margin-top: 50px;
  margin-bottom: 30px;
}

.third-child {
  background-color: blue;
  width: 100px;
  height: 100px;
}

</style>
```

效果如下：

![image-20200828114638270](https://lib.sixtyden.com/margin3.jpg)

可以看到并没有达到我们想要的结果，外边距跑到父元素上去了（实际上还是在子元素上，只是看起来像是作用在了父元素上）。

产生这样的结果是因为当父元素没有设置padding值以及border值时，父元素的上方与子元素的上方完全重合在了一起，无法分开。所以才会导致上述这种父元素和子元素同时向下的情况。

解决方法主要有两个方向，第一，触发父元素的[BFC](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)，第二，强行让父元素和子元素分开

1. 为父元素增加border
2. 为父元素增加padding-top
3. 为父元素添加属性 overflow:hidden
4. 让父元素或者子元素浮动
5. 改变父元素的 position
   ……

方法有很多，第1，2的缺点就是至少多占了一像素位置，后面几个方法都是触发BFC，其中我觉得比较好的就是给父元素增加`overflow: hidden`

```javascript
<template>
  <div class="parent">
    <div class="first-child">first-child</div>
    <!-- <div class="second-child">second-child</div> -->
    <!-- <div class="third-child">third-child</div> -->
  </div>
</template>

<script>
export default {
  name: 'CssMargin'
}
</script>
<style scoped>
.parent {
  background-color: #cccccc;
  color: #ffffff;
  width: 500px;
  height: 500px;
  overflow: hidden;
}

.first-child {
  background-color: red;
  margin-top: 50px;
  width: 100px;
  height: 100px;
}
.second-child {
  margin-top: 50px;
  margin-bottom: 30px;
}

.third-child {
  background-color: blue;
  width: 100px;
  height: 100px;
}
</style>
```

达到我们想要的效果

![image-20200828114710756](https://lib.sixtyden.com/margin4.jpg)

### 两个块级元素之间还有一个空的块级元素

```javascript
<template>
  <div class="parent">
    <div class="first-child">first-child</div>
    <div class="second-child"></div>
    <div class="third-child">third-child</div>
  </div>
</template>

<script>
export default {
  name: 'CssMargin'
}
</script>

<style scoped>
.parent {
  background-color: #cccccc;
  color: #ffffff
}
.first-child {
  background-color: red;
  width: 100px;
  height: 100px;
}

.second-child {
  margin-top: 50px;
  margin-bottom: 30px;
}

.third-child {
  background-color: blue;
  width: 100px;
  height: 100px;
}
</style>
```

效果如下：

![image-20200828114730632](https://lib.sixtyden.com/margin5.jpg)

两个div中间有一个没有内容，没有高度的div，这个div如果同时设置了`margin-top` 和 `margin-bottom` 的话，**最终距离也会取他们两者中最大的值**。

解决办法：
你为什么要弄一个没有内容的 div 放在那里呢？不用行不行。

### 注意：

1. 上述情况的组合会产生更复杂的外边距折叠。
2. 即使某一外边距为0，这些规则仍然适用。因此就算父元素的外边距是0，第一个或最后一个子元素的外边距仍然会“溢出”到父元素的外面。
3. 如果参与折叠的外边距中包含负值，折叠后的外边距的值为最大的正边距与最小的负边距（即绝对值最大的负边距）的和。
4. 如果所有参与折叠的外边距都为负，折叠后的外边距的值为最小的负边距的值。这一规则适用于相邻元素和嵌套元素。

### 补充

BFC可以创建相互独立的盒子、清除内部元素浮动、 解决margin折叠等，现在可以用 `display: flow-root` 来创建完全没有副作用的BFC，可以在以后常用BFC来做布局。

平常在布局的时候最好是把所有的样式分块，不要让自己影响到其他块的布局。比如有时候容器有限制高度，但是内容却超出了盒子，内容溢出会影响其他块的布局。可以用BFC来常见完全独立的盒模型容器。

有时候做响应式的时候，会做类似于卡片的排列，在不同的分辨率下排列方式改变，此时建议不要使用 margin 来做卡片之间的间隔，margin 会出现外部距离，这个距离占据的响应式的高度宽度，本来一行可以排三个，但是由于有 margin 就导致一排排三个就差一点点。可以用padding来做。

虽然 flex 布局做水平垂直居中很方便，但是你如果要兼容老一点的浏览器，你也可以使用 `margin: 0 auto`，加上 `line-height` 元素高度来做。

### 参考

1.[块级格式化上下文（BFC）](https://developer.mozilla.org/zh-CN/docs/Web/Guide/CSS/Block_formatting_context)
2.[深入理解margin](https://www.cnblogs.com/zhuzhenwei918/p/6124263.html#name3)
3.[ant design的card-list](https://preview.pro.ant.design/list/card-list)