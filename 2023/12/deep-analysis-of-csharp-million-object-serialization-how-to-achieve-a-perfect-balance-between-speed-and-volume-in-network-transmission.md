---
title: C#百万对象序列化深度剖析：如何在网络传输中实现速度与体积的完美平衡
slug: deep-analysis-of-csharp-million-object-serialization-how-to-achieve-a-perfect-balance-between-speed-and-volume-in-network-transmission
description: 在网络通信中，数据序列化是将对象状态转换为可存储或可传输的形式的过程，这对于TCP网络传输尤为关键。在项目中，当需要处理几十万条数据的传输时，传统的JSON序列化方式由于其冗余的字段名和字符串格式，导致了二进制包体积庞大，且序列化与反序列化的效率低下。为了解决这些问题，我们考虑采用更加高效的序列化方法，以减少包大小并提升处理速度。
date: 2023-12-09 12:15:26
lastmod: 2023-12-10 23:49:41
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2023/11/cover_03.png
categories: .NET相关
tags: 优化
---

**目录**

1. 本文背景
2. 构建数据结构
3. 方案对比
4. 总结

## 1. 本文背景

大家好，我是沙漠尽头的狼。

在网络通信中，数据序列化是将对象状态转换为可存储或可传输的形式的过程，这对于TCP网络传输尤为关键。在项目中，当需要处理几十万条数据的传输时，传统的`Json序列化`方式由于其冗余的字段名和字符串格式，导致了二进制包体积庞大，且序列化与反序列化的效率低下。为了解决这些问题，我考虑采用更加高效的序列化方法，以减少包大小并提升处理速度。本文将探讨自定义二进制序列化、`BinaryWriter/BinaryReader`、[MessagePack](https://github.com/msgpack/msgpack)和[ProtoBuf](https://github.com/protobuf-net/protobuf-net)等4种序列化方法，并通过比较它们的性能，为大家提供我目前认为的最佳实践指南。

## 2. 构建数据结构

创建C#控制台程序，添加以下两个类：

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

添加序列化接口，用于各种序列化库分别实现：

```csharp
public interface ISerializeHelper
{
    byte[] Serialize(Organization data);

    Organization? Deserialize(byte[] buffer);
}
```

再在`BenchmarkTest`类中添加测试用时和序列化包大小打印输出统计：

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

首先安装`System.Text.Json`包，创建`JsonSerializeHelper`实现`ISerializeHelper`接口：

```csharp
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

上面`JsonSerializer.SerializeToUtf8Bytes`内部将对象转Json、转二进制，别再手工傻傻的手工转哦。

再在`BenchmarkTest`类添加测试方法：

```chsarp
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

```shell
2023-12-10 22:28:24 880: JsonByteSerializeHelper Serialize 2813ms 196227181byte
2023-12-10 22:28:26 858: JsonByteSerializeHelper Deserialize 1964ms 1000000项
```

Json序列化100万条数据，需要2.8s，数据包187.14MB，真大、真慢。

### 3.2. 自定义二进制序列化

这是我原来常用的方式，目前看也是啰嗦，首先定义数据包字段规范：

| 数据类型                                              | 二进制长度    | 说明                                                         |
| ----------------------------------------------------- | ------------- | ------------------------------------------------------------ |
| 数字类型（short\ushort\int\uint\long\ulong\double等） | 2\2\4\4\8\8\8 | 基本的数字之类天然是定长                                     |
| string                                                | 4+n           | 用int类型4个字节表示字符串二进制后的长度，n表示字符串二进制实际长度 |
| T[]\`List<T>`                                         | 4+n           | 数组或列表和字符串类似，用int类型4个字节表示数组或列表二进制后的长度，n表示数组或列表二进制实际长度 |

添加`CustomSerializeHelper`实现接口`ISerializeHelper`：

```csharp
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

代码不少，其中还使用到辅助类`ByteHelper`，用于计算字符串数据：

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

```shell
2023-12-10 22:45:14 701: JsonSerializeHelper Serialize 2774ms 196225588byte
2023-12-10 22:45:16 613: JsonSerializeHelper Deserialize 1898ms 1000000项
2023-12-10 22:45:17 414: CustomSerializeHelper Serialize 801ms 92854209byte
2023-12-10 22:45:18 072: CustomSerializeHelper Deserialize 657ms 1000000项
```

能看出优化了不少，自定义二进制序列化减少1亿个字节（100MB左右），快2s左右：

| 序列化方法   | Json序列化                | 自定义二进制            |
| ------------ | ------------------------- | ----------------------- |
| 序列化包大小 | 196225588byte（187.14MB） | 92854209byte（88.55MB） |
| 序列化用时   | 2774ms                    | 801ms                   |
| 反序列化用时 | 1898ms                    | 657ms                   |

### 3.3. BinaryWriter\BinaryReader

创建`BinarySerializeHelper`类实现`ISerializeHelper`接口，这应该是二进制序列化的标准用法了：

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

```shell
2023-12-10 22:52:56 986: JsonSerializeHelper Serialize 2715ms 196225584byte
2023-12-10 22:52:58 910: JsonSerializeHelper Deserialize 1910ms 1000000项
2023-12-10 22:52:59 730: CustomSerializeHelper Serialize 819ms 92853722byte
2023-12-10 22:53:00 389: CustomSerializeHelper Deserialize 659ms 1000000项
2023-12-10 22:53:00 660: BinarySerializeHelper Serialize 269ms 83853707byte
2023-12-10 22:53:01 466: BinarySerializeHelper Deserialize 806ms 1000000项
```

 列出表格：

| 序列化方法   | Json序列化                | 自定义二进制            | BinaryWriter\BinaryReader |
| ------------ | ------------------------- | ----------------------- | ------------------------- |
| 序列化包大小 | 196225584byte（187.14MB） | 92853722byte（88.55MB） | 83853707byte(79.97MB)     |
| 序列化用时   | 2715ms                    | 819ms                   | 269ms                     |
| 反序列化用时 | 1910ms                    | 659ms                   | 806                       |

`BinaryWriter\BinaryReader`序列化包又比自定义二进制序列化方式小8.5MB左右，序列化速度也快0.5s，不错哟。

### 3.4. ProtoBuf

使用ProtoBuf需要安装包`protobuf-net`，并用需要给测试的对象类添加类序列化特性`[ProtoContract]`和属性序列化特性`[ProtoMember(序列化顺序)]`：

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

添加`ProtoBufSerializeHelper`类实现`ISerializeHelper`接口：

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

```shell
2023-12-10 23:01:17 478: JsonSerializeHelper Serialize 2767ms 196225803byte
2023-12-10 23:01:19 556: JsonSerializeHelper Deserialize 2064ms 1000000项
2023-12-10 23:01:20 350: CustomSerializeHelper Serialize 793ms 92853782byte
2023-12-10 23:01:21 012: CustomSerializeHelper Deserialize 662ms 1000000项
2023-12-10 23:01:21 271: BinarySerializeHelper Serialize 258ms 83853767byte
2023-12-10 23:01:22 086: BinarySerializeHelper Deserialize 815ms 1000000项
2023-12-10 23:01:22 629: ProtoBufSerializeHelper Serialize 542ms 88837248byte
2023-12-10 23:01:23 688: ProtoBufSerializeHelper Deserialize 1058ms 1000000项
```

咦，ProtoBuf比BinaryWriter的序列化包还大，并且还慢，难道我用的不对？可能还需要添加压缩算法吧，后面再研究了，我们继续看最后一个MessagePack。

### 3.5. MessagePack

需要安装包`MessagePack`包，并给类添加上`[MessagePackObject]`序列化特性，属性加上`[Key(序列化顺序)]`序列化特性：

```csharp
using MessagePack;
using ProtoBuf;

namespace ByteTest.Core.Models;

[MessagePackObject]
[ProtoContract]
public class Organization
{
    [Key(0)] [ProtoMember(1)] public int Id { get; set; }

    [Key(1)] [ProtoMember(2)] public string[]? Tags { get; set; }

    [Key(2)] [ProtoMember(3)] public List<Member>? Members { get; set; }
}

[MessagePackObject]
[ProtoContract]
public class Member
{
    [Key(0)] [ProtoMember(1)] public int Id { get; set; }

    [Key(1)] [ProtoMember(2)] public string? Name { get; set; }

    [Key(2)] [ProtoMember(3)] public string? Description { get; set; }

    [Key(3)] [ProtoMember(4)] public string? Address { get; set; }

    [Key(4)] [ProtoMember(5)] public double Value { get; set; }

    [Key(5)] [ProtoMember(6)] public long UpdateTime { get; set; }
}
```

添加类`MessagePackSerializeHelper`实现接口`ISerializeHelper`：

```csharp
using ByteTest.Core.Models;
using MessagePack;

namespace ByteTest.Core.Helpers;

public class MessagePackSerializeHelper : ISerializeHelper
{
    public byte[] Serialize(Organization data)
    {
        return MessagePackSerializer.Serialize(data);
    }

    public Organization? Deserialize(byte[] buffer)
    {
        return MessagePackSerializer.Deserialize<Organization>(buffer);
    }
}
```

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
2023-12-10 23:07:33 905: JsonSerializeHelper Serialize 2827ms 196227724byte
2023-12-10 23:07:35 923: JsonSerializeHelper Deserialize 2004ms 1000000项
2023-12-10 23:07:36 730: CustomSerializeHelper Serialize 806ms 92856096byte
2023-12-10 23:07:37 414: CustomSerializeHelper Deserialize 684ms 1000000项
2023-12-10 23:07:37 673: BinarySerializeHelper Serialize 258ms 83856081byte
2023-12-10 23:07:38 525: BinarySerializeHelper Deserialize 851ms 1000000项
2023-12-10 23:07:39 069: ProtoBufSerializeHelper Serialize 544ms 88839562byte
2023-12-10 23:07:40 040: ProtoBufSerializeHelper Deserialize 971ms 1000000项
2023-12-10 23:07:40 632: MessagePackSerializeHelper Serialize 591ms 87724621byte
2023-12-10 23:07:41 612: MessagePackSerializeHelper Deserialize 980ms 1000000项
```

MessagePack和ProtoBuf有啥区别？大小和速度都不占优呢？别慌，经过研究，MessagePack可以添加压缩参数：

```csharp
public class MessagePackSerializeHelper : ISerializeHelper
{
    public byte[] Serialize(Organization data)
    {
        var options = MessagePackSerializerOptions.Standard.WithCompression(MessagePackCompression.Lz4BlockArray);
        return MessagePackSerializer.Serialize(data, options);
    }

    public Organization? Deserialize(byte[] buffer)
    {
        var options = MessagePackSerializerOptions.Standard.WithCompression(MessagePackCompression.Lz4BlockArray);
        return MessagePackSerializer.Deserialize<Organization>(buffer, options);
    }
}
```

再运行：

```shell
2023-12-10 23:09:24 770: JsonSerializeHelper Serialize 2925ms 196225609byte
2023-12-10 23:09:26 668: JsonSerializeHelper Deserialize 1884ms 1000000项
2023-12-10 23:09:27 454: CustomSerializeHelper Serialize 785ms 92854497byte
2023-12-10 23:09:28 111: CustomSerializeHelper Deserialize 656ms 1000000项
2023-12-10 23:09:28 372: BinarySerializeHelper Serialize 261ms 83854482byte
2023-12-10 23:09:29 189: BinarySerializeHelper Deserialize 816ms 1000000项
2023-12-10 23:09:29 746: ProtoBufSerializeHelper Serialize 556ms 88837963byte
2023-12-10 23:09:30 817: ProtoBufSerializeHelper Deserialize 1071ms 1000000项
2023-12-10 23:09:31 478: MessagePackSerializeHelper Serialize 660ms 38699025byte
2023-12-10 23:09:32 513: MessagePackSerializeHelper Deserialize 1034ms 1000000项
```

MessagePack压缩后包小了不少，上面是Debug做的测试，换Release：

```shell
2023-12-10 23:10:28 256: JsonSerializeHelper Serialize 2966ms 196224925byte
2023-12-10 23:10:30 160: JsonSerializeHelper Deserialize 1889ms 1000000项
2023-12-10 23:10:30 893: CustomSerializeHelper Serialize 733ms 92853290byte
2023-12-10 23:10:31 511: CustomSerializeHelper Deserialize 617ms 1000000项
2023-12-10 23:10:31 764: BinarySerializeHelper Serialize 252ms 83853275byte
2023-12-10 23:10:32 646: BinarySerializeHelper Deserialize 882ms 1000000项
2023-12-10 23:10:33 228: ProtoBufSerializeHelper Serialize 581ms 88836756byte
2023-12-10 23:10:34 238: ProtoBufSerializeHelper Deserialize 1010ms 1000000项
2023-12-10 23:10:34 870: MessagePackSerializeHelper Serialize 631ms 38691436byte
2023-12-10 23:10:35 808: MessagePackSerializeHelper Deserialize 937ms 1000000项
```

其实差不多，最后我们来总结。

## 4. 总结

1000000万条测试对象，5种序列化方法测试如下：

| 序列化方法       | Json   | 自定义二进制 | Binary | ProtoBuf | MessagePack |
| ---------------- | ------ | ------------ | ------ | -------- | ----------- |
| 序列化包大小(MB) | 187.13 | 88.55        | 79.97  | 84.72    | 36.90       |
| 序列化用时(ms)   | 2966   | 733          | 252    | 581      | 631         |
| 反序列化用时(ms) | 1889   | 617          | 882    | 1010     | 937         |

通过上表分析，序列化后，MessagePack的包最小，为36.90MB，Json最大达到187.13MB，另三种在80MB左右；如果考虑序列化和反序列化效率，BinaryWriter更快，反序列竟然是我的自定义二进制方式最快？

最后加上基准测试看看，安装BenchmarkDotNet包，BenchamrkTest添加如下方法：

```csharp
[MemoryDiagnoser, RankColumn]
public class BenchmarkTest
{
    // 省略部分代码
     [Benchmark]
     public void BinarySerialize()
     {
         RunSerialize(new BinarySerializeHelper());
     }

     [Benchmark]
     public void CustomSerialize()
     {
         RunSerialize(new CustomSerializeHelper());
     }

     [Benchmark]
     public void JsonByteSerialize()
     {
         RunSerialize(new JsonSerializeHelper());
     }

     [Benchmark]
     public void MessagePackSerialize()
     {
         RunSerialize(new MessagePackSerializeHelper());
     }

     [Benchmark]
     public void ProtoBufPackSerialize()
     {
         RunSerialize(new ProtoBufSerializeHelper());
     }
     // 省略部分代码
}
```

Program修改测试代码：

```chsarp
using BenchmarkDotNet.Running;
using ByteTest.Core.Test;

// 运行基准测试
BenchmarkRunner.Run<BenchmarkTest>();

// 普通测试
//BenchmarkTest.Test();
```

运行输出如下：

![](https://img1.dotnet9.com/2023/11/0301.png)

具体使用哪种方式，从数据包压缩体积考虑，包越小，TCP分的包就少，网络来回传输折腾少；组包、解包效率高，程序响应就快，看怎么取舍了。

你有更好的方式推荐吗？欢迎留言讨论，您也可以给本文测试代码提PR哦，链接地址是[ByteTest](https://github.com/dotnet9/TerminalMACS.ManagerForWPF/tree/master/src/Demo/ByteTest)

**参考**

- [msgpack/msgpack: MessagePack is an extremely efficient object serialization library. It's like JSON, but very fast and small. (github.com)](https://github.com/msgpack/msgpack)

- [MessagePack简介及使用 - 简书 (jianshu.com)](https://www.jianshu.com/p/8c24bef40e2f)