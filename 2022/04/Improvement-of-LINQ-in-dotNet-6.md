---
title: .NET 6 中 LINQ 的改进
slug: Improvement-of-LINQ-in-dotNet-6
description: 如题
date: 2022-04-23 14:29:14
copyright: Reprint
author: 一个小伙子
originaltitle: .NET 6 中 LINQ 的改进
originallink: https://www.cnblogs.com/paopaotang/p/16133578.html
draft: False
cover: https://img1.dotnet9.com/2022/04/cover_31.jpeg
categories: .NET相关
tags: C#,.NET,LINQ
---

## 1. OrDefault 方法的默认值

`Enumerable.FirstOrDefault` 方法返回一个序列的第一个元素，如果没有找到，则返回一个默认值。在 .NET 6 中，你可以覆盖该方法的默认值。同样，你还可以覆盖 `SingleOrDefault` 和 `LastOrDefault` 方法的默认值。

```C#
List<int> list1 = new() { 1, 2, 3 };
int item1 = list1.FirstOrDefault(i => i == 4, -1);
Console.WriteLine(item1); // -1

List<string> list2 = new() { "Item1" };
string item2 = list2.SingleOrDefault(i => i == "Item2", "Not found");
Console.WriteLine(item2); // Not found
```

## 2.新的 By 方法

- MinBy
- MaxBy
- DistinctBy
- ExceptBy
- IntersectBy
- UnionBy

```C#
List<Product> products = new()
{
    new() { Name = "Product1", Price = 100 },
    new() { Name = "Product2", Price = 5 },
    new() { Name = "Product3", Price = 50 },
};

Product theCheapestProduct = products.MinBy(x => x.Price);
Product theMostExpensiveProduct = products.MaxBy(x => x.Price);
Console.WriteLine(theCheapestProduct);
// Output: Product { Name = Product2, Price = 5 }
Console.WriteLine(theMostExpensiveProduct);
// Output: Product { Name = Product1, Price = 100 }

record Product
{
    public string Name { get; set; }
    public decimal Price { get; set; }
}
```
 
## 3. 三向 Zip 方法

`Enumerable.Zip` 扩展方法可以将两个序列进行结合产生产生一个二元组序列。在 .NET 6 中，它可以结合三个序列产生一个三元组序列。

```C#
int[] numbers = { 1, 2, 3, 4, };
string[] months = { "Jan", "Feb", "Mar" };
string[] seasons = { "Winter", "Winter", "Spring" };

var test = numbers.Zip(months).Zip(seasons);

foreach ((int, string, string) zipped in numbers.Zip(months, seasons))
{
    Console.WriteLine($"{zipped.Item1} {zipped.Item2} {zipped.Item3}");
}
// Output:
// 1 Jan Winter
// 2 Feb Winter
// 3 Mar Spring
```

 ## 4. Take 方法支持 Range

.NET Core 3.0 中也引入了 `Range` 结构体，它被 C# 编译器用来支持一个范围操作符 `...`。在 .NET 6 中，`Enumerable.Take` 方法也支持 `Range`。

```C#
IEnumerable<int> numbers = new int[] { 1, 2, 3, 4, 5 };

IEnumerable<int> taken1 = numbers.Take(2..4);
foreach (int i in taken1)
    Console.WriteLine(i);
// Output:
// 3
// 4

IEnumerable<int> taken2 = numbers.Take(..3);
foreach (int i in taken2)
    Console.WriteLine(i);
// Output:
// 1
// 2
// 3

IEnumerable<int> taken3 = numbers.Take(3..);
foreach (int i in taken3)
    Console.WriteLine(i);
// Output:
// 4
// 5
```