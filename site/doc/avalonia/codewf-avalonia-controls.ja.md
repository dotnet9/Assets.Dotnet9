# CodeWF.AvaloniaControls

![CodeWF.AvaloniaControls](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-avalonia-controls.svg)

`CodeWF.AvaloniaControls` は、.NET 11 と Avalonia 12 に基づくオープンソースのコントロールリポジトリであり、再利用可能なクラスライブラリ、テーマリソースパック、および直接実行可能なサンプルプロジェクトを含んでいます。汎用コントロールの集約やコントロールテンプレートの検証に適しており、Avalonia プロジェクトに対して統一された基本 UI 機能を提供するのにも適しています。

## パッケージ一覧

| パッケージ | 説明 |
| --- | --- |
| `CodeWF.AvaloniaControls` | 汎用カスタムコントロール。 |
| `CodeWF.AvaloniaControls.Themes` | コントロールテンプレートとテーマリソースパック。 |

## インストール

```powershell
Install-Package CodeWF.AvaloniaControls
Install-Package CodeWF.AvaloniaControls.Themes
```

## リポジトリ構造

```text
src/
  CodeWF.AvaloniaControls/              汎用コントロールライブラリ
  CodeWF.AvaloniaControls.Themes/       コントロールテンプレートとテーマリソース
  CodeWF.AvaloniaControls.Showcase/     コントロールショーケース
  CodeWF.AvaloniaControls.FluentStarterDemo/ 軽量スタートウィンドウサンプル
docs/                                   スクリーンショット、GIF、ドキュメントリソース
artifacts/                              パッケージ出力
publish/                                サンプル公開ディレクトリ
```

## 注目ポイント

- Avalonia コントロールとテーマリソースを独立した NuGet パッケージに分割する方法。
- 実行可能なショーケースを維持し、コントロールの動作、スタイル、テーマ切り替えを可視化する方法。
- `Guide` を使ってデスクトップアプリの初回案内、機能ツアー、局所的なヒントを実装する方法。
- 一つのリポジトリ内でライブラリ、デモプログラム、パッケージスクリプト、公開スクリプトを同時に整理する方法。
- コントロールライブラリで商用パッケージへの依存を回避し、オープンソースプロジェクトの配布可能性を維持する方法。

## Guide コントロール

`Guide` は、Avalonia デスクトップアプリで初回案内、機能ツアー、局所的なヒントを表示するためのコントロールです。静的なボタンの強調だけでなく、デスクトップソフトウェアでよく使われる動的な入口にも対応します。メニュー、階層メニュー、`Popup`、`Flyout`、スクロール領域、`TabItem` の切り替え、遅れて表示されるターゲット、ウィンドウサイズ変更を扱えます。

- `GuideStep` は各ステップを異なるターゲットコントロールに関連付けられ、ターゲットなしの中央説明も表示できます。
- `GuideOverlay` はマスクの切り抜き、強調領域の角丸、ターゲットとの間隔、カード配置を処理します。
- `TargetResolveDelay` と `StepOpening` により、ステップ開始前にメニューを開いたりタブを切り替えたりしてからターゲットを解決できます。
- `DefaultGuideIndicator`、`TextGuideIndicator`、非モーダルヒント、レイアウト変更後の位置更新に対応します。
- Showcase には、基本的な複数ステップ案内、カバー内容、カスタムボタン、テキスト進捗、マスクなしヒントの例があります。

## サンプルプロジェクト

- `CodeWF.AvaloniaControls.Showcase`：コントロール機能、テーマ切り替え、サンプルページを集中的に表示するために使用。
- `CodeWF.AvaloniaControls.FluentStarterDemo`：軽量スタートウィンドウと基本スタイルの適用をデモンストレーションするために使用。

## ビルドとパッケージング

```powershell
dotnet restore CodeWF.AvaloniaControls.slnx
dotnet build CodeWF.AvaloniaControls.slnx --no-restore
.\pack.bat
```

## リポジトリ

- GitHub：[https://github.com/dotnet9/CodeWF.AvaloniaControls](https://github.com/dotnet9/CodeWF.AvaloniaControls)
- NuGet：[https://www.nuget.org/packages/CodeWF.AvaloniaControls](https://www.nuget.org/packages/CodeWF.AvaloniaControls)
