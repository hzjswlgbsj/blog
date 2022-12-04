# 恶补 JavaScript 基础之理解 JavaScript 对象

说实话，我刚开始学习 JavaScript 的时候，别人会直接告诉我它是一门面向过程的语言，我竟然就直接信了他的邪。
前段时间我都还有如下疑问：

- 为什么 JavaScript（直到 ES6）有对象的概念，但是却没有像其他的语言那样，有类的概念呢
- 为什么在 JavaScript 对象里面可以自由添加属性，而其他语言不能

我在网上看到有些人强调：JavaScript 不是**面向对象**的语言，而是**基于对象**的语言。但是既然 [官方](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/A_re-introduction_to_JavaScript) 都已经说了 JavaScript 是一门面向对象的语言（在很多经典书籍中也提到了这个，比如《JavaScript 高级程序设计》第 6 章的 6.4 小结），我想我们有必要深入了解一下 JavaScript 的对象设计。

> 注意：昨天我同事说中文的原文翻译添加了翻译者自己的理解，我不由的要提醒，我写的文章也是个人理解，请大家带着辨证的眼光看待！
>
> 英文原文：JavaScript is a multi-paradigm, dynamic language with types and operators, standard built-in objects, and methods.
>
> **个人理解**：直接硬翻译过来就是：JavaScript 是一种**多范式**的**动态语言**，具有类型和操作符、标准内置对象和方法。动态主要体现在 JavaScript 在运行时对象是可以改变的，多范式体现在，可以用原型来实现面向对象编程，也可以用大量高阶函数来模拟函数式编程等（原文下文也有提到）。原文下文便明确了 JavaScript 面向对象编程的范式。
>
> 英文原文：JavaScript supports object-oriented programming with object prototypes, instead of classes (see more about prototypical inheritance and ES2015 classes).
>
> **个人理解**：JavaScript 支持使用对象原型的面向对象编程，**而不是类**。这里明确指出只是 JavaScript 实现面向对象的方式不是类而已。然而在 ECMAscript2015 却自己以类的形式封装了原型，导致 JavaScript 看上去更加面向对象，毕竟类是面向对象实现得最好的方式嘛。

其实要探索 JavaScript 是否是面向对象的语言你首先得有一个认知：**「类」只是面向对象的一种实现方式，原型也许是另一种实现方式**。所以，在探索面向对象最本真的定义的时候，我们要忘记「类」这种面向对象的实现方式，回归到对象最朴素的概念。

## 到底什么是面向对象呢

那我们还是要先说说什么是对象吧，中文中的“对象”我很容易想到男女朋友这个方面，毕竟周围的人都被父母催婚了。但是，Object 在英文中是一切事物的总称，这和面向对象的抽象思维有互通之处吧。

我们应该知道对象这个词语不是计算机领域凭空造出来的概念，它是跟随人类思维模式产生的一种抽象，所以说面向对象被普遍认为是更接近于人类思维的一种 [编程范式](https://zh.wikipedia.org/wiki/编程范型)。

在《面向对象分析与设计》这本书中，Grady Booch (格雷迪·布奇) 为我们做了总结什么是对象，他认为对象应该是下列事物之一：

1. 一个可以触摸或者可以看见的东西
2. 人类的智力可以理解的东西
3. 可以指导思考或行动（进行想象或世家动作）的东西

上面扯的东西主要是想暂时清空你脑子里面，基于 **类** 的实现的面向对象的概念和基础设施，学过 Java 或者 C++的话就很容易想到类这个概念，因为基于 **类** 来来抽象描述对象的方式是最为成功的方式，但是这会阻碍我们理解 JavaScript 的对象。

我们具体来谈一下几个 JavaScript 相关的问题吧： JavaScript 对象的本质是什么？JavaScript 对象和类之间的关系？对象和引用类型数据的关系？

### JavaScript 对象的本质是什么

ECMA-262 把对象定义为：**无序属性的集合，其属性可以包含基本值、对象或者函数**。即对象是一组没有特定顺序的值，对象的每个属性或方法都有一个名字，而这个名字都映射到一个值。**故对象的本质是一个散列表**：其中是一组名值对，值可以是数据或函数。

### 对象和类的关系

**在 JavaScript 中，对象和类没有任何关系(直到 ES6)**。这是因为 ECMAScript 中根本就没有类的概念，它的对象与其他基于类的语言中的对象是不同的。

### 对象和引用类型的关系

对象跟引用类型肯定不是等价的，只能说对象都是基于引用类型创建的。

JavaScript 抽象对象选择的是比较冷门的方式，那就是**原型**，这也是很多人普遍认为 JavaScript 不是面向对象的原因吧。但是如果我们从运行时的角度来看的话，其实就是在讨论 JavaScript 实际运行中的模型，这是由于任何代码执行都绕不开运行时的对象模型。

幸运的是，从运行时的角度来看的话，我们就可以不用受到“基于类的设施”的困扰，这是因为任何语言运行时，类的概念都是被弱化的。下面我们就来看看 JavaScript 是怎么设计对象模型的。

## JavaScript 对象的特征

> 提示：本节部分内容引用自 winter 大神的《[重学前端](https://time.geekbang.org/column/154)》第六课时内容

不论我们使用什么样的编程语言，我们都应该先去理解对象的本质特征（参考《面向对象分析与设计》）。总结后，对象有如下几个特征：

- 对象具有唯一标识性：即使完全相同的两个对象，也并不是同一个对象
- 对象具有状态：队形具有状态，同一个对象可能处于不同的状态
- 对象具有行为：即对象的状态，可能因为它的行为产生变化

对于第一个特征，对象具有标识性。一般来说，各种语言的对象唯一标识性都是用内存地址来体现的，对象具有唯一标识的内存地址，所以对象具有唯一标识性。

所以 JavaScript 程序员都知道，任何对象都是不相等的（**注意**：null 就是没有（设置），null 并不是对象，将值设置为 null 的变量也不是对象，关于 null 你可以阅读 [更多](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/null)）。比如下面的 o1 和 o2 是不相等的：

```javascript
var o1 = {a: 1}
var 02 = {a: 1}
console.log(o1 === o2)  // false
```

关于第二个和第三个特征“状态”和“行为”，不同的语言有不同的表达方式。比如 C++中用分别用“成员变量”和”“成员函数”来描述它们，而 Java 称他们为“属性”和“方法”，然而在 JavaScript 中它们被统一抽象为“属性”。请看如下代码：

```javascript
var o = {
  name: "Sixty",
  sayName() {
    alert(this.name);
  },
};
```

o 就是一个对象，name 和 sayName 都是属性，尽管它们的写法不同，但是对于 JavaScript 来说它们就是普通的属性。所以总结来看的话：在 JavaScript 中，状态和行为都被抽象为了属性。**并且 JavaScript 还有个跟 Java 和 C++ 不同的地方是，JavaScript 对象允许使用者在运行时为对象添加或修改状态和行为**。

下面的代码演示了如何向一个**运行时**的 JavaScript 对象添加一个属性，首先我用字面量的方式创建了一个对象，定义完后我才给它添加了属性 b ，这样在 JavaScript 中是没问题的。

```javascript
var o = { a: 1 };
o.b = 2;
console.log(ob); // 2
```

## JavaScript 对象的两类属性

---

对于 JavaScript 来说，属性并非只是简单的名称和值，JavaScript 用一组特征（attribute）来描述属性（prototype）。JavaScript 有两类属性，数据属性和访问器属性。

### 数据属性

数据属性比较接近于其他语言的属性概念，他具有四个特征

- [[value]]：默认值为 undefined，表示属性的值，可读可写。
- [[writable]]：默认值为 true，决定属性能否被修改
- [[enumeration]]：默认值为 true，决定是否能用 for in 来枚举该属性
- [[configuration]]：默认值为 true，决定该属性能否被删除或者改变特征值，**能否把属性由数据属性改为访问器属性**

> **注意**：上述的默认值是指通过构造函数或者对象字面量创建的对象所自身拥有的属性，而不是 Object.defineProperty( )方法。

大多数情况下我们都使用对象字面量的方式来创建一个对象，这使得它的特征都是默认值，如果我们想要改变它的特征值的话，我们可以使用 Object.defineProperty( )方法来修改属性的默认特征值。下面我们通过例子来深入理解一下。

```javascript
var person = {};
Object.defineProperty(person, "name", {
  writable: false,
  value: "sixty",
});

console.log(person.name); // sixty
person.name = "Tom";
console.log(person.name); // sixty
```

这里我用对象字面量的方法创建了一个对象，但是没有同时创建方法和属性。而是利用了 Object.defineProperty()方法来创建了属性和修改了默认值。这里将 writable 设置为 false,于是后面我试图修改 person.name 时，是无效的。

```javascript
var person = {};
Object.defineProperty(person, "name", {
  value: "sixty",
});

console.log(person.name); // sixty
person.name = "Tom";
console.log(person.name); // sixty
```

这个例子中我删去了`writable:false`，为什么还是不能修改呢？这是因为之前我在介绍特性时，前三个默认为 ture，是在创建对象并创建属性的情况下得到的。对于通过调用`Object.defineProperty()`方法创建的属性，其前三个特性的默认值均为 false，这里需要注意。

```javascript
var person = {};
Object.defineProperty(person, "name", {
  value: "sixty",
  configurable: false,
});

console.log(person.name); // sixty
delete person.name;
console.log(person.name); // sixty
```

这里我们将新建的属性 name 的特性设置为了`configurable: false`因此下面删除属性的操作是无效的。根据上面的例子，可知 configurable，默认就是 false，即使去掉也不可修改。

```javascript
var person = {};
Object.defineProperty(person, "name", {
  value: "sixty",
  configurable: false,
});

console.log(person.name); // sixty

Object.defineProperty(person, "name", {
  value: "sixty",
  configurable: true,
});

console.log(person.name); // Uncaught TypeError: Cannot redefine property: name(…)
```

这里可以说明，即使前一步我们不管默认的 configurable:false ，后面得到的仍是不可配置。于是，可以得出结论，**为了可配置，必须在第一次调用 Object.defineProperty()函数时就将默认的值修改为 true**。

### 访问器属性

访问器属性也有四个特征

- [[enumeration]]：默认值为 true，决定是否能用 for in 来枚举该属性
- [[configuration]]：默认值为 true，决定该属性能否被删除或者改变特征值，**能否把属性由数据属性改为访问器属性**
- [[get]]：默认值为 undefined，在读取属性值时被调用的**函数**。
- [[set]]：默认值为 undefined，在设置属性值时被调用的**函数**。

**注意**：

1. 相对于数据属性，我们发现访问器属性中没有 writable 特性和 value 特性。**这是因为访问器属性不包含数据值**，那么我们当然就不可修改属性的值（用不到 writable 特性），更不用考虑 value 了。
2. **访问器属性不能直接定义，必须是用 Object.defineProperty()来定义**。（通过这个规定我们就能准确地判断出访问器属性和数据属性了）

我们来看一个例子：

```javascript
// 来自《JavaScript高级程序设计》
var book = {
  _year: 2004,
  edition: 1,
};

Object.defineProperty(book, "year", {
  get: function () {
    return this._year;
  },
  set: function (newValue) {
    if (newValue > 2004) {
      this._year = newValue;
      this.edition += newValue - 2004;
    }
  },
});

book.year = 2005;
console.log(book.edition); // 2
```

结合上面代码，我们总结一下：

- 访问器属性不能直接定义，必须使用`Object.defineProperty()`来定义，且该属性具有 set 和 ger 特性，于是可以判断，\_year 和 edition 是数据属性，而 year 是访问器属性。
- 我们看到 \_year 这个数据属性前面是以下划线开头的，这个一种常用的记号，用于表示**只能通过对象方法访问的属性**。从上面的例子中可以看到 get 相当于描述符对象的一个方法，而 \_year 正是在这个对象方法访问的属性。而 edition 既可以通过对象方法访问，也可以由对象直接访问。
- book.year 表示正在**读取访问器属性**，这时会调用 get 函数，并返回了 2004 这个有效的值。
- book.year=2005 表示**写入访问器属性**，这时会调用 set 函数并传入新值，即将 2005 传给 newValue，这个函数决定如何处理数据。
- 这是使用访问器属性的常见方法，即设置一个属性的值可以做我们想做的其他事，比如修改另一个属性的值。

当然，我们在使用字面量创建对象的时候依然可以使用 get 和 set 关键字来创建访问器属性，代码如下：

```javascript
var o = {
  get a() {
    return 1;
  },
};

console.log(o.a); // 1
```

访问器属性跟数据属性不同，它是通过执行 getter 或者 setter 函数来达到赋值取值的目的。这里我们在 getter 里面直接返回了 1，那么以后每次执行 o.a 都会得到 1。

我们可以看到，实际上 JavaScript 对象的运行时是个“属性的集合”，以字符串或者 Symbol 作为 key，以数据属性或者访问器属性为 value。以上面的例子来说的话，对象 o 中的 a 是 key，而 { writable: true, value: 1, configurable: true, enumerable: true } 是 value 。

## 最后

要理解 JavaScript 的对象，我们不能把自己的思维局限在“基于类的面向对象”中，要抛开语言的束缚，思考语言无关的面向对象定义。**但是很多人一提到面向对象，脑子里面就冲出「类」的概念，带着固有的思维去思考问题的那一刻起，也许你就错了**。

JavaScript 在对象的设计上确实跟其他语言不太一样，但是它完全实现了高度动态性的对象系统，这是它的特别之处，这也使得他可以实现面向对象的编程范式，所以 JavaScript 确实是一门面向对象的语言。

## 参考

1. [基于对象和面向对象](https://cloud.tencent.com/developer/article/1004486)
2. [适合初学者的 JavaScript 面向对象](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object-oriented_JS)
3. [对象原型](https://developer.mozilla.org/zh-CN/docs/Learn/JavaScript/Objects/Object_prototypes)
4. [JavaScript 面向对象简介](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript)
5. [理解 JavaScript 对象](https://www.cnblogs.com/zhangshiwen/p/3627085.html)

（完）
