# CodeWF.Markdown

![CodeWF.Markdown](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-markdown.svg)

`CodeWF.Markdown` は、Avalonia 12 に基づく Markdown レンダリングコントロール、タイポグラフィテーマ、実行可能サンプルです。このリポジトリは `CodeWF.AvaloniaControls` から分割され、MarkdownViewer、テーマリソース、および関連テストのメンテナンスに特化しています。

## パッケージ一覧

| パッケージ | 説明 |
| --- | --- |
| `CodeWF.Markdown` | 完全な MarkdownViewer。一般的な Markdown 要素、コードハイライト、画像プレビュー、SVG/画像、数式レンダリング拡張、多言語リソース、インクリメンタルレンダリング、エクスポート画像ヘルパー、リッチ HTML クリップボードヘルパーをサポート。 |
| `CodeWF.Markdown.Themes` | デフォルトコントロールテンプレートと複数のタイポグラフィテーマ。 |

## ホストアプリ向けヘルパー

- `MarkdownImageSourceLoader` と `MarkdownImageRasterizer` は PDF、PNG、Word などのエクスポート処理で再利用できます。相対ローカル画像、`data:image`、HTTP(S)、SVG/GIF/WebP の PNG 正規化に対応し、ホストアプリが画像をエクスポートファイルへ埋め込みやすくします。
- `MarkdownHtmlClipboard` は `text/html`、macOS `public.html`、Windows ネイティブ `HTML Format` を書き込みます。Windows ペイロードは UTF-8 CF_HTML バイトオフセットを使うため、WeChat Official Account、知乎、稀土掘金などの Web エディターへリッチ HTML として貼り付けやすく、HTML がそのままテキスト表示される問題を避けられます。

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
