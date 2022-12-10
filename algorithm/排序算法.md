# 排序算法

将分析直接写在代码的注释中了

```typescript
interface ICArray {
  dataStore: any[]; //
  pos: number; //
  numElements: number; //
  insert: (v: any) => void;
  toString: () => string;
  clear: () => void;
  setData: () => void;
  swap: (arr: any[], index1: number, index2: number) => void;
}

export class CArray implements ICArray {
  public dataStore: any[] = [];
  public pos: number = 0;
  public numElements: number = 0;

  constructor(numElements: number) {
    this.numElements = numElements;
    for (let i = 0; i < this.numElements; i++) {
      this.dataStore[i] = i;
    }
  }

  public setData() {
    for (let i = 0; i < this.numElements; ++i) {
      this.dataStore[i] = Math.floor(Math.random() * (this.numElements + 1));
    }
  }
  public clear() {
    for (let i = 0; i < this.dataStore.length; ++i) {
      this.dataStore[i] = 0;
    }
  }
  public insert(element: any) {
    this.dataStore[this.pos++] = element;
  }
  public toString() {
    let res = "";
    for (let i = 0; i < this.dataStore.length; ++i) {
      res += this.dataStore[i] + " ";
      if (i > 0 && i % 10 == 0) {
        res += "\n";
      }
    }
    return res;
  }

  public swap(arr: any[], index1: number, index2: number) {
    const temp = arr[index1];
    arr[index1] = arr[index2];
    arr[index2] = temp;
  }

  /**
   * 冒泡排序 O(n^2)
   * 1.比较相邻的元素。如果第一个比第二个大，就交换他们两个。
   * 2.对每一对相邻元素作同样的工作，从开始第一对到结尾的最后一对。这步做完后，最后的元素会是最大的数。
   * 3.针对所有的元素重复以上的步骤，除了最后一个。
   * 4.持续每次对越来越少的元素重复上面的步骤，直到没有任何一对数字需要比较。
   */
  public bubbleSort() {
    if (this.dataStore && this.dataStore.length > 0) {
      for (let i = 0; i < this.dataStore.length; i++) {
        for (let j = 0; j < this.dataStore.length - i; j++) {
          if (this.dataStore[j] > this.dataStore[j + 1]) {
            this.swap(this.dataStore, j, j + 1);
          }
        }
      }
    }
  }

  /**
   * 快速排序 O(n*logn)
   * 1.给数组找一个标志位 flag
   * 2.遍历所有剩余的元素都跟标志位元素比大小
   * 3.定义一个 left 和 一个 right，left 保存比标志位小的，right 保存比标志位大的
   * 4.然后合并 left、flag、right
   * 5.然后递归把 left 和 right 数组做相同的操作
   */
  function quickSort(arr) {
    // 如果数组的长度小于等于 1，则不需要排序
    if (arr.length <= 1) {
      return arr;
    }

    // 选取数组的第一个元素作为基准元素
    const pivot = arr[0];
    // 初始化小于基准元素的数组
    const left = [];
    // 初始化大于基准元素的数组
    const right = [];

    // 遍历数组，并根据元素的值与基准元素的值的大小关系，将元素分组
    for (let i = 1; i < arr.length; i++) {
      if (arr[i] < pivot) {
        left.push(arr[i]);
      } else {
        right.push(arr[i]);
      }
    }

    // 递归地对小于基准元素的数组和大于基准元素的数组进行快速排序
    return quickSort(left).concat([pivot]).concat(quickSort(right));
  }

  /**
   * 原地快速排序 O(n*logn)
   * 1.给数组找一个标志位 flag
   * 2.遍历所有剩余的元素都跟标志位元素比大小
   * 3.定义一个 left 和 一个 right，left 保存比标志位小的，right 保存比标志位大的
   * 4.然后合并 left、flag、right
   * 5.然后递归把 left 和 right 数组做相同的操作
   */
  public quickLocalSort(array: number[]): number[] {
    // if (array.length < 2) {
    //   return array;
    // }
  }

  /**
   * 选择排序
   * 1.首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置
   * 2.再从剩余未排序元素中继续寻找最小（大）元素，然后放到已排序序列的末尾。
   * 3.重复第二步，直到所有元素均排序完毕。
   */
  public selectionSort() {
    if (this.dataStore && this.dataStore.length > 0) {
      // 遍历到倒数第二个值就可以了，最后一个肯定是最大（小）的
      for (let i = 0; i < this.dataStore.length - 1; i++) {
        let min = i; // 每次都假设当前遍历的是最小值
        // 从未排序中找到最小值的下标
        for (let j = i + 1; j < this.dataStore.length; j++) {
          if (this.dataStore[j] < this.dataStore[min]) {
            min = j; // 这就是本轮最小值的下标
          }

          this.swap(this.dataStore, i, min); // 找到本轮最小的下标后将找到的值与当前值交换位置
        }
      }
    }
  }

  /**
   * 插入排序
   * 1.将第一待排序序列第一个元素看做一个有序序列，把第二个元素到最后一个元素当成是未排序序列。
   * 2.从头到尾依次扫描未排序序列，将扫描到的每个元素插入有序序列的适当位置。
   *   如果待插入的元素与有序序列中的某个元素相等，则将待插入元素插入到相等元素的后面。
   */
  public insertionSort() {
    const len = this.dataStore.length;
    let preIndex, current;
    for (let i = 1; i < len; i++) {
      preIndex = i - 1;
      current = this.dataStore[i];

      // 每进入一次循环，已经排好的数组长度+i了，如果当前值小于他的前一个值，那么前一个值应该
      // 放到当前值的这个位置来，然后一直循环做这个判断直到前一个值比当前值小，那当前这个位置
      // 就是当前值应该放入的位置。而当前值位置用 preIndex + 1表示
      while (preIndex >= 0 && this.dataStore[preIndex] > current) {
        // 将前一个值放入到当前位置，然后继续用当前值去比较前面的值
        this.dataStore[preIndex + 1] = this.dataStore[preIndex];
        preIndex--; // 前一个值的位置向前推移，继续判断
      }

      // 此时前一个值已经比当前值小了，所以当前位置就是当前值要插入的位置
      this.dataStore[preIndex + 1] = current;
    }
  }

  /**
   * 希尔排序，也称递减增量排序算法，是插入排序的一种更高效的改进版本。
   * 希尔排序的基本思想是：先将整个待排序的记录序列分割成为若干子序列分别进行直接插入排序，
   * 待整个序列中的记录"基本有序"时，再对全体记录进行依次直接插入排序。
   * 希尔排序详解：https://zhuanlan.zhihu.com/p/87781731
   *
   */
  public shellSort() {
    const len = this.dataStore.length;
    let gap = 1;
    while (gap < len / 3) {
      gap = gap * 3 + 1;
    }

    // 每个增量值执行一遍插入排序
    while (gap >= 1) {
      // 从增量值开始，从后往前遍历
      for (let i = gap; i < len; i++) {
        // 从后往前遍历，如果后面的比前面的小交换位置，然后减掉增量值继续下一次遍历
        for (
          let j = i;
          j >= gap && this.dataStore[j] < this.dataStore[j - gap];
          j -= gap
        ) {
          this.swap(this.dataStore, j, j - gap);
        }
      }
      gap = (gap - 1) / 3; // 一轮后更新增量值
    }
  }

  /**
   * 归并排序。自顶向下递归的实现方式
   * 递归的三个条件：
   * 1. 一个问题的解可以分解为几个子问题的解
   * 2. 这个问题与分解之后的子问题，除了数据规模不同，求解思路完全一样
   * 3. 存在递归终止条件
   */
  public mergeSort(arr: any[]): any[] {
    // mergeSort(p…r) = merge(mergeSort(p…q), mergeSort(q+1…r))
    // 终止条件：p >= r 不用再继续分解
    if (arr.length < 2) {
      return arr;
    }

    const middle = Math.floor(arr.length / 2);
    const left = arr.slice(0, middle);
    const right = arr.slice(middle);
    return this.merge(this.mergeSort(left), this.mergeSort(right));
  }

  /**
   * 按照大小合并两个数组
   */
  public merge(left: any[], right: any[]): any[] {
    const res: any[] = [];

    while (left.length > 0 && right.length > 0) {
      if (left[0] <= right[0]) {
        res.push(left.shift());
      } else {
        res.push(right.shift());
      }
    }

    while (left.length > 0) {
      res.push(left.shift());
    }

    while (right.length > 0) {
      res.push(right.shift());
    }

    return res;
  }

  /**
   * 归并排序。自底向上的实现方式
   */
  public mergeSort2() {
    if (this.dataStore.length < 2) {
      return;
    }

    let step = 1;
    let left, right;
    while (step < this.dataStore.length) {
      left = 0;
      right = step;
      while (right + step < this.dataStore.length) {
        this.merge2(this.dataStore, left, left + step, right, right + step);
        left = right + step;
        right = left + step;
      }

      if (right < this.dataStore.length) {
        this.merge2(
          this.dataStore,
          left,
          left + step,
          right,
          this.dataStore.length
        );
      }

      step *= 2;
    }
  }

  public merge2(
    arr: number[],
    startLeft: number,
    stopLeft: number,
    startRight: number,
    stopRight: number
  ) {
    let leftArr = new Array(stopLeft - stopLeft + 1);
    let rightArr = new Array(stopRight - stopRight + 1);
    let k = startRight;
    for (let i = 0; i < rightArr.length - 1; i++) {
      rightArr[i] = arr[k];
      k++;
    }

    k = startLeft;
    for (let i = 0; i < leftArr.length - 1; i++) {
      leftArr[i] = arr[k];
      k++;
    }

    leftArr[leftArr.length - 1] = Infinity; // 哨兵值
    rightArr[rightArr.length - 1] = Infinity; // 哨兵值
    let m = 0;
    let n = 0;
    for (let k = startLeft; k < stopRight; k++) {
      if (leftArr[m] <= rightArr[n]) {
        arr[k] = leftArr[m];
        m++;
      } else {
        arr[k] = rightArr[n];
        n++;
      }
    }

    console.log("leftArr", leftArr);
    console.log("rightArr", rightArr);
  }
}
```

## 参考

1. [十大排序算法](https://juejin.cn/post/6844903444365443080#heading-14)
