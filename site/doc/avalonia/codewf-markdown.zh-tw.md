# CodeWF.Markdown

![CodeWF.Markdown](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-markdown.svg)

`CodeWF.Markdown` 是基於 Avalonia 12 的 Markdown 渲染控制項、排版主題和可執行範例。此儲存庫從 `CodeWF.AvaloniaControls` 中拆分出來，專注維護 MarkdownViewer、主題資源和相關測試。

## 套件線

| 套件 | 說明 |
| --- | --- |
| `CodeWF.Markdown` | 完整 MarkdownViewer，支援常見 Markdown 元素、程式碼高亮、圖片預覽、SVG/圖片、數學渲染擴充、多語言資源、增量渲染、匯出圖片載入與富 HTML 剪貼簿輔助能力。 |
| `CodeWF.Markdown.Themes` | 預設控制項範本和多套排版主題。 |

## 宿主應用輔助能力

- `MarkdownDocumentExporter` 和 `ExportKind` 提供 PNG、可選取文字 PDF、Word `.docx` 一站式匯出 API，可從 Markdown 字串、Markdown 檔案或 `MarkdownExportDocument` 匯出；PDF 正文可選取複製，並複用圖片載入與柵格化能力嵌入本機、`data:image`、HTTP(S)、SVG/GIF/WebP 圖片。
- `MarkdownHtmlClipboardExtensions`、`CopyKind` 和 `MarkdownSocialCopyProfiles` 提供微信公眾號、知乎、稀土掘金的富 HTML 複製能力；宿主應用只需傳 Markdown、目前排版主題和目標平台，公共庫會生成 inline HTML 並寫入 `text/html`、macOS `public.html` 和 Windows 原生 `HTML Format`。
- 需要支援新平台時，可以擴展 `MarkdownSocialCopyProfile`；需要自訂排版主題時，可以傳 `MarkdownExportStyle` 或複用應用註冊的主題資源。

## 安裝

```powershell
Install-Package CodeWF.Markdown
Install-Package CodeWF.Markdown.Themes
```

## 使用方式

在 `App.axaml` 引入主題包：

```xml
<Application
    xmlns="https://github.com/avaloniaui"
    xmlns:markdown="https://codewf.com">
    <Application.Styles>
        <FluentTheme />
        <markdown:MarkdownThemes TypographyTheme="Simple" />
    </Application.Styles>
</Application>
```

在頁面中使用 `MarkdownViewer`：

```xml
<UserControl
    xmlns="https://github.com/avaloniaui"
    xmlns:md="https://codewf.com">
    <ScrollViewer
        HorizontalScrollBarVisibility="Disabled"
        VerticalScrollBarVisibility="Auto">
        <md:MarkdownViewer Markdown="{Binding Markdown}" />
    </ScrollViewer>
</UserControl>
```

## 儲存庫結構

```text
src/CodeWF.Markdown          MarkdownViewer 類別庫
src/CodeWF.Markdown.Themes   控制項範本和排版主題
src/CodeWF.Markdown.Sample   範例專案
tests/CodeWF.Markdown.Tests  渲染和差異服務測試
```

## 適合關注

- Avalonia 應用需要直接渲染 Markdown 內容。
- 需要為文件、更新日誌、AI 回覆或幫助中心提供統一排版主題。
- 需要支援圖片、SVG、程式碼高亮、多語言資源、增量渲染、匯出圖片嵌入和網頁編輯器富 HTML 貼上。
- 希望用範例專案驗證不同 Markdown 內容在桌面端的表現。

## 建置

```powershell
dotnet restore CodeWF.Markdown.slnx
dotnet build CodeWF.Markdown.slnx --no-restore
```

## 儲存庫

- GitHub：[https://github.com/dotnet9/CodeWF.Markdown](https://github.com/dotnet9/CodeWF.Markdown)
- NuGet：[https://www.nuget.org/packages/CodeWF.Markdown](https://www.nuget.org/packages/CodeWF.Markdown)
