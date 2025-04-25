# 理解 diff 算法

在上一篇文章中，我们已经梳理了 Vue3 渲染机制的整体流程，包括 VNode 的创建、渲染函数执行、以及 `patch` 的基本结构。在这个过程中，有一个非常核心但又复杂的环节 —— diff 算法，也就是 Vue 在更新阶段对比新旧虚拟节点、生成最小 DOM 操作集的关键逻辑。

由于 diff 本身的复杂度较高，为了让大家更清晰地理解它的设计与实现，我们将其从渲染机制中拆解出来，单独讲一篇。这篇文章就是上一篇的姊妹篇。

## 1. 为什么需要 Diff 算法？

设想一个场景：我们有一个通过 `v-for` 渲染的列表，数据变了，Vue 怎么知道哪些节点是可以复用的？哪些需要移动、删除或者新增？

Vue3 的 diff 算法正是为了解决这个问题而设计的：

- 避免每次都重新生成整个 DOM 树
- 通过对比新旧 vnode 列表，实现最小化更新

Vue 采用了“**同层对比 + 双端扫描 + key 优化**”的方式，高效完成节点更新。

为了更好地理解 diff 过程，我们来看一个具体的组件树示例。假设我们有如下组件树结构：

```mermaid
graph TD
  A --> B
  A --> C
  B --> D
  B --> E
```

现在我们要执行一个更新操作：

删除 B 节点下的 D 节点

在 B 节点下添加 F 节点（位于 E 节点之后）

更新后的组件树应该是：

```mermaid
graph TD
  A --> B
  A --> C
  B --> E
  B --> F
```

接下来，我们将基于这个具体的更新场景，一步步分析 Vue3 的 diff 算法如何处理这种变化。

## 2. patch 流程中 diff 的位置

在我们之前介绍的 `patch` 流程中，如果新旧节点类型一致，Vue 会调用 `patchElement`，而这其中最关键的一步就是 `patchChildren` —— 它会进入 diff 逻辑。

```ts
patch(n1, n2, container);
```

当旧节点 `n1` 和新节点 `n2` 都是元素类型，并且类型相同：

```ts
patchElement(n1, n2, container);
```

进入之后会调用：

```ts
patchChildren(n1, n2, container);
```

这时 Vue 会判断子节点类型，若新旧节点都是数组，就会走入最典型的 diff 算法处理路径 —— `patchKeyedChildren`。

下图是 patch 过程中 diff 所处的位置：

```mermaid
flowchart TD
  patch --> patchElement
  patchElement --> patchChildren
  patchChildren --> patchKeyedChildren
```

---

## 3. Vue3 的 Diff 核心逻辑

### 3.1 前后端双端对比（头尾指针法）

Vue3 使用了经典的“双端对比”技巧，即从头部和尾部同时进行比较，快速跳过两端一致的部分，缩小对比范围。
在我们的示例中，B 节点的子节点变化如下：

旧子节点: [D, E]

新子节点: [E, F]

```typescript
let i = 0;
let e1 = oldChildren.length - 1; // e1 = 1
let e2 = newChildren.length - 1; // e2 = 1

// 从头开始比较
while (i <= e1 && i <= e2) {
  if (isSameVNode(oldChildren[i], newChildren[i])) {
    patch(oldChildren[i], newChildren[i], container);
    i++;
  } else {
    break;
  }
}
// i = 0 (D 和 E 不同)
```

```mermaid
graph TD
  B --> D(旧D)
  B --> E(旧E)
  B --> E(新E)
  B --> F(新F)
```

由于第一个节点就不同，头部比较立即结束。接着进行尾部比较：

```typescript
// 从尾开始比较
// 从尾开始比较
while (i <= e1 && i <= e2) {
  if (isSameVNode(oldChildren[e1], newChildren[e2])) {
    patch(oldChildren[e1], newChildren[e2], container);
    e1--;
    e2--;
  } else {
    break;
  }
}
// e1 = 0, e2 = 0 (E 和 F 不同)
```

尾部比较也立即结束。此时 i=0, e1=0, e2=0，说明我们需要处理中间区域。

### 3.2 新增与删除判断

在我们的例子中，i > e1 和 i > e2 都不成立，所以跳过新增和删除的判断，直接进入乱序处理阶段。

但是如果存在新增和删除的情况，则接下来处理的是两端指针已经对比完成后的“剩余区域”：

- 若新节点多于旧节点，说明是新增
- 若旧节点多于新节点，说明是删除

```ts
if (i > e1) {
  // 挂载新节点
  while (i <= e2) {
    mountElement(newChildren[i++], container);
  }
} else if (i > e2) {
  // 卸载旧节点
  while (i <= e1) {
    unmount(oldChildren[i++]);
  }
}
```

这个阶段非常直观，属于 diff 中最简单的部分。

### 3.3 中间乱序区域处理

现在处理中间区域，建立 key 到 oldIndex 的映射：

```typescript
// 旧子节点: [D, E]
const keyToOldIndexMap = new Map();
keyToOldIndexMap.set("D", 0); // 假设 key 为 'D'
keyToOldIndexMap.set("E", 1); // 假设 key 为 'E'
```

然后遍历新子节点 [E, F]：

```typescript
const newIndexToOldIndexMap = [0, 0]; // 初始化为全0

// 处理 E (i=0)
const oldIndex = keyToOldIndexMap.get("E"); // oldIndex = 1
newIndexToOldIndexMap[0] = 1 + 1; // [2, 0]

// 处理 F (i=1)
const oldIndex = keyToOldIndexMap.get("F"); // undefined
// F 是新节点，直接挂载
mountElement(F, container);
```

此时 newIndexToOldIndexMap = [2, 0]，表示：

- 新位置 0 的节点来自旧位置 1
- 新位置 1 的节点是新增的

### 3.4 节点移动处理

最后，我们需要处理节点的移动。计算最长递增子序列：

```typescript
const increasingNewIndexSequence = getSequence([2, 0]); // 返回 [0]
```

这个结果告诉我们，新列表中的第 0 个节点（E）应该保持不动，其他节点需要移动。

由于 F 是新节点已经挂载，我们只需要移动 E 节点到正确位置：

```marmaid
graph TD
  B --> E
  B --> F
```

整个 diff 过程完成后，组件树就更新为了我们期望的结构。

## 4. 节点移动：LIS 最长递增子序列

当我们完成 patch 之后，最后还要判断哪些复用节点需要“移动”。这时候就要用到著名的**最长递增子序列**算法（LIS）。

它的作用是找出一组“顺序正确”的节点，这部分我们可以保留原位置，其他节点则需要移动。

Vue 内部实现了一个 `getSequence` 函数来获取这个序列：

```ts
const increasingNewIndexSequence = getSequence(newIndexToOldIndexMap);
```

下图展示了这个过程的逻辑关系：

```mermaid
flowchart TD
  A[对比 newIndexToOldIndexMap] --> B[求最长递增子序列 LIS]
  B --> C[保留顺序节点不动]
  B --> D[其余节点插入或移动]
```

通过 LIS，Vue 能够极大地优化 DOM 操作次数。

---

## 5. 总结与回顾

我们可以用一张总流程图来回顾 Vue3 中 diff 的整体逻辑：

```mermaid
flowchart TD
  A[patchKeyedChildren] --> B[双端对比跳过相同部分]
  B --> C[处理新增或删除节点]
  C --> D[建立 key 索引映射]
  D --> E[复用旧节点 + 挂载新节点]
  E --> F[生成 newIndexToOldIndexMap]
  F --> G[使用 LIS 判断是否移动节点]
  G --> H[最小化移动操作完成更新]
```

通过这些流程和策略，Vue3 的 diff 算法在性能和复杂度之间找到了很好的平衡点。

在下一篇文章中，我们将深入分析这个 diff 流程中使用到的 `getSequence` 实现，并进一步对比 Vue2 的 diff 策略，看看 Vue3 到底优化了哪些关键点。
