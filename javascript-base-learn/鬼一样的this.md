# 恶补 JavaScript 基础之鬼一样的this

## 前言

我觉得关于 this 关键字用这个标题最合适不过了，这块内容我刚接触 JavaScript 的时候我就感觉它跟鬼一样可怕捉摸不定。在《JavaScript高级程序设计》的第七章 函数表达式 中提到了this对象，但是我觉得讲的有点简略，我建议大家可以去读读《你不知道的 JavaScript》上卷，讲的更为详细。

this 是在函数被调用的时候才被确定的，他跟执行上下文相关内容紧密相连，我建议你先阅读《执行上下文》、《变量对象》和《作用域链》后，再来阅读本文。

>  **重要提醒**：我在学习这部分内容的时候查找了大量资料，包括网上的，《JavaScript高级程序设计》的7.2.2节以及《你不知道的JavaScript 上》的第二部分，[MDN关于 This 的文档](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this) 等几乎所有的讲关于 This 的文章或者书籍都是从 This 的调用方式或位置的角度来理解的（本文也是如此），但是[这里有大佬从 Ecmascript 规范的角度去解读 This](https://github.com/mqyqingfeng/Blog/issues/7)，思路清奇值得拜读！我建议阅读完本文后一定要去读读这篇文章！

## this 是什么

**《你不知道的 JavaScript》上卷：**

this 是在**运行时**进行绑定的，并不是在编写时绑定，他的上下文取决于函数调用是的各种条件。this 的绑定和函数的申明的位置没有任何关系，只取决于函数的调用方式。

当一个函数被调用时，会创建一个活动记录（有时候也称为执行上下文）。这个记录会包含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。this 就是这个记录中的一个属性，会在函数的执行过程中被用到。

**《JavaScript 高级程序设计》：**

this 是在**运行时**基于函数的执行环境绑定的：在全局函数中， this 等于 window，而当函数被作为某个对象的方法被调用时， this 等于那个对象。

**MDN：**

在绝大多数情况下，函数的调用方式决定了 `this` 的值（运行时绑定）。`this` 不能在执行期间被赋值，并且在每次函数被调用时 `this` 的值也可能会不同。ES5 引入了 [bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind) 方法来设置函数的 `this` 值，而不用考虑函数如何被调用的。ES2015 引入了[箭头函数](https://wiki.developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Functions/Arrow_functions)，箭头函数不提供自身的 this 绑定（`this` 的值将保持为闭合词法上下文的值）。



可以认为 this 值是一个与执行上下文相关的特殊对象。因此，我觉得可以将之称为上下文对象(即，在其中激活执行上下文的对象)。任何对象都可以用作上下文的这个值，但需要注意的是，在之前的文章《执行上下文》中提到 this 值是执行上下文（EC，execution context）的属性，而不是变量对象（VO，veriable object）的属性。

this 是在运行时基于函数执行环境绑定的，这一点非常重要，因为与变量相比，这个值从不参与标识符解析过程。例如，当在代码中访问 this 时，它的值直接从执行上下文中获取，不需要任何作用域链查找。这个值只在进入上下文时确定一次。但是要注意，在ES6中，this 实际上成为了词汇环境的属性，即ES3中变量对象的属性。这样做是为了让支持箭头函数拥有有词法环境的 this，它们从父上下文继承。

下面我们也从不同的执行环境来看看对应的 this 值。

> 下文内容翻译自[ECMA-262-3 in detail. Chapter 3. This.](http://dmitrysoshnikov.com/ecmascript/chapter-3-this/) ，接下来的内容非常精彩，如果有我觉得难理解的地方我会给出更简单的解释。

## 全局环境中的this

直接看代码，这种情况最简单

```javascript
// 显示定义全局对象的属性
this.a = 10; // global.a = 10
console.log(a); // 10
 
// 通过赋值给一个无标示符隐式
b = 20;
console.log(this.b); // 20
 
// 也是通过变量声明隐式声明的
// 因为全局上下文的变量对象是全局对象自身
var c = 30;
console.log(this.c); // 30
```

## 函数环境中的this

在函数代码中使用 this 时有些复杂，这种情况很难且会导致很多问题。这种类型的代码中，this 值的主要特点是它不是静态的绑定到一个函数。正如我们上面曾提到的那样，this 是在运行时时确定，在一个函数代码中，这个值在函数每执行的时候都有可能不同。但是不管怎样，在代码运行时的 this 值是不变的，也就是说，因为它不是一个变量，就不可能为其分配一个新值。

```javascript
var foo = {x: 10};
var bar = {
  x: 20,
  test: function () {
    console.log(this === bar); // true
    console.log(this.x); // 20
    this = foo; // 错误，任何时候不能改变this的值
    console.log(this.x); // 如果不出错的话，应该是10，而不是20
  }
};
 
// 在进入上下文的时候this被当成bar对象
bar.test(); // true, false, 20
foo.test = bar.test;
 
// 这里this是就变成foo了
foo.test(); // false, true, 10
```

那么，影响了函数代码中this值的变化有几个因素：

首先，在通常的函数调用中，this 是由激活上下文代码的调用者来提供的，即调用函数的父上下文(parent context )。this取决于调用函数的方式。为了在任何情况下准确无误的确定this值，有必要理解和记住这重要的一点。



即使是正常的全局函数也会被调用方式的不同形式激活，这些不同的调用方式导致了不同的 this 值，再看一个例子：

```javascript
function foo() {
  console.log(this);
}
 
foo(); // global
console.log(foo === foo.prototype.constructor); // true
 
// 但是同一个function的不同的调用表达式，this是不同的
foo.prototype.constructor(); // foo.prototype
```

有可能作为一些对象定义的方法来调用函数，但是this将不会设置为这个对象。

```javascript
var foo = {
  bar: function () {
    console.log(this);
    console.log(this === foo);
  }
};
 
foo.bar(); // foo, true
var exampleFunc = foo.bar;
console.log(exampleFunc === foo.bar); // true
 
// 再一次，同一个function的不同的调用表达式，this是不同的
exampleFunc(); // global, false
```

那么，调用函数的方式如何影响this值？为了充分理解this值是如何确定的，我们需要详细分析其**内部类型（internal type）**之一——引用类型（Reference type）。

>  译者注：这个引用类型跟我们在语言上层层面常说的引用类型不是同一个东西，它是语言内部一个**规范类型（Specification Type）**，是只存在于规范里的抽象类型。[了解更多](https://www.zhihu.com/question/31911373)

## 引用类型（Reference type)

使用伪代码我们可以将引用类型的值可以表示为拥有两个属性的对象——base（即拥有属性的那个对象），和 propertyName （es5增加了 static）。

```javascript
var valueOfReferenceType = {
  base: <base object>,
  propertyName: <property name>,
  strict: true, // es5增加的，标志是否以严格模式解析引用的标志。
};
```

引用类型的值仅存在于两种情况中：

1.   当我们处理一个标识符时
2.   或一个属性访问器

标识符的处理过程在 [Chapter 4. Scope chain](http://dmitrysoshnikov.com/ecmascript/chapter-4-scope-chain/) 里详细讨论，在这里我们只需要知道，在该算法的返回值中，总是一个引用类型的值（这对this来说很重要）。

标识符是变量名，函数名，函数参数名和全局对象中未识别的属性名。例如，下面标识符的值：

```javascript
var foo = 10;
function bar() {}
```

在操作的中间结果中，`引用类型` 对应的值如下：

```javascript
var fooReference = {
  base: global,
  propertyName: 'foo'
};
 
var barReference = {
  base: global,
  propertyName: 'bar'
};
```

为了从`引用类型`的值获取对象的真实值，有一个 `GetValue` 方法，可以用伪代码描述如下：

```javascript
function GetValue(value) {
  if (Type(value) != Reference) {
    return value;
  }
  var base = GetBase(value);
  if (base === null) {
    throw new ReferenceError;
  }
  return base.[[Get]](GetPropertyName(value));
}
```

内部的 `[[Get]]` 方法返回对象属性的**真实值**，包括从原型链继承的属性的分析：

```javascript
GetValue(fooReference); // 10
GetValue(barReference); // function object "bar"
```

**属性访问器** 是应该熟悉的，它有两种变体：**点表示法**（当属性名称是正确的标识符且事先已知时）或方**括号表示法**：

```JavaScript
foo.bar();
foo['bar']();
```

在计算中间的返回值中，引用类型对应的值如下：

```javascript
var fooBarReference = {
  base: foo,
  propertyName: 'bar'
};
  
GetValue(fooBarReference); // function object "bar"
```

那么，从最重要的意义上来说，引用类型的值与函数上下文中的 this 的值是如何关联起来的呢？这个关联的过程是这篇文章的核心。 在一个函数上下文中确定this的值的通用规则如下：

> 在一个函数上下文中，this 的值由调用者提供，并由调用表达式的当前形式（函数调用的语法编写方式）决定。
>
> 如果调用括号（...）的左边是引用类型的值，this 将设为这个引用类型值的 base 对象
>
> 在其他情况下（与引用类型不同的任何其它属性），this 的值都为 null。不过，实际不存在 this 的值为 null 的情况，因为当 this的值为 null 的时候，其值会被隐式转换为全局对象。

下面让我们看个例子：

```javascript
function foo() {
  return this;
}
 
foo(); // global
```

我们看到在调用括号的左边有一个引用类型值（因为foo是一个标识符）

```javascript
var fooReference = {
  base: global,
  propertyName: 'foo'
};
```

相应地，这个值被设置为引用类型的基对象，即全局对象。

同样，使用属性访问器：

``` javascript
var foo = {
  bar: function () {
    return this;
  }
};
 
foo.bar(); // foo
```

同样，我们拥有一个`引用类型`的值，其 base 是 `foo` 对象，在函数 `bar` 激活时将 base 设置给 `this`。

```javascript
var fooBarReference = {
  base: foo,
  propertyName: 'bar'
};
```

然而，使用*另一种形式的调用表达式*激活*相同的函数*时，this 已经是另一个值

```javascript
var test = foo.bar;
test(); // global
```

因为 `test` 作为标识符，产生了其他引用类型的值，该值的base（全局对象）被设置为 `this` 的值。

```javascript
var testReference = {
  base: global,
  propertyName: 'test'
};
```

> 注意，在ES5的严格模式下，该值不强制转换为global object，而是设置为 `undefined`。

现在，我们可以很明确的说明，为什么用不同的形式激活同一个函数会产生不同的this，答案在于不同的引用类型（type Reference）的中间值。

```javascript
function foo() {
  console.log(this);
}
 
foo(); // global, because
 
var fooReference = {
  base: global,
  propertyName: 'foo'
};
 
console.log(foo === foo.prototype.constructor); // true
 
// another form of the call expression
 
foo.prototype.constructor(); // foo.prototype, because
 
var fooPrototypeConstructorReference = {
  base: foo.prototype,
  propertyName: 'constructor'
};
```

通过调用表达式动态确定 this 值的另一个（经典）示例：

```javascript
function foo() {
  console.log(this.bar);
}
 
var x = {bar: 10};
var y = {bar: 20};
 
x.test = foo;
y.test = foo;
 
x.test(); // 10
y.test(); // 20
```



## 函数调用和非引用类型

那么，正如我们已经指出，当调用括号的左边不是引用类型而是其它类型，this的值自动设置为null，实际最终this的值被隐式转换为全局对象。

让我们思考下面这种函数表达式：

```javascript
(function  () {
  alert(this); // null => global
})();
```

在这个例子中，我们有一个函数对象但不是引用类型的对象（因为它不是标示符，也不是属性访问器），相应地，this的值最终被设为全局对象。

更多复杂的例子：

```javascript
var foo = {
  bar: function () {
    alert(this);
  }
};
  
foo.bar(); // Reference, OK => foo
(foo.bar)(); // Reference, OK => foo
  
(foo.bar = foo.bar)(); // global?
(false || foo.bar)(); // global?
(foo.bar, foo.bar)(); // global?
```

那么，为什么我们有一个属性访问器，它的中间值应该为引用类型的值，但是在某些调用中我们得到this的值不是base对象，而是global对象？

问题出现在后面的三个调用，在执行一定的操作运算之后，在调用括号的左边的值不再是引用类型。

第一个例子很明显———明显的引用类型，结果是，this为base对象，即foo。

在第二个例子中，分组操作符(译者注：这里的分组操作符就是指foo.bar外面的括号"()")没有实际意义，想想上面提到的，从引用类型中获得一个对象真正的值的方法，如GetValue (参考[11.1.6](http://bclary.com/2004/11/07/#a-11.1.6))。相应的，在分组操作的返回值中———我们得到的仍是一个引用类型。这就是this的值为什么再次被设为base对象，即 foo。

第三个例子中，与分组操作符不同，赋值操作符调用了GetValue方法(参考[11.13.1](http://bclary.com/2004/11/07/#a-11.13.1)的第三步)。返回的结果已经是函数对象（不是引用类型），这意味着this的值被设为null，实际最终结果是被设置为global对象。

第四个和第五个也是一样——逗号操作符和逻辑操作符（OR）调用了GetValue 方法，相应地，我们失去了引用类型的值而得到了函数类型的值，所以this的值再次被设为global对象。



## 引用类型和 this 为 null

有一种情况，如果调用方式确定了引用类型的值，不管怎样，只要this的值被设置为 null，其最终就会被隐式转换成 global 。当引用类型值的base对象是[活动对象]()时，就会导致这种情况。

内部函数被父函数调用，此时我们就能够看到上面说的那种特殊情况。正如我们在 [变量对象章节]() 学到的一样，局部变量、内部函数、形式参数都储存在给定函数的***活动对象***中。

```javascript
function foo() {
  function bar() {
    console.log(this); // global
  }
  bar(); // the same as AO.bar()
}
```

活动对象总是作为 this 的值返回——null（即伪代码AO.bar()相当于null.bar()）。这里我们再次回到上面描述的情况，this的值最终还是被设置为*全局对象*。

有一种情况除外：“在`with`语句中调用函数，且在 `with` 对象(译者注：即下面例子中的__withObject)中包含函数名属性时”。`with` 语句将其对象添加在作用域链最前端，即在活动对象的前面。那么对应的，`引用类型（Reference）`有值(通过标识符或属性访问器)，其base对象不再是激活对象，而是 `with` 语句的对象。顺便提一句，这种情况不仅跟内部函数相关，还跟全局函数相关，因为`with`对象比作用域链里的最前端的对象(全局对象或一个活动对象)还要靠前。

```javascript
var x = 10;
 
with ({
  foo: function () {
    console.log(this.x);
  },
  x: 20
}) {
  foo(); // 20
}
// because
var  fooReference = {
  base: __withObject,
  propertyName: 'foo'
};
```

在 `catch` 语句的实际参数中的函数调用存在类似情况：在这种情况下，`catch` 对象被添加到作用域的最前端，即在活动对象或全局对象的前面。但是，这个特定的行为被确认为是ECMA-262-3的一个bug，这个在新版的ECMA-262-5中修复了。修复后，在特定的激活对象中，`this` 指向全局对象。而不是 `catch`对象。

```javascript
try {
  throw function () {
    console.log(this);
  };
} catch (e) {
  e(); // __catchObject - in ES3, global - fixed in ES5
}
 
// on idea
var eReference = {
  base: __catchObject,
  propertyName: 'e'
};
 
// but, as this is a bug
// then this value is forced to global
// null => global
var eReference = {
  base: global,
  propertyName: 'e'
};
```

同样的情况出现在[命名函数](http://dmitrysoshnikov.com/ecmascript/chapter-5-functions/#feature-of-named-function-expression-nfe)（函数的更多细节参考[Chapter 5. Functions](http://dmitrysoshnikov.com/ecmascript/ru-chapter-5-functions/)）的递归调用中。在函数的第一次调用中，base对象是父活动对象（或全局对象），在递归调用中，base 对象应该是存储着函数表达式可选名称的特定对象。但是，在这种情况下，`this` 的值也总是被设置为 global。

```javascript
(function foo(bar) {
  console.log(this);
  !bar && foo(1); // "should" be special object, but always (correct) global
})(); // global
```

## this在作为构造器调用的函数中的值

还有一个在函数的上下文中与 `this` 的值相关的情况是：函数作为构造器调用时。

```javascript
function A() {
  console.log(this); // newly created object, below - "a" object
  this.x = 10;
}
 
var a = new A();
console.log(a.x); // 10
```

在这个例子中，new操作符调用 `A` 函数内部的 [[[Construct\]]](http://bclary.com/2004/11/07/#a-13.2.2)方法，接着，在对象创建后，调用其内部的 [[[Call\]]](http://bclary.com/2004/11/07/#a-13.2.1)方法，所有相同的函数 `A`都将this的值设置为新创建的对象。

## 手动设置一个函数调用的 this

在Function.prototype中定义了两个方法允许手动设置函数调用时 `this` 的值,它们是`apply`和 `call` 方法（所有的函数都可以访问它们）。它们用接受的第一个参数作为 `this` 的值，`this` 在调用的作用域中使用。这两个方法的区别不大，对于 `apply`，第二个参数必须是数组（或者是类似数组的对象，如 arguments，相反，`call` 能接受任何参数。两个方法必须的参数都是第一个—— `this` 值。

```javascript
var b = 10;
function a(c) {
  console.log(this.b);
  console.log(c);
}
 
a(20); // this === global, this.b == 10, c == 20
a.call({b: 20}, 30); // this === {b: 20}, this.b == 20, c == 30
a.apply({b: 30}, [40]) // this === {b: 30}, this.b == 30, c == 40
```

## 总结

this 关键字对于大多数新人来说都是理解的云里雾里，我也一样，我看了许多关于 this 的解释后总结了本文，要点就是：**理清楚 this 关键字所在的运行时的执行环境**。





## 参考

------

1. [MDN官方对this描述](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/this)
3. [JavaScript 的 this 原理](http://www.ruanyifeng.com/blog/2018/06/javascript-this.html)
3. [ECMA-262-3 in detail. Chapter 3. This.](http://dmitrysoshnikov.com/ecmascript/chapter-3-this/)
4. [关于内部引用类型的简单解释](https://www.zhihu.com/question/31911373)
5. [[深入理解“连等赋值”问题](https://segmentfault.com/a/1190000004224719)]

## 恶补JavaScript基础系列

------

恶补JavaScript基础系列目录地址：https://www.sixtyden.com/archive。
恶补JavaScript基础系列是我在从学校毕业入坑前端的学习产物，它主要是我看完书以及其他资料后的一个浓缩总结。以下是我参考的主要资料：

1. [JavaScript高级程序设计](https://book.douban.com/subject/10546125/)
2. [你不知道的JavaScript（上卷）](https://book.douban.com/subject/26351021/)
3. [陪你读书（JavaScript web前端）](https://www.ximalaya.com/jiaoyu/3740790/)
4. [王福朋的博客](https://www.cnblogs.com/wangfupeng1988/tag/javascript/)
5. [冴羽写博客的地方](https://github.com/mqyqingfeng/Blog)
6. [汤姆大叔深入 JavaScript 系列](https://www.cnblogs.com/TomXu/archive/2011/12/15/2288411.html)
7. [Facebook 的 ECMAScript 理论专家 Dmitry Soshnikov（最优秀的ECMAScript解读）](http://dmitrysoshnikov.com/ecmascript/javascript-the-core/)

本人能力有限，如果有错误或者不严谨的地方，请务必给予指出，十分感谢！愿与君共勉。

（完）