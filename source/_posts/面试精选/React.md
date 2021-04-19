---
title: React 面试精选
date: 2021-04-19
categories: [面试, React]
---

## 什么是 jsx? (jsx 的本质是什么)

- jsx 是一种 JavaScript 的语法扩展，仅仅只是 React.createElement 函数的语法糖，所有的 jsx 最终都会被转换成 React.createElement 的函数调用
- 它用于描述我们的 UI 界面，并且其完全可以和 JavaScript 混合使用
- 它不需要专门学习模版语法的一些额外指令

## React 组件通信如何实现

- 父 -> 子: props
- 子 -> 父: props + 回调
- 兄弟组件之间通信: 找到两个兄弟节点共同的父节点，结合 props
- 跨层级通信: Context
- 发布订阅模式: 发布者发布事件，订阅者监听事件并做出回应，通过引入 event 模块进行通信
- 全局状态管理工具: 借助 Redux 或 Mobx 等全局状态管理工具进行通信，这种工具会维护一个全局状态中心 Store，并根据不同的事件产生新的状态

## React 的生命周期

### 挂载阶段

- constructor: 通常用来初始化 state 和绑定 this
- getDerivedStateFromProps: 静态方法, 接收新属性想去修改 state
- render: 返回只需要渲染的内容
- componentDidMount: 组件装载后调用，可以获取 DOM 节点并操作

### 更新阶段

- getDerivedStateFromProps(在更新各挂载阶段都会调用)
- shouldComponentUpdate: 是否触发重渲染，返回一个 boolean，通常用来优化性能
- render: 更新阶段也会触发此生命周期
- getSnapshotBeforeUpdate: 返回值会作为第三个参数传递给 componentDidUpdate
- componentDidUpdate: 三个参数，prevProps、prevState、snapshot，最后在 componentDidUpdate 中统一触发回调或更新状态

### 卸载阶段

- componentWillUnmount: 当组件被卸载或销毁时调用，通常用于清除一些定时器，取消网络请求，清理无效 DOM 元素等垃圾清理工作

## setState 到底是异步还是同步

有时表现出异步，有时表现出同步

- setState 只在合成事件和钩⼦函数中是 "异步" 的，在 **原⽣事件** 和 **setTimeout** 中都是同步的
- setState 的 "异步" 并不是说内部由异步代码实现，其实本身执⾏的过程和代码都是同步的，只是 **合成事件** 和 **钩⼦函数** 的 **调⽤顺序在更新之前**，导致在合成事件和钩⼦函数中没法⽴⻢拿到更新后的值，形成了所谓的 "异步"，当然可以通过第⼆个参数 setState(partialState, callback)中的 callback 拿到更新后的结果
- setState 的批量更新优化也是建⽴在 "异步"（合成事件、钩⼦函数）之上的，在 原⽣事件 和 setTimeout 中不会批量更新，在 "异步" 中如果对同⼀个值进⾏多次 setState，setState 的批量更新策略会对其进⾏覆盖，取最后⼀次的执⾏，如果是同时 setState 多个不同的值，在更新时会对其进⾏合并批量更新。

## React 中 keys 的作用

在 React Diff 算法中 React 会借助元素的 Key 值来判断该元素是最新创建的还是被移动而来的元素，从而减少不必要的元素重渲染

## 如何避免组件的重新渲染

React 中最常见的问题之一是组件不必要地重新渲染。React 提供了两个方法，在这些情况下非常有用

- React.memo(): 这可以防止不必要地重新渲染函数组件
- PureComponent: 这可以防止不必要地重新渲染类组件

这两种方法都依赖于对传递给组件的 props 的浅比较，如果 props 没有改变，那么组件将不会重新渲染。虽然这两种工具都非常有用，但是浅比较会带来额外的性能损失，因此如果使用不当，这两种方法都会对性能产生负面影响。

## 谈一下你对 fiber 的理解

Fiber 就是通过对象记录组件上需要做或者已经完成的更新，一个组件可以对应多个 Fiber。

简单来说就是, react 的渲染和更新分为两个阶段:

- reconciliation 阶段(调和阶段): 执行 diff 算法(diff fiber tree)，纯 js 计算
- commit 阶段(交付阶段): 将 diff 结果渲染 DOM

但这么操作会造成性能问题:

- js 是单线程, 且和 DOM 渲染共用一个线程
- 当组件足够复杂，组件更新时计算和渲染的压力都很大
- 同时如果再有 DOM 操作(动画、鼠标拖拽等)将会发生卡顿

解决方案 fiber:

- 将 reconciliation 阶段进行任务拆分(commit 无法拆分)
- DOM 需要渲染时暂停，空闲时恢复
- window.requestIdleCallback (方法将在浏览器空闲时间段内对要调用的函数进行排队)

## fiber 的数据结构

- fiber 是个链表，有 child 和 sibing 属性，指向第一个子节点和相邻的兄弟节点，从而构成 fiber tree。return 属性指向其父节点

- 更新队列，updateQueue，是一个链表，有 first 和 last 两个属性，指向第一个和最后一个 update 对象
- 每个 fiber 有一个属性 updateQueue 指向其对应的更新队列。
- 每个 fiber（当前 fiber 可以称为 current）有一个属性 alternate，开始时指向一个自己的 clone 体，update 的变化会先更新到 alternate 上，当更新完毕，alternate 替换 current。

## 什么是高阶组件

高阶组件(HOC)是接受一个组件并返回一个新组件的函数。基本上，这是一个模式，是从 React 的组合特性中衍生出来的，称其为纯组件，因为它们可以接受任何动态提供的子组件，但不会修改或复制输入组件中的任何行为

HOC 可以用于以下许多用例：

1. 代码重用、逻辑和引导抽象
2. 渲染劫持
3. state 抽象和操作
4. props 处理

## 虚拟 DOM 是什么

虚拟 DOM 本质就是用一个原生的 JavaScript 对象去描述一个 DOM 节点，是对真实 DOM 的一层抽象

虚拟 DOM 对象最基本的三个属性：

1. 标签类型
2. 标签元素的属性
3. 标签元素的子节点

## redux 的⼯作流程

- Store：保存数据的地⽅，你可以把它看成⼀个容器，整个应⽤只能有⼀个 Store；
- State：Store 对象包含所有数据，如果想得到某个时点的数据，就要对 Store ⽣成快照，这种时点的数据集合，就叫 State；
- Action： State 的变化， 会导致 View 的变化。但是，⽤户接触不到 State，只能接触到 View。所以，State 的变化必须是 View 导致的。Action 就是 View 发出的通知，表示 State 应该要发⽣变化了；
- Action Creator：View 要发送多少种消息，就会有多少种 Action。如果都⼿写，会很麻烦，所以我们定义⼀个函数来⽣成 Action，这个函数就叫 Action Creator；
- Reducer：Store 收到 Action 以后，必须给出⼀个新的 State，这样 View 才会发⽣变化。这种 State 的计算过程就叫做 Reducer。Reducer 是⼀个函数，它接受 Action 和当前 State 作为参数，返回⼀个新的 State；
- dispatch：是 View 发出 Action 的唯⼀⽅法。

1. 首先，用户通过 View 发出 Action，发出方式用到了 dispatch 方法
2. Store 调用 Reducer, 传入两个参数: 当前 State 和收到的 Action，Reducer 会返回新的 State
3. State ⼀旦有变化，Store 就会调⽤监听函数，来更新 View