# CodeWF.Toolbox

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

`CodeWF.Toolbox` is a modular desktop toolbox built with Avalonia + Prism for daily developer workflows. The shell, shared services, and feature modules are kept separate so the app can keep growing without turning into a hard-to-maintain monolithic window project.

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

![CodeWF.Toolbox preview](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.svg)

![CodeWF.Toolbox screenshot](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.png)

## Features

- Built on Avalonia UI, Prism module catalog, dependency injection, and region navigation.
- Current modules cover AI utilities, format conversion, development helpers, and XML translation management.
- Native AOT-oriented project layout and release settings are kept in the repository.
- Multilingual resources are available in `en-US`, `zh-CN`, `zh-Hant`, and `ja-JP`.
- The menu and page structure are ready for additional tool pages later.

## Built-in Tools

| Group | Description |
| --- | --- |
| AI | Title-to-slug, prompt translation, and chat helper tools. |
| Format Conversion | Provides JSON/YAML and image-to-icon conversion tools. |
| Development Assistance | Provides daily development utilities such as JSON/YAML prettify. |
| XML Translation Manager | Used for comparing, merging, and maintaining XML internationalization resources. |

## Directory Structure

```text
src/
  CodeWF.Toolbox/                    Main desktop app, shell, settings, and resources
  CodeWF.Core/                       Common abstractions, services, and region definitions
  CodeWF.Controls/                   Common controls
  CodeWF.Modules.AI/                 AI tools module
  CodeWF.Modules.Converter/          Format conversion module
  CodeWF.Modules.Development/        Development helper module
  CodeWF.Modules.XmlTranslatorManager/ XML localization management module
tests/
  AvaloniaAotDemo/                   Avalonia AOT demo
  WinFormsAotDemo/                   WinForms AOT demo
  ConsoleAotDemo/                    Console AOT demo
docs/
  assets/                            Standalone SVG diagrams
```

## Quick Start

Prerequisites:

- .NET 9 SDK.
- A desktop environment supported by Avalonia. The project has been tested on Windows 7, Windows Server 2019, Windows 10, Windows 11, and macOS 11+.

```powershell
dotnet restore CodeWF.Toolbox.sln
dotnet build CodeWF.Toolbox.sln
dotnet run --project src/CodeWF.Toolbox/CodeWF.Toolbox.csproj
```

## Repository

- GitHub: [https://github.com/dotnet9/CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)
