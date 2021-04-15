---
title: keep-alive
date: 2021-04-15
categories: [Vue]
---

## keep-alive 的作用

在做组件切换的时候，保存一些组件的状态，防止多次渲染，同时也可缓存数据状态。

## 生命周期钩子

当我们使用 keep-alive 时，会额外增加两个钩子函数，activated 和 deactivated。用 keep-alive 包裹的组件在切换的时候不会进行销毁，而是缓存到内存中执行 deactivated 钩子函数，命中渲染缓存后会执行 activated 钩子函数。
