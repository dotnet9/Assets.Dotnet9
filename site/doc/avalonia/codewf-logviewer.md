# CodeWF.LogViewer

![CodeWF.LogViewer](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-logviewer.svg)

`CodeWF.LogViewer` 包含轻量级日志核心库和 Avalonia UI 日志展示控件。核心包可用于控制台、WPF、WinForms、Avalonia 等 C# 程序；Avalonia 控件则负责把日志实时显示在界面上，适合桌面应用调试和工具型客户端。

## 包线

| 包 | 说明 | 适用场景 |
| --- | --- | --- |
| `CodeWF.Log.Core` | 核心日志库，仅依赖 .NET。 | 控制台、WPF、WinForms、Avalonia 等 C# 程序。 |
| `CodeWF.LogViewer.Avalonia` | Avalonia UI 日志展示控件。 | Avalonia UI 程序内嵌日志窗口。 |

## 安装

```powershell
Install-Package CodeWF.Log.Core
Install-Package CodeWF.LogViewer.Avalonia
```

## 基本使用

```csharp
Logger.Debug("调试日志");
Logger.Info("普通日志");
Logger.Warn("警告日志");
Logger.Error("错误日志");
Logger.Fatal("严重错误日志");
```

控制台程序使用文件日志时，建议在启动和退出时处理文件通道：

```csharp
Logger.RecordToFile();

// 程序退出时刷新缓冲区
await Logger.FlushAsync();
```

## 输出目标控制

每个日志方法都可以控制是否输出到 UI、文件和控制台：

```csharp
Logger.Info(
    content: "写入文件的内容",
    uiContent: "UI 显示的友好内容",
    log2UI: true,
    log2File: true,
    log2Console: true);
```

也可以使用快捷方法：

```csharp
Logger.InfoToFile("仅写入文件");
Logger.LogToUI(LogType.Info, "仅显示 UI");
```

## Avalonia 控件

在 AXAML 中引入命名空间并放置控件：

```xml
xmlns:log="https://codewf.com"
```

```xml
<log:LogView />
```

`LogView` 内部会启动文件日志记录，并从 UI 通道消费日志显示到界面。高频日志场景中，控件使用批量处理和防抖刷新，避免界面频繁重绘。

## 仓库

- GitHub：[https://github.com/dotnet9/CodeWF.LogViewer](https://github.com/dotnet9/CodeWF.LogViewer)
- NuGet Core：[https://www.nuget.org/packages/CodeWF.Log.Core](https://www.nuget.org/packages/CodeWF.Log.Core)
- NuGet Avalonia：[https://www.nuget.org/packages/CodeWF.LogViewer.Avalonia](https://www.nuget.org/packages/CodeWF.LogViewer.Avalonia)
