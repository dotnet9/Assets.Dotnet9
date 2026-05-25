# Vex

![Vex](https://img1.dotnet9.com/2026/05/vex-avalonia-cover.svg)

`Vex` は .NET 10、Avalonia 12、Prism、Semi.Avalonia、Ursa.Avalonia で構築されたクロスプラットフォーム Markdown デスクトップエディターです。Markdown ソース編集、ネイティブプレビュー、ファイル管理、検索/置換、エクスポート、公開プラットフォーム向けコピーに重点を置いています。

リポジトリ：[https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)

Release v1.0.0：[https://github.com/dotnet9/Vex/releases/tag/v1.0.0](https://github.com/dotnet9/Vex/releases/tag/v1.0.0)

![Vex メインウィンドウ](https://img1.dotnet9.com/2026/05/vex-main-window.png)

## プロジェクトの位置づけ

- 無料でオープンソースの Markdown デスクトップ執筆ツールです。まずはソース編集とネイティブプレビューの流れを安定させる方針です。
- 左側にファイル一覧とアウトライン、中央に AvaloniaEdit の Markdown エディター、右側に CodeWF.Markdown のプレビューを配置します。
- 表示メニューではサイドバー、アウトライン、文書一覧、ソースモード、行番号、ステータスバー、全画面、常に手前を制御できます。
- ファイルメニューは新規、開く、フォルダーを開く、最近使った文書、エンコードを指定して開き直す、WeChat Official Account/知乎/稀土掘金へのコピー、保存、エクスポート、印刷、プロパティ、削除、閉じるを扱います。
- HTML、PNG、画像型 PDF、Word `.docx` のエクスポートに対応します。PNG/PDF/Word は `CodeWF.Markdown` の `MarkdownDocumentExporter` を再利用し、相対ローカル画像、`data:image`、HTTP(S)、SVG/GIF/WebP 変換も処理します。PDF と Word は画像リソースを埋め込むため、オフライン共有後も表示できます。
- WeChat Official Account、知乎、稀土掘金へのコピーは、現在の Markdown、組版テーマ、対象プラットフォームを `CodeWF.Markdown` の `MarkdownHtmlClipboardExtensions` に渡し、現在の組版テーマを反映したリッチ HTML クリップボード形式を書き込みます。
- 検索/置換は大文字小文字、単語単位、正規表現、件数表示、長文書向けのデバウンススキャンをサポートします。
- テーマ色、Markdown 組版テーマ、コンパクトレイアウト、言語切り替えはヘルプメニューにまとまっています。
- 簡体字中国語、繁体字中国語、英語、日本語の UI とヘルプ文書を含みます。

## 主な機能

| 機能 | 説明 |
| --- | --- |
| Markdown 編集 | AvaloniaEdit ベース。スマート改行、現在行の強調、ソースモード、行番号、よく使う書式挿入に対応。 |
| ネイティブプレビュー | CodeWF.Markdown ベース。WebView に依存せず、見出し、リスト、表、コードブロック、タスクリスト、ローカル画像、SVG、GIF に対応。 |
| ファイルワークフロー | 新規、単一ファイルを開く、フォルダーを開く、最近使った文書、ドラッグ&ドロップ、保存、名前を付けて保存、外部変更検出、再読み込み。 |
| アウトライン | Markdown 見出しからアウトラインを生成し、選択した位置へ移動できます。 |
| 検索/置換 | 大文字小文字、単語単位、正規表現、件数表示、次を置換、すべて置換。 |
| エクスポート | HTML、PNG、画像型 PDF、Word `.docx`、印刷プレビュー。PNG/PDF/Word は `CodeWF.Markdown` の共通エクスポート API を再利用し、PDF/Word はローカル、`data:image`、HTTP(S)、SVG/GIF/WebP 画像を埋め込みます。 |
| 公開向けコピー | `CodeWF.Markdown` の共通リッチ HTML クリップボード API を使って WeChat Official Account、知乎、稀土掘金向けに書き込みます。Windows `HTML Format` は UTF-8 CF_HTML バイトを使い、現在の組版テーマを適用します。 |
| 多言語 | Lang.Avalonia.Json により簡体字中国語、繁体字中国語、英語、日本語を提供。 |
| オンボーディング | Guide ステップがメニュー項目、TabItem、エディター、プレビュー領域を指せます。 |
| パッケージング | Windows、Linux、macOS の複数 RID、リリース zip、SHA256、任意の MSIX パッケージング。 |

## デモ

![編集とライブプレビュー](https://img1.dotnet9.com/2026/05/vex-edit-vex-article-live.gif)

![アウトラインナビゲーション](https://img1.dotnet9.com/2026/05/vex-outline-vex-article.gif)

![ソースモード](https://img1.dotnet9.com/2026/05/vex-source-mode-vex-article.gif)

![ファイルメニューとエクスポート](https://img1.dotnet9.com/2026/05/vex-file-export-menu.gif)

![検索と置換](https://img1.dotnet9.com/2026/05/vex-find-replace-vex-article.gif)

![テーマと言語](https://img1.dotnet9.com/2026/05/vex-theme-typography-language.gif)

![オンボーディング](https://img1.dotnet9.com/2026/05/codewf-guide-vex-onboarding-flow.gif)

## 技術スタック

- .NET 10
- Avalonia 12
- Prism.DryIoc.Avalonia
- ReactiveUI.Avalonia
- Semi.Avalonia
- Ursa.Avalonia
- AvaloniaEdit
- CodeWF.Markdown
- CodeWF.AvaloniaControls
- CodeWF.EventBus
- Lang.Avalonia.Json

## クイックスタート

必要環境：

- .NET 10 SDK

```powershell
git clone https://github.com/dotnet9/Vex.git
cd Vex
dotnet restore Vex.slnx
dotnet build Vex.slnx
dotnet run --project src/Vex/Vex.csproj
```

リリース成果物を作成：

```powershell
.\publish_vex_all.bat --package
```

## リポジトリとリリース

- リポジトリ：[https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)
- リリース：[https://github.com/dotnet9/Vex/releases/tag/v1.0.0](https://github.com/dotnet9/Vex/releases/tag/v1.0.0)
