# 枝見 Zhijian

![枝見 Zhijian](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian.svg)

`Zhijian` 是基於 C#、Avalonia 和 AtomUI 的本地腦圖編輯器。它圍繞同一份樹狀模型提供大綱、Markdown 和圖形腦圖三種視角，適合撰寫文章大綱、整理功能設計和維護可讀的 Markdown 腦圖文件。

專案倉庫：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)

![枝見雙視圖](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-dual-view.png)

本頁新增截圖和 GIF 均來自實際運行的枝見桌面程序，並通過模擬用戶操作截取。

## 專案定位

- Markdown-first：中心主題、子節點和備註可以保存為可讀 Markdown。
- 雙向同步：大綱、Markdown 和腦圖共用 `MindMapNode` 模型。
- 本地桌面體驗：應用層使用 AtomUI 主題、窗口、菜單、按鈕和輸入控件。
- 可重用控件：`CodeWF.MindView` 只依賴 Avalonia。
- 文件交換：支援 Markdown、OPML 和 XMind 導入導出。

## 主要功能

| 功能 | 說明 |
| --- | --- |
| 大綱編輯 | 支援鍵盤建立、刪除、升降級節點，節點圓點可打開備註/刪除菜單，也可拖拽調整結構。 |
| 可調雙欄 | 大綱和腦圖之間提供可見拖動分隔條，可以左右調整兩側寬度。 |
| 腦圖編輯 | 支援節點內聯編輯、備註、刪除、縮放、畫布平移、中心主題定位和拖拽調整父子關係。 |
| 備註同步 | 節點備註在大綱和腦圖中同步顯示；空備註失焦後自動隱藏。 |
| 拖拽預覽 | 拖到目標中部成為子節點，拖到上下邊緣調整同級順序，並顯示虛線預覽。 |
| 小圖概覽 | 小圖按真實節點坐標繪製全局結構，點擊小圖可定位到對應區域。 |
| Markdown 視圖 | 左側可切換 Markdown 編輯，標題層級和備註可直接作為文本維護。 |

![枝見備註同步](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-note-sync.gif)

![枝見分隔條拖動](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-splitter-resize.gif)

![枝見腦圖菜單](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-mind-menu.png)

![枝見小圖](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-minimap-overview.png)

![枝見 Markdown 和深色主題](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-markdown-theme.gif)

## 工程組織

```text
src/
  CodeWF.MindView/          可重用腦圖控件、節點模型、小圖和文件編解碼
  CodeWF.MindView.Themes/   腦圖控件預設 Avalonia 資源
  Zhijian/                  AtomUI 桌面應用、大綱視圖、主窗口和 ViewModel
docs/
  架構說明、源碼設計文檔和參考截圖
```

## 復用 CodeWF.MindView

新的 Avalonia 應用可以只引用 `CodeWF.MindView` 和 `CodeWF.MindView.Themes`，在 `App.axaml` 註冊 `<mindThemes:MindViewThemes />`，然後在頁面中使用：

```xml
<mind:MindMapEditor
    Roots="{Binding Roots}"
    SelectedNode="{Binding SelectedNode, Mode=TwoWay}"
    Controller="{Binding}" />
```

宿主 ViewModel 提供 `ObservableCollection<MindMapNode>`，並實作 `IMindMapEditorController`，接管節點層級、建立、刪除、升級和拖拽移動邏輯。`src/Zhijian` 是一個完整的 AtomUI 桌面應用接入示例。

## 快速開始

```powershell
dotnet restore Zhijian.slnx
dotnet build Zhijian.slnx
dotnet run --project src/Zhijian/Zhijian.csproj
```

## 倉庫

- GitHub：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)
