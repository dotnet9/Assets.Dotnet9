# CodeWF.Markdown

![CodeWF.Markdown](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-markdown.svg)

`CodeWF.Markdown` 是基于 Avalonia 12 的 Markdown 渲染控件、排版主题和可运行示例。该仓库从 `CodeWF.AvaloniaControls` 中拆分出来，专注维护 MarkdownViewer、主题资源和相关测试。

## 包线

| 包 | 说明 |
| --- | --- |
| `CodeWF.Markdown` | 完整 MarkdownViewer，支持常见 Markdown 元素、代码高亮、图片预览、SVG/图片、数学渲染扩展、多语言资源、增量渲染、导出图片加载与富 HTML 剪贴板辅助能力。 |
| `CodeWF.Markdown.Themes` | 默认控件模板和多套排版主题。 |

## 宿主应用辅助能力

- `MarkdownDocumentExporter` 和 `ExportKind` 提供 PNG、图像型 PDF、Word `.docx` 一站式导出 API，可从 Markdown 字符串、Markdown 文件或 `MarkdownExportDocument` 导出，并复用图片加载与栅格化能力嵌入本地、`data:image`、HTTP(S)、SVG/GIF/WebP 图片。
- `MarkdownHtmlClipboardExtensions`、`CopyKind` 和 `MarkdownSocialCopyProfiles` 提供微信公众号、知乎、稀土掘金的富 HTML 复制能力；宿主应用只需传 Markdown、当前排版主题和目标平台，公共库会生成 inline HTML 并写入 `text/html`、macOS `public.html` 和 Windows 原生 `HTML Format`。
- 需要支持新平台时，可以扩展 `MarkdownSocialCopyProfile`；需要自定义排版主题时，可以传 `MarkdownExportStyle` 或复用应用注册的主题资源。

## 安装

```powershell
Install-Package CodeWF.Markdown
Install-Package CodeWF.Markdown.Themes
```

## 使用方式

在 `App.axaml` 引入主题包：

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

在页面中使用 `MarkdownViewer`：

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

## 仓库结构

```text
src/CodeWF.Markdown          MarkdownViewer 类库
src/CodeWF.Markdown.Themes   控件模板和排版主题
src/CodeWF.Markdown.Sample   示例工程
tests/CodeWF.Markdown.Tests  渲染和差异服务测试
```

## 适合关注

- Avalonia 应用需要直接渲染 Markdown 内容。
- 需要为文档、更新日志、AI 回复或帮助中心提供统一排版主题。
- 需要支持图片、SVG、代码高亮、多语言资源、增量渲染、导出图片嵌入和网页编辑器富 HTML 粘贴。
- 希望用示例工程验证不同 Markdown 内容在桌面端的表现。

## 构建

```powershell
dotnet restore CodeWF.Markdown.slnx
dotnet build CodeWF.Markdown.slnx --no-restore
```

## 仓库

- GitHub：[https://github.com/dotnet9/CodeWF.Markdown](https://github.com/dotnet9/CodeWF.Markdown)
- NuGet：[https://www.nuget.org/packages/CodeWF.Markdown](https://www.nuget.org/packages/CodeWF.Markdown)
