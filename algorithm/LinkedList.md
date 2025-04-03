# 链表介绍

与数组相似，链表也是一种 `线性` 数据结构。

```
|---|---|    |---|---|    |---|---|
| 4 | •-|--->| 7 | •-|--->| 9 | •-|---> null        
|---|---|    |---|---|    |---|---| 
```

正如你所看到的，链表中的每个元素实际上是一个单独的对象，而所有对象都通过每个元素中的引用字段链接在一起。

链表结构有很多种，它们分别是：单链表、双向链表和循环链表。我们首先来看最简单、最常用的单链表。

# 单链表

单链表中的每个结点不仅包含值，还包含链接到下一个结点的引用字段。通过这种方式，单链表将所有结点按顺序组织起来。

一般单链表可以标识为如下结构

```typescript
interface SinglyListNode {
  val: numbar;
  next: SinglyListNode;
  SinglyListNode: (x: number) => { val = x }
}
```

在大多数情况下，我们将使用头结点(第一个结点)来表示整个列表。

与数组不同，我们无法在常量时间内访问单链表中的随机元素。 如果我们想要获得第 i 个元素，我们必须从头结点逐个遍历。 我们按 `索引` 来 `访问元素` 平均要花费 `O(N)` 时间，其中 `N` 是链表的长度。

## 实现单链表

### 分析与要求

设计链表的实现。您可以选择使用单链表或双链表。单链表中的节点应该具有两个属性：`val` 和 `next`。`val` 是当前节点的值，`next` 是指向下一个节点的指针/引用。如果要使用双向链表，则还需要一个属性 `prev` 以指示链表中的上一个节点。假设链表中的所有节点都是 0-index 的。

在链表类中实现这些功能：

- get(index)：获取链表中第 `index` 个节点的值。如果索引无效，则返回 `-1`。
- addAtHead(val)：在链表的第一个元素之前添加一个值为 `val` 的节点。插入后，新节点将成为链表的第一个节点。
- addAtTail(val)：将值为 `val` 的节点追加到链表的最后一个元素。
- addAtIndex(index,val)：在链表中的第 `index` 个节点之前添加值为 `val`  的节点。如果 `index` 等于链表的长度，则该节点将附加到链表的末尾。如果 `index` 大于链表长度，则不会插入节点。如果 `index` 小于 0，则在头部插入节点。
- deleteAtIndex(index)：如果索引 `index` 有效，则删除链表中的第 `index` 个节点。

```javascript
MyLinkedList linkedList = new MyLinkedList();
linkedList.addAtHead(1);
linkedList.addAtTail(3);
linkedList.addAtIndex(1,2);   // 链表变为1-> 2-> 3
linkedList.get(1);            // 返回2
linkedList.deleteAtIndex(1);  // 现在链表是1-> 3
linkedList.get(1);            // 返回3
```

### 单链表添加操作

如果要在单链表的 `prev` 节点后面添加一个新的节点步骤如下：

1. 创建一个新的节点 `cur`
2. 将 `prev` 的 `next` 节点链接到 `cur` 的 `next` 字段上
3. 将 `cur` 节点链接到 `prev` 的 `next` 字段

如果是在头节点添加节点，除了上面的步骤外，还需要将 `cur` 节点设置为链表的 `head` 节点。

### 单链表删除操作

如果想从单链表中删除现有结点 `cur`，可以分两步完成：

1. 找到 `cur` 的上一个结点 `prev` 及其下一个结点 `next`
2. 接下来链接 `prev` 到 `cur` 的下一个节点 `next`

在我们的第一步中，我们需要找出 prev 和 next。使用 cur 的参考字段很容易找出 next，但是，我们必须从头结点遍历链表，以找出 prev，它的平均时间是 O(N)，其中 N 是链表的长度。因此，删除结点的时间复杂度将是 O(N)。

空间复杂度为 O(1)，因为我们只需要常量空间来存储指针。

如果要删除头节点的话可以直接将链表的 `head` 指向现在头节点的 `next` 节点

### 实现

```javascript
var MyLinkedList = function() {
    this.head = null
    this.size = 0
};

/** 
 * @param {number} index
 * @return {number}
 */
MyLinkedList.prototype.get = function(index) {
  if (index >= this.size || index < 0) {
    return -1
  } else {
    let curNode = this.head
    let curIndex = 0
    while(curNode) {
      if (curIndex === index) {
        return curNode.val
      } else {
        curIndex++
      }
      curNode = curNode.next
    }

    return -1
  }
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtHead = function(val) {
  if (!this.head) {
    this.head = {val, next: null}
  } else {
    const temp = this.head
    this.head = {
      val,
      next: temp,
    }
  }
  
  this.size++
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtTail = function(val) {
  if (!this.head) {
    this.head = {
      val,
      next: null
    }
  } else {
    let last = null
    let curNode = this.head
    while(curNode) {
      if (!curNode.next) {
        last = curNode
        break
      }
      curNode = curNode.next
    }

    last.next = {
        val,
        next: null
    }
  }
  this.size++
};

/** 
 * @param {number} index 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtIndex = function(index, val) {
  if (index > this.size) {
    return
  } else if (index === this.size) {
    this.addAtTail(val)
  } else if (index <= 0) {
    this.addAtHead(val)
  } else {
    let curNode = this.head
    let last = null
    let curIndex = 0
    while(curNode) {
      if (curIndex === index - 1) {
        last = curNode
        break
      } else {
        curIndex++
      }
      curNode = curNode.next
    }

    const next = last.next
    last.next = {
      val,
      next
    }
  }

  this.size++
};

/** 
 * @param {number} index
 * @return {void}
 */
MyLinkedList.prototype.deleteAtIndex = function(index) {
  if (index >= this.size || index < 0) {
    return
  } else if (index === 0) {
    this.head = this.head.next
  } else {
    let curNode = this.head
    let last = null
    let curIndex = 0
    while(curNode) {
      if (curIndex === index - 1) {
        last = curNode
        break
      } else {
        curIndex++
      }
      curNode = curNode.next
    }

    last.next = last.next ? last.next.next : null
  }

  this.size--
};
```

### 用链表实现 最近最少使用策略LRU(Least Recently Used)

我们维护一个有序单链表，越靠近链表尾部的结点是越早之前访问的。当有一个新的数据被访问时，我们从链表头开始顺序遍历链表。

1. 如果此数据之前已经被缓存在链表中了，我们遍历得到这个数据对应的结点，并将其从原来的位置删除，然后再插入到链表的头部。
2. 如果此数据没有在缓存链表中，又可以分为两种情况：

- 如果此时缓存未满，则将此结点直接插入到链表的头部；
- 如果此时缓存已满，则链表尾结点删除，将新的数据结点插入链表的头部。

简单实现, 题目链接：<https://leetcode.cn/problems/lru-cache/>

```javascript
/**
 * @param {number} capacity
 */
var LRUCache = function (capacity) {
  this.size = 0;
  this.capacity = capacity;
  this.cache = new Map();
  // 使用伪头部和伪尾部节点
  this.head = this.DLinkedNode();
  this.tail = this.DLinkedNode();
  this.head.next = this.tail;
  this.tail.prev = this.head;
};
LRUCache.prototype.DLinkedNode = function (key, value) {
  return {
    key: key,
    value: value,
    prev: null,
    next: null,
  }
}
LRUCache.prototype.moveToHead = function (node) {
  // 先删除，再移动
  this.removeNode(node);
  this.addToHead(node);
}

LRUCache.prototype.removeNode = function (node) {
  node.prev.next = node.next;
  node.next.prev = node.prev;
}

LRUCache.prototype.addToHead = function (node) {
  node.prev = this.head
  node.next = this.head.next
  this.head.next.prev = node
  this.head.next = node;
}
/** 
 * @param {number} key
 * @return {number}
 */
LRUCache.prototype.get = function(key) {
  let node = this.cache.get(key)
  if (!node) {
    return -1
  }

  // 如果在缓存中存在，则删除并移动到头部
  this.moveToHead(node)

  return node.value
};

/** 
 * @param {number} key 
 * @param {number} value
 * @return {void}
 */
LRUCache.prototype.put = function(key, value) {
  // 先在缓存中查找
  let node = this.cache.get(key)

  // 如果 key 存在，先通过哈希表定位，再修改 value，并移到头部
  if (node) {
    node.value = value;
    this.moveToHead(node);
  } else {
    // 如果缓存中没有，那就创建节点并移动到最前面
    node = this.DLinkedNode(key, value);
    this.cache.set(key, node)
    this.addToHead(node)
    this.size++

    // 判断是否超出规定长度
    if (this.size > this.capacity) {
      // 如果超出容量，删除双向链表的尾部节点
      const last = this.tail.prev
      this.removeNode(last)

      // 删除哈希表中对应的项
      this.cache.delete(last.key);
      this.size--;
    }
  }
};
```

# 双链表

双链表与单链表以类似的方式工作，但 `还有一个引用字段`，称为 `“prev”` 字段。有了这个额外的字段，您就能够知道当前结点的前一个结点。

```
         |---|---|---|    |---|---|---|    |---|---|---|
null <---|-• | 4 | •-|--->|   | 7 | •-|--->|---| 9 | •-|---> null        
         |   |   |   |<---|-• |   |   |<---|-• |   |   | 
         |---|---|---|    |---|---|---|    |---|---|---| 
```

一般双链表可以标识为如下结构

```typescript
interface DoublyListNode {
  val: numbar;
  prev: DoublyListNode;
  next: DoublyListNode;
  DoublyListNode: (x: number) => { val = x }
}
```

与单链表类似，我们可以与单链表相同的方式访问数据：

- 我们不能在常量级的时间内访问随机位置。
- 我们必须从头部遍历才能得到我们想要的第一个结点。
- 在最坏的情况下，时间复杂度将是 O(N)，其中 N 是链表的长度。

对于添加和删除，会稍微复杂一些，因为我们还需要处理 `“prev”` 字段。

## 实现双链表

### 分析与要求

要求跟单链表一样，需要实现的方法也是一样

### 双链表添加操作

如果要在单链表的 `prev` 节点之后添加一个新的节点步骤如下：

1. 创建一个新的节点 `cur`
2. 链接 `cur` 与 `prev` 和 `next`，其中 `next` 是 `prev` 原始的下一个节点；
3. 用 `cur` 重新链接 `prev` 和 `next`。

如果是在头节点添加节点，除了上面的步骤外，还需要将 `cur` 节点d的 `next` 直接指向当前头节点。

### 双链表删除操作

如果我们想从双链表中删除一个现有的结点 `cur`，我们可以简单地将它的前一个结点 `prev` 与下一个结点 `next` 链接起来, 并更新 `next` 节点的 `prev`。

如果要删除头节点的话可以直接将链表的 `head` 指向现在头节点的 `next` 节点

### 实现

```javascript
var MyLinkedList = function() {
    this.head = {val: null, prev: null, next: null}
    this.length = 0
    this.addOneNode = () => this.length++
    this.reduceOneNode = () => this.length--
    this.createOneNode = (val = 0, prev = null, next = null) => {
        this.addOneNode()
        return {val, prev, next}
    }
};

/** 
 * @param {number} index
 * @return {number}
 */
MyLinkedList.prototype.get = function(index) {
    // 过滤无效索引
    if (typeof index !== 'number' || index < 0 || index >= this.length) {
        return -1
    }
    let curr = this.head.next
    for (let i = 0; i < index; i++) {
        curr = curr.next
    }
    return curr.val
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtHead = function(val) {
    let firstNode = this.head.next
    if (!firstNode) {
        this.head.next = this.createOneNode(val)
        return true
    }
    let node = this.createOneNode(val, null, firstNode)
    firstNode.prev = node
    this.head.next = node
    return true
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtTail = function(val) {
    let curr = this.head
    while (curr.next) {
        curr = curr.next
    }
    curr.next = this.createOneNode(val, curr, null)
    return true
};

/** 
 * @param {number} index 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtIndex = function(index, val) {
    if (index <= 0) {
        this.addAtHead(val) // index <= 0,在链表头部添加
    } else if (index === this.length) {
        this.addAtTail(val) // index === length, 在链表尾部添加
    } else if (index > this.length) {
        // index > length, 无操作
        return false
    } else {
        let curr = this.head
        for (let i = 0; i < index; i++) {
            curr = curr.next
        }
        let node = this.createOneNode(val, curr, curr.next)
        curr.prev = node
        curr.next = node
    }
    return true
};

/** 
 * @param {number} index
 * @return {void}
 */
MyLinkedList.prototype.deleteAtIndex = function(index) {
    // 过滤无效索引
    if (typeof index !== 'number' || index < 0 || index >= this.length) {
        return -1
    }
    this.reduceOneNode()
    let curr = this.head
    for (let i = 0; i < index; i++) {
        curr = curr.next
    }
    let next = curr.next.next
    curr.next = next
    if (next)  next.prev = curr
};
```

# 双指针技巧

虽然 **双指针技巧** 不是链表专用的，但是也算是比较常用的，在这里熟悉一下 **双指针技巧** 是怎么回事。

两种使用双指针技巧的情景：

- 两个指针 `从不同位置出发`：一个从始端开始，另一个从末端开始；
- 两个指针 `以不同速度移动`：一个指针快一些，另一个指针慢一些。

对于单链表，因为我们只能在一个方向上遍历链表，所以第一种情景可能无法工作。然而，第二种情景，也被称为 `慢指针和快指针技巧` ，是非常有用的。

## 判断环形链表
>
> 给定一个链表，判断链表中是否有环。

一般很直观的就能想到 `缓存` ,遍历链表的时候将节点缓存，每次都判断当前节点是否在缓存中出现。

```javascript
/**
 * @param {ListNode} head
 * @return {boolean}
 */
var hasCycle = function(head) {
  let hash = new Map()
  let curNode = head
  while (curNode) {
    if (hash.has(curNode)) {
      return true
    }
    hash.set(curNode)
    curNode = curNode.next
  }

  return false
}
```

但是我们可以使用 `双指针技巧` 轻松解决这个问题。

想象一下，有两个速度不同的跑步者。如果他们在直路上行驶，快跑者将首先到达目的地。但是，如果它们在圆形跑道上跑步，那么快跑者如果继续跑步就会追上慢跑者。

这正是我们在链表中使用两个速度不同的指针时会遇到的情况：

1. 如果没有环，快指针将停在链表的末尾。
2. 如果有环，快指针最终将与慢指针相遇。
所以剩下的问题是：

这两个指针的适当速度应该是多少？

一个安全的选择是每次移动慢指针一步，而移动快指针两步。每一次迭代，快速指针将额外移动一步。如果环的长度为 `M`，经过 `M` 次迭代后，快指针肯定会多绕环一周，并赶上慢指针。

```javascript
/**
 * @param {ListNode} head
 * @return {boolean}
 */
var hasCycle = function(head) {
  let slow = head
  let fast = head

  // 慢指针每次前进一步，快指针每次前进两步
  while (fast && fast.next) {
    slow = slow.next
    fast = fast.next.next

    if (slow === fast) {
      return true
    }
  }

  return false
}
```

可以继续做一下 [环形链表 II](https://leetcode.cn/leetbook/read/linked-list/jjhf6/)，直接找出链表的环的位置。

# 如何优雅的写出链表代码？

## 一、理解指针或引用的含义

### 1.含义

将某个变量（对象）赋值给指针（引用），实际上就是就是将这个变量（对象）的地址赋值给指针（引用）。

### 2. 示例

p.next = q; 表示 p 节点的后继指针存储了 q 节点的内存地址。

p.next = p.next.next; 表示p节点的后继指针存储了 p 节点的下下个节点的内存地址。

## 二、警惕指针丢失和内存泄漏（单链表）

### 1.插入节点

在节点a和节点b之间插入节点x，b是a的下一节点，，p指针指向节点a，则造成指针丢失和内存泄漏的代码：p.next = x;x.next = p.next; 显然这会导致x节点的后继指针指向自身。
正确的写法是2句代码交换顺序，即：x.next = p.next; p.next = x;

### 2.删除节点

在节点 a 和节点 b 之间删除节点 b，b 是 a 的下一节点，p 指针指向节点 a：p.next = p.next.next;

## 三、利用“哨兵”简化实现难度

### 1.什么是“哨兵”？

链表中的“哨兵”节点是解决边界问题的，不参与业务逻辑。如果我们引入“哨兵”节点，则不管链表是否为空，head指针都会指向这个“哨兵”节点。我们把这种有“哨兵”节点的链表称为带头链表，相反，没有“哨兵”节点的链表就称为不带头链表。

### 2.未引入“哨兵”的情况

如果在p节点后插入一个节点，只需2行代码即可搞定：

```javascript
new_node.next = p.next;
p.next = new_node;
```

但，若向空链表中插入一个节点，则代码如下：

```javascript
if(head == null){
  head = new_node;
}
```

如果要删除节点p的后继节点，只需1行代码即可搞定：

```javascript
p.next = p.next.next;
```

但，若是删除链表的最有一个节点（链表中只剩下这个节点），则代码如下：

```javascript
if(head.next == null){
  head = null;
}
```

从上面的情况可以看出，针对链表的插入、删除操作，需要对插入第一个节点和删除最后一个节点的情况进行特殊处理。这样代码就会显得很繁琐，所以引入“哨兵”节点来解决这个问题。

### 3.引入“哨兵”的情况

“哨兵”节点不存储数据，无论链表是否为空，head指针都会指向它，作为链表的头结点始终存在。这样，插入第一个节点和插入其他节点，删除最后一个节点和删除其他节点都可以统一为相同的代码实现逻辑了。

### 4.“哨兵”还有哪些应用场景？

这个知识有限，暂时想不出来呀！但总结起来，哨兵最大的作用就是简化边界条件的处理。

## 四、重点留意边界条件处理

经常用来检查链表是否正确的边界4个边界条件：

1. 如果链表为空时，代码是否能正常工作？
2. 如果链表只包含一个节点时，代码是否能正常工作？
3. 如果链表只包含两个节点时，代码是否能正常工作？
4. 代码逻辑在处理头尾节点时是否能正常工作？

## 五、举例画图，辅助思考

核心思想：释放脑容量，留更多的给逻辑思考，这样就会感觉到思路清晰很多。

## 六、多写多练，没有捷径

5个常见的链表操作：

1. 单链表反转
2. 链表中环的检测
3. 两个有序链表合并
4. 删除链表倒数第n个节点
5. 求链表的中间节点
