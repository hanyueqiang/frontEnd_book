> `useRef` 返回一个可变的 `ref` 对象，其 `.current` 属性被初始化为传入的参数`（initialValue）`。返回的 `ref` 对象在组件的整个生命周期内保持不变。

当 `ref` 对象内容发生变化时，`useRef` 并不会通知你。变更 `.current` 属性不会引发组件重新渲染。如果想要在 `React` 绑定或解绑 `DOM` 节点的 `ref` 时运行某些代码，则需要使用回调 `ref` 来实现.

react 提供的经典案例：

```
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` 指向已挂载到 DOM 上的文本输入元素
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```

### useRef 优雅使用一：作用于 dom 元素

```
const UseRefInput = () => {
	const inputRef = useRef();
	const getInputValue= () => {
    const value = inputRef.current.value;
		console.log(value);
  }

	return (
    <>
		  <input ref={inputRef} type="text" />
      <button onClick={getValue}>获取input的值</button>
    </>
  );
}
```

### useRef 优雅使用二：跨生命周期保存某个值，如定时器 id

一个计时器组件

```
export default () => {
  const timer = useRef(null);
  const [count, setCount] = useState(0);

  useEffect(() => {
    timer.current = setInterval(() => {
      setCount(count => count + 1);
    }, 1000);
    return () => clearInterval(timer.current);
  },[]);

  return (
    <>
      <span>开始计时</span>
      <span>{count}</span>
    </>
  );
}
```

useRef 返回一个可变的 ref 对象,返回的 ref 对象在组件的整个生命周期内保持不变。
将定时器函数提取出来，每次定时器触发时，都能取到最新到 count

```
function Counter() {
  const [count, setCount] = useState(0);
  const myRef = useRef(null);

  myRef.current = () => {
    setCount(count + 1);
  };

  useEffect(() => {
    const id = setInterval(() => {
      myRef.current();
    }, 1000);
    return () => clearInterval(id);
  }, []);

  return <h1>{count}</h1>;
}
```
