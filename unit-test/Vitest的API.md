## test 和 it

test 和 it 从功能上讲是一样的，都是创建一个测试用例，他们的不同主要是风格上的。 it 来源于 BDD（Behavior-driven development，行为驱动开发），是在 TDD（Test-driven development，测试驱动开发） 的基础之上做了延伸和扩展，它们的区别可[阅读这篇文章](https://www.pluralsight.com/blog/software-development/tdd-vs-bdd)。

BDD 要求开发者使用 `it should xxx` 来描述一个测试用例，主要框架有 Mocha、Jasmine，而 Jest 认为 test 比 it 可读性更好，但是他们之间互相都是有兼容的，所以都包含 test 和 it 这两个 API，vitest 当然也是支持两种。

## describe

describe 创建了一个测试套件（test suit），里面包含了多个测试用例，比如下面的例子，里面包含了两个相同行为或者类似行为的测试用例

```javascript
import { describe, it, expect } from"vitest"

describe("add"，() => {
  const user = {
    name:"sixty"
  }
  it('should add a item to todos', () => {
    expect(ucer.name).boBe('sixty')
  })

  it('should add a item to todos with reverse command', () => {
    expect(ucer.name).boBe('ytxis')
  })
})
```

同时 describe 拥有一些子方法用来控制这个测试套件的行为，比如 `skip()` 方法可以跳过本分组的测试用例， `only()` 方法可以只执行本分组的测试用例等等。当然， describe 是可以嵌套是使用的，但是不建议嵌套太多。

## expect

expect 就是断言，可以理解为逻辑判断。

### toBe

```javascript
it("toBe", () => {
  expect(1).toBe(1);
});
```

这段代码表示，断言 1 是否等于 1，`toBe` 可以类比为 '==='，一般用于值类型的判断。

### toEqual

```javascript
it("toEqual", () => {
  const user = {
    name: "sixty",
  };
  expect(user).toEqual({
    name: "sixty",
  });
});
```

这段代码表示，断言 `user` 是否等于 {name: "sixty"}，`toEqual` 一般用于对象类型的判断。注意这里跟 JavaScript 引用类型之间的判断不一样，这里只会判断 **值** 是否相等，在本例中测试是通过的。

### toTruthy

```javascript
it("toTruthy", () => {
  const testTruthy1 = 1;
  const testTruthy2 = true;
  const testTruthy3 = "1";
  expect(testTruthy1).toTruthy(); // 通过
  expect(testTruthy2).toTruthy(); // 通过
  expect(testTruthy3).toTruthy(); // 通过
});
```

这段代码表示，断言 testTruthy 是否为布尔真。

### toFalsy

```javascript
it("toFalsy", () => {
  const testFalsy1 = 0;
  const testFalsy2 = false;
  const testFalsy3 = "";
  expect(testFalsy1).toFalsy(); // 通过
  expect(testFalsy2).toFalsy(); // 通过
  expect(testFalsy3).toFalsy(); // 通过
});
```

这段代码表示，断言 testTruthy 是否为布尔假。

### toContain

```javascript
it("toContain", () => {
  const item1 = {
    name: "sixty",
  };
  const item2 = {
    name: "zhangsan",
  };
  const list = [item1, item2];
  expect(list).toContain(item1); // 通过
  expect("<div>123</div>").toContain("123"); // 通过
});
```

这段代码表示，断言数组或者字符串中是否包含某个值。

### toThrow

```javascript
it("toThrow", () => {
  function getName(name) {
    if (typeof name !== "string") {
      throw new Error("Invalid name");
    }

    return "default";
  }
  expect(() => {
    getName(1);
  }).toThrow; // 通过
});
```

这段代码中 `getName` 正确抛出了错误，所以判定当前函数会抛出异常。

这几个断言方法是比较常见的， 其余方法可以[边写边查](https://vitest.dev/api/)

## setup & teardown

了解生命周期从开始构建（setup）到拆卸载（teardown）的过程中，各个钩子的执行时机。

### 执行时机

直接看如何代码，和执行结果

```javascript
import { beforeAll, beforeEach, afterAll, afterEach, test } from "vitest";
test("first", () => {
  console.log("first");
});
test("second", () => {
  console.log("second");
});
beforeAll(() => {
  console.log("beforeAll");
});
beforeEach(() => {
  console.log("beforeEach");
});
afterAll(() => {
  console.log("afterAll");
});
afterEach(() => {
  console.log("afterEach");
});

// 打印顺序如下
// beforeAll

// stdout | tests/todo.spec.js > first
// beforeEach
// first
// afterEach

// stdout | tests/todo.spec.js > second
// beforeEach
// second
// afterEach

// stdout | unknown test
// afterAll
```

可以看到每一个测试都要经历的是: `beforeEach -> test -> afterEach 这个小循环`，顺序是根据代码书写的顺便来的，他们会被执行多次。然后是 `beforeAll` 和 `afterAll`，在整个大循环的最前面和最后面，都是只执行一次

接下来我们增加 `describe` 并在里面添加 beforeEach 和 afterEach，再来看看整个过程

```javascript
import {
  beforeAll,
  beforeEach,
  afterAll,
  afterEach,
  test,
  describe,
} from "vitest";

beforeAll(() => {
  console.log("beforeAll");
});
beforeEach(() => {
  console.log("beforeEach");
});
test("first", () => {
  console.log("first");
});
test("second", () => {
  console.log("second");
});
describe("add", () => {
  test("describe first", () => {
    console.log("describe first");
  });
  test("describe second", () => {
    console.log("describe second");
  });
  beforeEach(() => {
    console.log("describe beforeEach");
  });
  afterEach(() => {
    console.log("describe afterEach");
  });
});

afterEach(() => {
  console.log("afterEach");
});
afterAll(() => {
  console.log("afterAll");
});

// 打印顺序如下
// stdout | tests/todo.spec.js > first
// beforeEach
// first
// afterEach

// stdout | tests/todo.spec.js > second
// beforeEach
// second
// afterEach

// stdout | tests/todo.spec.js > add > describe first
// beforeEach
// describe beforeEach
// describe first
// describe afterEach
// afterEach

// stdout | tests/todo.spec.js > add > describe second
// beforeEach
// describe beforeEach
// describe second
// describe afterEach
// afterEach

// stdout | unknown test
// beforeAll
// afterAll
```

可以看到在 `describe` 内部执行的 test，依然会执行一遍外部的 `beforeEach` 和 `afterEach`，再执行内部的 `beforeEach` 和 `afterEach`。所有的过程可以总结为下图
![vitest的API执行顺序](https://lib.sixtyden.com/vitest%E7%9A%84API%E6%89%A7%E8%A1%8C%E9%A1%BA%E5%BA%8F.jpg)

顺便提一下，`beforeEach` 和 `beforeAll` 可以返回一个函数来代替 `afterEach` 和 `afterAll`，这种设计跟 react 的 useEffect 是一样的。这样代码更加连贯，更加清晰，也更好定位维护。

```javascript
beforeAll(() => {
  console.log("beforeAll");
  // afterAll
  return () => {
    console.log("afterAll");
  };
});
beforeEach(() => {
  console.log("beforeEach");
  // beforeAll
  return () => {
    console.log("beforeAll");
  };
});
```

### 使用时机

#### beforeAll

只执行一次，在最前面执行。当需要执行的测试有依赖要在最开始执行，并且只执行一次的时候使用该钩子。比如数据库的连接和创建，不需要每次测试一次都连接断开反复执行；再比如临时文件，也是只需要生成一次。

#### afterAll

只执行一次，在最后面执行。类比上面，数据库的断开操作，以及临时文件的删除操作都可以在这个钩子中执行。

#### beforeEach

每一个 test 都会执行一次，在测试用例执行之前执行。如果某个测试需要依赖一些前置数据，并且下次测试需要重新生成的需求可以在这个钩子中完成。比如初始化 pinia 和 store

#### afterEach

每一个 test 都会执行一次，在测试用例执行之后执行。如果当前测试中有些数据需要被清理或者卸载可以在这个钩子中完成。比如一些 reset 操作。

## filter

过滤器主要是方便平常进行可控制的测试耽搁或者多个测试，打开 vue 的源码，执行一次全量测试需要不少时间，不可能每次开发过程中都测试全量，所以一般测试框架都会有过滤器。

```javascript
describe("add", () => {
  // 先只测试添加操作
  it.only("should add a item", () => {
    todo.add("吃饭");
    expect(todo.get()).toEqual(["吃饭"]);
  });

  // 先跳过删除操作
  it.skip("should delete a item", () => {
    todo.remove();
    expect(todo.get()).toEqual([]);
  });

  // 可以用于功能点占位，开发之前列举出来，写完一个完善一个测试
  it.todo("edit");
});
```

当然除了在一个套间里面有 filter，你也可以每次只测试一个文件 `yarn test tests/todo.spec.js `。
