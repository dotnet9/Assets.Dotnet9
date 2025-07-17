---
title: Avalonia进阶技巧：实现DataGrid可取消排序
slug: avalonia-datagrid-cancel-sorting
description: 三击列头智能切换排序状态，完整实现方案详解
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
  - 交互设计
---

## 需求背景

默认DataGrid点击列头只能在升序(↑)、降序(↓)两种状态间切换：

![](https://img1.dotnet9.com/2025/07/0301.gif)

但在实际业务场景中，用户可能需要快速恢复默认数据排序。

## 实现方案

方法已经封装好，有更好的实现方式欢迎留言：

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

## 效果演示

![](https://img1.dotnet9.com/2025/07/0302.gif)

本号持续分享Avalonia实战技巧，欢迎关注，保持交流，共同进步。

