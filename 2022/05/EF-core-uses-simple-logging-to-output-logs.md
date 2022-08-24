---
title: EF Core使用Simple Logging输出日志
slug: EF-core-uses-simple-logging-to-output-logs
description: 在使用EF Core的时候，很多时候需要知道EF Core实际执行的SQL语句是什么。
date: 2022-05-04 15:56:21
copyright: Reprint
author: MyIO
originaltitle: EF Core使用Simple Logging输出日志
originallink: https://mp.weixin.qq.com/s/QXBhG_ayoeCcE9dGsgbNKw
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_08.jpg
categories: EF Core
---

在使用EF Core的时候，很多时候需要知道EF Core实际执行的SQL语句是什么。

Simple Logging是EF Core提供的一项功能，可用于在开发和调试应用程序时轻松获取日志。这种形式的日志记录需要最少的配置，而不需要其他NuGet包。

## 功能一瞥

配置起来非常简单，只需在`DbContext.OnConfiguring`实现中调用`LogTo`方法即可：

```C#
public class DefaultDbContext : DbContext
{
    protected override void OnConfiguring(DbContextOptionsBuilder options)
    {
        ...

        options.LogTo(Console.WriteLine);
    }
    ...
}
```

`LogTo`需要一个Action委托接受字符串，比如`Console.WriteLine`,你也可以编写自定义方法决定如何输出日志。

## 筛选

默认情况下，Simple Logging记录Debug或更高级别的每条日志。这样会导致输出的日志过多，对调试没有任何帮助，可以限制只记录Information或更高级别的日志：

```C#
options.LogTo(Console.WriteLine, 
    Microsoft.Extensions.Logging.LogLevel.Information);
```

## 查询标记

但是，这样还是会产生很多日志。这时我们可以结合`查询标记`，帮助我们快速定位到需要的日志：

```C#
var users = context.User.TagWith("查询所有用户").ToList();
```

![](https://img1.dotnet9.com/2022/05/0801.jpg)