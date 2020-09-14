## 手写 React Hooks

- Hooks 是 React 16.8 新增的特性，它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性
- 凡是 use 开头的 React API 都是 Hooks

## Hook 是什么

Hook 是一个特殊的函数，它可以让你“钩入” React 的特性。例如，useState 是允许你在 React 函数组件中添加 state 的 Hook。

## 为什么使用 Hooks

引用官网描述

- ##### 在组件之间复用状态逻辑很难
  可能要用到 render props （渲染属性）或者 HOC（高阶组件），但无论是渲染属性，还是高阶组件，都会在原先的组件外包裹一层父容器（一般都是 div 元素）.如果你在 React DevTools 中观察过 React 应用，你会发现由 providers，consumers，高阶组件，render props 等其他抽象层组成的组件会形成“嵌套地狱”。
- ##### 复杂组件变得难以理解
  组件常常在 componentDidMount 和 componentDidUpdate 中获取数据。但是，同一个 componentDidMount 中可能也包含很多其它的逻辑，如设置事件监听，而之后需在 componentWillUnmount 中清除。相互关联且需要对照修改的代码被进行了拆分，而完全不相关的代码却在同一个方法中组合在一起。如此很容易产生 bug
- ##### 难以理解的 class
  this 指向问题：父组件给子组件传递函数时，必须绑定 this

## Hook 规则

- 只能在函数内部的最外层调用 Hook，不要在循环、条件判断或者子函数中调用
- 只在 React 函数中调用 Hook
  在 React 的函数组件中调用 Hook
  在自定义 Hook 中调用其他 Hook

## 利用 eslint 做 hooks 规则检查

使用 eslint-plugin-react-hooks 来检查代码错误

```js
{
  "plugins": ["react-hooks"],
  // ...
  "rules": {
    "react-hooks/rules-of-hooks": 'error',// 检查 Hook 的规则
    "react-hooks/exhaustive-deps": 'warn' // 检查 effect 的依赖
  }
}
```

## useState

useState 会返回一个数组：一个 state，一个更新 state 的函数。

类似 class 组件的 this.setState，但是它不会把新的 state 和旧的 state 进行合并，而是直接替换

```js
// 保存状态的数组
let hookStates = [];
// 索引
let hookIndex = 0;

function useState(initialState) {
  hookStates[hookIndex] = hookStates[hookIndex] || initialState;
  // 利用闭包维护函数调用位置
  let currentIndex = hookIndex;
  function setState(newState) {
    // 判断传入的state是否为函数,如果是把prevState传入
    if (typeof newState === "function") {
      // 重新复制给newState
      newState = newState(hookStates[hookIndex]);
    }
    // 更新state
    hookStates[currentIndex] = newState;
    // 触发视图更新
    render();
  }
  // 返回数组形式，解构可写成任意变量
  return [hookStates[hookIndex++], setState];
}
```

## useEffect

useEffect 就是一个 Effect Hook，给函数组件增加了操作副作用的能力。它跟 class 组件中的 componentDidMount、componentDidUpdate 和 componentWillUnmount 具有相同的用途，只不过被合并成了一个 API

与 componentDidMount 或 componentDidUpdate 不同，使用 useEffect 调度的 effect 不会阻塞浏览器更新视图，这让你的应用看起来响应更快。
在特殊情况（例如测量布局），有单独的 useLayoutEffect Hook，使用与 useEffect 相同

```js
//保存状态的数组
let hookStates = [];
//索引
let hookIndex = 0;

function useEffect(callback, dependencies) {
  if (hookStates[hookIndex]) {
    // 非初始调用
    let lastDependencies = hookStates[hookIndex];
    // 判断传入依赖项跟上一次是否相同
    let same = dependencies.every(
      (item, index) => item === lastDependencies[index]
    );
    if (same) {
      hookIndex++;
    } else {
      hookStates[hookIndex++] = dependencies;
      callback();
    }
  } else {
    // 初始调用
    hookStates[hookIndex++] = dependencies;
    callback();
  }
}
```

## useMemo

允许你通过「记住」上一次计算结果的方式在多次渲染的之间缓存计算结果

使得控制具体子节点何时更新变得更容易，减少了对纯组件的需要

```js
// 保存状态的数组
let hookStates = [];
// 索引
let hookIndex = 0;

function useMemo(factory, dependencies) {
  if (hookStates[hookIndex]) {
    // 非首次
    let [lastMemo, lastDependencies] = hookStates[hookIndex];

    // 判断传入依赖项跟上一次是否相同
    let same = dependencies.every(
      (item, index) => item === lastDependencies[index]
    );
    if (same) {
      hookIndex++;
      return lastMemo;
    } else {
      // 只要有一个依赖变量不一样的话
      let newMemo = factory();
      hookStates[hookIndex++] = [newMemo, dependencies];
      return newMemo;
    }
  } else {
    // 首次调用
    let newMemo = factory();
    hookStates[hookIndex++] = [newMemo, dependencies];
    return newMemo;
  }
}
```

## useCallback

允许你在重新渲染之间保持对相同的回调引用以使得 shouldComponentUpdate 继续工作

```js
// 保存状态的数组
let hookStates = [];
// 索引
let hookIndex = 0;

function useCallback(callback, dependencies) {
  if (hookStates[hookIndex]) {
    // 非首次
    let [lastCallback, lastDependencies] = hookStates[hookIndex];

    let same = dependencies.every(
      (item, index) => item === lastDependencies[index]
    );
    if (same) {
      hookIndex++;
      return lastCallback;
    } else {
      // 只要有一个依赖变量不一样的话
      hookStates[hookIndex++] = [callback, dependencies];
      return callback;
    }
  } else {
    // 首次调用
    hookStates[hookIndex++] = [callback, dependencies];
    return callback;
  }
}
```

## memo

```js
function memo(OldFunctionComponent) {
  return class extends React.PureComponent {
    render() {
      return <OldFunctionComponent {...this.props} />;
    }
  };
}
```

## useContext

接收一个 context 对象（React.createContext 的返回值）并返回该 context 的当前值
useContext(MyContext) 只是让你能够读取 context 的值以及订阅 context 的变化。仍然需要在上层组件树中使用 <MyContext.Provider> 来为下层组件提供 context

```js
function useContext(context) {
  return context._currentValue;
}

// 父组件
const CountCtx = React.createContext();
function ParentComp() {
  const [state, setState] = React.useState({ number: 0 });
  return (
    <CountCtx.Provider value={{ state, setState }}>
      <Child />
    </CountCtx.Provider>
  );
}

// 子组件
function Child() {
  let { state, setState } = useContext(CountCtx);
  return (
    <div>
      <p>{state.number}</p>
      <button onClick={() => setState({ number: state.number + 1 })}>
        add
      </button>
    </div>
  );
}
```

## useRef

useRef 返回一个可变的 ref 对象，其 current 属性被初始化为传入的参数
useRef 返回的 ref 对象在组件的整个生命周期内保持不变，也就是说每次重新渲染函数组件时，返回的 ref 对象都是同一个（注意使用 React.createRef ，每次重新渲染组件都会重新创建 ref）

```js
let lastRef;

function useRef(value) {
  lastRef = lastRef || { current: value };
  return lastRef;
}
```

## useReducer

useReducer 和 redux 中 reducer 很像
useState 内部就是靠 useReducer 来实现的

```js
// 保存状态的数组
let hookStates = [];
// 索引
let hookIndex = 0;

function useReducer(reducer, initialState) {
  hookStates[hookIndex] = hookStates[hookIndex] || initialState;

  let currentIndex = hookIndex;
  function dispatch(action) {
    hookStates[currentIndex] = reducer
      ? reducer(hookStates[currentIndex], action)
      : action;
    // 触发视图更新
    render();
  }
  return [hookStates[hookIndex++], dispatch];
}

// useState可以使用useReducer改写
function useState(initialState) {
  return useReducer(null, initialState);
}
```

## 参考

[Hook 规则](https://react.docschina.org/docs/hooks-rules.html)

[React Hooks 详解 【近 1W 字】+ 项目实战](https://juejin.im/post/6844903985338400782)

## 推荐

[React Hooks 父组件中获取子组件实例值](https://juejin.im/post/6854573209644171277)

[React Hooks 中 useRef 的优雅使用](https://juejin.im/post/6854573209639976974)
