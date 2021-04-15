---
title: Koa 的使用
date: 2020-12-10
categories: [文档]
tags:
  - NodeJS
---

## 说明

> 官网：[https://koa.bootcss.com/](https://koa.bootcss.com/)
> 注意：`koa2`依赖 `node v7.6.0` 或 `ES2015` 及更高版本和 `async`方法支持

## 安装

> `$ npm install koa --save`

## 启动服务：

```javascript
const Koa = require("koa");
const app = new Koa();

app.use(async ctx => {
  ctx.body = "Hello World";
});
app.listen(3000, () => {
  console.log("服务开启成功");
});
```

## get/post 请求

- get

> 明文传递，传输体积小，因为在请求头里面

> `query`：返回格式化好的参数对象
> `queryString`：返回请求字符串

```javascript
const Koa = require("koa");
const app = new Koa();

app.use(async ctx => {
  let url = ctx.url;
  let qyery = ctx.query;
  let queryString = ctx.queryString;
  ctx.body = {
    url,
    qyery,
    qyeryString
  };
});
app.listen(3000, () => {
  console.log("服务开启成功");
});
```

- post

> 密文传递，传输体积大，因为在请求包体里面

- 方式 1：不用中间件

```javascript
const Koa = require("koa");
const app = new Koa();

app.use(async ctx => {
  // 叠加数据
  let data = "";
  // 监听data事件，收到表单的数据的时候就会执行
  ctx.req.on("data", chunk => {
    data += chunk; // 二进制
  });
  //    接受表单提交数据完成之后
  ctx.req.on("end", () => {
    data = decodeURI(data);
    console.log(data);
  });
  ctx.body = "123";
});
app.listen(3000, () => {
  console.log("服务开启成功");
});
```

- 方式 2：`koa-bodyparser`中间件

> 安装：`$ npm install koa-bodyparser --save`

```javascript
const Koa = require("koa");
const app = new Koa();
const bodyparser = require("koa-bodyparser");
app.use(bodyparser());
app.use(async ctx => {
  let data = ctx.request.body;
  ctx.body = data;
});
app.listen(3000, () => {
  console.log("服务开启成功");
});
```

## 路由 koa-router

> 安装：`$ npm install koa-router --save`

```javascript
const Koa = require("koa");
const app = new Koa();
const Router = require("koa-router");
const router = new Router({
  prefix: "/api" // 设置前缀
});

router.get("/abc", (ctx, next) => {
  ctx.body = ctx.query;
});
app.use(router.routes());
// 这里对应get方法，如果发送了post，则会报错；即只允许特定方法进行请求
app.use(router.allowedMothods());

app.listen(3000, () => {
  console.log("服务开启成功");
});
```

```javascript
const Koa = require("koa");
const app = new Koa();
const Router = require("koa-router");
const router = new Router();
// MVC模式 即导入控制器下的方法
let user = require("./controller/user.js");
// '/user'代表控制器名称，必须和请求的地址相对应
router.use("/user", user.routes());
app.use(router.routes());
// 这里对应get方法，如果发送了post，则会报错；即只允许特定方法进行请求
app.use(router.allowedMothods());

app.listen(3000, () => {
  console.log("服务开启成功");
});
```

## cookie

| 字段名      | 说明                                                                                                                                                                                            |
| ----------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `maxAge`    | 一个数字表示从 `Date.now()` 得到的毫秒数                                                                                                                                                        |
| `signed`    | `cookie` 签名值                                                                                                                                                                                 |
| `expires`   | `cookie` 过期的 `Date`                                                                                                                                                                          |
| `path`      | `Cookie` 路径，默认是 '`/`'                                                                                                                                                                     |
| `domain`    | `cookie` 域名                                                                                                                                                                                   |
| `secure`    | 安全 `cookie`                                                                                                                                                                                   |
| `httponly`  | 服务器可访问 `cookie`, 默认是 `true`                                                                                                                                                            |
| `overwrite` | 一个布尔值，表示是否覆盖以前设置的同名的 `cookie`（默认是 `false`）,如果是 `true`,在同一个请求中设置相同名称的所有 `Cookie`（不管路径或域）是否在设置此 `Cookie` 时从 `Set-Cookie` 标头中过滤掉 |

```javascript
const Koa = require("koa");
const app = new Koa();
app.use(async ctx => {
  if (ctx.url == "/weichuang") {
    //    设置cookie：set，获取cookie：get
    ctx.cookies.set("name", "weichang", {
      domain: "localhost",
      path: "/weichuang",
      maxAge: 24 * 60 * 60 * 1000,
      expires: new Date("2019-12-31"),
      httpOnly: false,
      overwrite: false
    });
    ctx.body = "cookie success";
  } else {
    ctx.body = "no cookie";
  }
});
app.listen(3000, () => {
  console.log("服务开启成功");
});
```

## ejs 模板引擎

> 安装：`$ npm install ejs --save`
> 中间件`koa-view`：`$ npm install koa-view --save`

```javascript
// index.ejs
<h1><%=title%></h1>
<ul>
   <%
       for(let i = 0; i < list.length; i++) {
   %>
    <li>
      <%=list[i].name%> -- <%=list[i].age%>
    </li>
   <%
       }
   %>
</ul>
// app.js
const Koa = require('koa')
const app = new Koa()
const views = require('koa-views')
const path = require('path')
// 加载模板
app.use(views(path.join(__dirname, './views'), {
   extension: 'ejs'
}))
app.use(async ctx => {
   let title = 'hello';
   await ctx.render('index', {
       title,
       list: [
           {name: 'a', age: 0},
           {name: 'a1', age: 1},
           {name: 'a2', age: 2},
           {name: 'a3', age: 3}
       ]
   })
})

app.listen(3000, () => {
   console.log('服务开启成功')
})
```

## cors 跨域

> 安装：`$ npm install koa2-cors --save`

```javascript
//解决跨域
const cors = require("koa2-cors");
app.use(
  cors({
    origin: ["http://localhost:8080"],
    // 配置证书
    credentials: true
  })
);
```

## 加盐加密

> 安装：`$ npm install bcrypt --save`
