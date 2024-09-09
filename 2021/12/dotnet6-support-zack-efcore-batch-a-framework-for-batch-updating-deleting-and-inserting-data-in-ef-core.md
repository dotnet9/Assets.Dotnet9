---
title: 支持.NET6！EF Core中批量执行更新、删除、插入数据的框架Zack.EFCore.Batch
slug: dotnet6-support-zack-efcore-batch-a-framework-for-batch-updating-deleting-and-inserting-data-in-ef-core
description: 在`EF Core`中`批量`执行`更新`、`删除`、`插入`数据的框架`Zack.EFCore.Batch`已经发布新版，新版增加了对`.NET 6`的支持，数据批量插入的时候支持`ValueConverter`，彻底解决了`“更新数据的时候，当两列的表达式等价时候出现的The count of columns should be even异常”`。
date: 2021-12-25 19:23:26
copyright: Reprinted
author: 杨中科
originalTitle: 支持.NET6！EF Core中批量执行更新、删除、插入数据的框架Zack.EFCore.Batch
originalLink: https://mp.weixin.qq.com/s/MYxVGxa_DQnn4XMIDryd9Q
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_40.jpeg
categories: 
    - .NET
tags: 
    - C#
    - EF Core
    - 批量更新
    - 批量删除
    - 批量插入
---

我开发的在`EF Core`中`批量`执行`更新`、`删除`、`插入`数据的框架`Zack.EFCore.Batch`已经发布新版，新版增加了对`.NET 6`的支持，数据批量插入的时候支持`ValueConverter`，彻底解决了`“更新数据的时候，当两列的表达式等价时候出现的The count of columns should be even异常”`。

目前项目已经有 200 多个 star，解决了 40 多个 issue，所以比较成熟了。

使用这个库，我们可以用如下的方式进行数据的批量更新、删除：

```C#
await ctx.DeleteRangeAsync<Book>(b => b.Price > n || b.AuthorName == "zack yang");

await ctx.BatchUpdate<Book>()
    .Set(b => b.Price, b => b.Price + 3)
    .Set(b => b.Title, b => s)
    .Set(b => b.AuthorName,b=>b.Title.Substring(3,2)+b.AuthorName.ToUpper())
    .Set(b => b.PubTime, DateTime.Now)
    .Where(b => b.Id > n || b.AuthorName.StartsWith("Zack"))
    .ExecuteAsync();
```

`Zack.EFCore.Batch`支持数据的`批量快速插入`。

当然，如果您使用`SqlSugar`、`Dapper`之类的`ORM`也可以实现类似的效果，我这个库最大的特色就是它是基于`EF Core`的扩展，可以复用`EF Core`的特性。如果您不使用`EF Core`，那么可以忽略它。

> 原文作者：杨中科
>
> 原文链接：https://mp.weixin.qq.com/s/MYxVGxa_DQnn4XMIDryd9Q
>
> 项目地址：https://github.com/yangzhongke/Zack.EFCore.Batch
