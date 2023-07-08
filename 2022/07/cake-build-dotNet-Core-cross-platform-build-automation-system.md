---
title: cake-build -.NET Core 跨平台构建自动化系统
slug: cake-build-dotNet-Core-cross-platform-build-automation-system
description: Cake (C# Make) 是一个带有 C# DSL 的构建自动化系统，用于执行编译代码、复制文件/文件夹、运行单元测试、压缩文件和构建 NuGet 包等操作。
date: 2022-07-12 20:13:27
copyright: Reprint
author: 黑哥聊dotNet
originaltitle: cake-build -.NET Core 跨平台构建自动化系统
originallink: https://mp.weixin.qq.com/s/FWP4AskTvcK_6NpdFo4dQg
draft: False
cover: https://img1.dotnet9.com/2022/07/cover_13.png
categories: .NET相关
---

## 介绍

Cake (C# Make) 是一个带有 C# DSL 的构建自动化系统，用于执行编译代码、复制文件/文件夹、运行单元测试、压缩文件和构建 NuGet 包等操作。

地址：[https://cakebuild.net/docs](https://cakebuild.net/docs)

## 构建

本教程使用Cake Frosting，它允许您将构建编写为标准控制台应用程序作为解决方案的一部分。有关如何运行 Cake 构建的其他可能性。

以下说明需要在 .NET Core 3.1.301 或更高版本上运行 Cake Frosting 1.0.0 或更高版本。您可以在[https://dotnet.microsoft.com/download](https://dotnet.microsoft.com/download)找到 .NET SDK

要创建一个新的Cake Frosting项目，您需要安装 Frosting 模板：

```shell
dotnet new --install Cake.Frosting.Template
```

创建一个新的 Frosting 项目：

```shell
dotnet new cakefrosting
```

这将创建 Cake Frosting 项目和引导脚本。

### 初始构建项目

该类Program包含配置和运行 Cake 的代码：

```csharp
public static class Program
{
    public static int Main(string[] args)
    {
        return new CakeHost()
            .UseContext<BuildContext>()
            .Run(args);
    }
}
```

该类`BuildContext`可用于添加其他自定义属性。`Delay`默认模板包含一个可以通过参数设置的示例属性`--delay`。您可以删除此属性并根据您的特定需求自定义属性。

```csharp
public class BuildContext : FrostingContext
{
    public bool Delay { get; set; }

    public BuildContext(ICakeContext context)
        : base(context)
    {
        Delay = context.Arguments.HasArgument("delay");
    }
}
```

该文件还包含三个任务类：

```csharp
[TaskName("Hello")]
public sealed class HelloTask : FrostingTask<BuildContext>
{
    public override void Run(BuildContext context)
    {
        context.Log.Information("Hello");
    }
}

[TaskName("World")]
[IsDependentOn(typeof(HelloTask))]
public sealed class WorldTask : AsyncFrostingTask<BuildContext>
{
    // Tasks can be asynchronous
    public override async Task RunAsync(BuildContext context)
    {
        if (context.Delay)
        {
            context.Log.Information("Waiting...");
            await Task.Delay(1500);
        }

        context.Log.Information("World");
    }
}

[TaskName("Default")]
[IsDependentOn(typeof(WorldTask))]
public class DefaultTask : FrostingTask
{
}
```
`Default`任务对`World`有依赖性。该`World`任务是一个异步任务`Delay`，如果设置了属性，则等待一秒半。

### 示例构建管道

以下示例创建了一个简单的构建管道，其中包含一个任务、一个编译 MsBuild 解决方案的任务和一个测试解决方案的任务。

以下示例需要Visual Studio 解决方案的存储库根文件夹中的src/Example.s中。

添加所需的 using 语句：

```csharp
using Cake.Common;
using Cake.Common.IO;
using Cake.Common.Tools.DotNet;
using Cake.Common.Tools.DotNet.Build;
using Cake.Common.Tools.DotNet.Test;
```

从类中删除`Delay`属性`BuildContext`并添加一个属性`MsBuildConfiguration`，它存储应该构建的解决方案的配置：

```csharp
public class BuildContext : FrostingContext
{
    public string MsBuildConfiguration { get; set; }

    public BuildContext(ICakeContext context)
        : base(context)
    {
        MsBuildConfiguration = context.Argument("configuration", "Release");
    }
}
```

和`HelloTask`类`WorldTask`可以删除。

`CleanTask`为清理目录的任务创建一个新类：

```csharp
[TaskName("Clean")]
public sealed class CleanTask : FrostingTask<BuildContext>
{
    public override void Run(BuildContext context)
    {
        context.CleanDirectory($"../src/Example/bin/{context.MsBuildConfiguration}");
    }
}
```

创建一个BuildTask用于构建解决方案的新类：

```csharp
[TaskName("Build")]
[IsDependentOn(typeof(CleanTask))]
public sealed class BuildTask : FrostingTask<BuildContext>
{
    public override void Run(BuildContext context)
{
        context.DotNetBuild("../src/Example.sln", new DotNetBuildSettings
        {
            Configuration = context.MsBuildConfiguration,
        });
    }
}
```

创建一个TestTask用于测试解决方案的新类：

```csharp
[TaskName("Test")]
[IsDependentOn(typeof(BuildTask))]
public sealed class TestTask : FrostingTask<BuildContext>
{
    public override void Run(BuildContext context)
{
        context.DotNetTest("../src/Example.sln", new DotNetTestSettings
        {
            Configuration = context.MsBuildConfiguration,
            NoBuild = true,
        });
    }
}
```

更新`DefaultTask`类以调用新任务：

```csharp
[IsDependentOn(typeof(TestTask))]
public sealed class Default : FrostingTask
{
}
```

### 运行构建脚本

运行构建脚本

```shell
./build.ps1
```

更多文档请前往cake-build官网：[https://cakebuild.net](https://cakebuild.net)。

最后大家如果喜欢我的文章，还麻烦给个关注, 希望.NET生态圈越来越好！