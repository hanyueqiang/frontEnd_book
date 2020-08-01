## 判断数据类型的几种方法，优缺点，实现方式

1.typeof 直接返回数据类型字符串，无法判断数组，对象，null,其中 null、{}、[] 都返回 object
2.instanceof 判断某个实例是不是属于原型

## react 中 setState 以后，是子树渲染还是整颗树渲染还是其他情况？

## setState 是异步的还是同步的，内部采用的是什么机制

## react 事件了解吗？ (合成事件) 和普通事件有什么区别，实现原理

## react Fibber 了解吗？ (只答了时间片轮转算法，调度策略)

## react diff 采用的什么原则， (重点 last_index)

## 算法，最大连续子序列(dp)

当子序列的某个元素之前的元素和为负数时，他对后边的最大和一定是一个负向增益，没有该元素本身大,利用这个特点，我们把目光投向整个数组，如果我们从前向后遍历，当遇到前方和为负数时，就可以抛点前边的元素，从当前元素继续向后去计算，也可以总结成一个动态规划的公式:`dp = Math.max(dp + current, current)`

```js
/**
 * @param {number[]} nums
 * @return {number}
 */
let maxSubArray = function(nums) {
  // 默认当前的最大和为第一个元素
  let sum = nums[0];
  // dp代表以当前元素结尾的最大和，默认也是第一个元素
  let dp = nums[0];
  // 从数组的第二个元素开始循环
  for (let i = 1; i < nums.length; i++) {
    // 上文中提到的dp公式
    dp = Math.max(dp + nums[i], nums[i]);
    // 同时进行当前最大值的记录
    sum = Math.max(sum, dp);
  }
  return sum;
};
```

## instanceOf 原理，手写一个 instanceOf

instanceof 可以检测某个对象是不是另一个对象的实例
如果一个对象是数组，应该怎么判断？用 arr instanceof Array

## react 开发的几种方式

## react 函数式写法和类写法的优缺点

## react 类组件新增的两个生命周期是哪两个，作用是什么

## react hook 使用过吗？ 使用过哪些 hook

React 现在的渲染都是由 Fiber 来调度
Fiber 调度过程中的两个阶段(以 Render 为界)

## react 高阶组件有哪几种方式，如何写一个高阶组件

## 什么时候使用 useRef，可以做到什么事情

## 算法，链表反转

## 二维码扫描登录实现原理

移动端基于 token 的认证机制。
二维码扫码登录的原理。

## 大体积文件上传【分片、断点续传】

使用 webuploader 组件实现大文件分片上传，断点续传,webuploader：是一个以 HTML5 为主， Flash 为辅的文件上传组件，采用大文件分片/并发上传的方式，极大地提高了文件上传的效率，同时兼容多种浏览器版本；

## 洗牌算法

## node 为什么适合做高并发

I/O 密集型任务

## 一个无限长有序可重复数组 N，查 X 最后出现的位置

## treeshaking 原理

Tree-shaking 的本质是消除无用的 js 代码。无用代码消除在广泛存在于传统的编程语言编译器中，编译器可以判断出某些代码根本不影响输出
ES6 模块依赖关系是确定的，和运行时的状态无关，可以进行可靠的静态分析，这就是 tree-shaking 的基础。

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

## 字符创连续最多字符

## redux 的设计

### 闭包 什么时候用,有什么特点

1、函数嵌套函数

2、内部函数可以访问外部函数的变量

3、参数和变量不会被回收。

```js
function test() {
  var a = 0;
  return function() {
    a++;
    alert(a);
  };
}
var atest = new test(); //引用返回的函数
atest(); // 1
```

### 箭头函数和普通函数有什么区别

.箭头函数相当于匿名函数，是不能作为构造函数的，不能使用 new 2.箭头函数不绑定 arguments,取而代之用 rest 参数…解决 3.箭头函数会捕获其所在上下文的 this 值，作为自己的 this 值。即箭头函数的作用域会继承自外围的作用域。 4.箭头函数当方法使用的时候没有定义 this 的绑定
.箭头函数没有函数原型

```js
obj = {
  a: 10,
  b: () => {
    console.log(this.a); //undefined
    console.log(this); //window
  },
  c: function() {
    return () => {
      console.log(this.a); //10
    };
  },
};
obj.b();
obj.c(); // 10
```

## JS 原型链与继承

> 每个构造函数(constructor)都有一个原型对象(prototype),原型对象都包含一个指向构造函数的指针,而实例(instance)都包含一个指向原型对象的内部指针.

确定原型和实例的关系,使用 instanceof 操作符

## CommonJS、AMD、CMD 和 ES6 模块化区别

NodeJS 是 CommonJS 规范的实现，webpack 也是以 CommonJS 的形式来书写。node.js 将 javascript 语言用于服务器端编程。

## 随机生成给定长度的字符串

## DOCTYPE 有哪些写法，有什么区别，什么是怪异模式，有哪些废弃掉的元素

## 引入 css 有哪些方式，link 中的 media 属性是什么作用，有哪些取值

## async，await 的理解

## 实现一个 repeat

## 事件代理

## 写一个计数器

```js
var count = (function() {
  var counter = 0;
  return function() {
    return ++counter;
  };
})();
```

## 代码输出

```js
function Foo() {
  Foo.a = function() {
    console.log(1);
  };
  this.a = function() {
    console.log(2);
  };
}
Foo.prototype.a = function() {
  console.log(3);
};
Foo.a = function() {
  console.log(4);
};

Foo.a(); // 4
let obj = new Foo();
obj.a(); // 2
Foo.a(); // 4
```
