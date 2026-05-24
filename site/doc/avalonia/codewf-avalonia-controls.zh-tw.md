# CodeWF.AvaloniaControls

![CodeWF.AvaloniaControls 控制項](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-avalonia-controls.svg)

`CodeWF.AvaloniaControls` 是基於 .NET 11 與 Avalonia 12 的開放原始碼控制項倉庫，包含可重複使用的類別庫、主題資源套件和可直接執行的範例專案。它適合用來沉澱通用控制項、驗證控制項範本，也適合為 Avalonia 專案提供一套統一的基礎 UI 能力。

## 套件一覽

| 套件 | 說明 |
| --- | --- |
| `CodeWF.AvaloniaControls` | 通用自訂控制項。 |
| `CodeWF.AvaloniaControls.Themes` | 控制項範本與主題資源套件。 |

## 安裝

```powershell
Install-Package CodeWF.AvaloniaControls
Install-Package CodeWF.AvaloniaControls.Themes
```

## 倉庫結構

```text
src/
  CodeWF.AvaloniaControls/              通用控制項類別庫
  CodeWF.AvaloniaControls.Themes/       控制項範本與主題資源
  CodeWF.AvaloniaControls.Showcase/     控制項展示館
  CodeWF.AvaloniaControls.FluentStarterDemo/ 輕量啟動視窗範例
docs/                                   螢幕截圖、GIF 與文件資源
artifacts/                              打包輸出
publish/                                範例發佈目錄
```

## 適合關注

- 如何將 Avalonia 控制項和主題資源拆分為獨立的 NuGet 套件。
- 如何維護可執行的 Showcase，讓控制項行為、樣式和主題切換可視化。
- 如何使用 `Guide` 在桌面應用中實作新手引導、功能導覽和局部提示。
- 如何在一個倉庫中同時組織類別庫、示範程式、打包腳本和發佈腳本。
- 如何在控制項庫中避免商業套件依賴，保持開放原始碼專案的可分發性。

## Guide 引導控制項

`Guide` 用於 Avalonia 桌面應用的新手引導、功能導覽和局部提示。它不只覆蓋一般按鈕高亮，也面向桌面軟體中更常見的動態入口：選單、二級選單、`Popup`、`Flyout`、捲動區域、`TabItem` 切換、目標延遲出現和視窗尺寸變化。

- `GuideStep` 可為每一步綁定不同目標控制項，也支援無目標的置中說明。
- `GuideOverlay` 負責遮罩挖洞、高亮圓角、目標間距和卡片方向控制。
- `TargetResolveDelay` 與 `StepOpening` 可在進入步驟前展開選單、切換頁籤，再延遲解析目標。
- 支援 `DefaultGuideIndicator`、`TextGuideIndicator`、非模態提示，以及佈局變化後的刷新定位。
- Showcase 中包含基礎多步驟引導、封面內容、自訂按鈕、文字進度和非遮罩提示範例。

## 範例專案

- `CodeWF.AvaloniaControls.Showcase`：用於集中展示控制項能力、主題切換和範例頁面。
- `CodeWF.AvaloniaControls.FluentStarterDemo`：用於示範輕量啟動視窗和基礎樣式接入。

## 建置與打包

```powershell
dotnet restore CodeWF.AvaloniaControls.slnx
dotnet build CodeWF.AvaloniaControls.slnx --no-restore
.\pack.bat
```

## 倉庫

- GitHub：[https://github.com/dotnet9/CodeWF.AvaloniaControls](https://github.com/dotnet9/CodeWF.AvaloniaControls)
- NuGet：[https://www.nuget.org/packages/CodeWF.AvaloniaControls](https://www.nuget.org/packages/CodeWF.AvaloniaControls)
