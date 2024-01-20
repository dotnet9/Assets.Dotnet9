---
title: 【C#】CsvHelper 使用手册
slug: Csharp-CsvHelper-User-Manual
description: CsvHelper 是一个用于读写 CSV 文件的.NET库。极其快速，灵活且易于使用。
date: 2024-01-19 22:15:11
lastmod: 2024-01-19 22:26:21
copyright: Reprinted
banner: false
author: 丹枫无迹
originaltitle: 【C#】CsvHelper 使用手册
originallink: https://www.cnblogs.com/gl1573/p/12922857.html
draft: false
cover: https://img1.dotnet9.com/2024/01/cover_08.png
categories: .NET相关
tags: CSV
---

> **本文代码基于 CsvHelper 15.0.5**

## 简介

CsvHelper 是一个用于读写 CSV 文件的.NET库。极其快速，灵活且易于使用。

CsvHelper 建立在.NET Standard 2.0 之上，几乎可以在任何地方运行。

Github 地址：https://github.com/joshclose/csvhelper

### 模块

| 模块                               | 功能                                  |
| ---------------------------------- | ------------------------------------- |
| CsvHelper                          | 读写 CSV 数据的核心类。               |
| CsvHelper.Configuration            | 配置 CsvHelper 读写行为的类。         |
| CsvHelper.Configuration.Attributes | 配置 CsvHelper 的特性。               |
| CsvHelper.Expressions              | 生成 LINQ 表达式的类。                |
| CsvHelper.TypeConversion           | 将 CSV 字段与 .NET 类型相互转换的类。 |

## 读取

**测试类**

```csharp
public class Foo
{
    public int ID { get; set; }

    public string Name { get; set; }
}
```

**csv 文件数据**

```mipsasm
ID,Name
1,Tom
2,Jerry
```

### 读取所有记录

```csharp
using (var reader = new StreamReader("foo.csv"))
{
    using (var csv = new CsvReader(reader, CultureInfo.InvariantCulture))
    {
        var records = csv.GetRecords<Foo>();
    }
}
```

> 读取 csv 文件时，空行将被忽略，若空行中包含空格，将报错。
> 如果是 Excel 编辑的 CSV 文件，空行将会变成仅包含分隔符 `,` 的行，也会报错。

### 逐条读取

```csharp
using (var reader = new StreamReader("foo.csv"))
{
    using (var csv = new CsvReader(reader, CultureInfo.InvariantCulture))
    {
        while (csv.Read())
        {
            var record = csv.GetRecord<Foo>();
        }
    }
}
```

> `GetRecords<T>` 方法通过 `yield` 返回一个 `IEnumerable<T>`，并不会将内容一次全部读进内存，除非调用了 `ToList` 或 `ToArray` 方法。所以这种逐条读取的写法没有太多必要。

### 读取单个字段

```csharp
using (var csv = new CsvReader(reader, CultureInfo.InvariantCulture))
{
    csv.Read();
    csv.ReadHeader();

    while (csv.Read())
    {
        var id = csv.GetField<int>(0);
        var name = csv.GetField<string>("Name");
    }
}
```

逐行读取时，可以不管标题行，但是，这里不行。

`csv.Read();` 这句是读取标题，如果没有的话，`while` 循环第一次取到的是标题，肯定会报错。

`csv.ReadHeader();` 这句是给标题赋值，如果没有的话，`csv.GetField<string>("Name")` 会报找不到标题。

使用 `TryGetField` 可以防止意外的报错。

```csharp
csv.TryGetField(0, out int id);
```

## 写入

### 写入所有记录

```csharp
var records = new List<Foo>
{
    new Foo { ID = 1, Name = "Tom" },
    new Foo { ID = 2, Name = "Jerry" },
};

using (var writer = new StreamWriter("foo.csv"))
{
    using (var csv = new CsvWriter(writer, CultureInfo.InvariantCulture))
    {
        csv.WriteRecords(records);
    }
}
```

### 逐条写入

```csharp
using (var writer = new StreamWriter("foo.csv"))
{
    using (var csv = new CsvWriter(writer, CultureInfo.InvariantCulture))
    {
        foreach (var record in records)
        {
            csv.WriteRecord(record);
        }
    }
}
```

### 逐字段写入

```csharp
using (var writer = new StreamWriter("foo.csv"))
{
    using (var csv = new CsvWriter(writer, CultureInfo.InvariantCulture))
    {
        csv.WriteHeader<Foo>();
        csv.NextRecord();

        foreach (var record in records)
        {
            csv.WriteField(record.ID);
            csv.WriteField(record.Name);
            csv.NextRecord();
        }
    }
}
```

## 特性

### Index

`Index` 特性用于标记字段顺序。

在读取文件时，如果没有标题，就只能通过顺序来确定字段。

```csharp
public class Foo
{
    [Index(0)]
    public int ID { get; set; }

    [Index(1)]
    public string Name { get; set; }
}
    
using (var reader = new StreamReader("foo.csv"))
{
    using (var csv = new CsvReader(reader, CultureInfo.InvariantCulture))
    {
        csv.Configuration.HasHeaderRecord = false;

        var records = csv.GetRecords<Foo>().ToList();
    }
}
```

> `csv.Configuration.HasHeaderRecord = false` 配置告知 `CsvReader` 没有标题。必须要加这一行，否则会默认第一行为标题而跳过，导致最后的结果中少了一行。如果数据量比较多，会很难发现这个 bug。

在写入文件的时候，会按 `Index` 顺序写入。如果不想写入标题，也需要添加 `csv.Configuration.HasHeaderRecord = false;`

### Name

如果字段名称和列名不一致，可以使用 `Name` 属性。

```csharp
public class Foo
{
    [Name("id")]
    public int ID { get; set; }

    [Name("name")]
    public string Name { get; set; }
}
```

### NameIndex

`NameIndex` 用于处理 CSV 文件中的同名列。

```csharp
public class Foo
{
    ...

    [Name("Name")]
    [NameIndex(0)]
    public string FirstName { get; set; }

    [Name("Name")]
    [NameIndex(1)]
    public string LastName { get; set; }
}
```

### Ignore

忽略字段

### Optional

读取时如果找不到匹配的字段，则忽略。

```csharp
public class Foo
{
    ...

    [Optional]
    public string Remarks { get; set; }
}
```

### Default

当读取的字段为空时 `Default` 特性可为其指定默认值。

`Default` 特性仅在读取时有效，写入时是不会将空值替换为默认值写入的。

### NullValues

```csharp
public class Foo
{
    ...

    [NullValues("None", "none", "Null", "null")]
    public string None { get; set; }
}
```

读取文件时，若 CSV 文件中某字段的值为空，那么读取后的值是 `""`，而非 `null`，标记 `NullValues` 特性后，若 CSV 文件中的某字段值为 `NullValues` 指定的值，则读取后为 `null`。

若同时标记了 `Default` 特性，则此特性不起作用。

> 坑爹的是，在写入文件时，此特性并不起作用。因此会引起读写不一致的问题。

### Constant

`Constant` 特性为字段指定一个常量值，读写时都使用此值，无论指定了什么其他映射或配置。

### Format

`Format` 指定类型转换时使用的字符串格式。

例如数字和时间类型，我们经常会指定其格式。

```csharp
public class Foo
{
    ...

    [Format("0.00")]
    public decimal Amount { get; set; }

    [Format("yyyy-MM-dd HH:mm:ss")]
    public DateTime JoinTime { get; set; }
}
```

### BooleanTrueValues 和 BooleanFalseValues

这两个特性用于将 bool 转换成指定的形式显示。

```csharp
public class Foo
{
    ...

    [BooleanTrueValues("yes")]
    [BooleanFalseValues("no")]
    public bool Vip { get; set; }
}
```

### NumberStyles

```csharp
public class Foo
{
    ...

    [Format("X2")]
    [NumberStyles(NumberStyles.HexNumber)]
    public int Data { get; set; }
}
```

比较有用是 `NumberStyles.HexNumber` 和 `NumberStyles.AllowHexSpecifier`，这两个枚举的作用差不多。此特性仅在读取时有效，写入时并不会转成 16 进制写入。这会导致读写不一致，可以用 `Format` 特性指定写入格式。

## 映射

如果无法给要映射的类添加特性，在这种情况下，可以使用 `ClassMap` 方式进行映射。

使用映射和使用特性效果是一样的，坑爹的地方也一样坑爹。以下示例用属性实现了上面特性的功能。

```csharp
public class Foo2
{
    public int ID { get; set; }

    public string Name { get; set; }

    public decimal Amount { get; set; }

    public DateTime JoinTime { get; set; }

    public string Msg { get; set; }

    public string Msg2 { get; set; }

    public bool Vip { get; set; }

    public string Remarks { get; set; }

    public string None { get; set; }

    public int Data { get; set; }
}

public class Foo2Map : ClassMap<Foo2>
{
    public Foo2Map()
    {
        Map(m => m.ID).Index(0).Name("id");
        Map(m => m.Name).Index(1).Name("name");
        Map(m => m.Amount).TypeConverterOption.Format("0.00");
        Map(m => m.JoinTime).TypeConverterOption.Format("yyyy-MM-dd HH:mm:ss");
        Map(m => m.Msg).Default("Hello");
        Map(m => m.Msg2).Ignore();
        Map(m => m.Vip)
            .TypeConverterOption.BooleanValues(true, true, new string[] { "yes" })
            .TypeConverterOption.BooleanValues(false, true, new string[] { "no" });
        Map(m => m.Remarks).Optional();
        Map(m => m.None).TypeConverterOption.NullValues("None", "none", "Null", "null");
        Map(m => m.Data)
            .TypeConverterOption.NumberStyles(NumberStyles.HexNumber)
            .TypeConverterOption.Format("X2");
    }
}
```

在使用映射前，需要先注册

```csharp
csv.Configuration.RegisterClassMap<Foo2Map>();
```

### ConvertUsing

`ConvertUsing` 允许使用一个委托方法实现类型转换。

```csharp
// 常数
Map(m => m.Constant).ConvertUsing(row => 3);

// 把两列聚合在一起
Map(m => m.Name).ConvertUsing(row => $"{row.GetField<string>("FirstName")} {row.GetField<string>("LastName")}");

Map(m => m.Names).ConvertUsing(row => new List<string> { row.GetField<string>("Name") } );
```

## 配置

### Delimiter

分隔符

```csharp
csv.Configuration.Delimiter = ",";
```

### HasHeaderRecord

此配置前文已经提到过，是否将第一行作为标题

```csharp
csv.Configuration.HasHeaderRecord = false;
```

### IgnoreBlankLines

是否忽略空行，默认 `true`

```csharp
csv.Configuration.IgnoreBlankLines = false;
```

无法忽略一个仅包含空格或 `,` 的行。

### AllowComments

是否允许注释，注释以 `#` 开头。

```csharp
csv.Configuration.AllowComments = true;
```

### Comment

获取或设置用于表示注释掉的行的字符。默认是 `#`。

```csharp
csv.Configuration.Comment = '/';
```

### BadDataFound

设置一个函数，该函数会在数据不正确时触发，可用于记录日志。

### IgnoreQuotes

获取或设置一个值，该值指示在解析时是否应忽略引号并将其与其他任何字符一样对待。

默认是 `false`，如果字符串中有引号，必须是 3 个 `"` 连在一起，读取到的字符串中才会有一个 `"`，如果是 1 个则忽略，2 个则报错。

如果为 `true`，则会将 `"` 当做字符串原样返回。

```csharp
csv.Configuration.IgnoreQuotes = true;
```

`CsvWriter` 中是没有这个属性的，一旦字符串中包含 `"`，写出来就是 3 个 `"` 连在一起。

### TrimOptions

去除字段首尾空格

```csharp
csv.Configuration.TrimOptions = TrimOptions.Trim;
```

### PrepareHeaderForMatch

`PrepareHeaderForMatch` 定义了属性名称与标题进行匹配的函数。标题和属性名称均通过该函数运行。此功能可用于删除标题中的空格，或者当标题和属性名称大小写不一致时统一大小写后比较。

```csharp
csv.Configuration.PrepareHeaderForMatch = (string header, int index) => header.ToLower();
```
