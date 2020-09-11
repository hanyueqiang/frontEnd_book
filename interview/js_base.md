## js 篇

#### 数组处理函数有哪些？ 创建数组新引用有哪些？

```
// every(): 数组的每一项都满足给定条件则返回true
var arr = [1, 2, 3, 4, 5];
var everyResult = arr.every(function(item, index, array) {
  return item > 2;
});

console.log(everyResult); // false

// some(): 数组中只需有一项满足给定条件则返回true
var arr = [1, 2, 3, 4, 5];
var everyResult = arr.some(function(item, index, array) {
  return item > 2;
});

console.log(everyResult); // true

// filter(): 返回所有满足给定条件的数据项所组成的新数组
var arr = [1, 2, 3, 4, 5];
var everyResult = arr.filter(function(item, index, array) {
  return item > 2;
});

console.log(everyResult); // [3, 4, 5]
console.log(arr); // [1,2,3,4,5]

// map()：对数组的每一项应用给定条件，返回新的数组
var arr = [1, 2, 3, 4, 5];
var everyResult = arr.map(function(item, index, array) {
  return item * 2;
});

console.log(everyResult); // [2, 4, 6, 8, 10]
console.log(arr); // [1, 2, 3, 4, 5]

// forEach(): 数组遍历，与for循环一样
var arr = [1, 2, 3, 4, 5];
arr.forEach(function(item, index, array) {
// 执行某些操作
});

// reduce()和reduceRight(), 这两个方法只是遍历方向不同
var arr = [1, 2, 3, 4, 5];
var sum = arr.reduce(function(prev, cur, index, array){
  return prev + cur;
}, 0);
// 注意初始值不设的话，遇到空数组会报错
console.log(sum); // 15

```

#### es6 let const 区别？

let 和 const 的相同点:

1.只在声明所在的块级作用域内有效。

2.不提升，同时存在暂时性死区，只能在声明的位置后面使用。

3.不可重复声明。

let 和 const 的不同点:

1.let 声明的变量可以改变，值和类型都可以改变；const 声明的常量不可以改变，这意味着，const 一旦声明，就必须立即初始化，不能以后再赋值;

```
const i ; // 报错，一旦声明，就必须立即初始化
const j = 5;
j = 10; // 报错，常量不可以改变
```

2.数组和对象等复合类型的变量，变量名不指向数据，而是指向数据所在的地址。const 只保证变量名指向的地址不变，并不保证该地址的数据不变，所以将一个复合类型的变量声明为常量必须非常小心

```
const arr = [];
// 报错，[1,2,3]与[]不是同一个地址
arr = [1,2,3];
const arr = [];
// 不报错，变量名arr指向的地址不变，只是数据改变
arr[0] = 1;
arr[1] = 2;
arr[2] = 3;
console.log(arr.length); // 输出：3
// 若想让定义的对象或数组的数据也不能改变，可以使用object.freeze(arr)进行冻结。冻结指的是不能向这个对象或数组添加新的属性，不能修改已有属性的值，不能删除已有属性。
const arr = [];
Object.freeze(arr);
// 不报错，但数据改变无效
arr[0] = 1;
arr[1] = 2;
arr[2] = 3;
console.log(arr.length); // 输出：0

var tmp = new Date();
function f() {
  console.log(tmp); // 想打印外层的时间作用域
  if (false) {
    var tmp = 'hello world'; // 这里声明的作用域为整个函数,变量提升
  }
}
f(); // undefined
```

#### js 性能优化有哪些？

1. 减少 HTTP 请求
2. 使用 HTTP2
3. 使用服务端渲染
   客户端渲染: 获取 HTML 文件，根据需要下载 JavaScript 文件，运行文件，生成 DOM，再渲染。
   服务端渲染：服务端返回 HTML 文件，客户端只需解析 HTML。
   优点：首屏渲染快，SEO 好;
4. 静态资源使用 CDN
5. 将 CSS 放在文件头部，JavaScript 文件放在底部
   所有放在 head 标签里的 CSS 和 JS 文件都会堵塞渲染。如果这些 CSS 和 JS 需要加载和解析很久的话，那么页面就空白了。所以 JS 文件要放在底部，等 HTML 解析完了再加载 JS 文件。
   那为什么 CSS 文件还要放在头部呢？
   因为先加载 HTML 再加载 CSS，会让用户第一时间看到的页面是没有样式的、“丑陋”的，为了避免这种情况发生，就要将 CSS 文件放在头部了。
   另外，JS 文件也不是不可以放在头部，只要给 script 标签加上 defer 属性就可以了，异步下载，延迟执行
6. 使用字体图标 iconfont 代替图片图标
   字体图标就是将图标制作成一个字体，使用时就跟字体一样，可以设置属性，例如 font-size、color 等等，非常方便。并且字体图标是矢量图，不会失真。还有一个优点是生成的文件特别小
7. 善用缓存，不重复加载相同的资源
8. 压缩文件
   压缩文件可以减少文件下载时间，让用户体验性更好。
   得益于 webpack 和 node 的发展，现在压缩文件已经非常方便了。
   在 webpack 可以使用如下插件进行压缩：
   JavaScript：UglifyPlugin
   CSS ：MiniCssExtractPlugin
   HTML：HtmlWebpackPlugin
   其实，我们还可以做得更好。那就是使用 gzip 压缩。可以通过向 HTTP 请求头中的 Accept-Encoding 头添加 gzip 标识来开启这一功能。当然，服务器也得支持这一功能。
   gzip 是目前最流行和最有效的压缩方法。举个例子，我用 Vue 开发的项目构建后生成的 app.js 文件大小为 1.4MB，使用 gzip 压缩后只有 573KB，体积减少了将近 60%。
9. 图片优化
   图片延迟加载,在页面中，先不给图片设置路径，只有当图片出现在浏览器的可视区域时，才去加载真正的图片，这就是延迟加载。对于图片很多的网站来说，一次性加载全部图片，会对用户体验造成很大的影响，所以需要使用图片延迟加载。
10. 通过 webpack 按需加载 JavaScript 代码
11. 使用事件委托
12. if-else 对比 switch
    当判断条件数量越来越多时，越倾向于使用 switch 而不是 if-else。

#### Object 上有哪些常用的函数？

1.hasOwnProperty(propertyName)

hasOwnProperty 方法接收一个字符串参数，该参数表示属性名称，用来判断该属性是否在当前对象实例中，而不是在对象的原型链中。直白点儿说就是检测当前对象有没有某个属性，返回一个布尔值

```
var arr = [];
console.log(arr.hasOwnProperty("length")); // true
console.log(arr.hasOwnProperty("hasOwnProperty")); // false
```

2.isPrototypeOf(Object)

isPrototype 方法接收一个对象，用来判断当前对象是否在传入的参数对象的原型链上，返回一个布尔值

这个检测的就是 Object 的原型对象是否在 obj 的原型链上，MyObject 是继承自 Object 对象的，而在 JS 中，继承是通过 prototype 来实现的，所以 Object 的 prototype 必定在 MyObject 对象实例的原型链上。所以结果是肯定的

```
function MyObject() {}
var obj = new MyObject();
console.log(Object.prototype.isPrototypeOf(obj)); // true
```

3.propertyIsEnumerable(prototypeName)
prototypeIsEnumerable 用来判断给定的属性是否可以被 for..in 语句给枚举出来(个人感觉可以理解为是否可以被 for..in 语句遍历到)出来，返回一个布尔值

```
var obj = {
  name: "objName"
}
for (var i in obj) {
  console.log(i); // name
}
console.log(obj.propertyIsEnumerable("constructor")); // false
```

执行这段代码输出字符串“name”，这就说明通过 for…in 语句可以得到 obj 的 name 这个属性，但是，obj 的属性还有很多，比如 constructor，比如 hasOwnPrototype 等等，它们并没有被输出，说明这些属性不能被 for…in 给枚举出来，可以通过 propertyIsEnumerable 方法来得到。

4.toLocaleString()

toLocalString 方法返回对象的字符串表示，和代码的执行环境有关。

```
var obj = {};
console.log(obj.toLocaleString()); // [object Object]

var date = new Date();
console.log(date.toLocaleString()); // 2020/4/26 上午11:04:50 输出的就是本地的时间
```

5.toString()
toString 用来返回对象的字符串表示

```
var obj = {};
console.log(obj.toString()); // [object Object]

var date = new Date();
console.log(date.toString()); // Sun Apr 26 2020 11:04:35 GMT+0800 (中国标准时间)
```

6.valueOf()
valueOf 方法返回对象的原始值，根据对象的不同，返回的可能是字符串、数值或 boolean 值等

```
var obj = {
  name: "obj"
};
console.log(obj.valueOf()); // Object {name: "obj"}

var arr = [1];
console.log(arr.valueOf()); // [1]

var date = new Date();
console.log(date.valueOf()); // 1456638436303
```

js 在 Object 常用属性
1.prototype(原型对象)
构造函数有一个 prototype 属性，指向实例对象的原型对象。通过同一个构造函数实例化的多个对象具有相同的原型对象。(经常使用原型对象来实现继承)

```
function Foo() {};
Foo.prototype.a = 1;
var f1 = new Foo;
var f2 = new Foo;

console.log(Foo.prototype.a); // 1
console.log(f1.a); // 1
console.log(f2.a); // 1
```

2.constructor
原型对象有一个 constructor 属性，指向该原型对象对应的构造函数

```
function Foo() {};
console.log(Foo.prototype.constructor === Foo); // true
```

3.\_\_proto\_\_
这个东西并不是标准的原型，它是一些浏览器提供的一个查看 prototype 的接口，多用于调试，prototype 才是标准原型，可用在代码中。

```
function Foo(){};
var f1 = new Foo;
console.log(f1.__proto__ === Foo.prototype); // true
```

#### 如何设计一个模块加载器

#### 事件委托机制

事件委托就是利用冒泡的原理，把事件加到父元素或祖先元素上，触发执行效果。
1、提高 JavaScript 性能。事件委托可以显著的提高事件的处理速度，减少内存的占用
2、 动态绑定事件

```
<ul id="list">
  <li>item 1</li>
  <li>item 2</li>
  <li>item 3</li>
  ......
  <li>item n</li>
</ul>

// 给父层元素绑定事件
document.getElementById('list').addEventListener('click', function (e) {
  // 兼容性处理
  var event = e || window.event;
  var target = event.target || event.srcElement;

  // 判断是否匹配目标元素
  if (target.nodeName.toLocaleLowerCase === 'li') {
    console.log(target.innerHTML);
  }
});
// target 元素则是在 #list 元素之下具体被点击的元素，然后通过判断 target 的一些属性（比如：nodeName，id 等等）

```

局限性

1、focus、blur 之类的事件本身没有事件冒泡机制，所以无法委托

2、mousemove、mouseout 虽然有事件冒泡，但是只能不断通过位置去计算定位，对性能消耗高，也不适合于事件委托

#### 事件冒泡，阻止冒泡事件，阻止默认事件

首先说明什么是事件冒泡？当事件发生后，这个事件就要开始传播（从里到外，或者从外向里）。为什么要传播呢？因为事件源本身（可能）并没有处理事件的能力，或处理事件的函数并未绑定在该事件源上

阻止冒泡事件

```
function bubbles(e){
  var ev = e || window.event;
  if(ev && ev.stopPropagation) {
    //非IE浏览器
    ev.stopPropagation();
  } else {
    //IE浏览器(IE11以下)
    ev.cancelBubble = true;
  }
  console.log("最底层盒子被点击了")
}
```

阻止默认事件

```
// 谷歌及IE8以上
e.preventDefault();

// IE8及以下
window.event.returnValue = false;

return false;
```

#### ES6 异步请求方法 promise/async await/generator 等

类似于将一个异步方法封装在一个具有回调函数的函数里，Promise 实际上充当了这种封装作用。然后通过 resolve 和 reject 函数向外输出成功时的数据和失败时的错误信息

```
const p = new Promise((resolve, reject) => {
  setTimeout(function(){
    const name = 'joyitsai';
    resolve(name);
  /*
  如果失败reject输出错误信息
    reject(`error_info`)
  */
  }, 1000);
});
p.then((data) => {
  console.log(data);
})
```

Promise，把它想象成一个容器，里面放着一些正在处理的问题，但不管过多长时间，它最终都会把它处理完成的结果(不管是成功还是失败的)输出给你，而且，它允许你通过.then((data)=>{})方法来接收这个结果数据。Promise 可以说是为处理异步而生.

1.关于 async

它是将一个方法或函数变成异步的，它会将一个普通函数封装成一个 Promise，为什么要封装成 Promise 呢？其实它只是 Promise 的搬运工，利用了 Promise 处理异步问题的能力，能让你更好地解决需要异步处理的代码

```
/**async/await 与Promise */
async function testAsync(){
    return 'Here is Async';
}
const result = testAsync();
console.log(result); // 输出 Promise { 'Here is Async' }
```

2.关于 await

async 用来申明里面包裹的内容可以进行同步的方式执行，await 则是进行执行顺序控制，每次执行一个 await，程序都会暂停等待 await 返回值，然后再执行之后的 await。

await 只能用在 async 函数之中，用在普通函数中会报错

await 命令后面的 Promise 对象，运行结果可能是 rejected，所以最好把 await 命令放在 try...catch 代码块中

await 具有阻塞功能，可以理解为：等待 await 语句执行完成后，后面的程序才会继续。这就意味着，虽然一段包含 await 语句的异步程序，最终却是按照同步的顺序来执行

```
async function test(){
    console.log(2);
    return 'Joyitsai';
}

async function run(){
    console.log(1);
    const data = await test();
    console.log(data);
    console.log(3);
}
run();

// 打印结果
1
2
Joyitsai
3
```

3.ES6 中的 Generator 函数

Generator 函数是 ES6 提供的一种异步编程解决方案，语法行为与传统函数完全不同。从语法上，首先可以把它理解成，Generator 函数是一个状态机，封装了多个内部状态

形式上，Generator 函数是一个普通函数，但是有两个特征：1.function 关键字与函数名之间有一个星号 2.函数体内部使用 yield 语句，定义不同的内部状态

```
var arr = [1, [[2, 3], 4], [5, 6]];

var flat = function* (a) {
  var length = a.length;
  for (var i = 0; i < length; i++) {
    var item = a[i];
    if (typeof item !== 'number') {
      yield* flat(item);
    } else {
      yield item;
    }
  }
};

for (var f of flat(arr)) {
  console.log(f);
}
// 1, 2, 3, 4, 5, 6
```

#### var let 和 const 区别

1. 块级作用域
   ES5 只有全局作用域和函数作用域，没有块级作用域。
   这带来很多不合理的场景: 1).内层变量可能覆盖外层变量, 2).用来计数的循环变量泄露为全局变量

```
var tmp = new Date();
function f() {
  console.log(tmp); // 想打印外层的时间作用域
  if (false) {
    var tmp = 'hello world'; // 这里声明的作用域为整个函数
  }
}
f(); // undefined
```

2. 不存在变量提升
   变量提升的现象：在同一作用域下，变量可以在声明之前使用，值为 undefined
   ES5 时使用 var 声明变量，经常会出现变量提升的现象。

```
// var 的情况
console.log(foo); // 输出undefined
var foo = 8;

// let 的情况
console.log(bar); // 报错ReferenceError
let bar = 8;
```

3. 暂时性死区
   只要一进入当前作用域，所要使用的变量就已经存在了，但是不可获取，只有等到声明变量的那一行代码出现，才可以获取和使用该变量

```
var tmp = 888;
if (true) {
  tmp = '999'; // 报错 因为本作用域内有tmp声明变量
  let tmp; // 绑定if这个块级的作用域不能出现tmp变量
}
```

暂时性死区和不能变量提升的意义在于: 为了减少运行时错误，防止在变量声明前就使用这个变量，从而导致意料之外的行为。

4. 不允许重复声明变量
   let、const 不允许在相同作用域内，重复声明同一个变量
5. let、const 声明的  全局变量不会挂在顶层对象下面
   var 声明的全局变量会挂在顶层对象下面，而 let、const 不会挂在顶层对象下面。如下面这个栗子

```
var a = 8;
// 如果在 Node环境，可以写成 global.a
// 或者采用通用方法，写成 this.a
window.a // 8

let b = 8;
window.b // undefined
```

#### 什么是作用域链 作用域 闭包?

作用域
先来谈谈变量的作用域
变量的作用域无非就是两种：全局变量和局部变量。
全局作用域：
最外层函数定义的变量拥有全局作用域，即对任何内部函数来说，都是可以访问的：

```
<script>
  var outerVar = "hello";
  function fn(){
    console.log(outerVar);
  }
  fn(); // hello
</script>
```

局部作用域：
和全局作用域相反，局部作用域一般只在固定的代码片段内可访问到，而对于函数外部是无法访问的，最常见的例如函数内部

```
<script>
  function fn(){
    var innerVar = "inner";
  }
  fn();
  console.log(innerVar); // ReferenceError: innerVar is not defined
</script>
```

#### 箭头函数的概念和普通函数的区别?

箭头函数的 this 指向规则：

1. 箭头函数没有 prototype(原型)，所以箭头函数本身没有 this

```
let a = () =>{};
console.log(a.prototype); // undefined
```

2. 箭头函数的 this 指向在定义的时候继承自外层第一个普通函数的 this。
3. 不能直接修改箭头函数的 this 指向
4. 箭头函数外层没有普通函数，严格模式和非严格模式下它的 this 都会指向 window(全局对象)

```
const obj = {
  array: [1, 2, 3],
  sum: () => {
    // 外层没有普通函数this会指向全局对象
    return this.array.push('全局对象下没有array，这里会报错'); // 找不到push方法
  }
};
obj.sum();

// 修改后 这两种写法是等价的
sum() {
  return this.array.push('this指向obj');
}
sum: function() {
  return this.array.push('this指向obj');
}
```

5. 使用 new 调用箭头函数会报错
   无论箭头函数的 thsi 指向哪里，使用 new 调用箭头函数都会报错，因为箭头函数没有 constructor

```
let a = () => {};
let b = new  a(); // a is not a constructor
```

#### apply、call、bind 改变 this call 和 apply 与箭头函数有什么区别?(某站)

#### js 的 toFixed 方法

toFixed(num) 方法可把 Number 四舍五入为指定小数位数的数字
num 必需。规定小数的位数，是 0 ~ 20 之间的值，包括 0 和 20
返回值：字符串 string

#### js 的 map 方法

map 方法返回一个新数组，数组中的元素为原始数组元素调用函数处理后的值

方法按照原始数组元素顺序依次处理元素

不会对空数组进行检测,不会改变原始数组,兼容 ie9+

#### 油漆桶算法

#### 最长公共子串怎么实现

#### null 是什么类型 typeof null

#### 说说哈希算法

#### js 中，不使用 es6 的 promise 异步方法，怎么进行异步请求？

#### ES6 都有哪些新的 api，每一个都详细谈谈

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
