---
title: Hybrid 简介
date: 2020-07-17
categories: [前端, JavaScript]
tags:
  - 技术实践
---

## hybrid 实现原理

> 原生的 `App` 中，使用 `WebView` 作为容器直接承载 `Web` 页面。 中间通过 `JSBridge` 来完成 `Native` 与 `JavaScript` 之间的通讯。

## hybrid 几种方案

### 基于  WebView UI  的基础方案

> 例如微信 `JS-SDK`，通过 `JSBridge` 完成 `H5` 与 `Native` 的双向通讯，从而赋予 `H5` 一定程度的原生能力。

### 基于  `Native UI`  的方案

> 例如 `React-Native、Weex` 。在赋予 `H5` 原生 `API` 能力的基础上，进一步通过 `JSBridge` 将 `js` 解析成的虚拟节点树( `Virtual DOM` )传递到 `Native` 并使用原生渲染。

### 小程序方案

> 通过更加定制化的 `JSBridge`，并使用双 `WebView` 双线程的模式隔离了 `JS` 逻辑与 `UI` 渲染，形成了特殊的开发模式，加强了 `H5` 与 `Native` 混合程度，提高了页面性能及开发体验。

## hybrid 和 h5 对比

### h5

- 优点
  - 开发速度快，一端开发多端运行
  - 如果需要频繁更换页面，用 `h5` 维护会更加容易
  - 版本更新不需要打包便可发布
- 缺点
  - 流畅度，安全问题
  - 性能不如原生
  - 不能调用移动硬件设备的功能
