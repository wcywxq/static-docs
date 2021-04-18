---
title: WebScoket
date: 2021-04-15
categories: [知识点, JavaScript]
---

## 理解

WebSocket 是一种在单个 TCP 连接上进行全双工通信的协议。WebSocket 是基于应用层传输控制协议，而 socket 是基于传输层的传输控制协议，WebSocket 是在 Socket 之上封装的一种上层通讯协议。它们都是全双工的(可以同时接收和发送)。为了区别于普通的 Http 请求，发起 WebSocket 请求时，会增加一个请求头，用来告诉服务器这是 WebSocket 请求。
