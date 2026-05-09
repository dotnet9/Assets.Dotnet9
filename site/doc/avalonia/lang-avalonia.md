# Lang.Avalonia

![Lang.Avalonia](https://img1.dotnet9.com/site/doc/avalonia/imgs/lang-avalonia.svg)

`Lang.Avalonia` 是面向 Avalonia UI 的插件化多语言库。核心包提供 XAML 标记扩展、绑定刷新流程、转换器、`I18nManager` 和 `ILangPlugin` 契约；资源加载由 JSON、XML、RESX 插件实现，也可以使用 Source Generator 生成强类型资源 Key。

## 包线

| 包 | 说明 |
| --- | --- |
| `Lang.Avalonia` | 核心能力，包含标记扩展、绑定刷新和资源管理。 |
| `Lang.Avalonia.Json` | JSON 资源提供器。 |
| `Lang.Avalonia.Xml` | XML 资源提供器。 |
| `Lang.Avalonia.Resx` | RESX 资源提供器。 |
| `Lang.Avalonia.Analysis` | 从 `AdditionalFiles` 编译期生成强类型 Key。 |

## 功能

- 统一的 AXAML 与 C# 多语言入口。
- 通过 `I18nManager.Instance.Culture` 运行时切换语言。
- JSON、XML、RESX 资源提供器统一实现 `ILangPlugin`。
- 支持默认文化回退和原始 Key 回退。
- 支持 T4 模板或 `Lang.Avalonia.Analysis` 生成强类型资源 Key。
- 支持固定文化预览、格式化字符串和动态绑定参数。

## 快速开始

安装核心包和一个资源提供器：

```bash
dotnet add package Lang.Avalonia
dotnet add package Lang.Avalonia.Json
```

在 `I18n` 目录创建语言文件，并将 JSON/XML 文件复制到输出目录：

```xml
<ItemGroup>
  <None Update="I18n\*.json" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

应用启动时注册插件：

```csharp
using Lang.Avalonia;
using Lang.Avalonia.Json;
using System.Globalization;

I18nManager.Instance.Register(new JsonLangPlugin(), new CultureInfo("en-US"), out var error);
if (!string.IsNullOrWhiteSpace(error))
{
    // 记录或展示初始化错误。
}
```

AXAML 中使用生成的常量：

```xml
xmlns:c="https://codewf.com"
xmlns:mainLangs="clr-namespace:Localization.Main"

<SelectableTextBlock Text="{c:I18n {x:Static mainLangs:MainView.Title}}" />
<SelectableTextBlock Text="{c:I18n {x:Static mainLangs:MainView.Title}, CultureName=ja-JP}" />
```

C# 中使用相同 Key：

```csharp
var title = I18nManager.Instance.GetResource(Localization.Main.MainView.Title);
var englishTitle = I18nManager.Instance.GetResource(Localization.Main.MainView.Title, "en-US");
```

运行时切换语言：

```csharp
I18nManager.Instance.Culture = new CultureInfo("zh-CN");
```

## 适合关注

- Avalonia 项目需要同时支持 JSON、XML 或 RESX 多语言资源。
- 希望通过统一 API 在 AXAML 与 C# 中读取翻译字符串。
- 希望用 T4 或 Source Generator 生成强类型 Key，减少硬编码字符串。
- 希望语言切换时界面绑定可以自动刷新。

## 仓库

- GitHub：[https://github.com/dotnet9/Lang.Avalonia](https://github.com/dotnet9/Lang.Avalonia)
- NuGet：[https://www.nuget.org/packages/Lang.Avalonia](https://www.nuget.org/packages/Lang.Avalonia)
