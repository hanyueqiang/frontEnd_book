## TS 干货分享

> `TypeScript` 是一种由微软开发的自由和开源的编程语言。它是 `JavaScript` 的一个超集，而且本质上向这个语言添加了可选的静态类型和基于类的面向对象编程

社区内或网上关于 `TS` 的文章大部分是对官方文档的解读，从头到尾介绍一遍 `TS` 的各种类型，这种对于已经有 `TS` 经验的开发者没有任何意义，对于初学者我建议阅读官方文档足够了，大部分读者包括我都在寻找能解决实际项目中遇到的问题文章，达到学以致用效果。

最近一年开始使用`TypeScript`开发项目，从最初的`anyTS`，到真正应用`TS`, 逐渐感受到`TS`为项目带来太多的好处，个人认为完全掌握`TS`还是需要花费时间成本学习，不过强大的`any`在你解决不了的问题时可以帮你一把，但是使用`any`失去类型的约束，也抛弃了引入`TS`的初衷，所以在项目实战中遇到的问题总结起来供大家阅读参考，能让读者从中得到帮助是本文分享的初衷！

## 为什么 js 被称为弱类型语言

强类型不允许隐式类型转换, 但`js`允许隐式类型转换，例如`a+b`,`a`和`b`是数字类型会相加，如果有一个是字符串类型会进行字符串拼接，其中的数字类型会先`toString()`转为字符串

## 为什么 js 在类型检查中是动态类型

`js`作为动态类型语言，是因为否允许随时修改变量类型，例如`var a = 100; a = 'ym'`,`a`的值先是数字类型，后面也可以以赋值为字符串类型

## 弱类型引起问题

```js
function sum(a, b) {
  return a + b;
}
sum(10, 10); // 20
sum(10, "10"); // '1010'

let obj = {};
obj[true] = 100;
console.log(obj["true"]); // 100， 对象字面量会对key经行.toString()
```

## 强类型优势

- 错误更早暴露
- 代码更加智能，编码更准确
- 重构更可靠
- 减少不必要类型判断

举例：

```js
// ym是啥？我哪知道
function render(element) {
  return elements.ym;
}

// ym是一个字符串，good
interface eleProps {
  ym: string;
}
function render1(element: eleProps) {
  return elements.ym;
}
```

## 枚举类型

有必要说下枚举类型，因为枚举类型会入侵代码，大部分面试官喜欢问枚举类型编译成 js 是什么样,

枚举类型会入侵代码，最终会编译成一个<strong>双向键值对对象</strong>，但是使用`const` 定义枚举 编译后会被移除

```js
enum Status {
  a,
  b,
}
console.log(Status.a);

// 编译成es5 js
var Status;
(function (Status) {
    Status[Status["a"] = 0] = "a";
    Status[Status["b"] = 1] = "b";
})(Status || (Status = {}));
console.log(Status.a);

enum Status {
  a = "ym",
  b = "yn",
}
console.log(Status.a);

// 编译成es5 js文件
var Status;
(function (Status) {
    Status["a"] = "ym";
    Status["b"] = "yn";
})(Status || (Status = {}));
console.log(Status.a);

// 建议使用常量枚举
const enum Status {
  a = "ym",
  b = "yn",
}
console.log(Status.a);

// 编译成es5 js文件 建议使用const 定义枚举 编译后会被移除
console.log("ym" /* a */);

```

## 函数类型约束

注意可选参数放在最后

```js
function func1(a: number, b?: number, ...rest: number[]): string {
  return "fun1";
}
```

## 任意类型 any

在个别场合也需要`any`出场，比如`stringify`

```js
function stringify(value: any) {
  return JSON.stringify(value);
}
// 也可以这样
function stringify<T>(value: T): string {
  return JSON.stringify(value);
}
```

## 类型断言

类型断言好比其他语言里的类型转换，但是不进行特殊的数据检查和解构。它没有运行时的影响，只是在编译阶段起作用。

在开发中我们定义`dom`时要使用

```js
// as 语法
const [callName, setCallName] = useState('ym' as string);

// 获取 dom 对象
const rootDom = document.getElementById('root') as HTMLElement;

// 尖括号语法
const [callName, setCallName] = useState<string>('ym');

const res = 1;
// 注意：在React报错，React认为是标签，需使用as语法
const num = <number>res;
```

## 定义对象字面量

遇到设置缓存，需创建一个对象字面量，存储我们需要缓存的属性值

```js
// 设置缓存
interface CacheProps {
  [prop: string]: string;
}

const cache: CacheProps = {};
cache.foo = "value";
cache.bar = "value2";
```

## interface vs type

`interface`和`type`是一对好兄弟，有共同点也有区别
建议能用`interface`定义的使用`interface`，定义不了的使用`type`

- 共同点

两者都可以用来描述对象或函数的类型

```js
// interface
interface Point {
  x: number;
  y: number;
}
interface PointHandle {
  (x: number, y: number): void;
}

// type
type Point = {
  x: number,
  y: number,
};
type PointHandle = (x: number, y: number) => void;
```

均可以使用`extends`扩展属性

```js
interface User {
  name: string;
}
interface UserProps extends User {
  userId: number;
}

type User = { name: string };
type UserProps = User & { userId: number };
```

- 不同点

类型别名还可以用于其他类型，如基本类型（原始值）、联合类型、元组

```js
// 基本类型
type State = string;
// 函数
type StateFun = () => string;
// 联合类型
type UnionState = State | StateFun;
// 元祖
type Data = [number, string];
```

type 能使用 in 关键字生成映射类型。interface 不可以

```js
type Keys = "ym" | "yn"

type NameType = {
  [key in Keys]: string
}

const demo: NameType = {
  ym: "name1",
  yn: "name2"
}
```

## 类的使用

描述具体事物的抽象特征、类的属性在使用前必须声明

```js
class Person {
  public name: string;
  // 私有属性
  private age: number;
  // 与private区别 只允许在子类中访问成员
  protected gender: boolean;
  // 只读属性
  readonly sex: string;
  constructor(name: string, age: number) {
    this.name = name;
    this.age = age;
  }
  say(msg: string): void {
    console.log(this.name, msg);
  }
  static create(name: string, age: number) {
    return new Person(name, age)
  }
}

class Students = new Person();
const ym = new Person('月陌', 20);
```

## 接口与类

为增强接口的复用性，把可公共方法定义抽出

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
  abstract like(action: string): void
}

class Dog extends Animal {
  like(action: string): void {
    console.log(`${action} ${this.eat}`);
  }
}
const dog = new Dog();
dog.eat('狗粮');
dog.like('撒');

```

## 使用泛型定义函数

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
