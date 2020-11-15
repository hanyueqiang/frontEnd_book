React 使创建交互式 UI 变得轻而易举。为你应用的每一个状态设计简洁的视图,当数据改变时 React 能有效地更新并正确地渲染组件。

#### 什么是 Virtual DOM

`Virtual DOM`也称虚拟 dom，是一个 js 对象保存在内存中，通过 ReactDom 与真实 dom 同步，⽤ JavaScript 对象表示 DOM 信息和结构，当状态变更更的时候，重新渲染这个 JavaScript 的对 象结构。这个 JavaScript 对象称为 virtual dom

#### Shadow DOM 与 Virtual DOM 区别

Shadow DOM 是一种浏览器技术，用于 web 组件封装变量 css

#### Fiber 是什么

Fiber 是 React16 中新的协调引擎，目的使 Virtual DOM 可以进行增量式渲染。

#### jsx 语法糖

React 使⽤用 JSX 来替代常规的 JavaScript。JSX 是⼀一个看起来很像 XML 的 JavaScript 语法扩展。

优点：使⽤用 JSX 编写模板简单快速，在编译过程中即使发现错误，与正常写 html/css/js 差别不大
与 vue 的虚拟 dom 区别： react 中虚拟 dom+jsx 的设计一开始就有，vue 则是演进过程中才出现的，jsx 本来就是 js 扩展，转义过程简单直接的多;vue 把 template 编译为 render 函数的过程需要 复杂的编译器器转换字符串 ast.js 函数字符串
原理： babel-loader 会预编译 JSX 为 React.createElement();

#### React.createElement

将传⼊入的节点定义转换为 vdom。
节点类型有：文本节点、HTML 标签节点、function 组件、class 组件、fragment、其他如 portal 等节点

```js
// ./src/react/index.js
import { TEXT } from "./const";

function createElement(type, config, ...children) {
  // 源码中做了详细处理，过滤掉key、ref等
  if (config) {
    delete config.__self;
    delete config.__source;
  }

  const props = {
    ...config,
    children: children.map((child) =>
      typeof child === "object" ? child : createTextNode(child)
    ),
  };
  return {
    type,
    props,
  };
}
function createTextNode(text) {
  return {
    type: TEXT,
    props: {
      children: [],
      nodeValue: text,
    },
  };
}
export default {
  createElement,
};
```

#### ReactDom.render

```js
ReactDOM.render(element, container, [callback]);
```

首次调用时，容器器节点⾥里里的所有 DOM 元素都会被替换，后续的调⽤用则会使用 React 的 DOM 差分算法(DOM diffing algorithm)进行⾼高效的更新。如果提供了可选的回调函数 callback，该回调将在组件被渲染或更更新之后被执⾏。

ReactDOM.render(vdom, container)可以将 vdom 转换为 dom 并追加到 container 中,转换过程需要经过⼀一个 diff 过程。

```js
// vnode代表虚拟dom节点 node代表真实dom节点
import { TEXT } from "./const";
function render(vnode, container) {
  console.log("vnode", vnode);
  // vnode _> node
  const node = createNode(vnode, container);
  container.appendChild(node);
}

// 返回真实的dom节点
function createNode(vnode, parentNode) {
  let node = null;
  const { type, props } = vnode;
  if (type === TEXT) {
    node = document.createTextNode("");
  } else if (typeof type === "string") {
    node = document.createElement(type);
  } else if (typeof type === "function") {
    node = type.isReactComponent
      ? updateClassComponent(vnode, parentNode)
      : updateFunctionComponent(vnode, parentNode);
  } else {
    node = document.createDocumentFragment();
  }

  reconcileChildren(props.children, node);
  updateNode(node, props);
  return node;
}

function reconcileChildren(children, node) {
  for (let i = 0; i < children.length; i++) {
    let child = children[i];
    if (Array.isArray(child)) {
      for (let j = 0; j < child.length; j++) {
        render(child[j], node);
      }
    } else {
      render(child, node);
    }
  }
}

function updateNode(node, nextVal) {
  Object.keys(nextVal)
    .filter((k) => k !== "children")
    .forEach((k) => {
      if (k.slice(0, 2) === "on") {
        let eventName = k.slice(2).toLocaleLowerCase();
        node.addEventListener(eventName, nextVal[k]);
      } else {
        node[k] = nextVal[k];
      }
    });
}

function updateFunctionComponent(vnode, parentNode) {
  const { type, props } = vnode;
  const vvnode = type(props);
  const node = createNode(vvnode, parentNode);
  return node;
}

function updateClassComponent(vnode, parentNode) {
  const { type, props } = vnode;
  let cmp = new type(props);
  const vvnode = cmp.render();
  const node = createNode(vvnode, parentNode);
  return node;
}
export default {
  render,
};
```
