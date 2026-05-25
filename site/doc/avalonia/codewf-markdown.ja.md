# CodeWF.Markdown

![CodeWF.Markdown](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-markdown.svg)

`CodeWF.Markdown` は、Avalonia 12 に基づく Markdown レンダリングコントロール、タイポグラフィテーマ、実行可能サンプルです。このリポジトリは `CodeWF.AvaloniaControls` から分割され、MarkdownViewer、テーマリソース、および関連テストのメンテナンスに特化しています。

## パッケージ一覧

| パッケージ | 説明 |
| --- | --- |
| `CodeWF.Markdown` | 完全な MarkdownViewer。一般的な Markdown 要素、コードハイライト、画像プレビュー、SVG/画像、数式レンダリング拡張、多言語リソース、インクリメンタルレンダリング、エクスポート画像ヘルパー、リッチ HTML クリップボードヘルパーをサポート。 |
| `CodeWF.Markdown.Themes` | デフォルトコントロールテンプレートと複数のタイポグラフィテーマ。 |

## ホストアプリ向けヘルパー

- `MarkdownDocumentExporter` と `ExportKind` は Markdown 文字列、Markdown ファイル、`MarkdownExportDocument` から PNG、選択可能なテキスト PDF、Word `.docx` へ出力する共通 API です。PDF 本文は選択・コピーでき、画像読み込みとラスタライズを再利用して相対ローカル画像、`data:image`、HTTP(S)、SVG/GIF/WebP を埋め込みます。
- `MarkdownHtmlClipboardExtensions`、`CopyKind`、`MarkdownSocialCopyProfiles` は WeChat Official Account、知乎、稀土掘金向けのリッチ HTML コピーを提供します。ホストアプリは Markdown、現在の組版テーマ、対象プラットフォームだけを渡し、共通ライブラリが inline HTML と `text/html`、macOS `public.html`、Windows `HTML Format` を生成します。
- 新しい公開先は `MarkdownSocialCopyProfile` で拡張できます。カスタム組版は `MarkdownExportStyle` またはアプリ登録済みテーマリソースから渡せます。

## インストール

```powershell
Install-Package CodeWF.Markdown
Install-Package CodeWF.Markdown.Themes
```

## 使用方法

`App.axaml` にテーマパッケージを導入：

```xml
<Application
    xmlns="https://github.com/avaloniaui"
    xmlns:markdown="https://codewf.com">
    <Application.Styles>
        <FluentTheme />
        <markdown:MarkdownThemes TypographyTheme="Simple" />
    </Application.Styles>
</Application>
```

ページ内で `MarkdownViewer` を使用：

```xml
<UserControl
    xmlns="https://github.com/avaloniaui"
    xmlns:md="https://codewf.com">
    <ScrollViewer
        HorizontalScrollBarVisibility="Disabled"
        VerticalScrollBarVisibility="Auto">
        <md:MarkdownViewer Markdown="{Binding Markdown}" />
    </ScrollViewer>
</UserControl>
```

## リポジトリ構成

```text
src/CodeWF.Markdown          MarkdownViewer クラスライブラリ
src/CodeWF.Markdown.Themes   コントロールテンプレートとタイポグラフィテーマ
src/CodeWF.Markdown.Sample   サンプルプロジェクト
tests/CodeWF.Markdown.Tests  レンダリングおよび差分サービステスト
```

## こんな方におすすめ

- Avalonia アプリケーションで Markdown コンテンツを直接レンダリングしたい場合。
- ドキュメント、変更履歴、AI 応答、ヘルプセンター向けに統一されたタイポグラフィテーマが必要な場合。
- 画像、SVG、コードハイライト、多言語リソース、インクリメンタルレンダリング、エクスポート画像埋め込み、Web エディターへのリッチ HTML 貼り付けをサポートしたい場合。
- デスクトップ上でさまざまな Markdown コンテンツの表示をサンプルプロジェクトで確認したい場合。

## ビルド

```powershell
dotnet restore CodeWF.Markdown.slnx
dotnet build CodeWF.Markdown.slnx --no-restore
```

## リポジトリ

- GitHub：[https://github.com/dotnet9/CodeWF.Markdown](https://github.com/dotnet9/CodeWF.Markdown)
- NuGet：[https://www.nuget.org/packages/CodeWF.Markdown](https://www.nuget.org/packages/CodeWF.Markdown)
