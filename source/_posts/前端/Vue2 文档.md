---
title: Vue2 文档
date: 2020-07-15
categories: 
  - [前端, Vue]
  - [文档]
tags:
  - Vue
---

## 1. 父子组件传值

- 父子： `props`
- 子父： `this.$emit('事件名', 参数)`

## 2. 获取 dom 节点

> `this.$refs`， 可以获取组件的引用，同时可以获取组件的 `data，methods` 等

## 3. 路由

- 后端路由

> 对于普通的网站，所有的超链接都是 `URL` 地址，所有的 `URL` 地址都对应服务器上的资源。

- 前端路由

> 对于单页面应用程序（`SPA`），主要通过 `URL` 的 `hash(#)` 来实现不同页面之间的切换；同时，`hash` 有一个特点，`http` 请求中不会包含 `hash` 相关的内容

## 4. 命名视图 router-view

```vue
<template>
  <div id="app">
    <router-view></router-view>
    <router-view name="left"></router-view>
    <router-view name="main"></router-view>
  </div>
</template>

<script>
var header = {
  template: "<div>header</div>"
};
var leftBox = {
  template: "<div>left</div>"
};
var mainBox = {
  template: "<div>main</div>"
};

var router = new VueRouter({
  routes: [
    {
      path: "/",
      components: {
        default: header,
        left: leftBox,
        main: mainBox
      }
    }
  ]
});

var vm = new Vue({
  el: "#app",
  router
});
</script>
```

## 5. watch

```vue
<template>
  <input type="text" v-model="msg" />
</template>

<script>
data: {
   msg: ''
},
watch: {
  msg(newVal, oldVal) {
    console.log(newVal, oldVal)
  },
  "$route.path": (newVal, oldVal) => {
    console.log(newVal, oldVal)
  }
}
</script>
```

## 6. computed

- 在 `computed` 中，可以定义一些属性（计算属性）；计算属性的本质就是一个方法。只不过在我们这些计算属性的时候，是直接把他们的名称当作属性来使用的，并不会把计算属性当作方法来调用。
- 计算属性的求值结果，会被缓存起来，方便下次直接使用; 如果 `计算属性` 方法中，所依赖的所有数据，都没有发生变化，则不会重新对 `计算属性` 进行求值

```vue
<template>
  <input type="text" v-model="firstname" />
  <input type="text" v-model="lastname" />
  <input type="text" v-model="fullname" />
</template>

<script>
data: {
    firstname: '',
    lastname: ''
},
computed: {
    fullname() {
        return this.firstname + '-' + this.lastname;
    }
}
</script>
```

## 7. render

- `createElements` 是一个方法，调用它，能够把指定的组件模板，渲染为 `html` 结构
- 通常习惯性将 `createElements` 写成 `h`

```vue
<script>
var login = {
  template: "<h1>这是登陆组件</h1>"
};

var vm = new Vue({
  el: "#app",
  data: {},
  render: function (createElements) {
    return createElements(login);
  }
});
</script>
```

## 8. 模块的导入与导出

- `commenJs` 导入导出模块

> 导入

```javascript
var 名称 = require("模块标识符");
```

> 导出

```javascript
module.exports = {};
```

- `import` 导入导出模块

> 导入

```javascript
inport 名称A from '模块标识符'
inport { 名称B } '标识路径'
```

> 导出

```javascript
export default 名称A
export 名称B
```

> 重命名

```javascript
import 名称A as newA from '模块标识符'
import { 名称B as newB } from '标示路径'
```

## 9. vuex

### 介绍

- `vuex` 是 `vue.js` 应用程序开发的状态管理模式（状态可以理解为数据），它采用集中式存储，管理应用的所有组件的状态，并以相应的规则保证状态以一种可检测的方式发生变化

### props，data 和 vuex 的区别

- `props`: 存放父组件传递过来的数据
- `data`: 存放私有数据
- `vuex`: 组件之间共享的数据

### state，mutations，actions，getters，modules

- `state`：类似于 `data`，用来存数据

> 访问

```javascript
this.$store.state.数据a

...mapState
```

- `mutations`：类似于 `methods`，处理同步方法

> 使用

```javascript
mutations: {
    "METHODS": function(state, value) {
        state.value = value;
    }
}
```

> 访问

```javascript
this.$store.commit('方法名', '参数')

...mapMutations
```

- `actions`：类似于 `methods`，处理异步方法

> 使用

```javascript
actions: {
    async methodsAsync({ commit }, value) {
        try {
            const result = await apiMethods()
            if (result.code === 200) {
                commit("SET_METHODS", result.data)
            }
        } catch(err) {
            throw new Error(err);
        }
    }
}
```

> 访问

```javascript
this.$store.dispatch('方法名', '参数')

...mapActions
```

- `getters`：类似于 `computed` 和 `filter`，处理数据

> 使用

```javascript
getters: {
    newData(state) {
        return state.arr.filter(item => item.id === 1)
    }
}
```

> 访问

```javascript
this.$store.getters('名称')

...mapGetters
```

## 10. props 传递数据未更新

> 解决方式：使用 `watch` 监听 `props`

## 11. 生命周期

### beforeCreate

- 组件创建阶段

> 此时，组件的 `data` 和 `methods` 以及页面 `DOM` 结构都还没有初始化

### created

- 组件创建阶段

> 此时，组件的 `data` 和 `methods` 已经可用了，但是页面 `DOM` 结构还没有渲染出来；经常用来发起 `ajax` 请求
> 注意，在 `ssr` 服务端渲染的时候，在此处发起 `ajax` 请求会有问题，推荐在 `mounted` 生命周期函数中来发起 `ajax` 请求。

### beforeMount

- 组件创建阶段

> 此时内存中的模板结构还没有*真正渲染到页面上*；此时，页面上*看不到真实的数据*，此时用户看到的只是一个模板页面

### Mounted

- 组件创建阶段

> 组件挂载完毕，可初始化插件

### beforeUpdate

> _数据肯定是最新的_，然而*页面呈现的数据还是旧的*

### updated

> 页面已经完成了更新。此时，`data` 和 `view` 上都是最新的数据

### beforeDestroy

- 组件销毁阶段

> 此时没有真正开始销毁，组件还是正常可用的。同时，`data` 、`methods` 等数据或方法，依然可以被正常访问；

### destroyed

- 组件销毁阶段

> 组件已经完成了销毁.`data` 和 `methods` 都不可用了

## 12  vue 组件从创建到销毁的整个过程

| 行为                                                               | 指向 | 作用                                                                                                                                                 |
| ------------------------------------------------------------------ | ---- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| `new Vue()`                                                        | ➡️   | 实例化 `Vue` 对象                                                                                                                                    |
| `Init Events & Lifecycle`                                          | ➡️   | 初始化组件 `methods` 和 `生命周期函数`                                                                                                               |
| `beforeCreate`                                                     | ➡️   | `data`，`methods` 未被初始化                                                                                                                         |
| `Init injections & reactivity`                                     | ➡️   | 正在初始化 `data` 和 `methods` 中的数据以及方法                                                                                                      |
| `created`                                                          | ➡️   | `data`，`methods` 可用，但 `Dom` 结构还未被渲染，常发起 `ajax` 请求                                                                                  |
| ⬇️                                                                 | ➡️   | 判断是否有 `el`，`template` 属性                                                                                                                     |
| ⬇️                                                                 | ➡️   | 用来编译模版结构，当所有指令解析完毕，则`模板页面就被渲染到内存中了`；当模板编译完成，模板页面还没有挂在到页面上，只是存在于内存中，用户看不到页面； |
| `beforeMount`                                                      | ➡️   | 此时，页面上看不到 `真实数据`，此时用户看到的只是一个模板页面                                                                                        |
| `Create vm.$el and replace "el" with it`                           | ➡️   | 正在把内存中渲染好的模板结构替换到页面上                                                                                                             |
| `mounted`                                                          | ➡️   | 此时，页面渲染完毕                                                                                                                                   |
| ⬇️(组件运行中的生命周期函数)                                       | ➡️   | 会根据 `data` 数据的变化，有选择行的触发 `0`次 或 `n`次                                                                                              |
| `beforeUpdate`                                                     | ➡️   | 数据是新的，但页面呈现的数据仍是旧的                                                                                                                 |
| `updated`                                                          | ➡️   | 页面已经完成了更新，此时，数据是最新的，同时页面上呈现的数据也是最新的                                                                               |
| ⬇️(销毁阶段)                                                       |      |                                                                                                                                                      |
| `beforeDestroy`                                                    | ➡️   | 组件即将被销毁，但是还没有真 正开始销毁，此时组件还是正常可用的。`data`，`methods` 仍可正常访问                                                      |
| `Teardown watchers,child components and event listeners`(销毁过程) | ➡️   |                                                                                                                                                      |
| `destroyed`                                                        | ➡️   | 组件已经完成了销毁，组件已经废了，`data` 和 `methods` 都不可用了                                                                                     |

## 13. keep-alive

### 新增生命周期

> 被包含在  `<keep-alive>`  中创建的组件，会多两个生命周期: `activated`  与  `deactivated`

- actived

> 组件被激活时调用，在组件第一次渲染时也会被调用，之后每次 `keep-alive` 激活时被调用。

- deactivated

> 组件被停用时调用。

### 新增属性

- include

> 包含的组件缓存生效。对应 `.vue` 文件的 `name` 属性

- exclude

> 排除的组件不缓存，优先级大于 `include`。对应 `.vue` 文件的 `name` 属性

## 14. `nextTick`

### 背景

> 在官方文档中说明 `Vue` 是异步执行 `DOM` 更新的，所以在某些场景下需要进行特殊处理才可正常执行某些逻辑。

### 应用场景

- 在 `Vue` 生命周期的 `created()` 钩子函数进行的 `DOM` 操作一定要放在 `Vue.nextTick()` 的回调函数中

> 原因: 在 `created()` 钩子函数执行的时候 `DOM` 其实并未进行任何渲染，而此时进行 `DOM` 操作毫无意义，因此一定要将操作 `Dom` 部分的代码放入 `Vue.nextTick()` 的回调函数中。与之对应的就是 `mounted()` 钩子函数，因为该钩子函数执行时所有的`DOM` 挂载和渲染都已完成，此时在该钩子函数中进行任何 `DOM` 操作都不会有问题 。

- 在数据变化后要执行的某个操作，而这个操作需要使用随数据改变而改变的 `DOM` 结构的时候，这个操作都应该放进 `Vue.nextTick()` 的回调函数中。

### nextTick 源码浅析

> `Vue.nextTick` 用于延迟执行一段代码，它接受 `2` 个参数（ `回调函数` 和 `执行回调函数的上下文环境`），如果没有提供回调函数，那么将返回 `promise` 对象。

> 主要的实现方式为判断设备所支持的三种属性，依据各自支持的属性来进行异步操作。如果支持 `promise`，会使用 `Promise.then` 来做延迟调用函数。如果设备不支持 `Promise` 对象，再判断是否支持 `MutationObserver` 对象，如果支持该对象，就使用`MutationObserver` 来做延迟，最后如果上面两种都不支持的话，我们会使用 `setTimeout` 来做延迟操作。

## 15. 自定义指令

### 简介

> 除了核心功能默认内置的指令 ( `v-model`  和  `v-show`)， `Vue`  也允许注册自定义指令。

> 注意，在 `Vue2.0` 中，代码复用和抽象的主要形式是组件。然而，有的情况下，你仍然需要对普通 `DOM`  元素进行底层操作，这时候就会用到自定义指令。

- 例

> 当页面加载时，该元素将获得焦点 (注意： `autofocus`  在移动版 `Safari`  上不工作)。事实上，只要你在打开这个页面后还没点击过任何内容，这个输入框就应当还是处于聚焦状态。现在让我们用指令来实现这个功能：

```javascript
// 注册一个全局自定义指令 `v-focus`
Vue.directive("focus", {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus();
  }
});
```

> 如果想注册局部指令，组件中也接受一个 `directives`  的选项：

```javascript
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```

> 然后你可以在模板中任何元素上使用新的  `v-focus`   `property`，如下

```html
<input v-focus />
```

### 钩子函数

> 一个指令定义对象可以提供如下几个钩子函数 (均为可选)：

- `bind`：只调用一次，指令第一次绑定到元素时调用。在这里可以进行一次性的初始化设置。
- `inserted`：被绑定元素插入父节点时调用 (仅保证父节点存在，但不一定已被插入文档中)。
- `update`：所在组件的 `VNode` 更新时调用，但是可能发生在其子 `VNode` 更新之前。指令的值可能发生了改变，也可能没有。但是你可以通过比较更新前后的值来忽略不必要的模板更新 (详细的钩子函数参数见下)。
- `componentUpdated`：指令所在组件的 `VNode` 及其子 `VNode` 全部更新后调用。
- `unbind`：只调用一次，指令与元素解绑时调用。

> 接下来我们来看一下钩子函数的参数 (即 el、binding、vnode 和 oldVnode)。

### 钩子函数参数

> 指令钩子函数会被传入以下参数：

- `el`：指令所绑定的元素，可以用来直接操作 `DOM`。
- `binding`：一个对象，包含以下 `property`：
  - `name`：指令名，不包括 `v-` 前缀。
  - `value`：指令的绑定值，例如：`v-my-directive="1 + 1"` 中，绑定值为 `2`。
  - `oldValue`：指令绑定的前一个值，仅在 `update` 和 `componentUpdated` 钩子中可用。无论值是否改变都可用。
  - `expression`：字符串形式的指令表达式。例如 `v-my-directive="1 + 1"` 中，表达式为 `"1 + 1"`。
  - `arg`：传给指令的参数，可选。例如 `v-my-directive:foo` 中，参数为 `"foo"`。
  - `modifiers`：一个包含修饰符的对象。例如：`v-my-directive.foo.bar` 中，修饰符对象为 `{ foo: true, bar: true }`。
- `vnode`：Vue 编译生成的虚拟节点。移步 [VNode API](https://cn.vuejs.org/v2/api/#VNode-%E6%8E%A5%E5%8F%A3) 来了解更多详情。
- `oldVnode`：上一个虚拟节点，仅在 `update` 和 `componentUpdated` 钩子中可用。

> 除了  `el`  之外，其它参数都应该是只读的，切勿进行修改。如果需要在钩子之间共享数据，建议通过元素的  [`dataset`](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/dataset)  来进行。

- 例

```html
<div id="hook-arguments-example" v-demo:foo.a.b="message"></div>
```

```javascript
Vue.directive("demo", {
  bind: function (el, binding, vnode) {
    var s = JSON.stringify;
    el.innerHTML =
      "name: " +
      s(binding.name) +
      "<br>" +
      "value: " +
      s(binding.value) +
      "<br>" +
      "expression: " +
      s(binding.expression) +
      "<br>" +
      "argument: " +
      s(binding.arg) +
      "<br>" +
      "modifiers: " +
      s(binding.modifiers) +
      "<br>" +
      "vnode keys: " +
      Object.keys(vnode).join(", ");
  }
});

new Vue({
  el: "#hook-arguments-example",
  data: {
    message: "hello!"
  }
});
```

### 动态指令参数

> 指令的参数可以是动态的。例如，在 `v-mydirective:[argument]="value"` 中，`argument` 参数可以根据组件实例数据进行更新！这使得自定义指令可以在应用中被灵活使用。

> 例如你想要创建一个自定义指令，用来通过固定布局将元素固定在页面上。我们可以像这样创建一个通过指令值来更新竖直位置像素值的自定义指令：

```html
<div id="baseexample">
  <p>Scroll down the page</p>
  <p v-pin="200">Stick me 200px from the top of the page</p>
</div>
```

```javascript
Vue.directive("pin", {
  bind: function (el, binding, vnode) {
    el.style.position = "fixed";
    el.style.top = binding.value + "px";
  }
});

new Vue({
  el: "#baseexample"
});
```

> 这会把该元素固定在距离页面顶部 200 像素的位置。但如果场景是我们需要把元素固定在左侧而不是顶部又该怎么办呢？这时使用动态参数就可以非常方便地根据每个组件实例来进行更新。

```html
<div id="dynamicexample">
  <h3>Scroll down inside this section ↓</h3>
  <p v-pin:[direction]="200">I am pinned onto the page at 200px to the left.</p>
</div>
```

```javascript
Vue.directive("pin", {
  bind: function (el, binding, vnode) {
    el.style.position = "fixed";
    var s = binding.arg == "left" ? "left" : "top";
    el.style[s] = binding.value + "px";
  }
});

new Vue({
  el: "#dynamicexample",
  data: function () {
    return {
      direction: "left"
    };
  }
});
```

### 函数简写

> 在很多时候，你可能想在  `bind`  和  `update`  时触发相同行为，而不关心其它的钩子。比如这样写：

```javascript
Vue.directive("color-swatch", function (el, binding) {
  el.style.backgroundColor = binding.value;
});
```

### 对象字面量

> 如果指令需要多个值，可以传入一个 `JavaScript` 对象字面量。记住，指令函数能够接受所有合法的 `JavaScript` 表达式。

```html
<div v-demo="{ color: 'white', text: 'hello!' }"></div>
```

```javascript
Vue.directive("demo", function (el, binding) {
  console.log(binding.value.color); // => "white"
  console.log(binding.value.text); // => "hello!"
});
```

## 15. vue 3.0 尝鲜

[中文文档](https://www.liulongbin.top:8085/)
