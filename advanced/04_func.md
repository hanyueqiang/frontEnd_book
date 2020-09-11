## 函数式编程

- 对面向过程的抽象，相同输入得到相同的输出
- 用来描述数据之间的映射
- 代码复用
- 函数是一等公民
- 高阶函数
- 闭包
- 函数就是一个普通的对象

## 高阶函数 Higher-order function

```js
function forEach(array, fn) {
  for (let i = 0; i < array.length; i++) {
    fn(array[i]);
  }
}

let arr = [1, 3, 4, 5, 8];

forEach(arr, function(item) {
  console.log(item);
});

function filter(array, fn) {
  let results = [];
  for (let i = 0; i < array.length; i++) {
    if (fn(array[i])) {
      results.push(array[i]);
    }
  }
  return results;
}

let arr = [1, 3, 4, 7, 8];

let r = filter(arr, function(item) {
  return item % 2 === 0;
});
```

## 函数作为返回值

生成一个函数

```js
functtion make() {
  return function() {
    console.log('ddd')
  }
}

// once
function once(fn) {
  let done = false;
  return function () {
    if(!done) {
      done = true;
      return fn.apply(this, arguments)
    }
  }
}

let pay = once(function(money) {
  console.log('money');
});

// map
const map = (array,fn) => {
  let result = [];
  for(let value of array) {
    result.push(fn(value));
  }
  return result;
}


// every
const every = (array, fn) => {
  let result = true;
  for(let value of array) {
    result = fn(value);
    if(!result) {
      break;
    }
  }
  return result;
}

// some
const some = (array,fn) {
  let result = false;
  for(let value of array) {
    result = fn(value);
    if(result) {
      break;
    }
  }
  return result;
}
```

## 闭包

```js
function makePow(power) {
  return function (number) {
    return Math.pow(number, power);
  };
}

// 求平方
let power2 = makePower(2);
let power3 = makePower(3);

console.log(power2(2));
```

## lodash

纯函数

```js
const _ = require("lodash");

const array = ["jack", "tom", "lucky"];
console.log(_.first(array));
console.log(_.reverse(array));

_.each(array, (item, index) => {
  console.log(item, index);
});

// 记忆函数
function getArea(r) {
  return Math.PI * r * r;
}
const getAreaMemory = _.memorize(getArea);

// 模拟memorize实现
function memorize(f) {
  let cache = {};
  return function () {
    let key = JSON.stringify(argumnets);
    cache[key] = cache[key] || f.apply(f, arguments);
    return cache[key];
  };
}
```

## 科里化

解决硬编码问题

```js
function checkAge(age) {
  let min = 18;
  return age >= min;
}

function checkage(min, age) {
  return age >= min;
}

// 科里化
function checkage(min) {
  return function (age) {
    return age >= min;
  };
}

let checkage = (min) => (age) => age >= min;
```

## lodash 科里化函数\_.curry

```js
const _ = require("lodash");

function getSum(a, b, c) {
  return a + b + c;
}

const curried = _.curry(getSum);

console.log(curried(1, 2, 3));
console.log(curried(1)(2, 3));
console.log(curried(1, 2)(3));

function match(reg, str) {
  return str.match(reg);
}

_.curry();
```
