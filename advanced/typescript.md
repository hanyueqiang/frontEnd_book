## 强类型与弱类型

描述下为什么 js 被称为弱类型语言

强类型不允许隐式类型转换, 但`js`允许隐式类型转换，例如`a+b`,`a`和`b`是数字类型会加算，如果有一个是字符串类型会进行字符串拼接，其中的数字类型会先`toString()`转为字符串

## 静态类型与动态类型

描述下为什么 js 在类型检查中是动态类型

`js`作为动态类型语言，是因为否允许随时修改变量类型，例如`var a = 100; a = 'foo'`,`a`的值先是数字类型，后面也可以以赋值为字符串类型

## 弱类型语问题

```js
function sum(a, b) {
  return a + b;
}
sum(100, 100); // 200
sum(100, "100"); // '100100'

let obj = {};
obj[true] = 100;
console.log(obj["true"]);
```

## 强类型优势

1.错误更早暴露 2.代码更加智能，编码更准确 3.重构更可靠 4.减少不必要类型判断

```js
function render(element) {
  return elements.inner; // 无法判断element是否含有属性inner
}
```

## Flow 静态类型检查器

小工具
工作原理：进行标注
功能: 类型注解

```js
function sum(a: number, b: number) {
  return a + b;
}
```

## 安装 Flow

```js
yarn add flow-bin --dev

yarn flow init

// 运行
yarn flow

// @flow
function sum(a: number, b: number) {
  return a + b;
}

```

## 移除注解

1.使用 flow-remove-types 插件
2.babel/core babel/preset-flow 编译移除，建议 babel

## 枚举类型

给一组数值定义
一个枚举固定的值

```js
const postStatus = {
  Draft: 0,
  pushlish: 1
}

enum postStatus {
  draft,
  pushlish
}

双向键值对 对象
postStatus[postStatus['draft']="Draft"] = 'Draft'；

建议使用const 定义枚举 编译后会被移除
```

## 元祖类型

```js
const tuple: [number, string] = [18, "zed"];
```

## 泛型

```js
function create<T>(length: number, value: T): T[] {
  const arr = Array < T > length.fill(value);
  return arr;
}
const res = create < string > (3, "foo");
```
