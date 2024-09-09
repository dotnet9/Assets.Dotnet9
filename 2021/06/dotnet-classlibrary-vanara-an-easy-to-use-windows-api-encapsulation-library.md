---
title: .NET类库“Vanara”：简单易用的Windows API封装库
slug: dotnet-classlibrary-vanara-an-easy-to-use-windows-api-encapsulation-library
description: 一系列非常简单易用，对Windows API做了极好封装的.NET类库，几乎不用再写繁琐的Windows API转换函数了。
date: 2021-06-28 22:53:47
copyright: Original
originalTitle: .NET类库“Vanara”：简单易用的Windows API封装库
draft: False
cover: https://img1.dotnet9.com/2021/06/cover_01.png
albums: 
    - 开源C#
categories: 
    - .NET
tags: 
    - .NET
    - C#
    - Windows API
---

> 仓库地址：https://github.com/dahall/Vanara
>
> 翻译：沙漠尽头的浪(水平有限，如有条件请尽量查看仓库地址翻阅)

![](https://img1.dotnet9.com/2021/06/0101.png)

> 一系列非常简单易用，对 Windows API 做了极好封装的.NET 类库，几乎不用再写繁琐的 Windows API 转换函数了。

此项目包含各种.NET 程序集，这些程序集包含来自 Windows 库的 P/Invoke 函数、接口、枚举和结构。每个程序集都与一个或几个紧密相关的库相关联。例如，Shlwapi.dll 包含从 Shlwapi.lib 导出的所有函数；Kernel32.dll 包含 Kernel32.lib 和 kernelbase.lib 的全部。

所有程序集都可通过 NuGet 获得，并提供针对.NET 2.0、3.5、4.0、4.5、Core 3.0、Core 3.1 和.NET 5.0（v3.2.20 中新增）的版本，并支持[SourceLink](https://docs.microsoft.com/en-us/dotnet/standard/library-guidance/sourcelink)。在依赖项不允许的所有情况下，.NET Standard 2.0、.NET Core 2.0 和 2.1 版本也包含在 UWP 和其他.NET Core 及标准项目中。

在充分测试之后，这个项目每隔几周发布一次新版本。新的版本和发行说明一起被编目在[Releases](https://github.com/dahall/Vanara/releases)部分，所有 NuGet 包都发布到[nuget.org](https://www.nuget.org/packages?q=dahall+Vanara)。每个 GitHub 推送都会触发[AppVeyor](https://ci.appveyor.com/project/dahall/vanara)构建。所有者感谢他们的免费开源帐户！文章开头显示了项目构建状态信息。[AppVeyor 源](https://ci.appveyor.com/nuget/vanara-prerelease)用于构建 NuGet 包。

## 怎么用？

1. 在 Microsoft 文档中查找所需的函数。请注意函数位于哪个库或 DLL 中。
2. 查看下面的支持库表，确认 Vanara 库存在并具有您需要的函数(Windows API)。单击程序集链接将带您深入了解该程序集的覆盖范围。找到你的函数，如果有一个匹配的实现，它会出现在右边。您还可以使用 GitHub 的项目搜索（页面左上角）来搜索函数、方法或常量。确保选择“在此存储库中”。
3. 通过 NuGet 将程序集添加到项目中。
4. 要使用该功能，您可以：

- 1. 直接调用`var bret = Vanara.PInvoke.Kernel32.GetComputerName(sb, ref sbSz);`
- 2. 在 C#6.0 及更高版本下，使用 static using 指令并调用它：

```C#
using static Vanara.PInvoke.Kernel32;

var bret = GetComputerName(sb, ref sbSz);
```

5.在某些情况下，其中一个[支持程序集]中有一个对应的 helper/wrapper 类，特别是对于安全性、系统服务、窗体和 Shell。转到他们的库页面（单击部分中的链接），浏览每个库中包含的类。

## 设计理念

- 从单个 DLL 导入的所有函数都应放置到以 DLL 命名的单个程序集中。
  - （例如，程序集`Vanara.PInvoke.Gdi32.dll`承载系统目录中从`gdi32.dll`导出的所有函数和支持的枚举、常量和结构。）
- 任何由许多库使用的结构、宏或枚举（非函数）都会放入`Vanara.Core`或'Vanara.PInvoke.Shared`库中。
  - （例如，宏`HIWORD'和结构`SIZE`都在`Vanara.PInvoke.Shared`中，简化互操作调用和本机内存管理的类都在'Vanara.Core`中）
- 在项目中，所有构造都包含在一个以头文件（\*.h）命名的文件中，其中这些结构在 Windows API 中定义。
  - （例如，在`Vanara.PInvoke.Kernel32`项目目录中，您将分别找到一个 FileApi.cs、WinBase.cs 和一个 WinNT.cs 文件，分别表示 FileApi.h、WinBase.h 和 WinNT.h）
- 如果直接解释结构或函数会导致内存泄漏或误用，我试图简化它的使用。
- 在结构体总是通过引用传递，并且在需要清理内存分配的地方，我将结构体更改为实现`IDispoable`的类。
- 尽可能，所有句柄都已转换为以 Windows API 句柄命名的`SafeHandle`派生工具。如果这些句柄需要调用函数以释放/关闭/销毁，则存在一个派生的`SafeHANDLE`，该函数将在 disposal 时执行该函数。
  - 例如，定义了`HTOKEN`。 `SafeHTOKEN`在该句柄上调用`CloseHandle`自动释放。
- 尽可能，分配调用方释放的内存的所有函数都使用安全的内存句柄。
- 程序集中所有 PInvoke 调用都以'Vanara.PInvoke`为前缀。
- 如果要将结构体作为常量传递到函数中，则使用`in`语句封装该结构体，该语句将通过引用传递结构体，而不需要`ref`关键字。
  - Windows API:`BOOL MapDialogRect(HWND hDlg, LPRECT LPRECT)`
  - Vanara:`bool MapDialogRect(HWND hDlg, in RECT lpRect);`
- 如果有类或扩展使用 PInvoke 调用，则它们位于以'Vanara'前缀的包装程序集中，然后后跟该功能的逻辑名称。今天，它们是 Core、Security、SystemServices、Windows.Forms 和 Windows.Shell。

## 支持的库

![](https://img1.dotnet9.com/2021/06/0102.png)

![](https://img1.dotnet9.com/2021/06/0103.png)

![](https://img1.dotnet9.com/2021/06/0104.png)

![](https://img1.dotnet9.com/2021/06/0105.png)

![](https://img1.dotnet9.com/2021/06/0106.png)

## 支持的程序集

![](https://img1.dotnet9.com/2021/06/0107.png)

## 链接

- [Documentation](https://github.com/dahall/Vanara/wiki)
- [Issues](https://github.com/dahall/Vanara/issues)

## 示例代码

There are numerous examples in the [UnitTest](https://github.com/dahall/Vanara/tree/master/UnitTests) folder and in the [WinClassicSamplesCS](https://github.com/dahall/WinClassicSamplesCS) project that recreates the Windows Samples in C# using Vanara.
