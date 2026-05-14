# CodeWF.LogViewer

![CodeWF.LogViewer](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-logviewer.svg)

`CodeWF.LogViewer` 包含輕量級日誌核心函式庫和 Avalonia UI 日誌顯示控制項。核心套件可用於主控台、WPF、WinForms、Avalonia 等 C# 程式；Avalonia 控制項則負責把日誌即時顯示在介面上，適合桌面應用程式除錯和工具型用戶端。

## 套件清單

| 套件 | 說明 | 適用場景 |
| --- | --- | --- |
| `CodeWF.Log.Core` | 核心日誌函式庫，僅依賴 .NET。 | 主控台、WPF、WinForms、Avalonia 等 C# 程式。 |
| `CodeWF.LogViewer.Avalonia` | Avalonia UI 日誌顯示控制項。 | Avalonia UI 程式內嵌日誌視窗。 |

## 安裝

```powershell
Install-Package CodeWF.Log.Core
Install-Package CodeWF.LogViewer.Avalonia
```

## 基本使用

```csharp
Logger.Debug("除錯日誌");
Logger.Info("一般日誌");
Logger.Warn("警告日誌");
Logger.Error("錯誤日誌");
Logger.Fatal("重大錯誤日誌");
```

主控台程式使用檔案日誌時，建議在啟動和結束時處理檔案通道：

```csharp
Logger.RecordToFile();

// 程式結束時重新整理緩衝區
await Logger.FlushAsync();
```

## 輸出目標控制

每個日誌方法都可以控制是否輸出到 UI、檔案和主控台：

```csharp
Logger.Info(
    content: "寫入檔案的內容",
    uiContent: "UI 顯示的友好內容",
    log2UI: true,
    log2File: true,
    log2Console: true);
```

也可以使用快捷方法：

```csharp
Logger.InfoToFile("僅寫入檔案");
Logger.LogToUI(LogType.Info, "僅顯示 UI");
```

## Avalonia 控制項

在 AXAML 中引入命名空間並放置控制項：

```xml
xmlns:log="https://codewf.com"
```

```xml
<log:LogView />
```

`LogView` 內部會啟動檔案日誌記錄，並從 UI 通道消費日誌顯示到介面。高頻日誌場景中，控制項使用批次處理和防抖重新整理，避免介面頻繁重繪。

## 倉庫

- GitHub：[https://github.com/dotnet9/CodeWF.LogViewer](https://github.com/dotnet9/CodeWF.LogViewer)
- NuGet Core：[https://www.nuget.org/packages/CodeWF.Log.Core](https://www.nuget.org/packages/CodeWF.Log.Core)
- NuGet Avalonia：[https://www.nuget.org/packages/CodeWF.LogViewer.Avalonia](https://www.nuget.org/packages/CodeWF.LogViewer.Avalonia)