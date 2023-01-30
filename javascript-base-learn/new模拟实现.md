# 恶补 JavaScript 基础之 new 模拟实现

> **new 运算符**创建一个用户定义的对象类型的实例或具有构造函数的内置对象的实例。 -- MDN

根据 MDN 的描述，new 关键字会进行如下操作：

1. 创建一个空的简单 JavaScript 对象（即 **{}**）；
2. 为步骤 1 新创建的对象添加属性 ****proto****，将该属性链接至构造函数的原型对象；
3. 将步骤 1 新创建的对象作为 **this** 的上下文；
4. 如果该函数没有返回对象，则返回 **this**。

当代码 new Foo(...) 执行时，会发生以下事情：

1. 一个继承自 `Foo.prototype` 的新对象被创建。
2. 使用指定的参数调用构造函数 `_Foo_`，并将 **this** 绑定到新创建的对象。`new Foo` 等同于 `_new Foo()_`，也就是没有指定参数列表，`Foo` 不带任何参数调用的情况。
3. 由构造函数返回的对象就是 `new` 表达式的结果。如果构造函数没有显式返回一个对象，则使用步骤 1 创建的对象。（一般情况下，构造函数不返回值，但是用户可以选择主动返回对象，来覆盖正常的对象创建步骤）

看一个使用例子：

```javascript
function Singer(name, age) {
  this.name = name;
  this.age = age;
}

Singer.prototype.habit = "sing";
Singer.prototype.sayName = function () {
  console.log("name is " + this.name);
};

var someone = new Singer("sixty", 18);
console.log(someone.name);
console.log(someone.habit);
someone.sayName();
```

可以看到，实例 someone 是一个新的对象，但是可以访问到构造函数里的属性，还能访问到构造函数的原型中的属性，可以先尝试实现这两个需求。

`new` 是原生的操作符关键字，模拟的话只能是一个函数，`newFunction` ，使用方式也有一点区别

```javascript
function Singer() {
  this.name = name;
}

// 使用 new
var someone = new Singer("sixty");
// 使用 newFunction
var someone = newFunction(someone, "sixty");
```

首先 new 的结果是一个新对象，所以在模拟实现的时候，我们也要建立一个新对象，这个新对象具有构造函数里的属性，想想经典继承的例子，我们可以使用 `apply` 来给新对象添加新的属性。

在 [《恶补 JavaScript 基础之原型与原型链》](https://www.sixtyden.com/#/docs/javascript-base-learn/%E5%8E%9F%E5%9E%8B%E4%B8%8E%E5%8E%9F%E5%9E%8B%E9%93%BE)中，我们知道实例的 `__proto__` 属性会指向构造函数的 `prototype`，也正是因为建立起这样的关系，实例可以访问原型上的属性。

```javascript
// 第一版代码
function newFunction() {
  var obj = new Object(); // 从Object.prototype上克隆一个对象
  var constructor = [].shift.call(arguments); // 取得外部传入的构造器(第一个参数)
  obj.__proto__ = constructor.prototype; // 修改构造函数的原型
  constructor.apply(obj, arguments); // 借用外部传入的构造器给obj设置属性
  return obj; // 确保构造器返回一个对象
}
```

测试一下

```javascript
function newFunction() {
  var obj = new Object(); // 从Object.prototype上克隆一个对象
  var constructor = [].shift.call(arguments); // 取得外部传入的构造器(第一个参数)
  obj.__proto__ = constructor.prototype; // 修改构造函数的原型
  constructor.apply(obj, arguments); // 借用外部传入的构造器给obj设置属性
  return obj; // 确保构造器返回一个对象
}

function Singer(name, age) {
  this.name = name;
  this.age = age;
}

Singer.prototype.habit = "sing";
Singer.prototype.sayName = function () {
  console.log("name is " + this.name);
};

var someone = newFunction(Singer, "sixty", 18);
console.log(someone.name); // sixty
console.log(someone.habit); // sing
someone.sayName(); // name is sixty
```

看起来已经完成功能了，但是构造函数有返回值的情况还没处理，先看看原生 `new` 的效果：

```javascript
function Singer(name, age) {
  this.name = name;
  this.age = age;
  return {
    name: this.name,
    habit: "sing",
  };
}

Singer.prototype.sayName = function () {
  console.log("name is " + this.name);
};

var someone = new Singer("sixty", 18);
console.log(someone.name); // sixty
console.log(someone.habit); // sing
console.log(someone.age); // undefined
someone.sayName(); // Uncaught TypeError: someone.sayName is not a function
```

很明显 `someone` 只能访问返回对象中的属性，而且如果构造函数中如果返回一个基本数据类型会怎么样呢，在看一个例子：

```javascript
function Singer(name, age) {
  this.habit = "sing";
  this.age = age;
  return "I love you!";
}

var someone = new Singer("sixty", 18);
console.log(someone.name); // undefined
console.log(someone.habit); // sing
console.log(someone.age); // 18
```

可以看到返回一个基本数据类型的话，是按照没有返回值处理的，所以在模式实现的时候需要拿到返回值，并做类型判断

```javascript
// 第一版代码
function newFunction() {
  var obj = new Object(); // 从Object.prototype上克隆一个对象
  var constructor = [].shift.call(arguments); // 取得外部传入的构造器(第一个参数)
  obj.__proto__ = constructor.prototype; // 修改构造函数的原型
  var res = constructor.apply(obj, arguments); // 借用外部传入的构造器给obj设置属性，拿到构造函数执行后的返回值
  return typeof res === "object" ? res : obj; // 确保构造器返回一个对象
}
```

不使用 **proto** 的版本

```javascript
function newFunction() {
  var constructor = [].shift.call(arguments); // 取得外部传入的构造器(第一个参数)
  var instance = Object.create(constructor.prototype); // 创建constructor实例 instance
  var temp = constructor.apply(instance, arguments); // 使用apply函数运行args, 把 instance 绑定到 this
  return typeof temp === "object" && temp !== null ? temp : instance; //返回对象判断 是object 还是 null 还是实例
}
```
