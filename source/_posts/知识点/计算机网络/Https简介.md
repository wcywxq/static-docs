---
title: Https简介
date: 2020-10-19
categories: [知识点, 计算机网络]
tags:
  - Https
---

https 是在 http 的基础上和 ssl/tls 证书结合起来的一种协议，保证了传输过程中的安全性，减少了恶意劫持的可能，很好的解决了 http 的三个缺点(被监听，被篡改，被伪装)
![https1.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608703700828-253dff98-95a7-425f-b35f-c1a4c3e7b53f.png#align=left&display=inline&height=430&margin=%5Bobject%20Object%5D&name=https1.png&originHeight=430&originWidth=643&size=176587&status=done&style=none&width=643)

## 对称加密和非对称加密

- 对称加密

> 加密的密钥和解密的密钥相同

- 非对称加密

> 将密钥分为公钥和私钥，公钥可公开，私钥保密，客户端公钥加密的数据，服务端可以通过私钥解密。

## 建立连接

- `http` 和 `https` 都需要在建立连接的基础上来进行数据传输
- 当客户在浏览器输入网址并按下回车，浏览器会在 `浏览器DNS缓存`，`本地DNS缓存` 以及 `host` 中寻找对应记录，如果没有获取到则会请求 `DNS` 服务来获取对应 `ip`
- 获取到 `ip` 后，`tcp` 连接会进行三次握手建立连接

## 三次握手四次挥手

![https2.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608703708786-c272a04f-2a68-45be-8a06-f8d5841f72aa.png#align=left&display=inline&height=497&margin=%5Bobject%20Object%5D&name=https2.png&originHeight=497&originWidth=500&size=35314&status=done&style=none&width=500)

### 三次握手

1. 建立连接时，客户端发送 `SYN` 包(`syn=j`)到服务端，并进入 `SYN_SEND` 状态，等待服务器确认
2. 服务器收到 `SYN` 包，向客户端返回 `ACK(ack=j+1)`，同时自己也发送一个 `SYN` 包 `(syn=k)`，即 `SYN+ACK` 包，此时服务器进入 `SYN_RCVD` 状态
3. 客户端收到服务端的 `SYN+ACK` 包，向服务器发送确认包 `ACK(ack=k+1)`，此包发送完毕，客户端和服务器进入 `ESTABLISHED` 状态，完成三次握手

### 四次挥手

1. `TCP` 向客户端发送一个 `FIN`，用来关闭客户到服务器的数据传输
2. 服务器收到这个 `FIN`，它返回一个 `ACK`，确认序号为收到的序号加 `1`，和 `SYN` 一样，一个 `FIN` 将占用一个序号
3. 服务器关闭客户端连接，发送一个 `FIN` 给客户端
4. 客户端发回 `ACK` 报文确认，并将确认序号设置为收到序号加 `1`
