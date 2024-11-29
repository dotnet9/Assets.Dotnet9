---
title: WPF显示数据图表(LiveCharts2)
slug: wpf-display-data-chart-live-charts2
description: LiveCharts是一个适用于.Net的数据可视化库，可以跨多个设备和框架运行
date: 2023-11-15 08:20:13
lastmod: 2023-11-15 09:58:46
copyright: Reprinted
author: 奔跑的皮卡峰
originalTitle: WPF显示数据图表(LiveCharts2)
originalLink: https://mp.weixin.qq.com/s/l3fmYcPlEryS6idnd9urXA
draft: false
cover: https://img1.dotnet9.com/2023/11/0618.gif
categories: 
    - WPF
tags: 
    - LiveChart
---

最近接手一个小项目，需要做一个数据显示大屏，要能显示曲线，显示进出量，重点是要好看，我哪知道什么是好看，就随便做做呗。

![作者实际项目](https://img1.dotnet9.com/2023/11/0601.png)

数据都是假的，只是做出一个效果。显示这种数据图，使用的是[LiveCharts2](https://github.com/beto-rodriguez/LiveCharts2)。这是一个非常漂亮的 Charts 模块。官网如下：

- https://www.lvcharts.com/

![官方首页](https://img1.dotnet9.com/2023/11/0602.gif)

![支持平台](https://img1.dotnet9.com/2023/11/0603.gif)

[LiveCharts2](https://github.com/beto-rodriguez/LiveCharts2)支持的还挺多，我们在这里选择`WPF`。

如果我们直接通过`NuGet`进行搜索，会发现找到的`LiveCharts`版本较旧。

![旧版本NuGet搜索结果](https://img1.dotnet9.com/2023/11/0604.png)

[LiveCharts2](https://github.com/beto-rodriguez/LiveCharts2)相对`V0`版本做了很多改进和修复，通过`NuGet`安装`LiveChartsCore.SkiaSharpView.WPF`。

![新版本NuGet搜索结果](https://img1.dotnet9.com/2023/11/0614.png)

或者打开程序包管理器控制台，输入：

```shell
Install-Package LiveChartsCore.SkiaSharpView.WPF -Version 2.0.0-rc2
```

![控制台安装截图](https://img1.dotnet9.com/2023/11/0606.png)

等待安装完成，就可以找到了。

![项目安装截图](https://img1.dotnet9.com/2023/11/0605.png)

顺便安装一下[CommunityToolkit.Mvvm](https://learn.microsoft.com/zh-cn/dotnet/communitytoolkit/mvvm/)，这个可以通过`NuGet`找到，这个包是因为[GalaSoft.MVVMLight](https://www.nuget.org/packages/MvvmLight)已经不再维护，所以官方做的新模型，可以很好的迁移进来。

新建一个`MainViewModel`，添加引用，增加代码。

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using LiveChartsCore;
using LiveChartsCore.SkiaSharpView;
using CommunityToolkit.Mvvm.ComponentModel;

namespace LiveChartsDemo
{
    [ObservableObject]
    public partial class MainViewModel
    {
        public ISeries[] Series { get; set; }
            = new ISeries[]
            {
                new LineSeries<double>
                {
                    Values = new double[] { 2, 1, 3, 5, 3, 4, 6 },
                    Fill = null
                }
            };
    }
}
```

然后到`MainWindow.xaml`里，引用`LiveCharts`，设置`DataContext`。

```xml
xmlns:lvc="clr-namespace:LiveChartsCore.SkiaSharpView.WPF;assembly=LiveChartsCore.SkiaSharpView.WPF"
```

```xml
<Window.DataContext>
	<local:MainViewModel />
</Window.DataContext>
```

然后增加`CartesianChart`

```xml
<Grid>
	<lvc:CartesianChart Series="{Binding Series}" />
</Grid>
```

![图片](https://img1.dotnet9.com/2023/11/0607.png)

`LineSeries`为设置的线形图，当然还能有柱状图。

```csharp
public ISeries[] Series { get; set; }
            = new ISeries[]
            {
                new ColumnSeries<double>
                {
                    Values = new double[] { 2, 1, 3, 5, 3, 4, 6 },
                },
                new LineSeries<double>
                {
                    Values = new double[] { 2, 1, 3, 5, 3, 4, 6 },
                    Fill = null
                },
            };
```

![图片](https://img1.dotnet9.com/2023/11/0608.png)

还可以增加 Title，用于说明图标的作用。

```csharp

public LabelVisual Title { get; set; }
            = new LabelVisual
            {
                Text = "Title",
                TextSize=20,
                Paint = new SolidColorPaint(SKColors.Black)
            };
```

```xml
<lvc:CartesianChart Series="{Binding Series}" Title="{Binding Title}"/>
```

![图片](https://img1.dotnet9.com/2023/11/0609.png)

这里就有个问题，如果你的 Title 设置为中文，

```csharp
public LabelVisual Title { get; set; }
            = new LabelVisual
            {
                Text = "标题",
                TextSize=20,
                Paint = new SolidColorPaint(SKColors.Black)
            };
```

就显示不出来了

![图片](https://img1.dotnet9.com/2023/11/0610.png)

我们需要修改一下设定，以便支持中文显示。

```csharp

public LabelVisual Title { get; set; }
            = new LabelVisual
            {
                Text = "标题",
                TextSize=20,
                Paint = new SolidColorPaint {
                    Color = SKColors.Black,
                    SKTypeface = SKFontManager.Default.MatchCharacter('汉')
                },
            };
```

![图片](https://img1.dotnet9.com/2023/11/0611.png)

官方文档非常丰富，也有对应的示例，做出来的结果还是非常不错的。

- [gallery - LiveCharts2](https://livecharts.dev/docs/WPF/2.0.0-rc2/gallery)

**部分效果 1：**

![图片](https://img1.dotnet9.com/2023/11/0612.gif)

**部分效果 2：**

![图片](https://img1.dotnet9.com/2023/11/0615.gif)

**部分效果 3：**

![图片](https://img1.dotnet9.com/2023/11/0616.gif)

**部分效果 4：**

![图片](https://img1.dotnet9.com/2023/11/0617.gif)

**部分效果 5：**

![图片](https://img1.dotnet9.com/2023/11/0613.gif)

**部分效果 6：**

![图片](https://img1.dotnet9.com/2023/11/0618.gif)

据说免费的`LiveCharts`在数据量超过 10000 渲染效果会不好，需要使用付费版。这个我还没有验证，读者可以自行尝试一下。
