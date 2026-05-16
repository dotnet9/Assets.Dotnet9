# CodeWF.Toolbox

![CodeWF.Toolbox](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox.svg)

`CodeWF.Toolbox` 是 CodeWF 體系下的本機桌面工具箱，基於 C#、Avalonia、Prism、Semi.Avalonia 與 Ursa 建構。專案面向開發者日常效率場景，將轉換、編碼、格式化、校驗、產生、查詢、日誌檢視等能力集中到一個可離線執行的跨平台桌面應用程式中。

專案倉庫：[https://github.com/dotnet9/CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)

![CodeWF.Toolbox 預覽](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.svg)

![CodeWF.Toolbox 截圖](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-preview.png)

## 專案定位

- 本機優先：常用轉換、格式化、產生和查詢工具不依賴臨時網頁，適合處理剪貼簿文字、日誌、設定與臨時資料。
- 模組化桌面應用：使用 Prism 模組目錄、相依性注入與 Region 導航組織工具頁，主殼只負責視窗、選單、主題、語言與公共服務。
- 工具規模：目前覆蓋 100 多個工具入口，首頁依常用與推薦工具提供快捷入口。
- 工程範例：適合學習 Avalonia 桌面應用中的主視窗、登入視窗、系統匣、主題切換、多語言資源、模組選單與發佈設定。

## 介面預覽

首頁不再只是歡迎頁，而是工具箱入口。頂部展示工具數量、模組數量和目前執行平台，下方提供常用與推薦工具入口。

![CodeWF.Toolbox 首頁](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-home-desert-zh-cn.png)

功能巡覽可以看到從首頁進入轉換、開發、安全、媒體、日誌等工具頁的整體流程。

![CodeWF.Toolbox 功能巡覽](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-feature-tour.gif)

轉換模組和資料驅動工具頁是目前工具箱的主體。Base64、JSON 格式化、Hash、QR Code 等頁面都採用統一的輸入、執行、複製和輸出習慣。

![CodeWF.Toolbox 轉換工具](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-converter-desert-zh-cn.png)

![CodeWF.Toolbox Base64 工具](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-base64-desert-zh-cn.png)

![CodeWF.Toolbox JSON 格式化](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-json-format-desert-zh-cn.png)

![CodeWF.Toolbox Hash 工具](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-hash-desert-zh-cn.png)

![CodeWF.Toolbox QR Code 工具](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-qrcode-desert-zh-cn.png)

日誌閱讀器面向大型檔案和即時 tail 場景；主題與語言可以在應用內直接切換，方便驗證不同地區和不同視覺風格下的介面表現。

![CodeWF.Toolbox 日誌閱讀器](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-logviewer-desert-zh-cn.png)

![CodeWF.Toolbox 主題與語言](https://img1.dotnet9.com/site/doc/avalonia/imgs/codewf-toolbox-theme-language.gif)

## 主要能力

| 分組 | 說明 |
| --- | --- |
| 轉換工具 | 時間戳、Base64、GUID、日期時間、圖片轉 ICO、JSON/TOML/XML/YAML 互轉、Markdown to HTML、羅馬數字轉換等。 |
| 開發工具 | JSON/YAML/XML/SQL/CSV 格式化與轉換，HTTP Header、Data URL、Docker、Chmod、Crontab、Regex、Git、Hex dump、SemVer、Markdown 表格等輔助工具。 |
| 安全工具 | Bcrypt、BIP39、AES、TripleDES、Hash、HMAC、密碼強度、PDF 簽章檢查、RSA 金鑰、Token、ULID、UUID 等。 |
| Web 工具 | Basic Auth、裝置資訊、HTML 實體、HTTP 狀態碼、JWT、Open Graph、MIME、OTP、SafeLink、Slugify、URL、User-Agent、JSON Diff 等。 |
| 媒體工具 | QR Code、WiFi QR Code 和 SVG 佔位符產生。 |
| 網路工具 | IPv4 位址轉換、IPv4 範圍到 CIDR、IPv4 子網路、IPv6 ULA、MAC 位址產生和廠商查詢。 |
| 數學、計量、文字與資料 | ETA、運算式計算、百分比、計時、溫度轉換、ASCII Art、Emoji、Lorem ipsum、文字 Diff、文字統計、IBAN、電話號碼解析等。 |
| 日誌閱讀器 | 面向大型日誌檔與即時 tail 場景，只渲染可見區域，避免一次性將大檔案讀入記憶體。 |
| 國際化資源管理 | XML 翻譯資源的合併、管理與語言屬性處理，適合維護多語言資源的一致性。 |

## 工程組織

核心程式碼位於 `src` 目錄：

```text
src/
  CodeWF.Toolbox/                    桌面應用程式入口、Shell、主視窗、設定、主題、資源與模組目錄
  CodeWF.Core/                       公共抽象、服務介面、Region 名稱、選單與通知等基礎能力
  CodeWF.Controls/                   公共控制項
  CodeWF.Modules.ToolFramework/      資料驅動工具執行框架、工具目錄與通用表單能力
  CodeWF.Modules.Converter/          轉換工具模組
  CodeWF.Modules.Development/        開發輔助模組
  CodeWF.Modules.Security/           安全工具模組
  CodeWF.Modules.Web/                Web 輔助模組
  CodeWF.Modules.Media/              媒體產生工具模組
  CodeWF.Modules.Network/            網路工具模組
  CodeWF.Modules.Math/               數學工具模組
  CodeWF.Modules.Measurement/        計量工具模組
  CodeWF.Modules.Text/               文字工具模組
  CodeWF.Modules.Data/               資料工具模組
  CodeWF.Modules.LogViewer/          大型日誌檔檢視模組
  CodeWF.Modules.XmlTranslatorManager/ XML 國際化資源管理模組
  CodeWF.Toolbox.Tests/              單元測試工程
```

## 擴充方式

專案裡的工具大致分為兩類：

- 獨立頁面工具：適合日誌閱讀器、Base64、GUID、圖片轉圖示、XML 翻譯資源管理這類互動更具體的頁面。
- 資料驅動工具：適合 JSON 格式化、JWT 解析、Hash 文字、URL 編碼、MIME 查詢、MAC 查詢、QR Code 產生、文字統計等輸入輸出結構穩定的工具。

資料驅動工具透過 `ToolSpec` 描述工具 ID、分類、輸入欄位、輸出欄位、執行函式與自動執行設定，重用統一表單與執行框架，減少重複 XAML。

## 快速開始

環境需求以倉庫 README 為準，目前主線使用：

- .NET 11 SDK。
- Avalonia 支援的 Windows、macOS 或 Linux 桌面環境。

```powershell
dotnet restore CodeWF.Toolbox.slnx
dotnet build CodeWF.Toolbox.slnx
dotnet run --project src/CodeWF.Toolbox/CodeWF.Toolbox.csproj
```

## 適合關注

- 需要本機優先、離線可用的開發者桌面工具箱。
- 想學習 Avalonia + Prism 模組化桌面應用如何組織主殼、選單、頁面與服務。
- 想參考資料驅動工具框架，降低大量小工具的維護成本。
- 想了解桌面應用裡的主題切換、多語言資源、系統匣、日誌大檔檢視與發佈設定。

## 倉庫

- GitHub：[https://github.com/dotnet9/CodeWF.Toolbox](https://github.com/dotnet9/CodeWF.Toolbox)
