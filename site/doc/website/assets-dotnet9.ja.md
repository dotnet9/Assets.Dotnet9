# Assets.Dotnet9

![Assets.Dotnet9](https://img1.dotnet9.com/site/doc/website/imgs/assets-dotnet9.svg)

`Assets.Dotnet9` は、CodeWF サイトのリソース兼コンテンツリポジトリです。画像ディレクトリにとどまらず、ファイル型コンテンツライブラリとして、記事、プロジェクトドキュメント、ツールメタデータ、タイムライン、フレンドリンク、サイトのMarkdownページ、添付画像などをここで管理します。

## 注目すべきポイント

- `dotnet9.com` がどのようにコンテンツデータをWebアプリケーションのソースコードから分離しているかを知りたい方。
- 年、月、カテゴリ、プロジェクトディレクトリごとにMarkdownと画像リソースを整理したい方。
- CodeWF サイトに新しい記事、プロジェクトの説明、ツールエントリ、サイト設定を追加したい方。

## ディレクトリ構成

```text
2019/ ... 2026/      年・月ごとに記事と添付画像を整理
site/
  about.md           アバウトページの内容
  Privacy.md         プライバシーポリシーページの内容
  albums.json        特集メタデータ
  categories.json    カテゴリメタデータ
  friend-links.json  フレンドリンクメタデータ
  timelines.json     タイムラインデータ
  doc/               プロジェクトセンターナビゲーション、本文、SVGカバー
  tools/             オンラインツールのメタデータとプレビュー画像
  pays/              投げ銭ページのリソース
  banners/           ページバナーリソース
  favicon/           サイトアイコンリソース
```

## コンテンツ管理の原則

| 原則 | 説明 |
| --- | --- |
| パスの安定性 | 記事、画像、ドキュメント、ツールのスラッグは可能な限り長期にわたり変更せず、過去のリンク切れを防止。 |
| メタデータの完全性 | 記事のFront Matter、カテゴリ、特集、プロジェクトナビゲーション、ツールJSONは同期して管理する必要あり。 |
| 明確な命名 | 画像、SVG、Markdownには意味のあるファイル名を使用し、検索や再利用を容易に。 |
| レビュー可能 | コンテンツの変更は主にテキストファイルであり、Git diff やプルリクエストレビューに適している。 |

## CodeWFとの関係

`CodeWF` がサイトプログラムを担当し、`Assets.Dotnet9` がコンテンツとリソースを担当します。アプリケーション起動時に `Site:LocalAssetsDir` を読み取り、このリポジトリのコンテンツをプロジェクトセンター、ブログ、ツール、検索、サイトマップに取り込みます。

開発時は、2つのリポジトリを並べてクローンすることを推奨します：

```text
CodeWF/
Assets.Dotnet9/
```

## リポジトリ

- リソースリポジトリ：[https://github.com/dotnet9/Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9)
- サイトソースコード：[https://github.com/dotnet9/CodeWF](https://github.com/dotnet9/CodeWF)