# CodeWF.Toolbox

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

`CodeWF.Toolbox` は、Avalonia + Prism ベースのモジュール式デスクトップツールボックスで、開発者の日常的な作業効率化を対象としています。シェル、共通サービス、機能モジュールを分離して構成しているため、新しいツールページを追加しても保守しづらい単体巨大アプリになりにくい設計です。

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

![CodeWF.Toolbox preview](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.svg)

![CodeWF.Toolbox screenshot](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.png)

## 特徴

- Avalonia UI、Prism のモジュールカタログ、依存性注入、Region ナビゲーションをベースに構成。
- 現在のモジュールは AI ツール、フォーマット変換、開発補助、XML 翻訳管理をカバー。
- Native AOT 向けのプロジェクト構成と公開設定を保持。
- 多言語リソースは `en-US`、`zh-CN`、`zh-Hant`、`ja-JP` を用意。
- メニューとページ構成は今後のツール追加に対応しやすい形になっています。

## 内蔵ツール

| グループ | 説明 |
| --- | --- |
| AI | タイトルから slug 生成、プロンプト翻訳、チャット補助などのツールを提供。 |
| フォーマット変換 | JSON/YAML や画像からアイコンへの変換ツールを提供。 |
| 開発補助 | JSON/YAML の整形など、日常的な開発用ユーティリティを提供。 |
| XML 翻訳管理 | XML 国際化リソースの比較、マージ、保守を実行。 |

## ディレクトリ構成

```text
src/
  CodeWF.Toolbox/                    メインデスクトップアプリ、シェル、設定、リソース
  CodeWF.Core/                       共通抽象、サービス、Region 定義
  CodeWF.Controls/                   共通コントロール
  CodeWF.Modules.AI/                 AI ツールモジュール
  CodeWF.Modules.Converter/          フォーマット変換モジュール
  CodeWF.Modules.Development/        開発補助モジュール
  CodeWF.Modules.XmlTranslatorManager/ XML 国際化管理モジュール
tests/
  AvaloniaAotDemo/                   Avalonia AOT デモ
  WinFormsAotDemo/                   WinForms AOT デモ
  ConsoleAotDemo/                    Console AOT デモ
docs/
  assets/                            個別 SVG 図
```

## クイックスタート

環境要件：

- .NET 9 SDK。
- Avalonia がサポートするデスクトップ環境。現時点では Windows 7、Windows Server 2019、Windows 10、Windows 11、macOS 11+ での動作を確認しています。

```powershell
dotnet restore CodeWF.Toolbox.sln
dotnet build CodeWF.Toolbox.sln
dotnet run --project src/CodeWF.Toolbox/CodeWF.Toolbox.csproj
```

## リポジトリ

- GitHub：[https://github.com/dotnet9/CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)
