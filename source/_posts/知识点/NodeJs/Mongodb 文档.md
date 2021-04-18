---
title: Mongodb 文档
date: 2020-12-13
categories: [知识点, NodeJS, Mongodb]
tags:
  - 文档
---

`mongodb` 是一种非关系型数据库，不必写 `sql` 语句，其操作类似于 `json` ，方便后端工作者进行开发。

`mongoose` 是一种 `mongodb` 的 `orm` 框架，方便操作和使用，其中，`Schema` 是关键。

## 一、常用命令：

| 常用命令                | 介绍                                       |
| ----------------------- | ------------------------------------------ |
| `show dbs`              | 显示数据库列表                             |
| `show collections`      | 显示当前数据库中的集合（类似数据库中的表） |
| `show users`            | 显示用户                                   |
| `use <db name>`         | 切换/创建当前数据库                        |
| `db.help()`             | 显示数据库操作命令                         |
| `db.dropDatabase()`     | 删除当前使用数据库                         |
| `db.createCollection()` | 创建一个集合(表)                           |
| `db/db.getName()`       | 查看当前所在数据库                         |
| `db.user.findOne()`     | 查询第一条数据                             |
| `load()`                | 加载文件                                   |
|                         |                                            |

### 1. find 查找

> `@params: [条件，指定打印字段的名称]`

- 查找 【`age >= 20` 并且 `age <= 30` 的】数据

```javascript
db.user.find(
  {
    age: {
      $gte: 20,
      $lte: 30
    }
  },
  {
    name: true,
    age: true,
    _id: false
  }
);
```

- 查找【21 岁 和 10 岁的】数据

```javascript
db.user.find(
  {
    age: {
      $in: [21, 10]
    }
  },
  {
    name: true,
    age: true,
    _id: false
  }
);
```

- 查找 【 要么 `age <= 10`，要么 `pc.brand = 'IBM'` 】的数据

> 与或非：`$and, $or, $not`

```javascript
db.user.find(
  {
    $or: [(age: { $lte: 10, "pc.brand": "IBM" })]
  },
  { name: true, age: true, _id: false }
);
```

- 数组查询：查找【喜欢篮球和敲代码的】数据

> `$all`：全部满足查询，`$in`：满足任意一个条件查询，`$size`：根据个数查询

```javascript
db.user.find(
  {
    hobby: { $all: ["篮球", "敲代码"] }
  },
  { name: true, age: true, _id: false }
);
```

- 分页查询：

> `limit`：每次查询几条，`skip`：从第几条开始，`sort`：排序方式(-1：降序，1：升序)

```javascript
db.user.find({}, { name: true, age: true, _id: false }).limit(1).skip(0).sort({ age: -1 });
```

- 逐条打印 `forEach`

```javascript
var db = connect("me");
var userlist = db.user.find();
userlist.forEach(user => {
  /** 业务逻辑处理 if else */
  printjson(user);
});
```

### 2. update 更新

> `db.user.update(条件, 方法)`

- `$inc`：进行加减操作
- `$set`：修改一个指定的`key`值

```javascript
//	修改数组中的某个元素的值
db.user.update({ name: "xiaofeng" }, { $set: { "age.0": 40 } });
```

- `$unset` ：删除一个`key`值和对应的`value`值
- `upsert` ： 如果当前找不到这个值， 那么就插入这个值

```javascript
db.user.update({ name: "xiaofeng" }, { $set: { age: 10 } }, { upsert: true });
```

- `multi`：给所有数据都增加属性 如果为`false`或者不加那么只有第一条数据会加

```javascript
db.user.update({}, { $set: { hobby: [] } }, { multi: true });
```

- `$push`：给数组字段里增加一个新的元素

```javascript
db.user.update({ name: "xiaowu" }, { $push: { hobby: "吃鸡" } });
```

- `$addToSet` ： 查找是否存在，如果不存在则`push`进去，如果存在则不`push`
- `$each`：在数组中插入多个元素

```javascript
//	在数组中插入多个元素
var newHobby = ["鸡", "鸭", "鹅"];
db.user.update({ name: "wxq" }, { $addToSet: { hobby: { $each: newHobby } } });
```

- `findAndModify` ： 有返回值，可以根据返回值来进行逻辑处理

```javascript
//	应答式的修改方法
var modify = {
  findAndModify: "user",
  query: { name: "wxq" },
  update: { $set: { age: 21 } },
  new: true //	true：返回修改之后的值，false：返回修改之前的值
};
var result = db.runCommand(modify);
printjson(result);
```

### 3. 插入 insert

> 使用`insert()`或`save()`方法向集合中插入文档。如果不指定`_id`字段，`save()`方法类似`insert()`方法；如果指定`_id`字段，则会更新该`_id`的数据。

### 4. 删除

> ```javascript
> db.collection.drop();
> ```

````



### 索引


> 建立索引可以提高查询效率，不是所有的情况都要建立索引；
> 当频繁查询数据时常用。



- 建立索引：



```javascript
db.tel.ensureIndex({tel: 1})
````

- 获取索引：

```javascript
db.tel.getIndexes();
```

- 删除索引：

```javascript
db.tel.dropIndex({ tel: 1 });
```

## 二、mongoose

> **介绍：**`Mongoose`是`MongoDB`的一个对象模型工具，是基于`node-mongodb-native`开发的`MongoDB nodejs`驱动，可以在**异步的环境下执行**。同时它也是针对`MongoDB`操作的一个对象模型库，封装了`MongoDB`对文档的的一些增删改查等常用方法，让`NodeJS`操作`Mongodb`数据库变得更加**灵活简单**。

### 1、安装

> `npm install mongoose`

### 2、数据库连接

```javascript
// 初始化mongoose
const mongoose = require("mongoose");
const db = "mongodb://localhost/shop";

exports.connect = () => {
  // 连接数据库
  mongoose.connect(db, {
    useNewUrlParser: true // 解析url
  });
  // 监听数据库连接
  mongoose.connection.on("disconnected", () => {
    mongoose.connect(db);
  });
  // 数据库出错
  mongoose.connection.on("error", error => {
    console.log(error);
    mongoose.connect(db);
  });
  // 连接的时候
  mongoose.connection.once("open", () => {
    console.log("mongoodb connected success");
  });
};
```

### 3、Schema

#### 3.1 定义一个 Schema

> **介绍**：数据属性模型(传统意义的表结构)，又或着是“集合”的模型骨架。

```javascript
var mongoose = require("mongoose");
var TestSchema = new mongoose.Schema({
  name: { type: String, unique: true }, // unique: true代表当前用户名是唯一的
  age: { type: Number, default: 18 },
  gender: { type: Boolean, default: true }
});
```

#### 3.2 Model

> **介绍**：由`Schema`构造生成的模型，除了`Schema`定义的数据库骨架以外，还具有数据库操作行为，类似于管理数据库属性、行为的类
> 创建`Model`模型，需要指定：**1.集合名称，2.集合的`Schema`结构对象**

```javascript
// 创建Model
var TestModel = mongoose.model("test1", TestSchema);
```

> `test1`：数据库中的集合名称，当我们对其添加数据时如果`test1`已经存在，则会保存到其目录下，如果未存在，则会创建`test1`集合，然后在保存数据。

#### 3.3 Entity

> **介绍**：由`Model`创建的实体，使用`save`方法保存数据，`Model`和`Entity`都有能影响数据库的操作，但`Model`比`Entity`更具操作性。

```javascript
// 创建Entity
var TestEntity = new TestModel({
  name: "Lenka",
  age: 36,
  email: "lenka@qq.com"
});
console.log(TestEntity.name); // Lenka
console.log(TestEntity.age); // 36
```

#### 3.4 创建集合

```javascript
var mongoose = require("mongoose");
mongoose.connect("mongodb://127.0.0.1:27017/test");
mongoose.connection.on("connected", function () {
  console.log("yes");
});

var TestSchema = new mongoose.Schema({
  name: { type: String },
  age: { type: Number, default: 0 },
  email: { type: String },
  time: { type: Date, defaultL: Date.now }
});

var TestModel = mongoose.model("test1", TestSchema);

var TestEntity = new TestModel({
  name: "zs",
  age: 20,
  email: "aa@bb.com"
});
TestEntity.save(function (error, doc) {
  if (error) {
    console.log("error:" + error);
  } else {
    console.log(doc);
  }
});
```

### 4、操作

#### 4.1 查询

##### 4.1.1 find 基本查询

> `obj.find(查询条件, callback)`

##### 4.1.2 find 过滤查询

- 属性过滤> `find(Conditions, field, callback)`，其中`field`省略或者设置为`null`，则返回所有属性

##### 4.1.3 单条数据查询

- `findOne`

> `findOne(Conditions, callback)`，注意：只返回第一个符合条件的文档数据

- `findById`> `findById(_id, callback)`，注意：只接受文档的`_id`作为参数

#### 4.2 Model 保存

- `Model.create(文档数据, callback)`

#### 4.3 entity 保存

```javascript
var Entity = new Model({ name: "zs", age: 27 });
Entity.save(function (error, doc) {
  if (error) {
    console.log(error);
  } else {
    console.log(doc);
  }
});
```

#### 4.4 数据更新

- `obj.update(查询条件, 更新对象, callback)`

#### 4.5 数据删除

- `obj.remove(查询条件, callback)`
