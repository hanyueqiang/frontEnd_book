#### JavaScript 数据类型

空值（null) 、未定义(undefined) 、布尔值（boolean) 、数字（number) 、字符串（string) 、符号（symbol, ES6 中新增) 、大整数（BigInt, ES2020 引入）
注意：对象 (object)是 js 内置类型，非基本类型

#### instanceof 实现原理

用代码实现 如果 A 沿着原型链能找到 B.prototype 那么 A instanceof B 为 true
遍历 A 的原型链 找到 B.prototype 返回 true

```js
const instanceof = (A, B) => {
  let p = A;
  while (p) {
    if (p === B.prototype) {
      return true;
    }
    p = p.__proto__;
  }
  return false;
};
```

#### 手写 call

```js
Function.prototype.newCall = function (context) {
  context = Object(context) || window;
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
  context = Object(context) || window;
  context.fn = this;
  let result;
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
Function.prototype.newBind = function (context) {
  if (typeof this !== "function") {
    throw new Error("不是一个函数");
  }
  const self = this;
  const args1 = [...arguments].slice(1);
  return function () {
    // 函数科里化
    const args2 = [...arguments];
    self.apply(context, args1.concat(args2));
  };
};
```

#### new 实现原理

```js
function Animal(type) {
  this.type = type;
}
Animal.prototype.say = function () {
  console.log("say");
};
function mockNew(Constructor, ...args) {
  // let Constructor = arguments.shift(); // 取出构造函数
  let obj = {}; // new 执行会创建一个新对象
  obj.__proto__ = Constructor.prototype;
  Constructor.apply(obj, args);
  return obj;
}
let animal = mockNew(Animal, "dog");
console.log(animal.type); // dog
```

#### 手写 Promise 简版

符合 A+规范

```js
function myPromise(constructor) {
  let self = this;
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
  switch (this.status) {
    case "resolved":
      onFullfilled(this.resolveVal);
      break;
    case "rejected":
      onRejected(this.rejectedVal);
      break;
  }
};
```

#### 手写 Promise

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

#### 手写 promise.all

将多个 Promise 实例包装成一个 Promise 实例，接收包含 Promise 对象或普通值的数组(或其它可迭代对象)作为参数，成功时返回值为 Promise 实例数组对应的结果数组，失败时返回第一个 rejected 的 Promise 实例的结果。

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

#### 手写 apromise.race

与 Promise.all 一样，Promise.race 也接收包含 Promise 对象或普通值的数组(或其它可迭代对象)作为参数，返回一个 Promise 实例对象。与 Promise.all 不同的是，一旦有一个 Promise 实例对象 resolve ，立即把这个 resolve 的值作为 Promise.race resolve 的值。一旦有一个对象 reject， Promise.race 也会立即 reject。

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

#### 手写防抖

作用是在短时间内多次触发同一个函数，只执行最后一次，或者只在开始时执行。 1.非立即执行:，如果你在一个事件触发的 n 秒内又触发了这个事件，那我就以新的事件的时间为准，n 秒后才执行

```js
function debounce(fn, delay) {
  let timeout;
  return function () {
    let context = this;
    let args = arguments;
    clearTimeout(timeout);
    timeout = setTimeout(() => {
      fn.apply(context, args);
    }, delay);
  };
}
```

2.立即执行：我不希望非要等到事件停止触发后才执行，我希望立刻执行函数，然后等到停止触发 n 秒后，才可以重新触发执行, 我们也可以为 debounce 函数加一个参数，可以选择是否立即执行函数

```js
function debounce(fn, delay, immediate) {
  let timeout = null;
  return function () {
    let context = this;
    let args = arguments;
    if (timeout) {
      clearTimeout(timeout);
    }
    if (immediate) {
      const callNow = !timeout;
      timeout = setTimeout(() => {
        timeout = null;
      }, delay);

      if (callNow) {
        fn.apply(context, args);
      }
    } else {
      timeout = setTimeout(() => {
        fn.apply(context, args);
      }, delay);
    }
  };
}
```

#### 手写节流

持续触发事件，每隔一段时间，只执行一次事件。

```js
function throttle(func, delay) {
  var prev = Date.now();
  return function () {
    var context = this;
    var args = arguments;
    var now = Date.now();
    if (now - prev >= delay) {
      func.apply(context, args);
      prev = Date.now();
    }
  };
}

// 定时器实现
var throttle = function (func, delay) {
  let timer = null;
  return function () {
    const context = this;
    const args = arguments;
    if (!timer) {
      timer = setTimeout(function () {
        func.apply(context, args);
        timer = null;
      }, delay);
    }
  };
};
```

#### 手写浅拷贝

```js
function copy(obj) {
  if (typeof obj !== "object" || obj === null) {
    return obj;
  }
  let obj1 = {};
  if (obj.constructor === Array) {
    obj1 = [];
  }
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      obj1[key] = obj[key];
    }
  }
  return obj1;
}
```

## 手写深拷贝

```js
function deepClone(obj) {
  // 如果是值类型或 null，则直接return
  if (typeof obj !== "object" || obj === null) {
    return obj;
  }
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof RegExp) return new RegExp(obj);

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

function deepClone(obj, hash = new WeakMap()) {
  if (obj === null) return obj; // 如果是null或者undefined我就不进行拷贝操作
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof RegExp) return new RegExp(obj);
  // 可能是对象或者普通的值  如果是函数的话是不需要深拷贝
  if (typeof obj !== "object") return obj;
  // 是对象的话就要进行深拷贝
  if (hash.get(obj)) return hash.get(obj);
  let cloneObj = new obj.constructor();
  // 找到的是所属类原型上的constructor,而原型上的 constructor指向的是当前类本身
  hash.set(obj, cloneObj);
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      // 实现一个递归拷贝
      cloneObj[key] = deepClone(obj[key], hash);
    }
  }
  return cloneObj;
}
```

#### 手写 ajax

(1)创建 XMLHttpRequest 对象,也就是创建一个异步调用对象.
(2)创建一个新的 HTTP 请求,并指定该 HTTP 请求的方法、URL 及验证信息.
(3)设置响应 HTTP 请求状态变化的函数.
(4)发送 HTTP 请求.
(5)获取异步调用返回的数据.
(6)使用 JavaScript 和 DOM 实现局部刷新.

```js
// 步骤
var XHR = new XMLHttpRequest();
XHR.open("get", "./a.php");
XHR.send(null);
XHR.onreadystatechange = function () {
  if (XHR.readyState === 4) {
    if (XHR.status === 200) {
      alert(XHR.responseText);
    }
  }
};
// 封装
window.ajax = function (url, method, body, success, fail) {
  let request = new XMLHttpRequest();
  request.open(method, url);
  request.onreadystatechange = () => {
    if (request.readyState === 4) {
      if (request.status >= 200 && request.status < 300) {
        success.call(undefined, request.responseText);
      } else if (request.status >= 400) {
        fail.call(undefined, request);
      }
    }
  };
  request.send(body);
};

// promsie版ajax
window.ajax = function ({ url, method }) {
  // 传参是 es6解构赋值
  return new Promise(function (resolve, reject) {
    let request = new XMLHttpRequest();
    request.open(method, url);
    request.onreadystatechange = () => {
      if (request.readyState === 4) {
        if (request.status >= 200 && request.status < 300) {
          resolve.call(undefined, request.responseText);
        } else if (request.status >= 400) {
          reject.call(undefined, request);
        }
      }
    };
    request.send();
  });
};
```
