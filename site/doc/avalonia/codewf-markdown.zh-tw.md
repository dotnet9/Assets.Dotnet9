# CodeWF.Markdown

![CodeWF.Markdown](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-markdown.svg)

`CodeWF.Markdown` 是基於 Avalonia 12 的 Markdown 渲染控制項、排版主題和可執行範例。此儲存庫從 `CodeWF.AvaloniaControls` 中拆分出來，專注維護 MarkdownViewer、主題資源和相關測試。

## 套件線

| 套件 | 說明 |
| --- | --- |
| `CodeWF.Markdown` | 完整 MarkdownViewer，支援常見 Markdown 元素、程式碼高亮、圖片預覽、SVG/圖片、數學渲染擴充、多語言資源和增量渲染。 |
| `CodeWF.Markdown.Themes` | 預設控制項範本和多套排版主題。 |

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
- 需要支援圖片、SVG、程式碼高亮、多語言資源和增量渲染。
- 希望用範例專案驗證不同 Markdown 內容在桌面端的表現。

## 建置

```powershell
dotnet restore CodeWF.Markdown.slnx
dotnet build CodeWF.Markdown.slnx --no-restore
```

## 儲存庫

- GitHub：[https://github.com/dotnet9/CodeWF.Markdown](https://github.com/dotnet9/CodeWF.Markdown)
- NuGet：[https://www.nuget.org/packages/CodeWF.Markdown](https://www.nuget.org/packages/CodeWF.Markdown)