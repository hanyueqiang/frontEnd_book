## TS 干货分享

最近的项目中一直使用`TypeScript`开发,从最初的`anyTS`逐渐开始改进，到真正应用`TS`, 逐渐感受到`TS`为项目带来太多的好处，个人认为上手`TS`还是需要花费一些时间成本来学习的，不过强大的`any`在你解决不了一个类型约束时，可以帮你一程，但是使用`any`失去类型的约束，也抛弃了引入`TS`的初衷，所以在工作中边学习边总结，不断加强自己对`TS`的理解和使用

## 为什么 js 被称为弱类型语言

强类型不允许隐式类型转换, 但`js`允许隐式类型转换，例如`a+b`,`a`和`b`是数字类型会相加，如果有一个是字符串类型会进行字符串拼接，其中的数字类型会先`toString()`转为字符串

## 为什么 js 在类型检查中是动态类型

`js`作为动态类型语言，是因为否允许随时修改变量类型，例如`var a = 100; a = 'ym'`,`a`的值先是数字类型，后面也可以以赋值为字符串类型

## 弱类型引起问题

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

- 错误更早暴露
- 代码更加智能，编码更准确
- 重构更可靠
- 减少不必要类型判断

```js
// ym是啥？我哪知道
function render(element) {
  return elements.ym;
}

// ym是一个字符串，good
interface eleProps {
  ym: string;
}
function renderTs(element: eleProps) {
  return elements.ym;
}
```

## TypeScript 概述

TypeScript 是一种由微软开发的自由和开源的编程语言。它是 JavaScript 的一个超集，而且本质上向这个语言添加了可选的静态类型和基于类的面向对象编程
功能强大,生态健全

## 安装

```js
yarn init --yes
yarn add typescript --dev

```

## 配置文件

tsconfig 配置项网上有详细配置说明，不做搬运

```js
yarn tsc --init

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

枚举类型会入侵代码，最终会编译一个双向键值对对象，-面试常问

```js
// 给一组数值定义，一个枚举固定的值
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

// 建议使用const 定义枚举 编译后会被移除
```

## 函数类型约束

```js
// 可选参数放在最后
function func1(a: number, b?: number, ...rest: number[]): string {
  return "fun1";
}
```

## 任意类型 any

在 TypeScript 中，任何类型都可以被归为 any 类型。这让 any 类型成为了类型系统的顶级类型（也被称作全局超级类型）

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
foo = "str"; // 类型错误
```

## 类型断言

类型断言好比其他语言里的类型转换，但是不进行特殊的数据检查和解构。它没有运行时的影响，只是在编译阶段起作用。

```js
// as 语法
const nums = [100, 2, 34, 44];
const res = nums.find(i => i > 0);

const num1 = res as number;
// “尖括号” 语法
const num2 = <number>res  // 注意：在jsx报错，不能使用,React当作标签
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

## 接口与类型别名的区别

- 接口和类型别名都可以用来描述对象的形状或函数签名

```js
interface Ym {
  x: number;
  y: number;
}

interface SetYm {
  (x: number, y: number): void;
}

type Ym = {
  x: number;
  y: number;
}

type SetYm = (x: number, y: number): void;

```

- 与接口类型不一样，类型别名可以用于一些其他类型，比如原始类型、联合类型和元组

```js
// primitive
type Ym = string;

// union
type UnionPoint = PartialPointX | PartialPointY;

// tuple
type Data = [number, string];
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
// 使用泛型定义一个函数
function create<T>(length: number, value: T): T[] {
  const arr = Array < T > （length）.fill(value);
  return arr;
}

const res = create <string> (3, "foo");
```

## 处理 Event 对象

使用 Event 对象，change 事件可以使用 `React.ChangeEvent`, click 事件可以使用 `React.MouseEvent`

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
