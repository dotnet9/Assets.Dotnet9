# Vex

![Vex](https://img1.dotnet9.com/2026/05/vex-avalonia-cover.svg)

`Vex` is a cross-platform Markdown desktop editor built with .NET 10, Avalonia 12, Prism, Semi.Avalonia, and Ursa.Avalonia. It focuses on source Markdown editing, native preview, file management, find/replace, export workflows, and copy paths for publishing platforms.

Repository: [https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)

Release v1.0.0: [https://github.com/dotnet9/Vex/releases/tag/v1.0.0](https://github.com/dotnet9/Vex/releases/tag/v1.0.0)

![Vex main window](https://img1.dotnet9.com/2026/05/vex-main-window.png)

## Positioning

- A free and open-source Markdown writing tool. It keeps source editing and preview separate instead of chasing a WYSIWYG editor surface first.
- The workspace combines a file/outline sidebar, an AvaloniaEdit Markdown editor, and a CodeWF.Markdown native preview.
- The View menu controls sidebar, outline, document list, source mode, line numbers, status bar, fullscreen, and always-on-top behavior.
- The File menu covers new/open/open folder/recent files/reopen with encoding/copy to WeChat Official Account, Zhihu, and Juejin/save/export/print/properties/delete/close.
- Export supports HTML, PNG, image-based PDF, and Word `.docx`; PNG/PDF/Word reuse `CodeWF.Markdown` `MarkdownDocumentExporter`, including relative local images, `data:image`, HTTP(S), SVG/GIF/WebP conversion, and save-location reveal. PDF and Word embed image assets so shared files remain viewable offline.
- Copy to WeChat Official Account, Zhihu, and Juejin passes the current Markdown, active typography theme, and target platform to `CodeWF.Markdown` `MarkdownHtmlClipboardExtensions`, writing rich HTML clipboard formats with the active typography theme applied.
- Find/replace supports case matching, whole-word matching, regex, match counts, and debounced scans for long documents.
- Theme color, Markdown typography theme, compact layout, and language switching are grouped under the Help menu.
- Simplified Chinese, Traditional Chinese, English, and Japanese UI/help documents are included.
- The onboarding guide highlights real menu items, tabs, editor, preview, sidebar, and status-bar targets.
- Release scripts cover multi-RID publishing, zip packages, SHA256 files, release manifests, and optional Windows MSIX layout packaging.

## Features

| Feature | Description |
| --- | --- |
| Markdown editing | AvaloniaEdit-based source editor with smart new lines, current-line highlight, source mode, line numbers, and formatting inserts. |
| Native preview | CodeWF.Markdown preview without WebView, covering headings, lists, tables, code blocks, task lists, local images, SVG, and GIF. |
| File workflow | New documents, single-file open, folder open, recent files, drag-and-drop open, save, save as, external change detection, and reload. |
| Outline navigation | Builds an outline from Markdown headings and jumps to the selected section. |
| Find/replace | Case, whole-word, regex, match count, replace next, and replace all. |
| Export | HTML, PNG, image-based PDF, Word `.docx`, and print preview; PNG/PDF/Word reuse the shared `CodeWF.Markdown` export API, and PDF/Word embed local, `data:image`, HTTP(S), SVG/GIF/WebP images. |
| Publishing copy | Shared `CodeWF.Markdown` rich HTML clipboard output for WeChat Official Account, Zhihu, and Juejin. Windows `HTML Format` uses UTF-8 CF_HTML bytes and applies the active typography theme. |
| Localization | Simplified Chinese, Traditional Chinese, English, and Japanese through Lang.Avalonia.Json. |
| Onboarding | CodeWF.AvaloniaControls Guide steps can target menus, TabItems, editor, and preview areas. |
| Packaging | Windows, Linux, and macOS RID publishing, release zips, SHA256 files, and optional MSIX packaging. |

## Demo

The typical Vex workspace uses a file/outline sidebar, source editor, and live preview.

![Editing and live preview](https://img1.dotnet9.com/2026/05/vex-edit-vex-article-live.gif)

Outline navigation is useful for long documents.

![Outline navigation](https://img1.dotnet9.com/2026/05/vex-outline-vex-article.gif)

Source mode temporarily hides the sidebar and preview.

![Source mode](https://img1.dotnet9.com/2026/05/vex-source-mode-vex-article.gif)

File actions and export paths live in the title menu.

![File export menu](https://img1.dotnet9.com/2026/05/vex-file-export-menu.gif)

Find/replace includes the common match options.

![Find and replace](https://img1.dotnet9.com/2026/05/vex-find-replace-vex-article.gif)

Theme color, typography, and language can be combined.

![Theme and language](https://img1.dotnet9.com/2026/05/vex-theme-typography-language.gif)

The first-run guide targets real controls and menu items.

![Onboarding](https://img1.dotnet9.com/2026/05/codewf-guide-vex-onboarding-flow.gif)

## Project Layout

```text
src/
  Vex/                         Desktop app, Shell, Workspace, Help, services, and ViewModels
  Vex.Controls/                Vex-specific controls
  Vex.Controls.Themes/         Control theme resources
docs/
  Quick start, changelog, acknowledgements, and requirements notes
scripts/
  Stress, release packaging, and MSIX packaging scripts
```

The application uses Prism for module composition and CodeWF.EventBus for cross-module messages. Markdown preview, export image loading, and rich social-editor clipboard output reuse CodeWF.Markdown, keeping the writing flow inside native Avalonia controls.

## Tech Stack

- .NET 10
- Avalonia 12
- Prism.DryIoc.Avalonia
- ReactiveUI.Avalonia
- Semi.Avalonia
- Ursa.Avalonia
- AvaloniaEdit
- CodeWF.Markdown
- CodeWF.AvaloniaControls
- CodeWF.EventBus
- Lang.Avalonia.Json

## Quick Start

Requirements:

- .NET 10 SDK

```powershell
git clone https://github.com/dotnet9/Vex.git
cd Vex
dotnet restore Vex.slnx
dotnet build Vex.slnx
dotnet run --project src/Vex/Vex.csproj
```

Create release artifacts:

```powershell
.\publish_vex_all.bat --package
```

## Repository and Releases

- Repository: [https://github.com/dotnet9/Vex](https://github.com/dotnet9/Vex)
- Release: [https://github.com/dotnet9/Vex/releases/tag/v1.0.0](https://github.com/dotnet9/Vex/releases/tag/v1.0.0)
