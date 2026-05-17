# 枝见 Zhijian

![枝见 Zhijian](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian.svg)

`Zhijian` 是一个基于 C#、Avalonia 和 AtomUI 的本地脑图编辑器。它围绕同一份树结构提供大纲、Markdown 和脑图三种编辑视角，适合写文章提纲、整理功能设计、梳理项目结构和维护可读的 Markdown 脑图文档。

项目仓库：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)

![枝见双视图](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-dual-view.png)

## 项目定位

- Markdown-first：中心主题、子节点和备注可以保存为可读 Markdown。
- 双向同步：大纲、Markdown 和脑图共用 `MindMapNode` 模型，任一视图修改都会同步到其他视图。
- 本地桌面体验：应用层使用 AtomUI 主题、窗口、菜单、按钮和输入控件。
- 可复用控件：`CodeWF.MindView` 只依赖 Avalonia，提供脑图编辑器、小图、节点模型和文件编解码。
- 文件交换：支持 Markdown、OPML 和 XMind 导入导出。

## 主要功能

| 功能 | 说明 |
| --- | --- |
| 大纲编辑 | 支持键盘创建、删除、升降级节点，节点前圆点可打开备注/删除菜单，也可拖拽调整结构。 |
| 脑图编辑 | 支持节点内联编辑、备注、删除、缩放、画布平移、中心主题定位和拖拽调整父子关系。 |
| 备注同步 | 节点备注在大纲和脑图中同步显示；空备注失焦后自动隐藏。 |
| 拖拽预览 | 拖到目标中部成为子节点，拖到上下边缘调整同级顺序，并显示虚线预览。 |
| 小图概览 | 小图按真实节点坐标绘制脑图全局结构，点击小图可以定位到对应区域。 |
| Markdown 视图 | 左侧可切换 Markdown 编辑，标题层级和备注可以直接作为文本维护。 |

## 界面预览

备注在大纲和脑图中同步显示，便于记录节点细节，又不会把备注和标题混在一起。

![枝见备注同步](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-note-sync.png)

拖拽节点时会提示即将成为子节点或插入到同级位置，降低结构调整的不确定性。

![枝见拖拽预览](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-mind-drag-preview.png)

小图显示的是当前脑图的真实概览，不是固定示意图。

![枝见小图](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-minimap-overview.png)

创建节点后焦点会自动进入新节点，适合连续录入大纲。

![枝见创建节点](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-create-node.gif)

## 工程组织

```text
src/
  CodeWF.MindView/          可复用脑图控件、节点模型、小图和文件编解码
  CodeWF.MindView.Themes/   脑图控件默认 Avalonia 资源
  Zhijian/                  AtomUI 桌面应用、大纲视图、主窗口和 ViewModel
docs/
  架构说明、源码设计文档和参考截图
```

`CodeWF.MindView` 不引用 AtomUI，方便其他 Avalonia 项目复用；`Zhijian` 应用层负责 AtomUI 主题和桌面交互。

## 快速开始

环境要求：

- .NET 10 SDK

```powershell
dotnet restore Zhijian.slnx
dotnet build Zhijian.slnx
dotnet run --project src/Zhijian/Zhijian.csproj
```

## 适合关注

- 需要一个轻量本地脑图编辑器，并希望默认文件可读、可版本管理。
- 想学习 Avalonia + AtomUI 桌面应用如何组织窗口、主题、菜单和自定义控件。
- 想参考脑图控件的布局、拖拽、小图、备注同步和 Markdown/OPML/XMind 编解码设计。

## 仓库

- GitHub：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)
