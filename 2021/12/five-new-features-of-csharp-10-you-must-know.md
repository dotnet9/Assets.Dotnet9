---
title: C# 10 必知的五大新功能
slug: five-new-features-of-csharp-10-you-must-know
description: C# 的 GitHub 页面上记载了一长串诱人的想法，其中一些令人头疼的问题仍在讨论中。
date: 2021-12-12 10:01:23
copyright: Reprinted
author: Matthew MacDonald
originalTitle: C# 10 必知的五大新功能
originalLink: https://medium.com/young-coder/a-closer-look-at-5-new-features-in-c-10-f99738b0158e
draft: False
cover: https://img1.dotnet9.com/2021/12/cover_11.png
categories: 
    - .NET
tags: 
    - C# 10
---

C# 的 GitHub 页面上记载了一长串诱人的想法，其中一些令人头疼的问题仍在讨论中。如果你想知道 C# 10 中究竟包含了哪些新功能，可以等待 11 月新版本的发布。或者，你也可以关注 C# 团队展示的他们最喜欢的功能。在最近的微软 Build 大会上，C# 的首席设计师 Mads Torgersen 透漏了一些目前正在进行的工作。以下是该语言的下一个版本将会提供的五大新功能。

![](https://img1.dotnet9.com/2021/12/1101.jpg)

## 1. global using

C# 的源代码文件开头一般都会导入一堆命名空间。下面是一个普通的 ASP.NET Web 应用程序的代码片段：

```C#
using LoggingTestApp.Data;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Hosting;
using Microsoft.AspNetCore.HttpsPolicy;
using Microsoft.AspNetCore.Identity;
using Microsoft.AspNetCore.Identity.UI;
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;
using Serilog;
using System;
using System.Collections.Generic;
using System.Linq;
using System.Threading.Tasks;
namespace LoggingTestApp
{
	public class Startup
    {
        ...
    }
}
```

这段代码的写法没有什么特别之处。以前，命名空间的导入可以让我们快速了解某个类正在使用哪些库。然而如今，这只不过是一堆不得不写又没人去看的代码了。

C# 10 引入了一种新模式，允许你使用关键字 global 定义整个项目的命名空间导入。推荐做法是，将全局导入放在一个单独的文件中（每个项目一个），可以命名为 usings.cs 或 imports.cs。其中的内容大致为：

```C#
global using Microsoft.AspNetCore.Builder;
global using Microsoft.AspNetCore.Hosting;
global using Microsoft.AspNetCore.HttpsPolicy;
global using Microsoft.AspNetCore.Identity;
global using Microsoft.AspNetCore.Identity.UI;
global using Microsoft.EntityFrameworkCore;
global using Microsoft.Extensions.Configuration;
global using Microsoft.Extensions.DependencyInjection;
global using Microsoft.Extensions.Hosting;
global using System;
global using System.Collections.Generic;
global using System.Linq;
global using System.Threading.Tasks;
```

然后就可以简化原来的文件了：

```C#
using LoggingTestApp.Data;
using Serilog;
namespace LoggingTestApp
{
	public class Startup
    {
        ...
    }
}
```

Visual Studio 会突出显示重复的命名空间（即同时在全局文件和本地文件中导入的命名空间）。尽管这不是错误，但删除重复的命名空间可以减少代码量，并将注意力集中在特定文件正在使用的特殊命名空间上。

## 2. 文件范围的命名空间

C# 10 提供了另一种简化代码的方法：声明文件范围的命名空间。文件范围的命名空间会自动应用于整个文件，而且无需缩进。

换句话说，下面这种写法：

```C#
namespace LoggingTestApp
{
	public class Startup
    {
        ...
    }
}
```

可以变成：

```C#
namespace LoggingTestApp;
public class Startup
{
    ...
}
```

如果在使用了文件范围命名空间的文件中，再添加一个命名空间块，则会创建一个嵌套命名空间：

```C#
namespace Company.Product;
// This block creates the namespace Company.Product.Component
namespace Component
{
}
```

C# 设计者认为这个改动可以清理水平空间的浪费（就像 global using 清理了垂直空间的浪费一样）。总体目标是让代码更短、更窄、更简洁。但这些变化也可以降低新手学习 C#的难度。结合 global using 与文件范围的命名空间，只需几行代码就可以创建出一个 Hello World 控制台应用程序。

## 3. 空参数检查

本着减少样板代码的精神，C# 提供了一个非常好的新功能：空参数检查。你肯定编写过需要检查空值的方法。比如，如下代码：

```C#
public UpdateAddress(int personId, Address newAddress)
{
	if (newAddress == null)
    {
		throw new ArgumentNullException("newAddress");
    }
    ...
}
```

如今，你只需要在参数名称末尾添加“!!”，C#就会自动加入这种空参数检查。上述代码可以简化为：

```C#
public UpdateAddress(int personId, Address newAddress!!)
{
    ...
}
```

现在，如果传递一个空值给 Address，就会自动抛出 ArgumentNullException。

这种细节可能看似微不足道，但实际上这是非常简单又很有价值的优化语言的方式。大量研究表明，导致程序出错的原因往往是由于非常容易避免的错误反复发生，不是因为代码中的概念太复杂，而是因为阅读代码很累，而人类的注意力有限。减少代码量可以减少审查代码所需的时间，处理代码所需的认知负荷，以及由于注意力减弱而忽略某些错误的可能性。

## 4. required 属性

以前，我们只能通过类构造函数来确保正确地创建对象。如今，我们经常使用更加轻量级的结构，比如下面这个记录中自动实现的属性：

```C#
public record Employee
{
    public string Name { get; init; }
    public decimal YearlySalary { get; init; }
    public DateTime HiredDate{ get; init; }
}
```

在创建这类轻量级对象的实例时，我们可能会使用对象的初始化语法：

```C#
var theNewGuy = new Employee
{
    Name = "Dave Bowman",
    YearlySalary = 100000m,
    HiredDate = DateTime.Now()
};
```

但是，如果你的对象中的某些属性是必须的，该怎么办？你可以像以前一样，添加一个构造函数，但如此一来就需要添加更多的样板代码了。此外，将值从一个参数复制到属性也是另一个很容易理解但很常见的错误。

C# 10 引入的关键字 required 可以消灭这类问题：

```C#
public record Employee
{
    public required string Name { get; init; }
    public decimal YearlySalary { get; init; }
    public DateTime HiredDate{ get; init; }
}
```

如此一来，如果不设置 Name 属性就无法创建 Employee 了。

## 5. 关键字 field

多年来，为了通过自动实现属性简化代码，C# 团队做出了大量努力，上面的 Employee 记录就是一个很好的例子，它使用 get 和 init 关键字声明了三个不可变的属性。数据存储在三个私有字段中，但这些字段都是自动创建的，无需人工干预。而且你永远不会看到这些字段。

自动实现的属性很棒，但它们的作用也仅限于此。当无法使用自动实现的属性时，你就必须添加支持字段到类，并编写正常的属性方法，就像回到 C# 2 一样。但是 C# 10 中提供了一个关键字 field，可以自动创建支持字段。

例如，假设你想创建一个记录，用于处理初始属性值。在下面的代码中，我们对 Employee 类进行了一些修改，确保 HiredDate 字段只包含来自 DateTime 对象的日期信息（不包含时间信息）：

```C#
public record Employee
{
    public required string Name { get; init; }
    public decimal YearlySalary { get; init; }
    public DateTime HiredDate{ get; init => field = value.Date(); }
}
```

这段代码非常整洁、简单，而且很接近声明式。

你可以使用关键字 field 访问 get、set 或 init 中的字段。而且，你可能需要验证某个属性，就像验证普通类中的属性一样：

```C#
private string _firstName;
public string FirstName
{
    get
    {
        return _firstName;
    }
    set
    {
        if (value.Trim() == "")
            throw new ArgumentException("No blank strings");
        _firstName = value;
    }
}
```

你可以使用 field 来验证自动实现的属性：

```C#
public string FirstName {get;
    set
    {
        if (value.Trim() == "")
            throw new ArgumentException("No blank strings");
        field = value;
    }
}
```

本质上，只要不需要修改属性的数据类型，就不需要自行声明支持字段。

## 6. 总结

当然，C# 10 中的新功能肯定不止这个五个。还有一些表达式方面的变化，以及一个有争议的变动：在接口中定义静态成员。我们只有耐心等待了。

总体来看，C# 10 的发展重点很明确，即减少代码量，提供更多便利性，减轻开发人员的负担。

> 原文作者：Matthew MacDonald
>
> 原文链接：https://medium.com/young-coder/a-closer-look-at-5-new-features-in-c-10-f99738b0158e
>
> 译者：弯月
>
> 责编：欧阳姝黎
>
> 出品：CSDN（ID：CSDNnews）
>
> 声明：本文由 CSDN 翻译，转载请注明来源。
