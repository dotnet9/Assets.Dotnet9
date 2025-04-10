---
title: C# 不同的方式检查Null
slug: csharp-check-nulls-in-different-ways
description: 多了解点没错的
date: 2021-06-19 14:24:28
copyright: Original
originalTitle: C# 不同的方式检查Null
draft: False
cover: https://img1.dotnet9.com/2021/06/cover_03.jpg
categories: 
    - .NET
tags: 
    - C#
---

> - 原文链接：https://www.thomasclaudiushuber.com/2020/03/12/c-different-ways-to-check-for-null/
> - 原文作者：Thomas
> - 翻译：沙漠尽头的狼

检查参数值是否为空的经典方法是什么？如果您已经使用 C 语言开发了一段时间，您可能会熟悉以下经典语法：

```C#
public static int CountNumberOfSInName(string name)
{
  if (name == null)
  {
    throw new ArgumentNullException(nameof(name));
  }

  return name.Count(c => char.ToLower(c).Equals('s'));
}
```

自 C# 7 开始，您可以使用 is 关键字进行 null 检查，如下面的代码段所示：

```C#
if (name is null)
{
  throw new ArgumentNullException(nameof(name));
}
```

但是对于 C# 7，甚至还有一个更短的语法。还引入了丢弃。它们是未使用且被忽略的变量，在代码中用下划线（\_）。结合空合并运算符（??），可以这样编写空检查：

```C#
_ = name ?? throw new ArgumentNullException(nameof(name));
```

也就是说，整个方法看起来就像这样：

```C#
public static int CountNumberOfSInName(string name)
{
  _ = name ?? throw new ArgumentNullException(nameof(name));

  return name.Count(c => char.ToLower(c).Equals('s'));
}
```

老实说，我真的很喜欢使用丢弃的最后一种方法，但是对于一些开发人员来说，这可能太多了。我认为 is 关键字非常清晰易读。它是我的最爱。

is 关键字还有一个很大的优点，就是它忽略了任何==/！=运算符或者重载特定类。不管是否有操作符重载，它都将执行 null 检查。这比仅仅使用==更好。你可以在[这篇博文](https://www.thomasclaudiushuber.com/2020/03/19/c-why-you-should-prefer-the-is-keyword-over-the-operator/)中了解更多。

## C# 9.0 中的 Is 关键字和 Not 模式

在 C# 9.0 中，如果您想检查对象不为 null，那么将 is 表达式与逻辑 not 模式结合起来这是非常强大的。在 C# 9.0 之前，您必须使用如下的 is 表达式来检查对象是否为 null：

```C#
if (!(name is null)) { }
```

一些开发人员倾向于使用以下语法来检查 name 不为 null：

```C#
if (name is object) { }
```

但是上面的陈述既不可读也不容易理解。这就是为什么许多开发人员仍然喜欢经典的方式：

```C#
if (name != null) { }
```

但从 C# 9.0 开始，您可以编写如下的非空检查，我认为这是真正可读的代码：

```C#
if (name is not null) { }
```

## 总结

So, with C# 9.0, you can write your null / not-nulll checks like below, and I think that’s readable:
因此，使用 C# 9.0，您可以编写 null/not-null 检查，如下所示，我认为这是可读的：

```C#
if (name is null) { }

if (name is not null) { }
```

祝您编程愉快！
