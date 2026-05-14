# CodeWF

![CodeWF](https://img1.dotnet9.com/site/doc/website/imgs/codewf.svg)

`CodeWF` は `dotnet9.com` / `codewf.com` のサイトソースコードリポジトリです。現在のサイトは ASP.NET Core Razor Pages で構築されています。同階層の `Assets.Dotnet9` をファイル型コンテンツリポジトリとして使用し、実行時に記事、プロジェクトドキュメント、ツールメタデータ、タイムライン、フレンドリンク、サイトレベルの Markdown ページを読み込みます。

## こんな方に

- `dotnet9.com` サイトが Razor Pages、コンテンツサービス、検索、サイトマップをどのように構成しているか知りたい方。
- Markdown、JSON、画像リソースをアプリケーションコードから分離し、軽量 CMS 的なメンテナンスフローを実現したい方。
- .NET を中心に、ブログ、プロジェクトセンター、オンラインツールを含むサイトエンジニアリングを参考にしたい方。

## コア機能

| モジュール | 説明 |
| --- | --- |
| `src/WebApp/Pages` | Razor Pages：トップページ、記事、プロジェクトセンター、ツールページ、検索ページなど。 |
| `src/WebApp/Services/AppService.cs` | リソースリポジトリのコンテンツを一括読み込み、メモリキャッシュ、検索インデックス、サイトマップを管理。 |
| `src/WebApp/Models` | 記事、カテゴリ、特集、プロジェクト、ツール、検索結果などのコンテンツモデル。 |
| `tests/WebApp.Tests` | 最小テストセット：Front Matter、Markdown レンダリング、検索キーワードインターセプトをカバー。 |

## リソースリポジトリとの連携

サイトは `Site:LocalAssetsDir` でローカルの `Assets.Dotnet9` ディレクトリを指定します。開発環境では、Markdown、JSON、画像、SVG が変更されるとファイル監視がコンテンツキャッシュをクリアするため、リソースリポジトリを更新した後、ページをリロードすれば結果が反映されます。

主な入力ファイル：

- `site/doc/navigation.json`
- `site/tools/tools.json`
- `site/categories.json`
- `site/albums.json`
- `site/timelines.json`
- `site/about.md`
- `2019/` から現在までの記事ディレクトリ

## ローカル実行

```powershell
dotnet run --project src/WebApp/WebApp.csproj
```

リソースリポジトリがデフォルトパスにない場合は、環境変数で上書き：

```powershell
$env:Site__LocalAssetsDir = "D:\github\owner\Assets.Dotnet9"
dotnet run --project src/WebApp/WebApp.csproj
```

## リポジトリ

- サイトソースコード：[https://github.com/dotnet9/CodeWF](https://github.com/dotnet9/CodeWF)
- コンテンツリソース：[https://github.com/dotnet9/Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9)