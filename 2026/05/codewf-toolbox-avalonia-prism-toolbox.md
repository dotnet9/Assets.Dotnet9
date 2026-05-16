![](https://img1.dotnet9.com/2026/05/codewf-toolbox-avalonia-cover.svg)

今天聊一个更偏实战的 .NET 桌面项目：**CodeWF Toolbox，码匠工具箱**。

它不是一个 Demo。

它是一个用 **C# + Avalonia + Prism** 做出来的模块化桌面工具箱，面向开发者日常效率场景：格式转换、Base64、GUID、日期时间、JSON/YAML/XML、日志查看、Web 辅助、安全工具、网络工具、二维码、国际化资源管理等等。

我这次不是只看 README，而是把项目拉起来检查了一轮：

- 看了项目结构、模块边界和注释。
- 跑了 `dotnet build` 和 `dotnet test`。
- 启动了 Avalonia 桌面程序。
- 分别切了中文浅色、英文深色、日文水生主题。
- 截了静态图，也合成了主题与语言切换 GIF。

如果你是 .NET 开发者，这个项目很适合拿来学习一件事：

**Avalonia 不只是“能跨平台的 WPF 替代品”，它已经很适合做认真维护的桌面生产力工具。**

## 先看效果

这是中文浅色主题下的首页。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-home-light-zh-cn.png)

首页信息很直接：

- 当前可用工具数量：117。
- 功能模块数量：12。
- 当前运行平台：Windows x64。
- 入口包含源码仓库、开发文档、在线工具箱、关注反馈。

界面不是那种花哨的展示页，而是偏“工程工具”的组织方式。左侧是模块类别，第二列是当前模块下的工具列表，中间是工具内容区。

点进转换工具模块，可以看到更典型的工具箱布局。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-converter-light-zh-cn.png)

这个页面里，左侧列出了转换类工具：

- 时间戳转换。
- Base64 编解码。
- GUID 生成器。
- YAML 转 JSON。
- JSON 转 YAML。
- 图片转 ICO。
- 二维码生成。
- Base64 文件转换。
- 大小写转换。
- Color 选择器。
- 日期时间转换器。
- 整数进制转换器。
- JSON/TOML/XML/YAML 等格式互转。

这类工具看起来小，但开发者一天里反复会用到。

我更关心的是它背后的组织方式：这些工具没有全部硬塞进主窗口，也没有把每个页面做成一堆互相耦合的事件代码，而是通过模块、菜单服务、Region 导航和 ViewModel 分层来组织。

再看主题和语言切换。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-theme-language.gif)

这是英文深色主题。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-home-dark-en-us.png)

这是日文水生主题。

![](https://img1.dotnet9.com/2026/05/codewf-toolbox-home-aquatic-ja-jp.png)

这几张图能说明一个 Avalonia 项目很重要的事实：桌面应用不是只把按钮摆出来就完了。真正能长期用的工具，需要同时处理主题、字体、语言、布局、系统托盘、平台能力和发布细节。

## 这个项目适合学什么

如果你平时主要写 ASP.NET Core，看到桌面项目可能会觉得“离我有点远”。

但这个项目很适合拿来补一块能力：**如何用现代 .NET 做一个可维护的客户端应用**。

它里面至少有几个值得学习的点：

- Avalonia 跨平台桌面入口。
- Prism 模块化和 Region 导航。
- View / ViewModel / Service 的边界。
- 多语言资源组织。
- 主题资源和第三方控件主题同步。
- Linux/macOS/Windows 平台能力分流。
- 发布、裁剪、AOT 相关准备。
- 数据驱动的工具注册和执行框架。

很多人学 Avalonia 时只写一个窗口、几个按钮，然后就停在“能跑起来”。

这个项目更进一步：它开始处理真实产品会遇到的结构问题。

## 项目结构

先看项目大概分层。

```text
src/
  CodeWF.Toolbox/                      桌面应用入口、主窗口、设置、资源、发布配置
  CodeWF.Core/                         公共抽象、服务接口、菜单服务、Region 名称
  CodeWF.Controls/                     公共控件
  CodeWF.Modules.Converter/            转换工具模块
  CodeWF.Modules.ToolFramework/        数据驱动工具框架
  CodeWF.Modules.Development/          开发辅助模块
  CodeWF.Modules.LogViewer/            大文件日志查看模块
  CodeWF.Modules.XmlTranslatorManager/ 国际化资源管理模块
  CodeWF.Modules.Security/             安全工具模块
  CodeWF.Modules.Web/                  Web 辅助模块
  CodeWF.Modules.Network/              网络工具模块
  CodeWF.Modules.Media/                媒体工具模块
  CodeWF.Modules.Math/                 数学工具模块
  CodeWF.Modules.Measurement/          测量工具模块
  CodeWF.Modules.Text/                 文本工具模块
  CodeWF.Modules.Data/                 数据工具模块
  CodeWF.Toolbox.Tests/                单元测试工程
```

这是一种比较清楚的桌面工程组织方式。

`CodeWF.Toolbox` 只做壳：

- AppBuilder。
- PrismApplication。
- 主窗口。
- 登录窗口。
- 主题资源。
- 多语言入口。
- 模块目录。
- 发布配置。

`CodeWF.Core` 放公共抽象：

- `IToolMenuService`
- `IApplicationService`
- `IFileChooserService`
- `INotificationService`
- `IUserProfileService`
- `RegionNames`
- `TabControlRegionAdapter`

具体工具能力放在 `CodeWF.Modules.*` 里。

这个拆法的好处很直接：以后要加工具，不需要把主窗口越写越大，也不需要让所有模块互相引用。

## Avalonia 程序入口

桌面入口在 `Program.cs`，核心是典型的 Avalonia AppBuilder：

```csharp
public static AppBuilder BuildAvaloniaApp()
    => AppBuilder.Configure<App>()
        .UsePlatformDetect()
        .With(new Win32PlatformOptions
        {
            RenderingMode =
            [
                Win32RenderingMode.AngleEgl,
                Win32RenderingMode.Wgl,
                Win32RenderingMode.Software
            ],
            OverlayPopups = true,
        })
        .With(new X11PlatformOptions
        {
            OverlayPopups = true
        })
        .With(new AvaloniaNativePlatformOptions
        {
            OverlayPopups = true
        })
        .WithBundledAppFont()
        .UseReactiveUI(_ => { })
        .LogToTrace();
```

这里有几个点值得注意。

第一，`UsePlatformDetect()` 会根据平台选择后端。Windows 走 Win32，Linux 会用 X11/FreeDesktop 相关实现，macOS 走 Avalonia Native。

第二，Windows 渲染模式不是只配一个。它按 AngleEgl、Wgl、Software 兜底，这对复杂桌面环境更稳。

第三，项目额外用了 `WithBundledAppFont()`。这不是装饰，它对 Linux 很重要。

桌面应用到了 Linux 上，字体发现和字体回退经常是第一批问题。项目把 Noto Sans SC 打进资源里，能减少“中文变方块”“AvaloniaEdit 字体初始化太早”的问题。

## Prism 模块目录

`App.axaml.cs` 里通过 Prism 注册模块：

```csharp
protected override void ConfigureModuleCatalog(IModuleCatalog moduleCatalog)
{
    moduleCatalog.AddModule<MainModule>();
    moduleCatalog.AddModule<ToolFrameworkModule>();
    moduleCatalog.AddModule<ConverterModule>();
    moduleCatalog.AddModule<DevelopmentModule>();
    moduleCatalog.AddModule<SecurityModule>();
    moduleCatalog.AddModule<WebModule>();
    moduleCatalog.AddModule<MediaModule>();
    moduleCatalog.AddModule<NetworkModule>();
    moduleCatalog.AddModule<MathModule>();
    moduleCatalog.AddModule<MeasurementModule>();
    moduleCatalog.AddModule<TextModule>();
    moduleCatalog.AddModule<DataModule>();
    moduleCatalog.AddModule<LogViewerModule>();
    moduleCatalog.AddModule<XmlTranslatorManagerModule>();
    base.ConfigureModuleCatalog(moduleCatalog);
}
```

这就是模块化桌面程序的关键。

主壳不用知道每个工具页面怎么做。它只知道模块会注册菜单，会把 View 注册到 Prism Region 里。

一个模块大致做三件事：

1. 通过 `IToolMenuService` 注册菜单分组和菜单项。
2. 通过 `IRegionManager` 注册 View。
3. 在自己的项目里维护 View、ViewModel、I18n 资源和业务代码。

这样一来，工具箱可以持续加功能，但主窗口不会被撑爆。

## Region 导航

项目里有一个很关键的类：`TabControlRegionAdapter`。

Avalonia 里的 TabControl 本身不知道 Prism Region 应该怎么承载页面，所以项目自定义了适配器。

代码里的中文注释说得很清楚：

```csharp
// Prism 默认不认识 Ursa/Semi 主题下的 TabControl 该如何承载 Region，
// 这里把每个 View 包装成 TabItem，并把 ViewModel 暴露的语言 Key 绑定到页签头。
```

这类注释是必要的。

它不是解释“这行代码在赋值”，而是在解释框架之间的连接点：Prism、Avalonia、Semi/Ursa、TabControl、Region 是怎么连起来的。

如果你做过 WPF Prism，会知道 Region 这套思路很熟悉。Avalonia 里也能做，但需要把控件适配层补好。

## 菜单服务

工具菜单不是写死在 XAML 里的，而是通过 `IToolMenuService` 维护。

这样模块可以自己注册菜单：

```csharp
toolMenuService.AddGroup(groupName, icon);
toolMenuService.AddItem(
    title,
    groupName,
    description,
    viewName,
    icon,
    ToolStatus.Complete);
```

这比直接在主窗口写一堆按钮更可维护。

菜单服务负责统一管理：

- 分组。
- 分隔符。
- 菜单项。
- 图标。
- 描述。
- 工具状态。
- 导航目标。

项目里还有一个细节处理得不错：模块加载顺序变化时，子菜单可能先于分组注册，所以 `ToolMenuService` 会兜底创建分组。

这类代码看着不起眼，但对模块化系统很重要。模块越多，初始化顺序越不应该变成隐形 bug。

## ToolFramework：工具能力数据化

项目里最值得拆出来看的，其实是 `CodeWF.Modules.ToolFramework`。

它把很多工具抽象成 `ToolSpec`：

- 工具 ID。
- 分类。
- 标题。
- 描述。
- 输入字段。
- 输出字段。
- 执行函数。
- 是否自动运行。

例如 Base64、颜色转换、日期时间、JWT、User-Agent、MIME、二维码、MAC 地址、CIDR、数学计算等工具，都可以按规格注册。

这种方式很适合工具箱项目。

传统写法是每个工具一个页面，每个页面一套输入框、一套按钮、一套输出控件。工具一多，重复代码非常明显。

数据驱动写法把重复 UI 抽出来：

- 文本输入用 `Text` 字段。
- 多行输入用 `Multi` 字段。
- 数字输入用 `Number` 字段。
- 布尔项用 `Bool` 字段。
- 下拉项用 `Select` 字段。
- 文件选择用 `File` 或 `SaveFile` 字段。
- 图片输出用 `ImageOutput`。

工具算法只关心输入和输出。

这就是桌面工具箱很实用的一种架构：**UI 表单可复用，工具逻辑可扩展**。

## ViewModel 边界

Avalonia 项目很容易写成“窗口代码背后一把梭”。

这个项目大体上没有这么做。复杂业务主要放在 ViewModel 或 Service 里：

- 登录逻辑在 `LoginService` 和 `LoginViewModel`。
- 应用主题与语言在 `ApplicationService`。
- 用户画像在 `UserProfileService`。
- 工具搜索和菜单导航在 `MainMenuViewModel`。
- 大文件日志读取在 `LogViewerViewModel`。
- 工具运行在 `ToolViewModel` 和 `ToolAlgorithms`。

View 的 code-behind 主要做控件初始化和必要的 UI 事件桥接。

这就是 Avalonia + MVVM 应该保持的边界。

你不是不能写 code-behind，而是不要把业务规则写进去。

## 多语言资源

项目使用 `Lang.Avalonia.Json` 做 JSON 多语言资源。

每个主要模块都有自己的 `I18n` 目录，例如：

```text
CodeWF.Toolbox/I18n/
CodeWF.Modules.Converter/I18n/
CodeWF.Modules.Development/I18n/
CodeWF.Modules.LogViewer/I18n/
CodeWF.Modules.ToolFramework/I18n/
CodeWF.Modules.XmlTranslatorManager/I18n/
```

语言覆盖包括：

- `zh-CN`
- `zh-Hant`
- `en-US`
- `ja-JP`

XAML 中用资源 Key 绑定显示文本：

```xml
Title="{i18n:I18n {x:Static language:MainModule.Title}}"
```

ViewModel 和服务层也通过生成的 `Localization.*` 常量使用资源 Key，避免散落硬编码字符串。

这点对桌面应用很重要。

桌面工具一旦准备给更多开发者用，语言资源就不能只靠“先写中文，回头再说”。正确做法是从项目结构上就给每个模块留下资源入口。

我这次检查资源键时，也发现一个小问题：`CodeWF.Modules.Converter` 的日文资源比英文资源少一个键：

```text
Localization.NuoCheView.InputTitle
```

这不是大问题，但说明国际化资源最好在 CI 里加一个 key parity 检查。否则后续工具越来越多，某个语言漏一两个 Key 很难靠肉眼发现。

## 主题系统

项目使用 Avalonia 自身主题能力，同时接入了 Semi.Avalonia、Ursa、CodeWF 自己的主题资源。

应用服务里维护主题列表：

```csharp
public List<ThemeItem> Themes { get; } = new()
{
    new ThemeItem(Localization.CommonSettingView.Default, nameof(ThemeVariant.Default), ThemeVariant.Default),
    new ThemeItem(Localization.CommonSettingView.Light, nameof(ThemeVariant.Light), ThemeVariant.Light),
    new ThemeItem(Localization.CommonSettingView.Dark, nameof(ThemeVariant.Dark), ThemeVariant.Dark),
    new ThemeItem(Localization.CommonSettingView.Aquatic, nameof(SemiTheme.Aquatic), SemiTheme.Aquatic),
    new ThemeItem(Localization.CommonSettingView.Desert, nameof(SemiTheme.Desert), SemiTheme.Desert),
    new ThemeItem(Localization.CommonSettingView.Dusk, nameof(SemiTheme.Dusk), SemiTheme.Dusk),
    new ThemeItem(Localization.CommonSettingView.NightSky, nameof(SemiTheme.NightSky), SemiTheme.NightSky),
};
```

切换主题时，本质上是改：

```csharp
App.Instance.RequestedThemeVariant = themeObj?.Theme;
```

这就是 Avalonia 比较舒服的地方。

主题不是全靠你手写 `if dark then ...`，而是让资源字典、DynamicResource、控件库主题一起工作。

实际看下来，浅色和深色都比较稳。水生主题也能正常呈现，不过日文主题名称在顶部下拉里出现了截断，这个可以通过加宽 ComboBox 或调整显示文本来优化。

## Linux 支持

这个项目目标里明确要支持 Linux。

入口项目已经引用 `Avalonia.Desktop`，并且使用 `UsePlatformDetect()`。正常情况下，Linux 桌面相关依赖会通过 Avalonia 的依赖链带进来：

```text
Avalonia.Desktop
  -> Avalonia.X11
    -> Avalonia.FreeDesktop
```

所以一般不需要手动单独装 `Avalonia.FreeDesktop`。

Linux 发布更需要注意的是这几件事：

- 发布时选择非 Windows TFM，例如 `net11.0`。
- RuntimeIdentifier 使用 `linux-x64` 或 `linux-arm64`。
- 目标机器要有 Avalonia 需要的系统库，比如 X11、fontconfig 等。
- 如果是单文件、自包含、裁剪发布，要特别关注反射、动态绑定、多语言资源和第三方库。
- Avalonia.FreeDesktop 依赖的 `Tmds.DBus.Protocol` 版本要和发布产物一致。

项目里已经有 Linux 发布配置：

```xml
<TargetFramework>net11.0</TargetFramework>
<RuntimeIdentifier>linux-x64</RuntimeIdentifier>
<PublishReadyToRun>false</PublishReadyToRun>
```

公共发布配置里也启用了：

```xml
<SelfContained>true</SelfContained>
<PublishSingleFile>true</PublishSingleFile>
<PublishTrimmed>true</PublishTrimmed>
```

这说明它不是只在 README 上写“跨平台”，而是真的往跨平台发布方向走了。

不过裁剪发布不是白送的。项目构建时仍有一些 trim warning 和 nullable warning，后续最好继续清。

## 大文件日志查看

我比较喜欢项目里日志查看模块的方向。

大文件日志不是简单一个 `File.ReadAllText()` 塞进 TextBox。

代码里有注释：

```csharp
// 大文件不一次性读入内存，只维护行起始偏移，滚动时按可见窗口读取。
```

这就是工具项目应该有的工程意识。

日志文件可能几十 MB、几百 MB，甚至更大。你不能因为桌面程序方便，就粗暴读进内存。

它还处理了 tail 跟随：

```csharp
// 只有用户仍停留在末尾时才自动跟随，手动上翻或正在选择文本时保留当前位置。
```

这个细节很实用。

很多日志工具最烦的一点就是：你正在往上看，文件一追加，视图又被强制拉到底部。这个项目明确考虑了用户阅读行为。

## 注释质量

我检查了一轮注释。

整体判断是：必要的中文注释有，但还可以再统一。

好的注释集中在这些地方：

- AvaloniaEdit 字体初始化和 Linux 字体兜底。
- Prism TabControl Region Adapter。
- 大文件日志读取。
- tail 跟随行为。
- AOT/裁剪下避免反射绑定。
- Shell 创建后再弹登录窗口。
- 工具菜单导航边界。

这些注释是有价值的，因为它们解释的是“为什么要这么做”。

但项目里也有一些可以清理的地方：

- 少量英文示例注释还留在代码里。
- 有被注释掉的旧代码。
- 部分 `ignored` catch 没有说明为什么吞异常是可接受的。
- 自动生成文件的注释可以保留，但不应该作为业务注释质量的参考。

我的建议是：不要追求“中文注释越多越好”，而是把注释放在框架衔接、跨平台差异、AOT 裁剪、复杂算法、用户体验边界这些地方。

## 当前代码规范检查

我跑了这些命令：

```powershell
dotnet build CodeWF.Toolbox.slnx
dotnet test src/CodeWF.Toolbox.Tests/CodeWF.Toolbox.Tests.csproj --no-restore
dotnet format CodeWF.Toolbox.slnx --verify-no-changes --no-restore --verbosity minimal
```

结果是：

- 构建通过。
- 测试通过，当前 1 个测试通过。
- 格式校验未通过。

构建主要警告集中在：

- nullable 引用警告。
- 过时 API，例如 `TextBox.Watermark`、`RenderOptions.SetTextRenderingMode`。
- Avalonia XAML 运行时加载可达性警告。
- trim warning。
- .NET 11 preview SDK 提示。

格式校验主要集中在：

- 文件尾换行。
- using 顺序。
- 空白格式。
- 少量属性/语句排版。

这说明项目主线是通的，但“工程洁癖”还可以继续补。

如果后面要更正式地开源推广，我建议补三类检查：

1. `dotnet format --verify-no-changes` 进入 CI。
2. 多语言 JSON key parity 检查进入 CI。
3. Linux 发布产物冒烟测试进入 CI。

## 怎么新增一个工具模块

如果你想照这个项目加一个自己的模块，可以按这个流程走。

第一步，新建模块工程：

```text
src/CodeWF.Modules.YourModule/
```

第二步，引用公共层：

```xml
<ProjectReference Include="..\CodeWF.Core\CodeWF.Core.csproj" />
```

第三步，实现 Prism 模块：

```csharp
public class YourModule : IModule
{
    public YourModule(IToolMenuService toolMenuService)
    {
        var groupName = Localization.YourModule.Title;
        toolMenuService.AddGroup(groupName, Icons.YourIcon);
        toolMenuService.AddItem(
            Localization.YourTool.Title,
            groupName,
            Localization.YourTool.Description,
            nameof(YourToolView),
            Icons.YourIcon,
            ToolStatus.Complete);
    }

    public void OnInitialized(IContainerProvider containerProvider)
    {
        var regionManager = containerProvider.Resolve<IRegionManager>();
        regionManager.RegisterViewWithRegion<YourToolView>(RegionNames.ContentRegion);
    }

    public void RegisterTypes(IContainerRegistry containerRegistry)
    {
    }
}
```

第四步，把模块加入主程序的模块目录。

第五步，补齐：

- `Views`
- `ViewModels`
- `I18n/*.json`
- 图标
- 单元测试或最小冒烟测试

这就是 Prism 模块化桌面开发的基本套路。

## 什么时候用独立 View，什么时候用 ToolFramework

这个项目里其实有两类工具。

第一类是独立页面工具。

比如日志查看、XML 翻译管理、图片转 ICO，这类工具有较复杂的交互和状态，适合单独写 View 和 ViewModel。

第二类是数据驱动工具。

比如 Base64、颜色转换、URL 编码、JWT 解析、MIME 查询、随机端口生成，这类工具输入输出结构比较标准，适合接入 `ToolFramework`。

我的判断标准很简单：

- 如果工具是“几个输入字段 + 一个运行按钮 + 一个输出结果”，优先走 ToolFramework。
- 如果工具有复杂交互、文件生命周期、虚拟滚动、批量管理、动态列，单独做模块页面。

这样做能避免项目后期出现大量重复 XAML。

## 为什么我觉得这个项目值得 .NET 开发者看

因为它刚好踩在一个很实用的位置上。

它不是小到只有一个窗口的入门 Demo，也不是大到读起来压力很大的企业客户端。

它有真实应用需要的结构：

- 登录。
- 用户配置。
- 主题。
- 多语言。
- 模块化。
- 工具导航。
- 文件选择。
- 剪贴板。
- 系统启动项。
- Linux/macOS/Windows 分流。
- 发布配置。
- 文档。
- 单元测试。

同时它的业务又是开发者熟悉的工具箱场景。

你不用理解复杂行业模型，就能直接看懂它为什么需要这些工程组织方式。

## 写 Avalonia 项目的几个建议

结合这个项目，我给几个比较实际的建议。

第一，入口项目只做壳。

主程序不要知道所有工具的细节。壳负责启动、主题、多语言、导航、DI、模块目录。

第二，公共能力先抽接口。

文件选择、通知、菜单注册、用户配置、应用设置，这些都应该放在公共层，模块只依赖抽象。

第三，XAML 不要堆业务逻辑。

Avalonia 的绑定能力足够强，ViewModel 可以承担状态和行为，View code-behind 只处理控件生命周期和必要桥接。

第四，跨平台要早测。

不要等 Windows 版本写完才想 Linux。字体、文件路径、系统启动项、托盘、D-Bus、X11、发布裁剪，越晚测越难收。

第五，主题和语言要一起设计。

深色主题不是把背景改黑，多语言也不是把中文换成英文。控件宽度、长文本、下拉框、二维码、图标对比度，都要一起看。

## 最后

CodeWF Toolbox 现在已经是一个很有学习价值的 Avalonia 项目。

它目前不是完美状态，代码规范、nullable、过时 API、多语言完整性、裁剪警告都还有继续打磨的空间。

但它的方向是对的：

**用 C# 写真实桌面工具，用 Avalonia 做跨平台 UI，用 Prism 管模块边界，用数据驱动方式降低工具扩展成本。**

如果你想学 Avalonia，我不建议只停留在“画一个窗口”。

更值得练的是这种能力：

- 怎么把功能拆成模块。
- 怎么让工具持续增加而不污染主窗口。
- 怎么处理主题、语言和平台差异。
- 怎么把桌面应用当成一个长期维护的工程来做。

这也是我觉得 .NET 开发者应该重新重视桌面开发的原因。

Web 很重要，但不是所有工具都应该塞进浏览器。

本地桌面工具在文件访问、剪贴板、系统集成、离线使用、响应速度、长期驻留这些场景里，仍然非常有价值。

而 C# + Avalonia，正好给了我们一个继续写现代桌面工具的路径。
