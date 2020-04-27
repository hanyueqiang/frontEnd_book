## js 基础篇

#### 数组处理函数有哪些？ 创建数组新引用有哪些？(某团)

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

#### es6 let const 区别？(某团)

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
```

#### js 性能优化有哪些？(某团)

整理中...

#### Object 上有哪些常用的函数？(某团)

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

#### 如何设计一个模块加载器(某团)

#### 事件委托机制(某站)

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

#### 事件冒泡，阻止冒泡事件，阻止默认事件(某站)

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

#### ES6 异步请求方法 promise/async await/generator 等(某站)

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

#### var let 和 const 区别， 块级作用域(某站)

#### 什么是作用域链 作用域 闭包?(某站)

#### 箭头函数的概念和普通函数的区别?(某站)

#### apply、call、bind 改变 this call 和 apply 与箭头函数有什么区别?(某站)

#### Math 的方法 Math.max Math.min 向上向下取整 Math.ceil Math.floor Math.round(某站)

#### js 的 toFixed 方法(某站)

#### js 的 map 方法(某站)

#### canvas 了解吗 webgl(某站)

#### 油漆桶算法(某站)

#### 最长公共子串怎么实现(某站)

#### null 是什么类型 typeof null(某站)

#### 说说哈希算法(某站)
