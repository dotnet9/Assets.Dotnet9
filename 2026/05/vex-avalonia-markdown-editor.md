![](https://img1.dotnet9.com/2026/05/vex-avalonia-cover.svg)

这次介绍一个新的桌面写作工具：**Vex（维刻）**。

Vex 是基于 **.NET 10 + Avalonia 12** 构建的免费开源跨平台 Markdown 编辑器。它不做大而全的知识库，也不复刻成熟商业编辑器，当前主线很清楚：**Markdown 源码编辑、实时预览、文件管理、导出，以及复制到内容平台**。

项目仓库：[https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)

Release v1.0.0：[https://github.com/dotnet9/Vex/releases/tag/v1.0.0](https://github.com/dotnet9/Vex/releases/tag/v1.0.0)

Vex 的 Slogan 是：**极简之力，妙笔成章。**

先看编辑体验。下面这段 GIF 用 Vex 打开本文副本，左侧继续写 Markdown，右侧实时预览；标题、引用、列表、代码块和状态栏统计都会跟着变化。

![](https://img1.dotnet9.com/2026/05/vex-edit-vex-article-live.gif)

写完以后，Vex 可以把 Markdown 复制成适合微信公众号、知乎、稀土掘金编辑器粘贴的富 HTML。下面是从文件菜单选择“复制到公众号”，再粘贴到公众号图文编辑器的过程。浏览器地址栏做了遮挡处理。

![](https://img1.dotnet9.com/2026/05/vex-copy-to-wechat-editor.gif)

## 不是 Typora

Vex 确实参考过 Typora 的简洁菜单、专注写作和 Markdown 文件优先，但它不是 Typora 复刻版。

Typora 的所见即所得编辑体验非常成熟，而把 Markdown 源码和最终排版无缝合在同一个编辑面里，本质上要处理光标映射、选区、撤销重做、输入法、表格、图片、代码块和跨平台文本行为。Vex 当前不走这条路线。

更现实的目标是：做一个免费开源的 **.NET + Avalonia 跨平台 Markdown 桌面编辑器**，把“写、看、查、导出、复制到发布平台”这条链路先做顺。

## v1.0.0

`v1.0.0` 发布于 2026 年 5 月 24 日，是 Vex 的首个稳定版本。这个版本已经覆盖日常写作的基本闭环：

- Markdown 源码编辑、实时预览、大纲导航、文档统计。
- 智能列表续写、源码模式、编辑区当前行高亮。
- 新建、打开、保存、另存为、打开文件夹、最近文档、拖拽打开、启动参数打开。
- 文件重命名、删除、打开所在位置、外部变更检测与重载。
- 查找替换、大小写/整词/正则匹配、命中计数和大文档防抖扫描。
- HTML、PNG、图像型 PDF、Word `.docx`、打印预览和复制到内容平台。
- 本地相对图、`data:image`、HTTP(S)、SVG/GIF/WebP 转 PNG、任务列表、排版主题化导出、PDF 页眉页脚；PNG/PDF/Word 复用 `CodeWF.Markdown.MarkdownDocumentExporter`，PDF 和 Word 会嵌入图片资源，离线分享后仍可查看。
- 多主题、多排版主题、紧凑布局、暗色模式细节优化。
- 简体中文、繁体中文、英语、日语四套 UI 和帮助文档。
- 多 RID 发布、压缩包、SHA256、release manifest，以及可选 Windows MSIX 打包。

当前主界面如下：左侧是文件列表和大纲，中央是 Markdown 源码编辑区，右侧是 Avalonia 原生渲染的预览区，底部状态栏显示保存状态、编码、行列号、词数和字符数。

![](https://img1.dotnet9.com/2026/05/vex-main-window.png)

## 写作与预览

Vex 的工作区是三栏结构：

- 左侧：文件列表与大纲导航。
- 中间：基于 AvaloniaEdit 的 Markdown 编辑区。
- 右侧：基于 CodeWF.Markdown 的预览区。

它没有把 Markdown 预览交给 WebView，而是沿用 CodeWF.Markdown 的 Avalonia 控件渲染路线。主题、字体、图片、表格、代码块、任务列表、SVG 和导出链路都留在桌面控件体系里处理。

长文档最需要的是大纲。Vex 会从标题结构里提取导航入口，切到“大纲”后可以直接跳转到对应段落。

![](https://img1.dotnet9.com/2026/05/vex-outline-vex-article.gif)

只想专注源码时，可以从视图菜单切换“源码模式”。它会临时收起侧栏和预览区，退出时恢复原来的布局。

![](https://img1.dotnet9.com/2026/05/vex-source-mode-vex-article.gif)

视图菜单还补了“刷新预览”，快捷键是 `F5`。它会强制刷新预览绑定，并给远程图片 URL 加版本查询参数，适合封面或远程配图刚更新但预览仍命中旧缓存的情况。

![](https://img1.dotnet9.com/2026/05/vex-refresh-preview.gif)

## 文件与发布

文件菜单放的是写作工具最常用的动作：新建、打开、打开文件夹、快速打开、最近文档、按编码重开、复制到内容平台、保存、另存为、属性、导出、打印和关闭。

![](https://img1.dotnet9.com/2026/05/vex-file-menu.png)

动态看更直观。文件菜单现在会按内容完整展开，不再需要在菜单里二次滚动；打开、保存、最近文件、编码重开、复制到内容平台、导出 HTML/PNG/PDF、打印都在同一条工作流里。

![](https://img1.dotnet9.com/2026/05/vex-file-export-menu.gif)

几个细节比较实用：

- 打开文件夹后，左侧文档列表可以在当前目录的 Markdown 文件之间切换。
- 最近文档减少重复定位文件的成本。
- 重新按编码打开保留 UTF-8、UTF-8 BOM、GB18030、Big5 等入口。
- 复制到公众号、知乎、稀土掘金把“发布到哪里”也放进编辑流程；Vex 会把当前 Markdown、排版主题和目标平台交给 `CodeWF.Markdown.MarkdownHtmlClipboardExtensions`，剪贴板会写入 `text/html`、macOS `public.html` 和 Windows 原生 `HTML Format`，并把当前排版主题转换为 inline style。
- 导出菜单支持 HTML、PNG、图像型 PDF 和 Word `.docx`；PNG/PDF/Word 复用 `CodeWF.Markdown.MarkdownDocumentExporter`，本地相对图、`data:image`、HTTP(S)、SVG/GIF/WebP 转 PNG 等边界会随导出一起处理，PDF 和 Word 会嵌入图片资源。

对公众号、知乎、掘金作者来说，真正高频的不是“保存一个 HTML 文件”，而是写完后直接粘贴到网页编辑器，并尽量保留标题、段落、引用、列表、代码块、表格和链接样式。Vex 当前复用 `CodeWF.Markdown` 的自媒体复制公共 API 生成目标平台 inline HTML 和 UTF-8 CF_HTML 字节载荷，避免编辑器把 HTML 片段当明文显示。

## 查找替换

长文档不能只靠眼睛扫。Vex 的查找栏支持查找、替换、下一个、替换、全部替换、关闭，以及大小写、整词和正则匹配。

![](https://img1.dotnet9.com/2026/05/vex-find-replace.png)

下面继续用本文演示：搜索 `Markdown`，再打开替换栏准备替换成更具体的词。查找栏会显示当前命中位置和总命中数。

![](https://img1.dotnet9.com/2026/05/vex-find-replace-vex-article.gif)

大文档扫描做了防抖处理，替换路径也尽量使用 AvaloniaEdit 文档级 `Replace`，避免每次替换都重建整篇文本。

## 主题、排版和语言

外观入口放在帮助菜单下，当前包含主题色、排版主题、紧凑布局和语言设置。

![](https://img1.dotnet9.com/2026/05/vex-theme-language-menu.gif)

主题色控制应用外壳，排版主题控制 Markdown 正文。两者分开后，可以组合出不同工作环境：浅色外壳配简洁排版，或暗色外壳配更适合代码阅读的排版主题。

![](https://img1.dotnet9.com/2026/05/vex-theme-typography-language.gif)

多语言当前覆盖：

- 简体中文
- 繁体中文
- English
- 日本語

这部分由 `Lang.Avalonia.Json`、Semi.Avalonia、Ursa.Avalonia 以及 Vex 自己的帮助文档共同组成。菜单、状态栏、错误详情、快速开始、更新日志、鸣谢和关于窗口都在本地化范围内。

## 新手引导

Vex 也是 CodeWF.AvaloniaControls 新增 `Guide` 引导控件的落地项目。首次启动时显示一次，之后可从帮助菜单重新打开。

引导覆盖文件菜单、导出菜单、段落/格式/视图菜单、主题菜单、左侧文件/大纲区域、编辑区、预览区和状态栏。

![](https://img1.dotnet9.com/2026/05/codewf-guide-vex-onboarding-flow.gif)

最有价值的是菜单项引导。很多桌面软件入口藏在菜单或二级菜单里，普通遮罩控件只能高亮页面上已经存在的按钮；Vex 会在步骤切换时主动展开菜单，再定位里面的 `MenuItem`。

![](https://img1.dotnet9.com/2026/05/codewf-guide-vex-menuitem-guide.gif)

左侧“文件/大纲”是 `TabControl`，引导到不同页签时需要先切换页签，再刷新高亮位置。

![](https://img1.dotnet9.com/2026/05/codewf-guide-vex-tabitem-guide.gif)

关于窗口展示软件名称、中文名、版本、编译时间、作者、网站和品牌信息。

![](https://img1.dotnet9.com/2026/05/vex-about-window.png)

## 技术栈

Vex 当前主要技术栈：

- .NET 10
- Avalonia 12.0.3
- Prism.DryIoc.Avalonia
- ReactiveUI.Avalonia
- Semi.Avalonia
- Ursa.Avalonia
- AvaloniaEdit
- CodeWF.AvaloniaControls
- CodeWF.Markdown
- CodeWF.EventBus
- Lang.Avalonia.Json

目标框架是 `net10.0` 和 `net10.0-windows`，运行时标识覆盖：

- `win-x64`
- `linux-x64`
- `linux-arm64`
- `osx-x64`
- `osx-arm64`

Windows 发布路径启用 Native AOT，并把最低 Windows 支持平台版本设到 `6.1`；非 Windows RID 走 self-contained single-file 发布。`publish_vex_all.bat --package` 会生成压缩包、SHA256 文件和 release manifest。

Release 里还有一个兼容性调整：事件总线统一直接使用 `CodeWF.EventBus.EventBus.Default`，避开 DryIoc 事件总线注册路径，改善 Windows 7 启动兼容性。

## 构建与发布

本地开发：

```powershell
dotnet build Vex.slnx
```

生成多平台发布产物：

```powershell
.\publish_vex_all.bat --package
```

准备 Windows MSIX 布局或安装包：

```powershell
.\scripts\package_vex_msix.ps1 -RuntimeIdentifier win-x64 -PrepareOnly
```

## 后续方向

Vex 后续不会优先挑战所见即所得实时编辑，而是继续围绕“编辑预览 + 发布复制”补基础能力：

- 继续基于 `CodeWF.Markdown` 的 `MarkdownSocialCopyProfile` 扩展复制到微信公众号、知乎、稀土掘金及后续平台的 HTML 结构和样式兼容性。
- 增加公众号移动端预览效果。
- 改进本地图片、相对路径和复制时的资源提示。
- 优化大纲跳转、编辑区和预览区滚动联动。
- 增加更多排版主题，并允许保存自定义发布样式。
- 继续完善 Word 导出的复杂排版、自动更新和安装包发布流程。
- 继续打磨 Linux、macOS 下的字体、菜单、快捷键和打包体验。

## 小结

Vex 1.0.0 的重点不是复杂知识库，而是把 Markdown 桌面写作工具的基本体验打通：打开文件、写内容、看预览、按大纲跳转、查找替换、换主题、导出交付。

文件夹切换、最近文档、外部变更重载、编码选择、复制到内容平台、多语言帮助、首次启动引导、状态栏统计，这些单独看都不大，但组合起来会直接影响它能不能进入日常写作流程。

相关地址：

- Vex 仓库：[https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)
- Release v1.0.0：[https://github.com/dotnet9/Vex/releases/tag/v1.0.0](https://github.com/dotnet9/Vex/releases/tag/v1.0.0)
