## 框架篇

#### react 生命周期是什么以及干什么？(某团)

#### vue 双向绑定原理(某团)

#### Vue/React 里为什么不能用 index 作为 key(某站)

#### vue 的 diff 和 react 的 diff

前端主流框架 vue 和 react 中都使用了虚拟 DOM（virtual DOM）技术，因为渲染真实 DOM 的开销是很大的，性能代价昂贵，比如有时候我们修改了某个数据，如果直接渲染到真实 dom 上会引起整个 dom 树的重绘和重排，而我们只需要更新修改过的那一小块 dom 而不要更新整个 dom

说到 DOM-diff 那一定要清楚其存在的意义，给定任意两棵树，采用先序深度优先遍历的算法找到最少的转换步骤
DOM-diff 比较两个虚拟 DOM 的区别，也就是在比较两个对象的区别。

梳理一下整个 DOM-diff 的过程：
用 JS 对象模拟 DOM（虚拟 DOM）
把此虚拟 DOM 转成真实 DOM 并插入页面中（render）
如果有事件发生修改了虚拟 DOM，比较两棵虚拟 DOM 树的差异，得到差异对象（diff）
把差异对象应用到真正的 DOM 树上（patch）

#### react 中 setState 以后，是子树渲染还是整颗树渲染还是其他情况？

#### setState 是异步的还是同步的，内部采用的是什么机制

#### react 事件了解吗？ (合成事件) 和普通事件有什么区别，实现原理

#### react Fibber 了解吗？ (只答了时间片轮转算法，调度策略)

#### react diff 采用的什么原则， (重点 last_index)

#### react 开发的几种方式

#### react 函数式写法和类写法的优缺点

#### react 类组件新增的两个生命周期是哪两个，作用是什么

#### react hook 使用过吗？ 使用过哪些 hook

React 现在的渲染都是由 Fiber 来调度
Fiber 调度过程中的两个阶段(以 Render 为界)

#### react 高阶组件有哪几种方式，如何写一个高阶组件

#### 什么时候使用 useRef，可以做到什么事情
