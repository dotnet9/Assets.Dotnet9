# CodeWF.Markdown

![CodeWF.Markdown](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-markdown.svg)

`CodeWF.Markdown` 是基于 Avalonia 12 的 Markdown 渲染控件、排版主题和可运行示例。该仓库从 `CodeWF.AvaloniaControls` 中拆分出来，专注维护 MarkdownViewer、主题资源和相关测试。

## 包线

| 包 | 说明 |
| --- | --- |
| `CodeWF.Markdown` | 完整 MarkdownViewer，支持常见 Markdown 元素、代码高亮、图片预览、SVG/图片、数学渲染扩展、多语言资源和增量渲染。 |
| `CodeWF.Markdown.Themes` | 默认控件模板和多套排版主题。 |

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
- 需要支持图片、SVG、代码高亮、多语言资源和增量渲染。
- 希望用示例工程验证不同 Markdown 内容在桌面端的表现。

## 构建

```powershell
dotnet restore CodeWF.Markdown.slnx
dotnet build CodeWF.Markdown.slnx --no-restore
```

## 仓库

- GitHub：[https://github.com/dotnet9/CodeWF.Markdown](https://github.com/dotnet9/CodeWF.Markdown)
- NuGet：[https://www.nuget.org/packages/CodeWF.Markdown](https://www.nuget.org/packages/CodeWF.Markdown)
