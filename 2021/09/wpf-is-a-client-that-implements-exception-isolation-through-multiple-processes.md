---
title: WPF 通过多进程实现异常隔离的客户端
slug: wpf-is-a-client-that-implements-exception-isolation-through-multiple-processes
description: 当 WPF 客户端需要实现插件系统的时候，一般可以基于容器或者进程来实现。如果需要对外部插件实现异常隔离，那么只能使用子进程来加载插件，这样插件如果抛出异常，也不会影响到主进程
date: 2021-09-22 22:10:21
copyright: Reprinted
author: 鹅群中的鸭霸
originalTitle: WPF 通过多进程实现异常隔离的客户端
originalLink: https://www.cnblogs.com/wengzp/archive/2021/09/17/15305896.html
draft: False
cover: https://img1.dotnet9.com/2021/09/cover_01.jpeg
categories: 
    - WPF
tags: 
    - WPF
    - 插件系统
    - 模块化
    - Win32
---

当 WPF 客户端需要实现插件系统的时候，一般可以基于容器或者进程来实现。如果需要对外部插件实现异常隔离，那么只能使用子进程来加载插件，这样插件如果抛出异常，也不会影响到主进程。WPF 元素无法跨进程传输，但是窗口句柄（HWND）可以，所以可以将 WPF 元素包装成 HWND，然后通过进程间通信将插件传输到客户端中，从而实现插件加载。

## 1. 使用 HwndSource 将 WPF 嵌入到 Win32 窗口

HwndSource 会生成一个可以嵌入 WPF 的 Win32 窗口，使用 HwndSource.RootVisual 添加一个 WPF 元素。

```C#
private static IntPtr ViewToHwnd(FrameworkElement element)
{
    var p = new HwndSourceParameters()
    {
        ParentWindow = new IntPtr(-3), // message only
        WindowStyle = 1073741824
    };
    var hwndSource= new HwndSource(p)
    {
        RootVisual = element,
        SizeToContent = SizeToContent.Manual,
    };
    hwndSource.CompositionTarget.BackgroundColor = Colors.White;
    return hwndSource.Handle;
}
```

## 2. 使用 HwndHost 将 Win32 窗口转换成 WPF 元素

Win32 窗口是无法直接嵌入到 WPF 页面中的，所以 .Net 提供了一个 HwndHost 类来转换。 HwndHost 是一个抽象类，通过实现 BuildWindowCore 方法，可以将一个 Win32 窗口转换成 WPF 元素。

```C#
class ViewHost : HwndHost
{
    private readonly IntPtr _handle;

    [DllImport("user32.dll", CharSet = CharSet.Auto)]
    public static extern IntPtr SetParent(HandleRef hWnd, HandleRef hWndParent);

    public ViewHost(IntPtr handle) => _handle = handle;

    protected override HandleRef BuildWindowCore(HandleRef hwndParent)
    {
        SetParent(new HandleRef(null, _handle), hwndParent);
        return new HandleRef(this, _handle);
    }

    protected override void DestroyWindowCore(HandleRef hwnd)
    {
    }
}
```

## 3. 约定插件的入口方法

可以通过多种方式返回插件的界面。我这里约定每个插件的 dll 都有一个 PluginStartup 类，PluginStartup.CreateView() 可以返回插件的界面。

```C#
namespace Plugin1
{
    public class PluginStartup
    {
        public FrameworkElement CreateView() => new UserControl1();
    }
}
```

## 4. 启动插件进程，使用匿名管道实现进程间通信

进程间通信有多种方式，需要功能齐全可以使用 grpc，简单的使用管道就好了。

- 客户端通过指定插件 dll 地址来加载插件。加载插件的时候，启动一个子进程，并且通过管道通信，传输包装插件的 Win32 窗口句柄。

```C#
private FrameworkElement LoadPlugin(string pluginDll)
{
    using (var pipeServer = new AnonymousPipeServerStream(PipeDirection.In, HandleInheritability.Inheritable))
    {
        var startInfo = new ProcessStartInfo()
        {
            FileName = "PluginProcess.exe",
            UseShellExecute = false,
            CreateNoWindow = true,
            Arguments = $"{pluginDll} {pipeServer.GetClientHandleAsString()}"
        };

        var process = new Process { StartInfo = startInfo };
        process.Start();
        _pluginProcessList.Add(process);
        pipeServer.DisposeLocalCopyOfClientHandle();
        using (var reader = new StreamReader(pipeServer))
        {
            var handle = new IntPtr(int.Parse(reader.ReadLine()));
            return new ViewHost(handle);
        }
    }
}
```

- 通过控制台程序装载插件 dll 并将插件界面转换成 Win32 窗口，然后通过管道传输句柄。

```C#
[STAThread]
[LoaderOptimization(LoaderOptimization.MultiDomain)]
static void Main(string[] args)
{
    if (args.Length != 2) return
    var dllPath = args[0];
    var serverHandle = args[1];
    var dll = Assembly.LoadFile(dllPath);
    var startupType = dll.GetType($"{dll.GetName().Name}.PluginStartup");
    var startup = Activator.CreateInstance(startupType);
    var view =(FrameworkElement)  startupType.GetMethod("CreateView").Invoke(startup, null);

    using (var pipeCline = new AnonymousPipeClientStream(PipeDirection.Out, serverHandle))
    {
        using (var writer = new StreamWriter(pipeCline))
        {
            writer.AutoFlush = true;
            var handle = ViewToHwnd(view);
            writer.WriteLine(handle.ToInt32());
        }
    }
    Dispatcher.Run();
}
```

## 5 效果

![](https://img1.dotnet9.com/2021/09/0101.gif)

参考资料和备注

- [示例源码](https://github.com/yijidao/blog/tree/master/WPF/MultipleProcessClient)
- win32 和 WPF 混合开发，不可避免会涉及空域问题。
- 如果不需要异常隔离，使用 mef 或者 prism 已经可以实现良好的插件功能。
- System.AddIn 也可以提供类似的功能，但是只支持到 .net framework 4.8。
- [这里有一个基于 System.AddIn 实现的多进程插件框架](https://github.com/yijidao/BaktunShell)
- [wpf 跟 win32 的文档](https://docs.microsoft.com/en-us/dotnet/desktop/wpf/advanced/wpf-and-win32-interoperation?view=netframeworkdesktop-4.8)
- [如果不具备窗口的知识，这里有篇博文讲的很好](https://www.cnblogs.com/helloj2ee/archive/2009/06/29/1513210.html)
