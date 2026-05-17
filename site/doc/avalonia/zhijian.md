# 枝见 Zhijian

![枝见 Zhijian](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian.svg)

`Zhijian` 是一个基于 C#、Avalonia 和 AtomUI 的本地 Markdown-first 脑图编辑器。它围绕同一份树结构提供大纲、Markdown 和脑图三种编辑视角，适合写文章提纲、整理功能设计、梳理项目结构和维护可读的 Markdown 脑图文档。

项目仓库：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)

![枝见主窗口](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-main-window.png)

本页截图和 GIF 均来自实际运行的枝见桌面程序，并通过模拟用户操作截取。

## 项目定位

- 打开后是空白脑图：只有一个可输入的中心主题。
- 大纲、Markdown 和脑图共用 `MindMapNode` 模型，任一视图修改都会同步。
- 文件菜单覆盖新建、新窗口、打开、打开文件夹、最近文件、保存、另存为、打开文件位置和关闭。
- 编辑菜单覆盖撤销、重做、结构调整、删除节点和复制为 Markdown。
- 主题、语言、帮助和关于菜单放在标题栏，常用操作带图标和快捷键。
- 语言切换使用 `Lang.Avalonia.Json`，支持中文简体、中文繁体、英语和日语。
- 首次启动使用 AtomUI Tour 引导新用户认识关键操作。
- 打开文件夹后，左侧通过“文件 / 大纲”两个 Tab 切换，先选文件再编辑大纲。
- 应用层使用 AtomUI 窗口、菜单、按钮、列表、输入控件、ToolTip、Tour、全局消息和深色主题。
- `CodeWF.MindView` 只依赖 Avalonia，提供可复用的脑图编辑器、小图、节点模型和文档编解码。

## 主要功能

| 功能 | 说明 |
| --- | --- |
| 大纲编辑 | 支持键盘创建、删除、升降级节点，也可通过节点菜单添加同级、添加子级、上移、下移、备注和删除。 |
| 文件工作流 | 支持空白新建、打开单文件、打开文件夹、最近文件、保存、另存为、打开文件位置和未保存关闭提示。 |
| 脑图编辑 | 支持节点内联编辑、备注、删除、缩放、画布平移、中心主题定位和拖拽调整父子关系。 |
| 备注同步 | 节点备注在大纲和脑图中同步显示，使用灰色文字与标题区分；空备注失焦后自动隐藏。 |
| 小图概览 | 小图按真实节点坐标绘制脑图全局结构，点击小图可以定位到对应区域。 |
| 多语言 | 通过 `Lang.Avalonia.Json` 提供中文简体、中文繁体、英语和日语界面。 |
| 新手引导 | 使用 AtomUI Tour 首次启动引导，配置项位于 `src/Zhijian/App.config`。 |
| 文件格式 | 支持 Markdown、OPML 和 XMind 文件交换。 |

## 运行演示

文件菜单已经补齐为日常编辑需要的完整入口。

![枝见文件菜单](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-file-menu.png)

标题栏菜单按真实操作分组，覆盖文件、编辑、主题、语言、帮助和关于。

![标题栏菜单](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-title-menus.gif)

首次启动 Tour 会引导用户认识关键入口。

![新手引导](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-onboarding.gif)

主题和语言可以直接从标题栏菜单切换。

![主题和语言切换](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-theme-language.gif)

复制为 Markdown 会写入剪贴板并显示 AtomUI 全局消息。

![复制 Markdown 提示](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-copy-markdown.gif)

打开文件夹后可以先浏览支持的脑图文件，再加载到大纲和脑图视图。

![打开文件夹流程](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-open-folder.gif)

大纲和脑图菜单都提供高频结构操作。

![节点菜单](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-node-menus.gif)

备注与标题使用文字大小和前景色区分，不再额外加背景块。

![备注同步](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-note-sync.gif)

小图、缩放和画布拖拽用于处理更大的脑图。

![小图导航](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-minimap.gif)

![缩放](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-zoom.gif)

![画布拖拽](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-canvas-pan.gif)

关于菜单提供打开网站、更新日志、关于和感谢入口。

![关于菜单](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-about-menu.png)

## 工程组织

```text
src/
  CodeWF.MindView/          可复用脑图控件、节点模型、小图和文件编解码
  CodeWF.MindView.Themes/   脑图控件默认 Avalonia 资源
  Zhijian/                  AtomUI 桌面应用、大纲视图、文件服务、主窗口和 ViewModel
docs/
  架构说明、源码设计文档和真实运行截图/GIF
```

`CodeWF.MindView` 不引用 AtomUI，方便其他 Avalonia 项目复用；`Zhijian` 应用层负责 AtomUI 主题、标题栏菜单、对话框、文件工作流和大纲视图。

## 复用 CodeWF.MindView

新的 Avalonia 应用可以只引用脑图控件库和主题库：

```xml
<ItemGroup>
  <ProjectReference Include="..\CodeWF.MindView\CodeWF.MindView.csproj" />
  <ProjectReference Include="..\CodeWF.MindView.Themes\CodeWF.MindView.Themes.csproj" />
</ItemGroup>
```

在 `App.axaml` 注册资源：

```xml
<Application
    xmlns="https://github.com/avaloniaui"
    xmlns:mindThemes="using:CodeWF.MindView.Themes">
    <Application.Styles>
        <mindThemes:MindViewThemes />
    </Application.Styles>
</Application>
```

页面中使用 `MindMapEditor`：

```xml
<UserControl
    xmlns="https://github.com/avaloniaui"
    xmlns:mind="https://codewf.com">
    <mind:MindMapEditor
        Roots="{Binding Roots}"
        SelectedNode="{Binding SelectedNode, Mode=TwoWay}"
        Controller="{Binding}" />
</UserControl>
```

宿主 ViewModel 提供 `ObservableCollection<MindMapNode>`，并实现 `IMindMapEditorController`，用于接管节点层级、创建、删除、升降级和拖拽移动逻辑。文件打开、保存、最近文件和未保存提示可以参考 `IMindMapFileService` 及 `src/Zhijian` 的应用层实现。

## 开源项目感谢

枝见的开发离不开这些优秀开源平台和项目：

- [Dotnet](https://dotnet.microsoft.com/zh-cn/)
- [Avalonia UI](https://avaloniaui.net/)
- [Semi.Avalonia](https://github.com/irihitech/Semi.Avalonia)
- [Ursa.Avalonia](https://github.com/irihitech/Ursa.Avalonia)
- [AtomUI](https://github.com/AtomUI/AtomUI)

## 快速开始

环境要求：

- .NET 10 SDK

```powershell
dotnet restore Zhijian.slnx
dotnet build Zhijian.slnx
dotnet run --project src/Zhijian/Zhijian.csproj
```

## 仓库

- GitHub：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)
