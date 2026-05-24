# 介紹

這裡集中整理 Dotnet9 / CodeWF 目前重點維護的開源專案，文件只保留網站、C# 通用庫和 Avalonia 相關專案，方便訪問者快速理解每個倉庫的定位、安裝方式與適用場景。

更多開源專案可訪問：[https://github.com/dotnet9/](https://github.com/dotnet9/)

## 網站

| 專案 | 定位 |
| --- | --- |
| [CodeWF](https://github.com/dotnet9/CodeWF) | `dotnet9.com` / `codewf.com` 的網站原始碼倉庫，基於 ASP.NET Core Razor Pages。 |
| [Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9) | 網站文章、文件、工具資料、圖片與站點設定的檔案型內容倉庫。 |

## C# 通用

| 專案 | 定位 |
| --- | --- |
| [CodeWF.EventBus.Socket](https://github.com/dotnet9/CodeWF.EventBus.Socket) | 基於 TCP Socket 的輕量級進程間事件匯流排，支援發佈訂閱和 Query/Response。 |
| [CodeWF.EventBus](https://github.com/dotnet9/CodeWF.EventBus) | 進程內事件匯流排，適合在桌面、Web、控制檯專案中做模組解耦和簡單 CQRS。 |
| [CodeWF.NetWeaver](https://github.com/dotnet9/CodeWF.NetWeaver) | 網路資料包序列化核心庫，並提供基於它的 TCP/UDP 封裝層 `CodeWF.NetWrapper`。 |

## Avalonia

| 專案 | 定位 |
| --- | --- |
| [CodeWF.AvaloniaControls](https://github.com/dotnet9/CodeWF.AvaloniaControls) | 面向 Avalonia 12 的通用控制項、Guide 引導控制項與主題包。 |
| [CodeWF.LogViewer](https://github.com/dotnet9/CodeWF.LogViewer) | 輕量日誌核心庫與 Avalonia 日誌檢視控制項。 |
| [Lang.Avalonia](https://github.com/dotnet9/Lang.Avalonia) | Avalonia UI 插件化多語言庫，支援 JSON、XML、RESX 和強型別 Key 生成。 |
| [CodeWF.Markdown](https://github.com/dotnet9/CodeWF.Markdown) | Avalonia Markdown 渲染控制項、排版主題和範例專案。 |
| [CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox) | 基於 Avalonia + Prism 的模組化跨平臺桌面工具箱。 |
