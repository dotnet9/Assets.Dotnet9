# 介绍

这里集中整理 Dotnet9 / CodeWF 目前重点维护的开源项目，文档只保留网站、C# 通用库和 Avalonia 相关项目，方便访问者快速理解每个仓库的定位、安装方式与适用场景。

更多开源项目可访问：[https://github.com/dotnet9/](https://github.com/dotnet9/)

## 网站

| 项目 | 定位 |
| --- | --- |
| [CodeWF](https://github.com/dotnet9/CodeWF) | `dotnet9.com` / `codewf.com` 的网站源码仓库，基于 ASP.NET Core Razor Pages。 |
| [Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9) | 网站文章、文档、工具数据、图片与站点配置的文件型内容仓库。 |

## C# 通用

| 项目 | 定位 |
| --- | --- |
| [CodeWF.EventBus.Socket](https://github.com/dotnet9/CodeWF.EventBus.Socket) | 基于 TCP Socket 的轻量级进程间事件总线，支持发布订阅和 Query/Response。 |
| [CodeWF.EventBus](https://github.com/dotnet9/CodeWF.EventBus) | 进程内事件总线，适合在桌面、Web、控制台项目中做模块解耦和简单 CQRS。 |
| [CodeWF.NetWeaver](https://github.com/dotnet9/CodeWF.NetWeaver) | 网络数据包序列化核心库，并提供基于它的 TCP/UDP 封装层 `CodeWF.NetWrapper`。 |

## Avalonia

| 项目 | 定位 |
| --- | --- |
| [CodeWF.AvaloniaControls](https://github.com/dotnet9/CodeWF.AvaloniaControls) | 面向 Avalonia 12 的通用控件、Guide 引导控件与主题包。 |
| [CodeWF.LogViewer](https://github.com/dotnet9/CodeWF.LogViewer) | 轻量日志核心库与 Avalonia 日志查看控件。 |
| [Lang.Avalonia](https://github.com/dotnet9/Lang.Avalonia) | Avalonia UI 插件化多语言库，支持 JSON、XML、RESX 和强类型 Key 生成。 |
| [CodeWF.Markdown](https://github.com/dotnet9/CodeWF.Markdown) | Avalonia Markdown 渲染控件、排版主题和示例工程。 |
| [CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox) | 基于 Avalonia + Prism 的模块化跨平台桌面工具箱。 |
