## 框架篇

#### vue 和 react 的区别

- Vue 和 React 相同点

  1.都使用 Virtural DOM 2.都使用组件化思想，流程基本一致 3.都是响应式，推崇单向数据流 4.都有成熟的社区，都支持服务端渲染

render 函数执行返回 VNode（虚拟 DOM 的数据结构，本质上是棵树）。当每一次 UI 更新时，总会根据 render 重新生成最新的 VNode，然后跟以前缓存起来老的 VNode 进行比对，再使用 Diff 算法（框架核心）去真正更新真实 DOM（虚拟 DOM 是 JS 对象结构，同样在 JS 引擎中，而真实 DOM 在浏览器渲染引擎中，所以操作虚拟 DOM 比操作真实 DOM 开销要小的多）

为什么 Vue 和 React 都选择 Virtual DOM？ 1.减少直接操作 DOM。框架给我们提供了屏蔽底层 dom 书写的方式，减少频繁的整更新 dom，同时也使得数据驱动视图 2.为函数式 UI 编程提供可能（React 核心思想） 3.可以跨平台，渲染到 DOM（web）之外的平台。比如 ReactNative，Weex

- 不同点

1. 核心思想不同
   Vue 早期定位是尽可能的降低前端开发的门槛（这跟 Vue 作者是独立开发者也有关系）。所以 Vue 推崇灵活易用（渐进式开发体验），数据可变，双向数据绑定（依赖收集）

React 推崇函数式编程（纯组件），数据不可变以及单向数据流。函数式编程最大的好处是其稳定性（无副作用）和可测试性（输入相同，输出一定相同），所以通常大家说的 React 适合大型应用，根本原因还是在于其函数式编程。

写法差异
Vue 推崇 template（简单易懂，从传统前端转过来易于理解）、单文件 vue
React 推崇 JSX、HOC、all in js

api 差异
template 模板中需要理解 slot、filter、指令等概念和 api
React 本质上核心只有一个 Virtual DOM + Diff 算法

社区差异
由于 Vue 定义简单易上手，能快速解决问题，所以很多常见的解决方案，是 Vue 官方主导开发和维护。比如状态管理库 Vuex、路由库 Vue-Router、脚手架 Vue-CLI、Vutur 工具等
React 只关注底层，上层应用解决方案基本不插手，比如状态管理库方面，有 redux、mobx、redux-sage、dva 等一大堆
所以 React 团队有更多时间专注于底层升级，比如花了近 2 年时间把底层架构改为 Fiber 架构，以及创造出 React Hooks 来替换 HOC，Suspense 等

未来升级方向不同
Vue 依然会定位简单易上手（渐进式开发），依然是考虑通过依赖收集来实现数据可变，这点从 Vue3 核心更新内容可以看到：template 语法基本不变、options api 只增加了 setup 选项（composition api）、基于依赖收集（Proxy）的数据可变

React 的函数式编程这个基本盘不会变，从 React Hooks 可以看出，React 团队致力于组件函数式编程，（纯组件，无 class 组件），尽量减少副作用（减少 this，this 会引起副作用）。

2.组件实现不同
Vue 源码实现是把 options 挂载到 Vue 核心类上，然后再 new Vue({options})拿到实例
React 内部实现比较简单，直接定义 render 函数以生成 VNode，而 React 内部使用了四大组件类包装 VNode，不同类型的 VNode 使用相应的组件类处理，职责划分清晰明了（后面的 Diff 算法也非常清晰）。React 类组件都是继承自 React.Component 类，其 this 指向用户自定义的类，对用户来说是透明的。

3.响应式原理不同
Vue2 和 Vue3 响应式原理基本一致，都是基于依赖收集，不同的是 Vue3 使用 Proxy
vue
Vue 依赖收集，自动优化，数据可变
Vue 递归监听 data 的所有属性,直接修改
当数据改变时，自动找到引用组件重新渲染

react
React 基于状态机，手动优化，数据不可变，需要 setState 驱动新的 State 替换老的 State
当数据改变时，以组件为根目录，默认全部重新渲染

4.diff 算法不同
两者流程思维上是类似的，都是基于两个假设（使得算法复杂度降为 O(n)）
不同的组件产生不同的 DOM 结构。当 type 不相同时，对应 DOM 操作就是直接销毁老的 DOM，创建新的 DOM。
同一层次的一组子节点，可以通过唯一的 key 区分。
Vue 基于 snabbdom 库，它有较好的速度以及模块机制。Vue Diff 使用双向链表，边对比，边更新 DOM。
React 主要使用 diff 队列保存需要更新哪些 DOM，得到 patch 树，再统一操作批量更新 DOM。

5.事件机制不同
vue
Vue 原生事件使用标准 Web 事件
Vue 组件自定义事件机制，是父子组件通信基础
Vue 合理利用了 snabbdom 库的模块插件
react
React 原生事件被包装，所有事件都冒泡到顶层 document 监听，然后在这里合成事件下发。基于这套，可以跨端使用事件机制，而不是和 Web DOM 强绑定
React 组件上无事件，父子组件通信使用 props

#### react 生命周期是什么以及干什么？

#### vue 双向绑定原理

#### Vue/React 里为什么不能用 index 作为 key

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

#### MVVM，说说与 MVC 有什么区别
