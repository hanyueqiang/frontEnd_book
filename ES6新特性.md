## 块级作用域 let/const

let 生明不会提升；
var 声明会提升；
es6 必须先声明变量，再使用变量
const 在声明同时必须设置初始值，且不能修改
const obj = {};
obj.name = 'zss';
不改变内存指向，可以设置属性

主用 const, 配合 let

## 数组的解构

arr=[100,200,300];
const [foo,bar,baz='hello'] = arr;
const [,,baz] = arr;
const [foo, ...rest] = arr;
const path = '/foo/bar/cf';
const temp = path.split('/');
const dir = temp[1];
const [,dir] = path.split('/');

## 对象的解构

const obj = {name: 'zz', age:39};
// 解构后重命名
const { name: objName } = obj;

## 模版字符串

const str = `hello dff`;
嵌入值
const name = 'tom'
const msg = `hello ${name}`;

## 带标签的模版字符串

const name = 'tom';
const gender = true;

function myTagFunc(strings,name,gender) {
return strings[0]+name+strings[1]+gender+strings[2]
}
const result = myTagFunc`hey, ${name} is a ${gender}`;
console.log(result)

## 字符串扩展方法

const msg = 'Error: foo is not defined.';

msg.startsWith('Error) // true
msg.endsWith('.') // true
msg.includes('foo') // true

## 参数默认值

带形参的默认值要放在最后
function foo(enable = true) {
console.log(enable)
}

## 剩余参数

// 以前 arguments
// 出现在行参最后一位，只能使用一次
function foo(...args) {
console.log(args) // [1,2, 3, 4]
}

foo(1,2,3,4)

## 展开数组

const arr = ['foo','bar','baz'];
console.log(...arr);

## 箭头函数

const inc = n => n+1;

const inc1 = (n,m) => {
return n+m;
}

console.log(inc(100));

// this 指向不会改变

const person = {
name: 'tom',
say1: () => {
console.log(this.name) // undefined
},
say: function() {
console.log(this.name) // tom
},
sayHi: function() {
setTimeout(() => {
console.log(this.name) // tom
},1000)
}
}
person.say1();

## 对象字面量增强

const bar = '123';

const obj = {
foo: '123',
bar,
method: function() {
console.log(this)
},
method() {
console.log(this) // 等价于指向当前对象
}
[Math.random()]: 123 // 计算属性名
}

## Object.assign

将多个源对象的属性复制到一个目标对象中
const source1 = {
a:1,
b: 123
}

const target = {
a: 345,
c: 34
}

const result = Object.assign(target,source1)

console.log(result === target) // true;

## object.is

NaN === NaN // false

Object.is(NaN,NaN) // true

## Proxy 代理

监听某个对象属性读写 Object.defineProperty
专门为对象设置代理器

第一个参数 代理对象
第二个参数， 代理设置对象
const person = {
name: 'zz',
age: 20
}
const personProxy = new Proxy(person, {
get(target, property) {
return property in target ? target[property]: 'default'
}
set(target, property, value) {
target[property] = value
}
})

personProxy.age = 100;
console.log(personProxy.name)

## Proxy vs defineProperty

defineProperty 只能监视读写

Proxy 可删除对象属性 deleteProperty
const personProxy = new Proxy(person, {
defineProperty(target,property) {
delete target[property];
}
})
delete personProxy.age

更好到对数组对象监视

const list = [];

const listProxy = new Proxy(list,{
// 监视数据写入
set(target, property,value) {
target[property] = value;
return true; // 表示写入成功
}
})

list.push(100);

proxy 已非入侵对方式监管对象对读写

## Reflect 属于一个静态类

封装类一系列对象对底层操作
最大对意义统一一套操作对象 api

Reflect.has(obj, 'name') // name in obj
Reflect.deleteProperty(obj,'age') // delete obj['age']
Reflect.ownKeys(obj) // Object.keys(obj)

## Promise

## class 类

function Person(name) {
this.name = name;
}
Person.property.say = function() {

}

class Person {
constructor(name) {
this.name = name
}
say() {
console.log(this.name)
}
}
cosnt p = new Person('tom')
p.say()

## 静态方法 static

// this 不会指向实例对象，指向类型
class Person {
constructor(name) {
this.name = name
}
say() {
console.log(this.name)
}
static create(name) {
return new Person(name)
}
}
const tom = Person.create('tom')
tom.say();

## 类的继承 extends

class Students extends Person {
constructor(name,number) {
super(name)
this.number = number
}
hello() {
super.say();
console.log(this.number)
}
}
const s = new Student('jsck','100');
s.hello();

## Set 数据结构

成员不允许重复

const s = new Set();
s.add(1).add(2);
console.log(s.size)

s.has(100)
s.delete(2) // true
用于去重
const new Set(arr);
Array.from(new Set(arr))

## Map 数据结构

与对象类似 建只能值字符串

const m = new Map();
不会转换字符串
可以使用任意类型数据结构作为建

## Symbol

表示独一无二的值
为对象添加独一无二的值
const cache = {};
cache['foo'] = Math.random();

cache['foo'] = '123';
const s = Symbol();
