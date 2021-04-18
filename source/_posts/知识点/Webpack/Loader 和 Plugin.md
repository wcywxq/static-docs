---
title: Loader 和 plugin
date: 2021-04-18
categories: [知识点, Webpack]
---

## 常见的 Loader

### raw-loader

- 加载文件原始内容 utf-8

### file-loader

- 把文件输出到一个文件夹中，在代码中通过相对 URL 去引用输入的文件
- 处理图片和字体

### url-loader

- 与 file-loader 类似，区别是用户可以设置一个阀值，大于阀值交给 file-loader，小于阀值返回文件 base64 形式编码
- 处理图片和字体

### source-map-loader

- 加载额外的 SourceMap 文件，方便断点调试

### image-loader

- 加载并压缩图片文件

### babel-loader

- 将 ES6 转换成 ES5

### ts-loader

- 将 TypeScript 转换成 JavaScript

### sass-loader

- 将 Scss/Sass 转换成 Css

### css-loader

- 将 Css 代码注入到 JavaScript 中，通过 Dom 操作加载 Css

### postcss-loader

- 扩展 Css 语法

### eslint-loader

- 通过 ESLint 检查 JavaScript 代码

### tslint-loader

- 通过 TSLint 检查 TypeScript 代码

### vue-loader

- 加载 Vue.js 单文件组件

### i18n-loader

- 国际化

## 常用的Plugin

### html-webpack-plugin

- 简化 HTML 文件创建 (依赖于 html-loader)

### serviceworker-webpack-plugin

- 为网页应用增加离线缓存功能

### clean-webpack-plugin

- 目录清理

## Loader 和 Plugin 的区别

Loader 本质是一个函数，在该函数中接受到文件内容，然后进行转换，返回转换后的结果。因为 Webpack 只认识 JavaScript，所以 Loader 就成了翻译官，对其他类型的资源进行转译的预处理工作。

Plugin 就是插件，基于事件流框架 Tapable，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件， Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果。

Loader 在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options(参数)等属性。

Plugin 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。
