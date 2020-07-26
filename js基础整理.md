## css 布局方式

#### 文档流布局（单列布局）

这是最基本的布局方式，就是按照文档的顺序一个一个显示出来，块元素独占一行，行内元素共享一行

#### 浮动布局

浮动方式布局就是使用 float 属性，使元素脱离文档流，浮动起来

#### 定位布局

我们也可以通过`position absolute`属性来进行定位

#### flex 布局(弹性盒子布局)

flex 只支持 ie 10+
垂直居中`align-items:center; justify-content：center`
水平两端对齐：`justify-content: space-between;`

## call、apply、bind 区别，能手写一个 bind 和 apply 吗

共同点：均改变函数执行时的上下文，再具体一点就是改变函数运行时的 this 指向
不同： call 和 apply 改变了函数的 this 上下文后便执行该函数, 而 bind 则是返回改变了上下文后的一个函数
call、apply 的区别
call 和 aplly 的第一个参数都是要改变上下文的对象，而 call 从第二个参数开始以参数列表的形式展现，apply 则是把除了改变上下文对象的参数放在一个数组里面作为它的第二个参数。

#### 手写 call

```js
Function.prototype.newCall = function (context) {
  var context = Object(context) || window;

  context.fn = this;

  let result = "";
  const args = [...arguments].slice(1);
  result = context.fn(...args);

  delete context.fn;

  return result;
};
```

#### 手写 apply

```js
Function.prototype.newApply = function (context, args) {
  var context = Object(context) || window;

  context.fn = this;

  let result = "";

  if (args) {
    result = context.fn(...args);
  } else {
    result = context.fn();
  }

  delete context.fn;

  return result;
};
```

#### 手写 bind

bind() 方法创建一个新的函数，在 bind() 被调用时，这个新函数的 this 被指定为 bind() 的第一个参数，而其余参数将作为新函数的参数

```js
Function.prototype.newBind = function(context) {
  if(typeof this !== function) {
    throw new Error('不是一个函数');
  }
  const self = this;
  const args1 = [...arguments].slice(1);

  return function() {
    // 函数科里化
    const args2 = [...arguments];
    self.apply(context, args1.concat(args2));
  }
}
```

#### 手写 Promise 简版

```js
function myPromise(constructor) {
  let self = this;
  // pending resolved rejected
  self.status = "pending";
  self.resolveVal = undefined;
  self.rejectedVal = undefined;
  function resolve(value) {
    if (self.status === "pending") {
      self.resolveVal = value;
      self.status = "resolved";
    }
  }
  function reject(reason) {
    if (self.status === "pending") {
      self.rejectedVal = reason;
      self.status = "rejected";
    }
  }
  try {
    constructor(resolve, reject);
  } catch (e) {
    reject(e);
  }
}

myPromise.prototype.then = function (onFullfilled, onRejected) {
  let self = this;
  switch (self.status) {
    case "resolved":
      onFullfilled(self.resolveVal);
      break;
    case "rejected":
      onRejected(self.rejectedVal);
      break;
  }
};
```

#### 手写 Promise、Promise.all、promise.race

```js
const PENDING = 'pending';
const RESOLVED = 'resolved';
const REJECTED = 'rejected';

function myPromise(fn) {
  const self = this; // 在function找到正确this指向
  self.status = PENDING;
  self.value = null; // 保存resolve/reject传入的值

  self.resolvedCallbacks = []; // 保存then中的回调,promise执行完后，state状态还可能是padding
  self.rejectedCallbacks = []; // 因此把回到函数保存起来，等待state状态改变时执行

  // 实现resolve
  funtion resolve(value) {
    if(value instanceof myPromise) {
      return value.then(resolve, reject)
    }
    setTimeout(()=> {
      if(self.status === PENDING) {
        self.status = RESOLVED;
        self.value = value;
        self.resolvedCallbacks.forEach(cb => cb(self.value)) // 执行回调
      }
    },0);
  }

  function reject(value) {
     setTimeout(()=> {
      if(self.status === PENDING) {
        self.status = REJECTED;
        self.value = value;
        self.rejectedCallbacks.forEach(cb => cb(self.value)) // 执行回调
      }
    },0);
  }
  // 执行函数fn
  try {
    fn(resolve,reject);
  } catch(e) {
    reject(e)
  }
}

myPromise.prototype.then = function(onFulfilled, onRejected) {
  const self = this;
  // 参数为可选参数 判断下
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v;
  onRejected = typeof onRejected === 'function' ? onRejected : v => {throw v};
  // 如果状态还是padding, 往回掉函数中push函数, 否则执行函数
  if(self.status === PENDING) {
    self.resolvedCallbacks.push(onFulfilled);
    self.rejectedCallbacks.push(onRejected);
  }
  if(self.status === RESOLVED) {
    onFulfilled(self.value);
  }
  if(self.status === REJECTED) {
    onRejected(self.value);
  }
}

```

#### promise.all

> 将多个 Promise 实例包装成一个 Promise 实例，接收包含 Promise 对象或普通值的数组(或其它可迭代对象)作为参数，成功时返回值为 Promise 实例数组对应的结果数组，失败时返回第一个 rejected 的 Promise 实例的结果。

```js
const p1 = new Promise((resolve) => {
  setTimeout(resolve, 200, 1);
});
const p2 = new Promise((resolve) => {
  resolve(2);
});
const p3 = 3;
console.log(Promise.all([p1, p2, p3])); // [1, 2, 3]

Promise.prototype.all = function (promiseArr) {
  let resArr = [];
  let count = 0;
  let len = promiseArr.length;
  // 返回一个新的promise实例
  return new Promise(function (resolve, reject) {
    for (let promise of promiseArr) {
      Promise.resolve(promise).then(
        function (res) {
          resArr[count] = res;
          count++;
          if (count === len) {
            return resolve(resArr);
          }
        },
        function (err) {
          return reject(err);
        }
      );
    }
  });
};
```

#### 使用 async await 实现 promise.all

```js
const promiseAsync = async (arr) {
  const resArr = [];
  for(let promise of arr) {
    resArr.push(await promise);
  }
  return await resArr;
}
```

#### 使用 apromise.race

> 与 Promise.all 一样，Promise.race 也接收包含 Promise 对象或普通值的数组(或其它可迭代对象)作为参数，返回一个 Promise 实例对象。与 Promise.all 不同的是，一旦有一个 Promise 实例对象 resolve ，立即把这个 resolve 的值作为 Promise.race resolve 的值。一旦有一个对象 reject， Promise.race 也会立即 reject。

```js
Promise.prototype.race = function (promiseArr) {
  return new Promise(function (resolve, reject) {
    for (let promise of promiseArr) {
      if (typeof promise === "object" && typeof promise.then === "function") {
        promise.then(resolve, reject);
      } else {
        // 不是promise实例对象直接返回
        resolve(promise);
      }
    }
  });
};
```

## 实现 js 的 sort api

#### 使用冒泡排序

```js
Array.prototype.csSort = function () {
  var newarr = this;
  /** 2、 冒泡法排序
   * 插入发排序，即那数组的前一项和后一项对比，如果前面一项小于后面
   * 一项，则将两者位置互换，从数组第1个元素开始对比；如下示例
   */
  for (let i = 0; i < newarr.length; i++) {
    for (let j = 0; j < newarr.length - i; j++) {
      if (newarr[j] > newarr[j + 1]) {
        let pre = newarr[j];
        newarr[j] = newarr[j + 1];
        newarr[j + 1] = pre;
      }
    }
  }
  return newarr;
};

function sort(arr) {
  var smallOne;
  for (var i = 0; i < arr.length; i++) {
    for (var j = 0; j < arr.length - i; j++) {
      if (arr[j] - arr[j + 1] > 0) {
        smallOne = arr[j + 1];
        arr[j + 1] = arr[j];
        arr[j] = smallOne;
      }
    }
  }
}
```

## 算法，合并两个有序链表

```js
/**
 * Definition for singly-linked list.
 * function ListNode(val) {
 *     this.val = val;
 *     this.next = null;
 * }
 */
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */

var mergeTwoLists = function (l1, l2) {
  if (l1 == null) return l2;
  if (l2 == null) return l1;
  if (l1.val < l2.val) {
    l1.next = mergeTwoLists(l1.next, l2);
    return l1;
  } else {
    l2.next = mergeTwoLists(l1, l2.next);
    return l2;
  }
};
```

## 算法 一个有序数组反转后，找到第 K 大的元素(时间复杂度小于等于 nlogn)

## 验证回文串

给定一个字符串，验证它是否是回文串，只考虑字母和数字字符，可以忽略字母的大小写。
说明：本题中，我们将空字符串定义为有效的回文串。

```js
var isPalindrome = function (s) {
  let reg = /[a-z]|[0-9]/;
  s = s
    .split("")
    .map((x) => x.toLowerCase())
    .filter((x) => reg.test(x))
    .join("");
  let head = 0;
  let tail = s.length - 1;
  while (head <= tail) {
    if (s[head] !== s[tail]) return false;
    head++;
    tail--;
  }
  return true;
};
```

## ES6 都有哪些新的 api，每一个都详细谈谈

includes 第一个参数表示需要查找的字符串，第二个参数表示查找的起始下标位置

```js
let a = "abc";
let result1 = a.includes("a"); // true
let result2 = a.includes("d"); // false
let result3 = a.includes("a", 1); // false
```

startsWith 第一个参数表示需要查找的字符串，第二个参数表示查找的起始下标位置

```js
let a = "abc";
let result1 = a.startsWith("a"); // true
let result2 = a.startsWith("b"); // false
let result3 = a.startsWith("b", 1); // true
```

数值的方法
Number.isInteger()

```js
Number.isInteger(2); // true
Number.isInteger(2.1); // false
```

Array.form()方法
用于将类数组的对象转为真实的数组，比如 dom

```js
let domList = document.getElementsByTagName("div");
let result = Array.from(domList);
console.log(resule, result instanceof Array); // [div, div, div] true
```

Array.of()
用于将一组值转为数组

```js
let a = "123";
let b = "456";
let result = Array.of(a, b);
console.log(result); // ['123', '456']
```

## vue 的 diff 和 react 的 diff

前端主流框架 vue 和 react 中都使用了虚拟 DOM（virtual DOM）技术，因为渲染真实 DOM 的开销是很大的，性能代价昂贵，比如有时候我们修改了某个数据，如果直接渲染到真实 dom 上会引起整个 dom 树的重绘和重排，而我们只需要更新修改过的那一小块 dom 而不要更新整个 dom

说到 DOM-diff 那一定要清楚其存在的意义，给定任意两棵树，采用先序深度优先遍历的算法找到最少的转换步骤
DOM-diff 比较两个虚拟 DOM 的区别，也就是在比较两个对象的区别。

梳理一下整个 DOM-diff 的过程：
用 JS 对象模拟 DOM（虚拟 DOM）
把此虚拟 DOM 转成真实 DOM 并插入页面中（render）
如果有事件发生修改了虚拟 DOM，比较两棵虚拟 DOM 树的差异，得到差异对象（diff）
把差异对象应用到真正的 DOM 树上（patch）

## http 和 https 的区别

## url 从输入到页面展示的过程

## https 的握手有了解过吗，详细过程

## 判断数据类型的几种方法，优缺点，实现方式

## react 中 setState 以后，是子树渲染还是整颗树渲染还是其他情况？

## setState 是异步的还是同步的，内部采用的是什么机制

## react 事件了解吗？ (合成事件) 和普通事件有什么区别，实现原理

## react Fibber 了解吗？ (只答了时间片轮转算法，调度策略)

## react diff 采用的什么原则， (重点 last_index)

## 算法，最大连续子序列(dp)

## instanceOf 原理，手写一个 instanceOf

## react 开发的几种方式

## react 函数式写法和类写法的优缺点

## react 类组件新增的两个生命周期是哪两个，作用是什么

## react hook 使用过吗？ 使用过哪些 hook

## react 高阶组件有哪几种方式，如何写一个高阶组件

## 什么时候使用 useRef，可以做到什么事情

## 算法，链表反转

## 二维码扫描登录实现原理

## 大体积文件上传【分片、断点续传】

## 洗牌算法

## node 为什么适合做高并发

## 实现 cacheRequest 方法，保证使用 ajax 请求相同资源实际只发送一次请求

## 一个无限长有序可重复数组 N，查 X 最后出现的位置

## treeshaking 原理

## node 如何捕获异常

## 尾递归函数优化

## 给定一个整数数组和一个目标值，找出数组中和为目标值的两个数

你可以假设每个输入只对应一种答案，且同样的元素不能被重复利用
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]

```js
const twoNum = (nums, target) => {
  const maps = {};
  for (let i = 0, len = nums.length; i < len; i++) {
    let diff = target - nums[i];
    if (maps[diff] > -1) {
      return [maps[diff], i];
    }
    maps[nums[i]] = i;
  }
  return [];
};
```
