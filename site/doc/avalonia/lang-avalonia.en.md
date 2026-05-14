# Lang.Avalonia

![Lang.Avalonia](https://img1.dotnet9.com/site/doc/avalonia/imgs/lang-avalonia.svg)

`Lang.Avalonia` is a plugin-based multilingual library for Avalonia UI. The core package provides XAML markup extensions, binding refresh flow, converters, `I18nManager` and `ILangPlugin` contract; resource loading is implemented by JSON, XML, RESX plugins, and you can also use Source Generator to generate strongly typed resource keys.

## Packages

| Package | Description |
| --- | --- |
| `Lang.Avalonia` | Core capabilities, including markup extensions, binding refresh and resource management. |
| `Lang.Avalonia.Json` | JSON resource provider. |
| `Lang.Avalonia.Xml` | XML resource provider. |
| `Lang.Avalonia.Resx` | RESX resource provider. |
| `Lang.Avalonia.Analysis` | Generates strongly typed keys at compile time from `AdditionalFiles`. |

## Features

- Unified multilingual entry point for AXAML and C#.
- Switch language at runtime via `I18nManager.Instance.Culture`.
- JSON, XML, RESX resource providers uniformly implement `ILangPlugin`.
- Supports default culture fallback and original key fallback.
- Supports T4 templates or `Lang.Avalonia.Analysis` to generate strongly typed resource keys.
- Supports fixed culture preview, formatted strings, and dynamic binding parameters.

## Quick Start

Install the core package and a resource provider:

```bash
dotnet add package Lang.Avalonia
dotnet add package Lang.Avalonia.Json
```

Create language files in the `I18n` directory and copy the JSON/XML files to the output directory:

```xml
<ItemGroup>
  <None Update="I18n\*.json" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

Register the plugin when the application starts:

```csharp
using Lang.Avalonia;
using Lang.Avalonia.Json;
using System.Globalization;

I18nManager.Instance.Register(new JsonLangPlugin(), new CultureInfo("en-US"), out var error);
if (!string.IsNullOrWhiteSpace(error))
{
    // Log or display initialization error.
}
```

Use the generated constants in AXAML:

```xml
xmlns:c="https://codewf.com"
xmlns:mainLangs="clr-namespace:Localization.Main"

<SelectableTextBlock Text="{c:I18n {x:Static mainLangs:MainView.Title}}" />
<SelectableTextBlock Text="{c:I18n {x:Static mainLangs:MainView.Title}, CultureName=ja-JP}" />
```

Use the same keys in C#:

```csharp
var title = I18nManager.Instance.GetResource(Localization.Main.MainView.Title);
var englishTitle = I18nManager.Instance.GetResource(Localization.Main.MainView.Title, "en-US");
```

Switch language at runtime:

```csharp
I18nManager.Instance.Culture = new CultureInfo("zh-CN");
```

## Suitable For

- Avalonia projects that need to support JSON, XML or RESX multilingual resources simultaneously.
- Want to read translation strings in both AXAML and C# through a unified API.
- Want to generate strongly typed keys using T4 or Source Generator to reduce hardcoded strings.
- Want UI bindings to automatically refresh when language switches.

## Repository

- GitHub: [https://github.com/dotnet9/Lang.Avalonia](https://github.com/dotnet9/Lang.Avalonia)
- NuGet: [https://www.nuget.org/packages/Lang.Avalonia](https://www.nuget.org/packages/Lang.Avalonia)