# Assets.Dotnet9

![Assets.Dotnet9](https://img1.dotnet9.com/site/doc/website/imgs/assets-dotnet9.svg)

`Assets.Dotnet9` 是 CodeWF 網站的資源與內容倉庫。它不只是圖片目錄，而是承擔了檔案型內容庫的角色：文章、專案文件、工具元資料、時間線、友情連結、站點 Markdown 頁面和配圖都在這裡維護。

## 適合關注

- 想了解 `dotnet9.com` 如何把內容資料從 Web 應用程式原始碼中分離出來。
- 想按年份、月份、欄目和專案目錄管理 Markdown 與圖片資源。
- 想給 CodeWF 網站新增文章、專案說明、工具入口或站點設定。

## 目錄結構

```text
2019/ ... 2026/      按年份、月份組織文章和配圖
site/
  about.md           關於頁內容
  Privacy.md         隱私頁內容
  albums.json        專題元資料
  categories.json    分類元資料
  friend-links.json  友情連結元資料
  timelines.json     時間線資料
  doc/               專案中心導航、正文和 SVG 封面
  tools/             線上工具元資料與預覽圖
  pays/              贊助頁資源
  banners/           頁面橫幅資源
  favicon/           站點圖示資源
```

## 內容維護原則

| 原則 | 說明 |
| --- | --- |
| 路徑穩定 | 文章、圖片、文件和工具 slug 盡量長期不變，避免歷史連結失效。 |
| 元資料完整 | 文章 Front Matter、分類、專題、專案導航和工具 JSON 需要同步維護。 |
| 命名清晰 | 圖片、SVG 和 Markdown 使用語意化檔名，便於搜尋和重複使用。 |
| 可審閱 | 內容變更以文字檔案為主，適合 Git diff 和 Pull Request 審閱。 |

## 與 CodeWF 的關係

`CodeWF` 負責網站程式，`Assets.Dotnet9` 負責內容和資源。應用程式啟動時讀取 `Site:LocalAssetsDir`，並把本倉庫內容載入到專案中心、部落格、工具、搜尋和站點地圖中。

開發時建議兩個倉庫並排複製：

```text
CodeWF/
Assets.Dotnet9/
```

## 倉庫

- 資源倉庫：[https://github.com/dotnet9/Assets.Dotnet9](https://github.com/dotnet9/Assets.Dotnet9)
- 網站原始碼：[https://github.com/dotnet9/CodeWF](https://github.com/dotnet9/CodeWF)