![](https://img1.dotnet9.com/2026/05/zhijian-avalonia-cover.svg)

今天继续打磨本地桌面脑图编辑器 **枝见 Zhijian**。

枝见是一个基于 **C# + Avalonia + AtomUI** 的 Markdown-first 脑图编辑器。它不是一个复杂项目管理系统，而是围绕写提纲、梳理功能设计、整理文章结构这些高频场景，把大纲、Markdown 和脑图三种视角绑定到同一棵树上。

项目仓库：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)。

本轮截图和 GIF 都来自实际运行的枝见桌面程序，并通过模拟用户操作截取，不是静态拼图。

![](https://img1.dotnet9.com/2026/05/zhijian-main-window.png)

## 打开就是空白脑图

新版本启动后不再默认加载示例数据，而是只保留一个待输入的中心主题。这个细节很小，但更像一个真正可用的编辑器：打开程序后用户可以直接开始写自己的内容，不需要先删除样例。

脑图二级节点也不再是单调灰色。新建或导入后，二级节点会使用更醒目的自动强调色；备注文字使用灰色前景和略大的字号，与标题区分，但不再额外加背景块，视觉负担更低。

## 文件菜单补齐成产品工作流

标题栏“文件”菜单现在不只是导入导出入口，而是完整文件工作流：

- 新建：创建一个空白脑图。
- 新建窗口：打开新的编辑器进程。
- 打开：通过一个文件对话框选择 Markdown、OPML、XMind 等支持格式。
- 打开文件夹：扫描文件夹下支持的脑图文件。
- 打开最近文件：记录最近操作过的文件。
- 保存、另存为、打开文件位置、关闭：补齐日常编辑需要的闭环。

![](https://img1.dotnet9.com/2026/05/zhijian-file-menu.png)

## 标题栏菜单继续按真实工作流整理

菜单现在不再只放“文件”和“关于”。标题栏已经整理为文件、编辑、主题、语言、帮助、关于几组入口，并统一使用 AtomUI `Menu/MenuItem`，菜单项带图标，常用操作显示快捷键。

![](https://img1.dotnet9.com/2026/05/zhijian-title-menus.gif)

“编辑”菜单放撤销、重做、添加同级、添加子级、提升、降级、上移、下移、删除，以及“复制为 Markdown”。复制后会写入系统剪贴板，并使用 AtomUI 全局消息提示。

![](https://img1.dotnet9.com/2026/05/zhijian-copy-markdown.gif)

主题不再使用标题栏 ToggleSwitch，而是单独放到“主题”菜单里，后续扩展更多主题会更自然。语言菜单使用 `Lang.Avalonia.Json`，当前提供中文简体、中文繁体、英语和日语资源。

![](https://img1.dotnet9.com/2026/05/zhijian-theme-language.gif)

“帮助”菜单提供问题反馈、提交需求、提交 PR 和 GitHub 仓库入口；“关于”菜单继续提供网站、更新日志、感谢和关于窗口。

首次启动还增加了 AtomUI Tour 新手引导，会把标题栏菜单、左侧输入区、Markdown 切换、脑图画布和状态栏导航依次介绍给新用户。是否显示由 `src/Zhijian/App.config` 控制。

![](https://img1.dotnet9.com/2026/05/zhijian-onboarding.gif)

打开文件夹后，左侧从单一大纲变成“文件 / 大纲”两个 Tab。先在“文件”里选择要编辑的文档，再自动切回“大纲”展示当前脑图结构。

![](https://img1.dotnet9.com/2026/05/zhijian-open-folder.gif)

关闭或切换文件前如果存在未保存修改，会弹出保存提示。最近文件记录保存在程序目录，避免每次重新定位常用文档。

## 大纲和脑图都要顺手

大纲视图和脑图视图共享同一个 `MindMapNode` 树，所以在任意一侧编辑标题、备注、父子关系，另一侧都会同步刷新。

常用结构操作补到了两个视图的菜单里：

- 添加子节点、添加同级节点。
- 提升为父节点、降级为子节点。
- 上移、下移。
- 备注、删除。

![](https://img1.dotnet9.com/2026/05/zhijian-node-menus.gif)

备注输入也做了细节处理：只有节点已有备注，或者用户明确选择“备注”时才显示备注输入框；空备注失焦后会自动收起。脑图节点里备注与标题已经按同一文本对齐方式绘制，短文本节点也能重新点回去继续输入。

![](https://img1.dotnet9.com/2026/05/zhijian-note-sync.gif)

## 小图、缩放和画布拖拽

脑图面积变大后，用户最需要的是“我现在看的是整张图的哪一块”。小图现在基于真实节点坐标和当前视口绘制，不是固定示意图；点击小图可以定位到对应区域。

![](https://img1.dotnet9.com/2026/05/zhijian-minimap.gif)

缩放、中心主题定位、画布拖拽也都在实际窗口里重新验证过。

![](https://img1.dotnet9.com/2026/05/zhijian-zoom.gif)

![](https://img1.dotnet9.com/2026/05/zhijian-canvas-pan.gif)

中间分隔条的拖拽调整宽度也已经改为明确的 `GridSplitter` 行为并通过截图验证，只是这类动图对阅读帮助不大，文档里不再把它作为重点演示。

## 关于、更新日志和感谢

标题栏“关于”菜单包含：

- 打开网站：[https://codewf.com](https://codewf.com)
- 更新日志：独立 AtomUI 窗口展示随程序复制的更新日志文件，并使用 `CodeWF.Markdown.Lite.Themes` 渲染。
- 关于：展示软件名称、简介、版本号、更新时间、作者、联系方式、仓库地址和 NuGet 包地址。
- 感谢：列出本项目使用和受益的开源项目，链接可直接跳转。

![](https://img1.dotnet9.com/2026/05/zhijian-about-menu.png)

枝见感谢这些优秀的开源平台和项目：

- [Dotnet](https://dotnet.microsoft.com/zh-cn/)
- [Avalonia UI](https://avaloniaui.net/)
- [Semi.Avalonia](https://github.com/irihitech/Semi.Avalonia)
- [Ursa.Avalonia](https://github.com/irihitech/Ursa.Avalonia)
- [AtomUI](https://github.com/AtomUI/AtomUI)

## 新应用怎么复用 CodeWF.MindView

如果你想在自己的 Avalonia 应用里复用脑图能力，优先引用 `CodeWF.MindView`，不要直接复制整个枝见应用。

`CodeWF.MindView` 当前包含：

- `MindMapNode`：共享节点模型，包含标题、备注、颜色、坐标和子节点。
- `MindMapEditor`：脑图主编辑控件。
- `MindMapMiniMap`：基于真实节点坐标的小图控件。
- `MindMapDocumentCodec`：Markdown、OPML、XMind 编解码。
- `IMindMapEditorController`：由宿主应用实现，用来接管节点创建、删除、升降级和拖拽移动等行为。
- `IMindMapFileService`：文件打开、保存、最近文件、文件夹加载和未保存提示等应用级文件服务契约。

新应用可以引用控件库和主题库：

```xml
<ItemGroup>
  <ProjectReference Include="..\CodeWF.MindView\CodeWF.MindView.csproj" />
  <ProjectReference Include="..\CodeWF.MindView.Themes\CodeWF.MindView.Themes.csproj" />
</ItemGroup>
```

在 `App.axaml` 注册主题：

```xml
<Application
    xmlns="https://github.com/avaloniaui"
    xmlns:mindThemes="using:CodeWF.MindView.Themes">
    <Application.Styles>
        <mindThemes:MindViewThemes />
    </Application.Styles>
</Application>
```

页面里直接放脑图控件：

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

宿主 ViewModel 提供 `ObservableCollection<MindMapNode>`，并实现 `IMindMapEditorController`。这样控件库负责显示、编辑、拖拽预览、小图和基础交互，应用负责自己的文档状态、文件菜单和业务规则。

如果你的应用也需要类似枝见的大纲视图、文件菜单、AtomUI 标题栏和 Markdown 面板，可以参考 `src/Zhijian`。其中 `OutlineEditor` 和桌面窗口属于应用层代码，因为它们使用了 AtomUI 菜单、输入控件和图标；`CodeWF.MindView` 则保持 Avalonia-only，方便其他项目复用。

仓库地址仍然是：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)。

## 本轮验证

这轮不只是改完代码看一眼编译。我实际运行了桌面程序，并用截图/GIF验证了这些操作：

- 新建空白脑图。
- 打开文件菜单和关于菜单，确认标题栏菜单可点击且右侧没有突兀箭头。
- 打开编辑、主题、语言、帮助菜单，确认 AtomUI 菜单、图标、快捷键和分类都正常。
- 切换深色/浅色主题，确认文字和菜单在不同主题下可读。
- 切换英语语言，确认标题栏菜单、Tab 和状态栏文本能更新。
- 使用复制为 Markdown，确认剪贴板命令执行并出现 AtomUI 全局消息。
- 重置首次启动标记，确认 AtomUI Tour 能按真实窗口显示。
- 打开文件夹，确认“文件 / 大纲”Tab 能切换并加载文件。
- 在大纲和脑图中打开节点菜单，确认常用结构操作齐全。
- 编辑短文本节点和备注，确认可以重新获得焦点且标题/备注对齐。
- 打开小图、缩放脑图、拖拽画布。
- 拖动左右分隔条，确认大纲和脑图宽度能真实调整。

最终构建命令：

```powershell
dotnet build Zhijian.slnx
```

构建结果为 0 警告、0 错误。

对我来说，枝见最重要的不是堆功能，而是每个高频操作都顺手：打开就是空白文档、菜单项能直接命中真实需求、节点创建后焦点在正确位置、备注不抢视觉、缩放和小图能帮用户重新找到方向。这些细节做好了，用户才愿意一直用它整理结构。
