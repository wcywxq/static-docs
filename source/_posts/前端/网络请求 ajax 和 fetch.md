---
title: 网络请求 ajax 和 fetch
date: 2020-07-13
categories: [前端, JavaScript]
tags:
  - 技术点
---

## 1. ajax

> `ajax` 是一种使用现有标准的新方法。指一种创建交互式、快速动态网页应用的网页开发技术，无需重新加载整个网页的情况下，能够更新部分网页的技术，接下来我们介绍一下原生的实现。

### 1.1 原生 ajax 实现

> 1. 创建 `XMLHttpRequest` 对象
> 2. 发送请求
> 3. 服务器端的响应

```javascript
var movieUrl = "https://***";
//	1.创建XMLHttpRequest对象
var xmlhttp;
if (window.XMLHttpRequest) {
  xmlhttp = new XMLHttpRequest();
} else {
  xmlhttp = new ActiveXObject("Microsoft.XMLHTTP");
}
//	2.发送请求
xmlhttp.open("GET", movieUrl, true); //	第三个参数 true:异步
xmlhttp.send();
//	3.服务器端的响应
xmlhttp.addEventListener("readystatechange", function () {
  if (xmlhttp.readyState == 4 && xmlhttp.status == 200) {
    var obj = JSON.parse(xmlhttp.responseText);
    console.log(obj);
  }
});
```
