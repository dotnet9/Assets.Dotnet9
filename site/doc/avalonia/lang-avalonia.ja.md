# Lang.Avalonia

![Lang.Avalonia](https://img1.dotnet9.com/site/doc/avalonia/imgs/lang-avalonia.svg)

`Lang.Avalonia` は、Avalonia UI 向けのプラグイン式多言語ライブラリです。コアパッケージは XAML マークアップ拡張、バインディングリフレッシュフロー、コンバーター、`I18nManager`、`ILangPlugin` コントラクトを提供します。リソースの読み込みは JSON、XML、RESX の各プラグインが担当し、Source Generator を使って強型別のリソースキーを生成することもできます。

## パッケージ一覧

| パッケージ | 説明 |
| --- | --- |
| `Lang.Avalonia` | コア機能。マークアップ拡張、バインディングリフレッシュ、リソース管理を含む。 |
| `Lang.Avalonia.Json` | JSON リソースプロバイダー。 |
| `Lang.Avalonia.Xml` | XML リソースプロバイダー。 |
| `Lang.Avalonia.Resx` | RESX リソースプロバイダー。 |
| `Lang.Avalonia.Analysis` | コンパイル時に `AdditionalFiles` から強型別キーを生成。 |

## 機能

- AXAML と C# の統一された多言語エントリポイント。
- `I18nManager.Instance.Culture` で実行時に言語を切り替え。
- JSON、XML、RESX リソースプロバイダーは `ILangPlugin` を統一的に実装。
- デフォルトカルチャのフォールバックと元のキーのフォールバックをサポート。
- T4 テンプレートまたは `Lang.Avalonia.Analysis` による強型別リソースキーの生成をサポート。
- 固定カルチャプレビュー、書式設定文字列、動的バインディングパラメータをサポート。

## クイックスタート

コアパッケージと1つのリソースプロバイダーをインストールします:

```bash
dotnet add package Lang.Avalonia
dotnet add package Lang.Avalonia.Json
```

`I18n` ディレクトリに言語ファイルを作成し、JSON/XML ファイルを出力ディレクトリにコピーします:

```xml
<ItemGroup>
  <None Update="I18n\*.json" CopyToOutputDirectory="PreserveNewest" />
</ItemGroup>
```

アプリケーション起動時にプラグインを登録します:

```csharp
using Lang.Avalonia;
using Lang.Avalonia.Json;
using System.Globalization;

I18nManager.Instance.Register(new JsonLangPlugin(), new CultureInfo("en-US"), out var error);
if (!string.IsNullOrWhiteSpace(error))
{
    // 初期化エラーを記録または表示
}
```

AXAML で生成された定数を使用します:

```xml
xmlns:c="https://codewf.com"
xmlns:mainLangs="clr-namespace:Localization.Main"

<SelectableTextBlock Text="{c:I18n {x:Static mainLangs:MainView.Title}}" />
<SelectableTextBlock Text="{c:I18n {x:Static mainLangs:MainView.Title}, CultureName=ja-JP}" />
```

C# で同じキーを使用します:

```csharp
var title = I18nManager.Instance.GetResource(Localization.Main.MainView.Title);
var englishTitle = I18nManager.Instance.GetResource(Localization.Main.MainView.Title, "en-US");
```

実行時に言語を切り替えます:

```csharp
I18nManager.Instance.Culture = new CultureInfo("zh-CN");
```

## こんな方におすすめ

- Avalonia プロジェクトで JSON、XML、RESX の多言語リソースを同時にサポートする必要がある場合。
- 統一された API で AXAML と C# から翻訳文字列を読み取りたい場合。
- T4 または Source Generator で強型別キーを生成し、ハードコードされた文字列を減らしたい場合。
- 言語切り替え時に UI バインディングが自動更新されることを期待する場合。

## リポジトリ

- GitHub：[https://github.com/dotnet9/Lang.Avalonia](https://github.com/dotnet9/Lang.Avalonia)
- NuGet：[https://www.nuget.org/packages/Lang.Avalonia](https://www.nuget.org/packages/Lang.Avalonia)