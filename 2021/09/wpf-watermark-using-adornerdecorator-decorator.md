---
title: WPF：使用AdornerDecorator装饰器实现水印
slug: wpf-watermark-using-adornerdecorator-decorator
description: 基本全是代码
date: 2021-09-09 23:55:02
copyright: Reprinted
author: 秋荷雨翔
originalTitle: WPF：使用AdornerDecorator装饰器实现水印
originalLink: https://www.cnblogs.com/s0611163/archive/2021/09/09/15245412.html
draft: False
cover: https://img1.dotnet9.com/2021/09/cover_04.jpg
categories: 
    - WPF
tags: 
    - WPF
    - 水印
---

水印装饰器 WatermarkAdorner 类代码：

```C#
using System;
using System.Collections.Generic;
using System.Globalization;
using System.Linq;
using System.Text;
using System.Text.RegularExpressions;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Documents;
using System.Windows.Media;

namespace WPF水印装饰器
{
    /// <summary>
    /// 水印装饰器
    /// </summary>
    public class WatermarkAdorner : Adorner
    {
        private string _watermarkText;

        public WatermarkAdorner(UIElement adornedElement, string watermarkText) : base(adornedElement)
        {
            _watermarkText = watermarkText;
            this.IsHitTestVisible = false; //使水印不捕获事件
        }

        protected override void OnRender(DrawingContext drawingContext)
        {
            Rect rect = new Rect(this.AdornedElement.RenderSize);
            double centerX = rect.Right / 2.0;
            double centerY = rect.Bottom / 2.0;

            drawingContext.PushOpacity(0.5);
            RotateTransform rotateTransform = new RotateTransform(45, centerX, centerY);
            drawingContext.PushTransform(rotateTransform);

            RotateTransform rt = new RotateTransform(-45, centerX, centerY);
            Point point = default(Point);
            double n = 5.0;
            double margin = 40;
            double halfWidth = GetTextLength(_watermarkText) * 10 / 2.0;

            //第1排3个
            point = RotatePoint(0.5, 0.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);
            point = RotatePoint(2.5, 0.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);
            point = RotatePoint(4.5, 0.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);

            //第2排2个
            point = RotatePoint(1.5, 1.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);
            point = RotatePoint(3.5, 1.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);

            //第3排3个
            point = RotatePoint(0.5, 2.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);
            point = RotatePoint(2.5, 2.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);
            point = RotatePoint(4.5, 2.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);

            //第4排2个
            point = RotatePoint(1.5, 3.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);
            point = RotatePoint(3.5, 3.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);

            //第5排3个
            point = RotatePoint(0.5, 4.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);
            point = RotatePoint(2.5, 4.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);
            point = RotatePoint(4.5, 4.5, n, rt, rect, margin);
            DrawText(point.X, point.Y, halfWidth, drawingContext, _watermarkText);

        }

        private void DrawText(double x, double y, double textHalfWidth, DrawingContext drawingContext, string text)
        {
            int fontSize = 20;
            SolidColorBrush colorBrush = new SolidColorBrush((Color)ColorConverter.ConvertFromString("#eeeef2"));
            Point point = new Point(x - textHalfWidth, y - fontSize / 2.0);
            FormattedText formattedText = new FormattedText(text, CultureInfo.CurrentCulture, FlowDirection.LeftToRight, new Typeface("宋体"), fontSize, colorBrush);
            drawingContext.DrawText(formattedText, point);
        }

        /// <summary>
        /// 旋转Point
        /// </summary>
        /// <param name="ratioX">文本中心点占区域宽度n等分的比例</param>
        /// <param name="ratioY">文本中心点占区域长度n等分的比例</param>
        /// <param name="n">区域长宽n等分</param>
        /// <param name="rotateTransform">旋转对象</param>
        /// <param name="rect">区域</param>
        /// <param name="margin">Margin</param>
        private Point RotatePoint(double ratioX, double ratioY, double n, RotateTransform rotateTransform, Rect rect, double margin)
        {
            return rotateTransform.Transform(new Point(ratioX / n * rect.Right, ratioY / n * (rect.Bottom - 2 * margin) + margin));
        }

        #region 计算文本长度(汉字计为2 大写字母计为1.5 小写字母计为1)
        /// <summary>
        /// 计算文本长度(汉字计为2 大写字母计为1.5 小写字母计为1)
        /// </summary>
        private double GetTextLength(string text)
        {
            double length = 0;

            Regex reg1 = new Regex("[\u4E00-\u9FFF]|[\uFE30-\uFFA0]");
            Regex reg2 = new Regex("[A-Z]");

            foreach (char c in text)
            {
                if (reg1.IsMatch(c.ToString()))
                {
                    length += 2;
                }
                else if (reg2.IsMatch(c.ToString()))
                {
                    length += 1.5;
                }
                else
                {
                    length += 1;
                }
            }

            return length;
        }
        #endregion

    }
}
```

如何使用：

在窗体或控件的 Loaded 方法中，添加如下代码：

```C#
UIElement uiElement = (UIElement)this.Content;
AdornerLayer adornerLayer = AdornerLayer.GetAdornerLayer(uiElement);
adornerLayer.Add(new WatermarkAdorner(uiElement, _watermarkText));
```

完整 MainWindow.xaml 代码：

```html
<Window
  x:Class="WPF水印装饰器.MainWindow"
  xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
  xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
  xmlns:d="http://schemas.microsoft.com/expression/blend/2008"
  xmlns:mc="http://schemas.openxmlformats.org/markup-compatibility/2006"
  xmlns:local="clr-namespace:WPF水印装饰器"
  mc:Ignorable="d"
  Title="MainWindow"
  Height="1040"
  Width="1920"
  Loaded="Window_Loaded"
  WindowStyle="None"
  ResizeMode="NoResize"
  WindowStartupLocation="CenterScreen"
  MouseRightButtonDown="Window_MouseRightButtonDown"
>
  <Window.Template>
    <ControlTemplate TargetType="{x:Type Window}">
      <!-- ControlTemplate不包含AdornerDecorator，需要在ControlTemplate中添加AdornerDecorator -->
      <AdornerDecorator>
        <ContentPresenter />
      </AdornerDecorator>
    </ControlTemplate>
  </Window.Template>
  <Window.Resources>
    <ResourceDictionary>
      <ControlTemplate x:Key="tmplBtn" TargetType="{x:Type Button}">
        <Border x:Name="border" Background="#068d6b" CornerRadius="5">
          <TextBlock
            Text="{TemplateBinding Content}"
            Foreground="#ffffff"
            HorizontalAlignment="Center"
            VerticalAlignment="Center"
          ></TextBlock>
        </Border>
        <ControlTemplate.Triggers>
          <Trigger Property="IsMouseOver" Value="true">
            <Setter
              TargetName="border"
              Property="Background"
              Value="#069d8b"
            ></Setter>
          </Trigger>
        </ControlTemplate.Triggers>
      </ControlTemplate>
    </ResourceDictionary>
  </Window.Resources>
  <Grid x:Name="grid" Background="#094760">
    <button
      x:Name="button"
      Content="显示子窗体"
      Margin="0,0,0,0"
      Width="100"
      Height="35"
      Click="button_Click"
      Template="{StaticResource tmplBtn}"
    ></button>
    <button
      x:Name="button2"
      Content="显示子窗体2"
      Margin="0,100,0,0"
      Width="100"
      Height="35"
      Click="button2_Click"
      Template="{StaticResource tmplBtn}"
    ></button>
  </Grid>
</Window>
```

**注意：如果窗体或控件使用了 ControlTemplate，因为 ControlTemplate 不包含 AdornerDecorator，所以需要在 ControlTemplate 中添加 AdornerDecorator。**

完整 MainWindow.xaml.cs 代码：

```C#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading;
using System.Threading.Tasks;
using System.Windows;
using System.Windows.Controls;
using System.Windows.Data;
using System.Windows.Documents;
using System.Windows.Input;
using System.Windows.Media;
using System.Windows.Media.Imaging;
using System.Windows.Navigation;
using System.Windows.Shapes;

namespace WPF水印装饰器
{
    /// <summary>
    /// MainWindow.xaml 的交互逻辑
    /// </summary>
    public partial class MainWindow : Window
    {
        private string _watermarkText = "持续研发测试账号 34.8.99.64";

        public MainWindow()
        {
            InitializeComponent();
        }

        private void Window_Loaded(object sender, RoutedEventArgs e)
        {
            UIElement uiElement = (UIElement)this.Content;
            AdornerLayer adornerLayer = AdornerLayer.GetAdornerLayer(uiElement);
            adornerLayer.Add(new WatermarkAdorner(uiElement, _watermarkText));
        }

        private void Window_MouseRightButtonDown(object sender, MouseButtonEventArgs e)
        {
            this.Close();
        }

        private void button_Click(object sender, RoutedEventArgs e)
        {
            Window2 win = new Window2(_watermarkText);
            win.Owner = this;
            win.WindowStartupLocation = WindowStartupLocation.CenterScreen;
            win.Show();
        }

        private void button2_Click(object sender, RoutedEventArgs e)
        {
            Watermark win = new Watermark(_watermarkText);
            win.Owner = this;
            win.WindowStartupLocation = WindowStartupLocation.CenterScreen;
            win.Show();
        }
    }
}
```

效果图：

![效果图](https://img1.dotnet9.com/2021/09/0401.png)

> 有一款 PPT 插件叫"[iSlide](https://islide.kf5.com/hc/kb/article/1378386/)"，其中 Windows 客户端是用 WPF 开发的，有个功能是 PPT 拼图，其中的水印功能就是上面类似的代码，我们看看效果结束本文：
>
> ![iSlide-PPT拼](https://img1.dotnet9.com/2021/09/0402.gif)
