---
title: WPF 基础控件之 PasswordBox 样式
slug: Passwordbox-style-of-WPF-basic-control
description: 基础控件
date: 2022-05-05 06:49:41
copyright: Reprinted
author: 驚鏵 WPF开发者
originaltitle: WPF 基础控件之 PasswordBox 样式
originallink: https://mp.weixin.qq.com/s/RTG4FxBT9hTAUNpbapuLwQ
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_14.jpg
categories: WPF
---

**其他基础控件**

1. Window
2. Button
3. CheckBox
4. ComboBox
5. DataGrid
6. DatePicker
7. Expander
8. GroupBox
9. ListBox
10. ListView11.Menu

`PasswordBox`  实现下面的效果

![](https://img1.dotnet9.com/2022/05/1401.png)

## 01 — 代码如下

1. `PasswordBox` 不支持水印，所以需要用到`DependencyObject`[附加属性](https://docs.microsoft.com/zh-cn/dotnet/api/system.windows.dependencyobject?view=windowsdesktop-6.0)来实现水印；

```csharp
public class ElementHelper : DependencyObject
{
    public static string GetWatermark(DependencyObject obj)
    {
        return (string)obj.GetValue(WatermarkProperty);
    }

    public static void SetWatermark(DependencyObject obj, string value)
    {
        obj.SetValue(WatermarkProperty, value);
    }

    public static readonly DependencyProperty WatermarkProperty =
        DependencyProperty.RegisterAttached("Watermark", typeof(string), typeof(ElementHelper), new PropertyMetadata("Please input"));

}
```

2. 引入命名空间`xmlns:wpfs="clr-namespace:WPFDevelopers.Minimal.Helpers"`；

```html
<TextBlock x:Name="PART_TextBlockWatermark"
    Text="{Binding Path=(wpfs:ElementHelper.Watermark),RelativeSource={RelativeSource TemplatedParent}}"
    Foreground="{StaticResource RegularTextSolidColorBrush}"
    VerticalAlignment="{TemplateBinding VerticalContentAlignment}"
    FontSize="{StaticResource NormalFontSize}"
    Margin="7.5,0" Visibility="Collapsed"/>
```

3. 水印设置完成后，下一步需要判断密码框内容为空时显示水印新增附加类`PasswordBoxHelper`；

```csharp
public class PasswordBoxHelper : DependencyObject
{
    public static bool GetIsMonitoring(DependencyObject obj)
    {
        return (bool)obj.GetValue(IsMonitoringProperty);
    }

    public static void SetIsMonitoring(DependencyObject obj, bool value)
    {
        obj.SetValue(IsMonitoringProperty, value);
    }

    public static readonly DependencyProperty IsMonitoringProperty =
        DependencyProperty.RegisterAttached("IsMonitoring", typeof(bool), typeof(PasswordBoxHelper), new UIPropertyMetadata(false, OnIsMonitoringChanged));



    public static int GetPasswordLength(DependencyObject obj)
    {
        return (int)obj.GetValue(PasswordLengthProperty);
    }

    public static void SetPasswordLength(DependencyObject obj, int value)
    {
        obj.SetValue(PasswordLengthProperty, value);
    }

    public static readonly DependencyProperty PasswordLengthProperty =
        DependencyProperty.RegisterAttached("PasswordLength", typeof(int), typeof(PasswordBoxHelper), new UIPropertyMetadata(0));

    private static void OnIsMonitoringChanged(DependencyObject d, DependencyPropertyChangedEventArgs e)
    {
        var pb = d as PasswordBox;
        if (pb == null)
        {
            return;
        }
        if ((bool)e.NewValue)
        {
            pb.PasswordChanged += PasswordChanged;
        }
        else
        {
            pb.PasswordChanged -= PasswordChanged;
        }
    }

    static void PasswordChanged(object sender, RoutedEventArgs e)
    {
        var pb = sender as PasswordBox;
        if (pb == null)
        {
            return;
        }
        SetPasswordLength(pb, pb.Password.Length);
    }
}
```

4. `XAML`中判断附加属性`PasswordLength`等于`0`时候显示水印；

```html
<Trigger Property="wpfs:PasswordBoxHelper.PasswordLength" Value="0">
    <Setter Property="Visibility" TargetName="PART_TextBlockWatermark" Value="Visible"/>
</Trigger>
```

5. `Styles.PasswordBox.xaml`  代码如下；

```xml
<ResourceDictionary xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    xmlns:sys="clr-namespace:System;assembly=mscorlib"
    xmlns:wpfs="clr-namespace:WPFDevelopers.Minimal.Helpers">

    <ResourceDictionary.MergedDictionaries>
        <ResourceDictionary Source="../Themes/Basic/ControlBasic.xaml"/>
        <ResourceDictionary Source="../Themes/Basic/Animations.xaml"/>
    </ResourceDictionary.MergedDictionaries>

    <Style TargetType="{x:Type PasswordBox}" BasedOn="{StaticResource ControlBasicStyle}">
        <Setter Property="KeyboardNavigation.TabNavigation" Value="None" />
        <Setter Property="FocusVisualStyle" Value="{x:Null}" />
        <Setter Property="VerticalContentAlignment" Value="Center"/>
        <Setter Property="PasswordChar" Value="●" />
        <Setter Property="MinHeight" Value="{StaticResource MinHeight}" />
        <Setter Property="MinWidth" Value="180"/>
        <Setter Property="AllowDrop" Value="True" />
        <Setter Property="Cursor" Value="Hand"/>
        <Setter Property="Padding" Value="6,0"/>
        <Setter Property="wpfs:PasswordBoxHelper.IsMonitoring" Value="True"/>
        <Setter Property="Template">
            <Setter.Value>
                <ControlTemplate TargetType="{x:Type PasswordBox}">
                    <Border x:Name="PART_Border"  
                            CornerRadius="{Binding Path=(wpfs:ElementHelper.CornerRadius),RelativeSource={RelativeSource TemplatedParent}}" 
                            BorderThickness="1"
                            Width="{TemplateBinding Width}"
                            Height="{TemplateBinding Height}">
                        <Border.Background>
                            <SolidColorBrush Color="{DynamicResource WhiteColor}" />
                        </Border.Background>
                        <Border.BorderBrush>
                            <SolidColorBrush Color="{DynamicResource BaseColor}" />
                        </Border.BorderBrush>
                        <VisualStateManager.VisualStateGroups>
                            <VisualStateGroup x:Name="CommonStates">
                                <VisualState x:Name="Normal" />
                                <VisualState x:Name="Disabled" />
                                <VisualState x:Name="MouseOver" >
                                    <Storyboard>
                                        <ColorAnimation Duration="00:00:0.3"
                                                        Storyboard.TargetName="PART_Border"
                                                        Storyboard.TargetProperty="(Border.BorderBrush).(SolidColorBrush.Color)"
                                                        To="{StaticResource PrimaryNormalColor}"/>
                                    </Storyboard>
                                </VisualState>
                            </VisualStateGroup>
                        </VisualStateManager.VisualStateGroups>
                        <!--<ScrollViewer x:Name="PART_ContentHost" />-->
                        <Grid Margin="{TemplateBinding Padding}">
                            <ScrollViewer x:Name="PART_ContentHost" />
                            <TextBlock x:Name="PART_TextBlockWatermark"
                                Text="{Binding Path=(wpfs:ElementHelper.Watermark),RelativeSource={RelativeSource TemplatedParent}}"
                                Foreground="{StaticResource RegularTextSolidColorBrush}"
                                VerticalAlignment="{TemplateBinding VerticalContentAlignment}"
                                FontSize="{StaticResource NormalFontSize}"
                                Margin="7.5,0" Visibility="Collapsed"/>
                        </Grid>
                    </Border>
                    <ControlTemplate.Triggers>
                        <Trigger Property="wpfs:PasswordBoxHelper.PasswordLength" Value="0">
                            <Setter Property="Visibility" TargetName="PART_TextBlockWatermark" Value="Visible"/>
                        </Trigger>
                    </ControlTemplate.Triggers>
                </ControlTemplate>
            </Setter.Value>
        </Setter>
    </Style>

</ResourceDictionary>
```

6. `Styles.PasswordBox.xaml` 代码如下；

```html
<WrapPanel Margin="0,10">
    <PasswordBox />
    <PasswordBox Margin="10,0" ws:ElementHelper.Watermark="请输入密码"/>
    <PasswordBox IsEnabled="False"/>
</WrapPanel>
```

## 02 — 效果预览

![](https://img1.dotnet9.com/2022/05/1402.gif)