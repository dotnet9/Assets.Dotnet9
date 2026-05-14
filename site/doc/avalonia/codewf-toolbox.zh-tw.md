# CodeWF.Toolbox

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

`CodeWF.Toolbox` 是一個基於 Avalonia + Prism 的模組化桌面工具箱，面向開發者日常效率場景。專案把主殼、公共服務與功能模組分開組織，後續新增工具頁時只需要擴充模組，不會讓應用程式演變成難以維護的單體視窗專案。

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

![CodeWF.Toolbox 預覽](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.svg)

![CodeWF.Toolbox 截圖](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.png)

## 特性

- 基於 Avalonia UI、Prism 模組目錄、相依性插入與 Region 導航建構。
- 目前模組涵蓋 AI 工具、格式轉換、開發輔助與 XML 翻譯管理。
- 保留面向 Native AOT 發佈的專案結構與相關設定。
- 多語言資源支援 `en-US`、`zh-CN`、`zh-Hant` 與 `ja-JP`。
- 選單與頁面結構也已整理為方便後續新增工具頁的形式。

## 內建工具

| 分組 | 說明 |
| --- | --- |
| AI | 提供標題轉 slug、提示詞翻譯與聊天輔助等工具。 |
| 格式轉換 | 提供 JSON/YAML、圖片轉圖示等格式轉換工具。 |
| 開發輔助 | 提供 JSON/YAML 格式化等日常開發小工具。 |
| XML 翻譯管理 | 用於比對、合併和維護 XML 國際化資源。 |

## 目錄結構

```text
src/
  CodeWF.Toolbox/                    主要桌面應用、主殼、設定與資源
  CodeWF.Core/                       公共抽象、服務與區域定義
  CodeWF.Controls/                   公共控制項
  CodeWF.Modules.AI/                 AI 工具模組
  CodeWF.Modules.Converter/          格式轉換模組
  CodeWF.Modules.Development/        開發輔助模組
  CodeWF.Modules.XmlTranslatorManager/ XML 國際化管理模組
tests/
  AvaloniaAotDemo/                   Avalonia AOT 示範
  WinFormsAotDemo/                   WinForms AOT 示範
  ConsoleAotDemo/                    Console AOT 示範
docs/
  assets/                            獨立 SVG 圖示
```

## 快速開始

環境需求：

- .NET 9 SDK。
- Avalonia 支援的桌面環境。目前已測試 Windows 7、Windows Server 2019、Windows 10、Windows 11 與 macOS 11+。

```powershell
dotnet restore CodeWF.Toolbox.sln
dotnet build CodeWF.Toolbox.sln
dotnet run --project src/CodeWF.Toolbox/CodeWF.Toolbox.csproj
```

## 倉庫

- GitHub：[https://github.com/dotnet9/CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)
