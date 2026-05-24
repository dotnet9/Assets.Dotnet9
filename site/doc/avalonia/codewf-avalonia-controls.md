# CodeWF.AvaloniaControls

![CodeWF.AvaloniaControls](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-avalonia-controls.svg)

`CodeWF.AvaloniaControls` 是基于 .NET 11 与 Avalonia 12 的开源控件仓库，包含可复用类库、主题资源包和可直接运行的示例工程。它适合用来沉淀通用控件、验证控件模板，也适合给 Avalonia 项目提供一套统一的基础 UI 能力。

## 包线

| 包 | 说明 |
| --- | --- |
| `CodeWF.AvaloniaControls` | 通用自定义控件。 |
| `CodeWF.AvaloniaControls.Themes` | 控件模板与主题资源包。 |

## 安装

```powershell
Install-Package CodeWF.AvaloniaControls
Install-Package CodeWF.AvaloniaControls.Themes
```

## 仓库结构

```text
src/
  CodeWF.AvaloniaControls/              通用控件类库
  CodeWF.AvaloniaControls.Themes/       控件模板与主题资源
  CodeWF.AvaloniaControls.Showcase/     控件展示馆
  CodeWF.AvaloniaControls.FluentStarterDemo/ 轻量启动窗口示例
docs/                                   截图、GIF 和文档资源
artifacts/                              打包输出
publish/                                示例发布目录
```

## 适合关注

- 如何把 Avalonia 控件和主题资源拆成独立 NuGet 包。
- 如何维护可运行的 Showcase，让控件行为、样式和主题切换可视化。
- 如何使用 `Guide` 在桌面应用中实现新手引导、功能漫游和局部提示。
- 如何在一个仓库中同时组织类库、演示程序、打包脚本和发布脚本。
- 如何在控件库中避免商业包依赖，保持开源项目的可分发性。

## Guide 引导控件

`Guide` 用于 Avalonia 桌面应用的新手引导、功能漫游和局部提示。它不只覆盖普通按钮高亮，也面向桌面软件中更常见的动态入口：菜单、二级菜单、`Popup`、`Flyout`、滚动区域、`TabItem` 切换、目标延迟出现和窗口尺寸变化。

- `GuideStep` 可为每一步绑定不同目标控件，也支持无目标居中说明。
- `GuideOverlay` 负责遮罩挖洞、高亮圆角、目标间距和卡片方向控制。
- `TargetResolveDelay` 与 `StepOpening` 可在进入步骤前展开菜单、切换页签，再延迟解析目标。
- 支持 `DefaultGuideIndicator`、`TextGuideIndicator`、非模态提示，以及布局变化后的刷新定位。
- Showcase 中包含基础多步骤引导、封面内容、自定义按钮、文本进度和非遮罩提示示例。

## 示例工程

- `CodeWF.AvaloniaControls.Showcase`：用于集中展示控件能力、主题切换和示例页面。
- `CodeWF.AvaloniaControls.FluentStarterDemo`：用于演示轻量启动窗口和基础样式接入。

## 构建与打包

```powershell
dotnet restore CodeWF.AvaloniaControls.slnx
dotnet build CodeWF.AvaloniaControls.slnx --no-restore
.\pack.bat
```

## 仓库

- GitHub：[https://github.com/dotnet9/CodeWF.AvaloniaControls](https://github.com/dotnet9/CodeWF.AvaloniaControls)
- NuGet：[https://www.nuget.org/packages/CodeWF.AvaloniaControls](https://www.nuget.org/packages/CodeWF.AvaloniaControls)
