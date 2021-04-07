---
title: Chrome 调试技巧
date: 2020-10-15
categories: [前端, js]
tags: 
  - 使用技巧
---

> 官方文档: [https://developers.google.com/](https://developers.google.com/)

> 隆重感谢: [掘金小册-你不知道的 Chrome 调试技巧](https://juejin.cn/book/6844733783166418958)

### 1、快速打印想要的 dom 元素

- 控制台输入 `$0`  即可打印出当前指针所选中的元素

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606962961066-f8c6bcff-e407-4e61-a727-4f67246c1891.png#align=left&display=inline&height=72&margin=%5Bobject%20Object%5D&name=image.png&originHeight=72&originWidth=273&size=3469&status=done&style=none&width=273)

### 2、如何给 Dom 元素打断点

- 鼠标右键选中元素，选中 `Break on`。
- `subtree modifications` 监听任何它内部的节点被 `移除` 或者 `添加`的事件
- `Break > attribute modifications`  监听任何当前选中的节点被 `添加`，`移除` 或者 `被修改值`的事件。此时属性被修改后将会产生断点调试。
- `Break > node removal`  监听被选中的元素被 `移除` 的事件。此时属性被删除后将会产生断点调试。

### 3、debug 函数

- 可以将想要打断点的函数传入进去，之后函数被调用时将会自动开启断点模式。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606962755747-0c7f7146-c878-47de-9ce8-b686d323fdbf.png#align=left&display=inline&height=124&margin=%5Bobject%20Object%5D&name=image.png&originHeight=124&originWidth=352&size=8413&status=done&style=none&width=352)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606962788973-1b1642f8-c89b-4ad6-a58a-62c3b386cfe5.png#align=left&display=inline&height=418&margin=%5Bobject%20Object%5D&name=image.png&originHeight=418&originWidth=1435&size=55406&status=done&style=none&width=1435)

### 4、console.log 添加样式

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606962692139-67609724-968f-48a4-b9cb-d3a17e89ef35.png#align=left&display=inline&height=55&margin=%5Bobject%20Object%5D&name=image.png&originHeight=55&originWidth=654&size=12329&status=done&style=none&width=654)

### 5、发现问题并定位代码的内存泄漏

- 选中 `Memory > Heap snapshot`  点击开始录制的小圆圈之后，chrome 将会生成快照，为了防止某些不必要的影响，录制之前最好先点一下垃圾回收。反复的执行可能被认为内存泄漏的部分后，多次生成快照，若内存一直在增加，没有被回收，说明内存已经泄漏。
- 选中垃圾回收按钮右侧下拉菜单里的 `Comparison` ，即可将选中的快照与它上一次快照进行对比。如 `delta`  的数据对比，从而快速定位到问题出现的位置。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606963059293-544a6af9-6f7c-40c1-9c70-0953335483ef.png#align=left&display=inline&height=793&margin=%5Bobject%20Object%5D&name=image.png&originHeight=793&originWidth=1919&size=95966&status=done&style=none&width=1919)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606963178973-dbc985f5-aefc-4bac-ae79-e9b62bf19485.png#align=left&display=inline&height=631&margin=%5Bobject%20Object%5D&name=image.png&originHeight=631&originWidth=1919&size=122190&status=done&style=none&width=1919)

### 6、$ 快速选中元素

- Chrome 内置了 $ 函数，它和 document.querySelector 功能相同，但只能选择到一个元素。$$ 将匹配到选择器所找到的所有元素

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606962607992-66922bde-af81-4856-a03a-920c36bb1671.png#align=left&display=inline&height=737&margin=%5Bobject%20Object%5D&name=image.png&originHeight=737&originWidth=567&size=74761&status=done&style=none&width=567)

### 7、使用 chrome 调试 node.js 程序

- `node --inspect-brk 文件名`

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606963302502-b4507a3c-9d9f-4653-ad80-353e509adf07.png#align=left&display=inline&height=39&margin=%5Bobject%20Object%5D&name=image.png&originHeight=39&originWidth=1033&size=8284&status=done&style=none&width=1033)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606963324179-88589108-e39a-462e-803e-a70bb2096085.png#align=left&display=inline&height=708&margin=%5Bobject%20Object%5D&name=image.png&originHeight=708&originWidth=2868&size=152314&status=done&style=none&width=2868)

### 8、点击一个元素，获取下面所有代码执行过程

- 方案 1: 定位该元素的时间处理函数，然后从该函数往后执行 `debug`，确定执行过程。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606962134298-7d0081df-45c3-497b-9ede-a1354c8a667e.png#align=left&display=inline&height=304&margin=%5Bobject%20Object%5D&name=image.png&originHeight=304&originWidth=623&size=27216&status=done&style=none&width=623)

- 方案 2: 找到当前点击所造成的影响（如：删除元素， `XHR`）等，通过点击 `Break > node removal` 等操作，在造成影响的位置下断点，通过查看 `call stack` 查看执行过程。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606962517481-012784d3-353b-47a6-a7ff-78abf31f6f32.png#align=left&display=inline&height=563&margin=%5Bobject%20Object%5D&name=image.png&originHeight=563&originWidth=938&size=57934&status=done&style=none&width=938)

- 方案 3: 使用 `getEventListeners` 获取 `Dom` 元素上绑定的事件。(注意：此方法只能在 `Chrome` 控制台使用)

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606967025807-3772a909-f699-4caf-add2-c5731f96873c.png#align=left&display=inline&height=318&margin=%5Bobject%20Object%5D&name=image.png&originHeight=318&originWidth=740&size=38124&status=done&style=none&width=740)

### 9、如何使用 Chrome 调试 Webpack 程序

1. 在想要调试的代码为止使用 debugger 打断点
1. 定位 webpack-dev-server 的命令文件
1. 使用 node --inspect-brk 打开调试服务
1. 打开 chrome devtool 进行调试

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606963641175-f70486be-9e35-4e1f-9623-e82720f7f850.png#align=left&display=inline&height=221&margin=%5Bobject%20Object%5D&name=image.png&originHeight=221&originWidth=1445&size=61528&status=done&style=none&width=1445)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606963678848-aaef5b5b-790f-41b5-baaf-a8f7323db103.png#align=left&display=inline&height=603&margin=%5Bobject%20Object%5D&name=image.png&originHeight=603&originWidth=991&size=81341&status=done&style=none&width=991)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606963786975-515fd78b-8273-4ea0-9f23-9ecd0432c169.png#align=left&display=inline&height=56&margin=%5Bobject%20Object%5D&name=image.png&originHeight=56&originWidth=1101&size=16555&status=done&style=none&width=1101)

### 10、使用错误断点，让程序在错误处暂停

- 方案 1: 在控制面板里直接点击错误信息
- 方案 2: 点击 `Sources`  后点击 `Pause on exceptions`  最后勾选 `Pause on exceptions`。之后刷新页面即可

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606964277828-1264593c-59f8-43c4-b13a-4bf37b26b95e.png#align=left&display=inline&height=372&margin=%5Bobject%20Object%5D&name=image.png&originHeight=372&originWidth=901&size=46311&status=done&style=none&width=901)

### 11、使用 Chrome 作为代码编辑工具

- 选中 `Sources > Filesystem`  然后关联本地文件夹即可

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606964541164-bfa7a598-c5df-4ac4-9782-93fc5e7cdd82.png#align=left&display=inline&height=620&margin=%5Bobject%20Object%5D&name=image.png&originHeight=620&originWidth=1067&size=131817&status=done&style=none&width=1067)

### 12、使用 Chrome 的 Snippets 功能保存代码片段，拒绝重复的代码

- 选中 `Sources > Snippets`  创建新的代码片段
- 输入 `Command + p`  后输入 `i`  后执行

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606964857768-858ca1ae-b5d2-4147-b2f8-1632e49c85b8.png#align=left&display=inline&height=446&margin=%5Bobject%20Object%5D&name=image.png&originHeight=446&originWidth=1845&size=79129&status=done&style=none&width=1845)

### 13、使用 Chome 实时保存更改的 css 样式

- 选中 `Source > Overrides` ，之后点击 `+`  选择一个本地文件夹用来保存更改后的样式，最后勾选 `Enable Local Overrides` 。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606965107087-3d47d726-c4fb-4d0c-9d48-38c5422db524.png#align=left&display=inline&height=114&margin=%5Bobject%20Object%5D&name=image.png&originHeight=114&originWidth=1522&size=23539&status=done&style=none&width=1522)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606965155644-099a9d4e-fec1-4433-9d24-55f52727ca22.png#align=left&display=inline&height=114&margin=%5Bobject%20Object%5D&name=image.png&originHeight=114&originWidth=395&size=9886&status=done&style=none&width=395)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606965811764-61b27e31-8e8f-4b23-b93f-46c0a52f8069.png#align=left&display=inline&height=417&margin=%5Bobject%20Object%5D&name=image.png&originHeight=417&originWidth=831&size=60690&status=done&style=none&width=831)

### 14、使用 Chrome 查看使用 Overrides 更改后内容与之前内容的对比

- 输入 `Command + shift + p` ，搜索 `show changes`

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606965562313-e3ac2cd8-614a-457e-a0c6-912660c48270.png#align=left&display=inline&height=95&margin=%5Bobject%20Object%5D&name=image.png&originHeight=95&originWidth=519&size=7584&status=done&style=none&width=519)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606965855015-feca9397-66d0-40ac-ab41-e349241c14e1.png#align=left&display=inline&height=651&margin=%5Bobject%20Object%5D&name=image.png&originHeight=651&originWidth=1136&size=118201&status=done&style=none&width=1136)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606965980764-b6e363ca-172d-4a63-b141-137b186f97e7.png#align=left&display=inline&height=435&margin=%5Bobject%20Object%5D&name=image.png&originHeight=435&originWidth=928&size=40226&status=done&style=none&width=928)

### 15、查看 Chrome 请求是由谁发起的

- 选择 `NetWork > Initialtor` ，不但可以查看调用位置，也可获取触发位置。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606966265653-8fd5a74d-5486-4b50-951d-3ac4aa2dafb9.png#align=left&display=inline&height=650&margin=%5Bobject%20Object%5D&name=image.png&originHeight=650&originWidth=1077&size=93651&status=done&style=none&width=1077)
![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606966315917-5db64699-69bd-4b64-b041-36e35a03bedc.png#align=left&display=inline&height=96&margin=%5Bobject%20Object%5D&name=image.png&originHeight=96&originWidth=744&size=15715&status=done&style=none&width=744)

### 16、Chrome 性能监测工具

- `Command + shift + p`  输入 `show performance monitor`  即可实时查看程序运行的性能。
  - `CPU usage` ： `cpu` 的监听
  - `JS heap size` : 内存占用监听，如添加到 `window`  对象上的事件不回收会造成内存溢出。
  - `DOM Nodes`: 内存中所分配的 `dom` 节点的个数
  - `JS envnt listeners`: `js` 中已经绑定的事件个数

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606966748112-fdd53dde-e210-458e-992a-68429c28b334.png#align=left&display=inline&height=319&margin=%5Bobject%20Object%5D&name=image.png&originHeight=319&originWidth=1919&size=60517&status=done&style=none&width=1919)

### 17、使用 Chrome 做性能调优，迅速定位问题

- 选择 `performance`  点击录制按钮，可以查看从录制开始到停止时间内 `js` 代码运行的情况。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606967325061-7ddbd154-20e4-4ff2-b903-ca2a8e05c378.png#align=left&display=inline&height=766&margin=%5Bobject%20Object%5D&name=image.png&originHeight=766&originWidth=1919&size=111943&status=done&style=none&width=1919)

### 18、使用 Chrome 动画检查器，检查修改 css 动画，提升效率

- `Command + shift + p`  输入 `show animations`  打开动画的调试器。

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1606967977814-c5fa0c33-8952-46b2-84d2-a1315a766658.png#align=left&display=inline&height=600&margin=%5Bobject%20Object%5D&name=image.png&originHeight=600&originWidth=1919&size=108889&status=done&style=none&width=1919)

### 19、使用 Chrome 查看代码覆盖率，检查代码覆盖情况

- `Command + shift +p`  输入 `show coverage`

![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1607172475188-07f61e49-f764-427e-8b1b-71a45b552b27.png#align=left&display=inline&height=555&margin=%5Bobject%20Object%5D&name=image.png&originHeight=555&originWidth=1893&size=172193&status=done&style=none&width=1893)

### 20、copy() 复制控制台内容

- 可以通过全局的方法  `copy()`  在  `console`  里  `copy`  任何你能拿到的资源，包括我们在后面[第六节]会提到的那些变量。例如  `copy($_)`  或  `copy($0)`

![1.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608278259306-ac7e6a27-9b73-424c-955c-9d60cde38aad.gif#align=left&display=inline&height=802&margin=%5Bobject%20Object%5D&name=1.gif&originHeight=802&originWidth=1332&size=1780049&status=done&style=none&width=1332)

### 21、$\_查看上一次程序执行结果

- `$_`  是对上次执行的结果的 **引用**

\*\*
**![image.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608278776314-7be09c82-b10e-4760-9dc0-ecccb00e01c9.png#align=left&display=inline&height=184&margin=%5Bobject%20Object%5D&name=image.png&originHeight=368&originWidth=1014&size=116869&status=done&style=none&width=507)**

### 22、$i 配合 chrome 插件引入第三方包

- 有时你只是想玩玩新出的 `npm` 包，现在不用再大费周章去建一个项目测试了，只需要在 [Chrome 插件:Console Importer](https://chrome.google.com/webstore/detail/console-importer/hgajpakhafplebkdljleajgbpdmplhie/related) 的帮助之下，快速的在 `console` 中引入和测试一些 `npm` 库。

![1.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608278892044-9115b62e-c6ff-40ac-8330-253b84191be6.gif#align=left&display=inline&height=802&margin=%5Bobject%20Object%5D&name=1.gif&originHeight=802&originWidth=1332&size=1817520&status=done&style=none&width=1332)

### 23、使用实时表达式

- `DevTools` 在 `Console` 面板中引入了一个非常漂亮的附加功能，这是一个名为 `Live expression` 的工具。只需按下 "眼睛" 符号，你就可以在那里定义任何 `JavaScript` 表达式。 它会不断更新，所以表达的结果将永远，存在 :-)

![1.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608281376215-967b1d31-4690-4f9d-a5f7-868365ce3f6a.gif#align=left&display=inline&height=716&margin=%5Bobject%20Object%5D&name=1.gif&originHeight=716&originWidth=1102&size=2073027&status=done&style=none&width=1102)

### 24、通过 'h' 来隐藏元素

- 按一下  `'h'`  就可以隐藏你在元素面板中选择的元素。再次按下 '`h`' 可以使它出现。某些的时候这很有用：例如你想截图，但你想去掉里面的敏感信息。

![1.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608281579251-0b47cee8-0d8d-4445-a858-2d409edd6e7c.gif#align=left&display=inline&height=802&margin=%5Bobject%20Object%5D&name=1.gif&originHeight=802&originWidth=1332&size=1651328&status=done&style=none&width=1332)

### 25、拖动 & 放置 元素

- 当你想看看页面的某一部分在  `DOM`  树的不同位置的显示效果时，只需要拖动放置它(到指定的位置)，就像在机器上的其他任何地方一样 :-)

![2.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608281657769-7775b7d5-2c49-44e8-9f5f-0d52ced8ac17.gif#align=left&display=inline&height=642&margin=%5Bobject%20Object%5D&name=2.gif&originHeight=642&originWidth=1066&size=891397&status=done&style=none&width=1066)

### 26、使用 `control` (按钮) 来移动元素!

- 如果你只是想移动你当前选中的元素，在  `DOM`  结构中往上挪一点或者往下挪一点，而不是拖动和放置，你同样可以使用`[ctrl]` + `[⬆]` / `[ctrl]` + `[⬇]` (`[⌘]` + `[⬆]` / `[⌘]` + `[⬇]` on Mac).

![3.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608281716760-63613a96-c29f-488f-b7e9-f16994b0c2b6.gif#align=left&display=inline&height=802&margin=%5Bobject%20Object%5D&name=3.gif&originHeight=802&originWidth=1332&size=3046583&status=done&style=none&width=1332)

### 27、元素面板中类似于基础编辑器的操作

- 从某一点来看，我们可以拖动，放置，编辑，复制(当然，以及使用 `[ctrl]` + `[v]` 来粘贴)， 所以我们可以在元素面板里把 `HTML` 结构搞得一团糟。在任意一个编辑器中都有一个标准，那么如何撤回你的操作呢？

- 使用`[ctrl]` + `[z]` (`[⌘]` + `[z]` on Mac)撤销我们的任何改动。 使用 `[ctrl]` + `[shift]` + `[z]`重新编辑我们的任何修改。

![1.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608281829967-1a9d3db2-270a-4f23-9d25-52585c80aed7.gif#align=left&display=inline&height=642&margin=%5Bobject%20Object%5D&name=1.gif&originHeight=642&originWidth=1066&size=5633497&status=done&style=none&width=1066)

### 28、`Shadow editor` 阴影编辑器

- 你可以通过在  `Style`  面板中点击靠近  `box-shadow`  属性或者  `text-shadow`  属性的  `阴影方形符号`  来打开它：

![1.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608281887248-eed208b7-7636-4172-b428-ba7a07bec474.gif#align=left&display=inline&height=718&margin=%5Bobject%20Object%5D&name=1.gif&originHeight=718&originWidth=576&size=722085&status=done&style=none&width=576)

### 29、Timing function editor 定时函数编辑器

- 也称为 `Cubic bezier(贝塞尔)` 编辑器。贝塞尔曲线是一串用来定义 `CSS` 的动画速度在整个动画过程中如何变化的 `魔法数值` 。我们将其定义为 `transition-timing-function` 或者 `animation-timing-function` CSS 属性。

- 像之前说的 `Color picker` 和 `Shadow editor` 一样，直接点击我们刚刚提到的属性(或者他们的简写形式：`trasition`， `animation` - 请注意：如果`timing` 函数的值没有设置在这个简写的形式中，这个符号不会显示出来)边上的曲线符号：

![1.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608281968319-1ce527ef-daf5-4eb7-8d8c-f3c2747bfa4d.gif#align=left&display=inline&height=590&margin=%5Bobject%20Object%5D&name=1.gif&originHeight=590&originWidth=474&size=3886698&status=done&style=none&width=474)

### 30、插入样式规则的按钮

当你把鼠标放在样式选择器的选择区域的最后时，你会看到几个让你可以快速的使用 `Color` 和 `Shadow` 编辑器添加 `CSS` 属性的按钮：

- `text-shadow`

- `box-shadow`

- `color`

- `background-color`

![1.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608282091218-b368c609-f0ae-49c2-a1cf-fbc31424917e.gif#align=left&display=inline&height=558&margin=%5Bobject%20Object%5D&name=1.gif&originHeight=558&originWidth=632&size=1958540&status=done&style=none&width=632)

### 31、在元素面板中展开所有的子节点

- 一个一个的去点击级联的  `▶`  按钮太慢了，不如使用右击节点后的  `expand recursively`  命令：

![1.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608282162863-1a2d16de-4444-497e-b927-0f85bfd59964.gif#align=left&display=inline&height=520&margin=%5Bobject%20Object%5D&name=1.gif&originHeight=520&originWidth=760&size=7216217&status=done&style=none&width=760)

### 32、控制传感器

- 如果你正在你的应用中使用一些获取位置信息的 `API` 而且想要测试一下它，总不能开着车环绕世界吧，(其实也不是不行 😉)。

- `Drawer` 里的 `Sensors(传感器)` 面板可以让你模拟特定的位置: 支持从预定义的位置中进行选择，添加自己的位置，或者手动键入纬度/经度。选定的值将被 `navigator.geolocation.watchPosition`（或 `.getCurrentPosition` ）报告。

- 如果你的 `App` 使用加速计，传感器面板也可以模拟你设备在 3D 空间中的位置！

![1.gif](https://cdn.nlark.com/yuque/0/2020/gif/732231/1608282513575-bb313d74-b584-4f58-ac1b-f577ffe719c0.gif#align=left&display=inline&height=728&margin=%5Bobject%20Object%5D&name=1.gif&originHeight=728&originWidth=774&size=1259259&status=done&style=none&width=774)
