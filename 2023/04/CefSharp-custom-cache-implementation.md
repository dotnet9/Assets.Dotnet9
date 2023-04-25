---
title: CefSharp自定义缓存实现
slug: CefSharp-custom-cache-implementation
description: 有在客户端内嵌网页的需求吗？CefSharp可能是个不错的选择！
date: 2023-04-25 11:36:26
copyright: Default
draft: true
cover: https://img1.dotnet9.com/2023/03/cover_14.png
categories: WPF,.NET相关,Winform
tags: CefSharp
---

大家好，我是沙漠尽头的狼。

上文介绍了[《C#使用CefSharp内嵌网页-并给出C#与JS的交互示例》](https://dotnet9.com/2023/03/Csharp-uses-CefSharp-to-embed-web-page-and-gives-an-example-of-the-interaction-between-Csharp-and-JS)，本文介绍CefSharp的缓存实现，先来说说添加缓存的好处如下：

1. 加速页面加载：CefSharp缓存可以缓存已经加载过的页面和资源，当用户再次访问相同的页面时，可以直接从缓存中加载，而不需要重新下载和解析页面和资源，从而加快页面加载速度。
2. 减少网络流量：理由同上。
3. 提高用户体验：CefSharp缓存可以提高用户体验，因为它可以使页面加载更快，减少页面闪烁和重新加载的情况，从而提高用户满意度。
4. 离线访问：CefSharp缓存可以使应用程序支持离线访问，因为它可以缓存已经下载过的页面和资源，当用户没有网络连接时，可以直接从缓存中加载页面和资源。

CefSharp缓存可以提高应用程序的性能和用户体验，减少网络流量，并支持离线访问。

本文示例：[Github](https://github.com/dotnet9/TerminalMACS.ManagerForWPF/tree/master/src/Demo/WpfWithCefSharpCacheDemo)

断网情况下，演示加载已经缓存的[百度](www.baidu.com)、[百度翻译](https://fanyi.baidu.com/)、[Dotnet9首页](https://dotnet9.com/)、[Dotnet9关于](ttps://dotnet9.com/about)4个页面：

![](https://img1.dotnet9.com/2023/04/0401.gif)

接下来讲解缓存的实现方式。

## 1. 默认缓存实现

CefSharp的默认缓存实现方式是基于Chromium的缓存机制。Chromium使用了两种类型的缓存：内存缓存和磁盘缓存。

### 1.1. 内存缓存

内存缓存是一个基于LRU（最近最少使用）算法的缓存，它缓存了最近访问的页面和资源。内存缓存的大小是有限的，当缓存达到最大大小时，最近最少使用的页面和资源将被删除。

内存缓存无法通过CefSharp.WPF的API进行设置。具体来说，Chromium会在内存中维护一个LRU（Least Recently Used）缓存，用于存储最近访问的网页数据。当缓存空间不足时，Chromium会根据LRU算法自动清除最近最少使用的缓存数据，以腾出空间存储新的数据。

在CefSharp.WPF中，我们可以通过调用Cef.GetGlobalRequestContext().ClearCacheAsync()方法来清除内存缓存中的数据。该方法会清除所有缓存数据，包括内存缓存和磁盘缓存。如果只需要清除内存缓存，可以调用Cef.GetGlobalRequestContext().ClearCache(CefCacheType.MemoryCache)方法。

需要注意的是，由于内存缓存是由Chromium自身维护的，因此我们无法直接控制其大小。如果需要控制缓存大小，可以通过设置磁盘缓存的大小来间接控制内存缓存的大小。

### 1.2. 磁盘缓存

磁盘缓存是一个基于文件系统的缓存，它缓存了已经下载的页面和资源。磁盘缓存的大小也是有限的，当缓存达到最大大小时，最早的页面和资源将被删除。

CefSharp.WPF的磁盘缓存是通过设置CefSettings中的CachePath属性来实现的。具体来说，我们可以通过以下代码设置磁盘缓存的路径：

```csharp
public partial class App : Application
{
    protected override void OnStartup(StartupEventArgs e)
    {
        base.OnStartup(e);

        // CachePath需要为绝对路径
        var settings = new CefSettings
        {
            CachePath = $"{AppDomain.CurrentDomain.BaseDirectory}DefaultCaches"
        };
        Cef.Initialize(settings);
    }
}
```

缓存目录结构如下：

![](https://img1.dotnet9.com/2023/04/0402.png)

其中，CachePath属性指定了磁盘缓存的路径（绝对路径）。如果不设置该属性，Chromium会将缓存数据存储在默认路径下（通常是用户目录下的AppData\\Local\\CefSharp目录）。

需要注意的是，磁盘缓存的大小是由Chromium自身控制的，我们可以通过设置CacheController的SetCacheLimit方法来控制缓存数据存储在磁盘上的最大空间。该方法接受一个long类型的参数，表示缓存数据的最大大小（单位为字节）。例如，以下代码将磁盘缓存的最大大小设置为100MB：

```csharp
var cacheController = Cef.GetGlobalRequestContext().CacheController;
cacheController.SetCacheLimit(100 * 1024 * 1024); // 100MB
```

需要注意的是，Chromium会根据LRU算法自动清除最近最少使用的缓存数据，以腾出空间存储新的数据。因此，即使设置了缓存大小，也不能保证所有数据都会被缓存。如果需要清除磁盘缓存中的数据，可以调用Cef.GetGlobalRequestContext().ClearCacheAsync()方法。

默认的缓存站长研究不多，上面的代码和描述通过ChatGPT搜索得来，我们来看自定义缓存的实现，默认缓存只是个引子。

## 2. 自定义缓存

这是本文介绍的重点，相对于默认缓存，`自定义缓存`有以下好处：

1. 更好的性能：自定义缓存可以根据应用程序的需求和特定的场景进行配置，以获得更好的性能。默认的缓存可能不适合某些特定的场景或者不适合您的应用程序的需求，而自定义缓存则可以根据您的需求进行调整，以获得更好的性能。
2. 更好的响应性：自定义缓存可以更好地适应应用程序的响应性需求。默认的缓存可能不能提供足够的响应性能，而自定义缓存则可以根据您的需求进行调整，以提供更好的响应性能。
3. 更好的安全性：自定义缓存可以提供更好的安全性。默认的缓存可能存在一些安全漏洞，而自定义缓存则可以根据您的需求进行调整，以提供更好的安全性能。
4. 更好的兼容性：自定义缓存可以更好地适应不同的浏览器和设备。默认的缓存可能不能提供足够的兼容性，而自定义缓存则可以根据您的需求进行调整，以提供更好的兼容性。

总结：自定义缓存可以为 CefSharp.Wpf 应用程序提供更好的性能、响应性、安全性和兼容性，从而提高应用程序的质量和用户体验，人话就是更好的`操控`。

### 2.1. 代码实现

**注释前面加的默认缓存代码。**

#### 2.1.1. 注册资源请求拦截处理程序

首先在使用`ChromiumWebBrowser`控件的后台代码里，注册请求拦截处理程序，`CefBrowser`是控件名，`CefRequestHandlerc`是处理程序：

```C#
public TestCefCacheView()
{
    InitializeComponent();

    var handler = new CefRequestHandlerc();
    CefBrowser.RequestHandler = handler;
}
```

#### 2.1.2. 请求拦截处理程序

在下面的`GetResourceRequestHandler`方法里返回`CefResourceRequestHandler`实例，页面中资源请求时会调用此方法：

```C#
using CefSharp;
using CefSharp.Handler;

namespace WpfWithCefSharpCacheDemo.Caches;

internal class CefRequestHandlerc : RequestHandler
{
    protected override IResourceRequestHandler GetResourceRequestHandler(IWebBrowser chromiumWebBrowser, IBrowser browser, IFrame frame,
        IRequest request, bool isNavigation, bool isDownload, string requestInitiator, ref bool disableDefaultHandling)
    {
        // 一个请求用一个CefResourceRequestHandler
        return new CefResourceRequestHandler();
    }
}
```

#### 2.1.3. 资源请求拦截程序

在下面的`CefResourceRequestHandler`类中:

1、`GetResourceHandler`方法：处理资源是否需要缓存，返回null不缓存，返回`CefResourceHandler`表示需要缓存，在这个类中做跨域处理。
2、`GetResourceResponseFilter`方法：注册资源缓存的操作类，即资源下载的实现。
3、`OnBeforeResourceLoad`方法：在这个方法里，我们可以实现给页面传递header参数。

```C#
using System.Collections.Specialized;
using CefSharp;
using CefSharp.Handler;

namespace WpfWithCefSharpCacheDemo.Caches;

internal class CefResourceRequestHandler : ResourceRequestHandler
{
    private string _localCacheFilePath;

    private bool IsLocalCacheFileExist => System.IO.File.Exists(_localCacheFilePath);

    protected override IResourceHandler? GetResourceHandler(IWebBrowser chromiumWebBrowser, IBrowser browser,
        IFrame frame, IRequest request)
    {
        try
        {
            _localCacheFilePath = CacheFileHelper.CalculateResourceFileName(request.Url, request.ResourceType);
            if (string.IsNullOrWhiteSpace(_localCacheFilePath))
            {
                return null;
            }
        }
        catch
        {
            return null;
        }

        if (!IsLocalCacheFileExist)
        {
            return null;
        }

        return new CefResourceHandler(_localCacheFilePath);
    }

    protected override IResponseFilter? GetResourceResponseFilter(IWebBrowser chromiumWebBrowser, IBrowser browser,
        IFrame frame,
        IRequest request, IResponse response)
    {
        return IsLocalCacheFileExist ? null : new CefResponseFilter { LocalCacheFilePath = _localCacheFilePath };
    }

    protected override CefReturnValue OnBeforeResourceLoad(IWebBrowser chromiumWebBrowser, IBrowser browser,
        IFrame frame, IRequest request,
        IRequestCallback callback)
    {
        var headers = new NameValueCollection(request.Headers);
        headers["Authorization"] = "Bearer xxxxxx.xxxxx.xxx";
        request.Headers = headers;

        return CefReturnValue.Continue;
    }
}
```

#### 2.1.4. CefResourceHandler

处理跨域问题：

```C#
using CefSharp;
using System.IO;

namespace WpfWithCefSharpCacheDemo.Caches;

internal class CefResourceHandler : ResourceHandler
{
    public CefResourceHandler(string filePath, string mimeType = null, bool autoDisposeStream = false,
        string charset = null) : base()
    {
        if (string.IsNullOrWhiteSpace(mimeType))
        {
            var fileExtension = Path.GetExtension(filePath);
            mimeType = Cef.GetMimeType(fileExtension);
            mimeType = mimeType ?? DefaultMimeType;
        }

        var stream = File.OpenRead(filePath);
        StatusCode = 200;
        StatusText = "OK";
        MimeType = mimeType;
        Stream = stream;
        AutoDisposeStream = autoDisposeStream;
        Charset = charset;

        Headers.Add("Access-Control-Allow-Origin", "*");
    }
}
```

#### 2.1.5. CefResponseFilter

处理文件缓存实际操作类，即资源下载实现：

```C#
using CefSharp;
using System.IO;

namespace WpfWithCefSharpCacheDemo.Caches;

internal class CefResponseFilter : IResponseFilter
{
    public string LocalCacheFilePath { get; set; }
    private const int BUFFER_LENGTH = 1024;
    private bool isFailCacheFile;


    public FilterStatus Filter(Stream? dataIn, out long dataInRead, Stream? dataOut, out long dataOutWritten)
    {
        dataInRead = 0;
        dataOutWritten = 0;

        if (dataIn == null)
        {
            return FilterStatus.NeedMoreData;
        }

        var length = dataIn.Length;
        var data = new byte[BUFFER_LENGTH];
        var count = dataIn.Read(data, 0, BUFFER_LENGTH);

        dataInRead = count;
        dataOutWritten = count;

        dataOut?.Write(data, 0, count);

        try
        {
            CacheFile(data, count);
        }
        catch
        {
            // ignored
        }

        return length == dataIn.Position ? FilterStatus.Done : FilterStatus.NeedMoreData;
    }

    public bool InitFilter()
    {
        try
        {
            var dirPath = Path.GetDirectoryName(LocalCacheFilePath);
            if (!string.IsNullOrWhiteSpace(dirPath) && !Directory.Exists(dirPath))
            {
                Directory.CreateDirectory(dirPath);
            }
        }
        catch
        {
            // ignored
        }

        return true;
    }

    public void Dispose()
    {
    }

    private void CacheFile(byte[] data, int count)
    {
        if (isFailCacheFile)
        {
            return;
        }

        try
        {
            if (!File.Exists(LocalCacheFilePath))
            {
                using var fs = File.Create(LocalCacheFilePath);
                fs.Write(data, 0, count);
            }
            else
            {
                using var fs = File.Open(LocalCacheFilePath, FileMode.Append);
                fs.Write(data,0,count);
            }
        }
        catch
        {
            isFailCacheFile = true;
            File.Delete(LocalCacheFilePath);
        }
    }
}
```

#### 2.1.6. CacheFileHelper

缓存文件帮助类，用于管理页面的ajax接口缓存白名单、缓存文件路径规范等：

```C#
using CefSharp;
using System;
using System.Collections.Generic;
using System.IO;

namespace WpfWithCefSharpCacheDemo.Caches;

internal static class CacheFileHelper
{
    private const string DEV_TOOLS_SCHEME = "devtools";
    private const string DEFAULT_INDEX_FILE = "index.html";

    private static HashSet<string> needInterceptedAjaxInterfaces = new();

    private static string CachePath = Path.Combine(AppDomain.CurrentDomain.BaseDirectory, "caches");

    public static void AddInterceptedAjaxInterfaces(string url)
    {
        if (needInterceptedAjaxInterfaces.Contains(url))
        {
            return;
        }

        needInterceptedAjaxInterfaces.Add(url);
    }

    private static bool IsNeedInterceptedAjaxInterface(string url, ResourceType resourceType)
    {
        var uri = new Uri(url);
        if (DEV_TOOLS_SCHEME == url)
        {
            return false;
        }

        if (ResourceType.Xhr == resourceType && !needInterceptedAjaxInterfaces.Contains(url))
        {
            return false;
        }

        return true;
    }

    public static string? CalculateResourceFileName(string url, ResourceType resourceType)
    {
        if (!IsNeedInterceptedAjaxInterface(url, resourceType))
        {
            return default;
        }

        var uri = new Uri(url);
        var urlPath = uri.LocalPath;

        if (urlPath.StartsWith("/"))
        {
            urlPath = urlPath.Substring(1);
        }

        var subFilePath = urlPath;
        if (ResourceType.MainFrame == resourceType || string.IsNullOrWhiteSpace(urlPath))
        {
            subFilePath = Path.Combine(urlPath, DEFAULT_INDEX_FILE);
        }

        var hostCachePath = Path.Combine(CachePath, uri.Host);
        var fullFilePath = Path.Combine(hostCachePath, subFilePath);
        return fullFilePath;
    }
}
```

自定义缓存的子目录以资源的域名为名创建：

![](https://img1.dotnet9.com/2023/04/0403.png)

打开缓存的[Dotnet9](https://dotnet9.com)目录，和程序发布目录基本一致，这更适合人看了！！！

![](https://img1.dotnet9.com/2023/04/0404.png)

### 2.2. 可能存在的问题

第一点，站长目前遇到的问题，后面4点由[文心一言](https://yiyan.baidu.com/)提供解释。

#### 2.2.1. 对缓存的资源URL带QueryString的方式支持不好。
   
建议用Route(路由的方式：https://dotnet9.com/albums/wpf)代替QueryString(查询参数的试工：https://dotnet9.com/albums?slug=wpf)的方式，站长有空再研究下QueryString的缓存方式。

如果确实资源带QueryString，那对于这种资源就放开缓存，直接通过网络请求吧。

#### 2.2.2. 内存泄漏

使用自定义缓存时，可能会发生内存泄漏。这是因为缓存实例在使用后没有被正确地销毁或回收，导致持有的内存无法释放。要避免内存泄漏，请确保在缓存实例不再需要时正确地释放它们。

#### 2.2.3. 性能问题

自定义缓存可能会影响应用程序的性能。这是因为缓存实例可能会消耗大量的系统资源，如内存和处理器时间。如果缓存实例的大小或性能限制了应用程序的性能，则可能会导致应用程序的运行速度变慢。要确保自定义缓存不会成为应用程序性能的瓶颈，请确保它们的大小和性能不会对应用程序的运行产生负面影响。

#### 2.2.4. 兼容性问题

自定义缓存可能会与应用程序的依赖项发生冲突。这是因为不同的依赖项可能使用不同的缓存实现或者有不同的配置参数。要确保自定义缓存与应用程序的依赖项相容，请在设计阶段考虑到缓存实现和配置参数的差异，并进行适当的测试和兼容性验证。

#### 2.2.5. 权限问题

在某些情况下，自定义缓存可能需要访问应用程序的私有数据或者访问受限的资源。在这种情况下，可能需要使用 CefSharp.WPF 提供的安全性功能来确保缓存的正确使用，并且遵守应用程序的访问控制策略。

总之，在使用自定义缓存时，需要注意内存泄漏、性能问题、兼容性问题和权限问题等问题，并根据具体情况进行适当的优化和测试。

## 参考：

- [CefSharp](https://cefsharp.github.io/)
- [关于CefSharp中C#与JS函数互相调用的应用](https://www.cnblogs.com/guhunjun/p/16229083.html)

- 微信技术交流群：添加微信（dotnet9com）备注“入群”
- QQ技术交流群：771992300。

![](https://img1.dotnet9.com/site/knowledgeplanet.jpg)