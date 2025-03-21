---
title: C# 如何将程序加密隐藏？
slug: How-to-encrypt-and-hide-programs-in-Csharp
description: 介绍如何通过LiteDB将自己的程序进行加密，实现介绍一下LiteDB。
date: 2023-08-15 20:23:53
copyright: Reprinted
author: tokengo
originalTitle: c# 如何将程序加密隐藏？
originalLink: https://mp.weixin.qq.com/s/JPBwcTfTjFZrSa-iI8gqCQ
draft: false
cover: https://img1.dotnet9.com/2023/08/cover_02.png
categories: 
    - .NET
tags: 
    - .NET
---

下面将介绍如何通过 LiteDB 将自己的程序进行加密，实现介绍一下 LiteDB。

## LiteDB

LiteDB 是一个轻量级的嵌入式数据库，它是用 C#编写的，适用于.NET 平台。它的设计目标是提供一个简单易用的数据库解决方案，可以在各种应用程序中使用。

LiteDB 使用单个文件作为数据库存储，这个文件可以在磁盘上或内存中。它支持文档存储模型，类似于 NoSQL 数据库，每个文档都是一个 JSON 格式的对象。这意味着你可以存储和检索任意类型的数据，而不需要预定义模式。

LiteDB 提供了一组简单的 API 来执行各种数据库操作，包括插入、更新、删除和查询。它还支持事务，可以确保数据的一致性和完整性。

LiteDB 还提供了一些高级功能，如索引、全文搜索和文件存储。索引可以加快查询的速度，全文搜索可以在文本数据中进行关键字搜索，文件存储可以将文件直接存储在数据库中。

LiteDB 的优点包括易于使用、轻量级、快速和可嵌入性。它的代码库非常小，可以很容易地集成到你的应用程序中。此外，它还具有跨平台的能力，可以在 Windows、Linux 和 Mac 等操作系统上运行。

总之，LiteDB 是一个简单易用的嵌入式数据库，适用于各种应用程序。它提供了一组简单的 API 来执行数据库操作，并支持一些高级功能。如果你需要一个轻量级的数据库解决方案，可以考虑使用 LiteDB。

## 加密封装

创建`LiteDB.Service`的 WebApi 项目。

右键发布：

![](https://img1.dotnet9.com/2023/08/0201.png)

创建控制台`LiteDB.Launch`项目。

`EntryPointDiscoverer.cs` 用于寻找执行方法。

```csharp
internal class EntryPointDiscoverer
{
    public static MethodInfo FindStaticEntryMethod(Assembly assembly, string? entryPointFullTypeName = null)
    {
        var candidates = new List<MethodInfo>();

        if (!string.IsNullOrWhiteSpace(entryPointFullTypeName))
        {
            var typeInfo = assembly.GetType(entryPointFullTypeName, false, false)?.GetTypeInfo();
            if (typeInfo == null)
            {
                throw new InvalidProgramException($"Could not find '{entryPointFullTypeName}' specified for Main method. See <StartupObject> project property.");
            }
            FindMainMethodCandidates(typeInfo, candidates);
        }
        else
        {
            foreach (var type in assembly
                         .DefinedTypes
                         .Where(t => t.IsClass)
                         .Where(t => t.GetCustomAttribute<CompilerGeneratedAttribute>() is null))
            {
                FindMainMethodCandidates(type, candidates);
            }
        }

        string MainMethodFullName()
        {
            return string.IsNullOrWhiteSpace(entryPointFullTypeName) ? "Main" : $"{entryPointFullTypeName}.Main";
        }

        if (candidates.Count > 1)
        {
            throw new AmbiguousMatchException(
                $"Ambiguous entry point. Found multiple static functions named '{MainMethodFullName()}'. Could not identify which method is the main entry point for this function.");
        }

        if (candidates.Count == 0)
        {
            throw new InvalidProgramException(
                $"Could not find a static entry point '{MainMethodFullName()}'.");
        }

        return candidates[0];
    }

    private static void FindMainMethodCandidates(TypeInfo type, List<MethodInfo> candidates)
    {
        foreach (var method in type
                     .GetMethods(BindingFlags.Static |
                                 BindingFlags.Public |
                                 BindingFlags.NonPublic)
                     .Where(m =>
                         string.Equals("Main", m.Name, StringComparison.OrdinalIgnoreCase)))
        {
            if (method.ReturnType == typeof(void)
                || method.ReturnType == typeof(int)
                || method.ReturnType == typeof(Task)
                || method.ReturnType == typeof(Task<int>))
            {
                candidates.Add(method);
            }
        }
    }
}
```

然后打开`Program.cs`文件，**请注意修改 SaveDb 的参数为自己项目打包的地址**

```csharp
// 用于打包指定程序。
SaveDb(@"E:\Project\LiteDB-Application\LiteDB.Service\bin\Release\net7.0\publish");

// 打开db
var db = new LiteDatabase("Launch.db");

// 打开表。
var files = db.GetCollection<FileAssembly>("files");

// 接管未找到的程序集处理
AppDomain.CurrentDomain.AssemblyResolve += (sender, eventArgs) =>
{
    try
    {
        var name = eventArgs.Name.Split(",")[0];
        if (!name.EndsWith(".dll"))
        {
            name += ".dll";
        }

        var file = files.FindOne(x => x.Name == name);

        return file != null ? Assembly.Load(file.Bytes) : default;
    }
    catch (Exception)
    {
        return default;
    }
};

// 启动程序。
StartServer("LiteDB.Service", new string[] { });

Console.ReadKey();

void StartServer(string assemblyName, string[] args)
{
    var value = files!.FindOne(x => x.Name == assemblyName + ".dll");
    var assembly = Assembly.Load(value!.Bytes);

    var entryPoint = EntryPointDiscoverer.FindStaticEntryMethod(assembly);

    try
    {
        var parameters = entryPoint.GetParameters();
        if (parameters.Length != 0)
        {
            var parameterValues = parameters.Select(p =>
                    p.ParameterType.IsValueType ? Activator.CreateInstance(p.ParameterType) : null)
                .ToArray();
            entryPoint.Invoke(null, parameterValues);
        }
        else
        {
            entryPoint.Invoke(null, null);
        }
    }
    catch (Exception e)
    {

    }
}


// 扫描指定目录下所有文件和子目录，保存到LiteDB数据库中。
void SaveDb(string path)
{
    var files = ScanDirectory(path);
    using var db = new LiteDatabase("Launch.db");
    var col = db.GetCollection<FileAssembly>("files");
    col.InsertBulk(files);
}

// 实现一个方法，扫描指定目录下所有文件和子目录，返回一个集合。
List<FileAssembly> ScanDirectory(string path)
{
    var files = new List<FileAssembly>();
    var dir = new DirectoryInfo(path);
    var fileInfos = dir.GetFiles("*", SearchOption.AllDirectories);
    foreach (var fileInfo in fileInfos)
    {
        var file = new FileAssembly
        {
            Name = fileInfo.Name,
            Bytes = File.ReadAllBytes(fileInfo.FullName)
        };
        files.Add(file);
    }

    return files;
}

class FileAssembly
{
    /// <summary>
    /// 文件名
    /// </summary>
    public string Name { get; set; }

    /// <summary>
    /// 文件的内容
    /// </summary>
    public byte[] Bytes { get; set; }
}
```

点击`LiteDB.Launch`项目文件，添加 LiteDB 依赖，并且修改 SDK 为`Microsoft.NET.Sdk.Web`

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">

    <PropertyGroup>
        <OutputType>Exe</OutputType>
        <TargetFramework>net7.0</TargetFramework>
        <ImplicitUsings>enable</ImplicitUsings>
        <Nullable>enable</Nullable>
    </PropertyGroup>

    <ItemGroup>
      <PackageReference Include="LiteDB" Version="5.0.17" />
    </ItemGroup>

</Project>
```

在启动项目的时候先将`LiteDB.Service`发布一下。然后修改`SaveDb`参数为发布的目录（会自动扫描所有文件打包到 LiteDB 的文件中。）

然后启动项目；

![](https://img1.dotnet9.com/2023/08/0202.png)

当我们启动了`LiteDB.Launch`以后在`StartServer`方法里面就会打开创建的 LiteDB 文件中搜索到指定的启动程序集。

然后在`AppDomain.CurrentDomain.AssemblyResolve`中会将启动程序集缺少的程序集加载到域中。

`AppDomain.CurrentDomain.AssemblyResolve`会在未找到依赖的时候触发的一个事件。

在存储到 LiteDB 的时候可以对于存储的内容进行加密,然后在`AppDomain.CurrentDomain.AssemblyResolve`触发的时候将读取 LiteDB 的文件的内容的时候进行解密。

## 结尾

来自 token 的分享

qq 技术交流群：737776595
