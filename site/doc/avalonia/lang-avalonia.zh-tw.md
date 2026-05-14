# Lang.Avalonia

![Lang.Avalonia](https://img1.dotnet9.com/site/doc/avalonia/imgs/lang-avalonia.svg)

`Lang.Avalonia` 是面向 Avalonia UI 的插件化多語系套件。核心套件提供 XAML 標記延伸、繫結重新整理流程、轉換器、`I18nManager` 與 `ILangPlugin` 合約；資源載入由 JSON、XML、RESX 插件實作，也可使用 Source Generator 產生強型別資源 Key。

## 套件陣列

| 套件 | 說明 |
| --- | --- |
| `Lang.Avalonia` | 核心能力，包含標記延伸、繫結重新整理與資源管理。 |
| `Lang.Avalonia.Json` | JSON 資源提供器。 |
| `Lang.Avalonia.Xml` | XML 資源提供器。 |
| `Lang.Avalonia.Resx` | RESX 資源提供器。 |
| `Lang.Avalonia.Analysis` | 從 `AdditionalFiles` 編譯期產生強型別 Key。 |

## 功能

- 統一的 AXAML 與 C# 多語系進入點。
- 透過 `I18nManager.Instance.Culture` 在執行階段切換語言。
- JSON、XML、RESX 資源提供器統一實作 `ILangPlugin`。
- 支援預設文化回退與原始 Key 回退。
- 支援 T4 範本或 `Lang.Avalonia.Analysis` 產生強型別資源 Key。
- 支援固定文化預覽、格式化字串與動態繫結參數。

## 快速開始

安裝核心套件與一個資源提供器：

```bash
dotnet add package Lang.Avalonia
dotnet add package Lang.Avalonia.Json
```

在 `I18n` 目錄建立語言檔案，並將 JSON/XML 檔案複製到輸出目錄：

```xml
<ItemGroup>
  <None Update="I18n\*.json" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

應用程式啟動時註冊插件：

```csharp
using Lang.Avalonia;
using Lang.Avalonia.Json;
using System.Globalization;

I18nManager.Instance.Register(new JsonLangPlugin(), new CultureInfo("en-US"), out var error);
if (!string.IsNullOrWhiteSpace(error))
{
    // 記錄或顯示初始化錯誤。
}
```

AXAML 中使用產生的常數：

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

執行階段切換語言：

```csharp
I18nManager.Instance.Culture = new CultureInfo("zh-CN");
```

## 適合留意

- Avalonia 專案需要同時支援 JSON、XML 或 RESX 多語系資源。
- 希望透過統一 API 在 AXAML 與 C# 中讀取翻譯字串。
- 希望用 T4 或 Source Generator 產生強型別 Key，減少硬編碼字串。
- 希望語言切換時介面繫結可以自動重新整理。

## 儲存庫

- GitHub：[https://github.com/dotnet9/Lang.Avalonia](https://github.com/dotnet9/Lang.Avalonia)
- NuGet：[https://www.nuget.org/packages/Lang.Avalonia](https://www.nuget.org/packages/Lang.Avalonia)