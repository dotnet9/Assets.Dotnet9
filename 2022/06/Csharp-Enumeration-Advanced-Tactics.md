---
title: C# 枚举高级战术
slug: Csharp-Enumeration-Advanced-Tactics
description: 阅读体验好，也不容易编错
date: 2022-06-02 21:32:27
copyright: Reprinted
author: liamwang 精致码农
originaltitle: C# 枚举高级战术
originallink: https://mp.weixin.qq.com/s/gKUIEVoG4aW69x6ExIMvYg
draft: False
cover: https://img1.dotnet9.com/2022/06/cover_03.jpg
categories: .NET
tags: C#
---

![](https://img1.dotnet9.com/2022/06/cover_03.jpg)

文章开头先给大家出一道面试题：

>在设计某小型项目的数据库（假设用的是 MySQL）时，如果给用户表（User）添加一个字段（Roles）用来存储用户的角色，你会给这个字段设置什么类型？提示：要考虑到角色在后端开发时需要用枚举表示，且一个用户可能会拥有多个角色。

映入你脑海的第一个答案可能是：varchar 类型，用分隔符的方式来存储多个角色，比如用 `1|2|3` 或 `1,2,3` 来表示用户拥有多个角色。当然如果角色数量可能超过个位数，考虑到数据库的查询方便（比如用 INSTR 或 POSITION 来判断用户是否包含某个角色），角色的值至少要从数字 10 开始。方案是可行的，可是不是太简单了，有没有更好的方案？

更好的回答应是整型（int、bigint 等），优点是写 SQL 查询条件更方便，性能、空间上都优于 varchar。但整型毕竟只是一个数字，怎么表示多个角色呢？此时想到了二进制位操作的你，心中应该早有了答案。

且保留你心中的答案，接着看完本文，或许你会有意外的收获，因为实际应用中可能还会遇到一连串的问题。为了更好的说明后面的问题，我们先来回顾一下枚举的基础知识。

## 枚举基础

枚举类型的作用是限制其变量只能从有限的选项中取值，这些选项（枚举类型的成员）各自对应于一个数字，数字默认从 0 开始，并以此递增。例如：

```csharp
public enum Days
{
    Sunday, Monday, Tuesday, // ...
}
```

其中 Sunday 的值是 0，Monday 是 1，以此类推。为了一眼能看出每个成员代表的值，一般推荐显示地将成员值写出来，不要省略：

```csharp
public enum Days
{
    Sunday = 0, Monday = 1, Tuesday = 2, // ...
}
```

C# 枚举成员的类型默认是 int 类型，通过继承可以声明枚举成员为其它类型，比如：

```csharp
public enum Days : byte
{
    Monday = 1,
    Tuesday = 2,
    Wednesday = 3,
    Thursday = 4,
    Friday = 5,
    Saturday = 6,
    Sunday = 7
}
```

枚举类型一定是继承自 byte、sbyte、short、ushort、int、uint、long 和 ulong 中的一种，不能是其它类型。

下面是几个枚举的常见用法（以上面的 Days 枚举为例）：

```csharp
// 枚举转字符串
string foo = Days.Saturday.ToString(); // "Saturday"
string foo = Enum.GetName(typeof(Days), 6); // "Saturday"
// 字符串转枚举
Enum.TryParse("Tuesday", out Days bar); // true, bar = Days.Tuesday
(Days)Enum.Parse(typeof(Days), "Tuesday"); // Days.Tuesday

// 枚举转数字
byte foo = (byte)Days.Monday; // 1
// 数字转枚举
Days foo = (Days)2; // Days.Tuesday

// 获取枚举所属的数字类型
Type foo = Enum.GetUnderlyingType(typeof(Days))); // System.Byte

// 获取所有的枚举成员
Array foo = Enum.GetValues(typeof(MyEnum);
// 获取所有枚举成员的字段名
string[] foo = Enum.GetNames(typeof(Days));
```

另外，值得注意的是，枚举可能会得到非预期的值（值没有对应的成员）。比如：

```csharp
Days d = (Days)21; // 不会报错
Enum.IsDefined(typeof(Days), d); // false
```

即使枚举没有值为 0 的成员，它的默认值永远都是 0。

```csharp
var z = default(Days); // 0
```

枚举可以通过 Description、Display 等特性来为成员添加有用的辅助信息，比如：

```csharp
public enum ApiStatus
{
    [Description("成功")]
    OK = 0,
    [Description("资源未找到")]
    NotFound = 2,
    [Description("拒绝访问")]
    AccessDenied = 3
}

static class EnumExtensions
{
    public static string GetDescription(this Enum val)
    {
        var field = val.GetType().GetField(val.ToString());
        var customAttribute = Attribute.GetCustomAttribute(field, typeof(DescriptionAttribute));
        if (customAttribute == null) { return val.ToString(); }
        else { return ((DescriptionAttribute)customAttribute).Description; }
    }
}

static void Main(string[] args)
{
    Console.WriteLine(ApiStatus.Ok.GetDescription()); // "成功"
}
```

上面这些我认为已经包含了大部分我们日常用到的枚举知识了。下面我们继续回到文章开头说的用户角色存储问题。

## 用户角色存储问题

我们先定义一个枚举类型来表示两种用户角色：

```csharp
public enum Roles
{
    Admin = 1,
    Member = 2
}
```

这样，如果某个用户同时拥有 Admin 和 Member 两种角色，那么 User 表的 Roles 字段就应该存 3。那问题来了，此时若查询所有拥有 Admin 角色的用户的 SQL 该怎么写呢？

对于有基础的程序员来说，这个问题很简单，只要用位操作符逻辑与（‘&’）来查询即可。

```sql
SELECT * FROM `User` WHERE `Roles` & 1 = 1;
```

同理，查询同时拥有这两种角色的用户，SQL 语句应该这么写：

```sql
SELECT * FROM `User` WHERE `Roles` & 3 = 3;
```

对这条 SQL 语句用 C# 来实现查询是这样的（为了简单，这里使用了 Dapper）：

```csharp
public class User
{
    public int Id { get; set; }
    public Roles Roles { get; set; }
}

connection.Query<User>(
    "SELECT * FROM `User` WHERE `Roles` & @roles = @roles;",
    new { roles = Roles.Admin | Roles.Member });
```

对应的，在 C# 中要判断用户是否拥有某个角色，可以这么判断：

```csharp
// 方式一
if (user.Roles & Roles.Admin == Roles.Admin)
{
    // 做管理员可以做的事情
}

// 方式二
if (user.Roles.HasFlag(Roles.Admin))
{
    // 做管理员可以做的事情
}
```

同理，在 C# 中你可以对枚举进行任意位逻辑运算，比如要把角色从某个枚举变量中移除：

```csharp
var foo = Roles.Admin | Roles.Member;
var bar = foo & ~foo;
```

这就解决了文章前面提到的用整型来存储多角色的问题，不论数据库还是 C# 语言，操作上都是可行的，而且也很方便灵活。

## 枚举的 Flags 特性

下面我们提供一个通过角色来查询用户的方法，并演示如何调用，如下：

```csharp
public IEnumerable<User> GetUsersInRoles(Roles roles)
{
    _logger.LogDebug(roles.ToString());
    _connection.Query<User>(
        "SELECT * FROM `User` WHERE `Roles` & @roles = @roles;",
        new { roles });
}

// 调用
_repository.GetUsersInRoles(Roles.Admin | Roles.Member);
```

`Roles.Admin | Roles.Member` 的值是 3，由于 Roles 枚举类型中并没有定义一个值为 3 的字段，所以在方法内 roles 参数显示的是 3。3 这个信息对于我们调试或打印日志很不友好。在方法内，我们并不知道这个 3 代表的是什么。为了解决这个问题，C# 枚举有个很有用的特性：FlagsAtrribute。

```csharp
[Flags]
public enum Roles
{
    Admin = 1,
    Member = 2
}
```

加上这个 Flags 特性后，我们再来调试 `GetUsersInRoles(Roles roles)` 方法时，roles 参数的值就会显示为 `Admin|Member` 了。简单来说，加不加 Flags 的区别是：

```csharp
var roles = Roles.Admin | Roles.Member;
Console.WriteLing(roles.ToString()); // "3"，没有 Flags 特性
Console.WriteLing(roles.ToString()); // "Admin, Member"，有 Flags 特性
```

给枚举加上 Flags 特性，我觉得应当视为 C# 编程的一种最佳实践，在定义枚举时尽量加上 Flags 特性。

## 解决枚举值冲突：2 的幂

到这，枚举类型 Roles 一切看上去没什么问题，但如果现在要增加一个角色：Mananger，会发生什么情况？按照数字值递增的规则，Manager 的值应当设为 3。

```csharp
[Flags]
public enum Roles
{
    Admin = 1,
    Member = 2,
    Manager = 3
}
```

能不能把 Manager 的值设为 3？显然不能，因为 Admin 和 Member 进行位的或逻辑运算（即：Admin | Member） 的值也是 3，表示同时拥有这两种角色，这和 Manager 冲突了。那怎样设值才能避免冲突呢？

既然是二进制逻辑运算“或”会和成员值产生冲突，那就利用逻辑运算或的规律来解决。我们知道“或”运算的逻辑是两边只要出现一个 1 结果就是 1，比如 1|1、1|0 结果都是 1，只有 0|0 的情况结果才是 0。那么我们就要避免任意两个值在相同的位置上出现 1。根据二进制满 2 进 1 的特点，只要保证枚举的各项值都是 2 的幂即可。比如：

```shell
1:  00000001
2:  00000010
4:  00000100
8:  00001000
```

再往后增加的话就是 16、32、64...，其中各值不论怎么相加都不会和成员的任一值冲突。这样问题就解决了，所以我们要这样定义 Roles 枚举的值：

```csharp
[Flags]
public enum Roles
{
    Admin = 1,
    Member = 2,
    Manager = 4,
    Operator = 8
}
```

不过在定义值的时候要在心中小小计算一下，如果你想懒一点，可以用下面这种“位移”的方法来定义：

```csharp
[Flags]
public enum Roles
{
    Admin    = 1 << 0,
    Member   = 1 << 1,
    Manager  = 1 << 2,
    Operator = 1 << 3
}
```

一直往下递增编值即可，阅读体验好，也不容易编错。两种方式是等效的，常量位移的计算是在编译的时候进行的，所以相比不会有额外的开销。

## 总结

本文通过一道小小的面试题引发一连串对枚举的思考。在小型系统中，把用户角色直接存储在用户表是很常见的做法，此时把角色字段设为整型（比如 int）是比较好的设计方案。但与此同时，也要考虑到一些最佳实践，比如使用 Flags 特性来帮助更好的调试和日志输出。也要考虑到实际开发中的各种潜在问题，比如多个枚举值进行或（‘|’）运算与成员值发生冲突的问题。

![](https://img1.dotnet9.com/2022/06/0301.jpg)