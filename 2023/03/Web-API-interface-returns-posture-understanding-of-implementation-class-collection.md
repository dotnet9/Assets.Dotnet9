---
title: Web API接口返回实现类集合的姿势了解
slug: Web-API-interface-returns-posture-understanding-of-implementation-class-collection
description: .NET Web API接口返回基类列表，测试接口只返回了基类的属性，实现类的属性怎么返回呢？
date: 2023-03-16 12:23:16
lastmod: 2023-03-19 20:55:32
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2023/03/cover_08.jpeg
categories: Web API
---

大家好，我是沙漠尽头的狼。

## 一. 问题描述

如下图，定义两个子类Student和Employ，都继承自抽象类PersonBase:

```csharp
public abstract class PersonBase
{
    public string Name { get; set; }

    protected PersonBase(string name)
    {
        Name = name;
    }
}

public class Student : PersonBase
{
    public string Number { get; set; }

    public Student(string name, string number) : base(name)
    {
        Number = number;
    }
}

public class Employ : PersonBase
{
    public string CompanyName { get; set; }

    public Employ(string name, string companyName) : base(name)
    {
        CompanyName = companyName;
    }
}
```

添加Web API接口返回基类集合：

```csharp
[ApiController]
[Route("[controller]")]
public class TestController : ControllerBase
{
    [HttpGet(Name = "GetDetails")]
    public IEnumerable<PersonBase> Get()
    {
        return new List<PersonBase>()
        {
            new Student("学生A", "学生号01"),
            new Employ("职员01", "百度")
        };
    }
}
```

接口返回值：

```json
[
  {
    "name": "学生A"
  },
  {
    "name": "职员01"
  }
]
```

发现问题了吗？Student类和Employ类实例的扩展属性（Student的Number属性，Employ的Company属性）都未被序列化展示，那么怎么序列化子类的所有属性呢？

## 二、实现类的所有属性序列化

参考微软文档《[如何使用System.Text.Json序列化派生类的属性](https://learn.microsoft.com/zh-cn/dotnet/standard/serialization/system-text-json/polymorphism?pivots=dotnet-7-0)》，有两种实现方式站长觉得比较简单。

### 2.1、.NET 7之前的实现方式

在 .NET 7 之前的版本中，System.Text.Json 不支持多态类型层次结构的序列化。 例如，如果接口的返回值类型为接口或抽象类集合，那么即使运行时类型具有其他属性，也只会序列化对接口或抽象类定义的属性。

解决方案：将接口返回值由`IEnumerable<PersonBase>`改为`object`，接口实现的`List<PersonBase>`改为`List<object>`:

```csharp
[HttpGet(Name = "GetDetails")]
public object Get()
{
    return new List<object>()
    {
        new Student("学生A", "学生号01"),
        new Employ("职员01", "百度")
    };
}
```

修改后，接口成功返回详细JSON信息：

```json
[
  {
    "number": "学生号01",
    "name": "学生A"
  },
  {
    "companyName": "百度",
    "name": "职员01"
  }
]
```

**原理：** 改为Object后，默认就是对实现类进行序列化了，改之前System.Text.Json只认识实现类的爸爸。

### 2.2、.NET 7及以后的实现方式

从 .NET 7 开始，System.Text.Json 支持使用特性标注的多态类型层次结构序列化和反序列化。

我们将接口恢复，在抽象类上添加特性，标明基类序列化时需要映射的子类类型：

```csharp
[JsonDerivedType(typeof(Student))]
[JsonDerivedType(typeof(Employ))]
public abstract class PersonBase
```

问题解决，接口返回值同上。

文档关于`JsonDerivedTypeAttribute`的描述：当放置在类型声明中时，则指示应选择指定的子类型进行多态序列化。 它还公开用于指定类型鉴别器的功能。

## 三、总结

上面两种方式看.NET版本选择，第二种方式需要您明确知道子类类型，详细使用请看微软文档：[如何使用System.Text.Json序列化派生类的属性](https://learn.microsoft.com/zh-cn/dotnet/standard/serialization/system-text-json/polymorphism?pivots=dotnet-7-0)

如果您有更好的方式欢迎留言探讨。

- 微信技术交流群：添加微信（dotnet9com）备注“入群”
- QQ技术交流群：771992300。

![](https://img1.dotnet9.com/site/knowledgeplanet_youhui.png)