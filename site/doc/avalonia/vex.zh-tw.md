# Vex 維刻

![Vex](https://img1.dotnet9.com/2026/05/vex-avalonia-cover.svg)

`Vex` 是一個基於 .NET 10、Avalonia 12、Prism、Semi.Avalonia 和 Ursa.Avalonia 建構的跨平台 Markdown 桌面編輯器。它聚焦 Markdown 原始碼編輯、即時預覽、檔案管理、查找替換、匯出交付和複製到內容平台。

專案倉庫：[https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)

Release v1.0.0：[https://github.com/dotnet9/Vex/releases/tag/v1.0.0](https://github.com/dotnet9/Vex/releases/tag/v1.0.0)

![Vex 主視窗](https://img1.dotnet9.com/2026/05/vex-main-window.png)

## 專案定位

- 免費開源的 Markdown 桌面寫作工具，先把原始碼編輯與原生預覽鏈路做穩。
- 左側提供檔案列表和大綱，中央使用 AvaloniaEdit 編輯 Markdown，右側使用 CodeWF.Markdown 渲染預覽。
- 檢視選單支援側欄、大綱、文件列表、原始碼模式、行號、狀態列、全螢幕和置頂。
- 檔案選單覆蓋新建、開啟、開啟資料夾、最近文件、按編碼重開、複製到公眾號/知乎/稀土掘金、儲存、匯出、列印、屬性、刪除和關閉。
- 匯出支援 HTML、PNG、圖像型 PDF 和 Word `.docx`，並處理本機相對圖片、`data:image`、HTTP(S)、SVG/GIF/WebP 等圖片邊界；PDF 和 Word 會嵌入圖片資源，離線分享後仍可查看。
- 複製到公眾號、知乎與稀土掘金會寫入富 HTML 剪貼簿格式，包括 `text/html`、macOS `public.html` 和 Windows 原生 `HTML Format`，並把目前排版主題與緊湊版面轉為 inline style。
- 查找替換支援大小寫、整詞、正則、命中計數和長文件防抖掃描。
- 主題色、Markdown 排版主題、緊湊布局和語言切換集中在幫助選單下。
- 簡體中文、繁體中文、英文和日文介面與幫助文件已覆蓋主要入口。

## 主要功能

| 功能 | 說明 |
| --- | --- |
| Markdown 編輯 | 基於 AvaloniaEdit，支援智慧換行、目前行高亮、原始碼模式、行號和常用格式插入。 |
| 原生預覽 | 基於 CodeWF.Markdown，不依賴 WebView，支援標題、列表、表格、程式碼區塊、任務列表、本機圖片、SVG 和 GIF。 |
| 檔案工作流 | 支援新建、開啟單檔、開啟資料夾、最近文件、拖放開啟、儲存、另存為、外部變更偵測和重載。 |
| 大綱導航 | 從 Markdown 標題生成大綱，點擊即可跳轉到對應位置。 |
| 查找替換 | 支援大小寫、整詞、正則、命中計數、替換下一個和全部替換。 |
| 匯出交付 | 支援 HTML、PNG、圖像型 PDF、Word `.docx` 和列印預覽；PDF/Word 會嵌入本機、`data:image`、HTTP(S)、SVG/GIF/WebP 圖片。 |
| 發布複製 | 複製到公眾號、知乎、稀土掘金時寫入富 HTML 剪貼簿載荷，Windows `HTML Format` 使用 UTF-8 CF_HTML 位元組資料，並套用目前排版主題。 |
| 多語言 | 透過 Lang.Avalonia.Json 提供簡體中文、繁體中文、英文和日文介面。 |
| 新手引導 | Guide 步驟可以定位選單項、TabItem、編輯區和預覽區。 |
| 發布打包 | 支援 Windows、Linux、macOS 多 RID 發布，並提供壓縮包與可選 MSIX 打包腳本。 |

## 執行示範

![編輯和即時預覽](https://img1.dotnet9.com/2026/05/vex-edit-vex-article-live.gif)

![大綱導航](https://img1.dotnet9.com/2026/05/vex-outline-vex-article.gif)

![原始碼模式](https://img1.dotnet9.com/2026/05/vex-source-mode-vex-article.gif)

![檔案選單與匯出](https://img1.dotnet9.com/2026/05/vex-file-export-menu.gif)

![查找替換](https://img1.dotnet9.com/2026/05/vex-find-replace-vex-article.gif)

![主題和語言](https://img1.dotnet9.com/2026/05/vex-theme-typography-language.gif)

![新手引導](https://img1.dotnet9.com/2026/05/codewf-guide-vex-onboarding-flow.gif)

## 技術棧

- .NET 10
- Avalonia 12
- Prism.DryIoc.Avalonia
- ReactiveUI.Avalonia
- Semi.Avalonia
- Ursa.Avalonia
- AvaloniaEdit
- CodeWF.Markdown
- CodeWF.AvaloniaControls
- CodeWF.EventBus
- Lang.Avalonia.Json

## 快速開始

環境要求：

- .NET 10 SDK

```powershell
git clone https://github.com/dotnet9/Vex.git
cd Vex
dotnet restore Vex.slnx
dotnet build Vex.slnx
dotnet run --project src/Vex/Vex.csproj
```

生成發布產物：

```powershell
.\publish_vex_all.bat --package
```

## 倉庫與發布

- 倉庫地址：[https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)
- 發布地址：[https://github.com/dotnet9/Vex/releases/tag/v1.0.0](https://github.com/dotnet9/Vex/releases/tag/v1.0.0)
