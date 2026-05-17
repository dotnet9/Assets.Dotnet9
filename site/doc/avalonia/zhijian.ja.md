# Zhijian

![Zhijian](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian.svg)

`Zhijian` は C#、Avalonia、AtomUI で構築したローカル向けマインドマップエディターです。同じツリーモデルを、アウトライン、Markdown、グラフィカルなマインドマップの 3 つの視点で編集できます。記事の構成作成、機能設計、Markdown ベースのマインドマップ文書の管理に向いています。

リポジトリ：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)

![Zhijian dual view](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-dual-view.png)

## 位置づけ

- Markdown-first の文書モデル。
- `MindMapNode` を共有するアウトライン、Markdown、マインドマップ表示。
- AtomUI によるデスクトップシェルとテーマ。
- Avalonia のみを UI 依存にした再利用可能な `CodeWF.MindView`。
- Markdown、OPML、XMind のインポートとエクスポート。

## 主な機能

| 機能 | 説明 |
| --- | --- |
| アウトライン編集 | キーボードでの作成、削除、昇格、降格、ノートメニュー、ドラッグによる構造調整。 |
| マインドマップ編集 | インライン編集、ノート、削除、ズーム、パン、中心トピック移動、ノードドラッグ。 |
| ノート同期 | ノートはアウトラインとマインドマップに同期表示され、空のノート入力欄は自動で閉じます。 |
| ドロッププレビュー | 子ノード化または同階層挿入を破線プレビューで確認できます。 |
| ミニマップ | 現在のノード座標とビューポートを元に全体像を描画します。 |
| Markdown ビュー | 左ペインを Markdown 編集に切り替えられます。 |

![Zhijian notes](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-note-sync.png)

![Zhijian drag preview](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-mind-drag-preview.png)

![Zhijian mini map](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-minimap-overview.png)

![Zhijian node creation](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-create-node.gif)

## 構成

```text
src/
  CodeWF.MindView/          再利用可能なマインドマップコントロール、モデル、ミニマップ、コーデック
  CodeWF.MindView.Themes/   デフォルト Avalonia リソース
  Zhijian/                  AtomUI デスクトップアプリ、アウトラインビュー、シェル、ViewModel
docs/
  アーキテクチャ、ソース設計ドキュメント、スクリーンショット
```

## クイックスタート

```powershell
dotnet restore Zhijian.slnx
dotnet build Zhijian.slnx
dotnet run --project src/Zhijian/Zhijian.csproj
```

## リポジトリ

- GitHub：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)
