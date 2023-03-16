---
title: Web API接口返回实现类集合的姿势了解
slug: Web-API-interface-returns-posture-understanding-of-implementation-class-collection
description: .NET Web API接口返回基类列表，测试接口只返回了基类的属性，实现类的属性怎么返回呢？
date: 2023-03-16 12:23:16
copyright: Default
draft: false
cover: https://img1.dotnet9.com/2023/03/cover_08.png
categories: .NET相关
---

如下图，定义两个子类Student和Employ，都继承自抽象类PersonBase，Web API接口返回基类集合，默认接口只返回了基类的属性：

![默认无法序列化派生类属性](https://img1.dotnet9.com/2023/03/0801.png)

**我们怎么返回子类的属性呢？**

很简单，只需要在基类PersonBase上加上子类的类型声明即可，这种方式需要您明确知道子类类型，详细使用请看微软文档：[如何使用System.Text.Json序列化派生类的属性](https://learn.microsoft.com/zh-cn/dotnet/standard/serialization/system-text-json/polymorphism?pivots=dotnet-7-0)

![声明子类型解决序列化问题](https://img1.dotnet9.com/2023/03/0802.png)

如果您有更好的方式欢迎留言探讨。