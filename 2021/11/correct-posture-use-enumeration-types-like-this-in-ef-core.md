---
title: 正确姿势？EF Core 中这样使用枚举类型？
slug: correct-posture-use-enumeration-types-like-this-in-ef-core
description: 在EntityFramework Core中的实体是不直接支持枚举类型的操作，这让我们在开发过程中带来不少的麻烦
date: 2021-11-09 10:06:30
copyright: Reprinted
author: waitaction
originalTitle: 正确姿势？EF Core 中这样使用枚举类型？
originalLink: https://blog.csdn.net/waitaction/article/details/88639152
draft: False
cover: https://img1.dotnet9.com/2021/11/cover_03.jpeg
categories: .NET
tags: 
    - .NET CORE
    - ORM
    - EF Core
---

![](https://img1.dotnet9.com/2021/11/cover_03.jpeg)

在 EntityFramework Core 中的实体是不直接支持枚举类型的操作，这让我们在开发过程中带来不少的麻烦，下面总结一下在 ef core 中使用枚举的方法.

例如下面的**MsgInfo**实体，对应着数据库表**MsgInfo**，其中字段**SendState**发送状态在业务逻辑上有**发送成功**和**发送失败**两种枚举状态。但 ef 把它生成了 int 类型，而不是枚举，当然也不能修改成枚举，这样会导致 ef 写入和读取数据异常。

## 原来的实体

```C#
public partial class MsgInfo
{
    public string Id { get; set; }
    public string UserAddress { get; set; }
    public string Content { get; set; }
    public int SendState { get; set; }
}
```

这里新增一个字段**SendStateEnum**设置为枚举类型，并使用 `[NotMapped]` 为不映射到数据库，为了防止输出 HTTP 时被序列化，也可以添加 `[Newtonsoft.Json.JsonIgnore]` 标记

> 需添加 Nuget 包 Newtonsoft.Json

修改完的实体代码如下

## 修改实体

```C#
public partial class MsgInfo
{
    public string Id { get; set; }
    public string UserAddress { get; set; }
    public string Content { get; set; }
    public int SendState { get; set; }

    [NotMapped]
    [Newtonsoft.Json.JsonIgnore]
    public SendStateEnum SendStateEnum
    {
        get
        {
            switch (SendState)
            {
                case (int)SendStateEnum.Fail:
                    return SendStateEnum.Fail;
                case (int)SendStateEnum.Success:
                    return SendStateEnum.Success;
                default:
                    return SendStateEnum.UnKnow;
            }
        }
        set
        {
            SendState = (int)value;
        }

    }
}
public enum SendStateEnum
{
    Success = 1,
    Fail = 2,
    UnKnow =3
}
```

添加了**SendStateEnum**字段后，以后使用 ef core 操作或者读取**SendStateEnum** 代替了 SendState 字段（**注：站长实测与原文有出入，如下面的代码（原文代码）貌似应该使用 SendState 进行 EF Core 的操作，如有不同意见请留言指正。**）

```C#
using (var context = new FrameworkDbContext())
{
    var result = context.MsgInfo.Where(m => m.SendStateEnum == SendStateEnum.Success);
}
```

当然，为了防止原来的 **SendState** 字段被使用，可以添加标记 `[Obsolete]` 提醒用户该字段 **SendState** 已过时。

## 修改后的最终实体代码如下

```C#
public partial class MsgInfo
{
    public string Id { get; set; }
    public string UserAddress { get; set; }
    public string Content { get; set; }

    [Obsolete]
    public int SendState { get; set; }

    [NotMapped]
    [Newtonsoft.Json.JsonIgnore]
    public SendStateEnum SendStateEnum
    {
        get
        {
            switch (SendState)
            {
                case (int)SendStateEnum.Fail:
                    return SendStateEnum.Fail;
                case (int)SendStateEnum.Success:
                    return SendStateEnum.Success;
                default:
                    return SendStateEnum.UnKnow;
            }
        }
        set
        {
            SendState = (int)value;
        }
    }
}
public enum SendStateEnum
{
    Success = 1,
    Fail = 2,
    UnKnow =3
}
```
