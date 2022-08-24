---
title: MAUI使用Masa blazor组件库
slug: Use-masa-blazor-in-maui-blazor
description: 有一款漂亮、美观的组件库可以达到事半功倍的效果
date: 2022-06-21 21:09:47
copyright: Default
originaltitle: MAUI使用Masa blazor组件库
draft: False
cover: https://img1.dotnet9.com/2022/06/1309.png
categories: MAUI,Blazor
---

上一篇([点击阅读](https://dotnet9.com/2022/06/Share-razor-library-between-maui-and-blazor-server-or-client))我们实现了UI在Web端(Blazor Server/Wasm)和客户端(Windows/macOS/Android/iOS)共享，这篇我加上 [Masa Blazor](https://masa-blazor-docs-dev.lonsid.cn/)组件库的引用，并把前几个月写的[时间戳转换](https://dotnet9.com/2022/02/Use-Blazor-to-be-a-simple-online-timestamp-conversion-tool)工具加上。

![](https://img1.dotnet9.com/2022/06/1401.gif)

## 1. 前置知识

关于Masa Blazor请点击[Masa Blazor官网](https://masa-blazor-docs-dev.lonsid.cn/)了解：

>**MASA Blazor**
>
>基于Material Design和BlazorComponent的交互能力提供标准的基础组件库。提供如布局、弹框标准、Loading、全局异常处理等标准场景的预置组件。

## 2. 组件库的引用

组件库的添加参考[Masa官网](https://masa-blazor-docs-dev.lonsid.cn/)，这里写下[Dotnet9后台](https://github.com/dotnet9/Dotnet9)添加记录：

### 2.1 UI共享库的修改-`Dotnet9.WebApp`

1. UI共享库 `Dotnet9.WebApp` 添加`Maas.Blazor`包，刚好今天（21号）Masa发布了`0.5.0-preview.3`版本，我们下载使用：

```shell
Install-Package Masa.Blazor -Version 0.5.0-preview.3
```

![](https://img1.dotnet9.com/2022/06/1402.png)

2. 封装`Maas.Blazor`组件引用

添加文件`./MasaExtensions/MasaSetup.cs`，代码如下：

```C#
using Microsoft.Extensions.DependencyInjection;

namespace Dotnet9.WebApp.MasaExtensions;

public static class MasaSetup
{
    public static void AddMasaSetup(this IServiceCollection services)
    {
        if (services == null) throw new ArgumentNullException(nameof(services));

        services.AddMasaBlazor();   // 这句关键代码
    }
}
```

关键代码只有一行`services.AddMasaBlazor();`，添加扩展类是为了功能扩展，为了其他项目方便使用...

3. `_Imports.razor` 引入`Masa.Blazor`命名空间

```shell
@using Masa.Blazor
```

就这3步对 `Dotnet9.WebApp`的修改。

### 2.2 跨平台项目修改-Dotnet9.MAUI

1. 修改`MauiProgram.cs`文件，添加上面封装的扩展方法`AddMasaSetup()`:

```C#
using Dotnet9.WebApp.MasaExtensions;    // 第1处：添加这个命名空间

namespace Dotnet9.MAUI;

public static class MauiProgram
{
    public static MauiApp CreateMauiApp()
    {
        var builder = MauiApp.CreateBuilder();
        builder
            .UseMauiApp<App>()
            .ConfigureFonts(fonts => { fonts.AddFont("OpenSans-Regular.ttf", "OpenSansRegular"); });

        builder.Services.AddMauiBlazorWebView();
#if DEBUG
        builder.Services.AddBlazorWebViewDeveloperTools();
#endif
        builder.Services.AddMasaSetup();    // 第2外：添加扩展方法引入Masa Blazor

        return builder.Build();
    }
}
```

2. 添加`Masa.Blazor`资源文件

修改`wwwwroot/index.html`文件，添加以下样式（直接复制 [Masa.Blazor](https://masa-blazor-docs-dev.lonsid.cn/getting-started/installation) `Blazor WebAssembly`使用的资源文件）

```html
<link href="_content/Masa.Blazor/css/masa-blazor.css" rel="stylesheet" />
<link href="_content/Masa.Blazor/css/masa-extend-blazor.css" rel="stylesheet" />

<link href="https://cdn.masastack.com/npm/@mdi/font@5.x/css/materialdesignicons.min.css" rel="stylesheet">
<link href="https://cdn.masastack.com/npm/materialicons/materialicons.css" rel="stylesheet">
<link href="https://cdn.masastack.com/npm/fontawesome/v5.0.13/css/all.css" rel="stylesheet">

<script src="_content/BlazorComponent/js/blazor-component.js"></script>
```

### 2.3 Blazor WebAssembly项目修改-Dotnet9.Wasm

1. 修改`Program.cs`文件，添加上面封装的扩展方法`AddMasaSetup()`:

```C#
using Dotnet9.WebApp;
using Dotnet9.WebApp.MasaExtensions;        // 第1处：添加这个命名空间
using Microsoft.AspNetCore.Components.Web;
using Microsoft.AspNetCore.Components.WebAssembly.Hosting;

var builder = WebAssemblyHostBuilder.CreateDefault(args);
builder.RootComponents.Add<Main>("#app");
builder.RootComponents.Add<HeadOutlet>("head::after");

builder.Services.AddMasaSetup();            // 第2外：添加扩展方法引入Masa Blazor
builder.Services.AddScoped(sp => new HttpClient { BaseAddress = new Uri(builder.HostEnvironment.BaseAddress) });

await builder.Build().RunAsync();
```

2. 添加`Masa.Blazor`资源文件

同`Dotnet9.MAUI`


### 2.4 Blazor Server项目修改-Dotnet9.Server

1. 修改`Program.cs`文件，添加上面封装的扩展方法`AddMasaSetup()`:

```C#
using Dotnet9.WebApp.MasaExtensions;        // 第1处：添加这个命名空间

var builder = WebApplication.CreateBuilder(args);

// Add services to the container.
builder.Services.AddRazorPages();
builder.Services.AddServerSideBlazor();
builder.Services.AddMasaSetup();            // 第2外：添加扩展方法引入Masa Blazor

var app = builder.Build();

// Configure the HTTP request pipeline.
if (!app.Environment.IsDevelopment())
{
    app.UseExceptionHandler("/Error");
    // The default HSTS value is 30 days. You may want to change this for production scenarios, see https://aka.ms/aspnetcore-hsts.
    app.UseHsts();
}

app.UseHttpsRedirection();

app.UseStaticFiles();

app.UseRouting();

app.MapBlazorHub(); 
app.MapFallbackToPage("/_Host");

app.Run();
```

2. 添加`Masa.Blazor`资源文件

修改`Pages/_Layout.cshtml`文件，添加以下样式（复制 [Masa.Blazor](https://masa-blazor-docs-dev.lonsid.cn/getting-started/installation) `Blazor Server`使用的资源文件）

```html
<!-- masa blazor css style -->
<link href="_content/Masa.Blazor/css/masa-blazor.css" rel="stylesheet" />
<link href="_content/Masa.Blazor/css/masa-extend-blazor.css" rel="stylesheet" />

<!--icon file,import need to use-->
<link href="https://cdn.masastack.com/npm/@("@mdi")/font@5.x/css/materialdesignicons.min.css" rel="stylesheet">
<link href="https://cdn.masastack.com/npm/materialicons/materialicons.css" rel="stylesheet">
<link href="https://cdn.masastack.com/npm/fontawesome/v5.0.13/css/all.css" rel="stylesheet">

<!--js(should lay the end of file)-->
<script src="_content/BlazorComponent/js/blazor-component.js"></script>
```

**注意**：MAUI Blazor和Blazor WebAssembly两个项目引入Masa Blazor资源文件的代码一样，Blazor Server和前两者主要区别是`materialdesignicons.min.css`文件：

![](https://img1.dotnet9.com/2022/06/1403.png)

这里关于`Masa.Blazor`的引入就介绍完了，总结下关键三步：

1. 添加`Masa.Blazor` Nuget包：`Install-Package Masa.Blazor`；
2. `Masa.Blazor`组件注册使用：`services.AddMasaBlazor();`；
3. 添加`Masa.Blazor`资源文件：Wasm是`wwwwroot/index.html`, blazor server是`_Layout.cshtml`，注意两者添加资源文件的区别。

## 3. 时间戳功能的添加

在做Blazor Server版本网站时，有过一次时间戳功能开发的介绍([点击阅读](https://dotnet9.com/2022/02/Use-Blazor-to-be-a-simple-online-timestamp-conversion-tool))，代码很简单，这里不再细说，不能再水了....

## 4. 总结

`Masa.Blazor`组件库已经添加，并恢复了时间戳功能，下一步，就是继续搭建网站后台了，使用`Masa.Blazor`搭建框架喽。

- 本文Blazor Server站点预览：[https://server.dotnet9.com/](https://server.dotnet9.com/)
- 本文Blazor Wasm站点预览：[https://wasm.dotnet9.com/](https://wasm.dotnet9.com/)
- MAUI(Android\Windows\macOS\iOS)预览：https://github.com/dotnet9/Dotnet9/tree/develop/src/Dotnet9.MAUI（未做发布文件，需要您源码自行编译）