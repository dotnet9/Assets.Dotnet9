---
title: 从0开始做一个WPF+Blazor对话小程序
slug: Start-a-WPF-Blazor-chat-application-from-0
description: 从一个WPF Hello World程序开始，逐渐引入Blazor，做个免费能看的对话小程序耍耍。
date: 2022-10-28 22:06:32
lastmod: 2022-11-05 12:27:38
copyright: Default
author: 沙漠尽头的狼
draft: false
cover: https://img1.dotnet9.com/2022/10/2-chat-window.png
categories: WPF,Blazor
---

大家好，我是沙漠尽头的狼。

>.NET是免费，跨平台，开源，用于构建所有应用的开发人员平台。

本文演示如何在WPF中使用Blazor开发漂亮的UI，为客户端开发注入新活力。

**注**

要使WPF支持Blazor，.NET版本必须是 6.0 或更高版本，本示例使用的.NET 7.0，版本要求见[链接](https://learn.microsoft.com/zh-cn/aspnet/core/blazor/hybrid/tutorials/wpf?view=aspnetcore-7.0)，截图看如下文字：

![.NET版本要求](https://img1.dotnet9.com/2022/10/1003.png)

## 1. WPF默认程序

使用WPF模板创建一个默认程序，取名【WPFBlazorChat】，项目组织结构如下：

![空白WPF项目](https://img1.dotnet9.com/2022/10/1001.png)

运行项目，一个空白窗口：

![WPF项目空白窗口](https://img1.dotnet9.com/2022/10/1002.png)

接着往下看，我们添加Blazor支持。

## 2. 添加Blazor支持

依然使用上面的工程，添加Blazor支持，此部分参考微软文档[生成 Windows Presentation Foundation (WPF) Blazor 应用](https://learn.microsoft.com/zh-cn/aspnet/core/blazor/hybrid/tutorials/wpf?view=aspnetcore-7.0)，本小节快速略过。

### 2.1 编辑工程文件`WPFBlazorChat.csproj`

![工程文件修改对比](https://img1.dotnet9.com/2022/10/1004.png)

1. 在项目文件的顶部，将 SDK 更改为 `Microsoft.NET.Sdk.Razor`。
2. 添加节点`<RootNameSpace>WPFBlazorChat</RootNameSpace>`，将项目命名空间 `WPFBlazorChat` 设置为应用的根命名空间。
3. 添加`Nuget`包`Microsoft.AspNetCore.Components.WebView.Wpf`，版本看你选择的`.NET`版本而定。

### 2.2 添加`_Imports.razor`文件

`_Imports.razor`文件类似一个`Global` using文件，放置一些全局的命名空间，精简代码。

内容如下，引入了一个命名空间`Microsoft.AspNetCore.Components.Web`，这是Razor常用命名空间：

```html
@using Microsoft.AspNetCore.Components.Web
```

### 2.3 添加`wwwroot\index.html`文件

和Vue\React一样，需要一个`html`文件承载`Razor`组件，页面内容类似：

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>WPFBlazorChat</title>
    <base href="/" />
    <link href="css/app.css" rel="stylesheet" />
    <link href="WpfBlazor.styles.css" rel="stylesheet" />
</head>

<body>
<div id="app">Loading...</div>

<div id="blazor-error-ui">
    An unhandled error has occurred.
    <a href="" class="reload">Reload</a>
    <a class="dismiss">🗙</a>
</div>
<script src="_framework/blazor.webview.js"></script>
</body>

</html>
```

1. `app.css`下面给出了定义。
2. 看`<div id="app">Loading...</div>`，这里是承载`Razor`组件的地方，后面所有加载的`Razor`组件都是在这里渲染出来的。
3. 其他暂时不管。

### 2.4 添加`wwwroot\css\app.css`文件

页面的基本样式，通用的样式可放在这个文件：

```css
html, body {
    font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
}

h1:focus {
    outline: none;
}

a, .btn-link {
    color: #0071c1;
}

.btn-primary {
    color: #fff;
    background-color: #1b6ec2;
    border-color: #1861ac;
}

.valid.modified:not([type=checkbox]) {
    outline: 1px solid #26b050;
}

.invalid {
    outline: 1px solid red;
}

.validation-message {
    color: red;
}

#blazor-error-ui {
    background: lightyellow;
    bottom: 0;
    box-shadow: 0 -1px 2px rgba(0, 0, 0, 0.2);
    display: none;
    left: 0;
    padding: 0.6rem 1.25rem 0.7rem 1.25rem;
    position: fixed;
    width: 100%;
    z-index: 1000;
}

#blazor-error-ui .dismiss {
    cursor: pointer;
    position: absolute;
    right: 0.75rem;
    top: 0.5rem;
}
```

### 2.5 添加一个Razor组件

加一个Razor的经典组件`Counter.razor`，`Blazor`的`Hello World`程序就有这么一个组件，文件路径：`/RazorViews/Counter.razor`，之所以放`RazorViews`目录，是为了和WPF常用的`Views`目录区分，该组件内容如下：

```html
<h1>Counter</h1>

<p>好开心，你点我了，现在是:<span style="color: red;">@currentCount</span></p>

<button class="btn btn-primary" @onclick="IncrementCount">快快点我</button>

@code {
    private int currentCount = 0;

    private void IncrementCount()
    {
        currentCount++;
    }
}
```

一个按钮【快快点我】，点击`@onclick="IncrementCount"`使变量`currentCount`自增，同时页面显示此变量值，相信你能看懂。

### 2.6 Blazor与WPF窗体关联

这是两者产生关系的关键一步，打开窗体`MainWindow.xaml`，修改如下：

![窗体Xaml修改](https://img1.dotnet9.com/2022/10/1005.png)

如上代码，要点如下：

1. 添加上面引入的`Nuget`包`Microsoft.AspNetCore.Components.WebView.Wpf`的命名空间，命名为`blazor`，主要是要使用`BlazorWebView`组件;
2. `BlazorWebView`组件属性`HostPage`指定承载的html文件，`Services`指定razor组件的Ioc容器，看下面的代码;
3. `RootComponent`的`Selector="#app"`属性指示`Razor`组件渲染的位置，看`index.html`中id为`app`的html元素，`ComponentType`指示需要在`#app`中渲染的`Razor`组件类型。

打开`MainWindow.xaml.cs`，修改如下：

![注入Ioc容器](https://img1.dotnet9.com/2022/10/1006.png)

在WPF里可以使用Prism等框架提供的`Unity`、`DryIoc`等`Ioc`容器实现视图与服务的注入；`Razor`组件这里，默认使用`ASP.NET Core`的`IServiceCollection`容器；如果WPF窗体与Razor组件需要共享数据，可以通过后面要说的`Messager`发送消息，也可以通过`Ioc`容器注入的方式实现，比如从WPF窗体中注入的数据（通过MainWindow构造函数注入），通过`IServiceCollection`容器再注入`Razor`组件使用，这里后面也有提到。

![WPF与Razor组件之间通过Ioc数据传输](https://img1.dotnet9.com/2022/10/1008.png)

上面步骤做完后，运行程序：

![WPF集成Blazor的默认程序在](https://img1.dotnet9.com/2022/10/1007.gif)

OK，WPF与Blazor集成成功，打完收工！

等等，还没完呢，接着往下看。

## 3. 自定义窗体

### 3.1 WPF原生实现

### 3.2 Blazor实现标题栏

## 4. 添加第三方Blazor组件

## 5. 多窗体消息通知

### 5.1 单例实现通知

#### 5.1.1 属性、方法、委托

### 5.2 定义一个Messager

## 6. 实现本文示例

## 7. Click Once发布尝试

参考：[《快速创建软件安装包-ClickOnce》](https://mp.weixin.qq.com/s/zcO1J-AqiK7LkU52MRwmqw)

## 8. Q&A

- 8.1 BlazorWebView的竖直滚动条怎么回事？

隐藏BlazorWebView滚动条

- 窗体拖动：https://github.com/James231/BlazorDesktopWPF-CustomTitleBar

- 8.2 WPF + Blazor支持哪些操作系统

支持Windows 7，这是本文示例Click Once安装页面：https://dotnet9.com/WPFBlazorChat

- 8.3 为啥要在WPF里使用Blazor？吃饱了撑的？

- 8.4 Blazor还有哪些框架可以使用？

- 8.5 本文示例代码能给我不？

