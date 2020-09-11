## 强类型与弱类型

描述下为什么 js 被称为弱类型语言

强类型不允许隐式类型转换, 但`js`允许隐式类型转换，例如`a+b`,`a`和`b`是数字类型会加算，如果有一个是字符串类型会进行字符串拼接，其中的数字类型会先`toString()`转为字符串

## 静态类型与动态类型

js 动态类型语言，

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
console.log(obj["true"]); // 100
```

## 强类型优势

1.错误更早暴露 2.代码更加智能，编码更准确 3.重构更可靠 4.减少不必要类型判断

```js
// ym是什么未知
function render(element) {
  return elements.ym;
}
```

## TypeScript 概述

`javaScript` 超集，`es6`+类型系统=》编译为 `JavaScript`

功能强大。生态健全
`vue3.0`
`Angular`
`TypeScript`-前端领域对第二语言
缺点：本身很多概念
缺点二：项目初期，`ts` 会增加一些成本

## 基本使用

```js
yarn init --yes
yarn add typescript --dev

const hello = name => {
  console.log(name)
}
hello('typescript');

// 编译
yarn tsc a.ts
```

## 配置文件

```js
yarn tsc --init

// tsconfig.json
// target: 'es5', // 把多有对新特性转换为es5
// output: 'dist'
// sourceMap: true
// strict 开启多有严格检测
```

## Object 类型

并不单只对象，包括数组/函数

```js
const foo: object = function() {};
const obj: { foo: number, bar: string } = { foo: 123, bar: "ym" };
```

## 数组类型

const arr1: Array<number> = [1,2,3];
const arr2: number[] = [2,3,4];

## 元组类型

`hooks` 中 `useState` 是元组类型

```js
const tuple: [number, string] = [18, "zed"];
```

## 枚举类型

枚举类型会入侵代码，最终会编译一个双向键值对对象
给一组数值定义，一个枚举固定的值

```js
const postStatus = {
  Draft: 0,
  pushlish: 1
}

enum postStatus {
  draft,
  pushlish
}

// 建议使用常量枚举
const enum postStatus {
  draft,
  pushlish
}

// 双向键值对对象
postStatus[postStatus['draft']="Draft"] = 'Draft'；

建议使用const 定义枚举 编译后会被移除
```

## 函数类型约束

可选参数放在最后

```js
function func1(a: number, b?: number, ...rest: number[]): string {
  return "fun1";
}
```

## 任意类型 any

`stringify` 或兼容老的代码

```js
function stringify(value: any) {
  return JSON.stringify(value);
}
```

## 隐式类型推断

```js
let age = 18;
age = "str"; // 类型错误

let foo;
foo = 100;
foo = "str";
```

## 类型断言

```js
const nums = [100, 2, 34, 44];
const res = nums.find(i => i > 0);

const num1 = res as number;

const num2 = <number>res  // jsx报错 不能使用 当作标签
```

## 接口

约束对象的结构/约定成员

```js
interface Post {
  readonly summary: string // 只读成员
  title: string;
  content: string;
  name?: string
}

// 设置缓存
interface cache {
  [prop: string]: string
}

const cache: cahe {};
cache.foo = 'value';
cache.bar = 'value2';

function post(post: Post) {
  console.log(post.title);
  console.log(post.content);
}
```

## 类

描述具体事物的抽象特征、类的属性在使用前必须声明

```js
class Person {
  public name: string; // = 'init name'
  private age: number; // 私有属性
  protected gender: boolean // 区别 只允许在子类中访问成员
  readonly sex: string // 只读属性
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
  sayHi(msg: string): void {
    console.log(this.name, msg);
  }
  static create(name: string, age: number) {
    return new Person(name, age)
  }
}

class Students = new Person();
const tom = new Person('tom', 19);
```

## 接口与类

```js
interface Eat {
  eat(food: string): void;
}
interface Run {
  run(distance: number): void;
}

class Person implements Eat, Run {
  eat(food: string): void {
    console.log(food);
  }
  run(distance: number) {
    console.log(distance);
  }
}
```

## 抽象类

使用 `abstract` 不能被 `new` 只能被继承

```js
abstract class Animal {
  eat(food: string): void {
    console.log(food);
  }
  abstract run(distance: number): void
}

class Dog extends Animal {
  run(distance: number): void {
    console.log(distance);
  }
}
const d = new Dog();
d.eat('dfd');
d.run();

```

## 泛型

```js
function create<T>(length: number, value: T): T[] {
  const arr = Array < T > （length）.fill(value);
  return arr;
}
const create = <T>(length: number, value: T): T[] => {
  const arr = Array <T>(length).fill(value);
  return arr;
}
const res = create < string > (3, "foo");
```

## 类型声明

```js
// lodash
import { camelCase } from 'lodash';

declare function camelCase(input: string): void

yarn add @types/lodash --dev
```

## 处理 Event 对象

在工作中，可能经常会使用 Event 对象，change 事件可以使用 `React.ChangeEvent`, click 事件可以使用 `React.MouseEvent`

```js
onClick = (e: React.MouseEvent<HTMLButtonElement>) => {
  // do something
};

onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  // do something
};
```

## 获取 dom 对象

`document.getElementById('root') as HTMLElement`
