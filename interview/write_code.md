## 手撕代码篇

#### 考察 new 和闭包

```js
// 不使用全局变量前提下实现如下逻辑
let a = new Foo(); //a.id -> 1
let b = new Foo(); //b.id -> 2

// 使用闭包
const Foo = (function() {
  let index = 1;
  return function() {
    this.id = index++;
  };
})();

let a = new Foo(); // a.id -> 1
let b = new Foo(); // b.id -> 2

// 增加难度,考察`new`和直接调用的区别
let a = Foo(); // a.id -> 1
let b = new Foo(); // b.id -> 2
let c = new Foo(); // c.id -> 3
let d = Foo(); // d.id -> 4

// 分析
// d就是函数Foo()执行的返回值，没有返回值也就是undefined. 在函数执行过程中，属性被加到全局作用域或者Foo方法所属的对象上
// 考察对new的理解，new会改变this指向，指向实例化的实例，那我们就可以使用instanceof判断了

const Foo = (function() {
  let index = 1;
  return function() {
    if (this instanceof Foo) {
      // 使用new
      this.id = index++;
    } else {
      // 没有使用new, 直接返回一个对象
      return {
        id: index++,
      };
    }
  };
})();

let a = Foo(); // a.id -> 1
let b = new Foo(); // b.id -> 2
let c = new Foo(); // c.id -> 3
let d = Foo(); // d.id -> 4
```

#### 写一个正则匹配字符转成驼峰？

实现: border-bottom-color 》 borderBottomColor

```js
let str = "border-bottom-color";

// 使用正则
// 通过正则找到-b  -c。默认的是匹配一次，所以要用g来全局匹配。
// \w指的字符, $0代表正则，$1代表指向, replace替换就是B替换-b

function toCaml(str) {
  let reg = /-(\w)/g;
  return str.replace(reg, function($0, $1) {
    return $1.toUpperCase();
  });
}

console.log(toCaml(str));

// for循环
// 用split()函数来进行分割字符串arr里面包括
function test(str) {
  var arr = str.split("-");
  // 从数组的第二项开始循环，charAt(0)找到第一个字母。substring(1)截掉第一个字母
  for (let i = 1; i < arr.length; i++) {
    arr[i] = arr[i].charAt(0).toUpperCase() + arr[i].substring(1);
  }
  return arr.join("");
}

console.log(test(str));
```

扩展：驼峰转连字符

```js
let str = "strArrTest";
function test(str) {
  let str1 = str.replace(/([A-Z])/g, function($1) {
    return "-" + $1.toLocaleLowerCase();
  });
  return str1;
}

console.log(test(str));
```

#### 实现一个 ajax 方法

```js
function ajax(url) {
  return new Promise(function(resolve, reject) {
    var xhr = new XMLHttpRequest();
    xhr.open("GET", url);
    xhr.responseType = "json";
    xhr.onload = function() {
      if (this.status === 200) {
        resolve(this.response);
      } else {
        reject(new Error(this.statusText));
      }
    };
  });
}
```

#### call、apply、bind 区别

- 共同点：

均改变函数执行时的上下文，再具体一点就是改变函数运行时的 this 指向

- 不同：

call 和 apply 改变了函数的 this 上下文后便执行该函数, 而 bind 则是返回改变了上下文后的一个函数

call、apply 的区别：
call 和 aplly 的第一个参数都是要改变上下文的对象，而 call 从第二个参数开始以参数列表的形式展现，apply 则是把除了改变上下文对象的参数放在一个数组里面作为它的第二个参数。

#### 手写 call

```js
Function.prototype.newCall = function(context) {
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
Function.prototype.newApply = function(context, args) {
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

符合 A+规范

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

myPromise.prototype.then = function(onFullfilled, onRejected) {
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

Promise.prototype.all = function(promiseArr) {
  let resArr = [];
  let count = 0;
  let len = promiseArr.length;
  // 返回一个新的promise实例
  return new Promise(function(resolve, reject) {
    for (let promise of promiseArr) {
      Promise.resolve(promise).then(
        function(res) {
          resArr[count] = res;
          count++;
          if (count === len) {
            return resolve(resArr);
          }
        },
        function(err) {
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
Promise.prototype.race = function(promiseArr) {
  return new Promise(function(resolve, reject) {
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

## 算法，合并两个有序链表

```js
/**
 * @param {ListNode} l1
 * @param {ListNode} l2
 * @return {ListNode}
 */
var mergeTwoLists = function(l1, l2) {
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
var isPalindrome = function(s) {
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

## 手写防抖和节流

防抖 debounce
非立即执行:，如果你在一个事件触发的 n 秒内又触发了这个事件，那我就以新的事件的时间为准，n 秒后才执行
立即执行：我不希望非要等到事件停止触发后才执行，我希望立刻执行函数，然后等到停止触发 n 秒后，才可以重新触发执行

```js
function debounce(fn, wait = 500) {
  var timeout = null;
  return function() {
    if (timeout !== null) {
      clearTimeout(timeout);
    }
    timeout = setTimeout(fn, wait);
  };
}
```

节流 throttle
持续触发事件，每隔一段时间，只执行一次事件。

```js
function throttle(func, delay) {
  var prev = Date.now();
  return function() {
    var context = this;
    var args = arguments;
    var now = Date.now();
    if (now - prev >= delay) {
      func.apply(context, args);
      prev = Date.now();
    }
  };
}
```

## 实现浅拷贝、深拷贝

浅拷贝

```js
function copy(obj1) {
  var obj2 = {};
  for (var key in obj1) {
    obj2[key] = obj1[key];
  }
  return obj2;
}
```

深拷贝

```js
function deepClone(obj) {
  // 如果是值类型或 null，则直接return
  if (typeof obj !== "object" || obj === null) {
    return obj;
  }

  // 定义结果对象
  let copy = {};
  // 如果对象是数组，则定义结果数组
  if (obj.constructor === Array) {
    copy = [];
  }
  // 遍历对象的key
  for (let key in obj) {
    // 如果key是对象的自有属性
    if (obj.hasOwnProperty(key)) {
      // 递归调用深拷贝方法
      copy[key] = deepClone(obj[key]);
    }
  }
  return copy;
}
```

## 实现 cacheRequest 方法，保证使用 ajax 请求相同资源实际只发送一次请求

```js
// 构建Map，用作缓存数据
const dict = new Map();
// 这里简单的把url作为cacheKey
const cacheRquest = (url) => {
  if (dict.has(url)) {
    return Promise.resolve(dict.get(url));
  } else {
    // 无缓存，发起真实请求，成功后写入缓存
    return request(url)
      .then((res) => {
        dict.set(url, res);
        return res;
      })
      .catch((err) => Promise.reject(err));
  }
};
```

#### ajax 是什么？ 原理 步骤

(1)创建 XMLHttpRequest 对象,也就是创建一个异步调用对象.
(2)创建一个新的 HTTP 请求,并指定该 HTTP 请求的方法、URL 及验证信息.
(3)设置响应 HTTP 请求状态变化的函数.
(4)发送 HTTP 请求.
(5)获取异步调用返回的数据.
(6)使用 JavaScript 和 DOM 实现局部刷新.

```js
var XHR = new XMLHttpRequest();
XHR.open("get", "./a.php");
XHR.send(null);
XHR.onreadystatechange = function() {
  if (XHR.readyState === 4) {
    if (XHR.status === 200) {
      alert(XHR.responseText);
    }
  }
};
```

## 匹配任何空白字符

```js
function trim(str) {
  var reg = /^\s+|\s+$/g;

  str = str.replace(reg, "");
  console.log(str);
}
trim(" abc123   ");
```

```js
function randomName(len) {
  len = len || 23;

  var chars = "ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678";

  var charsLength = chars.length;

  var str = "";

  for (i = 0; i < len; i++) {
    str += chars.charAt(Math.floor(Math.random() * charsLength));
  }

  return new Date().getTime() + str;
}
```

## 合并两个有序数组

```js
// 双指针
function newArr(arr1, arr2) {
  let [i, j] = [0, 0];
  let newArr = [];
  while (i < arr1.length || j < arr2.length) {
    if (arr1[i] < arr2[j]) {
      newArr.push(arr1[i]);
      i++;
    } else if (arr1[i] > arr2[j]) {
      newArr.push(arr2[j]);
      j++;
    } else {
      if (arr1[i]) newArr.push(arr1[i]);
      if (arr2[j]) newArr.push(arr2[j]);
      i++;
      j++;
    }
  }
  return newArr;
}
```

#### 写一个计数器

```js
var count = (function() {
  var counter = 0;
  return function() {
    return ++counter;
  };
})();
```
