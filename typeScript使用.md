## type 与 interface 区别

### 相同点

都可以描述一个对象和函数

interface

```js
interface User {
  name: string
  age: number
}

interface SetUser {
  (name: string, age: number): void
}
```

type

```js
type User = {
  name: string
  age: number
}
type SetUser = (name: string, age: number) => void
```

都允许扩展（extends）
interface 和 type 都可以扩展
interface extends interface

```js
interface Name {
  name: string;
}
interface User extends Name {
  age: number;
}
```

type extends type

```js
type Name = {
  name: string,
};
type User = Name & { age: number };
```

### 不同点

type 可以声明基本类型别名。联合类型、元组等类型

```js
// 基本类型别名
type Name = string;

// 联合类型
interface Dog {
  wong();
}

type Pet = Dog | Cat;

// 具体定义数组中某个位置类型
type PetList = [Dog, Pet];

// 使用typeof 进行赋值
let div = document.createElement('div');
type B = typeof div;
```

interface 能够声明合并

```js
interface User {
  name: string
  age: string
}
interface User {
  sex: string
}
// 合并后的
User {
  name: string
  age: number
  sex: string
}
```
