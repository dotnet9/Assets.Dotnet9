# CodeWF.AvaloniaControls

![CodeWF.AvaloniaControls](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-avalonia-controls.svg)

`CodeWF.AvaloniaControls` is an open-source control repository based on .NET 11 and Avalonia 12, containing reusable class libraries, theme resource packages, and a directly runnable sample project. It is suitable for accumulating common controls, validating control templates, and providing a set of unified basic UI capabilities for Avalonia projects.

## Packages

| Package | Description |
| --- | --- |
| `CodeWF.AvaloniaControls` | General custom controls. |
| `CodeWF.AvaloniaControls.Themes` | Control templates and theme resource packages. |

## Installation

```powershell
Install-Package CodeWF.AvaloniaControls
Install-Package CodeWF.AvaloniaControls.Themes
```

## Repository Structure

```text
src/
  CodeWF.AvaloniaControls/              Common control class library
  CodeWF.AvaloniaControls.Themes/       Control templates and theme resources
  CodeWF.AvaloniaControls.Showcase/     Control showcase
  CodeWF.AvaloniaControls.FluentStarterDemo/  Lightweight startup window demo
docs/                                   Screenshots, GIFs, and documentation assets
artifacts/                              Package output
publish/                                Sample publish directory
```

## Worth Paying Attention To

- How to separate Avalonia controls and theme resources into independent NuGet packages.
- How to maintain a runnable Showcase to visualize control behavior, styles, and theme switching.
- How to use `Guide` to build onboarding, feature tours, and contextual hints in desktop apps.
- How to organize class libraries, demo applications, packaging scripts, and publishing scripts in a single repository.
- How to avoid commercial package dependencies in a control library and maintain the distributability of an open-source project.

## Guide Control

`Guide` is used for first-run onboarding, feature tours, and contextual hints in Avalonia desktop applications. It goes beyond highlighting static buttons and targets the dynamic entry points common in desktop software: menus, nested menus, `Popup`, `Flyout`, scroll regions, `TabItem` switching, delayed target creation, and window size changes.

- `GuideStep` can bind each step to a different target control, and can also show centered content without a target.
- `GuideOverlay` handles mask cutouts, highlight corner radius, target spacing, and card placement.
- `TargetResolveDelay` and `StepOpening` let an app open menus or switch tabs before resolving the target.
- It supports `DefaultGuideIndicator`, `TextGuideIndicator`, non-modal hints, and refreshed positioning after layout changes.
- The Showcase includes basic multi-step tours, cover content, custom buttons, text progress, and non-mask hint examples.

## Sample Projects

- `CodeWF.AvaloniaControls.Showcase`: Centrally showcases control capabilities, theme switching, and example pages.
- `CodeWF.AvaloniaControls.FluentStarterDemo`: Demonstrates lightweight startup windows and basic style integration.

## Build & Package

```powershell
dotnet restore CodeWF.AvaloniaControls.slnx
dotnet build CodeWF.AvaloniaControls.slnx --no-restore
.\pack.bat
```

## Repository

- GitHub: [https://github.com/dotnet9/CodeWF.AvaloniaControls](https://github.com/dotnet9/CodeWF.AvaloniaControls)
- NuGet: [https://www.nuget.org/packages/CodeWF.AvaloniaControls](https://www.nuget.org/packages/CodeWF.AvaloniaControls)
