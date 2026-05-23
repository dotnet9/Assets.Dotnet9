![](https://img1.dotnet9.com/2026/05/codewf-avalonia-guide-cover.svg)

这篇重新整理一下 **CodeWF.AvaloniaControls** 里新增的 `Guide` 引导控件。

新手引导这种控件，只讲属性和实现确实不直观。它本质上是一个强交互控件：遮罩、高亮、卡片定位、菜单展开、TabItem 切换、目标控件延迟出现，这些效果都需要先看画面，再看代码才容易理解。

先看 Vex 里实际落地的新手引导流程。

![](https://img1.dotnet9.com/2026/05/codewf-guide-vex-onboarding-flow.gif)

这组引导覆盖了 Vex 的标题栏菜单、文件菜单项、大纲入口、左侧大纲页签、主题菜单和帮助菜单。最关键的是，它不是只指向页面上本来就存在的按钮，而是会在步骤切换时主动打开菜单、切换侧边栏 TabItem，然后再高亮新出现的目标。

`Guide` 的源码在这里：

```text
https://github.com/dotnet9/CodeWF.AvaloniaControls/tree/main/src/CodeWF.AvaloniaControls/Controls/Guide
```

Vex 的落地代码在这里：

```text
https://github.com/dotnet9/Vex/tree/main/src/Vex/Modules/Shell/Views\MainWindow.axaml
https://github.com/dotnet9/Vex/tree/main/src/Vex/Modules/Shell/Views\MainWindow.axaml.cs
https://github.com/dotnet9/Vex/tree/main/src/Vex/Modules/Shell/Views\ShellTitleMenuView.axaml
https://github.com/dotnet9/Vex/tree/main/src/Vex/Modules/Shell/Views\ShellTitleMenuView.axaml.cs
```

## 为什么要做 Guide

Avalonia 里有 `Popup`、`MenuItem`、`TabControl`、`Flyout` 这些基础控件，但它们并不会直接组合成一个完整的新手引导流程。

一个真正能用在桌面软件里的引导控件，需要处理这些问题：

- 多步骤流程：上一页、下一页、完成、关闭。
- 每一步绑定不同目标控件。
- 没有目标控件时居中显示说明。
- 有目标控件时绘制遮罩，并在目标周围挖出高亮区域。
- 引导卡片根据目标位置显示在上、下、左、右等方向。
- 目标在滚动区域内时自动滚动到可见位置。
- 目标控件晚一点才出现时，等待并重新定位。
- 目标在 `Menu`、`Popup`、`Flyout` 这种弹层里时，也能正确高亮。
- 布局变化、窗口大小变化后，重新计算高亮区域。

我参考的是 [**AtomUI**](https://github.com/AtomUI/AtomUI) 里的 `Tour` 漫游式引导控件。AtomUI 的 `Tour` 把主控件做成 `TemplatedControl`，步骤抽象为 `ITourStepOption`，再用弹层和遮罩层组合出引导效果。这个方向很适合 Avalonia。

CodeWF 的 `Guide` 沿用了这个思路，但在实现上更偏向我自己的项目需求：

- 使用 `GuideOverlay` 自绘遮罩和高亮区域。
- 使用 `Popup` 显示引导卡片。
- 通过 `GuideStep` 声明每一步，也支持 `StepsSource` 数据源。
- 通过 `StepOpening` 和 `OpeningCommand` 支持动态业务动作。
- 通过 `TargetResolveDelay` 等待菜单、TabItem、Popup 内容完成布局。
- 对弹层里的 `MenuItem` 做额外处理，避免菜单 light-dismiss 影响“下一步”按钮。

## 控件结构

`Guide` 相关类型比较清晰：

- `Guide`：主控件，管理打开关闭、当前步骤、遮罩、弹层、目标解析。
- `GuideStep`：声明式步骤，用在 XAML 里。
- `GuideStepOption`：代码创建步骤时使用。
- `IGuideStepOption`：步骤统一接口。
- `GuideOverlay`：负责绘制遮罩和目标高亮洞。
- `DefaultGuideIndicator`：默认圆点进度指示器。
- `TextGuideIndicator`：文本进度指示器，例如 `1 / 6`。
- `GuidePlacementMode`：卡片位置枚举。
- `GuideMissingTargetBehavior`：目标缺失时居中、跳过或关闭。

主题文件在：

```text
https://github.com/dotnet9/CodeWF.AvaloniaControls/tree/main/src\CodeWF.AvaloniaControls.Themes\Themes\Controls\Guide.axaml
```

模板里有三个关键弹层：

- `PART_MaskPopup`：当前窗口上的遮罩。
- `PART_TargetMaskPopup`：目标在其他弹层或 `TopLevel` 里时使用。
- `PART_Popup`：引导卡片本身。

这个结构让业务侧只关心“引导哪几个目标”，控件内部负责遮罩、定位、按钮、指示器和清理。

## 基础用法

最简单的用法是把 `Guide` 放到页面根布局里，给每个 `GuideStep` 绑定目标控件：

```xml
<Grid>
    <StackPanel Orientation="Horizontal" Spacing="10">
        <Button x:Name="UploadButton" Content="上传文件" />
        <Button x:Name="SaveButton" Content="保存变更" />
        <Button x:Name="MoreButton" Content="更多操作" />
    </StackPanel>

    <codewf:Guide x:Name="BasicGuide" Placement="Bottom" PopupOffset="14">
        <codewf:GuideStep
            Target="{Binding ElementName=UploadButton}"
            Title="上传文件"
            Description="把本地文件加入处理队列。" />
        <codewf:GuideStep
            Target="{Binding ElementName=SaveButton}"
            Placement="Right"
            Title="保存变更"
            Description="保存当前工作区的配置和数据。" />
        <codewf:GuideStep
            Target="{Binding ElementName=MoreButton}"
            Placement="Top"
            Title="更多操作"
            Description="更多操作可以继续展开为导出、复制或批处理。" />
    </codewf:Guide>
</Grid>
```

打开引导：

```csharp
BasicGuide.GoTo(0);
BasicGuide.Show();
```

如果要做非模态提示，可以关闭遮罩：

```xml
<codewf:Guide
    x:Name="NonMaskGuide"
    IsShowMask="False"
    Placement="Top"
    StyleType="Primary">
    <codewf:Guide.Indicator>
        <codewf:TextGuideIndicator />
    </codewf:Guide.Indicator>
</codewf:Guide>
```

每一步也可以单独调整高亮区域：

```xml
<codewf:GuideStep
    Target="{Binding ElementName=PreviewPanel}"
    Placement="Left"
    GapOffsetX="16"
    GapOffsetY="16"
    GapRadius="14"
    Title="自定义高亮区域"
    Description="扩大圈选间距和圆角，适合突出整块区域。" />
```

## 遮罩怎么画

`GuideOverlay` 的核心是用 EvenOdd 几何规则挖洞。

它先画一个覆盖整个窗口的矩形，再把目标控件区域作为第二个矩形加入同一个 `GeometryGroup`，并设置：

```csharp
geometry.FillRule = FillRule.EvenOdd;
```

最终效果就是：整屏变暗，目标控件区域保持透明。

目标区域通过屏幕坐标换算出来：

```csharp
var targetTopLeft = target.PointToScreen(new Point(0, 0));
var origin = relativeTopLevel.PointToClient(targetTopLeft);
var rect = new Rect(origin, target.Bounds.Size);
var result = rect.Inflate(new Thickness(gapX, gapY));
```

这里没有直接用 `TranslatePoint` 只在同一个视觉树里转换，是因为菜单、Popup、Flyout 这些目标可能已经在另一个弹层宿主里。先拿屏幕坐标，再转回对应 `TopLevel` 的客户区坐标，会更稳。

## 动态菜单引导

动态菜单是这次 `Guide` 最重要的增强之一。

先看效果。

![](https://img1.dotnet9.com/2026/05/codewf-guide-vex-menuitem-guide.gif)

普通引导的目标控件本来就在页面上，直接高亮就行。菜单项不一样：子级 `MenuItem` 只有父菜单打开以后才会出现在视觉树里。

例如 Vex 里文件菜单、段落菜单、格式菜单、视图菜单、主题菜单和帮助菜单都是这样。引导到某个菜单项之前，需要先打开对应菜单，再等待菜单项完成布局。

Demo 里的思路是：

```xml
<Menu>
    <MenuItem x:Name="GuideThemeMenu" Header="主题色">
        <MenuItem x:Name="GuideThemeBlueItem" Header="蓝色" />
        <MenuItem x:Name="GuideThemeGreenItem" Header="绿色" />
        <MenuItem x:Name="GuideThemePurpleItem" Header="紫色" />
    </MenuItem>
</Menu>

<codewf:Guide
    x:Name="DynamicGuide"
    TargetResolveDelay="00:00:00.220"
    StepOpening="DynamicGuide_OnStepOpening">
    <codewf:GuideStep
        Target="{Binding ElementName=GuideThemeMenu}"
        Title="主题色菜单"
        Description="先说明菜单入口本身。" />
    <codewf:GuideStep
        Target="{Binding ElementName=GuideThemeBlueItem}"
        Placement="RightBottom"
        Title="蓝色主题"
        Description="打开菜单后再圈选下拉 MenuItem。" />
</codewf:Guide>
```

进入步骤时打开菜单：

```csharp
private void DynamicGuide_OnStepOpening(object? sender, GuideStepEventArgs e)
{
    GuideThemeMenu.IsSubMenuOpen = e.Index is >= 1 and <= 3;
}
```

实际项目里我通常还会再投递一次到 UI 线程后台队列：

```csharp
Dispatcher.UIThread.Post(
    () => GuideThemeMenu.IsSubMenuOpen = true,
    DispatcherPriority.Background);
```

原因是菜单弹层创建和布局不是完全同步完成的。`Guide` 的 `TargetResolveDelay` 会给菜单弹层一点时间，再去解析目标 `MenuItem`。

## Vex 中的菜单项落地

Vex 的标题栏菜单在 `ShellTitleMenuView.axaml` 里，关键项都给了名字：

```xml
<MenuItem x:Name="FileMenuItem" Header="{i18n:I18n {x:Static l:VexL.MenuFile}}">
    <MenuItem x:Name="OpenFolderMenuItem" Header="{i18n:I18n {x:Static l:VexL.OpenFolder}}" />
    <MenuItem x:Name="ExportMenuItem" Header="{i18n:I18n {x:Static l:VexL.Export}}">
        <MenuItem Header="HTML" />
        <MenuItem Header="PDF" />
        <MenuItem Header="PNG" />
    </MenuItem>
</MenuItem>
```

`ShellTitleMenuView.axaml.cs` 把这些控件暴露给主窗口：

```csharp
public MenuItem FileMenuTarget => FileMenuItem;
public MenuItem OpenFolderMenuTarget => OpenFolderMenuItem;
public MenuItem ExportMenuTarget => ExportMenuItem;
public MenuItem TableMenuTarget => TableMenuItem;
public MenuItem LinkMenuTarget => LinkMenuItem;
public MenuItem SourceModeMenuTarget => SourceModeMenuItem;
public MenuItem OutlineMenuTarget => OutlineMenuItem;
public MenuItem ThemeDarkMenuTarget => ThemeDarkMenuItem;
public MenuItem BeginGuideMenuTarget => BeginGuideMenuItem;
```

主窗口再把这些目标赋给对应的 `GuideStep`：

```csharp
private void ConfigureOnboardingGuideTargets()
{
    GuideFileMenuStep.Target = TitleMenuView.FileMenuTarget;
    GuideFileOpenStep.Target = TitleMenuView.OpenFolderMenuTarget;
    GuideFileExportStep.Target = TitleMenuView.ExportMenuTarget;
    GuideParagraphMenuStep.Target = TitleMenuView.TableMenuTarget;
    GuideFormatMenuStep.Target = TitleMenuView.LinkMenuTarget;
    GuideViewMenuStep.Target = TitleMenuView.SourceModeMenuTarget;
    GuideViewOutlineMenuStep.Target = TitleMenuView.OutlineMenuTarget;
    GuideThemeMenuStep.Target = TitleMenuView.ThemeDarkMenuTarget;
    GuideHelpMenuStep.Target = TitleMenuView.BeginGuideMenuTarget;
}
```

进入步骤时，主窗口判断当前步骤属于哪个菜单：

```csharp
private void OnboardingGuide_OnStepOpening(object? sender, GuideStepEventArgs e)
{
    PrepareOnboardingGuideStep(e.Step);
    TitleMenuView.SetGuideMenuOpen(GetGuideMenuKey(e.Step));
}
```

`SetGuideMenuOpen` 的职责是先关闭所有菜单，再打开当前步骤需要的菜单：

```csharp
public void SetGuideMenuOpen(string? menuKey)
{
    CloseGuideMenus();
    if (string.IsNullOrWhiteSpace(menuKey))
    {
        return;
    }

    ApplyGuideMenuOpen(menuKey);
    Dispatcher.UIThread.Post(() => ApplyGuideMenuOpen(menuKey), DispatcherPriority.Background);
}
```

主题菜单还有二级菜单，处理时要连续打开：

```csharp
case ThemeColorGuideMenu:
    ThemeMenuItem.IsSubMenuOpen = true;
    ThemeColorMenuItem.IsSubMenuOpen = true;
    break;
```

这就是菜单项引导的完整链路：

1. `GuideStep.Target` 指向具体 `MenuItem`。
2. `StepOpening` 打开父菜单。
3. `TargetResolveDelay` 等待弹层完成布局。
4. `Guide` 解析目标、绘制遮罩、显示卡片。
5. 步骤结束或引导关闭时收起菜单。

## TabItem 切换后再显示引导

Vex 左侧侧边栏是一个 `TabControl`，里面有“文件”和“大纲”两个页签。新手引导里有一个很典型的动态流程：先在“视图”菜单里高亮“大纲”入口，然后自动切到左侧“大纲”页签，再说明大纲导航区域。

效果如下。

![](https://img1.dotnet9.com/2026/05/codewf-guide-vex-tabitem-guide.gif)

主窗口里，侧边栏目标是同一个 `Border`：

```xml
<Border
    x:Name="SidebarGuideTarget"
    IsVisible="{Binding Layout.IsSidebarVisible}">
    <TabControl
        prism:RegionManager.RegionName="{x:Static regions:RegionNames.ShellSidebarRegion}"
        SelectedIndex="{Binding Navigation.SelectedSideTabIndex, Mode=TwoWay}" />
</Border>
```

两个步骤都指向 `SidebarGuideTarget`：

```xml
<codewf:GuideStep
    x:Name="GuideSidebarFilesStep"
    Target="{Binding ElementName=SidebarGuideTarget}"
    Title="{i18n:I18n {x:Static l:VexL.GuideSidebarFilesTitle}}" />

<codewf:GuideStep
    x:Name="GuideSidebarOutlineStep"
    Target="{Binding ElementName=SidebarGuideTarget}"
    Title="{i18n:I18n {x:Static l:VexL.GuideSidebarOutlineTitle}}" />
```

区别在于进入步骤前先切换 TabItem：

```csharp
private void PrepareOnboardingGuideStep(IGuideStepOption step)
{
    if (DataContext is not MainWindowViewModel viewModel)
    {
        return;
    }

    if (ReferenceEquals(step, GuideSidebarFilesStep))
    {
        viewModel.Layout.ShowFiles();
        QueueOnboardingGuideRefresh();
        return;
    }

    if (ReferenceEquals(step, GuideSidebarOutlineStep))
    {
        viewModel.Layout.ShowOutline();
        QueueOnboardingGuideRefresh();
    }
}
```

`ShowFiles()` 和 `ShowOutline()` 会确保侧边栏可见，并切换 `SelectedSideTabIndex`：

```csharp
public void ShowOutline()
{
    IsSidebarVisible = true;
    SelectSidebarTab(1);
}

public void ShowFiles()
{
    IsSidebarVisible = true;
    SelectSidebarTab(0);
}
```

最后重新刷新引导位置：

```csharp
private void QueueOnboardingGuideRefresh()
{
    Dispatcher.UIThread.Post(OnboardingGuide.Refresh, DispatcherPriority.Background);
}
```

这一步很重要。TabItem 切换后，新内容需要等布局系统刷新才能得到正确尺寸。先切换业务状态，再把 `Guide.Refresh()` 投递到后台队列，能避免高亮区域还停在旧布局上。

## 首次启动只显示一次

Vex 里新手引导不是每次启动都弹出。配置里增加了：

```xml
<add key="HasSeenOnboardingGuide" value="false" />
```

窗口打开后检查这个状态：

```csharp
private void QueueFirstRunOnboardingGuide()
{
    if (_settingsStore is null || _settingsStore.Current.HasSeenOnboardingGuide == true)
    {
        return;
    }

    _settingsStore.Update(settings => settings with { HasSeenOnboardingGuide = true });
    Dispatcher.UIThread.Post(BeginOnboardingGuide, DispatcherPriority.Background);
}
```

第一次启动自动展示一次，并立即写回状态。之后用户可以从帮助菜单再次打开：

```xml
<MenuItem
    x:Name="BeginGuideMenuItem"
    Header="{i18n:I18n {x:Static l:VexL.OnboardingGuide}}"
    Click="BeginGuideMenuItem_OnClick" />
```

重新打开时从第一步开始：

```csharp
private void BeginOnboardingGuide()
{
    ConfigureOnboardingGuideTargets();
    TitleMenuView.CloseGuideMenus();
    OnboardingGuide.GoTo(0);
    OnboardingGuide.Show();
}
```

## 实现里几个容易忽略的点

**1. 目标延迟出现**

菜单项、Popup 内容、TabItem 内容都可能不是马上可见。`Guide` 里有 `TargetResolveDelay`，并且会在目标暂时不可见时重试几次。

**2. 弹层目标**

菜单项通常挂在 `PopupRoot` 或 `OverlayPopupHost` 下，不一定和主窗口内容在同一棵视觉树里。`Guide` 会判断目标是否来自弹层宿主，并用屏幕坐标换算高亮区域。

**3. 菜单 light-dismiss**

如果目标在菜单弹层里，用户点击引导卡片的“下一步”时，菜单可能先收到 light-dismiss 导致普通 `Button.Click` 丢失。`Guide` 对上一步、下一步、完成按钮额外处理了 `PointerPressed`，确保引导导航优先完成。

**4. 布局刷新**

目标控件 `LayoutUpdated`、窗口 `ClientSize` 变化时，都要重新计算高亮区域。否则窗口调整大小、侧栏展开收起以后，高亮框就会错位。

**5. 清理**

关闭引导时要停止定时器、关闭所有 Popup、解绑目标和窗口事件，并把焦点尽量还给引导打开前的控件。这类控件横跨页面和弹层，如果清理不完整，很容易留下残余遮罩。

## 小结

`Guide` 现在已经能覆盖桌面应用里常见的新手引导场景：

- 基础多步骤引导。
- 居中欢迎和结束步骤。
- 封面内容、自定义操作按钮。
- 默认圆点进度和文本进度。
- 遮罩、非模态、高亮间距和圆角。
- 菜单展开后引导 `MenuItem`。
- 二级菜单项引导。
- 切换 TabItem 后刷新并继续引导。
- 目标延迟出现后的等待和重试。
- 首次启动自动显示一次，帮助菜单再次打开。

这次在 Vex 里落地后，我更确定新手引导控件不能只做“静态按钮高亮”。桌面应用的真实入口经常藏在菜单、弹层和页签后面，`Guide` 要能跟业务状态一起走，才算真正可用。

## 仓库地址与感谢

感谢 AtomUI 项目提供 `Tour` 漫游式引导控件作为重要参考：

- AtomUI 控件仓库：[https://github.com/AtomUI/AtomUI](https://github.com/AtomUI/AtomUI)
- 本文控件仓库 CodeWF.AvaloniaControls：[https://github.com/dotnet9/CodeWF.AvaloniaControls](https://github.com/dotnet9/CodeWF.AvaloniaControls)
- Vex 项目仓库：[https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)

本文到此结束。
