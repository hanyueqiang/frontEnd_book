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

const tuple: [number,string] = [18,'zed'];

## 泛型

```js
function create<T>(length: number, value: T): T[] {
  const arr = Array < T > length.fill(value);
  return arr;
}
const res = create < string > (3, "foo");
```
