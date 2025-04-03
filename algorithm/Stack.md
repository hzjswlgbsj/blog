# 栈

## 基本概念

在 **LIFO（Last In First Out）** 数据结构中，将首先处理添加到队列中的最新元素。

与队列不同，栈是一个 `LIFO` 数据结构。通常，插入操作在栈中被称作入栈 `push` 。与队列类似，总是在堆栈的末尾添加一个新元素。但是，删除操作，退栈 `pop` ，将始终删除队列中相对于它的最后一个元素。

从栈的操作特性上来看，**栈是一种“操作受限”的线性表**，只允许在一端插入和删除数据。

## 用途

事实上，从功能上来说，数组或链表确实可以替代栈，但你要知道，特定的数据结构是对特定场景的抽象，而且，数组或链表暴露了太多的操作接口，操作上的确灵活自由，但使用时就比较不可控，自然也就更容易出错。

**当某个数据集合只涉及在一端插入和删除数据，并且满足后进先出、先进后出的特性，这时我们就应该首选“栈”这种数据结构**。

## 实现

从刚才栈的定义里，我们可以看出，栈主要包含两个操作，入栈和出栈，也就是在栈顶插入一个数据和从栈顶删除一个数据。

栈既可以用数组来实现，也可以用链表来实现。用数组实现的栈，我们叫作 `顺序栈`，用链表实现的栈，我们叫作 `链式栈`。

### 基于数组实现的顺序栈

```typescript
class ArrayStack {
  private items: string[]; // 数组
  private count: number; // 栈中元素个数
  private n: number;  //栈的大小

  // 初始化数组，申请一个大小为n的数组空间
  construct(int n) {
    this.items = new Array(n).fill(n)
    this.n = n;
    this.count = 0;
  }

  // 入栈操作
  public push(item: string): boolean {
    // 数组空间不够了，直接返回false，入栈失败。
    if (this.count == n) {
      return false
    };

    // 将item放到下标为count的位置，并且count加一
    this.items[this.count] = item;
    ++this.count;
    return true;
  }

  // 出栈操作
  public pop(): string {
    // 栈为空，则直接返回null
    if (this.count == 0) {
      return null
    };

    // 返回下标为count-1的数组元素，并且栈中元素个数count减一
    const tmp: string = this.items[this.count - 1];
    --this.count;
    return tmp;
  }
}
```

### 基于链表实现的链式栈

```typescript
class Node {
  construct(val: string, next: Node) {
    this.val = val;
    this.next = next;
  }
}

class LinkedListStack {
  construct() {
    this.top = null;
    this.length = 0;
  }

  // 入栈操作
  public push(item: string): boolean {
    if (!this.top) {
      this.top = node;
      this.length++;
    } else {
      node.next = this.top;
      this.top = node;
      this.length++;
    }
  }

  // 出栈操作
  public pop(): string {
    let res = this.top || undefined;
    // 删除栈顶
    if (this.top) {
      this.top = this.top.next;
      this.length--;
    }
    return res;
  }
}
```

## 栈与 DFS

与 BFS 类似，`深度优先搜索`（DFS）也可用于查找从根结点到目标结点的路径。在本文中，我们提供了示例来解释 DFS 是如何工作的以及栈是如何逐步帮助 DFS 工作的。

### DFS - 模板 I

正如我们在本章的描述中提到的，在大多数情况下，我们在能使用 BFS 时也可以使用 DFS。但是有一个重要的区别：**遍历顺序**。

与 BFS 不同，`更早访问的结点可能不是更靠近根结点的结点`。因此，你在 DFS 中找到的第一条路径 `可能不是最短路径`。

```java
/*
 * Return true if there is a path from cur to target.
 */
boolean DFS(Node cur, Node target, Set<Node> visited) {
  return true if cur is target;
  for (next : each neighbor of cur) {
    if (next is not in visited) {
      add next to visted;
      return true if DFS(next, target, visited) == true;
    }
  }
  return false;
}
```

### DFS - 模板 II

递归解决方案的优点是它更容易实现。 但是，存在一个很大的缺点：如果递归的深度太高，你将遭受堆栈溢出。 在这种情况下，您可能会希望使用 BFS，或使用显式栈实现 DFS。

这里我们提供了一个使用显式栈的模板：

```java
/*
 * Return true if there is a path from cur to target.
 */
boolean DFS(int root, int target) {
  Set<Node> visited;
  Stack<Node> s;
  add root to s;
  while (s is not empty) {
    Node cur = the top element in s;
    return true if cur is target;
    for (Node next : the neighbors of cur) {
      if (next is not in visited) {
        add next to s;
        add next to visited;
      }
    }
    remove cur from s;
  }
  return false;
}
```
