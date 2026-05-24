# 紹介

ここでは、Dotnet9 / CodeWF が現在重点的にメンテナンスしているオープンソースプロジェクトをまとめています。ドキュメントでは、ウェブサイト、C# 汎用ライブラリ、Avalonia 関連プロジェクトのみを掲載し、訪問者が各リポジトリの位置づけ、インストール方法、適用シナリオを素早く理解できるようにしています。

その他のオープンソースプロジェクトはこちらからアクセスしてください：[https://github.com/dotnet9/](https://github.com/dotnet9/)

## ウェブサイト

| プロジェクト | 位置づけ |
| --- | --- |
| [CodeWF](https://github.com/dotnet9/CodeWF) | `dotnet9.com` / `codewf.com` のウェブサイトソースコードリポジトリ。ASP.NET Core Razor Pages ベース。 |
| [Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9) | ウェブサイトの記事、ドキュメント、ツールデータ、画像、サイト設定のファイル形式コンテンツリポジトリ。 |

## C# 汎用

| プロジェクト | 位置づけ |
| --- | --- |
| [CodeWF.EventBus.Socket](https://github.com/dotnet9/CodeWF.EventBus.Socket) | TCP Socket ベースの軽量プロセス間イベントバス。Pub-Sub と Query/Response をサポート。 |
| [CodeWF.EventBus](https://github.com/dotnet9/CodeWF.EventBus) | プロセス内イベントバス。デスクトップ、Web、コンソールプロジェクトでのモジュール疎結合とシンプルなCQRSに適しています。 |
| [CodeWF.NetWeaver](https://github.com/dotnet9/CodeWF.NetWeaver) | ネットワークパケットシリアル化コアライブラリ。これに基づく TCP/UDP ラッパー層 `CodeWF.NetWrapper` も提供。 |

## Avalonia

| プロジェクト | 位置づけ |
| --- | --- |
| [CodeWF.AvaloniaControls](https://github.com/dotnet9/CodeWF.AvaloniaControls) | Avalonia 12 向け汎用コントロール、Guide 案内コントロール、テーマパック。 |
| [CodeWF.LogViewer](https://github.com/dotnet9/CodeWF.LogViewer) | 軽量ログコアライブラリとAvaloniaログ表示コントロール。 |
| [Lang.Avalonia](https://github.com/dotnet9/Lang.Avalonia) | Avalonia UI プラグイン方式の多言語ライブラリ。JSON、XML、RESX と強タイプキー生成をサポート。 |
| [CodeWF.Markdown](https://github.com/dotnet9/CodeWF.Markdown) | Avalonia Markdown レンダリングコントロール、組版テーマ、サンプルプロジェクト。 |
| [CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox) | Avalonia + Prism ベースのモジュール式クロスプラットフォームデスクトップツールボックス。 |
