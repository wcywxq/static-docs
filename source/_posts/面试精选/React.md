---
title: React 面试精选
date: 2021-04-19
categories: [面试, React]
---

## react 的优势

1. 前端框架发展说起(业务量的增长)
2. react 是用于构建用户界面的 JavaScript 库
   - 库: 自己实现功能
   - 框架：直接使用功能即可
3. 社区
4. 适用场景，设计理念
5. 高内聚，低耦合

## React 中 keys 的作用

在 React Diff 算法中 React 会借助元素的 Key 值来判断该元素是最新创建的还是被移动而来的元素，从而减少不必要的元素重渲染

## 如何避免组件的重新渲染

React 中最常见的问题之一是组件不必要地重新渲染。React 提供了两个方法，在这些情况下非常有用

- React.memo(): 这可以防止不必要地重新渲染函数组件
- PureComponent: 这可以防止不必要地重新渲染类组件

这两种方法都依赖于对传递给组件的 props 的浅比较，如果 props 没有改变，那么组件将不会重新渲染。虽然这两种工具都非常有用，但是浅比较会带来额外的性能损失，因此如果使用不当，这两种方法都会对性能产生负面影响。

## react 组件通信如何实现

- 父 -> 子: props
- 子 -> 父: props + 回调
- 兄弟组件之间通信: 找到两个兄弟节点共同的父节点，结合 props
- 跨层级通信: Context
- 发布订阅模式: 发布者发布事件，订阅者监听事件并做出回应，通过引入 event 模块进行通信
- 全局状态管理工具: 借助 Redux 或 Mobx 等全局状态管理工具进行通信，这种工具会维护一个全局状态中心 Store，并根据不同的事件产生新的状态

## react 生命周期

### 挂载阶段

- constructor: 通常用来初始化 state 和绑定 this
- getDerivedStateFromProps: 静态方法, 接收新属性想去修改 state(配合 componentDidUpdate，可覆盖 componentWillReveiveProps 所有用法
- render: 返回只需要渲染的内容
- componentDidMount: 组件装载后调用，可以获取 DOM 节点并操作

### 更新阶段

- getDerivedStateFromProps(在更新各挂载阶段都会调用)
- shouldComponentUpdate: 是否触发重渲染，返回一个 boolean，通常用来优化性能
- render: 更新阶段也会触发此生命周期
- getSnapshotBeforeUpdate: 返回值会作为第三个参数传递给 componentDidUpdate (static getSnapshotBeforeUpdate -> 代替 componentWillUpdate (在最终 render 前被调用)))
- componentDidUpdate: 三个参数，prevProps、prevState、snapshot，最后在 componentDidUpdate 中统一触发回调或更新状态

### 卸载阶段

- componentWillUnmount: 当组件被卸载或销毁时调用，通常用于清除一些定时器，取消网络请求，清理无效 DOM 元素等垃圾清理工作

## react 项目中有哪些细节可以优化？实际开发中都做过哪些性能优化

分为: 开发过程中、上线之后的首屏、运行过程的状态

### 上线之后的首屏及运行状态:

- 首屏优化一般涉及到几个指标 FP、FCP、FMP；要有一个良好的体验是尽可能的把 FCP 提前，需要做一些工程化的处理，去优化资源的加载

  - FP: 页面在导航后首次呈现出不同于导航前内容的时间点;
  - FCP: 首次绘制任何文本，图像，非空白 canvas 或 SVG 的时间点;
  - FMP: 是由 Google 工程师引入的一种现代性能指标，它告诉我们页面何时 **有用**。其本质上是通过一种算法来猜测某个时间点可能是 FMP

- 方式及分包策略，资源的减少是最有效的加快首屏打开的方式

- 对于 CSR 的应用，FCP 的过程一般是首先加载 js 与 css 资源，js 在本地执行完成，然后加载数据回来，做内容初始化渲染，这中间就有几次的网络反复请求的过程；所以 CSR 可以考虑使用骨架屏及预渲染（部分结构预渲染）、suspence 与 lazy 做懒加载动态组件的方式

- 当然还有另外一种方式就是 SSR 的方式，SSR 对于首屏的优化有一定的优势，但是这种瓶颈一般在 Node 服务端的处理，建议使用 stream 流的方式来处理，对于体验与 node 端的内存管理等，都有优势；

- 不管对于 CSR 或者 SSR，都建议配合使用 Service worker，来控制资源的调配及骨架屏秒开的体验

- react 项目上线之后，首先需要保障的是可用性，所以可以通过 React.Profiler 分析组件的渲染次数及耗时的一些任务，但是 Profile 记录的是 commit 阶段的数据，所以对于 react 的调和阶段就需要结合 performance API 一起分析

- 由于 React 是父级 props 改变之后，所有与 props 不相关子组件在没有添加条件控制的情况之下，也会触发 render 渲染，这是没有必要的，可以结合 React 的 PureComponent 以及 React.memo 等做浅比较处理，这中间有涉及到不可变数据的处理，当然也可以结合使用 ShouldComponentUpdate 做深比较处理

- 所有的运行状态优化，都是减少不必要的 render，React.useMemo 与 React.useCallback 也是可以做很多优化的地方

- 在很多应用中，都会涉及到使用 redux 以及使用 context，这两个都可能造成许多不必要的 render，所以在使用的时候，也需要谨慎的处理一些数据

- 最后就是保证整个应用的可用性，为组件创建错误边界，可以使用 componentDidCatch 来处理

### 实际项目中开发过程中还有很多其他的优化点

- 保证数据的不可变性
- 使用唯一的键值迭代
- 使用 web worker 做密集型的任务处理
- 不在 render 中处理数据
- 不必要的标签，使用 React.Fragments

## react 最新版本解决了什么问题 加了哪些东西

### React 16.x 的三大新特性 Time Slicing, Suspense，hooks

- Time Slicing

（解决 CPU 速度问题）使得在执行任务的期间可以随时暂停，跑去干别的事情，这个特性使得 react 能在性能极其差的机器跑时，仍然保持有良好的性能

- Suspense

（解决网络 IO 问题）和 lazy 配合，实现异步加载组件。 能暂停当前组件的渲染, 当完成某件事以后再继续渲染，解决从 react 出生到现在都存在的「异步副作用」的问题，而且解决得非常的优雅，使用的是「异步但是同步的写法」，我个人认为，这是最好的解决异步问题的方式

- componentDidCatch

此外，还提供了一个内置函数 componentDidCatch，当有错误发生时, 我们可以友好地展示 fallback 组件；可以捕捉到它的子元素（包括嵌套子元素）抛出的异常；可以复用错误组件。

### React16.8

- 加入 hooks，让 React 函数式组件更加灵活
- hooks 之前，React 存在很多问题
  - 在组件间复用状态逻辑很难
  - 复杂组件变得难以理解，高阶组件和函数组件的嵌套过深。
  - class 组件的 this 指向问题 (如果在 render 中 bind(this)，会造成每次都返回一个新函数，造成对应组件的重渲染; 如果使用 () => func() 也会每次都造成组件的重渲染)
  - 难以记忆的生命周期
- hooks 很好的解决了上述问题，hooks 提供了很多方法
  - useState 返回有状态值，以及更新这个状态值的函数
  - useEffect 接受包含命令式，可能有副作用代码的函数。
  - useContext 接受上下文对象（从 React.createContext 返回的值）并返回当前上下文值，
  - useReducer useState 的替代方案。接受类型为(state，action) => newState 的 reducer，并返回与 dispatch 方法配对的当前状态。
  - useCallback 返回一个回忆的 memoized 版本，该版本仅在其中一个输入发生更改时才会更改。纯函数的输入输出确定性
  - useMemo 纯的一个记忆函数
  - useRef 返回一个可变的 ref 对象，其.current 属性被初始化为传递的参数，返回的 ref 对象在组件的整个生命周期内保持不变。
  - useImperativeMethods 自定义使用 ref 时公开给父组件的实例值
  - useMutationEffect 更新兄弟组件之前，它在 React 执行其 DOM 改变的同一阶段同步触发
  - useLayoutEffect DOM 改变后同步触发。使用它来从 DOM 读取布局并同步重新渲染

## react 事件合成

![高阶组件](/images/事件合成.png)

react 根据 w3c 规范定义了每个事件处理函数的参数，即合成事件。react 在合成事件做了两件事，分别为事件委派和自动绑定。

- 事件委派

1. 事件处理程序将会传递 SyntheticEvent(事件合成) 的实例，这是一个跨浏览器原生事件包装器，它具有与浏览器原生事件相同的接口，支持 stopProgation 和 preventDefault，可以通过使用 nativeEvent 属性来访问原生事件对象，同时在所有浏览器中它们的工作方式都相同。
2. react 合成的 SyntheticEvent 采用了事件池，这样做可以大大节省内存，而不会频繁的创建和销毁事件对象。
3. react 并不会把事件处理函数直接绑定到真实的节点上，而是把所有事件放到统一的事件队列中，用监听器做监听。
4. 通过监听器上的映射来保存所有组件内部的事件监听和处理函数
5. 组件挂载或卸载时，改变这个监听器，通过对象的方式插入 or 删除
6. 事件发生时，先被监听器处理，然后通过映射调用真正的事件处理函数

- 自动绑定

1. 在 react 组件中，每个方法的上下文都会指向改组件的实例，即自动绑定 this 为当前组件
2. react 会对这种引用做缓存，从而优化 cpu 和 内存
3. 在使用 类组件或函数组件时，需手动实现 this 绑定

### react 和 原生事件的执行顺序

1. 原生事件，依次冒泡执行

2. react 合成事件，依次冒泡执行

3. document 上挂载的事件执行

```js
/*
 * 执行结果：
 * 1. dom child
 * 2. dom parent
 * 3. react child
 * 4. react parent
 * 5. dom document
 * */
```

### react 事件与原生事件可以混用么

React 事件和原生事件最好不要混用。原生事件中如果执行了 stopPropagation 方法，则会导致其他 React 事件失效。因为所有元素的事件将无法冒泡到 document 上，导致所有的 React 事件都将无法被触发

## 虚拟 dom 是什么

在原生的 JavaScript 程序中，我们直接对 DOM 进行创建和更改，而 DOM 元素通过我们监听的事件和我们的应用程序进行通讯。

而 React 会先将你的代码转换成一个 JavaScript 对象，然后这个 JavaScript 对象再转换成真实 DOM。这个 JavaScript 对象就是所谓的虚拟 DOM。

当我们需要创建或更新元素时， React 首先会让这个 VitrualDom 对象进行创建和更改，然后再将 VitrualDom 对象渲染成真实 DOM。

当我们需要对 DOM 进行事件监听时，首先对 VitrualDom 进行事件监听， VitrualDom 会代理原生的 DOM 事件从而做出响应。

## react 如何进行组件/逻辑复用

1. HOC(高阶组件)
2. react-hooks
3. 渲染属性(render props): 即通过 render 属性传递渲染内容

## 高阶组件

![高阶组件](/images/高阶组件.png)

高阶组件就是一个函数，且该函数接受一个组件作为参数，并返回一个新的组件。

### 通过属性代理实现高阶组件

函数返回一个我们自己定义的组件，然后在 render 中返回要包裹的组件，这样我们就可以代理所有传入的 props，并且决定如何渲染

```jsx
function proxyHOC(WrappedComponent) {
  return class extends Component {
    render() {
      return <WrappedComponent {...this.props} />;
    }
  };
}
```

对比原生组件增强的项:

- 可操作所有传入的 props
- 可操作组件的生命周期
- 可操作组件的 static 方法
- 获取 refs

### 通过反向继承实现高阶组件

返回一个组件，继承原组件，在 render 中调用原组件的 render。由于继承了原组件，能通过 this 访问到原组件的 生命周期、props、state、render 等，相比属性代理它能操作更多的属性

```jsx
function inheritHOC(WrappedComponent) {
  return class extends WrappedComponent {
    render() {
      return super.render();
    }
  };
}
```

对比原生组件增强的项:

- 可操作所有传入的 props
- 可操作组件的生命周期
- 可操作组件的 static 方法
- 获取 refs
- 可操作 state
- 可以渲染劫持

### HOC 在业务场景中有哪些实际应用场景

HOC 可以实现的功能：

- 组合渲染
- 条件渲染
- 操作 props
- 获取 refs
- 状态管理
- 操作 state
- 渲染劫持

HOC 在业务中的实际应用场景：

- 日志打点
- 权限控制
- 双向绑定
- 表单校验

## react hooks

### 使用 react hooks 好处是什么

hooks 通常支持提取和重用跨多个组件通用的有状态逻辑，而无需承担高阶组件或渲染 props 的负担。hooks 可以轻松地操作函数组件的状态，而不需要将它们转换为类组件

Hooks 在类中不起作用，通过使用它们，咱们可以完全避免使用生命周期方法，例如 componentDidMount、componentDidUpdate、componentWillUnmount。相反，使用像 useEffect 这样的内置钩子。

### React Hooks 的使用注意事项和问题

- 只能在函数内部的最外层调用 hook，不要在循环、条件判断或者子函数中调用

> 因为 react hooks 内部是通过单项链表实现的，其内部的顺序是极其关键的，如果不按照规则使用，就会造成取值出现偏移的问题

- 只能在 React 的函数组件中调用 Hook，不要在其他的 js 函数里调用

- 为什么 useEffect 的第二个参数是空数组，就相当于 componentDidMount 只执行一次

- 自定义的 hook 是怎样操作组件的

### react hooks 如何保存状态

- react hooks 和 类组件的状态值都被挂载在组件实例对象 FiberNode 的 memoizedState 属性中
- react hooks 和 类组件的数据结构完全不同。类组件是直接把 state 属性中挂载的这个开发者自定义的对象给保存到 memoizedState 属性中；而 React Hooks 是用链表来保存状态的，memoizedState 属性保存的实际上是这个链表的头指针。
- 所有的 hooks 都以链表的形式挂载在 FiberNode.updateQueue 中

### react hooks 常用 api

- 基础 hooks

  1. useState: 状态钩子，为函数组件提供内部状态
  2. useEffect: 副作用钩子，提供了类似于 componentDidMount 等生命周期钩子的功能
  3. useContext: 共享钩子，在组件之间共享状态，可以解决 react 逐层通过 props 传递数据

- 额外的 hooks
  1. useReducer: action 钩子，提供了状态管理，其基本原理是通过用户在页面上发起的 action，从而通过 reduce 方法来改变 state，从而实现页面和状态的通信，使用很像 redux
  2. useCallBack: 把内联回调函数及依赖项数组作为参数传入 useCallback, 它将返回该回调函数的 memoized 版本, 该回调函数仅在某个依赖项改变时才会更新
  3. useMemo: 把创建函数和依赖项数组作为参数传入 useMemo, 它仅会在某个依赖项改变时重新计算, 可以作为性能优化的手段。
  4. useRef: 获取组件的实例，返回一个可变的 ref 对象, 返回的 ref 对象在组件的整个生命周期内保持不变
  5. useLayoutEffect: 它会在所有 DOM 变更后同步调用 effect

## react fiber

### 什么是 fiber

- fiber 是一种基于浏览器的单线程调度算法
- fiber 是一个执行单元, 每次执行完一个执行单元, react 就会检查还剩多少时间, 如果没有时间则将控制权转让。(利用浏览器的 requestIdleCallback api 实现, 其中如果浏览器一直很忙，则会判断 requestIdleCallback 的 timeout 参数，如果超过 timeout 的值，则会在下一帧强制执行)
- fiber 是一种数据结构, react fiber 就是采用链表实现的, 每个虚拟 dom 都可以表示为一个 fiber
- fiber 实现了自己的组件调用栈, 它以链表的形式遍历组件树, 可以灵活的暂停、继续和丢弃执行的任务

> 利用 requestIdleCallback 的回调可以充分的通过利用浏览器空闲时间来解决任务调度问题。由于 requestIdleCallback 的兼容性很差，所以 react 采用了 messageChannel 模拟实现了这一 api 的功能。

> 当浏览器很忙的时候，如 1000 / 60 ～ 16ms 时间间隔内都有任务执行，则会通过判断 requestIdleCallback 的 timeout 是否超时和该回调中的任务的优先级来强制在下一帧执行回调中的任务，避免卡顿现象。

> react 里预订了 5 个优先级等级
> 
> 1. Immediate: 最高优先级, 这个优先级的任务应该被马上执行不能中断
> 2. UserBlocking: 这些任务一般是用户交互的结果, 需要即时得到反馈
> 3. Normal: 不需要用户立即就感受到变化, 比如网络请求
> 4. Low: 这些任务可以延后, 但是最终也需要执行
> 5. Idle: 可以被无限期延后

### fiber 解决的问题(背景)

- 为了使 react 渲染过程中可以被中断，可以将控制权交还给浏览器, 可以让位给高优先级的任务，浏览器空闲后再恢复渲染。
- 对于计算量比较大的 js 计算或 dom 计算，就不会显得特别卡顿，而是一帧一帧的有规律的执行任务。
- react 16 之前采用的是递归 diff, 想要中断递归是很困难的。
- 为了解决这个问题，我们将大型的计算拆分成一个个小型的计算，然后按照执行顺序异步调用，这样就不会长时间霸占线程，UI 也能够在两次计算执行的间隙进行更新，从而给用户及时的反馈。

### fiber 实现原理

- 拆分：把渲染过程进行拆分成多个小任务
- 检查：每次执行完一个小任务，就去队列中检查是否有新的响应需要处理
- 继续执行：如果有就执行优化及更高的响应事件，如果没有继续执行后续任务

### fiber 数据结构

Fiber 其实指的是一种数据结构，它可以用一个纯 JS 对象来表示：

```js
const fiber = {
    stateNode,    // 节点实例
    child,        // 子节点
    sibling,      // 兄弟节点
    return,       // 父节点
};
```

fiber 是个链表，有 child 和 sibing 属性，指向第一个子节点和相邻的兄弟节点，从而构成 fiber tree。return 属性指向其父节点

- 更新队列，updateQueue，是一个链表，有 first 和 last 两个属性，指向第一个和最后一个 update 对象
- 每个 fiber 有一个属性 updateQueue 指向其对应的更新队列。
- 每个 fiber（当前 fiber 可以称为 current）有一个属性 alternate，开始时指向一个自己的 clone 体，update 的变化会先更新到 alternate 上，当更新完毕，alternate 替换 current。

### fiber 的基本规则

- 调和阶段
  找出需要更新的工作 (Diff Fiber Tree), 就是一个计算阶段，计算结果可以被缓存，也可被打断

- 交付阶段
  提交所有更新并渲染，为了防止页面抖动，不能被打断

### fiber 的执行流程

1. 用户操作引起 setState 被调用，进而初始化一些数据结构
2. 根据优先级插入队列相应位置，初始化两个更新的队列
3. 开始进行任务分片调度，首先更新每个 fiber 的优先级，当 fiber 返回 null 时找到父级节点，然后将所有变化归到 root
4. 把当前的更新添加到调度队列中，根据当前是否异步渲染，做异步调用
5. 判断浏览器空闲时，完成下一个分片的工作，如果没有工作完，将会放弃
6. 执行调和阶段和调度阶段

## setState

### setState 到底是异步还是同步

有时表现出异步，有时表现出同步

- setState 只在合成事件和钩⼦函数中是 "异步" 的，在 原⽣事件 和 setTimeout 中都是同步的
- setState 的 "异步" 并不是说内部由异步代码实现，其实本身执⾏的过程和代码都是同步的，只是 合成事件 和 钩⼦函数 的 调⽤顺序在更新之前，导致在合成事件和钩⼦函数中没法⽴⻢拿到更新后的值，形成了所谓的 "异步"，当然可以通过第⼆个参数 setState(partialState, callback)中的 callback 拿到更新后的结果
- setState 的批量更新优化也是建⽴在 "异步"（合成事件、钩⼦函数）之上的，在 原⽣事件 和 setTimeout 中不会批量更新，在 "异步" 中如果对同⼀个值进⾏多次 setState，setState 的批量更新策略会对其进⾏覆盖，取最后⼀次的执⾏，如果是同时 setState 多个不同的值，在更新时会对其进⾏合并批量更新。

### 为什么 setState 不设计成同步的

- 保持内部的一致性和状态的安全性

state, props, refs 一致性

- 性能优化

react 会对依据不同的调用源，给不同的 setState 调用分配不同的优先级

调用源包括：事件处理、网络请求、动画

- 更多可能性

异步获取数据后，统一渲染页面, 保持一致性

## redux

### redux 的⼯作流程

- Store：保存数据的地⽅，你可以把它看成⼀个容器，整个应⽤只能有⼀个 Store；
- State：Store 对象包含所有数据，如果想得到某个时点的数据，就要对 Store ⽣成快照，这种时点的数据集合，就叫 State；
- Action： State 的变化， 会导致 View 的变化。但是，⽤户接触不到 State，只能接触到 View。所以，State 的变化必须是 View 导致的。Action 就是 View 发出的通知，表示 State 应该要发⽣变化了；
- Action Creator：View 要发送多少种消息，就会有多少种 Action。如果都⼿写，会很麻烦，所以我们定义⼀个函数来⽣成 Action，这个函数就叫 Action Creator；
- Reducer：Store 收到 Action 以后，必须给出⼀个新的 State，这样 View 才会发⽣变化。这种 State 的计算过程就叫做 Reducer。Reducer 是⼀个函数，它接受 Action 和当前 State 作为参数，返回⼀个新的 State；
- dispatch：是 View 发出 Action 的唯⼀⽅法。

1. 首先，用户通过 View 发出 Action，发出方式用到了 dispatch 方法
2. Store 调用 Reducer, 传入两个参数: 当前 State 和收到的 Action，Reducer 会返回新的 State
3. State ⼀旦有变化，Store 就会调⽤监听函数，来更新 View

## mobx

### mobx 的工作流程

![mobx工作流程图](/images/mobx流程.png)

### mobx 数据流向

触发 action，在 action 中修改 state，通过 computed 拿到 state 的计算值，自动触发对应的 reactions，这里包含 autorun，渲染视图等。

> 有一点需要注意：相对于 react 来说，mobx 没有一个全局的状态树，状态分散在各个独立的 store 中。

### mobx 的工作原理

使用 Proxy 来拦截对数据的访问，一旦值发生变化，将会调用 react 的 render 方法来实现重新渲染视图的功能或者触发 autorun 等。

### Mobx 的核心原理

通过 action 触发 state 的变化，进而触发 state 的衍生对象（computed value & Reactions）

## redux 和 mobx 对比

1. Redux 要解决的问题是统一数据流，数据流完全可控并可追踪。要实现该目标，便需要进行相关的约束
2. Redux 由此引出 dispatch action reducer 等概念，对 state 的概念进行强约束，然而对于一些项目来说，太过强，便失去了灵活性。Mobx 便是填补此空缺的
3. redux 将数据保存在单一的 store 中，mobx 将数据保存在分散的多个 store 中
4. redux 使用 plain object 保存数据，需要手动处理变化后的操作；mobx 使用 observable 保存数据，数据变化后自动处理响应的操作
5. redux 使用不可变状态，这意味着状态是只读的，不能直接去修改它，而是应该返回一个新的状态，同时使用纯函数；mobx 中的状态是可变的，可以直接对其进行修改
6. mobx 相对来说比较简单，在其中有很多的抽象，mobx 更多的使用面向对象的编程思维；redux 会比较复杂，因为其中的函数式编程思想掌握起来不是那么容易，同时需要借助一系列的中间件来处理异步和副作用
7. mobx 中有更多的抽象和封装，调试会比较困难，同时结果也难以预测；而 redux 提供能够进行时间回溯的开发工具，同时其纯函数以及更少的抽象，让调试变得更加的容易
8. 使用 mobx 的 react 感觉很像 vue
