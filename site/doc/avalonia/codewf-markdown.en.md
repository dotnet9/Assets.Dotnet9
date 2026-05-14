# CodeWF.Markdown

![CodeWF.Markdown](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-markdown.svg)

`CodeWF.Markdown` is an Avalonia 12-based Markdown rendering control, typography themes, and runnable samples. This repository is split from `CodeWF.AvaloniaControls` and focuses on maintaining MarkdownViewer, theme resources, and related tests.

## Packages

| Package | Description |
| --- | --- |
| `CodeWF.Markdown` | Full MarkdownViewer, supports common Markdown elements, code highlighting, image preview, SVG/images, math rendering extensions, multilingual resources, and incremental rendering. |
| `CodeWF.Markdown.Themes` | Default control templates and multiple typography themes. |

## Installation

```powershell
Install-Package CodeWF.Markdown
Install-Package CodeWF.Markdown.Themes
```

## Usage

In `App.axaml`, import the theme package:

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

Use `MarkdownViewer` in a page:

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

## Repository Structure

```text
src/CodeWF.Markdown          MarkdownViewer class library
src/CodeWF.Markdown.Themes   Control templates and typography themes
src/CodeWF.Markdown.Sample   Sample project
tests/CodeWF.Markdown.Tests  Rendering and diff service tests
```

## Ideal For

- Avalonia applications that need to render Markdown content directly.
- Need unified typography themes for documentation, changelogs, AI responses, or help centers.
- Need support for images, SVG, code highlighting, multilingual resources, and incremental rendering.
- Want to use sample projects to verify the rendering of different Markdown content on the desktop.

## Build

```powershell
dotnet restore CodeWF.Markdown.slnx
dotnet build CodeWF.Markdown.slnx --no-restore
```

## Repository

- GitHub：[https://github.com/dotnet9/CodeWF.Markdown](https://github.com/dotnet9/CodeWF.Markdown)
- NuGet：[https://www.nuget.org/packages/CodeWF.Markdown](https://www.nuget.org/packages/CodeWF.Markdown)