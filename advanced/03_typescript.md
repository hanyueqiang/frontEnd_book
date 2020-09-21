## TypeScript 在 React 中使用总结

> `TypeScript` 是一种由微软开发的自由和开源的编程语言。它是 `JavaScript` 的一个超集，而且本质上向这个语言添加了可选的静态类型和基于类的面向对象编程

社区内或网上关于 `TS` 的文章大部分是对官方文档的解读，从基础类型开始介绍，这种对于已经有 `TS` 经验的开发者没有任何意义，对于刚上手同学建议阅读官方文档足够了，大部分同学包括我都希望看到解决实际项目中遇到的问题分享。

最近一年一直使用`TypeScript`开发项目，从最初的`anyTS`，到真正应用`TS`, 逐渐感受到`TS`为项目带来太多的好处。甚至在维护非 TS 的项目老感觉不自然，浑身乏力、头晕等症状，这其中多少跟项目有点关系，嘿嘿！

## 为什么要使用 TypeScript

当我们使用`js`玩的`happy`的时候，突然要使用`ts`了，我不时有点紧张，增加了学习成本不说、还要踩坑、挖坑。遵循<strong>他大舅，他二舅 都是他舅</strong>，我鼓起勇气在项目中大刀阔斧的搞起来了。
要回来这个问题首先要把下面的问题解决了，答案就出来了

### 为什么 js 在类型检查中是动态类型

`js`作为动态类型语言，是因为否允许随时修改变量类型，例如`var a = 100; a = 'ym'`,`a`的值先是数字类型，后面也可以以赋值为字符串类型

### 为什么 js 被称为弱类型语言

强类型不允许隐式类型转换, 但`js`允许隐式类型转换，例如`a+b`,`a`和`b`是数字类型会相加，如果有一个是字符串类型会进行字符串拼接，其中的数字类型会先`toString()`转为字符串

### 弱类型引起问题

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

### 强类型优势

- 错误更早暴露
- 代码更加智能，编码更准确
- 重构更可靠
- 减少不必要类型判断

举例：

```js
// element类型不确定，很可能会报错，或undefined
function render(element) {
  return elements.name;
}

// element对象中有一个name字符串类型，入参类型一目了然
interface eleProps {
  name: string;
}
function render1(element: eleProps) {
  return elements.name;
}
```

## 使用枚举类型注意事项

有必要说下枚举类型，因为枚举类型会入侵代码，而且大部分面试官喜欢问枚举类型编译成 `js` 是什么

枚举类型会入侵代码，最终会编译成一个<strong>双向键值对对象</strong>，但是使用`const` 定义枚举 编译后会被移除

```js
enum Status {
  a,
  b,
}
console.log(Status.a);

// 编译成es5
var Status;
(function (Status) {
    Status[Status["a"] = 0] = "a";
    Status[Status["b"] = 1] = "b";
})(Status || (Status = {}));
console.log(Status.a); // 0

console.log(Status); // {0: "a", 1: "b", a: 0, b: 1}

enum Status {
  a = "ym",
  b = "yn",
}
console.log(Status.a);

// 编译成es5
var Status;
(function (Status) {
    Status["a"] = "ym";
    Status["b"] = "yn";
})(Status || (Status = {}));
console.log(Status.a); // ym

console.log(Status); // {a: "ym", b: "yn"}

// 建议使用常量枚举
const enum Status {
  a = "ym",
  b = "yn",
}
console.log(Status.a);

// 编译成es5 js文件 建议使用const 定义枚举 编译后会被移除
console.log("ym" /* a */);

```

## 枚举类型应用场景

下面的例子对于一个地方使用感觉不出好处，但是用的地方多的话，抽离状态可维护性更强

```js
// 未使用
const { caseAllergy, caseOld, caseMarriage } = props;
const getCaseValue = (type: string) => {
  switch (type) {
    case "caseAllergy":
      return caseAllergy;
    case "caseOld":
      return caseOld;
    case "caseMarriage":
      return caseMarriage;
    default:
      return "";
  }
};

// 使用枚举
enum CaseType {
  caseAllergy = 'caseAllergy',
  caseOld='caseOld',
  caseMarriage='caseMarriage'
}

const { caseAllergy, caseOld, caseMarriage } = props;
const getCaseValue = (type: string) => {
  switch (type) {
    case CaseType.caseAllergy:
      return caseAllergy;
    case CaseType.caseOld:
      return caseOld;
    case CaseType.caseMarriage:
      return caseMarriage;
    default:
      return "";
  }
};
```

## 函数内传参带有可选参数

注意可选参数放在必传参数后面

```js
function func1(a: number, b?: number, ...rest: number[]): string {
  return "balabala";
}
```

## 使用任意类型 any

在特殊场合也需要`any`出场，比如`stringify`

```js
function stringify(value: any) {
  return JSON.stringify(value);
}
// 也可以这样
function stringify<T>(value: T): string {
  return JSON.stringify(value);
}
```

## 类型断言的使用

类型断言好比其他语言里的类型转换，但是不进行特殊的数据检查和解构。它没有运行时的影响，只是在编译阶段起作用。

在开发中我们定义`dom`时使用，或者用`Hooks`对`state`定义

```js
// as 语法
const [callName, setCallName] = useState('user1' as string);

// 获取 dom 对象
const rootDom = document.getElementById('root') as HTMLElement;

// 尖括号语法
const [callName, setCallName] = useState<string>('ym');

// 注意：这样使用尖括号语法在React报错，React认为是标签，使用as代替
const res = 1;
const num = <number>res;
```

## 如何定义对象字面量

遇到设置缓存，存储临时变量等场景，需创建一个对象字面量

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

类型别名(type)可以像接口(interface)一样，然而，仍有一些细微差别。

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

类型别名不能被 extends 和 implements（自己也不能 extends 和 implements 其它类型）。

```js
interface User {
  name: string;
}
interface UserProps extends User {
  userId: number;
}

// type需要使用&符达到扩展属性目的
type User = { name: string };
type UserProps = User & { userId: number };
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

## 类的使用注意事项

描述具体事物的抽象特征、类的属性在使用前必须声明

```js
class Person {
  public name: string;
  // 私有属性，不能被外部访问
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
const ym = new Person('name1', 20);
```

## 接口在类的应用

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

## 处理 Event 对象

React 的声明文件提供了 Event 对象的类型声明。
change 事件可以使用 `React.ChangeEvent`, click 事件可以使用 `React.MouseEvent`

```js
// 鼠标事件
onClick = (e: React.MouseEvent<HTMLDivElement>) => {
  // do something
};
// input change事件
onChange = (e: React.ChangeEvent<HTMLInputElement>) => {
  // do something
};
// 拖拽事件
onChange = (e: React.DragEvent<HTMLDivElement>) => {
  // do something
};
// 滚轮事件
onChange = (e: React.WheelEvent<HTMLDivElement>) => {
  // do something
};
// 剪贴板事件
onChange = (e: React.ClipboardEvent<HTMLDivElement>) => {
  // do something
};
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

## 使用泛型定义高阶组件

```js
interface IVisible {
  visible: boolean;
}

function withVisible<T>(
  WrapComponent: React.ComponentType<T & IVisible>
): React.ConponentType<omit<T, "visible">> {
  return class extends Component<T> {
    render() {
      return <WrapComponent {...this.props} visible={true} />;
    }
  };
}
```

## 结束

在项目中遇到的`TS`问题依然不断总结中，敬请关注！

## 基于 TS+React+Antd 快速开发项目

技术栈：`umi3+ react hooks + dva + typescript + antd`

[https://github.com/hanyueqiang/umi-ts-app](https://github.com/hanyueqiang/umi-ts-app)

## 推荐阅读

[手写 React Hooks](https://juejin.im/post/6872223515580481544)

[React 里为什么不能用 index 作为 key](https://juejin.im/post/6854573209623216136)

[React Hooks 中 useRef 的优雅使用](https://juejin.im/post/6854573209639976974)
