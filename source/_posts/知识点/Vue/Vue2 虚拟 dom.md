---
title: Vue2 虚拟 dom
date: 2020-07-18
categories: [知识点, Vue]
tags:
  - 虚拟 Dom
---

## 关键点

### 1.Vnode 对象

> 一个 `VNode` 的实例对象包含了以下属性:

- `tag`: 当前节点的标签名
- `data`: 当前节点的数据对象，具体包含哪些字段可以参考 `vue` 源码 `types/vnode.d.ts` 中对 `VNodeData` 的定义
- `children`: 数组类型，包含了当前节点的子节点
- `text`: 当前节点的文本，一般文本节点或注释节点会有该属性
- `elm`: 当前虚拟节点对应的真实的 `dom` 节点
- `ns`: 节点的 `namespace`
- `context`: 编译作用域
- `functionalContext`: 函数化组件的作用域
- `key`: 节点的 `key` 属性，用于作为节点的标识，有利于 `patch` 的优化
- `componentOptions`: 创建组件实例时会用到的选项信息
- `child`: 当前节点对应的组件实例
- `parent`: 组件的占位节点
- `raw`: `raw html`
- `isStatic`: 静态节点的标识
- `isRootInsert`: 是否作为根节点插入，被 `<transition>` 包裹的节点，该属性的值为 `false`
- `isComment`: 当前节点是否是注释节点
- `isCloned`: 当前节点是否为克隆节点
- `isOnce`: 当前节点是否有 `v-once` 指令

### 2.Vnode 分类

- 简介

> `VNode` 可以理解为 `vue` 框架的虚拟 `dom` 的基类，通过 `new` 实例化的 `VNode` 大致可以分为几类

- `EmptyVNode`: 没有内容的注释节点
- `TextVNode`: 文本节点
- `ElementVNode`: 普通元素节点
- `ComponentVNode`: 组件节点
- `CloneVNode`: 克隆节点，可以是以上任意类型的节点，唯一的区别在于 `isCloned` 属性为 `true`

### 3.createElement 解析

```javascript
const SIMPLE_NORMALIZE = 1;
const ALWAYS_NORMALIZE = 2;

function createElement(context, tag, data, children, normalizationType, alwaysNormalize) {
  // 兼容不传data的情况
  if (Array.isArray(data) || isPrimitive(data)) {
    normalizationType = children;
    children = data;
    data = undefined;
  }
  // 如果alwaysNormalize是true
  // 那么normalizationType应该设置为常量ALWAYS_NORMALIZE的值
  if (alwaysNormalize) normalizationType = ALWAYS_NORMALIZE;
  // 调用_createElement创建虚拟节点
  return _createElement(context, tag, data, children, normalizationType);
}

function _createElement(context, tag, data, children, normalizationType) {
  /**
   * 如果存在data.__ob__，说明data是被Observer观察的数据
   * 不能用作虚拟节点的data
   * 需要抛出警告，并返回一个空节点
   *
   * 被监控的data不能被用作vnode渲染的数据的原因是：
   * data在vnode渲染过程中可能会被改变，这样会触发监控，导致不符合预期的操作
   */
  if (data && data.__ob__) {
    process.env.NODE_ENV !== "production" && warn(`Avoid using observed data object as vnode data: ${JSON.stringify(data)}\n` + "Always create fresh vnode data objects in each render!", context);
    return createEmptyVNode();
  }
  // 当组件的is属性被设置为一个falsy的值
  // Vue将不会知道要把这个组件渲染成什么
  // 所以渲染一个空节点
  if (!tag) {
    return createEmptyVNode();
  }
  // 作用域插槽
  if (Array.isArray(children) && typeof children[0] === "function") {
    data = data || {};
    data.scopedSlots = { default: children[0] };
    children.length = 0;
  }
  // 根据normalizationType的值，选择不同的处理方法
  if (normalizationType === ALWAYS_NORMALIZE) {
    children = normalizeChildren(children);
  } else if (normalizationType === SIMPLE_NORMALIZE) {
    children = simpleNormalizeChildren(children);
  }
  let vnode, ns;
  // 如果标签名是字符串类型
  if (typeof tag === "string") {
    let Ctor;
    // 获取标签名的命名空间
    ns = config.getTagNamespace(tag);
    // 判断是否为保留标签
    if (config.isReservedTag(tag)) {
      // 如果是保留标签,就创建一个这样的vnode
      vnode = new VNode(config.parsePlatformTagName(tag), data, children, undefined, undefined, context);
      // 如果不是保留标签，那么我们将尝试从vm的components上查找是否有这个标签的定义
    } else if ((Ctor = resolveAsset(context.$options, "components", tag))) {
      // 如果找到了这个标签的定义，就以此创建虚拟组件节点
      vnode = createComponent(Ctor, data, context, children, tag);
    } else {
      // 兜底方案，正常创建一个vnode
      vnode = new VNode(tag, data, children, undefined, undefined, context);
    }
    // 当tag不是字符串的时候，我们认为tag是组件的构造类
    // 所以直接创建
  } else {
    vnode = createComponent(tag, data, context, children);
  }
  // 如果有vnode
  if (vnode) {
    // 如果有namespace，就应用下namespace，然后返回vnode
    if (ns) applyNS(vnode, ns);
    return vnode;
    // 否则，返回一个空节点
  } else {
    return createEmptyVNode();
  }
}
```

### 4.patch 原理

#### patch 函数接收 6 个参数

1. `oldVnode`: 旧的虚拟节点或旧的真实 `dom` 节点
2. `vnode`: 新的虚拟节点
3. `hydrating`: 是否要跟真实 `dom` 混合
4. `removeOnly`: 特殊 `flag`，用于 `<transition-group>` 组件
5. `parentElm`: 父节点
6. `refElm`: 新节点将插入到 `refElm` 之前

#### patch 的策略

- 如果`vnode` 不存在但是 `oldVnode` 存在，说明意图是要销毁老节点，那么就调用 `invokeDestroyHook(oldVnode)` 来进行销毁
- 如果 `oldVnode` 不存在但是 `vnode` 存在，说明意图是要创建新节点，那么就调用 `createElm` 来创建新节点
- 当 `vnode` 和 `oldVnode` 都存在时
  - 如果 `oldVnode` 和 `vnode` 是同一个节点，就调用 `patchVnode` 来进行 `patch`
  - 当 `vnode` 和 `oldVnode` 不是同一个节点时，如果 `oldVnode` 是真实 `dom` 节点 或 `hydrating` 设置为`true`，需要用 `hydrate` 函数将虚拟 `dom` 和真实 `dom` 进行映射，然后将 `oldVnode` 设置为对应的虚拟 `dom`，找到 `oldVnode.elm` 的父节点，根据 `vnode` 创建一个真实 `dom` 节点并插入到该父节点中 `oldVnode.elm` 的位置

> 这里面值得一提的是 `patchVnode` 函数，因为真正的 `patch` 算法是由它来实现的（`patchVnode` 中更新子节点的算法其实是在 `updateChildren` 函数中实现的

#### patchVnode 算法

1. 如果 `oldVnode` 跟 `vnode` 完全一致，那么不需要做任何事情
2. 如果 `oldVnode` 跟 `vnode` 都是静态节点，且具有相同的 `key`，当 `vnode` 是克隆节点或是 `v-once` 指令控制的节点时，只需要把 `oldVnode.elm` 和 `oldVnode.child` 都复制到 `vnode` 上，也不用再有其他操作
3. 否则，如果`vnode`不是文本节点或注释节点

- 如果 `oldVnode`和 `vnode` 都有子节点，且双方的子节点不完全一致，就执行更新子节点的操作（这一部分其实是在 `updateChildren` 函数中实现），算法如下:

> 分别获取 `oldVnode` 和 `vnode` 的 `firstChild`、`lastChild`，赋值给 `oldStartVnode`、`oldEndVnode`、`newStartVnode`、`newEndVnode`

> 如果 `oldStartVnode` 和 `newStartVnode` 是同一节点，调用 `patchVnode` 进行 `patch`，然后将 `oldStartVnode` 和 `newStartVnode` 都设置为下一个子节点，重复上述流程
> ![virtual_dom1.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608704111320-57210d35-7b20-45f7-87b1-8a6a541754bc.png#align=left&display=inline&height=204&margin=%5Bobject%20Object%5D&name=virtual_dom1.png&originHeight=204&originWidth=667&size=20067&status=done&style=none&width=667)

> 如果 `oldEndVnode` 和 `newEndVnode` 是同一节点，调用 `patchVnode` 进行 `patch`，然后将 `oldEndVnode` 和 `newEndVnode` 都设置为上一个子节点，重复上述流程
> ![virtual_dom2.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608704123342-e1a719db-cacd-4fe3-a4b5-730e73dec203.png#align=left&display=inline&height=221&margin=%5Bobject%20Object%5D&name=virtual_dom2.png&originHeight=221&originWidth=676&size=19741&status=done&style=none&width=676)

> 如果 `oldStartVnode` 和 `newEndVnode` 是同一节点，调用 `patchVnode` 进行 `patch`，如果 `removeOnly` 是`false`，那么可以把 `oldStartVnode.elm` 移动到 `oldEndVnode.elm` 之后，然后把 `oldStartVnode` 设置为下一个节点，`newEndVnode` 设置为上一个节点，重复上述流程![virtual_dom3.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608704137134-da7b6eab-d7ab-4e0d-bd3c-816548aa7050.png#align=left&display=inline&height=224&margin=%5Bobject%20Object%5D&name=virtual_dom3.png&originHeight=224&originWidth=826&size=24829&status=done&style=none&width=826)

> 如果 `newStartVnode` 和 `oldEndVnode` 是同一节点，调用 `patchVnode` 进行 `patch`，如果 `removeOnly` 是 `false`，那么可以把 `oldEndVnode.elm` 移动到 `oldStartVnode.elm` 之前，然后把 `newStartVnode` 设置为下一个节点，`oldEndVnode` 设置为上一个节点，重复上述流程
> ![virtual_dom4.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608704166102-db7c7879-3e36-49db-9e96-9f587cfcb771.png#align=left&display=inline&height=214&margin=%5Bobject%20Object%5D&name=virtual_dom4.png&originHeight=214&originWidth=864&size=24556&status=done&style=none&width=864)

> 如果以上都不匹配，就尝试在 `oldChildren` 中寻找跟 `newStartVnode` 具有相同 `key` 的节点，如果找不到相同 `key`的节点，说明 `newStartVnode` 是一个新节点，就创建一个，然后把 `newStartVnode` 设置为下一个节点

> 如果上一步找到了跟 `newStartVnode` 相同 `key` 的节点，那么通过其他属性的比较来判断这 2 个节点是否是同一个节点，如果是，就调用 `patchVnode` 进行 `patch`，如果 `removeOnly` 是 `false`，就把 `newStartVnode.elm` 插入到 `oldStartVnode.elm` 之前，把 `newStartVnode` 设置为下一个节点，重复上述流程
> ![virtual_dom5.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608704181185-2af64dae-c1a4-4505-aeeb-5ec7efad8930.png#align=left&display=inline&height=227&margin=%5Bobject%20Object%5D&name=virtual_dom5.png&originHeight=227&originWidth=869&size=24398&status=done&style=none&width=869)

- 如果只有 `oldVnode` 有子节点，那就把这些节点都删除
- 如果只有 `vnode` 有子节点，那就创建这些子节点
- 如果 `oldVnode和vnode` 都没有子节点，但是 `oldVnode` 是文本节点或注释节点，就把 `vnode.elm` 的文本设置为空字符串

4. 如果 `vnode` 是文本节点或注释节点，但是 `vnode.text != oldVnode.text` 时，只需要更新 `vnode.elm` 的文本内容就可以

### 生命周期

- `patch` 提供了 `5` 个生命周期钩子，分别是
  - `create`: 创建 `patch` 时
  - `activate`: 激活组件时
  - `update`: 更新节点时
  - `remove`: 移除节点时
  - `destroy`: 销毁节点时
- `vnode`也提供了生命周期钩子，分别是
  - `init`: `vdom` 初始化时
  - `create`: `vdom` 创建时
  - `prepatch`: `patch` 之前
  - `insert`: `vdom` 插入后
  - `update`: `vdom` 更新前
  - `postpatch`: `patch` 之后
  - `remove`: `vdom` 移除时
  - `destroy`: `vdom` 销毁时
