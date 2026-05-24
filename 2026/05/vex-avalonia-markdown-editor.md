![](https://img1.dotnet9.com/2026/05/vex-avalonia-cover.svg)

今天介绍一个新的桌面写作工具：**Vex（维刻）**。

Vex 是基于 **.NET 10 + Avalonia 12** 构建的跨平台 Markdown 编辑器，定位不是“大而全的笔记系统”，而是围绕日常写作、实时预览、文件管理、查找替换和文档导出，把 Markdown 编辑这条主线做顺。

项目仓库：[https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)

Release v1.0.0：[https://github.com/dotnet9/Vex/releases/tag/v1.0.0](https://github.com/dotnet9/Vex/releases/tag/v1.0.0)

Vex 的 Slogan 是：**极简之力，妙笔成章。**

## v1.0.0 首个稳定版本

`v1.0.0` 发布于 2026 年 5 月 24 日，是 Vex 的首个稳定版本。这个版本已经能覆盖日常 Markdown 写作里的主要闭环：

- Markdown 编辑、实时预览、大纲导航、文档统计。
- 智能列表续写，以及更适合专注写作的源码模式。
- 新建、打开、保存、另存为、打开文件夹、最近文档、拖拽打开、启动参数打开。
- 文件重命名、删除、打开文件位置，以及外部文件变更检测与重载。
- 查找替换、大小写/整词/正则匹配、当前/总命中计数和替换操作。
- HTML、PNG、图像型 PDF、复制 HTML、打印预览等导出和分享能力。
- 多主题、多排版主题、紧凑布局、暗色模式、本地化帮助和更新日志。
- 简体中文、繁体中文、英语、日语四套 UI 资源。
- 多 RID 发布、压缩包、SHA256 文件、发布 manifest，以及可选 Windows MSIX 打包。

下面这张图是当前 v1.0.0 的主界面。左侧是文档列表和大纲，中央是 Markdown 源码编辑区，右侧是 Avalonia 原生渲染的预览区，底部状态栏显示保存状态、编码、缩放、行列号、词数和字符数。

![](https://img1.dotnet9.com/2026/05/vex-main-window.png)

## 写作界面：编辑与预览并排

Vex 的主工作区采用三栏结构：

- 左侧：文件列表与大纲导航。
- 中间：基于 AvaloniaEdit 的 Markdown 编辑区。
- 右侧：基于 CodeWF.Markdown 的实时预览区。

这套结构很适合写长文档。编辑区保留 Markdown 的直接控制感，预览区负责即时确认排版效果；大纲区则从标题结构里提取导航入口，写到后面不用靠滚动条反复定位。

Vex 没有把 Markdown 预览交给 WebView，而是继续沿用 CodeWF.Markdown 的 Avalonia 控件渲染路线。这样主题、字体、图片、表格、代码块、任务列表、SVG 栅格化和导出链路都能留在桌面控件体系里处理。

## 文件菜单：把写作闭环补完整

文件菜单里放的是写作工具最常用的一组动作：新建、打开、打开文件夹、快速打开、最近文档、重新按编码打开、复制到公众号/知乎/稀土掘金、保存、另存为、属性、导出、打印和关闭。

![](https://img1.dotnet9.com/2026/05/vex-file-menu.png)

这里有几个细节比较实用：

- 打开文件夹后，左侧文档列表可以在当前目录里的 Markdown 文件之间切换。
- 最近文档可以降低重复定位文件的成本。
- 重新按编码打开保留了 UTF-8、UTF-8 BOM、GB18030、Big5 等入口，对中文历史文档比较友好。
- 复制到公众号、知乎、稀土掘金，把“写完以后发布到哪里”也放进了编辑流程。
- 导出菜单目前用于生成 HTML、PNG 和 PDF；Word 入口已经预留，后续能力成熟后再补齐。

## 查找替换：长文档不能只靠眼睛扫

Markdown 文档一长，查找替换就会成为高频能力。Vex 的查找栏支持查找、替换、下一个、替换、全部替换、关闭，以及大小写、整词和正则匹配选项。

![](https://img1.dotnet9.com/2026/05/vex-find-replace.png)

发布说明里提到，查找替换对大文档扫描做了防抖处理。这类细节看起来不显眼，但对桌面编辑器很关键：输入搜索词时不应该每敲一个字符就把界面拖慢，尤其是文档里有大量代码块、表格或长段落时。

## 主题、排版和多语言

Vex 的主题入口放在帮助菜单下，当前包含主题色、排版主题、紧凑布局和国际化设置。

![](https://img1.dotnet9.com/2026/05/vex-theme-language-menu.png)

主题色解决的是应用外壳的视觉风格，排版主题解决的是 Markdown 正文的阅读风格。两者分开后，用户可以在不同的工作环境里组合出适合自己的界面：例如浅色外壳配简洁排版，或者暗色外壳配更适合代码阅读的排版主题。

多语言当前覆盖：

- 简体中文
- 繁体中文
- English
- 日本語

这部分由 `Lang.Avalonia.Json`、Semi.Avalonia 和 Ursa 相关本地化资源协同完成。对桌面工具来说，多语言不只是菜单翻译，还包括帮助文档、更新日志、错误详情和关于窗口这些边角位置。

## 新手引导与关于窗口

Vex 也是 CodeWF.AvaloniaControls 新增 `Guide` 引导控件的落地项目。首次启动时会显示一次引导，之后可以从帮助菜单重新打开。引导会覆盖文件菜单、导出菜单、段落/格式/视图菜单、主题菜单、左侧文件/大纲区域、编辑区、预览区和状态栏。

关于窗口则展示软件名称、中文名、版本、编译时间、作者、网站和品牌信息。

![](https://img1.dotnet9.com/2026/05/vex-about-window.png)

从 v1.0.0 开始，Vex 也把帮助文档、更新日志和鸣谢做成了本地化文档窗口，而不是只在 README 里放文字。用户安装后可以直接从软件内部查看这些信息。

## 技术栈

Vex 当前的主要技术栈包括：

- .NET 10
- Avalonia 12.0.3
- Prism.DryIoc.Avalonia
- Semi.Avalonia
- Ursa.Avalonia
- AvaloniaEdit
- CodeWF.AvaloniaControls
- CodeWF.Markdown
- CodeWF.EventBus
- Lang.Avalonia.Json

项目目标框架是 `net10.0` 和 `net10.0-windows`，运行时标识覆盖：

- `win-x64`
- `linux-x64`
- `linux-arm64`
- `osx-x64`
- `osx-arm64`

Windows 发布路径启用了 Native AOT，并把最低 Windows 支持平台版本设到 `6.1`；非 Windows RID 则走 self-contained single-file 发布。`publish_vex_all.bat --package` 会在发布完成后生成压缩包、SHA256 文件和 release manifest，方便后续上传 GitHub Release。

## 构建与发布验证

本地开发时可以先执行：

```powershell
dotnet build Vex.slnx
```

需要生成多平台发布产物时执行：

```powershell
.\publish_vex_all.bat --package
```

如果要准备 Windows MSIX 布局或安装包，可以使用仓库里的 `scripts/package_vex_msix.ps1`。

## 小结

Vex 1.0.0 的重点不是做一个复杂的知识库，而是先把 Markdown 桌面写作工具的基本体验打通：打开文件、写内容、看预览、按大纲跳转、查找替换、换主题、导出交付。

这类工具的价值很大一部分在细节里：文件夹文档切换、最近文档、外部变更重载、编码选择、复制到内容平台、多语言帮助、首次启动引导、状态栏统计，都不是单独看很大的功能，但组合起来会直接影响用户愿不愿意把它放进日常写作流程。

## 仓库与发布地址

- Vex 仓库：[dotnet9/Vex](https://github.com/dotnet9/Vex)
- Release v1.0.0：[Vex 1.0.0 - First Stable Release](https://github.com/dotnet9/Vex/releases/tag/v1.0.0)
