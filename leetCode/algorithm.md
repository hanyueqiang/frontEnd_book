## 前端数据结构与算法

> 目的在有限的时间内，做对关键的已知题目；通过对关键的已知题目进行解构和反思，消化掉其中核心的算法思想和解题套路，从而最大化各位在真正面试时做对任意未知题目的可能性。[—修言]

## 栈（Stack）

栈是一种后进先出`(LIFO，Last In First Out)`的数据结构，
使用数组的`pop` 和 `push` 模拟栈后进先出
这个过程有两个特征：

- 只允许从尾部添加元素
- 只允许从尾部取出元素

举例搬砖，a 任务用砖砌一道墙，从底部开始往上码，b 任务把码好的砖转移到另一个地方，需要从上往下搬，即 a 最后摆上的被 b 最先取出。

举例模拟栈（后进先出）工作状态

```js
// 初始状态，栈空
const stack = [];
// 入栈过程
stack.push("第1层砖");
stack.push("第2层砖");
stack.push("第3层砖");

// 出栈过程，栈不为空时才执行
while (stack.length) {
  // 单纯访问栈顶元素（不出栈）
  const top = stack[stack.length - 1];
  console.log("现在取出砖", top);
  // 将栈顶元素出栈
  stack.pop();
}

// 栈空
console.log(stack); // []
```

## 队列（Queue）

队列是一种先进先出（FIFO，First In First Out）的数据结构。
使用数组的`push` 和 `shift`模拟队列的先进先出
它比较像排队点餐。先点餐的人先出餐，后点餐的人后出餐：
这个过程有两个特征：

- 只允许从尾部添加元素
- 只允许从头部移除元素

使用场景：
js 异步中任务队列（由于 js 单线程，无法处理异步中的并发任务，利用队列处理）

```js
const queue = [];
queue.push("1号");
queue.push("2号");
queue.push("3号");

while (queue.length) {
  // 单纯访问队头元素（不出队）
  const top = queue[0];
  console.log(top, "取餐");
  // 将队头元素出队
  queue.shift();
}

// 队空
console.log(queue); // []
```

## 数组

### 两数求和问题

采用空间换时间，使用 Map

```js
// 示例: 给定 nums = [2, 7, 11, 15], target = 9
// 因为 nums[0] + nums[1] = 2 + 7 = 9 所以返回 [0, 1]

/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number[]}
 */
const twoSum = function(nums, target) {
  // 这里我用对象来模拟 map 的能力
  const diffs = {};
  // 缓存数组长度
  const len = nums.length;
  // 遍历数组
  for (let i = 0; i < len; i++) {
    // 判断当前值对应的 target 差值是否存在（是否已遍历过）
    if (diffs[target - nums[i]] !== undefined) {
      // 若有对应差值，那么答案get！
      return [diffs[target - nums[i]], i];
    }
    // 若没有对应差值，则记录当前值
    diffs[nums[i]] = i;
  }
};
```

### 双指针法

```js
// 真题描述：给你两个有序整数数组 nums1 和 nums2，请你将 nums2 合并到 nums1 中，使 nums1 成为一个有序数组。
// 说明: 初始化 nums1 和 nums2 的元素数量分别为 m 和 n 。 你可以假设 nums1 有足够的空间（空间大小大于或等于 m + n）来保存 nums2 中的元素。
// 示例: 输入:
// nums1 = [1,2,3,0,0,0], m = 3
// nums2 = [2,5,6], n = 3
// 输出: [1,2,2,3,5,6]
const merge = function(nums1, m, nums2, n) {
  // 初始化两个指针的指向，初始化 nums1 尾部索引k
  let i = m - 1,
    j = n - 1,
    k = m + n - 1;
  // 当两个数组都没遍历完时，指针同步移动
  while (i >= 0 && j >= 0) {
    // 取较大的值，从末尾往前填补
    if (nums1[i] >= nums2[j]) {
      nums1[k] = nums1[i];
      i--;
      k--;
    } else {
      nums1[k] = nums2[j];
      j--;
      k--;
    }
  }

  // nums2 留下的情况，特殊处理一下
  while (j >= 0) {
    nums1[k] = nums2[j];
    k--;
    j--;
  }
};
```

### 三数求和问题

三数之和延续两数之和的思路，我们可以把求和问题变成求差问题——固定其中一个数，在剩下的数中寻找是否有两个数和这个固定数相加是等于 0 的。
双指针法用在涉及求和、比大小类的数组题目里时，大前提往往是：该数组必须有序。否则双指针根本无法帮助我们缩小定位的范围，压根没有意义。因此这道题的第一步是将数组排序

```js
nums = nums.sort((a, b) => {
  return a - b;
});
```

```js
// 真题描述：给你一个包含 n 个整数的数组 nums，判断 nums 中是否存在三个元素 a，b，c ，使得 a + b + c = 0 ？请你找出所有满足条件且不重复的三元组。
// 注意：答案中不可以包含重复的三元组。
// 示例： 给定数组 nums = [-1, 0, 1, 2, -1, -4]， 满足要求的三元组集合为： [ [-1, 0, 1], [-1, -1, 2] ]

function threeSum(nums) {
  const arr = nums.sort((a, b) => a - b);
  console.log(arr);
  let results = [];
  const len = arr.length;
  // 尾指针 i移动指针 j头指针
  for (let i = 0; i < len; i++) {
    let current = arr[i];
    let j = i + 1;
    let k = len - 1;
    // 如果遇到重复的数字，则跳过
    if (i > 0 && arr[i] === arr[i - 1]) {
      continue;
    }
    while (j < k) {
      if (current + arr[j] + arr[k] > 0) {
        k--;
      } else if (current + arr[j] + arr[k] < 0) {
        j++;
      } else {
        results.push([current, arr[j], arr[k]]);
        j++;
        k--;
      }
    }
  }
  return results;
}
```

### 数组排序

```js
arr.sort((a, b) => {
  return a - b;
});
```

#### 冒泡排序

时间复杂度`O(n*2)`

```js
function sort1(arr) {
  const length = arr.length;
  for (let i = 0; i < length; i++) {
    for (let j = 0; j < length - 1 - i; j++) {
      let temp;
      if (arr[j] > arr[j + 1]) {
        temp = arr[j + 1];
        arr[j + 1] = arr[j];
        arr[j] = temp;
        // 不引入地三个变量可使用
        // [arr[j + 1], arr[j]] = [arr[j], arr[j + 1]];
      }
    }
  }
  return arr;
}
```

#### 选择排序

时间复杂度 `O(n*2)`

选择排序关键是最小值，循环遍历数组，每次找到范围内最小值，放在当前范围头部，然后缩小排序范围
也要走两层循环，时间复杂度`（O(n*2)）`

```js
function selectSort(arr) {
  const len = arr.length;
  for (i = 0; i < len - 1; i++) {
    let minIndex = i;
    for (let j = i; j < len; j++) {
      if (arr[j] < arr[minIndex]) {
        minIndex = j;
      }
    }
    if (i !== minIndex) {
      let temp;
      temp = arr[i];
      arr[i] = arr[minIndex];
      arr[minIndex] = temp;
    }
  }
  return arr;
}
```

#### 插入排序

插入排序的核心思想是“找到元素在它前面那个序列中的正确位置”。
具体来说，插入排序所有的操作都基于一个这样的前提：当前元素前面的序列是有序的。基于这个前提，从后往前去寻找当前元素在前面那个序列里的正确位置。

```js
function insertSort(arr) {
  const len = arr.length;
  for (let i = 1; i < len; i++) {
    let temp = arr[i];
    let j = i;
    while (j > 0 && arr[j - 1] > temp) {
      arr[j] = arr[j - 1];
      j--;
    }
    arr[j] = temp;
  }
  return arr;
}
```

### 归并排序

归并排序是对分治思想的典型应用，它按照如下的思路对分治思想“三步走”的框架进行了填充

- 分解子问题：将需要被排序的数组从中间分割为两半，然后再将分割出来的每个子数组各分割为两半，重复以上操作，直到单个子数组只有一个元素为止。
- 求解每个子问题：从粒度最小的子数组开始，两两合并、确保每次合并出来的数组都是有序的。（这里的“子问题”指的就是对每个子数组进行排序）。
- 合并子问题的解，得出大问题的解：当数组被合并至原有的规模时，就得到了一个完全排序的数组

合并是将原子项反复地组装回原有的大数组。整个过程符合两个特征： 1.重复（令人想到递归或迭代） 2.有去有回（令人想到回溯，进而明确递归这条路）

因此，归并排序在实现上依托的就是递归思想。
除此之外，这里还涉及到另一个小小的知识点——两个有序数组的合并(双指针)
归并排序的时间复杂度是 `O(nlog(n))`

```js
function merge1Sort(arr) {
  const len = arr.length;
  if (len <= 1) {
    return arr;
  }

  let mid = Math.floor(len / 2);
  let leftArr = merge1Sort(arr.slice(0, mid));
  let rightArr = merge1Sort(arr.slice(mid, len));

  arr = mergeArr(leftArr, rightArr);
  return arr;
}
function mergeArr(left, right) {
  let i = 0;
  let j = 0;
  let len1 = left.length;
  let len2 = right.length;
  let arr = [];
  while (i < len1 && j < len2) {
    if (left[i] < right[j]) {
      arr.push(left[i]);
      i++;
    } else if (left[i] > right[j]) {
      arr.push(right[j]);
      j++;
    } else {
      arr.push(left[i]);
      arr.push(right[j]);
      i++;
      j++;
    }
  }

  if (i < len1) {
    return arr.concat(left.slice(i));
  }
  if (j < len2) {
    return arr.concat(right.slice(j));
  }
}
```

## 字符串

反转字符串

```js
const str = "absdef";

const result = str
  .split("")
  .reverse()
  .join();
```

判断一个字符串是否为回文串

```js
const str = "sfefsfs";

function isRepeat(str) {
  const reverseStr = str
    .split("")
    .reverse()
    .join("");
  return str === reverseStr;
}
isRepeat(str);

// 解法2 利用对称特性
function isPalindrome(str) {
  // 缓存字符串的长度
  const len = str.length;
  // 遍历前半部分，判断和后半部分是否对称
  for (let i = 0; i < len / 2; i++) {
    if (str[i] !== str[len - i - 1]) {
      return false;
    }
  }
  return true;
}
```

真题描述：给定一个非空字符串 s，最多删除一个字符。判断是否能成为回文字符串。

```js
示例 1: 输入: "aba"
输出: True
示例 2:
输入: "abca"
输出: True
解释: 你可以删除c字符。
注意: 字符串只包含从 a-z 的小写字母。字符串的最大长度是50000。

const validPalindrome = function(s) {
    // 缓存字符串的长度
    const len = s.length

    // i、j分别为左右指针
    let i=0, j=len-1

    // 当左右指针均满足对称时，一起向中间前进
    while(i<j&&s[i]===s[j]) {
        i++
        j--
    }

    // 尝试判断跳过左指针元素后字符串是否回文
    if(isPalindrome(i+1,j)) {
      return true
    }
    // 尝试判断跳过右指针元素后字符串是否回文
    if(isPalindrome(i,j-1)) {
        return true
    }

    // 工具方法，用于判断字符串是否回文
    function isPalindrome(st, ed) {
        while(st<ed) {
            if(s[st] !== s[ed]) {
                return false
            }
            st++
            ed--
        }
        return true
    }

    // 默认返回 false
    return false
};
```

## 链表

链表和数组相似，它们都是有序的列表、都是线性结构（有且仅有一个前驱、有且仅有一个后继）。不同点在于，链表中，数据单位的名称叫做“结点”，而结点和结点的分布，在内存中可以是离散的。
在链表中，每一个结点的结构都包括了两部分的内容：数据域和指针域。`JS` 中的链表，是以嵌套的对象的形式来实现的，`js` 原型链也是一个链表 ，沿着`__proto__`
特点：

- 多个元素组成列表
- 元素存储不连续，用 next 指针连在一起

```js
{
  // 数据域
  val: 1,
  // 指针域，指向下一个结点
  next: {
    val:2,
    next: ...
  }
}
```

创建链表结点:

```js
// 需要一个构造函数
function ListNode(val) {
  this.val = val;
  this.next = null;
}

// 在使用构造函数创建结点时，传入 val （数据域对应的值内容）、指定 next （下一个链表结点）即可
const node = new ListNode(1);
node.next = new ListNode(2);
```

### 链表和数组的辨析

相对于数组来说，链表有一个明显的优点，就是添加和删除元素都不需要挪动多余的元素。

```js
// 遍历链表
const a = { val: "a" };
const b = { val: "b" };
a.next = b;
let p = a;
while (p) {
  console.log(p.val);
  p = p.next;
}

// 它是一个纯数字数组，那么对应的确实是连续内存
const arr = [1, 2, 3, 4];

// 它对应的就是一段非连续的内存。
// 此时，JS 数组不再具有数组的特征，其底层使用哈希映射分配内存空间，是由对象链表来实现的
const arr = ["haha", 1, { a: 1 }];
```

总结:
结合上述分析，我们不难得出这样的结论：链表的插入/删除效率较高 O(1)，而访问效率较低 O(n)；数组的访问效率较高 O(1)，而插入效率较低 O(n)。这个特性需要大家牢记，可能会作为数据结构选型的依据来单独考察

## 原型链简介

obj -> Object.prototype -> null
func -> Function.prototype -> Object.prototype -> null
arr -> Array.prototype -> Object.ptototype -> null

```js
const obj = {};
const func = () => {};
const arr = [];
```

简述 instanceof 原理，并用代码实现 1.如果 A 沿着原型链能找到 B.prototype 那么 A instanceof B 为 true
遍历 A 的原型链 找到 B.prototype 返回 true

```js
const instanceof = (A, B) => {
  let p = A;
  while (p) {
    if (p === B.proto ptype) {
      return true;
    }
    p = p.__proto__;
  }
  return false;
};
```

## 使用链表指针获取 json

```js
const json = {
  a: {
    b: {
      c: 1,
    },
  },
};

const path = ["a", "b", "c"];

let p = json;
path.forEach((k) => {
  p = p[k];
});
console.log(p);
```

## 集合 Set

一种无序且唯一数据结构
es6 有集合，Set
去重，判断是否在集合中，求交集

```js
// 去重
const arr = [1, 2, 3, 2];
const arr2 = [...new Set(arr)];

// 判断是否在集合
const set = new Set(arr);
const has = set.has(3);

// 求交集
const arr3 = new Set([2, 3]);
const filter = new Set([...set].filter((item) => arr3.has(item)));
```

### 如何使用 set 对象

```js
let myset = new Set();

myset.add(1);
myset.add(5);

myset.has(5);

myset.delete(5);

myset.keys() === myset.values();

for(letm item of myset) {

}

// 与arr互转
const myarr = [... myset];
const myarr2 = Array.from(myset);

// 交集
const intersection = new Set([...myset].filter(x => myset2.has(x)))
```

## 字典 Map

以键值对增删改查

// 增
const m = new Map();
m.set('a','aa');
// 查
m.get('a') // 'aa'
// 删
m.delete('a')
// 删除多有
m.clear();

## 树

一种分层数据抽象结构

常见树：

- Dom 树
- 级联组件
- Tree 树形结构

树的常用操作：深度/广度优先遍历/先中后序遍历

### 什么是深度优先遍历

尽可能访问深的搜索树的分支

### 什么是广度优先遍历

先访问离根结点最近的节点

### 深度优先算法口诀

- 访问根结点
- 对根结点 children 挨个进行深度优先遍历

```js
const dfs = (root) => {
  console.log(root.val);
  root.children.forEach(dfs);
};
```

### 广度优先算法口诀

- 新建一个队列，把根结点入队
- 队头出队并访问
- 把队头的 children 挨个入队
- 重复第二三步，直到队列为空

```js
const bfs = (root) => {
  const q = [root];
  while (q.length > 0) {
    const n = q.shift();
    console.log(n.val);
    n.children.forEach((child) => q.push(child));
  }
};
```

### 二叉树

二叉树不能被简单定义为每个结点的度都是 2 的树。普通的树并不会区分左子树和右子树，但在二叉树中，左右子树的位置是严格约定、不能交换的

js 中用 object 模拟

```js
// 二叉树结点的构造函数
function TreeNode(val) {
  this.val = val;
  this.left = this.right = null;
}
const node = new TreeNode(1);
```

### 遍历二叉树（递归版）

遍历二叉树算法口诀

- [先序]根结点 -> 左子树 -> 右子树
- [中序]左子树 -> 根结点 -> 右子树
- [后序]左子树 -> 右子树 -> 根结点

```js
const root = {
  val: "A",
  left: {
    val: "B",
    left: {
      val: "D",
    },
    right: {
      val: "E",
    },
  },
  right: {
    val: "C",
    right: {
      val: "F",
    },
  },
};

// 先序遍历
// 所有遍历函数的入参都是树的根结点对象
function preorder(root) {
  // 递归边界，root 为空
  if (!root) {
    return;
  }

  // 先序-输出当前遍历的结点值
  console.log("当前遍历的结点值是：", root.val);
  // 递归遍历左子树
  preorder(root.left);
  // 递归遍历右子树
  preorder(root.right);
}

// 中序遍历
function preorder(root) {
  // 递归边界，root 为空
  if (!root) {
    return;
  }

  // 递归遍历左子树
  preorder(root.left);
  // 中序-输出当前遍历的结点值
  console.log("当前遍历的结点值是：", root.val);
  // 递归遍历右子树
  preorder(root.right);
}

// 后序遍历
function preorder(root) {
  // 递归边界，root 为空
  if (!root) {
    return;
  }

  // 递归遍历左子树
  preorder(root.left);
  // 递归遍历右子树
  preorder(root.right);

  console.log("当前遍历的结点值是：", root.val);
}
```

### 二叉树中的先中后序遍历（非递归版）

递归调用实质是利用堆栈，下面利用栈模拟

先序遍历

- 顺序：根节点 -> 左子树 -> 右子树

```js
// 先序遍历
function preorder(root) {
  if (!root) {
    return;
  }
  const stack = [root];
  while (stack.length) {
    const n = stack.pop();
    console.log(n.val);
    // 这里注意栈是后进先出，要想让左子树先出栈，先让右子树入栈，左子树后入栈
    if (n.right) {
      stack.push(n.right);
    }
    if (n.left) {
      stack.push(n.left);
    }
  }
}
```

中序遍历

- 顺序：左子树 -> 根节点 -> 右子树

```js
function preorder(root) {
  if (!root) {
    return;
  }
  const stack = [];
  let p = root;
  // 此处循环继续到右子树内寻找左子树
  while (stack.length || p) {
    // 此处把所有左子树入栈
    while (p) {
      stack.push(p);
      p = p.left;
    }
    // 左子树出栈并访问
    const n = stack.pop();
    console.log(n.val);
    // 右子树入栈
    p = n.right;
  }
}
```

后序遍历

- 顺序：左子树 -> 右子树 -> 根节点
- 规律：利用先序遍历访问根、左、右进行改造

```js
function preorder(root) {
  if (!root) {
    return;
  }
  // 使用两个栈，一个作为先序栈，一个做后序遍历出栈使用
  const stack = [root];
  const outStack = [];
  // 先序遍历改造
  while (stack.length) {
    const n = stack.pop();
    outStack.push(n);

    if (n.left) {
      stack.push(n.left);
    }
    // 注意这里顺序变化，入栈变成 根->右子树->左子树
    if (n.right) {
      stack.push(n.right);
    }
  }
  // 后序出栈 左、右、根出栈
  while (outStack.length) {
    const n = outStack.pop();
    console.log(n.val);
  }
}
```

### 遍历 JSON 的所有节点值

深度优先遍历

```js
const json = {
  a: {
    b: {
      c: 1,
    },
  },
  d: [1, 2],
};

const dfs = (n, path) => {
  console.log(n, path);
  Object.keys(n).forEach((k) => {
    dfs(n[k], path.concat(k));
  });
};
dfs(json, []);
```

### 渲染树组件

深度优先遍历

```js
import { Tree } from "antd";
const { TreeNode } = Tree;
const treeData = [
  {
    title: "一级菜单",
    key: "1",
    children: [
      {
        title: "二级菜单",
        key: "2",
        children: [
          {
            title: "三级菜单",
            key: "3",
            children: [],
          },
        ],
      },
    ],
  },
  {
    title: "一级菜单2",
    key: "4",
    children: [],
  },
];

const TreeComp = () => {
  const dfs = (n) => {
    return (
      <TreeNode title={n.title} key={n.key}>
        {n.children.map(dfs)}
      </TreeNode>
    );
  };
  return <Tree>{treeData.map(dfs)}</Tree>;
};
```

### leetCode 题目练习

#### 104. 二叉树的最大深度

给定一个二叉树，找出其最大深度。

二叉树的深度为根节点到最远叶子节点的最长路径上的节点数。

说明:  叶子节点是指没有子节点的节点。

示例：
给定二叉树 [3,9,20,null,null,15,7]，返回它的最大深度 3 。
来源：力扣（LeetCode）

利用深度优先遍历做该题

```js
/**
 * @param {TreeNode} root
 * @return {number}
 */
var maxDepth = function(root) {
  let sum = 0;
  function dfs(n, l) {
    if (!n) {
      return;
    }
    if (!n.left && !n.right) {
      sum = Math.max(sum, l);
    }
    dfs(n.left, l + 1);
    dfs(n.right, l + 1);
  }
  dfs(root, 1);
  return sum;
};
```

#### 111. 二叉树的最小深度

给定一个二叉树，找出其最小深度。

最小深度是从根节点到最近叶子节点的最短路径上的节点数量。

说明: 叶子节点是指没有子节点的节点。
示例:

给定二叉树 [3,9,20,null,null,15,7],
返回它的最小深度 2.
来源：力扣（LeetCode）

利用广度优先遍历

```js
/**
 * @param {TreeNode} root
 * @return {number}
 */
var minDepth = function(root) {
  if (!root) {
    return 0;
  }
  const q = [[root, 1]];
  while (q.length) {
    const [n, l] = q.shift();
    if (!n.left && !n.right) {
      return l;
    }
    if (n.left) {
      q.push([n.left, l + 1]);
    }
    if (n.right) {
      q.push([n.right, l + 1]);
    }
  }
};
```

#### 102. 二叉树的层序遍历

给你一个二叉树，请你返回其按 层序遍历 得到的节点值。 （即逐层地，从左到右访问所有节点）。
示例：
二叉树：[3,9,20,null,null,15,7],
返回其层次遍历结果：

```js
[[3], [9, 20], [15, 7]];
```

来源：力扣（LeetCode）

所谓层序遍历就是广度优先遍历
利用广度优先遍历

```js
/**
 * @param {TreeNode} root
 * @return {number[][]}
 */
var levelOrder = function(root) {
  if (!root) {
    return [];
  }
  const q = [[root, 0]];
  const res = [];
  while (q.length) {
    const [n, level] = q.shift();
    if (!res[level]) {
      res.push([n.val]);
    } else {
      res[level].push(n.val);
    }

    if (n.left) {
      q.push([n.left, l + 1]);
    }
    if (n.right) {
      q.push([n.right, l + 1]);
    }
  }
  return res;
};

// 解法2
var levelOrder = function(root) {
  if (!root) {
    return [];
  }

  const q = [root];
  const res = [];
  while (q.length) {
    let len = q.length;
    res.push([]);
    // 把所有同级节点进入队列
    while (len--) {
      const n = q.shift();
      res[res.length - 1].push(n.val);
      if (n.left) {
        q.push(n.left);
      }
      if (n.right) {
        q.push(n.right);
      }
    }
  }
  return res;
};
```

#### 94. 二叉树的中序遍历

给定一个二叉树，返回它的中序 遍历。
输入: [1, null, 2, 3]
输出: [1, 3, 2]
进阶: 递归算法很简单，你可以通过迭代算法完成吗？
来源：力扣（LeetCode）

```js
/**
 * @param {TreeNode} root
 * @return {number[]}
 */
var inorderTraversal = function(root) {
  if (!root) {
    return [];
  }
  const res = [];
  const stack = [];

  let p = root;
  while (p || stack.length) {
    while (p) {
      stack.push(p);
      p = p.left;
    }
    const n = stack.pop();
    res.push(n.val);
    p = n.right;
  }
  return res;
};
```

#### 112.路径总和

给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。
说明: 叶子节点是指没有子节点的节点。
示例:
给定如下二叉树，以及目标和 sum = 22，
返回 true, 因为存在目标和为 22 的根节点到叶子节点的路径 5->4->11->2。
来源：力扣（LeetCode）

利用深度优先遍历，递归，时间复杂度 O(n)，空间复杂度 O(n)

```js
/**
 * @param {TreeNode} root
 * @param {number} sum
 * @return {boolean}
 */
var hasPathSum = function(root, sum) {
  if (!root) {
    return false;
  }
  let res = false;
  function dfs(n, s) {
    console.log(n.val, s);
    if (!n.left && !n.right && s === sum) {
      res = true;
    }
    if (n.left) {
      dfs(n.left, s + n.left.val);
    }
    if (n.right) {
      dfs(n.right, s + n.right.val);
    }
  }
  dfs(root, root.val);

  return res;
};
```

## 堆

堆是一种特殊的完全二叉树
所有的节点都大于等于或者小于等于子节点
js 中通常使用数组表示堆

## 排序和搜索

排序：就是某个乱序的数组变成升序或者降序的数组

搜索：就是找某个元素的下标

数组的 sort
搜索 indexOf
排序

- 冒泡排序

```js
const bubbleSort = (arr) => {
  const length = arr.length;
  for (let i = 0; i < length - 1; i++) {
    for (let j = 0; j < length - 1 - i; j++) {
      if (arr[j] > arr[j + 1]) {
        const temp = arr[j + 1];
        arr[j + 1] = arr[j];
        arr[j] = temp;
      }
    }
  }
  return arr;
};
```

- 选择排序
- 插入排序
- 归并排序
- 快速排序

搜索

- 顺序搜索
- 二分搜索
