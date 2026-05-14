# CodeWF.LogViewer

![CodeWF.LogViewer](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-logviewer.svg)

`CodeWF.LogViewer` は、軽量ログコアライブラリと Avalonia UI ログ表示コントロールを含みます。コアパッケージはコンソール、WPF、WinForms、Avalonia などの C# アプリで使用できます。Avalonia コントロールは、ログをリアルタイムで画面に表示する役割を担い、デスクトップアプリケーションのデバッグやツール型クライアントに適しています。

## パッケージ一覧

| パッケージ | 説明 | 適用シーン |
| --- | --- | --- |
| `CodeWF.Log.Core` | コアログライブラリ。.NET のみに依存。 | Console、WPF、WinForms、Avalonia などの C# アプリケーション。 |
| `CodeWF.LogViewer.Avalonia` | Avalonia UI ログ表示コントロール。 | Avalonia UI アプリケーション内にログウィンドウを埋め込む。 |

## インストール

```powershell
Install-Package CodeWF.Log.Core
Install-Package CodeWF.LogViewer.Avalonia
```

## 基本的な使い方

```csharp
Logger.Debug("调试日志");
Logger.Info("普通日志");
Logger.Warn("警告日志");
Logger.Error("错误日志");
Logger.Fatal("严重错误日志");
```

コンソールアプリでファイルログを使用する場合、起動時と終了時にファイルチャネルを処理することを推奨します：

```csharp
Logger.RecordToFile();

// 程序退出时刷新缓冲区
await Logger.FlushAsync();
```

## 出力先の制御

各ログメソッドでは、UI、ファイル、コンソールへの出力を制御できます：

```csharp
Logger.Info(
    content: "写入文件的内容",
    uiContent: "UI 显示的友好内容",
    log2UI: true,
    log2File: true,
    log2Console: true);
```

ショートカットメソッドも使用できます：

```csharp
Logger.InfoToFile("仅写入文件");
Logger.LogToUI(LogType.Info, "仅显示 UI");
```

## Avalonia コントロール

AXAML で名前空間を導入し、コントロールを配置します：

```xml
xmlns:log="https://codewf.com"
```

```xml
<log:LogView />
```

`LogView` は内部でファイルログ記録を開始し、UI チャネルからログを消費して画面に表示します。高頻度のログシナリオでは、コントロールはバッチ処理とデバウンスリフレッシュを使用し、UI の頻繁な再描画を回避します。

## リポジトリ

- GitHub：[https://github.com/dotnet9/CodeWF.LogViewer](https://github.com/dotnet9/CodeWF.LogViewer)
- NuGet Core：[https://www.nuget.org/packages/CodeWF.Log.Core](https://www.nuget.org/packages/CodeWF.Log.Core)
- NuGet Avalonia：[https://www.nuget.org/packages/CodeWF.LogViewer.Avalonia](https://www.nuget.org/packages/CodeWF.LogViewer.Avalonia)