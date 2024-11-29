---
title: 几条EF core性能优化，让你程序健步如飞
slug: Several-EF-core-performance-optimizations-make-your-program-fly-fast
description: 数条建议
date: 2022-05-04 15:41:07
copyright: Reprinted
author: 清和时光
originalTitle: 几条EF core性能优化，让你程序健步如飞
originalLink: https://www.cnblogs.com/qingheshiguang/p/13559561.html
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_07.png
categories: 
    - EF Core
tags: 
    - .NET
    - ORM
    - EF Core
---

## 1. 使用 EF.Functions.xxx 进行查询

### 1.1 使用 EF.Functions.Like 进行模糊查询要比 StartsWith、Contains 和 EndsWith 方法生成的 SQL 语句性能更优。

A. Contains 语句，生成的 sql 为：

```csharp
var data3 = dbContext.T_UserInfor.Where(u => u.userName.Contains("p")).ToList();
```

用的是 charindex

![](https://img1.dotnet9.com/2022/05/0701.png)

B. EF.Functions.Like 语句生成的 sql 为：（Like 搭配 SQL 查询的通配符使用）

```csharp
  var data1 = dbContext.T_UserInfor.Where(u => EF.Functions.Like(u.userName, "%p%")).ToList();
  //或者
  var data2 = (from p in dbContext.T_UserInfor
               where EF.Functions.Like(p.userName, "%p%")
               select p).ToList();
```

用的是 Like

![](https://img1.dotnet9.com/2022/05/0702.png)

**PS**：在传统的.Net 中，还有种用法 SqlMethods，详见：[https://www.cnblogs.com/yaopengfei/p/11805980.html](https://www.cnblogs.com/yaopengfei/p/11805980.html)

### 1.2. 还有 EF.Functions.DateDiffDay (DateDiffHour、DateDiffMonth),求天、小时、月之间的数量

**PS**：在 EF Core 中 StartsWith、Contains 和 EndsWith 模糊查询实际分别被解析成为 Left、CharIndex 和 Right，而不是 Like,而 EF.Functions.Like 会解析成 Like 语句。

详见：[https://www.cnblogs.com/tdfblog/p/entity-framework-core-like-query.html](https://www.cnblogs.com/tdfblog/p/entity-framework-core-like-query.html)

## 2. 添加 Z.EntityFramework.Plus.EFCore 依赖使用一些特殊的语法

这个是免费的，但 Z.EntityFramework.Plus 的一些批量数据操作的包是收费的

1. EFCore 删除必须先查询再删除，优化后可直接删除：

```csharp
context.User.Where(t => t.Id == 100).Delete();
```

2. 优化更新语句：

```csharp
context.User.Where(t => t.Id == 4).Update(t =>new User() { NickName = "2224114" ,Phone = "1234"} );
```

## 3. 正确使用 Find(id=10)来代替 FirstOrDefault(t=>t.id=10)

Find 会优先查询缓存，当前面已经查询过这条数据的时候使用，而 FirstOrDefault 每次都会查询数据库；当 id=10 的数据被修改之后，find 查出的数据是新数据。

## 4. 禁用实体追踪

当我们从数据库中查询出数据时，上下文就会创建实体快照，从而追踪实体。在调用 SaveChanges 时，实体有任何更改都会保存到数据库中。

但是当我们只需要查询出实体而不需要修改时（只读），实体追踪就没有任何用途了。这时我们就可以调用 AsNoTracking 获取非追踪的数据，这样可以提高查询性能。具体代码如下：

```csharp
var users = db.Users.AsNoTracking().ToList();
```

**注：如果是多表查询可以在查询前**

```csharp
db.ChangeTracker.QueryTrackingBehavior = QueryTrackingBehavior.NoTracking;
```

这样就把所有表查询设置成了非追踪状态

## 5. 判断查询出的列表是否有值时，使用 .Any(),尽量不使用 .Count(); .FirstOrDefault()
