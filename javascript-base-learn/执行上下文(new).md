# 执行上下文(new)

JavaScript 中，每当代码运行到一个新的执行上下文（Execution Context），都会创建一套用于追踪变量、作用域和 `this` 的机制。但这种机制在 **ES5** 与 **ES6+** 中有所演进。

## 执行上下文的定义

**执行上下文（Execution Context）** 是 JavaScript 引擎在运行某段代码时所建立的“运行环境”。每个上下文都保存着该段代码运行所需的所有信息。

与 ES5 一致执行上下文的种类基本还是这三种：

- 全局执行上下文（Global Execution Context）
- 函数执行上下文（Function Execution Context）
- `eval` 执行上下文（不推荐）

## ES5 中的执行上下文结构

在 ES5 中，执行上下文主要包含：

| 组成部分 | 描述 |
|----------|------|
| `Variable Object (VO)` | 存储变量、函数声明、参数（函数上下文中又叫 **Activation Object (AO)**） |
| `Scope Chain` | 当前上下文的变量对象 + 外层作用域变量对象的链表 |
| `this` | 当前上下文的 this 指向（依调用方式而定） |

### 全局上下文结构示意（ES5）

```javascript
GlobalExecutionContext {
  VO: {
    var foo = ...,
    function bar() {},
    ...
  },
  ScopeChain: [VO],
  this: window
}
```

### 函数上下文结构示意（ES5）

```javascript
FunctionExecutionContext {
  AO: {
    arguments: [...],
    参数: 值,
    var 定义: 值,
    function 声明: 引用,
  },
  ScopeChain: [AO, 外部上下文VO, ..., Global VO],
  this: 调用时决定
}
```

## ES6+ 的执行上下文变化

自 **ES6 引入 `let/const/class` 等块级作用域**之后，ES5 的 Variable Object 概念已无法准确表达作用域模型。因此标准引入了：

- **LexicalEnvironment（词法环境）**
- **VariableEnvironment（变量环境）**

### ES6+ 中的执行上下文包含

| 组成部分 | 描述 |
|----------|------|
| `LexicalEnvironment` | 存储 `let` / `const` / 函数声明等绑定（支持块级作用域） |
| `VariableEnvironment` | 专用于存储 `var` 声明的变量（函数作用域） |
| `this` | 当前执行上下文的 `this` 绑定 |
| `IsStrictMode` | 是否为严格模式 |
| `Function` | 正在执行的函数引用（函数上下文才有） |

### 结构关系图（简化示意）

```javascript
ExecutionContext {
  LexicalEnvironment: {
    EnvironmentRecord: {
      let a, const b, function foo() {}
    },
    Outer: <父词法环境>
  },
  VariableEnvironment: {
    EnvironmentRecord: {
      var x, var y
    },
    Outer: <父词法环境>
  },
  ThisBinding: object,
  IsStrictMode: true/false
}
```

## 词法环境（LexicalEnvironment）

### 每个 LexicalEnvironment 包含两个组件

1. **Environment Record**：保存当前作用域中所有变量和函数的绑定（如 `let x = 1`）

2. **Outer Environment Reference**：指向其上一级词法环境（构成作用域链）

### 环境记录的两种形式

| 类型 | 说明 |
|------|------|
| Declarative Environment Record | `let`、`const`、函数声明等绑定 |
| Object Environment Record | `with` 环境或全局环境（使用 `window` 对象） |

## 作用域链的本质演化

| 概念 | ES5 表达 | ES6+ 表达 |
|------|----------|-----------|
| 作用域链 | Scope Chain（VO 组成） | Outer LexicalEnvironment 递归结构 |
| 当前作用域 | VO / AO | 当前 LexicalEnvironment.EnvironmentRecord |
| 父作用域 | ScopeChain 中的前一个对象 | LexicalEnvironment.Outer |

## 举例对比

以如下代码为例：

```javascript
function foo(x) {
  var y = 2;
  let z = 3;
  function bar() {}
}
```

### ES5 的视角

- 所有变量和函数都在 AO 中
- 不区分 let/var（因为没有 let）

```javascript
AO = {
  arguments: { x: 1 },
  x: 1,
  y: 2,
  bar: <function>
}
```

### ES6+ 的视角

- `var y` → VariableEnvironment
- `let z`、`function bar` → LexicalEnvironment

```javascript
VariableEnvironment = {
  EnvironmentRecord: {
    y: 2
  }
}

LexicalEnvironment = {
  EnvironmentRecord: {
    z: 3,
    bar: <function>
  }
}
```

## this 的绑定规则（ES5 / ES6 一致）

- 默认绑定：`this` 是全局对象（非严格模式下）
- 显式绑定：使用 `call`、`apply`、`bind`
- 对象方法调用：谁调用谁是 `this`
- 构造函数：`new` 会绑定到新对象
- 箭头函数：**没有自己的 this，继承自外层上下文**

## 总结对比表

| 特性 | ES5 | ES6+ |
|------|-----|------|
| 变量绑定 | VO（函数为 AO） | Lexical + Variable Environment |
| 作用域链 | Scope Chain | OuterEnvironmentReference |
| 块级作用域 | 不支持 | 支持（let/const/class） |
| this | 支持绑定规则 | 一致（箭头函数例外） |
| let/const/class | 不支持 | 支持 |
| TDZ（暂时性死区） | 无 | 有 |

> ES5 执行上下文简单清晰，但无法支持现代语言特性。  
> ES6+ 引入 Lexical Environment 与 Variable Environment，更精细地管理作用域与绑定，成为现代 JS 引擎执行模型的核心基础。
