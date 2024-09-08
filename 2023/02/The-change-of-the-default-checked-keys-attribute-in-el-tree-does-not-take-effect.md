---
title: el-tree中default-checked-keys属性变化不生效
slug: The-change-of-the-default-checked-keys-attribute-in-el-tree-does-not-take-effect
description: 这里是笔者在开发 MAUI 应用时踩的坑，以及一些笔记的汇总。
date: 2023-01-18 22:02:29
copyright: Reprinted
author: 水星梦月
originalTitle: el-tree中default-checked-keys属性变化不生效
originalLink: https://blog.csdn.net/monparadis/article/details/114087838
draft: false
cover: https://img1.dotnet9.com/2023/02/cover_01.png
categories: .NET
tags: MAUI
---

> 站长开发[Dotnet9](https://dotnet9.com)网站后台前端的文章编辑分类管理功能时遇到了`element-ui`的`tree`组件使用问题，个人尝试未解决，下面一文是一个前端大佬给出的解决方案，至于问题是什么、怎么解决的，请接着看吧，希望对大家有用。

**正文开始**

---

前言：使用`element-ui`中`tree`组件时，天真的以为只要动态修改`default-checked-keys`这个属性值得变化，默认勾选的值也会随之变化，实际发现并不是这样的。

我们先看看官网上对`default-checked-keys`的定义：（看完依旧觉得没问题）

![](https://img1.dotnet9.com/2023/02/0101.png)

于是我就去探寻了一下原因是啥：监听到`defaultCheckedKeys`的变化后遍历数组，添加选中状态，但是当`defaultCheckedKeys`数组减少时，前面设置的选中的`key`不会取消。

原因是：`tree.vue`中使用`watch`监听`default-checked-keys`值得变化，调用了以下方法：

![](https://img1.dotnet9.com/2023/02/0102.png)

在 tree-store.js 中又使用了此方法

![](https://img1.dotnet9.com/2023/02/0103.png)

终于知道原因了，只有监听到`default-checked-keys`值变化时才会遍历`default-checked-keys`数组设置给里面的值设置选中状态，但是没有取消选中状态的操作，所以当在`default-checked-keys`原有的数组里面增加需要选中的数据，新增的节点会被选中，反之则不会！！！

**这该如何是好？？？**

思路：

1. 修改依赖代码

我在这边博客中已提到：[el-tree 处理大量数据](https://blog.csdn.net/monparadis/article/details/114025100)

2. 再仔细看看文档找思路，找到了：就是调用`setCheckedKeys`方法，将需要选中的`key`值数组传进去，他会重新设置选中状态：

![](https://img1.dotnet9.com/2023/02/0104.png)

**使用该方法的时候注意了：**

1. `tree`已经存在`dom`中后调用，如果`tree`没有加载完成，`setCheckedKeys`是不存在的，所以我们需要使用`this.$nextTick(callback)`方法,该方法会在 dom 加载完毕之后,执行回调函数。

2. 调用完这个方法后，如果你使用懒加载加载节点的话你手动设置的半选状态会没有哦，记得重置一下半选。

> 本文来自转载。
>
> 作者：水星梦月
>
> 原文标题：el-tree 中 default-checked-keys 属性变化不生效
>
> 原文链接：https://blog.csdn.net/monparadis/article/details/114087838
