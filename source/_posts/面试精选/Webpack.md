---
title: Webpack 面试精选
date: 2021-04-18
categories: [面试, Webpack]
---

## 异步编程的实现方式

- 回调函数
- 事件监听
- 发布订阅模式(观察者模式)
- Promise
- Generator
- async/await

## 为什么 Proxy 不能被 Polyfill

Class 可用 function 模拟
Promise 可用 callback 模拟
Proxy 不能用 Object.defineProperty 模拟

## npm 打包注意点

1. 支持 CommonJS 规范
2. 打包结果应该为 ES5
3. npm 包应尽量小
4. 发布模块不应将依赖模块一同打包
5. ui 组件类的模块应将依赖的其他资源文件包含在发布模块里

## webpack 与 grunt、gulp 的不同点

- webpack: 基于入口。webpack 会自动地递归解析入口所需要加载的所有资源文件，然后用不同的 loader 处理不同的文件，采用 plugin 扩展功能。适用于大型复杂的前端站点构建。
- grunt: 基于任务, 找到一个(或一类)文件, 对其做一系列链式操作,更新流上的数据。整条链式操作构成一个任务，多个任务就构成了整个 web 的构建流程。
- gulp: 基于流,找到一个(或一类)文件, 对其做一系列链式操作,更新流上的数据。整条链式操作构成一个任务，多个任务就构成了整个 web 的构建流程。
- roolup: 适用于基础库打包。

## webpack 的构建流程

Webpack 的运行流程是一个串行的流程，从启动到结束会依次执行以下流程：

- 初始化参数：从配置文件和 Shell 语句中读取与合并参数，得出最终的参数
- 开始编译: 用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译
- 确定入口：根据配置中的 entry 找出所有的入口文件
- 编译模块：从入口文件出发，调用所有配置的 Loader 对模块进行编译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过本步骤的处理
- 完成模块编译：经过上一步后，使用 Loader 编译完所有模块后，得到了每个模块编译后的最终内容以及他们之间的依赖关系
- 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 chunk, 再把每个 chunk 转换成一个单独的文件加入到输出列表，这一步是可以修改输出内容的最后机会
- 输出完成：在确定好输入内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

在以上过程中，Webpack 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 Webpack 提供的 Api 改变 Webpack 的运行结果

### 简单说法

- 初始化: 启动构建, 读取与合并配置参数, 加载 Plugin, 实例化 Compiler(编译器)
- 编译: 从 Entry(入口文件) 出发，针对每个 Module 串型调用对应的 Loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归的进行编译处理
- 输出: 将编译后的 Module 组合成 Chunk, 将 Chunk 转换成文件, 输出到文件系统中

## loader

### webpack 常用 loader

- raw-loader: 加载文件原始内容（utf-8）
- file-loader: 把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输出的文件
- url-loader: 和 file-loader 类似, 区别是用可以设置一个阀值，大于阈值会交给 file-loader 处理，小于阈值时返回文件 base64 形式编码 (处理图片和字体)

> 在 webpack 5，通过添加 4 种新的模块类型，来替换所有这些 loader
>
> 1. asset/resource 替换 file-loader
> 2. asset/inline 替换 url-loader
> 3. asset/source 替换 raw-loader
>
> 在 webpack 5 中，可通过设置 `type: "javascript/auto"` 来使用旧的 assets loader

- svg-inline-loader: 将压缩后的 svg 内容注入到代码中
- source-map-loader: 加载额外的 source map 文件，以方便断点调试
- image-loader: 加载并且压缩图片文件
- babel-loader: 把 es6 转换成 es5
- css-loader: 加载 css, 支持模块化、压缩、文件导入等特性
- style-loader: 把 CSS 代码注入到 JavaScript 中，通过 DOM 操作去加载 CSS
- MiniCssExtractPlugin.loader: 替换 style-loader, 用于将 JavaScript 中的 css 分离
- postcss-loader: 扩展 CSS 语法，使用下一代 CSS，可以配合 autoprefixer 插件自动补齐 CSS3 前缀
- source-map-loader: 从现有的源文件中提取 source maps
- eslint-loader: 通过 ESLint 检查 JavaScript 代码
- tslint-loader: 通过 TSLint 检查 TypeScript 代码
- mocha-loader: 加载 Mocha 测试用例的代码
- coverjs-loader: 计算测试的覆盖率
- ts-loader: 将 TypeScript 转换成 JavaScript
- vue-loader: 加载 vue.js 单文件组件
- cache-loader: 可以在一些性能开销较大的 Loader 之前添加，目的是将结果缓存到磁盘里

### webpack loader 执行顺序

> 从下到上, 从右向左。因为使用了函数式编程的方式

### 编写 loader 的注意点

1. loader 支持链式调用, 因此开发过程中需严格遵守 `单一指责`, 即每个 loader 只负责自己需要处理的内容
2. loader 运行在 node.js 中, 因此需导出一个函数, 参数是加载文件的原始 UTF-8 格式编码的字符串, 返回处理后的内容
3. 如果要返回多个结果，可以使用 this.callback(err: Error | null, content: string | Buffer, sourceMap?: SourceMap, meta?: any)
4. 在 loader 中, 异步操作需使用 this.async
5. loader-utils 提供了 getOptions 方法用来获取参数信息; schema-utils 提供了 validate 方法用来做 JSON 模式验证

## plugin

### 介绍 webpack plugin

webpack 插件是一个具有 apply 属性的 JavaScript 对象。apply 属性会被 webpack compiler 调用，并且 compiler 对象可在整个编译生命周期访问

- compiler 实例

Compiler 模块是 webpack 的支柱引擎，它通过 cli 或 node api 传递的所有选项，创建出一个 compilation 实例。它扩展 (extend) 自 Tapable 类，以便注册和调用插件。

- compilation 钩子

Compilation 模块会被 Compiler 用来创建新的 compilation 对象(或新的 build 对象)。compilation 实例能够访问所有的模块和它们的依赖(大部分是循环依赖)。它会对应用程序的依赖图中所有模块, 进行字面上的编译(literal compilation)。在编译阶段，模块会被加载(load)、封存(seal)、优化(optimize)、分块(chunk)、哈希(hash)和重新创建(restore)。

### webpack 常用 plugin

- webpack-bar: 自定义 webpack bar
- html-webpack-plugin: 可根据模版自动生成 html 代码, 并自动引用 css 和 js 文件
- mini-css-extract-plugin: 用于分割 css chunk 包
- optimize-css-assets-webpack-plugin/css-minimizer-webpack-plugin: 用于压缩 css 代码, 同时可对不同组件中重复的 css 代码去重
- uglifyjs-webpack-plugin/terser-webpack-plugin: 压缩 JavaScript
- clean-webpack-plugin: 目录清理
- webpack-bundle-analyze: 可视化分析包大小体积
- compression-webpack-plugin: 开启 gzip 压缩
- terser-webpack-plugin: 使用多进程并行压缩代码, 需设置 `parallel: true`
- DefinePlugin: 编译时配置全局变量, 对开发模式和生产模式的构建允许不同的行为非常有用(可配合 .env 使用)
- HotModuleReplacementPlugin: 热更新
- DllPlugin: 拆分捆绑包以大幅缩短构建时间, 用于抽离第三方模块，常用于对静态不变的第三方库进行处理
- SplitChunksPlugin: 自动拆分 chunks, 公共资源拆分
- SourceMapDevToolPlugin: 对 sourceMap 的更精细的配置

### webpack plugin 执行顺序

>

### 编写 Plugin 的步骤

1. 创建一个 JavaScript 函数或 JavaScript 类。
2. 在其原型上绑定 apply 方法
3. 指定要利用的事件挂钩(hook)。
4. 处理 webpack 内部实例特定的数据。
5. 功能完成后，调用 webpack 提供的回调(callback)。

## webpack loader 和 plugin 的区别

loader 本质是一个函数, 接收一个参数, 参数是接收到的文件内容, 然后进行转换, 返回转换后的结果。本质起到了对非 JavaScript 类型资源的转译的预处理工作。

plugin 就是插件, 基于事件流框架 tapable, 插件可以扩展 webpack 的功能, 在 webpack 运行的生命周期中会广播出许多事件, plugibn 可以监听这些事件, 在合适的时机通过 webpack 提供的 API 改变输出结果

loader 在 module.rules 中配置, 作为模块的解析规则，是一个数组, 每一项都是一个对象, 内部包含了 test(类型文件)、loader、options(参数)等属性

plugin 在 plugins 中单独配置, 类型为数组, 每一项都是一个 plugin 实例, 参数都通过构造函数传入

### Webpack 提高效率的 plugin

- webpack-dashboard: 更友好的展示相关的打包信息
- webpack-merge: 提取公共配置，减少重复配置代码
- speed-measure-webpack-plugin: 简称为 SMP，分析出 Webpack 打包过程中 Loader 和 Plugin 的耗时, 有助于找到构建过程中的性能瓶颈
- size-plugin: 监控资源的体积变化，尽早的发现问题
- HotModuleReplacementPlugin: 热模块替换

## 什么是 SourceMap

source map 是将编译、打包、压缩后的代码映射回源代码的过程。打包压缩后的代码不具备良好的可读性，想要调试源码就需要 soucre map。map 文件只要不打开开发者工具，浏览器是不会加载的。

线上环境一般有三种处理方案：

- hidden-source-map：借助第三方错误监控平台 Sentry 使用
- nosources-source-map：只会显示具体行数以及查看源代码的错误栈。安全性比 sourcemap 高
- sourcemap：通过 nginx 设置将 .map 文件只对白名单开放(公司内网)

## Webpack 模块打包原理

Webpack 实际上为各个模块创造了一个可以导出和导入的环境，本质上并没有修改代码的执行逻辑，代码的执行顺序和模块的加载顺讯也完全一致

## Webpack 热更新原理

webpack 的热更新又叫做热替换, 即 HMR。这个机制可以做到不用刷新浏览器而将新变更的模块替换掉旧的模块

HMR 的核心是客户端从服务器拉取更新后的文件, 准确说就是 chunk diff (chunk 需要更新的部分), 实际上 WDS (无线分布式系统) 与浏览器之间维护了一个 WebSocket, 当本地资源发生变化时, WDS 会向浏览器推送更新，并带上构建时的 hash, 让客户端与上一次资源进行对比。

客户端对比出差异后会向 WDS 发起 AJAX 请求来获取更改后的内容(文件列表、hash), 这样客户端就可以借助这些信息继续向 WDS 发起 jsonp 请求该 chunk 的增量更新。

## 代码分割的本质

代码分割的本质就是在 源代码直接上线 和 打包成唯一脚本 main.bundle.js 这两种极端方案之间的一种更适合时机场景的中间状态。

用可接受的服务器性能压力增加来换取更好的用户体验

源代码直接上线：虽然过程可控，但 http 请求多，性能开销大
打包成唯一脚本：服务器压力小，但页面空白期常，用户体验差

## 前端性能优化

### 工程化方向

1. 客户端使用 gizp 离线包, 服务端开启 gzip 压缩
2. 使用 Tree-shaking 去除未使用代码
3. es-module
4. 动态 import
5. 图片加载优化, 客户端预渲染
6. 使用 CDN 加速
7. 采取 Webpack 提供的优化
   - base64, tree-shaking, 资源压缩, 拆包 chunk
   - DLL 拆分基础包
   - 使用别名
8. 骨架图
9. 数据预取
10. 减少重定向
11. 合理利用浏览器缓存
12. 启用 http2
13. 使用负载均衡(可提高响应速度)

### 细节方向

1. 图片占位, 雪碧图
2. 预加载
3. 合理利用缓存策略
4. script 标签合理利用 defer/async
5. 减少 dom 操作，减少重排重绘
6. 接口请求合并
7. 数据缓存
8. 首页加载不可视组件
9. 防止渲染抖动, 控制时序
10. 减少组件层级
11. 使用 flex 布局

## 如何优化 Webpack 的构建速度

1. 使用高版本的 Webpack 和 Node.js
2. 多进程/多实例构建 thread-loader
3. 代码压缩
4. 图片压缩
5. 缩小打包作用域
   - exclude/include(确定 loader 规则范围)
   - resolve.modules 指明第三方模块的绝对路径(减少不必要的查找)
   - 对不需要解析的库进行忽略
   - 完全排除模块
   - 合理利用 alias
6. 提取页面公共资源
   - 基础包分离，将基础包通过 CDN 引入，不打入 bundle 中
   - 使用 SplitChunksPlugin 进行公共资源分离
7. DLL
   - 使用 DLLPlugin 进行分包，使用索引链接对 mainfest.json 引用，让一些基本不会改动的代码先打包成静态资源，避免反复编译浪费时间
8. 充分利用缓存提升二次构建速度
   - babel-loader 开启缓存
   - terser-webpack-plugin 开启缓存
   - 使用 cache-loader 或者 hard-source-webpack-plugin
9. Tree shaking
   - 打包过程中检测工程中没有使用过的模块并进行标记，在资源压缩时将他们从最终的 bundle 中去掉，在开发中尽可能使用 ES6 Module 的模块，提高 tree-shaking 效率
   - 禁用 babel-loader 的模块依赖解析，否则 Webpack 接收到的就是转换过的 CommonJS 形式的模块，无法进行 tree-shaking
   - 去掉无用的 css 代码

## Babel 原理

Babel 大概分为三大部分：

1. 解析：将代码转换成 AST

   - 词法分析：将代码(字符串)分割为 token 流，即语法单元成的数组
   - 语法分析：分析 token 流(上面生成的数组)并生成 AST

2. 转换：访问 AST 的节点进行变换操作生产新的 AST

   - Taro 就是利用 babel 完成的小程序语法转换

3. 生成：以新的 AST 为基础生成代码
