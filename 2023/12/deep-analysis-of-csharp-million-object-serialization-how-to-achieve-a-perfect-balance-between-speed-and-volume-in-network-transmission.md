---
title: C#百万对象序列化深度剖析：如何在网络传输中实现速度与体积的完美平衡
slug: deep-analysis-of-csharp-million-object-serialization-how-to-achieve-a-perfect-balance-between-speed-and-volume-in-network-transmission
description: 在网络通信中，数据序列化是将对象状态转换为可存储或可传输的形式的过程，这对于TCP网络传输尤为关键。在项目中，当需要处理几十万条数据的传输时，传统的JSON序列化方式由于其冗余的字段名和字符串格式，导致了二进制包体积庞大，且序列化与反序列化的效率低下。为了解决这些问题，我们考虑采用更加高效的序列化方法，以减少包大小并提升处理速度。
date: 2023-12-09 12:15:26
lastmod: 2023-12-11 22:29:29
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2023/11/cover_03.png
categories: 
    - .NET
tags: 
    - 优化
---

**目录**

1. 本文背景
2. 构建测试数据
3. 方案对比
   - Json序列化
   - 自定义二进制序列化
   - BinaryWriter\BinaryReader
   - ProtoBuf
   - MessagePack
   - 方案分析
   - 基准测试
4. 总结

## 1. 本文背景

大家好，我是沙漠尽头的狼。

在网络通信中，数据序列化是将对象状态转换为可存储或可传输的形式的过程，这对于TCP网络传输尤为关键。在项目中，当需要处理几十万条数据的传输时，传统的`Json序列化`方式由于其冗余的字段名和字符串格式，导致了二进制包体积庞大，且序列化与反序列化的效率低下。为了解决这些问题，我考虑采用更加高效的序列化方法，以减少包大小并提升处理速度。本文将探讨自定义二进制序列化、`BinaryWriter/BinaryReader`、[MessagePack](https://github.com/msgpack/msgpack)和[ProtoBuf](https://github.com/protobuf-net/protobuf-net)等4种序列化方法，并通过比较它们的性能，为大家提供我目前认为的最佳实践指南。

## 2. 构建测试数据

创建C#控制台程序，添加`Organization`和`Member`两个类，类中包含基本的数据类型和`List<T>`，其他数组、字典可以自行扩展：

```csharp
public class Organization
{
    public int Id { get; set; }

    public string[]? Tags { get; set; }

    public List<Member>? Members { get; set; }
}

public class Member
{
    public int Id { get; set; }

    public string? Name { get; set; }

    public string? Description { get; set; }

    public string? Address { get; set; }

    public double Value { get; set; }

    public long UpdateTime { get; set; }
}
```

创建测试数据100万条，为了和标题遥相呼应：

```csharp
public class BenchmarkTest
{
    /// <summary>
    /// 测试数据量
    /// </summary>
    private const int DataCount = 1000000;

    private static readonly Random RandomShared = new(DateTime.Now.Millisecond);

    /// <summary>
    /// 测试数据
    /// </summary>
    private static readonly Organization TestData = new()
    {
        Id = 1,
        Tags = Enumerable.Range(0, 5).Select(index => $"测试标签{index}").ToArray(),
        Members = Enumerable.Range(0, DataCount).Select(index => new Member()
        {
            Id = index,
            Name = $"测试名字{index}",
            Description = $"测试描述{RandomShared.Next(1, int.MaxValue)}",
            Address = $"测试地址{RandomShared.Next(1, int.MaxValue)}",
            Value = RandomShared.Next(1, int.MaxValue),
            UpdateTime = ((DateTimeOffset)DateTime.Now).ToUnixTimeMilliseconds()
        }).ToList()
    };
}
```

## 3. 方案对比

首先创建序列化接口`ISerializeHelper`，各种序列化提供程序需要分别实现：

```csharp
public interface ISerializeHelper
{
    byte[] Serialize(Organization data);

    Organization? Deserialize(byte[] buffer);
}
```

再创建`BenchmarkTest`类，添加`RunSerialize`方法用于执行序列化提供程序，在此方法中依次调用提供程序的序列化和反序列方法，统一对测试方法进行耗时和组包大小打印输出统计：

```csharp
public class BenchmarkTest
{
    // ...省略前面的代码
    private static void RunSerialize(ISerializeHelper helper)
    {
        Stopwatch sw = Stopwatch.StartNew();

        var buffer = helper.Serialize(TestData);

        sw.Stop();
        Log($"{helper.GetType().Name} Serialize {sw.ElapsedMilliseconds}ms {buffer.Length}byte");

        sw.Restart();

        var data = helper.Deserialize(buffer);

        sw.Stop();

        Log($"{helper.GetType().Name} Deserialize {sw.ElapsedMilliseconds}ms {data?.Members?.Count}项");
    }

    private static void Log(string log)
    {
        Console.WriteLine($"{DateTime.Now:yyyy-MM-dd HH:mm:ss fff}: {log}");
    }
}
```

### 3.1. Json序列化

首先测试Json序列化，安装`System.Text.Json`包：

```xml
<PackageReference Include="System.Text.Json" Version="8.0.0" />
```

创建`JsonSerializeHelper`提供程序，并实现`ISerializeHelper`接口：

```csharp
using ByteTest.Core.Models;
using System.Text.Json;

namespace ByteTest.Core.Helpers;

public class JsonSerializeHelper : ISerializeHelper
{
    public byte[] Serialize(Organization data)
    {
        return JsonSerializer.SerializeToUtf8Bytes(data);
    }

    public Organization? Deserialize(byte[] buffer)
    {
        var data = JsonSerializer.Deserialize<Organization>(buffer);
        return data;
    }
}
```

`JsonSerializer.SerializeToUtf8Bytes` 方法会直接生成字节数据，而不是先生成字符串然后再转换为字节，这对于一些需要将 JSON 数据写入文件或网络流的场景非常有用，因为这些场景通常需要字节数据而不是字符串。此外，由于避免了不必要的字符串分配，它还可以提高性能并减少内存压力。

再在`BenchmarkTest`类添加测试方法`Test`：

```csharp
public static void Test()
{
    RunSerialize(new JsonByteSerializeHelper());
}
```

在Program中调用`Test()`方法：

```csharp
BenchmarkTest.Test();
```

程序输出如下：

```csharp
2023-12-10 22:28:24 880: JsonByteSerializeHelper Serialize 2813ms 196227181byte
2023-12-10 22:28:26 858: JsonByteSerializeHelper Deserialize 1964ms 1000000项
```

`Json`序列化100万条数据，需要`2.8`s，数据包`187.14`MB，真大、真慢。

### 3.2. 自定义二进制序列化

接下来测试下自定义的二进制序列化，这是我原来常用的方式，目前看也是啰嗦，首先定义数据包字段规范：

| 数据类型                                              | 二进制长度    | 说明                                                         |
| ----------------------------------------------------- | ------------- | ------------------------------------------------------------ |
| 数字类型（short\ushort\int\uint\long\ulong\double等） | 2\2\4\4\8\8\8 | 基本的数字类型是定长的                                       |
| string                                                | 4+n           | 用int类型4个字节表示字符串二进制后的长度，n表示字符串二进制数组实际长度 |
| `T[]`\`List<T>`                                       | 4+n           | 数组或列表和字符串类似，用int类型4个字节表示数组或列表二进制后的长度，n表示数组或列表二进制数组实际长度 |

添加`CustomSerializeHelper`实现接口`ISerializeHelper`：

```csharp
using ByteTest.Core.Models;

namespace ByteTest.Core.Helpers;

public class CustomSerializeHelper : ISerializeHelper
{
    public byte[] Serialize(Organization data)
    {
        // 1、计算Id
        var idBuffer = BitConverter.GetBytes(data.Id);

        // 2、计算Tag数组
        var tagBuffer = GetBytes(data.Tags);

        // 3、计算Members
        var membersBuffer = GetBytes(data.Members);

        return GetBytes(new[] { idBuffer, tagBuffer, membersBuffer });
    }

    public Organization? Deserialize(byte[] buffer)
    {
        var data = new Organization();
        var index = 0;

        data.Id = BitConverter.ToInt32(buffer, index);
        index += sizeof(int);

        data.Tags = GetTags(buffer, ref index);
        data.Members = GetMembers(buffer, ref index);
        return data;
    }

    /// <summary>
    /// 获取字符串列表byte[]
    /// </summary>
    /// <param name="data"></param>
    /// <returns></returns>
    private byte[] GetBytes(string[]? data)
    {
        var dataCount = data?.Length ?? 0;
        var dataCountBuffer = BitConverter.GetBytes(dataCount);

        if (dataCount <= 0)
        {
            return dataCountBuffer;
        }

        var dataValueBuffers = data!.Select(item => ByteHelper.GetBytes(item)!).ToArray();
        var dataValueBuffer = GetBytes(dataValueBuffers);
        return GetBytes(new[] { dataCountBuffer, dataValueBuffer });
    }

    /// <summary>
    /// 获取成功列表byte[]
    /// </summary>
    /// <param name="data"></param>
    /// <returns></returns>
    private byte[] GetBytes(List<Member>? data)
    {
        var dataCount = data?.Count ?? 0;
        var dataCountBuffer = BitConverter.GetBytes(dataCount);

        if (dataCount <= 0)
        {
            return dataCountBuffer;
        }

        var dataValueBuffers = data!.Select(item =>
        {
            var idBuffer = BitConverter.GetBytes(item.Id);
            var nameBuffer = ByteHelper.GetBytes(item.Name);
            var descriptionBuffer = ByteHelper.GetBytes(item.Description);
            var addressBuffer = ByteHelper.GetBytes(item.Address);
            var valueBuffer = BitConverter.GetBytes(item.Value);
            var updateTimeBuffer = BitConverter.GetBytes(item.UpdateTime);

            var buffer = GetBytes(new byte[][]
                { idBuffer, nameBuffer, descriptionBuffer, addressBuffer, valueBuffer, updateTimeBuffer });
            return buffer;
        }).ToArray();
        var dataValueBuffer = GetBytes(dataValueBuffers);
        return GetBytes(new[] { dataCountBuffer, dataValueBuffer });
    }

    private byte[] GetBytes(byte[][] data)
    {
        var dataBufferLen = data.Sum(itemBuffer => itemBuffer.Length);
        var dataBuffer = new byte[dataBufferLen];
        var dataIndex = 0;
        for (var i = 0; i < data.Length; i++)
        {
            var itemBuffer = data[i];
            Array.Copy(itemBuffer, 0, dataBuffer, dataIndex, itemBuffer.Length);

            dataIndex += itemBuffer.Length;
        }

        return dataBuffer;
    }

    private string[]? GetTags(byte[] buffer, ref int index)
    {
        var count = BitConverter.ToInt32(buffer, index);
        index += sizeof(int);

        if (count <= 0)
        {
            return default;
        }

        var data = new string[count];
        for (var i = 0; i < count; i++)
        {
            data[i] = GetString(buffer, ref index);
        }

        return data;
    }

    private List<Member>? GetMembers(byte[] buffer, ref int index)
    {
        var count = BitConverter.ToInt32(buffer, index);
        index += sizeof(int);

        if (count <= 0)
        {
            return default;
        }

        var data = new List<Member>();
        for (var i = 0; i < count; i++)
        {
            var people = new Member();

            people.Id = BitConverter.ToInt32(buffer, index);
            index += sizeof(int);

            people.Name = GetString(buffer, ref index);

            people.Description = GetString(buffer, ref index);

            people.Address = GetString(buffer, ref index);

            people.Value = BitConverter.ToDouble(buffer, index);
            index += sizeof(double);

            people.UpdateTime = BitConverter.ToInt64(buffer, index);
            index += sizeof(long);

            data.Add(people);
        }

        return data;
    }

    private string GetString(byte[] buffer, ref int index)
    {
        var count = BitConverter.ToInt32(buffer, index);
        index += sizeof(int);

        if (count <= 0)
        {
            return string.Empty;
        }

        var data = ByteHelper.DefaultEncoding.GetString(buffer, index, count);
        index += count;
        return data;
    }
}
```

代码不少，看个大概就好，主要用`BitConverter.GetBytes`和`BitConverter.ToXXX`获取或设置基本数据类型的byte[]数据，对于多byte[]的复制，如果数据较小，用`Array.Copy`，如果数据较大建议用`Buffer.BlockCopy`。

代码中使用到辅助类`ByteHelper`，用于计算字符串byte[]：

```csharp
public static class ByteHelper
{
    public static Encoding DefaultEncoding = Encoding.UTF8;

    /// <summary>
    /// 获取字符串二进制数据：字符串二进制数据=4个字节int表示字符串实际值长度+n个字节表示字符串实际值
    /// </summary>
    /// <param name="str"></param>
    /// <returns></returns>
    public static byte[] GetBytes(string? str)
    {
        if (string.IsNullOrEmpty(str))
        {
            return BitConverter.GetBytes(0);
        }

        var strValueBuffer = DefaultEncoding.GetBytes(str);
        var strValueLen = strValueBuffer.Length;
        var strValueLenBuffer = BitConverter.GetBytes(strValueLen);

        var strBufferLen = sizeof(int) + strValueLen;
        var strBuffer = new byte[strBufferLen];

        var index = 0;
        Array.Copy(strValueLenBuffer, 0, strBuffer, index, sizeof(int));
        index += sizeof(int);

        Array.Copy(strValueBuffer, 0, strBuffer, index, strValueLen);

        return strBuffer;
    }
}
```

我们修改Test方法，加入`CustomSerializeHelper`测试：

```csharp
public static void Test()
{
    var serializeHelpers = new List<ISerializeHelper>
    {
        new JsonSerializeHelper(),
        new CustomSerializeHelper()
    };

    serializeHelpers.ForEach(RunSerialize);
}
```

程序输出如下：

```csharp
2023-12-10 22:45:14 701: JsonSerializeHelper Serialize 2774ms 196225588byte
2023-12-10 22:45:16 613: JsonSerializeHelper Deserialize 1898ms 1000000项
2023-12-10 22:45:17 414: CustomSerializeHelper Serialize 801ms 92854209byte
2023-12-10 22:45:18 072: CustomSerializeHelper Deserialize 657ms 1000000项
```

能看出优化了不少，自定义二进制序列化减少1亿个字节（100MB左右），序列化（组包）快2s左右，反序列化（解包）快1s多。

### 3.3. BinaryWriter\BinaryReader

`BinaryWriter` 和 `BinaryReader` 类是用于以二进制格式写入和读取数据的类。它们分别提供了一系列的方法来写入和读取各种基本数据类型（如int, float, double, string等）的二进制表示。这些类通常与文件流（`FileStream`）一起使用，但也可以与其他类型的流（如`MemoryStream`）配合使用。

自定义的方式全手工操作，需要自己进行字节数组的复制，各种转换，有点原始，使用`BinaryWriter\BinaryReader`进行序列化操作应该二进制序列化的标准用法了。

创建`BinarySerializeHelper`类，并实现`ISerializeHelper`接口：

```csharp
public class BinarySerializeHelper : ISerializeHelper
{
    public byte[] Serialize(Organization data)
    {
        using var stream = new MemoryStream();
        using var writer = new BinaryWriter(stream, ByteHelper.DefaultEncoding);
        writer.Write(data.Id);
        Write(writer, data.Tags);
        Write(writer, data.Members);

        return stream.ToArray();
    }

    public Organization Deserialize(byte[] buffer)
    {
        var data = new Organization();
        using var stream = new MemoryStream(buffer);
        using var reader = new BinaryReader(stream, ByteHelper.DefaultEncoding);
        data.Id = reader.ReadInt32();
        data.Tags = ReadStringList(reader);
        data.Members = ReadPeopleList(reader);

        return data;
    }

    private static void Write(BinaryWriter writer, string[]? data)
    {
        var count = data?.Length ?? 0;
        writer.Write(count);
        if (count <= 0)
        {
            return;
        }

        foreach (var item in data!)
        {
            writer.Write(item);
        }
    }

    private static void Write(BinaryWriter writer, List<Member>? data)
    {
        var count = data?.Count ?? 0;
        writer.Write(count);
        if (count > 0)
        {
            foreach (var item in data)
            {
                writer.Write(item.Id);
                writer.Write(item.Name ?? string.Empty);
                writer.Write(item.Description ?? string.Empty);
                writer.Write(item.Address ?? string.Empty);
                writer.Write(item.Value);
                writer.Write(item.UpdateTime);
            }
        }
    }

    private static string[]? ReadStringList(BinaryReader reader)
    {
        var count = reader.ReadInt32();
        if (count <= 0)
        {
            return default;
        }

        var values = new string[count];
        for (int i = 0; i < count; i++)
        {
            values[i] = reader.ReadString();
        }

        return values;
    }

    private static List<Member>? ReadPeopleList(BinaryReader reader)
    {
        var count = reader.ReadInt32();
        if (count <= 0)
        {
            return default;
        }

        var values = new List<Member>();
        for (int i = 0; i < count; i++)
        {
            var item = new Member();
            item.Id = reader.ReadInt32();
            item.Name = reader.ReadString();
            item.Description = reader.ReadString();
            item.Address = reader.ReadString();
            item.Value = reader.ReadDouble();
            item.UpdateTime = reader.ReadInt64();

            values.Add(item);
        }

        return values;
    }
}
```

代码也不少，列表的序列化和反序列在上面自定义二进制序列化时就应该封装成方法，通过反射实现通用列表的序列化和反序列化，这一小节也是，不想再折腾了，我们在`BenchmarkTest`类的Test方法内加上`BinarySerializeHelper`，再运行程序：

```csharp
2023-12-10 22:52:56 986: JsonSerializeHelper Serialize 2715ms 196225584byte
2023-12-10 22:52:58 910: JsonSerializeHelper Deserialize 1910ms 1000000项
2023-12-10 22:52:59 730: CustomSerializeHelper Serialize 819ms 92853722byte
2023-12-10 22:53:00 389: CustomSerializeHelper Deserialize 659ms 1000000项
2023-12-10 22:53:00 660: BinarySerializeHelper Serialize 269ms 83853707byte
2023-12-10 22:53:01 466: BinarySerializeHelper Deserialize 806ms 1000000项
```

`BinaryWriter\BinaryReader`序列化包又比自定义二进制序列化方式小8.5MB左右，序列化速度也快0.5s，反序列化稍微慢点，不错哟。

### 3.4. ProtoBuf

大家听过或使用过Google 的 Protocol Buffers吧？

本小节介绍使用`protobuf-net`库，这是一个在 .NET 环境中使用的库，它提供了对 Google 的 Protocol Buffers 数据序列化格式的支持。Protocol Buffers 是一种轻量级、高效的结构化数据序列化机制，通常用于跨服务或应用程序的通信，以及数据存储。

安装包`protobuf-net`：

```xml
<PackageReference Include="protobuf-net" Version="3.2.30" />
```

给测试的类添加类序列化特性`[ProtoContract]`和属性序列化特性`[ProtoMember(序列化顺序)]`：

```csharp
[ProtoContract]
public class Organization
{
    [ProtoMember(1)] public int Id { get; set; }

    [ProtoMember(2)] public string[]? Tags { get; set; }

    [ProtoMember(3)] public List<Member>? Members { get; set; }
}

[ProtoContract]
public class Member
{
    [ProtoMember(1)] public int Id { get; set; }

    [ProtoMember(2)] public string? Name { get; set; }

    [ProtoMember(3)] public string? Description { get; set; }

    [ProtoMember(4)] public string? Address { get; set; }

    [ProtoMember(5)] public double Value { get; set; }

    [ProtoMember(6)] public long UpdateTime { get; set; }
}
```

添加`ProtoBufSerializeHelper`类，并实现`ISerializeHelper`接口：

```csharp
using ByteTest.Core.Models;
using ProtoBuf;

namespace ByteTest.Core.Helpers;

public class ProtoBufSerializeHelper : ISerializeHelper
{
    public byte[] Serialize(Organization data)
    {
        using var stream = new MemoryStream();
        Serializer.Serialize(stream, data);
        return stream.ToArray();
    }

    public Organization? Deserialize(byte[] buffer)
    {
        using var stream = new MemoryStream(buffer);
        return Serializer.Deserialize<Organization>(stream);
    }
}
```

还有一步，给上面的`Test`方法加上`ProtoBufSerializeHelper`序列化方式，程序输出如下：

```csharp
2023-12-10 23:01:17 478: JsonSerializeHelper Serialize 2767ms 196225803byte
2023-12-10 23:01:19 556: JsonSerializeHelper Deserialize 2064ms 1000000项
2023-12-10 23:01:20 350: CustomSerializeHelper Serialize 793ms 92853782byte
2023-12-10 23:01:21 012: CustomSerializeHelper Deserialize 662ms 1000000项
2023-12-10 23:01:21 271: BinarySerializeHelper Serialize 258ms 83853767byte
2023-12-10 23:01:22 086: BinarySerializeHelper Deserialize 815ms 1000000项
2023-12-10 23:01:22 629: ProtoBufSerializeHelper Serialize 542ms 88837248byte
2023-12-10 23:01:23 688: ProtoBufSerializeHelper Deserialize 1058ms 1000000项
```

咦，ProtoBuf比BinaryWriter的序列化包还大，并且还慢，难道我用的不对？可能还需要添加压缩算法吧，后面再研究了，我们继续看最后一个MessagePack，有使用问题欢迎指出。

### 3.5. MessagePack

介绍最后一种序列化包`MessagePack`，这是一种高效的二进制序列化格式，它允许数据在不同的系统之间进行快速且紧凑的传输。它类似于JSON，但是更小、更快、更节省空间。

需要安装包`MessagePack`包：

```xml
<PackageReference Include="MessagePack" Version="2.6.100-alpha" />
```

添加类`MessagePackSerializeHelper`，并实现接口`ISerializeHelper`：

```csharp
using ByteTest.Core.Models;
using MessagePack;

namespace ByteTest.Core.Helpers;

public class MessagePackSerializeHelper : ISerializeHelper
{
    // 这种方式需要在类和字段上添加特性，稍显麻烦，但添加压缩选项后，组包体积、组包和解包速度更快
    //readonly MessagePackSerializerOptions _options = MessagePackSerializerOptions.Standard.WithCompression(MessagePackCompression.Lz4BlockArray);

    // 这种方式不需要给传输对象添加特性，也可添加压缩选项
    readonly MessagePackSerializerOptions _options =
        MessagePack.Resolvers.ContractlessStandardResolver.Options.WithCompression(MessagePackCompression
            .Lz4BlockArray);

    public byte[] Serialize(Organization data)
    {
        return MessagePackSerializer.Serialize(data, _options);
    }

    public Organization? Deserialize(byte[] buffer)
    {
        return MessagePackSerializer.Deserialize<Organization>(buffer, _options);
    }
}
```

看上面的注释代码，提供的选项可能更优，压缩体积更小，后面我们添加测试。

最后修改Test方法：

```csharp
    public static void Test(List<ISerializeHelper>? moreHelpers = null)
    {
        var serializeHelpers = new List<ISerializeHelper>
        {
            new JsonSerializeHelper(),
            new CustomSerializeHelper(),
            new BinarySerializeHelper(),
            new ProtoBufSerializeHelper(),
            new MessagePackSerializeHelper(),
        };
        if (moreHelpers?.Count() > 0)
        {
            serializeHelpers.AddRange(moreHelpers);
        }

        serializeHelpers.ForEach(RunSerialize);
    }
```

运行程序输出：

```csharp
2023-12-11 21:34:47 782: JsonSerializeHelper Serialize 2456ms 196225500byte
2023-12-11 21:34:51 215: JsonSerializeHelper Deserialize 3430ms 1000000项
2023-12-11 21:34:52 186: CustomSerializeHelper Serialize 970ms 92853911byte
2023-12-11 21:34:52 711: CustomSerializeHelper Deserialize 526ms 1000000项
2023-12-11 21:34:53 734: BinarySerializeHelper Serialize 1022ms 83853896byte
2023-12-11 21:34:54 354: BinarySerializeHelper Deserialize 620ms 1000000项
2023-12-11 21:34:55 170: ProtoBufSerializeHelper Serialize 815ms 88837377byte
2023-12-11 21:34:56 205: ProtoBufSerializeHelper Deserialize 1035ms 1000000项
2023-12-11 21:34:57 123: MessagePackSerializeHelper Serialize 917ms 43583878byte
2023-12-11 21:34:58 527: MessagePackSerializeHelper Deserialize 1403ms 1000000项
```

再切换上面的注释选项，代码修改如下：

```csharp
using ByteTest.Core.Models;
using MessagePack;

namespace ByteTest.Core.Helpers;

public class MessagePackSerializeHelper : ISerializeHelper
{
    // 这种方式需要在类和字段上添加特性，稍显麻烦，但添加压缩选项后，组包体积、组包和解包速度更快
    readonly MessagePackSerializerOptions _options = MessagePackSerializerOptions.Standard.WithCompression(MessagePackCompression.Lz4BlockArray);

    // 这种方式不需要给传输对象添加特性，也可添加压缩选项
    //readonly MessagePackSerializerOptions _options =
    //    MessagePack.Resolvers.ContractlessStandardResolver.Options.WithCompression(MessagePackCompression
    //        .Lz4BlockArray);

    public byte[] Serialize(Organization data)
    {
        return MessagePackSerializer.Serialize(data, _options);
    }

    public Organization? Deserialize(byte[] buffer)
    {
        return MessagePackSerializer.Deserialize<Organization>(buffer, _options);
    }
}
```

并给传输类添加特性`[MessagePackObject]`，需要序列化的属性添加特性`[Key(序列化索引)]`：

```csharp
using MessagePack;
using ProtoBuf;

namespace ByteTest.Core.Models;

[ProtoContract]
[MessagePackObject]
public class Organization
{
    [ProtoMember(1)] [Key(0)] public int Id { get; set; }

    [ProtoMember(2)] [Key(1)] public string[]? Tags { get; set; }

    [ProtoMember(3)] [Key(2)] public List<Member>? Members { get; set; }
}

[ProtoContract]
[MessagePackObject]
public class Member
{
    [ProtoMember(1)] [Key(0)] public int Id { get; set; }

    [ProtoMember(2)] [Key(1)] public string? Name { get; set; }

    [ProtoMember(3)] [Key(2)] public string? Description { get; set; }

    [ProtoMember(4)] [Key(3)] public string? Address { get; set; }

    [ProtoMember(5)] [Key(4)] public double Value { get; set; }

    [ProtoMember(6)] [Key(5)] public long UpdateTime { get; set; }
}
```

程序输出：

```csharp
2023-12-11 21:49:34 153: JsonSerializeHelper Serialize 2383ms 196226140byte
2023-12-11 21:49:37 736: JsonSerializeHelper Deserialize 3581ms 1000000项
2023-12-11 21:49:38 720: CustomSerializeHelper Serialize 983ms 92854251byte
2023-12-11 21:49:39 250: CustomSerializeHelper Deserialize 530ms 1000000项
2023-12-11 21:49:40 273: BinarySerializeHelper Serialize 1023ms 83854236byte
2023-12-11 21:49:40 907: BinarySerializeHelper Deserialize 632ms 1000000项
2023-12-11 21:49:41 660: ProtoBufSerializeHelper Serialize 754ms 88837717byte
2023-12-11 21:49:42 676: ProtoBufSerializeHelper Deserialize 1014ms 1000000项
2023-12-11 21:49:43 357: MessagePackSerializeHelper Serialize 681ms 38706475byte
2023-12-11 21:49:44 344: MessagePackSerializeHelper Deserialize 986ms 1000000项
```

这里再贴出上一个选项MessagePack的输出：

```csharp
2023-12-11 21:34:57 123: MessagePackSerializeHelper Serialize 917ms 43583878byte
2023-12-11 21:34:58 527: MessagePackSerializeHelper Deserialize 1403ms 1000000项
```

看出还是第一个选项序列化包体积和速度更优秀。

### 方案分析

100万条测试数据，5种序列化方法测试统计数据列出表格如下：

| 序列化方法       | Json   | 自定义二进制 | Binary | ProtoBuf | MessagePack |
| ---------------- | ------ | ------------ | ------ | -------- | ----------- |
| 序列化包大小(MB) | 187.13 | 88.55        | 79.97  | 84.72    | 36.91       |
| 序列化用时(ms)   | 2383   | 983          | 1023   | 754      | 681         |
| 反序列化用时(ms) | 3581   | 530          | 632    | 1014     | 986         |

通过上表分析，序列化后，MessagePack的包最小，为36.91MB，Json最大达到187.13MB，另三种在80MB左右；如果考虑序列化效率MessagePack最好，反序列化效率竟然是我的自定义二进制方式最快？我们去掉Json方式，再运行一次：

```csharp
2023-12-11 21:55:47 813: CustomSerializeHelper Serialize 1263ms 92854890byte
2023-12-11 21:55:48 804: CustomSerializeHelper Deserialize 989ms 1000000项
2023-12-11 21:55:49 215: BinarySerializeHelper Serialize 410ms 83854875byte
2023-12-11 21:55:50 081: BinarySerializeHelper Deserialize 866ms 1000000项
2023-12-11 21:55:50 726: ProtoBufSerializeHelper Serialize 644ms 88838356byte
2023-12-11 21:55:51 725: ProtoBufSerializeHelper Deserialize 999ms 1000000项
2023-12-11 21:55:52 426: MessagePackSerializeHelper Serialize 701ms 38701799byte
2023-12-11 21:55:53 427: MessagePackSerializeHelper Deserialize 999ms 1000000项
```

| 序列化方法       | 自定义二进制 | Binary | ProtoBuf | MessagePack |
| ---------------- | ------------ | ------ | -------- | ----------- |
| 序列化包大小(MB) | 88.55        | 79.97  | 84.72    | 36.91       |
| 序列化用时(ms)   | 1263         | 410    | 644      | 701         |
| 反序列化用时(ms) | 989          | 866    | 999      | 999         |

组包大小不变，序列化使用BinaryWriter最快，反序列也是BinaryReader，测试数据不可靠呀，我们使用基准测试。

### 基准测试

安装BenchmarkDotNet包用于基准测试：

```xml
<PackageReference Include="BenchmarkDotNet" Version="0.13.11" />
```

修改BenchmarkTest类：

```csharp
using BenchmarkDotNet.Attributes;
using ByteTest.Core.Helpers;
using ByteTest.Core.Models;
using MessagePack;
using System.Diagnostics;

namespace ByteTest.Core.Test;

[MemoryDiagnoser, RankColumn]
public class BenchmarkTest
{
    // 省略测试数据代码

    //[Benchmark]
    //public void JsonByteSerialize()
    //{
    //    RunSerialize(new JsonSerializeHelper());
    //}

    [Benchmark]
    public void CustomSerialize()
    {
        RunSerialize(new CustomSerializeHelper());
    }

    [Benchmark]
    public void BinarySerialize()
    {
        RunSerialize(new BinarySerializeHelper());
    }

    [Benchmark]
    public void ProtoBufPackSerialize()
    {
        RunSerialize(new ProtoBufSerializeHelper());
    }

    [Benchmark]
    public void MessagePackSerialize()
    {
        RunSerialize(new MessagePackSerializeHelper());
    }
	
    // 省略统计相关代码
}
```

修改Program.cs：

```csharp
using BenchmarkDotNet.Running;
using ByteTest.Core.Test;

// 运行基准测试
BenchmarkRunner.Run<BenchmarkTest>();

// 普通测试
//BenchmarkTest.Test();
```

测试结果如下：

| Method                |    Mean |    Error |   StdDev | Rank |        Gen0 |       Gen1 |      Gen2 |  Allocated |
| --------------------- | ------: | -------: | -------: | ---: | ----------: | ---------: | --------: | ---------: |
| CustomSerialize       | 1.702 s | 0.0120 s | 0.0094 s |    4 | 156000.0000 | 45000.0000 | 2000.0000 | 1230.31 MB |
| BinarySerialize       | 1.100 s | 0.0101 s | 0.0084 s |    1 |  38000.0000 | 14000.0000 | 2000.0000 |  566.16 MB |
| ProtoBufPackSerialize | 1.337 s | 0.0190 s | 0.0159 s |    2 |  38000.0000 | 14000.0000 | 2000.0000 |  581.66 MB |
| MessagePackSerialize  | 1.544 s | 0.0222 s | 0.0197 s |    3 |  68000.0000 | 29000.0000 | 1000.0000 |  449.67 MB |

大致看下，体积肯定MessagePack占优秀，网络传输中分片更少，意味着网络来回时间花费少；组包（封包）和解包（拆包）原生的BinaryWriter和BinaryReader更优。

## 4. 总结

总的来说，数据包大小需要根据网络环境和设备能力来合理设置，以确保高效的数据传输。同时，高效的组包和解包处理能力对于维持网络传输性能也是至关重要的，前者可考虑MessagePack进行压缩，后者考虑原生BinaryWriter和BinaryReader。

你有更好的方式推荐吗？欢迎留言讨论，您也可以给本文测试代码提PR哦，链接地址是[ByteTest](https://github.com/dotnet9/TerminalMACS.ManagerForWPF/tree/master/src/Demo/ByteTest)

**参考**

- [msgpack/msgpack: MessagePack is an extremely efficient object serialization library. It's like JSON, but very fast and small. (github.com)](https://github.com/msgpack/msgpack)

- [MessagePack简介及使用 - 简书 (jianshu.com)](https://www.jianshu.com/p/8c24bef40e2f)