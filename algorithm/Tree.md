# 树

## 基本概念

树是前端开发中非常重要的数据结构，无论是DOM树、组件树还是算法中的应用，都离不开树的理解。本文将系统介绍树的基本概念、分类和遍历方法。

### 什么是树？

树是一种**非线性数据结构**，它完美模拟了现实世界中许多层次关系比如：

- 文件系统的目录结构
- HTML DOM树
- 公司组织架构
- React/Vue的组件树
- 家族族谱

### 树的组成

树由若干个**节点(Node)**和连接它们的**边(Edge)**组成。每个树都有以下关键部分：

1. **根节点(Root)**: 树的顶端节点，没有父节点
2. **子节点(Child)**: 被其他节点指向的节点
3. **父节点(Parent)**: 指向子节点的节点
4. **叶节点(Leaf)**: 没有子节点的节点
5. **子树(Subtree)**: 某个节点及其所有后代组成的树

![树结构示意图](https://lib.sixtyden.com/tree.jpg)

### 树的代码表示

最基本的树节点可以用如下TypeScript接口表示：

```typescript
interface TreeNode {
  value: any;         // 节点存储的值
  children: TreeNode[]; // 子节点数组
}
```

### 重要术语

- **高度(Height)**：节点到最远叶节点的最长路径边数
- **深度(Depth)**：根节点到该节点的边数
- **层级(Level):**：深度 + 1（根节点为第1层）

---

## 二叉树

**二叉树**是每个节点最多有两个子节点的树结构，分别称为：

- 左子节点(left child)
- 右子节点(right child)

```typescript
interface BinaryTreeNode {
  value: any;
  left: BinaryTreeNode | null;
  right: BinaryTreeNode | null;
}
```

### 二叉树分类

| 类型            | 特点                             | 示例            |
|----------------|---------------------------------|-----------------|
| 满二叉树        | 每个节点都有 0 或 2 个子节点        | 满二叉树          |
| 完全二叉树      | 除最后一层外完全填充，最后一层向左对齐 | 完全二叉树        |
| 二叉搜索树(BST) | 左子节点 ≤ 根节点 ≤ 右子节点        |  BST            |
| 平衡二叉树      | 任意节点的左右子树高度差 ≤ 1        |  AVL 树          |
| 红黑树         | 自平衡的二叉搜索树                  | Map/Set 底层实现 |

### 储存方式

**链式存储（常用）：**

```javascript
{ value: 1, left: {...}, right: {...} }
```

**数组存储（适合完全二叉树）：**

- 索引i的左子节点：2*i + 1
- 索引i的右子节点：2*i + 2
- 索引i的父节点：Math.floor((i-1)/2)

---

## 树的遍历

树的遍历方式可以分为**深度优先遍历（DFS）**和**广度优先遍历（BFS）**。

遍历的目的包括：

- 查找、搜索特定节点。
- 修改或删除某个节点。
- 计算树的高度、深度等属性。

### 深度优先遍历（DFS）

**深度优先遍历**（Depth-First Search，DFS）按照纵向方向深入到树的叶子节点，然后回溯。可以用递归或栈来实现。

#### 二叉树的 DFS

**前序遍历（Preorder）**：

```javascript
/**
 * 先访问根节点 → 左子树 → 右子树
 *      A
 *     / \
 *    B   C
 *    / \   \
 *   D   E   F
 *      / \
 *     G   H
 * 
 * 访问顺序：A → B → D → E → G → H → C → F
 */
function preorder(root) {
  if (!root) return;
  console.log(root.value); // 先访问根
  preorder(root.left);    // 再左子树
  preorder(root.right);   // 最后右子树
}
```

**中序遍历（Inorder）**：

```javascript
/**
 * 先访问左子树 → 根节点 → 右子树
 *      A
 *     / \
 *    B   C
 *    / \   \
 *   D   E   F
 *      / \
 *     G   H
 * 
 * 访问顺序：D → B → G → E → H → A → C → F
 */
function inorder(root) {
  if (!root) return;
  inorder(root.left);    // 先左子树
  console.log(root.value); // 再访问根
  inorder(root.right);   // 最后右子树  
}
```

**后序遍历（Postorder）**：

```javascript
/**
 * 先访问左子树 → 右子树 → 根节点
 *      A
 *     / \
 *    B   C
 *    / \   \
 *   D   E   F
 *      / \
 *     G   H
 * 
 * 访问顺序：D → G → H → E → B → F → C → A
 */
function postorder(root) {
  if (!root) return;
  postorder(root.left);    // 先左子树
  postorder(root.right);   // 再右子树
  console.log(root.value); // 最后访问根 
}
```

#### 二叉树的 DFS 非递归实现

**前序遍历迭代实现**：

```typescript
function preorderTraversalNonRecursive(root: TreeNode | null): number[] {
  if (!root) return [];
  const result: number[] = [];
  const stack: TreeNode[] = [root];
  while (stack.length) {
    const node = stack.pop();
    if (!node) continue;
    /** 前序遍历：根 -> 左 -> 右 */
    result.push(node.val);
    /** 注意：栈是先入后出的，所以右子树要先入栈，左子树后入栈 */
    if (node?.right) stack.push(node.right);
    if (node?.left) stack.push(node.left);
  }

  return result;
}
```

**中序遍历迭代实现**：

```typescript
export function inorderTraversalNonRecursive(root: TreeNode | null): number[] {
  if (!root) return [];
  const result: number[] = [];
  const stack: TreeNode[] = [];
  /** 中序遍历：左 -> 根 -> 右 */
  while (stack.length || root) {
    if (root) {
      stack.push(root);
      /** 一直找左子树直到左子树为空 */
      root = root.left;
    } else {
      /** 没有 root 了说明当前在最深最左边，弹出栈顶元素 */
      const node = stack.pop()!;
      result.push(node.val);
      /** 访问完左子树后，访问右子树 */
      root = node.right;
    }
  }
  return result;
}
```

**后序遍历迭代实现**：

```typescript
export function postorderTraversalNonRecursive(
  root: TreeNode | null
): number[] {
  const result: number[] = [];
  const stack: TreeNode[] = [];
  /** 
   * 为什么需要 prev ?
   * 我们不能单靠栈判断“右子树是否已访问”。所以需要额外变量 prev（上一个访问的节点）来判断：
   * 当前节点的右子树：
   * 如果为空，或者
   * 如果右子树已经访问过（node.right === prev）
   * → 才能访问当前节点（“根”）
   */
  let prev: TreeNode | null = null;
  let curr = root;

  while (curr || stack.length) {
    // 先一路向左，把所有左节点压栈
    while (curr) {
      stack.push(curr);
      curr = curr.left;
    }

    // 查看栈顶节点（但不弹出）
    curr = stack[stack.length - 1];

    /**
     * 判断当前节点是否可以访问：
     * - 没有右子树（说明左右都处理完了）
     * - 或右子树已经被访问过（通过 prev 记录）
     */
    if (!curr.right || curr.right === prev) {
      result.push(curr.val); // 访问当前节点
      stack.pop(); // 弹出节点
      prev = curr; // 标记为已访问
      curr = null; // 不再往右走
    } else {
      // 右子树还没访问过，转向右子树
      curr = curr.right;
    }
  }

  return result;
}
```

二叉树的后序遍历非递归实现相对来说要复杂一些，再来捋一捋。

与中序遍历不同的是我们需要使用一个指针 `prev` 记录“上一个访问的节点”，以此判断“右子树是否已经被访问”。

```javascript
先访问左子树 → 右子树 → 根节点
      A
     / \
    B   C
    / \   \
   D   E   F
      / \
     G   H
 
访问顺序：D → G → H → E → B → F → C → A
```

**关键点解释：**

- **后序遍历要等左右子树都处理完才能访问当前节点**，因此不能像前序/中序那样一边走一边访问。
- **判断当前节点是否可以访问的条件**：
  1. 当前节点没有右子树
  2. 或者右子树已经访问过（通过 `prev` 判断）
- `prev` 是一个非常关键的变量，用来追踪“上一次访问的节点”。

**步骤流程：**

1. 一直向左走，把左子树的所有节点压栈。
2. 查看栈顶节点（不弹出）：
   - 如果它没有右子树，或者右子树已访问过，则访问它。
   - 否则进入右子树。
3. 每次访问后，更新 `prev = 当前节点`，用于下次判断。

其实还可以使用前序反转法，就是前序遍历改一下，先访问右子树，再访问左子树，这样就是后序遍历的逆序。

```typescript
/**
 * Trick：变形前序遍历 + reverse
 * 遍历顺序：根 → 右 → 左，再反转成 左 → 右 → 根（即后序）
 */
export function postorderTraversalViaReverse(root: TreeNode | null): number[] {
  if (!root) return [];

  const result: number[] = [];
  const stack: TreeNode[] = [root];

  while (stack.length > 0) {
    const node = stack.pop()!;
    result.push(node.val);

    // 注意：先压左，再压右，这样出栈顺序是右 → 左
    if (node.left) stack.push(node.left);
    if (node.right) stack.push(node.right);
  }

  return result.reverse();
}

```

#### 普通树的深度优先遍历

对于普通的多叉树，每个节点可以有多个子节点：

**使用递归：**

```javascript
function dfs(root) {
  if (!root) return;
  console.log(root.value);
  for (const child of root.children) {
    dfs(child); // 递归每个子节点
  }
}
```

**使用栈迭代实现：**

```javascript
function dfsIterative(root) {
  if (!root) return;
  const stack = [root];
  while (stack.length) {
    const node = stack.pop();
    console.log(node.value);
    // 子节点反向入栈
    for (let i = node.children.length - 1; i >= 0; i--) {
      stack.push(node.children[i]);
    }
  }
}
```

### 广度优先遍历（BFS）

**广度优先遍历**（Breadth-First Search，BFS）按层级遍历树，从上到下访问每一层的所有节点。

使用**队列（Queue）**来实现：

```typescript
function breadthFirstSearch(root: Node | null): void {
  if (!root) return;
  const queue: Node[] = [root];
  while (queue.length > 0) {
    const node = queue.shift()!;
    console.log(node.value);
    for (const child of node.children) {
      queue.push(child);
    }
  }
}
```

---

### DFS 和 BFS 对比

| 特性                | DFS                      | BFS                   |
|---------------------|--------------------------|-----------------------|
| **访问顺序**         | 深度方向优先              | 层级顺序优先             |
| **数据结构**         | 栈（递归/显式栈）         | 队列                    |
| **空间复杂度**       | O(h)（h为树高度）         | O(w)（w为树最大宽度）     |
| **适用场景**         | 路径查找/拓扑排序         | 最短路径/层级遍历         |
| **实现复杂度**       | 递归实现简单              | 需显式维护队列            |

#### DFS遍历顺序（前序）

```mermaid
graph TD
    A --> B
    A --> C
    B --> D
    B --> E
    C --> F
    style A fill:#f9f
    style B fill:#f9f
    style D fill:#f9f
    style E fill:#f9f
    style C fill:#f9f
    style F fill:#f9f
```

访问顺序：A → B → D → E → C → F

#### BFS遍历顺序

```mermaid
graph TD
    A --> B
    A --> C
    B --> D
    B --> E
    C --> F
    style A fill:#9ff
    style B fill:#9ff
    style C fill:#9ff
    style D fill:#9ff
    style E fill:#9ff
    style F fill:#9ff
```

访问顺序：A → B → C → D → E → F

### 如何选择

#### 何时选择 DFS（深度优先遍历）？

**适合处理路径相关问题**  
例如：「是否存在一条路径，其路径和为目标值？」

```javascript
// 查找从根到叶子的路径和
function dfsPathSum(root, target) {
  if (!root) return false;
  target -= root.val;
  if (!root.left && !root.right) return target === 0;
  return dfsPathSum(root.left, target) || dfsPathSum(root.right, target);
}
```

**典型应用场景**  

- 文件系统的全路径扫描（如查找所有 `.md` 文件）  
- 算法题中的组合 / 排列（如 N 皇后、子集等）  
- 所有涉及**回溯**的场景（Backtracking）

**更优的空间效率**  
当树的**深度（h）远小于宽度（n）**时，DFS 的空间复杂度 O(h) 明显优于 BFS 的 O(n)

---

#### 何时选择 BFS（广度优先遍历）？

**适合处理层级结构问题**  
例如：「计算每一层的平均值？」

```javascript
// 层级平均值计算
function bfsLevelAverage(root) {
  const queue = [root];
  const result = [];
  while (queue.length) {
    let levelSum = 0;
    const levelSize = queue.length;
    for (let i = 0; i < levelSize; i++) {
      const node = queue.shift();
      levelSum += node.val;
      if (node.left) queue.push(node.left);
      if (node.right) queue.push(node.right);
    }
    result.push(levelSum / levelSize);
  }
  return result;
}
```

**最短路径相关场景**  

- 社交网络的好友推荐（第二、三度连接）  
- 网页爬虫的层级限制  
- 迷宫、地图中的最短路径搜索（如走迷宫）

**树结构平衡或宽度可控时**  
BFS 空间复杂度与当前层节点数量有关，适合宽度不大的树结构

---

#### 决策流程图

```mermaid
graph TD
    A[需要遍历树?] --> B{是否需要最短路径?}
    B -->|Yes| C[BFS 更合适]
    B -->|No| D{是否处理完整路径?}
    D -->|Yes| E[DFS 更合适]
    D -->|No| F{树深度 vs 宽度?}
    F -->|深且窄| E
    F -->|宽且浅| C
```

---

#### 常见场景对照表

| 场景特征                      | 推荐算法 | 原因说明                            |
|-----------------------------|----------|-------------------------------------|
| 是否存在一条路径满足条件？     | DFS      | 深度探索单一路径效率更高              |
| 离根节点最近的满足条件节点？   | BFS      | 层序遍历，先访问浅层节点，保证最近性    |
| 树的深度超过 1000 层？        | 迭代 DFS | 避免递归栈溢出，浏览器/JS栈有限         |
| 需要序列化整个树结构？         | BFS      | 层级序列化便于还原结构（如 LeetCode 输入）|
| 查找所有叶子节点？             | 均可      | DFS 更简洁，BFS 更直观                 |

## 总结

树是一种重要的数据结构，广泛应用于文件系统、数据库索引、前端虚拟 DOM、人工智能的决策树等场景。理解树的基本概念、二叉树的分类、以及深度优先和广度优先遍历方法，对于学习算法和数据结构至关重要。

在实际应用中，选择合适的存储方式（链表或数组）和遍历方式（DFS 或 BFS），可以提高算法的效率，使程序更加高效和易维护。
