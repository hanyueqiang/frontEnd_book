## 前端算法系统学习(含 leetcode 真题)

> 前端学习算法的目的在有限的时间内，做对关键的已知题目；通过真题消化其中核心的算法思想和解题套路，从而最大化各位在真正面试时做对任意未知题目的可能性。[—修言]

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

## 图

图是网络结构的抽象模型，是一组由边连接的节点，图可以表示任何二元关系，比如道路、航班

js 中没有图,但可以是使用 object 和 array 构建

图的常用操作

- 深度优先遍历

尽可能搜索图的分支，
访问根节点
对根对没访问对相邻节点挨个进行深度优先遍历

```js
const graph = {
  0: [1, 2],
  1: [2],
  2: [0, 3],
  3: [3],
};

const grapgDfs = (n) => {
  const set = new Set();

  function dfs(root) {
    console.log(root);
    set.add(root);
    graph[root].forEach((item) => {
      if (!set.has(item)) {
        dfs(item);
      }
    });
  }
  dfs(n);
};
grapgDfs(2);
```

- 广度优先遍历

先访问离根结点最近的节点
新建一个队列，把根节点入队
把队头出队并访问
把队头的没访问相邻节点入队

```js
const graph = {
  0: [1,2],
  1: [2],
  2: [0,3],
  3: [3]
};

const graphBfs = (root) {
  const set = new Set();
  const q = [root];
  set.add(root);
  while(q.length) {
    const n = q.shift();
    console.log(n);
    graph[n].forEach(c => {
      if(!set.has(c)) {
        q.push(c)
      }
    })
  }
}
graphBfs(2)
```

## 排序和搜索

数组排序

```js
arr.sort((a, b) => {
  return a - b;
});
```

排序

选择排序
插入排序
归并排序
快速排序

搜索

顺序搜索
二分搜索

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

思想是遍历数组找到最小值，把最小值放到素组的左面

```js
function selectSort(arr) {
  const length = arr.length;
  for (let i = 0; i < length - 1; i++) {
    let indexMin = i;
    for (let j = i; j < length; j++) {
      if (arr[indexMin] > arr[j]) {
        indexMin = j;
      }
    }
    if (indexMin !== i) {
      const temp = arr[i];
      arr[i] = arr[indexMin];
      arr[indexMin] = temp;
    }
  }
  return arr;
}
```

- 插入排序

时间复杂度 O(n\*2)
思路

- 从第二个数开始往前比
- 比他大就往后排
- 依次类推到最后一个数

```js
function insertSort(arr) {
  const length = arr.length;
  for (let i = 1; i < length; i++) {
    const temp = arr[i];
    let index = i;
    while (index > 0) {
      if (arr[index - 1] > temp) {
        arr[index] = arr[index - 1];
      } else {
        break;
      }
      index--;
    }
    arr[index] = temp;
  }

  return arr;
}
```

- 归并排序

归并排序是对分治思想的典型应用，它按照如下的思路对分治思想进行：

1.分解子问题：将需要被排序的数组从中间分割为两半，然后再将分割出来的每个子数组各分割为两半，重复以上操作，直到单个子数组只有一个元素为止。

2.求解每个子问题：从粒度最小的子数组开始，两两合并、确保每次合并出来的数组都是有序的。（这里的“子问题”指的就是对每个子数组进行排序）。

3.合并子问题的解，得出大问题的解：当数组被合并至原有的规模时，就得到了一个完全排序的数组

合并是将原子项反复地组装回原有的大数组。整个过程符合两个特征： 1.重复（令人想到递归或迭代） 2.有去有回（令人想到回溯，进而明确递归这条路）

因此，归并排序在实现上依托的就是递归思想。除此之外，这里还涉及到另一个小小的知识点——两个有序数组的合并(双指针)
归并排序的时间复杂度是 `O(nlog(n))`

思路：
分：数组分为两半，再递归对子数组排序
合：两个数进行合并，在对有序数组进行合并，直到子数组合并一个完整数组
新建一个空数组 res,用于存放最终排序后的数组

```js
function mergeSort(arr) {
  if (arr.length === 1) {
    return arr;
  }
  const mid = Math.floor(arr.length / 2);
  const left = arr.slice(0, mid);
  const right = arr.slice(mid, arr.length);
  const orderLeft = mergeSort(left);
  const orderRight = mergeSort(right);
  const res = [];
  while (orderLeft.length || orderRight.length) {
    if (orderLeft.length && orderRight.length) {
      res.push(
        orderLeft[0] < orderRight[0] ? orderLeft.shift() : orderRight.shift()
      );
    } else if (orderLeft.length) {
      res.push(orderLeft.shift());
    } else if (orderRight.length) {
      res.push(orderRight.shift());
    }
  }
  return res;
}
```

#### 合并两个有序链表 (leetCode21)

将两个升序链表合并为一个新的 升序 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val, next) {
 *     this.val = (val===undefined ? 0 : val)
 *     this.next = (next===undefined ? null : next)
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var mergeTwoLists = function(l1, l2) {
  let res = new ListNode(0);
  let p1 = l1;
  let p2 = l2;
  let p = res;
  while (p1 && p2) {
    if (p1.val < p2.val) {
      p.next = p1;
      p1 = p1.next;
    } else {
      p.next = p2;
      p2 = p2.next;
    }
    p = p.next;
  }
  if (p1) {
    p.next = p1;
  }
  if (p2) {
    p.next = p2;
  }
  return res.next;
};
```

- 快速排序

chrome 做 sort 排序算法
分区：从数组中选择任意一个基准，所有比基准小的元素放在基准前面，比基准大的元素当奥基准的后面
递归：递归对基准前后对子数组进行分区

```js
function quickSort(arr) {
  if (arr.length <= 1) {
    return arr;
  }
  const left = [];
  const right = [];
  const mid = arr[0];
  for (let i = 1; i < arr.length; i++) {
    if (arr[i] > mid) {
      right.push(arr[i]);
    } else {
      left.push(arr[i]);
    }
  }
  return [...quickSort(left), mid, ...quickSort(right)];
}
```

- 顺序搜索
  O(n)

```js
function search(arr, str) {
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] === str) {
      return i;
    }
  }
  return -1;
}
```

- 二分搜索
  O(logN)
  也称折半搜索
  前提是数组有序

思路：

从数组中间元素开始
如果目标值大于或小于中间元素，择在另一半数组搜索

```js
function secondSort(arr, str) {
  let low = 0;
  let high = arr.length - 1;

  while (low <= high) {
    const mid = Math.floor((low + high) / 2);
    const element = arr[mid];
    if (element < str) {
      low = mid + 1;
    } else if (element > str) {
      high = mid - 1;
    } else {
      return mid;
    }
    return -1;
  }
}
```

#### 猜数字大小(leetcode374)

猜数字游戏的规则如下：

每轮游戏，我都会从  1  到  n 随机选择一个数字。 请你猜选出的是哪个数字。
如果你猜错了，我会告诉你，你猜测的数字比我选出的数字是大了还是小了。
你可以通过调用一个预先定义好的接口 int guess(int num) 来获取猜测结果，返回值一共有 3 种可能的情况（-1，1  或 0）：

-1：我选出的数字比你猜的数字小 pick < num
1：我选出的数字比你猜的数字大 pick > num
0：我选出的数字和你猜的数字一样。恭喜！你猜对了！pick == num

```js
/**
 * Forward declaration of guess API.
 * @param {number} num   your guess
 * @return 	            -1 if num is lower than the guess number
 *			             1 if num is higher than the guess number
 *                       otherwise return 0
 * var guess = function(num) {}
 */
/**
 * @param {number} n
 * @return {number}
 */
var guessNumber = function(n) {
  let low = 1;
  let high = n;
  while (low <= high) {
    let mid = Math.floor((low + high) / 2);
    const res = guess(mid);
    if (res === 0) {
      return mid;
    } else if (res === -1) {
      high = mid - 1;
    } else {
      low = mid + 1;
    }
  }
};
```

## 分而治之

是算法设计中一种方法
递归解决小问题，在将结果合并解决原来的问题

场景一：归并排序

分：把数组从中间一份为二
解：递归对两个子数组进行归并排序
合：合并有序子数组

场景二：快速排序

分：选基准，按基准把数组分为两个子数组
解：递归对两个子数组进行快速排序
合：对两个子数组进行合并

#### 翻转二叉树(leetcode226)

翻转一棵二叉树。

解题思路：
先翻转左右子树，再将子树换个位置
符合分解合特性
分：获取左右子树 `root.right`
解：递归翻转左右子树 `invertTree(root.right)`
合：将翻转后的左右子树放到根节点 `left: invertTree(root.right)`

```js
/**
 * Definition for a binary tree node.
 * function TreeNode(val) {
 *     this.val = val;
 *     this.left = this.right = null;
 * }
 */
/**
 * @param {TreeNode} root
 * @return {TreeNode}
 */
var invertTree = function(root) {
  if (!root) {
    return null;
  }
  return {
    val: root.val,
    left: invertTree(root.right),
    right: invertTree(root.left),
  };
};
```

#### 相同的树(leetcode100)

给定两个二叉树，编写一个函数来检验它们是否相同。
如果两个树在结构上相同，并且节点具有相同的值，则认为它们是相同的

解题思路：
两个树：根节点值相同，左子树相同，右子树相同
符合：分解合特性
分：获取两个树的左子树、右子树 `p.left`
解：递归判断是否相同 `isSameTree(p.left, q.left)`
合：根节点是否相同 `p.val === q.val`
时间复杂度 O(n)

```js
/**
 * @param {TreeNode} p
 * @param {TreeNode} q
 * @return {boolean}
 */
var isSameTree = function(p, q) {
  if (!p && !q) {
    return true;
  }
  if (
    p &&
    q &&
    p.val === q.val &&
    isSameTree(p.left, q.left) &&
    isSameTree(p.right, q.right)
  ) {
    return true;
  }
  return false;
};
```

#### 对称二叉树(leetcode101)

给定一个二叉树，检查它是否是镜像对称的。
例如，二叉树 [1,2,2,3,4,4,3] 是对称的

解题思路：
转化为：左右子树是否镜像
分解为：树 1 的左子树与树 2 的右子树是否镜像，
分：获取两个树的左子树和右子树
解：递归判断树 1 左和树 2 右是否镜像，树 1 右与树 2 左是否镜像
时间复杂度 O(n)

```js
/**
 * @param {TreeNode} root
 * @return {boolean}
 */
var isSymmetric = function(root) {
  if (!root) {
    return true;
  }
  function isMirror(l, r) {
    if (!l && !r) {
      return true;
    }
    if (
      l &&
      r &&
      l.val === r.val &&
      isMirror(l.left, r.right) &&
      isMirror(l.right, r.left)
    ) {
      return true;
    }
    return false;
  }
  return isMirror(root.left, root.right);
};
```

## 动态规划

动态规划是算法设计的中一种方法
将一个问题分解相互重叠的子问题，通过反复求解子问题来解决相同问题

斐波那契数列
定义子问题：`F(n) = F(n-1)+F(n-2)`

#### 爬楼梯(leetcode70)

解题思路：
爬到第 n 阶可以第 n-1 阶爬一个台阶，或者 n-2 阶爬两个台阶

```js
var climbStairs = function(n) {
  if (n < 2) {
    return 1;
  }
  let dp0 = 1;
  let dp1 = 1;
  for (let i = 2; i <= n; i++) {
    const temp = dp0;
    dp0 = dp1;
    dp1 = dp0 + temp;
  }
  return dp1;
};
```

#### 打家劫舍(leetcode198)

你是一个专业的小偷，计划偷窃沿街的房屋。每间房内都藏有一定的现金，影响你偷窃的唯一制约因素就是相邻的房屋装有相互连通的防盗系统，如果两间相邻的房屋在同一晚上被小偷闯入，系统会自动报警。

给定一个代表每个房屋存放金额的非负整数数组，计算你 不触动警报装置的情况下 ，一夜之内能够偷窃到的最高金额。

解题思路：

f(k) = 从前 K 个房屋能偷窃的最大数额

ak=第 k 个房屋钱数
取最大值前一位加当前金额与前一个房间最大金额
f(k) = max(f(k-2)+Ak, f(k-1))

```js
var rob = function(nums) {
  if (nums.length === 0) {
    return 0;
  }
  const dp = [0, nums[0]];

  for (let i = 2; i <= nums.length; i++) {
    dp[i] = Math.max(dp[i - 2] + nums[i - 1], dp[i - 1]);
  }
  return dp[nums.length];
};
// 优化
var rob = function(nums) {
  if (nums.length === 0) {
    return 0;
  }
  let dp0 = 0;
  let dp1 = nums[0];

  for (let i = 2; i <= nums.length; i++) {
    const dp2 = Math.max(dp0 + nums[i - 1], dp1);
    dp0 = dp1;
    dp1 = dp2;
  }
  return dp1;
};
```

#### 零钱兑换(leetcode322)

## 贪心算法

是算法设计中的一种方法

期盼每个阶段的局部最优选择，从而达到全局的最优
结果并不一定是最优
