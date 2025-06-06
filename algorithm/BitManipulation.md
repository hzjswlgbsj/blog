# 位运算

## 基本概念

程序中的数在计算机内存中都是以二进制的形式存在的，位运算就是直接对整数在内存中对应的二进制位进行操作。

位运算就是和加减乘除等算术运算一样基础的逻辑运算，包括与或非，异或和移位。虽然非常基础，不过在日常的变成中使用的场景却不多，主要是技巧性太强，而且可读性差。即使在汇编编程中，位运算使用场景比较广的也是接口编程等比较专业的地方。但是，在底层编程，标准库等场景中，位运算还是有着不小的应用场景。另外在状态压缩，大数处理，甚至面试中，也少不了它的声音。

### 左移 <<

左移就是在二进制位上向高位移动 << 运算符右侧数字个位置，相当于在低位补 0，将位置像高位推移，比如

`1 << 2` 表示 1 左移 2 位，他的结果是 `4`

1 的二进制可以表示为 `0001` (高位补了 3 位 0)，那么左移 2 位结果就是 `0100`，高位多出的一位 0 是未使用的，所以结果也可表示为`100` 他的十进制数字为 `4`

## 右移 >>

右移就是在二进制位上向右移动 >> 运算符右侧数字个位置，相当于在低位舍弃位置，在高位补 0，将位置像低位推移，比如

`8 >> 1` 表示 8 右移 2 位，他的结果是 `4`

8 的二进制可以表示为 `1000` ，那么右移 1 位结果就是 `0100`，也就是 `100`(低位去掉了一位 0，高位补了一个 0), `0100` 的十进制数字为 `4`

因为右移是直接舍弃低位在高位补 0，所以右移会丢失精度，比如

`9 >> 1` 表示 9 右移 2 位，他的结果也是 `4`

9 的二进制可以表示为 `1001` ，那么右移 1 位结果也是 `0100`，也就是 `100`(低位被去掉了一位，该位置值为 1，导致丢失精度),`0100` 的十进制数字为 `4`

### 按位与 &

按位与运算将参与运算的两数对应的二进制位相与，当对应的二进制位均为 `1` 时，结果位为 `1`，否则结果位为 `0`。按位与运算的运算符为 `&`，参与运算的数以补码方式出现。

举个例子，将数字 `5` 和数字 `8` 进行按位与运算，其实是将数字 `5` 对应的二进制 `0000 0101` 和数字 `8` 对应的二进制 `0000 1000` 进行按位与运算，即：

```
0000 0101
&
0000 1000
```

按照按位与的运算规则，得到如下结果

```
0000 0101
&
0000 1000
---------
0000 0000
```

将结果 `00000000` 换算成十进制，得到 `0`，即 `5&8 = 0`。

### 按位或 |

按位或运算将参与运算的两数对应的二进制位相或，只要对应的二进制位中有 `1`，结果位为 `1`，否则结果位为 `0`。按位或运算的运算符为 `|`，参与运算的数以补码方式出现。

还是上面的例子，将数字 `5` 和数字 `8` 进行按位或运算，其实是将数字 `5` 对应的二进制 `0000 0101` 和数字 8 对应的二进制 `0000 1000` 进行按位或运算，即：

```
0000 0101
|
0000 1000
```

按照按位或的运算规则，得到如下结果

```
0000 0101
&
0000 1000
---------
0000 1101
```

将结果 `00001101` 换算成十进制，得到 `13`，即 `5|8 = 13`。

### 按位异或

按位异或运算将参与运算的两数对应的二进制位相异或，当对应的二进制位值不同时，结果位为 `1`，否则结果位为 `0`。按位异或的运算符为 `^`，参与运算的数以补码方式出现。

举个例子，将数字 `12` 和数字 `7` 进行按位异或运算，其实是将数字 `1`2 对应的二进制 `0000 1100` 和数字 `7` 对应的二进制 `0000 0111` 进行按位异或运算，即：

```
0000 1100
^
0000 0111
```

按照按位异或的运算规则，得到如下结果

```
0000 1100
^
0000 0111
---------
0000 1011
```

将结果 `00001101` 换算成十进制，得到 `11`，即 `12^7 = 11`。

## 位运算的用法

在 Vue 源码中 `ShapeFlags.ts` 是虚拟 DOM 的类型二进制表示，用于在程序中方便的判断虚拟节点的类型。

```typescript
// ShapeFlags.ts 部分flag
export const enum ShapeFlags {
  ELEMENT = 1, // 00001
  STATEFUL_COMPONENT = 1 << 1, // 00010，由0001的值1左移1位得到
  TEXT_CHILDREN = 1 << 2, // 00100，由0001的值1左移2位得到
  ARRAY_CHILDREN = 1 << 3, // 01000，由0001的值1左移3位得到
  SLOT_CHILDREN = 1 << 4, // 10000，由0001的值1左移4位得到
}
```

下面我们简化一下，假设有 3 种类型

```typescript
const text = 001; // 文本
const element = 010; // HTML 标签，（相当于 001 << 1）
const component = 100; // 组件，（相当于 001 << 2）
```

注意观察，在不同的二进制位置上用 1 来标记不同的属性，这样的话他们不同位 取与（&）或者 取或（|）就能改变对应位置的值，按照位置的值我们就能够做判断操作。

我们可以用 `|` 来做授权，用 `&` 来进行校验。

**授权**

比如同时拥有 `文本` 和 `HTML` 标签权限的话，我们可以这样授权：

```typescript
const text = 001; // 文本
const element = 010; // HTML 标签，（相当于 001 << 1）
const component = 100; // 组件，（相当于 001 << 2）
const isTextOrHtml = text | element; // 结果是 011
```

`001 | 010 = 011`，这可以理解为，通过 `|` 操作我们在两个低位都获得了 `1`，代表在这两个位置上是有权限的。

**鉴权**

```typescript
const text = 001; // 文本
const element = 010; // HTML 标签，（相当于 001 << 1）
const component = 100; // 组件，（相当于 001 << 2）
const res = text | element; // 结果是 011

// 鉴权
const res1 = res & text; // 鉴定是否有 text 的权限
const res2 = res & element; // 鉴定是否有 element 的权限
const res3 = res & component; // 鉴定是否有 component 的权限
```

`res1` 的操作为 `011 & 001 = 001` 结果第一位是 `1`，表示在第一位有权限，验证通过。
`res2` 的操作为 `011 & 010 = 010` 结果第二位是 `1`，表示在第二位有权限，验证通过。
`res3` 的操作为 `011 & 100 = 000` 结果所有位都是 `0`，表示没有任何权限，验证不通过。

上面的结果 `001` 、 `010` 和 `000` 十进制分别是 `1`、`2` 和 `0`，正整数的数字转化为布尔值为 `true`，0 转化为布尔值为 `false`，所以我们可以写出如 vue 源码中的代码

```typescript
export const enum ShapeFlags {
  ELEMENT = 1, // HTML 元素
  STATEFUL_COMPONENT = 1 << 1, // 组件
  SLOT_CHILDREN = 1 << 4, // slot
  // ...
}

const vnode = {
  shapeFlag: ShapeFlags.SLOT_CHILDREN,
  // ...
};

if (vnode.shapeFlag & ShapeFlags.SLOT_CHILDREN) {
  // normalizeObjectSlots(children, instance.slots)
}
```

## 参考

1. [基础知识参考](https://juejin.cn/post/6844903888072474632)
