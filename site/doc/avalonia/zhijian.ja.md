# Zhijian

![Zhijian](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian.svg)

`Zhijian` は C#、Avalonia、AtomUI で作られたローカル向け Markdown-first マインドマップエディターです。アウトライン、Markdown、グラフィカルなマインドマップを同じツリーモデルで同期し、記事の構成、機能設計、Markdown ベースのマインドマップ文書を扱いやすくします。

リポジトリ：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)

![Zhijian main window](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-main-window.png)

このページのスクリーンショットと GIF は、実際に起動した Zhijian デスクトップアプリを操作して取得したものです。

## 主な機能

- 起動時は空のマインドマップで、編集可能な中心トピックだけを表示します。
- File メニューは New、New Window、Open、Open Folder、Recent Files、Save、Save As、Open File Location、Close を提供します。
- フォルダーを開くと、左側で `Files` と `Outline` タブを切り替えられます。
- アウトライン、Markdown、マインドマップは同じ `MindMapNode` を共有します。
- アウトラインとマインドマップのメニューは、兄弟追加、子追加、昇格、降格、上下移動、ノート、削除を提供します。
- `CodeWF.MindView` は Avalonia のみへ依存し、他の Avalonia アプリから再利用できます。

## 実行プレビュー

![File menu](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-file-menu.png)

![Open folder workflow](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-open-folder.gif)

![Node menus](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-node-menus.gif)

![Note synchronization](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-note-sync.gif)

![Mini-map navigation](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-minimap.gif)

![Zoom](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-zoom.gif)

![Canvas panning](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-canvas-pan.gif)

![About menu](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-about-menu.png)

## CodeWF.MindView の再利用

新しい Avalonia アプリでは `CodeWF.MindView` と `CodeWF.MindView.Themes` を参照し、`App.axaml` に `<mindThemes:MindViewThemes />` を登録してから、ビューに `MindMapEditor` を配置できます。

```xml
<mind:MindMapEditor
    Roots="{Binding Roots}"
    SelectedNode="{Binding SelectedNode, Mode=TwoWay}"
    Controller="{Binding}" />
```

ホスト ViewModel は `ObservableCollection<MindMapNode>` を提供し、`IMindMapEditorController` を実装して、階層判定、作成、削除、昇格、降格、ドラッグ移動を処理します。ファイルの open/save、recent files、未保存確認は `IMindMapFileService` と `src/Zhijian` の実装を参考にできます。

## Open Source Thanks

- [Dotnet](https://dotnet.microsoft.com/zh-cn/)
- [Avalonia UI](https://avaloniaui.net/)
- [Semi.Avalonia](https://github.com/irihitech/Semi.Avalonia)
- [Ursa.Avalonia](https://github.com/irihitech/Ursa.Avalonia)
- [AtomUI](https://github.com/AtomUI/AtomUI)

## Quick Start

```powershell
dotnet restore Zhijian.slnx
dotnet build Zhijian.slnx
dotnet run --project src/Zhijian/Zhijian.csproj
```

## Repository

- GitHub：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)
