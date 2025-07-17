---
title: Avalonia小窍门之DataGrid添加取消排序
slug: avalonia-datagrid-cancel-sorting
description: 默认点击列头只有升序、降序排序，无法取消
date: 2025-07-17 22:40:43
lastmod: 2025-07-17 22:57:14
draft: false
cover: https://img1.dotnet9.com/2025/07/0302.gif
categories:
  - Avalonia UI
tags:
  - C#
  - Avalonia
  - DataGrid
  - 可取消排序
---

Avalonia的DataGrid列点击排序效果如下：

![](https://img1.dotnet9.com/2025/07/0302.gif)

怎么取消排序呢？比如下面这样：

![](https://img1.dotnet9.com/2025/07/0303.gif)

这是同事贡献的代码，大家可以直接拿去用：

```csharp
public static class DataGridExtension
{
    public static void AddSorting(this Avalonia.Controls.DataGrid dataGrid)
    {
        var view = new DataGridCollectionView(dataGrid.ItemsSource);
        dataGrid.Sorting += (s, e) =>
        {
            if (s is not Avalonia.Controls.DataGrid) return;

            var memberPath = e.Column.SortMemberPath;
            var sortDescription = view.SortDescriptions.FirstOrDefault(d => d.PropertyPath == memberPath);
            if (sortDescription is not null && sortDescription.Direction == ListSortDirection.Descending)
            {
                view.SortDescriptions.Clear();
                e.Handled = true;
            }

            dataGrid.ItemsSource = view;
            view.Refresh();
        };
    }
}
```

本号不定时分享Avalonia相关技术，欢迎关注