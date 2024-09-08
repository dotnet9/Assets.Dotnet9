---
title: C#中的闭包和意想不到的坑
slug: Closures-and-unexpected-pits-in-Csharp
description: 使用委托或者lambda表达式，也可以在C#中使用闭包。
date: 2022-06-15 22:49:32
copyright: Reprinted
author: 老胡写代码
originalTitle: C#中的闭包和意想不到的坑
originalLink: https://www.cnblogs.com/deatharthas/p/13166987.html
draft: False
cover: https://img1.dotnet9.com/2022/06/cover_11.jpeg
categories: .NET
tags: C#
---

虽然闭包主要是函数式编程的玩意儿，而 C#的最主要特征是面向对象，但是利用委托或 lambda 表达式，C#也可以写出具有函数式编程风味的代码。同样的，使用委托或者 lambda 表达式，也可以在 C#中使用闭包。

> 根据 WIKI 的定义，闭包又称语法闭包或函数闭包，是在函数式编程语言中实现语法绑定的一种技术。闭包在实现上是一个结构体，它存储了一个函数（通常是其入口地址）和一个关联的环境（相当于一个符号查找表）。闭包也可以延迟变量的生存周期。

嗯。。看定义好像有点迷糊，让我们看看下面的例子吧

```csharp
class Program
{
    static Action CreateGreeting(string message)
    {
        return () => { Console.WriteLine("Hello " + message); };
    }

    static void Main()
    {
        Action action = CreateGreeting("DeathArthas");
        action();
    }
}
```

这个例子非常简单，用 lambda 表达式创建一个 Action 对象，之后再调用这个 Action 对象。

但是仔细观察会发现，当 Action 对象被调用的时候，`CreateGreeting`方法已经返回了，作为它的实参的 message 应该已经被销毁了，那么为什么我们在调用 Action 对象的时候，还是能够得到正确的结果呢？

原来奥秘就在于，这里形成了闭包。虽然 CreateGreeting 已经返回了，但是它的局部变量被返回的 lambda 表达式所捕获，延迟了其生命周期。怎么样，这样再回头看闭包定义，是不是更清楚了一些？

闭包就是这么简单，其实我们经常都在使用，只是有时候我们都不自知而已。比如大家肯定都写过类似下面的代码。

```csharp
void AddControlClickLogger(Control control, string message)
{
	control.Click += delegate
	{
		Console.WriteLine("Control clicked: {0}", message);
	}
}
```

这里的代码其实就用了闭包，因为我们可以肯定，在 control 被点击的时候，这个 message 早就超过了它的声明周期。合理使用闭包，可以确保我们写出在空间和时间上面解耦的委托。

不过在使用闭包的时候，要注意一个陷阱。因为闭包会延迟局部变量的生命周期，在某些情况下程序产生的结果会和预想的不一样。让我们看看下面的例子。

```csharp
class Program
{
    static List<Action> CreateActions()
    {
        var result = new List<Action>();
        for(int i = 0; i < 5; i++)
        {
            result.Add(() => Console.WriteLine(i));
        }
        return result;
    }

    static void Main()
    {
        var actions = CreateActions();
        for(int i = 0;i<actions.Count;i++)
        {
            actions[i]();
        }
    }
}
```

这个例子也非常简单，创建一个 Action 链表并依次执行它们。看看结果

```shell
5
5
5
5
5
```

![](https://img1.dotnet9.com/2022/06/1101.png)

相信很多人看到这个结果的表情是这样的！！难道不应该是 0，1，2，3，4 吗？出了什么问题？

刨根问底，这儿的问题还是出现在闭包的本质上面，作为“闭包延迟了变量的生命周期”这个硬币的另外一面，是一个变量可能在不经意间被多个闭包所引用。

在这个例子里面，局部变量 i 同时被 5 个闭包引用，这 5 个闭包共享 i，所以最后他们打印出来的值是一样的，都是 i 最后退出循环时候的值 5。

要想解决这个问题也很简单，多声明一个局部变量，让各个闭包引用自己的局部变量就可以了。

```csharp
//其他都保持与之前一致
static List<Action> CreateActions()
{
    var result = new List<Action>();
    for (int i = 0; i < 5; i++)
    {
        int temp = i; //添加局部变量
        result.Add(() => Console.WriteLine(temp));
    }
    return result;
}
```

```shell
0
1
2
3
4
```

这样各个闭包引用不同的局部变量，刚刚的问题就解决了。

除此之外，还有一个修复的方法，在创建闭包的时候，使用`foreach`而不是`for`。至少在 C# 7.0 的版本上面，这个问题已经被注意到了，使用 foreach 的时候编译器会自动生成代码绕过这个闭包陷阱。

```csharp
//这样fix也是可以的
static List<Action> CreateActions()
{
    var result = new List<Action>();
    foreach (var i in Enumerable.Range(0,5))
    {
        result.Add(() => Console.WriteLine(i));
    }
    return result;
}
```

这就是在闭包在 C#中的使用和其使用中的一个小陷阱，希望大家能通过老胡的文章了解到这个知识点并且在开发中少走弯路！
