## JSX 如何成为 DOM

> JSX 是 JavaScript 的一种语法扩展，它和模板语言很接近，但是它充分具备 JavaScript 的能力。
> JSX 会被编译为 React.createElement()， React.createElement() 将返回一个叫作“React Element”的 JS 对象。

JSX 的本质：JavaScript 的语法扩展, JSX 的本质是 React.createElement 这个 JavaScript 调用的语法糖

对应 dom 结构

```js
<ul className="list">
  <li key="1">1</li>
  <li key="2">2</li>
</ul>
```

createElement 转化 dom

```js
React.createElement(
  "ul",
  {
    // 传入属性键值对
    className: "list",
    // 从第三个入参开始往后，传入的参数都是 children
  },
  React.createElement(
    "li",
    {
      key: "1",
    },
    "1"
  ),
  React.createElement(
    "li",
    {
      key: "2",
    },
    "2"
  )
);
```
