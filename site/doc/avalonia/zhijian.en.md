# Zhijian

![Zhijian](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian.svg)

`Zhijian` is a local mind-map editor built with C#, Avalonia, and AtomUI. It provides outline, Markdown, and graphical mind-map views over the same tree model, making it useful for writing outlines, planning features, and keeping readable Markdown-based mind-map documents.

Repository: [https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)

![Zhijian dual view](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-dual-view.png)

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
| Mind-map editing | Inline title editing, notes, delete action, zoom, panning, centering, and node drag/drop. |
| Notes | Notes are synchronized between outline and mind-map views and empty note editors collapse automatically. |
| Drop preview | Dashed previews show whether a node will become a child or be inserted as a sibling. |
| Mini map | The mini map renders the real current mind-map coordinates and viewport. |
| Markdown view | The left pane can switch to Markdown for text-first editing. |

![Zhijian notes](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-note-sync.png)

![Zhijian drag preview](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-mind-drag-preview.png)

![Zhijian mini map](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-minimap-overview.png)

![Zhijian node creation](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-create-node.gif)

## Structure

```text
src/
  CodeWF.MindView/          Reusable mind-map controls, model, mini map, and codecs
  CodeWF.MindView.Themes/   Default Avalonia resources
  Zhijian/                  AtomUI desktop app, outline view, shell, and ViewModel
docs/
  Architecture notes, source-design document, and screenshots
```

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
