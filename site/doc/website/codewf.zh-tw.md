# CodeWF

![CodeWF](https://img1.dotnet9.com/site/doc/website/imgs/codewf.svg)

`CodeWF` 是 `dotnet9.com` / `codewf.com` 的網站原始碼倉庫，當前站點基於 ASP.NET Core Razor Pages 構建。它把同級的 `Assets.Dotnet9` 作為檔案型內容倉庫使用，執行時讀取文章、專案文件、工具元資料、時間線、友情連結和網站層級 Markdown 頁面。

## 適合關注

- 想了解 `dotnet9.com` 網站如何組織 Razor Pages、內容服務、搜尋和站點地圖。
- 想把 Markdown、JSON 和圖片資源從應用程式原始碼中拆分出來，形成輕量 CMS 式維護流程。
- 想參考一個以 .NET 為主、同時包含部落格、專案中心和線上工具的網站工程。

## 核心職責

| 模組 | 說明 |
| --- | --- |
| `src/WebApp/Pages` | Razor Pages 頁面，包括首頁、文章、專案中心、工具頁、搜尋頁等。 |
| `src/WebApp/Services/AppService.cs` | 統一載入資源倉庫內容，並維護記憶體快取、搜尋索引和站點地圖。 |
| `src/WebApp/Models` | 文章、分類、專題、專案、工具、搜尋結果等內容模型。 |
| `tests/WebApp.Tests` | 最小測試集，涵蓋 Front Matter、Markdown 渲染和搜尋關鍵詞攔截。 |

## 與資源倉庫協作

站點透過 `Site:LocalAssetsDir` 指向本機 `Assets.Dotnet9` 目錄。開發環境下，檔案監聽會在 Markdown、JSON、圖片或 SVG 變更時清空內容快取，所以維護資源倉庫後重新整理頁面即可看到結果。

常見輸入包括：

- `site/doc/navigation.json`
- `site/tools/tools.json`
- `site/categories.json`
- `site/albums.json`
- `site/timelines.json`
- `site/about.md`
- `2019/` 到當前年份的文章目錄

## 本機執行

```powershell
dotnet run --project src/WebApp/WebApp.csproj
```

如果資源倉庫不在預設路徑，可用環境變數覆蓋：

```powershell
$env:Site__LocalAssetsDir = "D:\github\owner\Assets.Dotnet9"
dotnet run --project src/WebApp/WebApp.csproj
```

## 倉庫

- 網站原始碼：[https://github.com/dotnet9/CodeWF](https://github.com/dotnet9/CodeWF)
- 內容資源：[https://github.com/dotnet9/Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9)