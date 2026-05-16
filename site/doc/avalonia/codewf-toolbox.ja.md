# CodeWF.Toolbox

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

`CodeWF.Toolbox` は CodeWF エコシステムのローカルデスクトップツールボックスで、C#、Avalonia、Prism、Semi.Avalonia、Ursa を使って構築されています。変換、エンコード、整形、検証、生成、検索、ログ閲覧など、開発者が日常的に使う小さな機能を、オフラインでも動作するクロスプラットフォームのデスクトップアプリにまとめています。

リポジトリ：[https://github.com/dotnet9/CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)

![CodeWF.Toolbox preview](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.svg)

![CodeWF.Toolbox screenshot](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.png)

## 位置づけ

- ローカル優先：よく使う変換、整形、生成、検索ツールを一時的な Web ページに頼らず使えるため、クリップボードのテキスト、ログ、設定、臨時データの処理に向いています。
- モジュール式デスクトップアプリ：Prism のモジュールカタログ、依存性注入、Region ナビゲーションでツールページを分離し、シェルはウィンドウ、メニュー、テーマ、言語、共通サービスを担当します。
- ツール規模：現在は 100 を超えるツール入口を持ち、ホーム画面からよく使うツールと推奨ツールへ素早くアクセスできます。
- エンジニアリング例：Avalonia デスクトップアプリのメインウィンドウ、ログインウィンドウ、システムトレイ、テーマ切り替え、多言語リソース、モジュールメニュー、公開設定を学ぶ対象として使えます。

## 画面プレビュー

ホーム画面は単なるウェルカムページではなく、ツールボックスの入口です。上部にツール数、モジュール数、現在の実行プラットフォームを表示し、下部によく使うツールと推奨ツールへのショートカットを配置しています。

![CodeWF.Toolbox ホーム画面](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-home-aquatic-ja-jp.png)

機能ツアーでは、ホーム画面から変換、開発、セキュリティ、メディア、ログなどのツールページへ移動する流れを確認できます。

![CodeWF.Toolbox 機能ツアー](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-feature-tour.gif)

変換モジュールとデータ駆動型ツールページが、このツールボックスの中心です。Base64、JSON 整形、Hash、QR コードなどのページは、入力、実行、コピー、出力の操作感をそろえています。

![CodeWF.Toolbox 変換ツール](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-converter-desert-zh-cn.png)

![CodeWF.Toolbox Base64 ツール](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-base64-desert-zh-cn.png)

![CodeWF.Toolbox JSON 整形](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-json-format-desert-zh-cn.png)

![CodeWF.Toolbox Hash ツール](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-hash-desert-zh-cn.png)

![CodeWF.Toolbox QR コードツール](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-qrcode-desert-zh-cn.png)

ログビューアは大きなファイルとリアルタイム tail を想定しています。テーマと言語はアプリ内で切り替えられるため、異なるロケールや表示スタイルでの画面確認にも使えます。

![CodeWF.Toolbox ログビューア](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-logviewer-desert-zh-cn.png)

![CodeWF.Toolbox テーマと言語](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-theme-language.gif)

## 主な機能

| グループ | 説明 |
| --- | --- |
| 変換ツール | タイムスタンプ、Base64、GUID、日時、画像から ICO、JSON/TOML/XML/YAML 変換、Markdown to HTML、ローマ数字変換など。 |
| 開発ツール | JSON/YAML/XML/SQL/CSV の整形と変換、HTTP Header、Data URL、Docker、Chmod、Crontab、Regex、Git、Hex dump、SemVer、Markdown テーブルなど。 |
| セキュリティツール | Bcrypt、BIP39、AES、TripleDES、Hash、HMAC、パスワード強度、PDF 署名チェック、RSA キー、Token、ULID、UUID など。 |
| Web ツール | Basic Auth、デバイス情報、HTML エンティティ、HTTP ステータスコード、JWT、Open Graph、MIME、OTP、SafeLink、Slugify、URL、User-Agent、JSON Diff など。 |
| メディアツール | QR コード、WiFi QR コード、SVG プレースホルダー生成。 |
| ネットワークツール | IPv4 アドレス変換、IPv4 範囲から CIDR、IPv4 サブネット、IPv6 ULA、MAC アドレス生成、ベンダー検索。 |
| 数学、計測、テキスト、データ | ETA、式計算、百分率、クロノメーター、温度変換、ASCII Art、Emoji、Lorem ipsum、テキスト Diff、テキスト統計、IBAN、電話番号解析など。 |
| ログビューア | 大きなログファイルとリアルタイム tail を想定し、ファイル全体ではなく表示範囲だけを描画します。 |
| 国際化リソース管理 | XML 翻訳リソースのマージ、管理、言語メタデータ処理。多言語リソースの整合性を保つための機能です。 |

## プロジェクト構成

主要コードは `src` にあります：

```text
src/
  CodeWF.Toolbox/                    デスクトップアプリ入口、Shell、メインウィンドウ、設定、テーマ、リソース、モジュールカタログ
  CodeWF.Core/                       共通抽象、サービス契約、Region 名、メニュー、通知などの基盤
  CodeWF.Controls/                   共通コントロール
  CodeWF.Modules.ToolFramework/      データ駆動ツールランタイム、ツールカタログ、共通フォーム機能
  CodeWF.Modules.Converter/          変換ツールモジュール
  CodeWF.Modules.Development/        開発支援モジュール
  CodeWF.Modules.Security/           セキュリティツールモジュール
  CodeWF.Modules.Web/                Web 支援モジュール
  CodeWF.Modules.Media/              メディア生成ツールモジュール
  CodeWF.Modules.Network/            ネットワークツールモジュール
  CodeWF.Modules.Math/               数学ツールモジュール
  CodeWF.Modules.Measurement/        計測ツールモジュール
  CodeWF.Modules.Text/               テキストツールモジュール
  CodeWF.Modules.Data/               データツールモジュール
  CodeWF.Modules.LogViewer/          大きなログファイル向けビューアモジュール
  CodeWF.Modules.XmlTranslatorManager/ XML 国際化リソース管理モジュール
  CodeWF.Toolbox.Tests/              単体テストプロジェクト
```

## 拡張モデル

このプロジェクトのツールは大きく 2 種類に分かれます：

- 独立ページ型ツール：ログビューア、Base64、GUID、画像からアイコン、XML 翻訳リソース管理など、固有の操作が多いページに向いています。
- データ駆動型ツール：JSON 整形、JWT 解析、テキスト Hash、URL エンコード、MIME 検索、MAC 検索、QR コード生成、テキスト統計など、入出力構造が安定しているツールに向いています。

データ駆動型ツールは `ToolSpec` でツール ID、カテゴリ、入力フィールド、出力フィールド、実行関数、自動実行設定を記述します。これにより、各小ツールで XAML を繰り返し書かず、共通フォームとランタイムを再利用できます。

## クイックスタート

最新の環境要件はリポジトリの README を確認してください。現在のメインラインでは次を使用します：

- .NET 11 SDK。
- Avalonia がサポートする Windows、macOS、Linux のデスクトップ環境。

```powershell
dotnet restore CodeWF.Toolbox.slnx
dotnet build CodeWF.Toolbox.slnx
dotnet run --project src/CodeWF.Toolbox/CodeWF.Toolbox.csproj
```

## 向いている用途

- ローカル優先で、オフラインでも使える開発者向けデスクトップツールボックスが必要な場合。
- Avalonia + Prism のモジュール式デスクトップアプリで、シェル、メニュー、ページ、サービスをどう整理するか学びたい場合。
- 多数の小さなツールの保守コストを下げるデータ駆動型ツールフレームワークを参考にしたい場合。
- デスクトップアプリのテーマ切り替え、多言語リソース、システムトレイ連携、大きなログファイルの閲覧、公開設定を見たい場合。

## リポジトリ

- GitHub：[https://github.com/dotnet9/CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)
