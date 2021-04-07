---
title: Vue2 diff 算法
date: 2020-07-20
categories: [前端, vue]
tags:
  - diff 算法
---

## 结构图

![结构图.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608703926503-0a8b70ad-ee2b-4b15-9f24-cb3008e56d1e.png#align=left&display=inline&height=699&margin=%5Bobject%20Object%5D&name=%E7%BB%93%E6%9E%84%E5%9B%BE.png&originHeight=699&originWidth=1424&size=257532&status=done&style=none&width=1424)

## 三个维度

> 执行时按照以下顺序依次执行

### tree diff

> `tree diff` 是对树的每一层进行遍历,如果某个组件不存在了，则会直接销毁。

> 如图所示，左边是旧属，右边是新属，第一层是 `R` 组件，一模一样，不会发生变化；
> 第二层进入 `Component DIFF`，同一类型组件继续比较下去，发现 `A` 组件没有，所以直接删掉 `A、B、C` 组件；
> 继续第三层，重新创建 `A、B、C` 组件。

![tree-diff.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608703948400-4b9d5cfa-ddf5-404f-83f0-7de2c979afd2.png#align=left&display=inline&height=317&margin=%5Bobject%20Object%5D&name=tree-diff.png&originHeight=317&originWidth=533&size=61410&status=done&style=none&width=533)

### component diff

> 如图所示，第一层遍历完，进行第二层遍历时，D 和 G 组件是不同类型的组件，不同类型组件直接进行替换，将 D 删掉，再将 `G` 重建。

![component-diff.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608703964946-362729fd-486b-461b-8210-c191053da87c.png#align=left&display=inline&height=244&margin=%5Bobject%20Object%5D&name=component-diff.png&originHeight=244&originWidth=566&size=81300&status=done&style=none&width=566)

### element diff

> `Element DIFF` 紧接着以上统一类型组件继续比较下去，常见类型就是列表。同一个列表由旧变新有三种行为，插入、移动和删除，它的比较策略是对于每一个列表指定 `key`，先将所有列表遍历一遍，确定要新增和删除的，再确定需要移动的。
> 如图所示，第一步将 `D` 删掉，第二步增加 `E`，再次执行时 `A` 和 `B` 只需要移动位置即可。

![element-diff.png](https://cdn.nlark.com/yuque/0/2020/png/732231/1608703980058-5f99c653-2a0a-46c4-9bb5-f1af80d45afe.png#align=left&display=inline&height=331&margin=%5Bobject%20Object%5D&name=element-diff.png&originHeight=331&originWidth=523&size=81565&status=done&style=none&width=523)
