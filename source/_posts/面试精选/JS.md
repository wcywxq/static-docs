---
title: JS 面试精选
date: 2021-04-19
categories: [面试, JS]
---

## 从 0 到 1 搭建项目的思考

- 开发工具
- 技术选型
- 构建工具
- 构建仓库
- 依赖选择
- 测试策略
- 技术架构
- 环境信息
- 编码规则

## 前端项目的性能指标

- 白屏时间
- 首屏时间
- 用户可操作时间
- 页面总下载时间

## flex 布局

可以自由操作容器中子元素的排列方式

- flex-flow: 是 flex-direction 和 flex-wrap 的集合，默认属性为: row nowrap
- align-content: 用于控制多行项目的对齐方式，如果项目只有一行则不会启作用
- order: 默认为 0, 用于决定项目排列顺序，数值越小，项目排列越靠前
- flex-grow: 默认 0，用于决定项目在有剩余空间的情况下是否放大，默认不放大；注意，即便设置了固定宽度，也会放大。
- flex-shrink: 默认 1，用于决定项目在空间不足时是否缩小，默认项目都是 1，即空间不足时大家一起等比缩小；注意，即便设置了固定宽度，也会缩小。
- flex-basics: 默认 auto，用于设置项目宽度，默认 auto 时，项目会保持默认宽度，或者以 width 为自身的宽度，但如果设置了 flex-basis，权重会比 width 属性高，因此会覆盖 width 属性。
- flex: 默认 0 1 auto，flex 属性是 flex-grow，flex-shrink 与 flex-basis 三个属性的简写，用于定义项目放大，缩小与宽度。
- align-self: 表示继承父容器的 align-items 属性。如果没父元素，则默认 stretch。 用于让个别项目拥有与其它项目不同的对齐方式，各值的表现与父容器 的 align-items 属性完全一致。

## css 优先级顺序

1. !important
2. 行内样式
3. ID 选择器
4. 类选择器、属性选择器、伪类选择器
5. 元素选择器、关系选择器、伪元素选择器
6. 通配符 \*

## js 原型

- 在 js 中，我们使用构造函数新建一个对象，每个构造函数内部都有一个 prototype 属性值，该属性值是一个对象，该对象包含了可以被该构造函数的所有实例所共享的属性和方法。
- 在我们使用构造函数新建一个对象后，在这个对象的内部将包含一个指针，这个指针指向构造函数的 prototype 属性值，即这个对象的原型。

## js 原型链

当我们访问一个对象的属性时，若对象内部不存在这一属性，便会去它的原型对象里去找这个属性，同时这个原型对象又会有自己的原型，然后这样一直找下去，便形成了原型链

> 个人理解: 原型链就是链表, this 就是连标当前指向的那个原型, bind call apply 就是改变链表的 next 指向

## js 原型特点

javascript 对象使用过引用来传递的，我们创建的每个新对象实体中并没有一份属于自己的原型副本，当我们修改原型时，与之相关的对象也会继承这一改变

## js 的几种继承方式

- 原型链继承

> 缺点: 引用类型数据会被所有实例所共享，会造成修改数据混乱; 创建子类时无法向超类型传参

- 借用构造函数继承

> 通过在子类函数中调用超类型的构造函数实现。解决了无法向超类传参数的缺点，但是无法实现函数方法的复用，同时超类定义的方法子类也无法访问。

- 组合式继承

> 将原型链继承和借用构造函继承组合使用，通过借用构造函数的方式来实现类型属性的继承，通过将子类型的原型设置为超类型的实例来实现方法的继承。缺点是调用了两次超类构造函数。

- 原型式继承

> 基于已有的对象创建新的对象。向函数中传递一个对象，然后返回一个以这个对象为原型的对象。Object.create() 方法就是原型式继承的实现，缺点同原型继承

- 寄生式继承

> 通过 Object.create 创建副本，对副本进行扩展，最后将副本返回

- 寄生组合式继承

> 缺点: 使用超类实例做子类型原型，多了不必要的属性。

## js 的栈和堆

- 栈(基本数据类型: number, string, boolean, undefined, null, symbol, bigInt)

> 占据空间小，大小固定，频繁使用。

- 堆(应用数据类型: object, array, function)

> 占据空间大，大小不固定

## 事件冒泡和事件捕获

- 事件冒泡: 自底向上

```js
window.addEventListener("click", () => {
  // 事件触发顺序为冒泡顺序
});
```

- 事件捕获: 自顶向下

```js
window.addEventListener(
  "click",
  () => {
    // 事件触发顺序为捕获顺序
  },
  true
);
```

## 事件的执行顺序

- 先事件捕获(从 window -> document 依次往下执行)
- 再是对目标事件的处理
- 最后是事件冒泡

## 事件冒泡和事件捕获的应用场景

1. 事件委托

## var、let、const 对比

- var 定义的变量会进行变量提升; let 和 const 定义的变量不存在变量提升(不存在变量提升)
  - 对于 var 来说，如果变量定义在函数内部，则会将变量提升到函数的开头
  - 对于 var 来说，如果变量声明是一个全局变量，则会将变量声明提升到全局作用域的开头
- var 的作用域是函数级; let 和 const 的作用域是块级(声明的变量只在声明的代码块内有效)
- var 和 let 声明变量时不需要初始值; const 声明变量需要初始值
- let 声明的变量存在暂时性死区(只要块级作用域内部存在 let 命令，它所声明的变量就会绑定这个区域，不再受外部影响)
- var 定义的变量可以重复进行定义; let 和 const 声明的变量不可以重复进行定义(不能够重复声明)

## 实现 new 关键字 (new 的过程/new 操作符做了什么)

- 创建一个新的空对象
- 设置原型，将对象的原型设置为函数的 prototype 对象
- 绑定 this 指向，执行构造函数
- 返回新对象

```js
function ObjectFactory() {
  let obj = {};
  let constructor = Array.prototype.shift.call(arguments);
  obj.__proto__ = constructor.prototype;
  constructor.apply(obj, arguments);
  return obj;
}
```

## this 指向

- 全局作用域下的 this => window
- 普通函数 => 非严格模式 window; 严格模式 undefined
- 箭头函数 => 指向外层的 this
- 对象内部函数 => 当前对象
- 构造函数 => 指向当前构造函数创建的对象实例
- call/apply/bind => 通过设置第一个参数上下文来改变 this 指向

## 实现 call、apply、bind

### call

```js
Function.prototype.call2 = function (ctx, ...args) {
  let context = ctx || window;
  context.fn = this;
  let result = context.fn(args);
  delete context.fn;
  return result;
};
```

### apply

```js
Function.prototype.apply2 = function (ctx, ...args) {
  let context = ctx || window;
  context.fn = this;
  if (!args[0].length) return context.fn();
  let result = context.fn(...args[0]);
  delete context.fn;
  return result;
};
```

### bind

```js
Function.prototype.bind2 = function (ctx, ...args) {
  let _self = this;

  function Fn() {}

  let f = function (...fArgs) {
    return _self.apply(this instanceof Fn ? this : ctx, args.concat(fArgs));
  };
  Fn.prototype = this.prototype;
  f.prototype = new Fn();
  return f;
};
```

## js 闭包

闭包指的是有权访问另一个函数作用域中变量的函数，创建闭包最常见的方式就是在一个函数内创建另一个函数(本质就是函数的作用域链中保存着外部函数变量对象的引用)

## weakMap/weakSet

对于 weakMap 和 weakSet 来说不会计入垃圾回收

weakMap 的键名只能是对象

weakSet 成员只能是对象

## 什么是 JavaScript 事件循环

- 因为 js 是单线程执行的(两个线程同时操作 dom, 会发生冲突)，在代码执行的过程中，通过将不同函数的执行上下文压入执行栈中来保证代码的有序执行。
- 在执行同步代码的时候，如果遇到了异步事件，js 引擎并不会一直等待其返回结果，而是会将这个事件暂时挂起，继续执行执行栈中的其他任务。
- 当异步事件执行完毕后，再将异步事件对应的回调加入到与当前执行栈中不同的另一个任务队列中等待执行。
- 任务队列分为
  - 宏任务队列
    > script 脚本的执行
    > setTimeout/setInterval/setImmediate
    > I/O 操作
    > UI 渲染
  - 微任务队列
    > promise 的回调 (必须有 resolve/reject 结果, 同一块作用域内多个 resolve 中其中一个执行完后其他不再执行)
    > node 中的 process.nextTick
    > 对 DOM 变化监听的 MutationObserver
- 当当前执行栈中的事件执行完毕后，js 引擎会首先判断微任务队列中是否有任务可以执行。如果有就将微任务队列的队首事件压入执行栈中执行。当微任务队列中的任务都执行完成后，再去判断宏任务队列中的任务。

## node 中的事件循环和浏览器中的事件循环有什么区别

node 中宏任务的执行顺序:

1. timers 定时器: 执行已经安排的 setTimeout 和 setInterval 的回调函数
2. pending callback 待定回调: 执行延迟到下一个循环迭代的 I/O 回调
3. idle, prepare: 仅系统内部使用
4. poll: 检索新的 I/O 事件，执行与 I/O 相关的回调
5. check: 执行 setImmediate() 回调函数
6. close callbacks: socket 的关闭的回调(socket.on('close', () => {}))

微任务和宏任务在 node 中的执行顺序:

- Node V10 及以前:

  - 执行完一个阶段中的所有任务(1-6)
  - 执行 nextTick 队列里的内容
  - 最后执行微任务队列里的内容

- Node V10 后:
  - 与浏览器行为保持一致

## 什么是事件队列

事件队列是一个存储着待执行任务的队列，其中的任务严格按照事件顺序执行，队头的任务率先执行，队尾的任务后执行。每次仅执行一个任务。

## 执行栈是什么

执行栈是类似函数调用栈的运行容器，执行栈为空，js 引擎检查事件队列是否为空，不为空则将第一个任务压入执行栈执行。

## js 判断数据类型

### 准确判断数据类型

```js
Object.prototype.toString.call(1); // ["object Number"]
Object.prototype.toString.call("1"); // ["object String"]
Object.prototype.toString.call(false); // ["object Boolean"]
Object.prototype.toString.call(undefined); // ["object Undefined"]
Object.prototype.toString.call(null); // ["object Null"]
Object.prototype.toString.call([1, 2, 3]); // ["object Array"]
Object.prototype.toString.call({}
})
; // ["object Object"]
Object.prototype.toString.call(NaN); // ["object Number"]
```

### 利用 typeof 判断基本数据类型

```js
typeof 1; // "number"
typeof "1"; // "string"
typeof false; // boolean
typeof undefined; // undefined
```

### 利用 instanceof 判断引用数据类型

```js
[1, 2, 3] instanceof Array; // true
{} instanceof Object; // true 
function () {} instanceof Function; // true

'1' instanceof String; // false
1 instanceof Number; // false
false instanceof Boolean; // false
```

## ajax 理解

ajax 是一种异步通信方式，直接由 js 脚本向服务器发起 http 通信，根据服务器返回的数据，更新网页响应部分而不刷新网页。

## 浏览器缓存

![缓存](/images/缓存.png)

### 什么是 Service worker

是一个服务器与浏览器之间的中间人角色，如果网站中注册了 service worker，那么它可以拦截当前网站所有的请求，然后进行判断。
如果需要向服务器发起请求，那么就转给服务器，如果可以直接使用缓存就直接返回缓存内容不再转给服务器，从而大大提高浏览体验。

#### Service worker 的生命周期

Service Worker 的生命周期与 web 页面完全分离。

它包含以下几个阶段:

- 下载

这是浏览器下载包含 Service worker 的 .js 文件的时候

- 安装

在安装过程中，通常需要缓存某些静态资产，如果某些资源已成功缓存，那么 Service worker 就安装完毕。如果任何文件下载失败或缓存失败，那么安装步骤将会失败，Service Worker 就无法激活（也就是说, 不会安装）。如果发生这种情况，不必担心，它下次会再试一次。

- 激活

安装成功之后，接下来就是激活步骤，通常会在这个阶段管理旧缓存。要为 web 应用程序安装 Service worker，必须先注册它，这可以在 JavaScript 代码中完成。注册 Service Worker 后，它会提示浏览器在后台启动 Service Worker 安装步骤

![Service worker 生命周期](/images/service_worker.png)

#### Service worker 经常配合哪种缓存使用

经常配合 CacheStorage 离线缓存一起使用

```js
if ("serviceWorker" in navigator) {
  navigator.serviceWorker.register("./sw.js");
}

// sw.js
var VERSION = "v1";

// 缓存
self.addEventListener("install", function (event) {
  event.waitUntil(
    caches.open(VERSION).then(function (cache) {
      return cache.addAll(["./start.html", "./static/jquery.min.js", "./static/mm1.jpg"]);
    })
  );
});

// 缓存更新
self.addEventListener("activate", function (event) {
  event.waitUntil(
    caches.keys().then(function (cacheNames) {
      return Promise.all(
        cacheNames.map(function (cacheName) {
          // 如果当前版本和缓存版本不一致
          if (cacheName !== VERSION) {
            return caches.delete(cacheName);
          }
        })
      );
    })
  );
});

// 捕获请求并返回缓存数据
self.addEventListener("fetch", function (event) {
  event.respondWith(
    caches
      .match(event.request)
      .catch(function () {
        return fetch(event.request);
      })
      .then(function (response) {
        caches.open(VERSION).then(function (cache) {
          cache.put(event.request, response);
        });
        return response.clone();
      })
      .catch(function () {
        return caches.match("./static/mm1.jpg");
      })
  );
});
```

### 浏览器缓存机制

- 通过在一段时间内保留已接收到的 web 资源的一个副本，如果在有效时间内，发起了对这个资源的再一次请求，浏览器将直接使用缓存副本，而不是再发起请求。
- 可以提高页面的打开速度，减少不必要的带宽消耗.

### 浏览器缓存位置

- Service Worker

> Service Worker 的缓存与浏览器其他内建的缓存机制不同，它可以让我们自由控制缓存哪些文件，如何匹配缓存，如何读取缓存，并且缓存是可持续性的。

- Memory Cache

> Memory Cahce 是内存中的缓存，读取内存中的数据肯定比读取磁盘中的数据快。但是内存缓存虽然读取高效，但是缓存持续性很短，会随着进程的释放而释放。

- Disk Cache

> Disk Cache 是存储在硬盘中的缓存，虽然读取速度慢，但是都可以存储到磁盘中，胜在容量和饿存储时效性上。

- Push Cache

> Push Cache 是 Http/2 中的内容，当以上三种缓存都没有命中时，它才会被使用，并且缓存时间也很短暂，只在 session 会话中存在，一旦会话结束将会被释放

- 网络请求

> 如果所有缓存都没有命中的话，那么只能发起请求来获取资源了。

### 浏览器缓存策略

#### 强缓存

- 介绍

强缓存可以通过设置两种 Http 头来实现，分别是 expries 和 cache-control。强缓存表示在缓存期间不需要发起请求，其 http 状态码为 200。

> expries 是 HTTP/1 的产物，表示资源会在设置的时间后过期，需要再次发起请求，因为 expries 受限于本地时间，如果修改了本地时间，可能会造成缓存失效。
> cache-control 出现于 HTTP/1.1，优先级高于 expries，该属性值表示资源会在 max-age 后贵气，过期后需再次发起请求。

- no-cache 和 no-store

> Cache-Control: no-cache 可以在本地进行缓存，但每次请求时，都要向服务器进行验证，如果服务器允许，才能够使用本地缓存。

> Cache-Control: no-store 这个才是响应缓存不被缓存的意思

#### 协商缓存

如果缓存过期了，就需要发起请求验证资源是否有更新。协商缓存可以通过设置两种 Http 头实现: Last-Modified 和 Etag。

当浏览器发起请求验证资源时，如果资源没有做改变，那么服务端就会返回 304 状态码，并且更新浏览器缓存的有效期。

- Last-Modified/If-Modified-Since

> Last-Modified 表示本地文件最后修改日期，If-Modified-Since 会将 Last-Modified 的值发送给服务器，询问服务器在该日期后资源是否有更新，如果有更新则将新资源发送回，否则返回 304

> Last-Modified 的弊端:
> 如果本地打开缓存文件，即使没有对文件进行修改，还是会造成 Last-Modified 被修改，服务端不能命中缓存导致发送相同的资源;
> 因为 Last-Modified 只能以秒计时，如果在不可感知的时间内修改完成文件，那么服务端就会任务资源还是命中了，不会返回正确的资源

- ETag/If-None-Match

> 因为 Last-Modified 的弊端，所以在 Http/1.1 出现了 Etag

> Etag 类似于文件指纹，If-None-Match 会将当前 Etag 发送给服务器，询问该资源 Etag 是否变动，有变动的话就将新的资源发送回来。并且 Etag 优先级 > Last-Modified

> 如果什么缓存策略都没设置，浏览器会采用一个启发式算法，通常会取响应头中的 Date 减去 Last-Modified 值的 10% 作为缓存时间

## get 和 post 在缓存方面的区别

get 类似于查找，可用缓存；post 必须与数据库进行交互，不能使用缓存

## 浏览器跨域解决方案

1. jsonp
2. document.domain + iframe
3. location.hash + iframe
4. window.name + iframe
5. postMessage
6. 跨域资源共享 cors
7. nginx 反向代理
8. node.js 中间件代理(proxy)
9. websocket 原生支持跨域

## 前端安全

- xss 攻击

> 跨站脚本攻击，是一种代码注入攻击，可以盗取用户的 cookie
> 预防方式: 数据转义、资源白名单

- csrf 跨站请求伪造

> 攻击者诱导用户进入第三方网站，然后向被攻击网站发送跨站请求，如果此时用户处于登陆状态，则可盗用用户信息进行操作。
> 预防方式：进行同源检测，csrf token 进行随即验证

- 点击劫持

> 点击劫持是一种视觉欺骗的攻击手段，攻击者将需要攻击的网站通过 iframe 嵌套的方式嵌入自己的网页中，并将 iframe 设置为透明，在页面中透初一个按钮诱导用户点击。
> 预防方式：通过在 http header 设置 x-frame-options 来防御 iframe 嵌套的点击劫持攻击

## 观察者模式对比发布订阅模式

> 发布订阅模式属于广义的观察者模式

> 观察者模式: 直接订阅目标事件

> 发布订阅模式: 中间多了一个调度中心，整体流程为：发布者 -> 调度中心 -> 调度者

## 从 url 输入到页面渲染整个流程

1. 解析 host
2. dns 查询，通过域名查找 ip
3. tcp 握手
4. 解析文件如何进行解码
5. 渲染流程，根据 html 代码生成 dom 树，根据 css 代码生成 cssom 树，将 css 与 dom 合并，整合为渲染树最后渲染。
6. 遇到 script 标签则暂停渲染，优先加载执行 js 代码，完成后继续

## http

### webSocket

webSocket 是一种在单个 TCP 连接上进行全双工通信的协议，webSocket 是基于应用层传输控制协议，它们都是全双工的，区别于普通的 http 请求，发起 webScoket 请求时，会增加一个请求头，用来告诉服务器这是
webSocket 请求

### http 三次握手

1. 建立连接时，客户端发送 SYN 包到服务端, 并进入 SYN_SEND 状态
2. 服务端收到 SYN 包后，向客户端发送 SYN+ACK 包，服务器进入 SYN_RCVD 状态
3. 客户端收到服务端的 SYN + ACK 包后，向服务器发送 ACK 确认包，发送完后，两边同时进入 establish 状态

### http 四次挥手

1. TCP 向客户端发送 FIN 包，用来关闭客户端到服务端之间的传输
2. 服务端收到 FIN 包，返回一个 ACK 包
3. 服务器关闭客户端的链接，发送一个 FIN 包给客户端
4. 客户端发送 ACK 报文进行确认

### http/1 和 http/2 对比

- 大幅度提高了网页性能，http/1 最大的请求数为 6，会造成队头阻塞，需等待其他资源请求完成后才能发起请求
- 多路复用
  > 用二进制分帧传输，用帧标识请求，因为一个 TCP 连接可在多条流中，即可以发送多个请求，因此可避免 http1 中队头阻塞问题
  > 通信双方都可以给对方发二进制帧，这种二进制帧的双向传输序列，也叫做流。HTTP/2 用流来在一个 TCP 连接上来进行数据帧的通信
- header 压缩
  > 采用 HPACK 算法，在客户端和服务端两端建立 "字典"，用索引号表示重复的字符串
  > 采用哈夫曼编码来压缩整数和字符串，可以达到 50% ～ 90% 的高压缩率
- 服务端推送
  > 服务器不再是完全被动地响应请求，也可以新建 "流" 主动向客户端发送消息
- 二进制传输
  > http/1 通过文本进行传输，http/2 引入了新的编码机制，所有传输数据都被分割，并采用二进制进行编码。

### http/3

http/3 重新构建了一个基于 UDP 的 QUIC 协议，解决了 http/2 下多路复用时丢包产生的问题，即解决了 TCP 协议的一些问题

### https

https 是在 http 基础上和 ssl/tls 证书结合起来的一种协议，保证了传输过程中的安全性，减少了恶意劫持的可能，很好的解决了 http 的多个缺点(被监听、被篡改、被伪装)

http + 加密 + 认证 + 完整性保护 = https

http 是铭文传输，即未被加密

## import 和 require 的区别

1. 模块的加载时机不同(require: 运行时加载，import: 会提升到整个模块的头部，在编译时加载)
2. require: 模块就是对象， 输入时必须查找对象属性；import: es6 模块不是对象

## commonjs 和 es6 module 区别

1. commonjs 默认采用的是非严格模式
2. es6 module 自动采用严格模式
3. commonjs 模块输出的是一个值的拷贝，es6 module 输出的是值的引用

## Promise 的一些考点

### Promise.all

- 一些特性

Promise.all 可接受参数可以为常量，也可以为 promise 对象

Promise.all 执行过程中一旦某个 promise 报错，将直接抛出异常，但同时其余的 prommise 也被执行了，因为 promise 实例在创建之初就被执行了

- 实现 Promise.all

```js
function PromiseAll(promiseArray) {
  return new Promise((resolve, reject) => {
    if (!Array.isArray(promiseArray)) {
      return reject(new Error("传入的参数必须是数组!"));
    }
    const res = [];
    const len = promiseArray.length;
    let index = 0;
    for (let i = 0; i < len; i++) {
      Promise.resolve(promiseArray[i])
        .then(val => {
          index++;
          res[i] = val;
          if (index === len) {
            resolve(res);
          }
        })
        .catch(err => {
          reject(err);
        });
    }
  });
}
```

### Promise 缓存

> 为了解决 promise 调用常量，每个页面都会调用这个常量，以解决多次调用接口对服务器造成请求的浪费。
> 可以使用 promise 缓存或者全局状态管理。

```js
const cacheMap = new Map();

function enableCache(target, name, descriptor) {
  const val = descriptor.value;
  descriptor.value = async function (...args) {
    const cacheKey = name + JSON.stringify(args);
    if (!cacheMap.get(cacheKey)) {
      const cacheValue = Promise.resolve(val.apply(this, args)).catch(_ => {
        cacheMap.set(cacheKey, null);
      });
      cacheMap.set(cacheKey, cacheValue);
    }
    return cacheMap.get(cacheKey);
  };
  return desciptor;
}

class PromiseClass {
  @enableCache
  static async getInfo() {}
}

PromiseClass.getInfo();
PromiseClass.getInfo();
PromiseClass.getInfo();
PromiseClass.getInfo();
```

## 前端内存处理

### 内存的声明周期

- 内存分配: 声明变量，函数，对象的时候，js 会自动分配内存
- 内存使用: 调用的时候，使用的时候
- 内存回收: js 自己的垃圾回收机制

### js 中的垃圾回收机制

#### 引用计数垃圾回收

> a 对象对 b 对象有访问权限，那么称为 a 引用 b 对象，只有当无引用时才会被成为垃圾，然后被回收
>
> 缺陷: 循环引用，造成内存泄漏

#### 标记清除法

> 对内存中存活对象做标记，标记结束后清除未被标记的
>
> 步骤:
>
> 1. 在运行的时候给存储在内存的所有变量加上标记
> 2. 从根部触发，能触及的对象，把标记清除
> 3. 哪些有标记的就被视为即将要删除的变量

#### 标记压缩法

> 使用内存碎片对内存分配

#### 增量标记

> 垃圾回收时间长所产生，用来交替运行

## js 内存泄漏的几种原因

1. 意外的全局变量
2. 未清除的定时器或回调函数
3. 脱离 DOM 的引用

   ```js
   const elements = {
     image: document.getElementById("image");
   };
   document.body.removeChild(document.getElementById("image")); // 会造成内存泄漏
   elements.image = null; // 正确的使用方式
   ```

4. 不合理地使用闭包

## 如何减少内存泄漏

1. 减少不必要的全局变量
2. 使用完数据后，及时解除引用

## e.target 和 e.currentTarget 的区别

- e.target 就是触发事件的标签，触发谁就是谁
- e.currentTarget 就是绑定事件的标签，绑定哪个事件输出的就是该事件

> 如果绑定的事件所在的组件没有子元素，那么 e.target === e.currentTarget
> 如果事件绑定在父元素中，且该父元素有子元素。当用 e.currentTarget 时，不管点击父元素所在区域还是子元素(当前事件)，都正确执行；若用 e.target 时，点击父元素所在区域无错，点击子元素区域，执行报错。报错的原因是事件没绑定在子元素上，是在父元素上，子元素要用 e.currentTarget 才正确
