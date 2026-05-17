# 枝見 Zhijian

![枝見 Zhijian](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian.svg)

`Zhijian` 是基於 C#、Avalonia 和 AtomUI 的本地 Markdown-first 腦圖編輯器。它讓大綱、Markdown 和圖形腦圖共用同一份樹狀模型，適合撰寫文章大綱、整理功能設計與維護可讀的 Markdown 腦圖文件。

專案倉庫：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)

![枝見主窗口](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-main-window.png)

本頁截圖和 GIF 均來自實際執行的枝見桌面程式，並透過模擬使用者操作截取。

## 主要能力

- 啟動後是空白腦圖，只保留一個可輸入的中心主題。
- 文件選單支援新建、新窗口、開啟、開啟資料夾、最近文件、儲存、另存新檔、開啟文件位置和關閉。
- 開啟資料夾後可在「文件 / 大綱」兩個 Tab 間切換。
- 大綱、Markdown 和腦圖共用 `MindMapNode`，任一視圖修改都會同步。
- 大綱和腦圖選單都支援新增同級、新增子級、升級、降級、上移、下移、備註和刪除。
- `CodeWF.MindView` 只依賴 Avalonia，可在其他 Avalonia 應用中重用。

## 執行預覽

![文件選單](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-file-menu.png)

![開啟資料夾流程](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-open-folder.gif)

![節點選單](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-node-menus.gif)

![備註同步](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-note-sync.gif)

![小圖導航](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-minimap.gif)

![縮放](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-zoom.gif)

![畫布拖曳](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-canvas-pan.gif)

![關於選單](https://img1.dotnet9.com/site/doc/avalonia/imgs/zhijian-about-menu.png)

## 重用 CodeWF.MindView

新的 Avalonia 應用可以引用 `CodeWF.MindView` 和 `CodeWF.MindView.Themes`，在 `App.axaml` 註冊 `<mindThemes:MindViewThemes />`，再於頁面中放入 `MindMapEditor`：

```xml
<mind:MindMapEditor
    Roots="{Binding Roots}"
    SelectedNode="{Binding SelectedNode, Mode=TwoWay}"
    Controller="{Binding}" />
```

宿主 ViewModel 提供 `ObservableCollection<MindMapNode>`，並實作 `IMindMapEditorController`，接管節點層級、建立、刪除、升降級和拖曳移動邏輯。文件開啟、儲存、最近文件和未儲存提示可參考 `IMindMapFileService` 與 `src/Zhijian`。

## 開源專案感謝

- [Dotnet](https://dotnet.microsoft.com/zh-cn/)
- [Avalonia UI](https://avaloniaui.net/)
- [Semi.Avalonia](https://github.com/irihitech/Semi.Avalonia)
- [Ursa.Avalonia](https://github.com/irihitech/Ursa.Avalonia)
- [AtomUI](https://github.com/AtomUI/AtomUI)

## 快速開始

```powershell
dotnet restore Zhijian.slnx
dotnet build Zhijian.slnx
dotnet run --project src/Zhijian/Zhijian.csproj
```

## 倉庫

- GitHub：[https://github.com/dotnet9/Zhijian](https://github.com/dotnet9/Zhijian)
