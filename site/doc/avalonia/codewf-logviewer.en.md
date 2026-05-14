# CodeWF.LogViewer

![CodeWF.LogViewer](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-logviewer.svg)

`CodeWF.LogViewer` includes a lightweight logging core library and an Avalonia UI log display control. The core package can be used in console, WPF, WinForms, Avalonia, and other C# programs; the Avalonia control is responsible for displaying logs in real time on the UI, suitable for desktop application debugging and tool-based clients.

## NuGet Packages

| Package | Description | Use Case |
| --- | --- | --- |
| `CodeWF.Log.Core` | Core logging library, only depends on .NET. | Console, WPF, WinForms, Avalonia, etc. C# programs. |
| `CodeWF.LogViewer.Avalonia` | Avalonia UI log display control. | Embedded log window in Avalonia UI applications. |

## Installation

```powershell
Install-Package CodeWF.Log.Core
Install-Package CodeWF.LogViewer.Avalonia
```

## Basic Usage

```csharp
Logger.Debug("Debug log");
Logger.Info("Info log");
Logger.Warn("Warning log");
Logger.Error("Error log");
Logger.Fatal("Fatal error log");
```

When using file logging in a console application, it is recommended to handle the file channel on startup and exit:

```csharp
Logger.RecordToFile();

// Flush buffer on program exit
await Logger.FlushAsync();
```

## Output Target Control

Each log method can control whether to output to UI, file, and console:

```csharp
Logger.Info(
    content: "Content written to file",
    uiContent: "User-friendly content for UI",
    log2UI: true,
    log2File: true,
    log2Console: true);
```

You can also use shortcut methods:

```csharp
Logger.InfoToFile("Write to file only");
Logger.LogToUI(LogType.Info, "Display UI only");
```

## Avalonia Control

Import the namespace and place the control in AXAML:

```xml
xmlns:log="https://codewf.com"
```

```xml
<log:LogView />
```

`LogView` internally starts file logging and consumes logs from the UI channel to display them on the interface. For high-frequency logging scenarios, the control uses batch processing and debounce refresh to avoid frequent UI redraws.

## Repository

- GitHub: [https://github.com/dotnet9/CodeWF.LogViewer](https://github.com/dotnet9/CodeWF.LogViewer)
- NuGet Core: [https://www.nuget.org/packages/CodeWF.Log.Core](https://www.nuget.org/packages/CodeWF.Log.Core)
- NuGet Avalonia: [https://www.nuget.org/packages/CodeWF.LogViewer.Avalonia](https://www.nuget.org/packages/CodeWF.LogViewer.Avalonia)