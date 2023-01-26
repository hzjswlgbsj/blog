## 简介

之前总结了 `this` 关键字，这是 JavaScript 中比较重要的概念，`this` 本质上是对象的一个引用方便操作对象上的属性，但有些时候 `this` 的引用会丢失，或者说不会是我们预期的值。在 JavaScript 中有几个方法可以让程序员自己来改变 `this` 的指向，它们是 `call` 、`apply` 和 `bind`。

`call` 和 `apply` 是立即调用函数并改变 `this` 指向，它是一次性的动作；`bind` 是返回一个函数，这个函数执行的上下文已经被切换，下次调用还是切换后的上下文，这是持续性的动作。

## 使用

### call 和 apply

`call` 和 `apply` 都很简单都是在调用函数的时候直接改变 `this` 指向，它们的唯一区别就是额外参数注入的方式不同，`call` 是一个一个的注入参数，而 `apply` 是将参数放到一个数组中一起注入。

举个例子，下面我们分别创建一个对象和一个函数，它们没有任何关联。

```javascript
const person = {
  nickname: "sixty",
  age: 18,
};

function print() {
  console.log(`nickname is ${this.nickname}, age is ${this.age}`);
}

print(); // nickname is undefined, age is undefined
```

上面的代码很简单，很清晰，如何让 `print` 方法访问到 `person` 中的属性呢？你可以使用 `call` 或者 `apply` 来达到目的。

```javascript
print.call(person); // nickname is sixty, age is 18
// or:
print.apply(person); // nickname is sixty, age is 18
```

在看看参数注入的方式

```javascript
const person = {
  nickname: "sixty",
  age: 18,
};

function print(year) {
  console.log(
    `nickname is ${this.nickname}, age is ${this.age}, born in ${year}`
  );
}

print.call(person, 1993); // nickname is sixty, age is 18, born in 1993
// or:
print.apply(person, [1993]); // nickname is sixty, age is 18, born in 1993
```

### bind

`call` 和 `apply` 都是一次性的，有时候你需要某个函数的 `this` 执行一直保持住以便重复调用，那 `bind` 是符合你需求的，他会为你返回一个新的函数，这个函数的 `this` 会一直指向你传入的对象。

```javascript
const person = {
  nickname: "sixty",
  age: 18,
};

function print(year) {
  console.log(
    `nickname is ${this.nickname}, age is ${this.age}, born in ${year}`
  );
}

const newPrint = print.bind(person, 1993);
newPrint(); // // nickname is sixty, age is 18, born in 1993
newPrint(); // // nickname is sixty, age is 18, born in 1993
```

这样就显式的保留了 `this` 的指向，符合预期

## 实现

接下来探究一下他们是如何改变执行上下文的，然后再尝试简单模拟实现他们的功能。

### 模拟 call

通过使用案例可以很容易总结出来：

1. call 改变了 this 的指向
2. call 帮助调用了函数
3. 可以注入参数
4. 正确的返回值

先想想第一点，如何改变函数调用时的上下文呢，可不可以反过来，我们将函数以属性的方式直接加到目标对象上，那此时函数作为目标对象的属性被调用的时候 this 不就是指向的目标对象吗

```javascript
const person = {
  nickname: "sixty",
  age: 18,
  print: function () {
    console.log(`nickname is ${this.nickname}, age is ${this.age}`);
  },
};

person.print(); // nickname is sixty, age is 18
```

但是这有点问题，我们改变原来的对象了，这个方法被添加到原来对象中去了，这是不合理的。不过不用担心，我们不是有 `delete` 操作符吗，调用完了给它删了就好了，可以尝试写第一步的实现了

```javascript
Function.prototype.myCall = function (context) {
  // 首先要获取到未来调用myCall的那个函数，此时其实就是this
  context.fn = this; // 函数名称叫fn或者其他的都行，反正都是要被删除的
  context.fn(); // 以context成员变量的方式调用fn
  delete context.fn; // 删除fn
};

// 测试
const person = {
  nickname: "sixty",
  age: 18,
};

function print() {
  console.log(`nickname is ${this.nickname}, age is ${this.age}`);
}

print.myCall(person); // nickname is sixty, age is 18
```

可以，没问题！第一步和第二步都完成了，继续实现第三步，其实就是在 `myCall` 接受不定数量的参数。这很简单，有 `Arguments` 帮助我们，可以将 argument 类数组中的参数提取出来放到一个数组中，方便后面传入到被调用函数的参数中去。

```javascript
// 因为arguments是类数组对象，所以可以用for循环
var args = [];
for (var i = 1, len = arguments.length; i < len; i++) {
  args.push("arguments[" + i + "]");
}

// 执行后 args为 ["arguments[1]", "arguments[2]", ...]
```

将拿到的参数放到被调用函数的参数中

```javascript
context.fn(args.join(","));
```

看起来没毛病是吧，我开始也觉得没问题，仔细看看 args 里面保存的是 `"arguments[1]"` 这种字符串，通过 ES6 倒是不用再存一遍 arguments 了，直接解构就行

```javascript
Function.prototype.myCall = function (context) {
  context.fn = this;
  context.fn(...Array.from(arguments).slice(1));
  delete context.fn;
};
// 或者再简单点，在 myCall 直接解构
Function.prototype.myCall = function (context, ...args) {
  context.fn = this;
  let result = context.fn(...args);
  delete context.fn;
};
```

但是 `call` 方法本来就是 ES3 的方法，用 ES6 的语法是模拟就感觉很奇怪，要在 ES3 中将字符串 `"arguments[1]"` 作为代码解析那可以使用 `eval` 。

```javascript
Function.prototype.myCall = function (context) {
  context.fn = this;
  var args = [];
  for (var i = 1, len = arguments.length; i < len; i++) {
    args.push("arguments[" + i + "]");
  }
  eval("context.fn(" + args + ")");
  delete context.fn;
};
```

注意这里用 `+` 连接可以直接将数组转成字符串

```javascript
console.log("" + [1, 2, 3] + ""); // 1,2,3
eval("context.fn(" + args + ")"); // eval执行后 context.fn(arguments[1], arguments[2])
```

最后一步处理被调用函数的返回值

```javascript
Function.prototype.myCall = function (context) {
  var context = context || window; // 1.this 参数可以传 null，当为 null 的时候，视为指向 window
  context.fn = this;

  var args = [];
  for (var i = 1, len = arguments.length; i < len; i++) {
    args.push("arguments[" + i + "]");
  }

  var result = eval("context.fn(" + args + ")");

  delete context.fn;
  return result;
};
```

这样一个简单版本的 `call` 方法就模拟出来了，其实还有很多问题没考虑和解决，比如出入的 `context` ，本来就有 `fn` 属性那就炸了，可以想办法搞个特殊字符串，或者用 ES6 的 `Symbol` , 而且 `context` 还有可能被传入基本数据类型比如字符串，这都得处理，但是模拟实现本来就是学习语言设计，正式环境肯定不会使用自己实现的。

### 模拟 apply

apply 的实现跟 call 类似，在这里直接给代码

```javascript
Function.prototype.myApply = function (context, arr) {
  var context = Object(context) || window;
  context.fn = this;

  var result;
  if (!arr) {
    result = context.fn();
  } else {
    var args = [];
    for (var i = 0, len = arr.length; i < len; i++) {
      args.push("arr[" + i + "]");
    }
    result = eval("context.fn(" + args + ")");
  }

  delete context.fn;
  return result;
};
```

## 参考

1. [不能使用 call,apply,bind，如何用 js 实现 call 或者 apply 的功能？](https://www.zhihu.com/question/35787390)
2. [Understanding This, Bind, Call, and Apply in JavaScript](https://www.taniarascia.com/this-bind-call-apply-javascript/)
3. [JS 中的 call、apply、bind 方法详解](https://segmentfault.com/a/1190000018270750)
