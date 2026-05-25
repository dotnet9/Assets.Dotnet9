# Vex 维刻

![Vex](https://img1.dotnet9.com/2026/05/vex-avalonia-cover.svg)

`Vex` 是一个基于 .NET 10、Avalonia 12、Prism、Semi.Avalonia 和 Ursa.Avalonia 构建的跨平台 Markdown 桌面编辑器。它聚焦 Markdown 源码编辑、实时预览、文件管理、查找替换、导出交付和复制到内容平台，适合写技术文章、产品说明、项目文档和发布草稿。

项目仓库：[https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)

Release v1.1.0：[https://github.com/dotnet9/Vex/releases/tag/v1.1.0](https://github.com/dotnet9/Vex/releases/tag/v1.1.0)

![Vex 主窗口](https://img1.dotnet9.com/2026/05/vex-main-window.png)

## 项目定位

- 免费开源的 Markdown 桌面写作工具，当前不走所见即所得编辑路线，而是把源码编辑和原生预览链路做稳。
- 左侧提供文件列表和大纲，中央使用 AvaloniaEdit 编辑 Markdown，右侧使用 CodeWF.Markdown 渲染预览。
- 视图菜单支持侧栏、大纲、文档列表、源码模式、行号、状态栏、全屏和置顶等工作区控制。
- 文件菜单覆盖新建、打开、打开文件夹、最近文档、按编码重开、复制到公众号/知乎/稀土掘金、保存、另存为、导出、打印、属性、删除和关闭。
- 导出支持 HTML、PNG、图像型 PDF 和 Word `.docx`；PNG/PDF/Word 复用 `CodeWF.Markdown` 的 `MarkdownDocumentExporter`，本地相对图、`data:image`、HTTP(S)、SVG/GIF/WebP 等图片边界会随导出结果一起处理，PDF 和 Word 会嵌入图片资源，离线发送后仍可查看。
- 复制到公众号、知乎、稀土掘金会把当前 Markdown、排版主题和目标平台交给 `CodeWF.Markdown` 的 `MarkdownHtmlClipboardExtensions`，写入富 HTML 剪贴板格式并应用当前排版主题。
- 查找替换支持大小写、整词、正则、命中计数和长文档防抖扫描。
- 主题色、Markdown 排版主题、紧凑布局和语言切换都集中在帮助菜单下。
- 简体中文、繁体中文、英语和日语界面与帮助文档已覆盖主要入口。
- 首次启动引导会高亮文件、导出、段落、格式、视图、主题、侧栏、编辑区、预览区和状态栏等关键入口。
- 发布脚本覆盖多 RID 发布、压缩包、SHA256、release manifest，并可准备 Windows MSIX 布局。

## 主要功能

| 功能 | 说明 |
| --- | --- |
| Markdown 编辑 | 基于 AvaloniaEdit，支持智能换行、当前行高亮、源码模式、行号和常用格式插入。 |
| 原生预览 | 基于 CodeWF.Markdown，不依赖 WebView，支持标题、列表、表格、代码块、任务列表、本地图片、SVG 和 GIF。 |
| 文件工作流 | 支持新建、打开单文件、打开文件夹、最近文档、拖拽打开、保存、另存为、外部变更检测和重载。 |
| 大纲导航 | 从 Markdown 标题生成大纲，点击即可跳转到对应位置。 |
| 查找替换 | 支持大小写、整词、正则、命中计数、替换下一个和全部替换。 |
| 导出交付 | 支持 HTML、PNG、图像型 PDF、Word `.docx` 和打印预览，PNG/PDF/Word 复用 `CodeWF.Markdown` 公共导出 API，PDF/Word 会嵌入本地、`data:image`、HTTP(S)、SVG/GIF/WebP 图片，导出成功后可定位文件。 |
| 发布复制 | 复制到公众号、知乎、稀土掘金时调用 `CodeWF.Markdown` 公共富 HTML 剪贴板 API，Windows `HTML Format` 使用 UTF-8 CF_HTML 字节数据，并应用当前排版主题。 |
| 多语言 | 通过 Lang.Avalonia.Json 提供中文简体、中文繁体、英语和日语界面。 |
| 新手引导 | 基于 CodeWF.AvaloniaControls Guide 控件，可以定位菜单项、TabItem、编辑区和预览区。 |
| 发布打包 | 支持 Windows、Linux、macOS 多 RID 发布，并提供压缩包和可选 MSIX 打包脚本。 |

## 运行演示

Vex 的典型工作区是文件/大纲、源码编辑和实时预览三栏布局。

![编辑和实时预览](https://img1.dotnet9.com/2026/05/vex-edit-vex-article-live.gif)

大纲适合在长文档里快速跳转。

![大纲导航](https://img1.dotnet9.com/2026/05/vex-outline-vex-article.gif)

源码模式可以临时收起侧栏和预览区，专注当前 Markdown 文本。

![源码模式](https://img1.dotnet9.com/2026/05/vex-source-mode-vex-article.gif)

文件菜单集中放置打开、保存、导出和打印等高频写作动作。

![文件菜单与导出](https://img1.dotnet9.com/2026/05/vex-file-export-menu.gif)

查找替换栏支持大小写、整词和正则等常用选项。

![查找替换](https://img1.dotnet9.com/2026/05/vex-find-replace-vex-article.gif)

主题色、排版主题和语言可以组合切换。

![主题和语言](https://img1.dotnet9.com/2026/05/vex-theme-typography-language.gif)

首次启动引导会覆盖菜单和工作区中的真实目标。

![新手引导](https://img1.dotnet9.com/2026/05/codewf-guide-vex-onboarding-flow.gif)

## 工程组织

```text
src/
  Vex/                         桌面应用、Shell、Workspace、Help、服务和 ViewModel
  Vex.Controls/                Vex 自定义控件
  Vex.Controls.Themes/         Vex 控件主题资源
docs/
  快速开始、更新日志、鸣谢和需求文档
scripts/
  压测、发布打包和 MSIX 打包脚本
```

应用层使用 Prism 做模块组合，跨模块业务消息通过 CodeWF.EventBus 传递；Markdown 预览、导出图片加载和自媒体富 HTML 剪贴板复用 CodeWF.Markdown，避免把核心写作流程绑定到 WebView。

## 技术栈

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

## 快速开始

环境要求：

- .NET 10 SDK

```powershell
git clone https://github.com/dotnet9/Vex.git
cd Vex
dotnet restore Vex.slnx
dotnet build Vex.slnx
dotnet run --project src/Vex/Vex.csproj
```

生成发布产物：

```powershell
.\publish_vex_all.bat --package
```

## 仓库与发布

- 仓库地址：[https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)
- 发布地址：[https://github.com/dotnet9/Vex/releases/tag/v1.1.0](https://github.com/dotnet9/Vex/releases/tag/v1.1.0)
