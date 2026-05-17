![](https://img1.dotnet9.com/2026/05/zhijian-avalonia-cover.svg)

今天这篇文章，站长来介绍一个最近继续打磨的本地桌面小工具：**枝见 Zhijian**。

它是一个基于 **C# + Avalonia + AtomUI** 的脑图编辑器，目标不是做一个复杂项目管理工具，而是把我平时写提纲、整理文章结构、梳理功能设计时最常用的几件事做好：左边用大纲或 Markdown 快速输入，右边同步看到脑图结构；节点可以写备注；结构可以拖拽调整；最后还能用 Markdown、OPML、XMind 这些格式导入导出。

项目仓库在 GitHub：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)。

先看一张当前主界面。本文里的新增截图和 GIF，均来自本轮实际运行枝见桌面程序，并通过模拟用户操作截取，不是手工拼出来的示意图。

![](https://img1.dotnet9.com/2026/05/zhijian-dual-view.png)

## 为什么做枝见

我自己写文章或者做功能设计时，经常会在两种视角之间切换：

- 大纲视角：适合快速输入、调整层级、看章节顺序。
- 脑图视角：适合看关系、分支密度和整体结构。
- Markdown 视角：适合保存、版本管理和后续发布。

很多工具只做好其中一种视角。枝见的目标是让这三种视角围绕同一份树结构工作，减少来回复制和格式转换。

当前版本的定位比较清晰：它是一个本地优先、Markdown-first 的脑图编辑器。

## 双视图：大纲和脑图始终同步

左侧默认是大纲视图，右侧是脑图画布。两个视图绑定的是同一个 `MindMapNode` 树，所以在任意一侧编辑标题、创建节点、删除节点、调整父子关系，另一侧都会立即更新。

这次我重点补了几个实际使用中很影响体验的细节：

- 创建节点后，焦点会直接进入新节点输入框。
- 大纲和脑图之间有明确可见的拖动分隔条，可以直接左右调整两侧宽度。
- 一级、二级和更深层节点都有最大宽度，标题变长后自动换行。
- 第 4 层以后也会按真实节点宽度重新计算列距，避免连线和输入框挤在一起。
- 脑图节点前面的可见竖线已经去掉，只保留透明拖拽命中区。
- 标题栏的文件和关于入口改成了纯文本按钮，文件菜单也平铺为直接导入/导出动作，没有多余的右侧箭头。
- 大纲和脑图菜单补齐了添加同级、提升/降级、上移/下移这些常用结构操作，连续整理层级时更顺手。

分隔条拖动过程如下：

![](https://img1.dotnet9.com/2026/05/zhijian-splitter-resize.gif)

标题栏的文件和关于菜单也截了一张实际运行图，菜单项更直接。

![](https://img1.dotnet9.com/2026/05/zhijian-file-menu.png)

![](https://img1.dotnet9.com/2026/05/zhijian-about-menu.png)

## 备注：不是和标题混在一起

备注是这次补得比较细的一块。

大纲和脑图里的备注使用同一个 `MindMapNode.Note` 字段。它不会默认把每个节点都撑出一个空输入框，而是只有两种情况会显示：

- 节点已经有备注。
- 用户通过菜单明确点击“备注”。

在脑图里，标题获得焦点后会显示一个轻量悬浮工具条，也可以通过菜单完成添加子级、添加同级、升降级、移动、备注和删除等结构操作。

![](https://img1.dotnet9.com/2026/05/zhijian-mind-menu.png)

点击备注后，脑图节点内会出现备注输入区，大纲同一节点也会同步显示备注。

![](https://img1.dotnet9.com/2026/05/zhijian-note-sync.png)

动态效果如下：

![](https://img1.dotnet9.com/2026/05/zhijian-note-sync.gif)

这里还有一个细节：如果备注框是空的，失焦后会自动隐藏；如果光标在空备注框里，再按 Backspace 或 Delete，就相当于删除备注，不再保留空输入框。

## 大纲圆点菜单

大纲节点前面的圆点现在承担两个动作：

- 短按或右键：打开菜单。
- 按住并拖动超过一定距离：进入拖拽调整结构。

菜单现在保留添加子级、添加同级、提升/降级、上移/下移、备注和删除这些高频动作，并使用 AtomUI 的菜单和 AntDesign 图标。

![](https://img1.dotnet9.com/2026/05/zhijian-outline-menu.png)

我刻意没有把菜单项做得很满。大纲编辑器最重要的是快，菜单只应该补足键盘操作之外的高频动作。

## 脑图拖拽：预览最终结果

脑图节点现在支持拖拽调整结构：

- 拖到目标节点中部：成为它的子节点。
- 拖到目标节点上边缘：插入到它前面。
- 拖到目标节点下边缘：插入到它后面。

拖动时会显示虚线预览，绿色虚线框表示“成为子节点”，蓝色插入线表示“调整同级顺序”。

脑图菜单和悬浮工具条的实际运行截图前面已经放过。拖拽逻辑本轮也在运行窗口里验证过，文档里不再放重复动图。

大纲视图也使用同样的落点语义。这样用户不需要记两套规则。

## 小图：真实脑图概览

底部工具条的小图不再是固定示意图，而是按照当前节点真实坐标、节点尺寸和连线重新绘制。蓝色框表示当前视口，点击小图可以定位到对应区域。

![](https://img1.dotnet9.com/2026/05/zhijian-minimap-overview.png)

这个功能看起来小，但在脑图变宽以后很实用。用户可以快速知道自己现在看的是整张图的哪一块。

## Markdown 仍然是默认存储

枝见不是把脑图数据锁在某个私有格式里。默认文档模型可以直接转成 Markdown：

- `#` 表示中心主题。
- `##` 表示二级节点。
- `###` 和更深标题表示子节点。
- 标题下面的普通文本作为备注。

左侧可以一键切换到 Markdown 视图。

![](https://img1.dotnet9.com/2026/05/zhijian-markdown-view.png)

目前导入导出支持：

- Markdown
- OPML
- XMind

这让它更适合作为一个轻量编辑入口，而不是把数据困在应用内部。

## AtomUI 主题，不再依赖 FluentTheme

应用层使用 AtomUI 主题和桌面控件，标题栏、按钮、菜单、文本框这些界面元素都交给 AtomUI。

同时，我把可复用脑图控件拆到 `CodeWF.MindView`，这个工程只引用 Avalonia，不引用 `AtomUI.Desktop.Controls`。这样做有两个好处：

- 枝见应用可以继续使用 AtomUI 保持整体风格。
- `CodeWF.MindView` 仍然可以被其他 Avalonia 项目复用，不强迫使用 AtomUI。

深色主题也同步覆盖大纲、脑图、状态栏和 Markdown 区域。

![](https://img1.dotnet9.com/2026/05/zhijian-dark-theme.png)

Markdown 视图和深色主题切换过程如下：

![](https://img1.dotnet9.com/2026/05/zhijian-markdown-theme.gif)

更新日志和关于窗口也改成了独立 AtomUI 窗口，内容来自随程序复制的更新日志文件和当前程序集信息。

![](https://img1.dotnet9.com/2026/05/zhijian-changelog-window.png)

![](https://img1.dotnet9.com/2026/05/zhijian-about-window.png)

## 当前源码组织

仓库现在大致分为三块：

```text
src/
  CodeWF.MindView/
    节点模型、脑图控件、小图控件、Markdown/OPML/XMind 编解码
  CodeWF.MindView.Themes/
    脑图控件默认 Avalonia 资源
  Zhijian/
    AtomUI 桌面应用、大纲视图、主窗口、文件服务和 ViewModel
docs/
  架构说明、源码设计文档和参考截图
```

核心思路是：共享模型放在可复用库里，应用交互放在桌面工程里。脑图控件不绑定具体应用外壳，大纲视图则可以放心使用 AtomUI 的菜单、图标和输入控件。

这次我也在 `docs` 下补了一份中文源码设计文档，说明项目分层、数据流、拖拽落点、备注生命周期、小图和主题边界，方便后续继续维护。

## 新应用怎么使用 CodeWF.MindView

如果你想在自己的 Avalonia 应用里复用脑图能力，优先看 `CodeWF.MindView`，而不是直接把整个枝见应用搬过去。

`CodeWF.MindView` 当前包含：

- `MindMapNode`：共享节点模型，包含标题、备注、颜色、坐标和子节点。
- `MindMapEditor`：脑图主编辑控件。
- `MindMapMiniMap`：真实坐标小图概览控件。
- `MindMapDocumentCodec`：Markdown、OPML、XMind 编解码。
- `IMindMapEditorController`：宿主应用提供节点创建、删除、移动、层级判断等行为的接口。

新应用只需要引用控件库和主题库：

```xml
<ItemGroup>
  <ProjectReference Include="..\CodeWF.MindView\CodeWF.MindView.csproj" />
  <ProjectReference Include="..\CodeWF.MindView.Themes\CodeWF.MindView.Themes.csproj" />
</ItemGroup>
```

在 `App.axaml` 里注册脑图主题：

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

宿主 ViewModel 提供 `ObservableCollection<MindMapNode>`，并实现 `IMindMapEditorController`：

```csharp
public sealed class MindMapPageViewModel : IMindMapEditorController
{
    public ObservableCollection<MindMapNode> Roots { get; } =
    [
        new MindMapNode("中心主题")
    ];

    public MindMapNode? SelectedNode { get; set; }

    public int GetLevel(MindMapNode node) => ...;
    public bool IsRoot(MindMapNode? node) => ...;
    public MindMapNode HandleMapEnter(MindMapNode node) => ...;
    public MindMapNode HandleMapTab(MindMapNode node) => ...;
    public bool PromoteNode(MindMapNode? node) => ...;
    public MindMapNode DeleteNode(MindMapNode? node) => ...;
    public bool CanMoveNode(MindMapNode? node, MindMapNode? target) => ...;
    public bool MoveNode(MindMapNode? node, MindMapNode? target, MindMapDropPlacement placement) => ...;
}
```

这样控件库负责显示、编辑、拖拽预览、小图和基础交互，应用负责自己的文档状态和业务规则。

如果你的应用也想要类似枝见的大纲视图、文件菜单、AtomUI 标题栏和 Markdown 面板，可以直接参考 `src/Zhijian`。其中 `OutlineEditor` 是应用层代码，因为它使用了 AtomUI 的菜单、文本框和 AntDesign 图标；`CodeWF.MindView` 则保持 Avalonia-only，方便复用。

仓库地址仍然放在这里：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)。

## 本轮验证

这次不是只改完代码看一眼编译。我实际跑了应用，并模拟了这些操作：

- 在大纲创建节点，确认焦点进入新节点。
- 拖动中间分隔条，确认大纲和脑图宽度可以左右调整。
- 在脑图节点上添加备注，确认大纲同步显示。
- 点击大纲圆点弹出菜单，确认不影响圆点拖拽。
- 拖拽脑图节点，确认虚线预览和最终父子关系。
- 打开小图，确认它反映真实脑图结构。
- 切换 Markdown 视图和深色主题。
- 后续又跑了 30 分钟自动化桌面操作，循环覆盖分隔条拖拽、视图切换、主题切换、小图、缩放、节点创建/删除、备注添加/删除和脑图拖拽。
- 本文新增截图和 GIF 重新来自桌面程序实际运行窗口，包括文件菜单、关于菜单、大纲菜单、脑图菜单、备注同步、分隔条拖动、Markdown/深色主题、更新日志和关于窗口。

最终构建命令：

```powershell
dotnet build Zhijian.slnx
```

构建结果为 0 警告、0 错误。

## 后续

枝见现在已经有一个比较完整的编辑闭环：大纲输入、脑图观察、备注记录、拖拽整理、Markdown 保存、OPML/XMind 交换。

后面可以继续打磨的方向包括：

- 更完整的快捷键提示和帮助页。
- 更细的节点样式设置。
- 更复杂文档的导入兼容性验证。
- 更丰富的 Markdown 编辑辅助。
- 打包发布和跨平台体验验证。

对我来说，枝见更像一个“写作和设计前的结构工作台”。它不用很重，但每一个小交互都要顺手。节点创建后焦点在哪、备注什么时候显示、拖拽预览是不是可信、小图是不是对应真实脑图，这些细节都会直接影响用户愿不愿意继续用下去。
