---
title: Avalonia自定义TabItem边框
slug: avalonia-custom-tabitem-border
description: 可作为参考，实现其他形式的TabItem边框
date: 2025-07-07 21:46:07
lastmod: 2025-07-07 22:28:04
draft: false
cover: https://img1.dotnet9.com/2025/07/0103.gif
categories:
  - Avalonia UI
tags:
  - C#
  - Avalonia
  - TabItem
---

## 背景

感谢微信【Avalonia开发交流群】的 @kankankan 大佬提供的代码示例：

![](https://img1.dotnet9.com/2025/07/0201.jpg)

![](https://img1.dotnet9.com/2025/07/0202.jpg)

下图是按个性化要求修改效果：

![](https://img1.dotnet9.com/2025/07/0103.gif)

为了兼容[Semi.Avalonia](https://github.com/irihitech/Semi.Avalonia)主题风格，我们的TabControl控件主题从参考Semi的Card风格控件主题开始，Semi的效果如下：

![](https://img1.dotnet9.com/2025/07/0203.gif)

我们修改后，在各主题切换时，展示效果如下：

![](https://img1.dotnet9.com/2025/07/0204.gif)

## 实现

讲代码总是枯燥的，讲个大概吧。

这是Semi的TabControl的ControlTheme代码：

![](https://img1.dotnet9.com/2025/07/0205.png)

我们主要是修改TabItem的边框风格，所以将Semi的这段代码直接复制粘贴，给ControlTheme换个Key，再把图中框选部分ItemContainerTheme的Value指向另一个TabItem主题，其他部分代码按需修改，我这没动其他代码，这是修改后代码截图：

![](https://img1.dotnet9.com/2025/07/0206.png)

TabItem的控件主题代码如下，关键代码是自定义的边框代码位置：

![](https://img1.dotnet9.com/2025/07/0207.png)

其中TrapezoidShapedTabItemBorder继承自Control，主要是重写它的Render方法：

```csharp
public partial class TrapezoidShapedTabItemBorder : Control
{
    public const double DiagonalFilletRatio = 0.8;

    public static readonly StyledProperty<IBrush> BorderBrushProperty =
        AvaloniaProperty.Register<TrapezoidShapedTabItemBorder, IBrush>(nameof(BorderBrush),
            new SolidColorBrush(Color.Parse("#05CCCCCC")));

    public static readonly StyledProperty<double> BorderThicknessProperty =
        AvaloniaProperty.Register<TrapezoidShapedTabItemBorder, double>(nameof(BorderThickness), 1);

    public static readonly StyledProperty<IBrush> BackgroundProperty =
        AvaloniaProperty.Register<TrapezoidShapedTabItemBorder, IBrush>(nameof(Background), Brushes.DarkGreen);

    public IBrush BorderBrush
    {
        get => GetValue(BorderBrushProperty);
        set => SetValue(BorderBrushProperty, value);
    }

    public double BorderThickness
    {
        get => GetValue(BorderThicknessProperty);
        set => SetValue(BorderThicknessProperty, value);
    }

    public IBrush Background
    {
        get => GetValue(BackgroundProperty);
        set => SetValue(BackgroundProperty, value);
    }

    public override void Render(DrawingContext context)
    {
        base.Render(context);
        if (BorderThickness < 1)
        {
            return;
        }

        if (Parent?.Parent?.Parent is not TabControl tabControl ||
            Parent?.Parent is not TabItem currentTabItem)
        {
            return;
        }

        var index = tabControl.Items.IndexOf(currentTabItem);
        var isFirst = index == 0;
        var isLast = index == tabControl.Items.Count - 1;
        var radius = currentTabItem.CornerRadius;

        // 获取控件的尺寸
        var rect = new Rect(Bounds.Size);
        var borderThickness = BorderThickness;
        // 偏移路径使线条对齐像素网格
        var halfBorder = borderThickness / 2.0;
        var adjustedRect = rect.Deflate(halfBorder);

        // 设置边框路径
        var pathGeometry = new StreamGeometry();
        using (var ctx = pathGeometry.Open())
        {
            if (isFirst & !isLast)
            {
                if (tabControl.TabStripPlacement == Dock.Top)
                {
                    DrawTopFirstTabItemBorder(ctx, adjustedRect, radius, rect);
                }
            }
            else if (!isFirst && isLast)
            {
                if (tabControl.TabStripPlacement == Dock.Top)
                {
                    DrawTopLastTabItemBorder(ctx, adjustedRect, radius, rect);
                }
            }
            else
            {
                if (tabControl.TabStripPlacement == Dock.Top)
                {
                    DrawTopOtherTabItemBorder(ctx, adjustedRect, radius, rect);
                }
            }

            // 底边消失（不绘制）
            // 这里直接跳过底边路径，确保底边消失
            ctx.EndFigure(isClosed: true);
        }

        // 绘制边框
        context.DrawGeometry(Background, new Pen(BorderBrush, BorderThickness)
        {
            Thickness = BorderThickness,
            LineJoin = PenLineJoin.Round, // 圆角连接
            LineCap = PenLineCap.Round // 圆角端点
        }, pathGeometry);
    }
}
```

Render中，根据当前TabItem是TabControl的第一个、最后一个、中间部分，调用不同的方法绘制边框，比如绘制第一索引的TabItem方法：

```csharp
private static void DrawTopFirstTabItemBorder(StreamGeometryContext ctx, Rect adjustedRect, CornerRadius radius,
    Rect rect)
{
    var x = adjustedRect.Left;
    var y = adjustedRect.Bottom;

    // 左下角开始
    ctx.BeginFigure(new Point(x, y), isFilled: true);

    // 左下角外圆
    if (radius.BottomLeft > 0)
    {
        x = rect.Left + radius.BottomLeft;
        y = adjustedRect.Bottom - radius.BottomLeft;
        ctx.ArcTo(
            new Point(x, y),
            new Size(radius.BottomLeft, radius.BottomLeft),
            0,
            false,
            SweepDirection.CounterClockwise);
    }

    // 左边直线
    y = adjustedRect.Top + radius.TopLeft;
    ctx.LineTo(new Point(x, y));

    // 左上角内圆角
    if (radius.TopLeft > 0)
    {
        x += radius.TopLeft;
        y = adjustedRect.Top;
        ctx.ArcTo(
            new Point(x, y),
            new Size(radius.TopLeft, radius.TopLeft),
            0,
            false,
            SweepDirection.Clockwise);
    }

    // 上边直线
    x = adjustedRect.Right - radius.TopRight * 2 - radius.BottomRight * 2;
    ctx.LineTo(new Point(x, y));

    // 右上角内圆角
    if (radius.TopRight > 0)
    {
        x += radius.TopRight;
        y += radius.TopRight * DiagonalFilletRatio;
        ctx.ArcTo(
            new Point(x, y),
            new Size(radius.TopRight, radius.TopRight),
            0,
            false,
            SweepDirection.Clockwise);
    }

    // 右边斜线
    x = adjustedRect.Right - radius.BottomRight;
    y = adjustedRect.Bottom - radius.BottomRight;
    ctx.LineTo(new Point(x, y));

    // 右下角外圆
    if (radius.BottomRight > 0)
    {
        x = rect.Right;
        y = adjustedRect.Bottom;
        ctx.ArcTo(
            new Point(x, y),
            new Size(radius.BottomRight, radius.BottomRight),
            0,
            false,
            SweepDirection.CounterClockwise);
    }
}
```

通过StreamGeometryContext调用LineTo方法绘制直线，调用ArcTo方法绘制圆弧（内圆、外圆），各种风格的边框都能画了，比如Edge的TabItem效果：

![](https://img1.dotnet9.com/2025/07/0208.png)

这种效果很好实现了吧？

## 总结

本文只说个大概，大家可以看具体的实现代码，举一反三，其他控件效果可以使用类似的方式实现。

仓库：

- CodeWF.AvaloniaControls：https://github.com/dotnet9/CodeWF.AvaloniaControls

- CodeF.ToolBox：https://github.com/dotnet9/CodeWF.Toolbox