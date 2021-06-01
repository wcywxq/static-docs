---
title: Vue 面试精选
date: 2021-05-23
categories: [面试, Vue]
---

## Vue 响应式数据原理

- 整体思路: 数据劫持 + 观察者模式(发布-订阅者模式)
- 通过 Object.defineProperty(Proxy) 来劫持各个属性的 getter 和 setter
- 当页面使用对应属性时，首先进行依赖收集(收集当前组件的 watcher)
- 如果属性发生变化, 发布消息给订阅者, 触发响应的监听回调

具体步骤:

1. 对 Observer 对象进行递归遍历, 包括子属性对象的属性，都加上 setter 和 getter。这样的话，给这个对象的某个值赋值，就会触发 setter，那么就能监听到变化。
2. complile 解析模版指令，将模版中的变量替换成数据，然后初始化渲染页面视图，并将每个指令对应的节点绑定更新函数，添加监听数据的订阅者，一旦数据发生变动，收到通知后更新视图
3. 待变动属性 dep.notice() 通知时，能调用自身的 update() 方法，并触发 Compile 中绑定的回调，则功成身退
4. MVVM 作为数据绑定的入口，整合 Observer、Compile 和 Watcher 三者，通过 Observer 来监听自己的 model 数据变化，通过 Compile 来解析编译模版指令，最终利用 Watcher 搭建起 Observer 和 Compile 之间的通信桥梁，达到数据变化 -> 视图更新；视图交互变化 -> 数据 model 变更的双向绑定的效果

## Vue 中 key 的作用

如果不使用 key，Vue 会使用一种最大限度减少动态元素并且尽可能的尝试就地修改/复用相同类型元素的算法。key 是为 Vue 中 vnode 的唯一标记，通过这个 key，我们的 diff 操作可以更准确、更快速

更准确：因为带 key 就不是就地复用了，在 sameNode 函数 a.key === b.key 对比中可以避免就地复用的情况。所以会更加准确。

更快速：利用 key 的唯一性生成 map 对象来获取对应节点，比遍历方式更快

## Vue 事件绑定原理

原生事件: 通过 addEventListener 绑定给真实元素
组件事件: 通过 Vue 自定义的 $on 实现; 在组件上使用原生事件, 需要加 .native 修饰符(相当于父组件中把子组件当作普通的 html 标签, 然后加上原生事件)

on、emit 是基于发布订阅模式的, 维护一个事件中心, on 的时候将事件按名称存在事件中心里，即订阅者。然后 emit 将对应事件发布，去执行事件中心里对应的监听器。

## Vue-Router 路由钩子函数和执行顺序

钩子函数的种类分为: 全局守卫、路由守卫、组件守卫

### 完整的导航解析流程

1. 导航被触发。
2. 在失活的组件里调用 beforeRouteLeave 守卫。
3. 调用全局的 beforeEach 守卫。
4. 在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
5. 在路由配置里调用 beforeEnter。
6. 解析异步路由组件。
7. 在被激活的组件里调用 beforeRouteEnter。
8. 调用全局的 beforeResolve 守卫 (2.5+)。
9. 导航被确认。
10. 调用全局的 afterEach 钩子。
11. 触发 DOM 更新。
12. 调用 beforeRouteEnter 守卫中传给 next 的回调函数，创建好的组件实例会作为回调函数的参数传入。

## 什么是 nextTick

在下次 DOM 更新循环结束之后执行延迟回调。nextTick 主要使用了宏任务和微任务。根据执行环境分别尝试采用

- Promise
- MutationObserver
- setImmediate

如果以上都不行则采用 setTimeout

## Vue 生命周期

1. beforeCreate: new Vue()之后触发的第一个钩子, data、methods、computed 以及 watch 上的数据和方法都不能被访问。
2. created: 当前阶段已经完成了数据观测，可以使用数据，更改数据，在这里更改数据不会触发 updated 函数。可以做一些初始数据的获取，在当前阶段无法与 Dom 进行交互，如果非要想，可以通过 vm.$nextTick 来访问 Dom。
3. beforeMount 发生在挂载之前，在这之前 template 模板已导入渲染函数编译。而当前阶段虚拟 Dom 已经创建完成，即将开始渲染。在此时也可以对数据进行更改，不会触发 updated。
4. mounted: 真实的 Dom 挂载完毕，数据完成双向绑定，可以访问到 Dom 节点，使用 $refs 属性对 Dom 进行操作。
5. beforeUpdate: 也就是响应式数据发生更新，虚拟 dom 重新渲染之前被触发，可以在当前阶段进行更改数据，不会造成重渲染。
6. updated: 当前阶段组件 Dom 已完成更新。要注意的是避免在此期间更改数据，因为这可能会导致无限循环的更新。
7. beforeDestroy: 在当前阶段实例完全可以被使用，我们可以在这时进行善后收尾工作，比如清除计时器。
8. destroyed: 这个时候只剩下了 dom 空壳。组件已被拆解，数据绑定被卸除，监听被移出，子实例也统统被销毁。

## Vue 组件中的 data 为什么是函数

一个组件被复用多次的话，也就会创建多个实例。本质上，这些实例用的都是同一个构造函数。如果 data 是对象的话，对象属于引用类型，会影响到所有的实例。所以为了保证组件不同的实例之间 data 不冲突，data 必须是一个函数。

## Vue 模版的编译原理

Vue 的编译过程就是将 template 转化 为 render 函数的过程。会经历以下阶段

1. 首先解析模版，生成 AST 语法树(一种用 JavaScript 对象的形式来描述整个模板)
2. 使用大量的正则表达式对模板进行解析，遇到标签、文本的时候都会执行对应的钩子进行相关处理
3. Vue 的数据是响应式的，但其实模板中并不是所有的数据都是响应式的。有一些数据首次渲染后就不会再变化，对应的 DOM 也不会变化。那么优化过程就是深度遍历 AST 树，按照相关条件对树节点进行标记。这些被标记的节点(静态节点)我们就可以跳过对它们的比对，对运行时的模板起到很大的优化作用。

## Vue2 和 Vue3 渲染器的 diff 算法

简单来说, diff 算法有以下过程:

1. 同级比较, 再比较子节点
2. 先判断一方有子节点一方没有子节点的情况(如果新的 children 没有子节点，将旧的子节点移除)
3. 比较都有子节点的情况(核心 diff)
4. 递归比较子节点

正常 Diff 两个树的时间复杂度是 O(n^3)，但实际情况下我们很少会进行跨层级的移动 DOM，所以 Vue 将 Diff 进行了优化，从 O(n^3) -> O(n)，只有当新旧 children 都为多个子节点时才需要用核心的 Diff 算法进行同层级比较。

Vue2 的核心 Diff 算法采用了 **双端比较** 的算法，同时从新旧 children 的两端开始进行比较，借助 key 值找到可复用的节点，再进行相关操作。相比 React 的 Diff 算法，同样情况下可以减少移动节点次数，减少不必要的性能损耗，更加的优雅。

Vue3.x 借鉴了 ivi 算法和 inferno 算法, 在创建 VNode 时就确定其类型，以及在 mount/patch 的过程中采用位运算来判断一个 VNode 的类型，在这个基础之上再配合核心的 Diff 算法，使得性能上较 Vue2.x 有了提升。(实际的实现可以结合 Vue3.x 源码看。)
该算法中还运用了动态规划的思想求解最长递归子序列。

## Vue 的 Keep-alive

keep-alive 可以实现组件缓存，当组件切换时不会对当前组件进行卸载。

常用的两个属性 include/exclude，允许组件有条件的进行缓存。

两个生命周期 activated(命中缓存时调用)/deactivated(切换时调用)，用来得知当前组件是否处于活跃状态。

keep-alive 的中还运用了 LRU(Least Recently Used)算法。

## Vue 的 computed 和 watch 的差异

computed: 计算一个新属性，挂载到实例上(当依赖发生变化后才会重新计算)。

watch: 监听已经被挂载到实例上的数据，数据发生变化了就会调用。

## Vue 生命周期调用顺序

组件的调用顺序都是先父后子, 渲染完成的顺序是先子后父。

组件的销毁操作是先父后子，销毁完成的顺序是先子后父。

## Vue SSR

SSR 也就是服务端渲染，也就是将 Vue 在客户端把标签渲染成 HTML 的工作放在服务端完成，然后再把 html 直接返回给客户端。

SSR 有着更好的 SEO、并且首屏加载速度更快等优点。不过它也有一些缺点，比如我们的开发条件会受到限制，服务器端渲染只支持 beforeCreate 和 created 两个钩子，当我们需要一些外部扩展库时需要特殊处理，服务端渲染应用程序也需要处于 Node.js 的运行环境。还有就是服务器会有更大的负载需求。

## Vue 性能优化

### 编码阶段

- 尽量减少 data 中的数据，data 中的数据都会增加 getter 和 setter，会收集对应的 watcher
- 对象层级不要过深
- 不需要响应式的数据不要放在 data 中(可以使用 Object.freeze() 冻结数据)
- v-if/v-show, computed/watch 区分使用场景
- v-for 避免同时使用 v-if, 同时要 key, key 最好是唯一值
- 如果需要使用 v-for 给每项元素绑定事件时使用事件代理
- 大数据列表和表格性能优化(虚拟列表/虚拟表格), 长列表滚动到可视区域动态加载
- 防止内部泄漏, 组件销毁后把全局事件和事件销毁
- 图片/路由懒加载
- 适当使用 keep-alive 缓存组件
- 第三方模块按需引入
- 防抖节流

### SEO 优化

- 服务端 SSR / 服务端预渲染

### 打包优化

- webpack 相关打包优化

### 用户体验

- 骨架屏
- PWA

## hash 路由和 history 路由实现原理说一下

location.hash 的值实际就是 URL 中#后面的东西。

history 实际采用了 HTML5 中提供的 API 来实现，主要有 history.pushState()和 history.replaceState()。

## Vuex

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。每一个 Vuex 应用的核心就是 store（仓库）。store 基本上就是一个容器，它包含应用中大部分的状态 ( state )。

1. Vuex 的状态存储是响应式的。当 Vue 组件从 store 中读取状态的时候，若 store 中的状态发生变化，那么相应的组件也会相应地得到高效更新。
2. 改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation。这样使得我们可以方便地跟踪每一个状态的变化。

主要包括以下几个模块：

- State：定义了应用状态的数据结构，可以在这里设置默认的初始状态
- Getter：允许组件从 Store 中获取数据，mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性
- Mutation：是唯一更改 store 中状态的方法，且必须是同步函数
- Action：用于提交 mutation，而不是直接变更状态，可以包含任意异步操作
- Module：允许将单一的 Store 拆分为多个 store 且同时保存在单一的状态树中

## proxy

### 什么是 proxy

> Proxy 对象用于定义基本操作的自定义行为(属性查找、赋值、枚举、函数调用等)。对象代理模式
> target: 目标对象
> handler: 行为
> const proxy = new Proxy(target, handler)

### 为什么使用 proxy? proxy 利弊

> vue3 带来了基于 proxy 的观察者模式的实现, 提供了全范围响应式的能力, 消除了 vue2 基于 Object.defineProperty 存在的局限性
> 局限性包含以下几点:
>
> 1. 对属性添加、删除动作的检测
> 2. 对数组基于下标修改、对于数组长度修改的检测
> 3. 对于 Map、Set、WeakMap、WeakSet 支持(使用 WeakMap, WeakSet 的好处: 弱引用, 避免内存泄漏, 不会阻止垃圾回收器回收它所引用的 key, 必须非空且需要引用数据类型作为 key)

### proxy 中 handler 对象基本用法

```js
// has 捕获器(拦截判断 target 对象是否含有属性 propKey 的操作)
const handler = {
  has(target, propKey) {
    return propKey in target;
  }
};
const proxy = new Proxy(target, handler);

// get 捕获器(拦截对象属性的读取)
const handler = {
  get(target, propKey) {
    return propKey in target ? target[propKey] : "no";
  }
};
const proxy = new Proxy({}, handler);
proxy.apple = "苹果";
proxy.banana = "香蕉";
console.log(proxy.apple, proxy.banana);
console.log("watermalon" in proxy, proxy.watermalon);

// set 捕获器(拦截对象的属性赋值操作)
let validator = {
  set(obj, prop, value, receiver) {
    if (prop === "age") {
      if (!Number.isInteger(value)) {
        throw new TypeError("The age is not an integer");
      }
      if (value > 200) {
        throw new RangeError("The age seems invalid");
      }
    }
    obj[prop] = value;
    return true;
  }
};
const proxy = new Proxy({}, validator);
proxy.age = 100;
console.log(proxy.age); // 100
proxy.age = "young"; // Error
proxy.age = 300; // Error

// deleteProperty 捕获器(拦截删除 target 对象的 propKey 属性的操作)
const foot = { apple: "苹果", banana: "香蕉" };
const proxy = new Proxy(foot, {
  deleteProperty(target, prop) {
    console.log("当前删除水果 :", target[prop]);
    return delete target[prop];
  }
});
delete proxy.apple;
console.log(foot);
// 运行结果: '当前删除水果 : 苹果' {  banana:'香蕉'  }

// ownKeys 捕获器(拦截获取键值的操作)
let obj = { a: 10, [Symbol.for("foo")]: 2 };
Object.defineProperty(obj, "c", {
  value: 3,
  enumerable: false
});
let proxy = new Proxy(obj, {
  ownKeys(target) {
    return [...Reflect.ownKeys(target), "b", Symbol.for("bar")];
  }
});
const keys = Object.keys(proxy); // ["a"]
for (let prop in proxy) {
  console.log("prop-", prop);
}

const ownNames = Object.getOwnPropertyNames(proxy); // ['a', 'c', 'b']
const ownSymbols = Object.getOwnPropertySymbols(proxy); // [Symbol(foo), Symbol(bar)]
const ownKeys = Reflect.ownKeys(proxy); // ['a', 'c', Symbol(foo), 'b', Symbol(bar)]
```

## 
