---
title: C# Aggregate 累加器
slug: Csharp-aggregate-accumulator
description: 累加器是什么？累加器怎么用？别急，一项新技术的诞生，基本都是为了满足某种需求，从需求出发，更容易理解这个函数的特点。
date: 2022-04-20 07:11:25
copyright: Reprint
author: 遇水寒
originaltitle: C# Aggregate 累加器
originallink: https://blog.csdn.net/weixin_42117950/article/details/121985075
draft: False
cover: https://img1.dotnet9.com/2022/04/cover_23.jpg
categories: .NET相关
tags: C#,Aggregate
---

## 1. 需求

累加器是什么？累加器怎么用？别急，一项新技术的诞生，基本都是为了满足某种需求，从需求出发，更容易理解这个函数的特点。

为方便理解，假设有一个int一维数组，储存5个数字，求出第一个值(-1)加上第二个值(0)的和(-1)，再将求得的值(-1)和数组的第三个数(3)相加，再次求和(2)，再次将该值和数组的第四个数（5）相加。。。以此类推，得出其实就是：int result = -1 + 0 + 3 + 5 + 8;

```C#
int[] numbers={-1, 0, 3, 5,8};
```

### 1.1 基本需求

```C#
//V1.0版本
static void Main(string[] args)
{
    int[] numbers = { -1, 0, 3, 5, 8 };
    int result = numbers[0];
    for (int i = 1; i < numbers.Length; i++)
    {
        result = result + numbers[i];
    }
    Console.WriteLine(result);//输出15
    Console.ReadKey();
}
```

### 1.2 封装算法部分

把中间的算法提取出来，这样可以输入其他不同长度的int类型数组了：

```C#
//V1.1 版本
static void Main(string[] args)
{
    int[] numbers = { -1, 0, 3, 5, 8 };
    var result = Aggregate(numbers);
    Console.WriteLine(result);
    Console.ReadKey();
}

static int Aggregate(int[] array)
{
    int result = array[0];
    for (int i = 1; i < array.Length; i++)
    {
        result = result + array[i];
    }
    return result;
}

```

### 1.3 不只是实现加法–算法替换

累加器要做到不是只能做加法，例如：

```C#
int result = -1 * 0 * 3 * 5 * 8;

int result = -1 - 0 - 3 - 5 - 8;
```

```C#
//V1.2 版本,实现算法替换
//需了解委托,Lambda相关知识
static void Main(string[] args)
{
    int[] numbers = { -1, 0, 3, 5, 8 };
    var result = Aggregate(numbers,(result,next)=>result * next);//实现乘法
    //var result = Aggregate(numbers,(result,next)=>result - next);//实现减法
    Console.WriteLine(result);
    Console.ReadKey();
}

static int Aggregate(int[] array,Func<int,int,int> func)
{
    int result = array[0];
    for (int i = 1; i < array.Length; i++)
    {
        result = func(result, array[i]);
    }
    return result;
}

```

### 1.4 不只是int类型–泛型

目前该算法`int Aggregate(int[] array,Func<int,int,int> func)`只支持int类型，现在要扩展到任意类型–泛型。

```C#
//V1.3 版本，实现泛型
//需了解泛型，IEnumerator接口相关知识
static void Main(string[] args)
{
    int[] numbers = { -1, 0, 3, 5, 8 };
    var result = Aggregate(numbers, (result, next) => result + next);
    Console.WriteLine(result);
    Console.ReadKey();
}

static TSource Aggregate<TSource>(IEnumerable<TSource> source,Func<TSource, TSource, TSource> func)
{
    using (IEnumerator<TSource> e = source.GetEnumerator())
    {
        if (!e.MoveNext())
        {
            throw new ArgumentException();
        }

        TSource result = e.Current;
        while (e.MoveNext())
        {
            result = func(result, e.Current);
        }

        return result;
    }
}
```

### 1.5 实现方式变成拓展方法

直接对实现了IEnumerator接口的类，添加扩展方法。至此，该Aggregate实现的功能已经和官网提供的累加器相似了。

```C#
//V1.4 版本，扩展方法
class Program
{
    static void Main(string[] args)
    {
        int[] numbers = { -1, 0, 3, 5, 8 };
        var result = numbers.Aggregate((result, next) => result + next);
        Console.WriteLine(result);
        Console.ReadKey();
    }        
}

public static class Helper
{
    public static TSource Aggregate<TSource>(this IEnumerable<TSource> source, Func<TSource, TSource, TSource> func)
    {
        using (IEnumerator<TSource> e = source.GetEnumerator())
        {
            if (!e.MoveNext())
            {
                throw new ArgumentException("需要至少两个元素");
            }

            TSource result = e.Current;
            while (e.MoveNext())
            {
                result = func(result, e.Current);
            }

            return result;
        }
    }
}
```

## 2. 微软提供的API

在System.Linq命名空间下，提供了：

```C#
1. public static TSource Aggregate<TSource>(this IEnumerable<TSource> source, Func<TSource, TSource, TSource> func);

2. public static TAccumulate Aggregate<TSource, TAccumulate>(this IEnumerable<TSource> source, TAccumulate seed, Func<TAccumulate, TSource, TAccumulate> func);

3. public static TResult Aggregate<TSource, TAccumulate, TResult>(this IEnumerable<TSource> source, TAccumulate seed, Func<TAccumulate, TSource, TAccumulate> func, Func<TAccumulate, TResult> resultSelector);
```

其中第一个接口，与我上述举例的功能一样。其他两个功能放后面讲解（有时间的话）。

## 3.由浅入深

如果是初学者，可能大脑第一反应还是觉得这个`Aggregate`只能加减乘除，如果这样想，说明其实还是理解得不够深刻，现在来深度剖析该方法的传参：`func`。

如下图所示，累加器会遍历每一个元素，除了第一次，是直接拿第一和第二个元素进行func运算之外，从第三个开始，都是拿上一次的计算结果result作为func的第一个输入参数，第二个输入参数为数组的下一个元素，直到遍历到最后，返回一个最终的result值。

实际使用：当数据库中一张表引用了多张表的外键，当需要查询这些外键的时候，就可以使用累加器，把需要查询的条件累加起来，向数据库进行查询。

![](https://img1.dotnet9.com/2022/04/2301.png)