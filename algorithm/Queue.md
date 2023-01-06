## 基本概念

在 FIFO（first in, first out） 数据结构中，将首先处理添加到队列中的第一个元素。

队列是典型的 FIFO 数据结构。插入（insert）操作也称作入队（enqueue），新元素始终被添加在队列的末尾。 删除（delete）操作也被称为出队（dequeue)。 你只能移除第一个元素。

所以，**队列是一种操作受限的线性表数据结构**。

## 用途

作为一种非常基础的数据结构，队列的应用也非常广泛，特别是一些具有某些额外特性的队列，比如循环队列、阻塞队列、并发队列。它们在很多偏底层系统、框架、中间件的开发中，起着关键性的作用。比如高性能队列 Disruptor、Linux 环形缓存，都用到了循环并发队列；Java concurrent 并发包利用 ArrayBlockingQueue 来实现公平锁等。

## 实现

跟 **栈** 一样，队列可以用数组来实现，也可以用链表来实现。用数组实现的栈叫作顺序栈，用链表实现的栈叫作链式栈。同样，用数组实现的队列叫作 **顺序队列** ，用链表实现的队列叫作 **链式队列**。

### 基于数组实现

```typescript
// 用数组实现的队列
class QueueBasedOnArray {
  // 数组：items，数组大小：n
  private items: string[] = [];
  private n: number = 0;
  // head表示队头下标，tail表示队尾下标
  private head: number = 0;
  private tail: number = 0;

  // 申请一个大小为capacity的数组
  public ArrayQueue(capacity: number) {
    items.length = capacity;
    n = capacity;
  }

  // 入队
  public enqueue(item: string): boolean {
    // 如果tail == n 表示队列已经满了
    if (tail === n) return false;
    items[tail] = item;
    ++tail;
    return true;
  }

  // 出队
  public dequeue(): string {
    // 如果head == tail 表示队列为空
    if (head === tail) return null;
    // 为了让其他语言的同学看的更加明确，把--操作放到单独一行来写了
    const ret: string = items[head];
    ++head;
    return ret;
  }
}
```

上面的代码有个问题，随着不停地进行入队、出队操作，head 和 tail 都会持续往后移动。当 tail 移动到最右边，即使数组中还有空闲空间，也无法继续往队列中添加数据了。这个问题该如何解决呢？

如果你在每次进行出队操作都做数据搬移的话，相当于删除数组下标为 0 的数据，要搬移整个队列中的数据，这样出队操作的时间复杂度就会从原来的 O(1) 变为 O(n)。能不能优化一下呢？

实际上，我们在出队时可以不用搬移数据。如果没有空闲空间了，我们只需要在入队时，再集中触发一次数据的搬移操作。借助这个思想，出队函数 dequeue() 保持不变，我们稍加改造一下入队函数 enqueue() 的实现，就可以轻松解决刚才的问题了。下面是具体的代码：

```typescript
public enqueue(item: string): boolean {
  // tail === n表示队列末尾没有空间了
  if (tail === n) {
    // tail === n && head === 0，表示整个队列都占满了
    if (head === 0) return false;
    // 数据搬移
    for (let i = head; i < tail; ++i) {
      items[i-head] = items[i];
    }
    // 搬移完之后重新更新head和tail
    tail -= head;
    head = 0;
  }

  items[tail] = item;
  ++tail;
  return true;
}
```

### 基于链表实现

基于链表的实现，我们同样需要两个指针：head 指针和 tail 指针。它们分别指向链表的第一个结点和最后一个结点。如图所示，入队时，tail.next = new_node, tail = tail.next；出队时，head = head.next。

```typescript
class Node {
  constructor(element) {
    this.element = element;
    this.next = null;
  }
}

class QueueBasedOnLinkedList {
  constructor() {
    this.head = null;
    this.tail = null;
  }

  enqueue(value) {
    if (this.head === null) {
      this.head = new Node(value);
      this.tail = this.head;
    } else {
      this.tail.next = new Node(value);
      this.tail = this.tail.next;
    }
  }

  dequeue() {
    if (this.head !== null) {
      const value = this.head.element;
      this.head = this.head.next;
      return value;
    } else {
      return -1;
    }
  }
}
```

## 循环队列

上面实现的队列是比较低效的，代码搬运的是比较低效的，更有效的方法是使用循环队列。 具体来说，我们可以使用 `固定大小的数组` 和 `两个指针` 来指示起始位置和结束位置。 目的是 `重用` 我们之前提到的 `被浪费的存储`。

要很好的写出循环队列需要总结出检查队列是 `空` 还是 `满` 的策略。

```JavaScript
/**
 * @param {number} k
 */
var MyCircularQueue = function(k) {
  this.arr = new Array(k+1);
  this.front = 0;
  this.tail = 0;
};

/**
 * 向循环队列插入一个元素。如果成功插入则返回真。
 * @param {number} value
 * @return {boolean}
 */
MyCircularQueue.prototype.enQueue = function(value) {
  if (this.isFull()) {
    return false;
  } else {
    this.arr[this.tail] = value;
    this.tail = (this.tail + 1) % this.arr.length;
    return true;
  }
};

/**
 * 从循环队列中删除一个元素。如果成功删除则返回真
 * @return {boolean}
 */
MyCircularQueue.prototype.deQueue = function() {
  if (this.isEmpty()) {
    return false;
  } else {
    this.front = (this.front + 1) % this.arr.length;
    return true;
  }
};

/**
 * 从队首获取元素。如果队列为空，返回 -1
 * @return {number}
 */
MyCircularQueue.prototype.Front = function() {
  if (this.isEmpty()) {
    return -1;
  } else {
    return this.arr[this.front]
  }
};

/**
 * 获取队尾元素。如果队列为空，返回 -1
 * @return {number}
 */
MyCircularQueue.prototype.Rear = function() {
  if (this.isEmpty()) {
    return -1;
  } else {
    return this.arr[(this.tail - 1 + this.arr.length) % this.arr.length];
  }
};

/**
 * 检查循环队列是否为空
 * @return {boolean}
 */
MyCircularQueue.prototype.isEmpty = function() {
  return this.tail === this.front
};

/**
 * 检查循环队列是否已满
 * @return {boolean}
 */
MyCircularQueue.prototype.isFull = function() {
  return (this.tail+1) % this.arr.length === this.front
};

/**
 * Your MyCircularQueue object will be instantiated and called as such:
 * var obj = new MyCircularQueue(k)
 * var param_1 = obj.enQueue(value)
 * var param_2 = obj.deQueue()
 * var param_3 = obj.Front()
 * var param_4 = obj.Rear()
 * var param_5 = obj.isEmpty()
 * var param_6 = obj.isFull()
 */
```

## 队列与 BFS

使用 BFS 的两个主要方案：`遍历` 或 `找出最短路径`。通常，这发生在树或图中。正如我们在章节描述中提到的，BFS 也可以用于更抽象的场景中。

下面给出 BFS 的模板

```java
/**
 * 返回根节点到目标节点之间最短路径的长度。
 */
int BFS(Node root, Node target) {
  Queue<Node> queue;  // 存储等待处理的所有节点
  int step = 0;       // 从根到当前节点的步骤数量
  // initialize
  add root to queue;
  // BFS
  while (queue is not empty) {
      step = step + 1;
      // 迭代已经在队列中的节点
      int size = queue.size();
      for (int i = 0; i < size; ++i) {
          Node cur = the first node in queue;
          return step if cur is target;
          for (Node next : the neighbors of cur) {
              add next to queue;
          }
          remove the first node from queue;
      }
  }
  return -1; // 没有从根到目标的路径
}
```

模板二

```java
/**
 * 返回根节点到目标节点之间最短路径的长度。
 */
int BFS(Node root, Node target) {
  Queue<Node> queue;  // 存储等待处理的所有节点
  Set<Node> used;     // 存储所有使用的节点
  int step = 0;       // 从根到当前节点的步骤数量
  // initialize
  add root to queue;
  add root to used;
  // BFS
  while (queue is not empty) {
    step = step + 1;
    // 迭代已经在队列中的节点
    int size = queue.size();
    for (int i = 0; i < size; ++i) {
      Node cur = the first node in queue;
      return step if cur is target;
      for (Node next : the neighbors of cur) {
        if (next is not in used) {
          add next to queue;
          add next to used;
        }
      }
      remove the first node from queue;
    }
  }
  return -1; // 没有从根到目标的路径
}
```
