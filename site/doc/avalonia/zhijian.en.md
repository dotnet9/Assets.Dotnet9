# Zhijian

![Zhijian](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian.svg)

`Zhijian` is a local mind-map editor built with C#, Avalonia, and AtomUI. It provides outline, Markdown, and graphical mind-map views over the same tree model, making it useful for writing outlines, planning features, and keeping readable Markdown-based mind-map documents.

Repository: [https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)

![Zhijian dual view](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-dual-view.png)

The screenshots and GIFs added here were captured from a real running Zhijian desktop session with simulated user operations.

## Positioning

- Markdown-first document model for topics and notes.
- Synchronized outline, Markdown, and mind-map views backed by `MindMapNode`.
- AtomUI-based desktop shell and theme.
- Reusable `CodeWF.MindView` control library with only Avalonia as a UI dependency.
- Import and export support for Markdown, OPML, and XMind.

## Highlights

| Feature | Description |
| --- | --- |
| Outline editing | Keyboard creation, deletion, promotion, demotion, note menu, and drag/drop structure editing. |
| Resizable panes | A visible splitter lets users resize the outline and mind-map panes. |
| Mind-map editing | Inline title editing, notes, delete action, zoom, panning, centering, and node drag/drop. |
| Notes | Notes are synchronized between outline and mind-map views and empty note editors collapse automatically. |
| Drop preview | Dashed previews show whether a node will become a child or be inserted as a sibling. |
| Mini map | The mini map renders the real current mind-map coordinates and viewport. |
| Markdown view | The left pane can switch to Markdown for text-first editing. |

![Zhijian notes](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-note-sync.gif)

![Zhijian splitter resize](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-splitter-resize.gif)

![Zhijian mind-map menu](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-mind-menu.png)

![Zhijian mini map](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-minimap-overview.png)

![Zhijian Markdown and dark theme](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-markdown-theme.gif)

![Zhijian File menu](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-file-menu.png)

![Zhijian changelog window](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-changelog-window.png)

## Structure

```text
src/
  CodeWF.MindView/          Reusable mind-map controls, model, mini map, and codecs
  CodeWF.MindView.Themes/   Default Avalonia resources
  Zhijian/                  AtomUI desktop app, outline view, shell, and ViewModel
docs/
  Architecture notes, source-design document, and screenshots
```

## Reusing CodeWF.MindView

A new Avalonia app can reference `CodeWF.MindView` and `CodeWF.MindView.Themes`, register `<mindThemes:MindViewThemes />` in `App.axaml`, and place `MindMapEditor` in a view:

```xml
<mind:MindMapEditor
    Roots="{Binding Roots}"
    SelectedNode="{Binding SelectedNode, Mode=TwoWay}"
    Controller="{Binding}" />
```

The host ViewModel provides `ObservableCollection<MindMapNode>` and implements `IMindMapEditorController` for level lookup, node creation, deletion, promotion, and drag/drop moves. The `src/Zhijian` app is a complete AtomUI desktop shell reference around the reusable control.

## Open Source Thanks

Zhijian is built on excellent open source platforms and libraries:

- [Dotnet](https://dotnet.microsoft.com/zh-cn/)
- [Avalonia UI](https://avaloniaui.net/)
- [Semi.Avalonia](https://github.com/irihitech/Semi.Avalonia)
- [Ursa.Avalonia](https://github.com/irihitech/Ursa.Avalonia)
- [AtomUI](https://github.com/AtomUI/AtomUI)

## Quick Start

Requirements:

- .NET 10 SDK

```powershell
dotnet restore Zhijian.slnx
dotnet build Zhijian.slnx
dotnet run --project src/Zhijian/Zhijian.csproj
```

## Repository

- GitHub: [https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)
