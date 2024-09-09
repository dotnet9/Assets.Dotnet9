---
title: WPF实现消息中心
slug: WPF-implements-message-center
description: 本文将讲解基于WPF实现一个消息中心的功能，比如常见的软件当中会经常收到服务端推送的“新闻”、“公告”等消息。
date: 2022-05-11 07:31:17
copyright: Reprinted
author: juster.zhu
originalTitle: WPF实现消息中心
originalLink: https://mp.weixin.qq.com/s/seSaQkhkE7dpWqoiQ96fzQ
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_33.png
categories: 
    - .NET
tags: 
    - WPF
---

## 一、概要

本文将讲解基于 WPF 实现一个消息中心的功能，比如常见的软件当中会经常收到服务端推送的“新闻”、“公告”等消息。这个时候就需要对这个需求进行分析了。

功能分析如下：

- 消息内容显示。
- 消息管理增、删、批量删除。
- 消息分类（通知类消息、交互类型消息例如可跳转到某个连接或程序内的模块）
- 消息处理（接受、删除、忽略）

## 二、实现

![](https://img1.dotnet9.com/2022/05/3301.gif)

1. 消息内容显示
   这里考虑自定义的控件为 Listbox，消息本身是一个多项的内容且需要操作每一项。

```xml
  <ListBox
    Grid.Row="1"
    MaxHeight="335"
    Background="{x:Null}"
    BorderThickness="0"
    ItemContainerStyle="{DynamicResource ListBoxItemStyle}"
    ItemsSource="{Binding MessageItem}"
    ScrollViewer.HorizontalScrollBarVisibility="Hidden">
    <ListBox.ItemsPanel>
        <ItemsPanelTemplate>
            <VirtualizingStackPanel Orientation="Vertical" />
        </ItemsPanelTemplate>
    </ListBox.ItemsPanel>
    <ListBox.ItemTemplate>
        <DataTemplate DataType="{x:Type localModel:MessageItemModel}">
            <Border
                Height="30"
                BorderBrush="#FFBDBDBD"
                BorderThickness="0,0,0,0.6">
                <Grid>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="1*" />
                        <ColumnDefinition Width="40" />
                    </Grid.ColumnDefinitions>
                    <DockPanel>
                        <Label
                            MaxWidth="70"
                            Content="{Binding Path=Name}"
                            Foreground="Red"
                            ToolTip="{Binding Path=Name}" />
                        <Label
                            MaxWidth="130"
                            Content="{Binding Path=Content}"
                            Foreground="White"
                            ToolTip="{Binding Path=Content}" />
                    </DockPanel>
                    <CheckBox
                        Grid.Column="1"
                        FlowDirection="RightToLeft"
                        IsChecked="{Binding Path=CheckBoxState}" />
                </Grid>
            </Border>
        </DataTemplate>
    </ListBox.ItemTemplate>
</ListBox>
```

2. 消息管理增、删、批量删除

主要容器定下来之后那么接下来每一项消息就是自定义 ListboxItem 即可，针对每一条消息要有具体的处理。

例如：通知类消息，只需要确定按钮。

交互类型消息，需要确认、删除、忽略

```xml
 <DataTemplate x:Key="SelectedTemplate" DataType="{x:Type localModel:MessageItemModel}">
     <Border BorderBrush="#FFBDBDBD" BorderThickness="0,0,0,0.6">
         <StackPanel>
             <TextBox
                 MaxWidth="240"
                 MaxHeight="200"
                 Padding="0,5,0,0"
                 HorizontalAlignment="Center"
                 VerticalAlignment="Center"
                 Background="Transparent"
                 BorderThickness="0"
                 FontSize="14"
                 Foreground="White"
                 IsReadOnly="True"
                 Text="{Binding Path=Content}"
                 TextAlignment="Center"
                 TextWrapping="WrapWithOverflow"
                 ToolTip="{Binding Path=Content}"
                 VerticalScrollBarVisibility="Auto" />
             <StackPanel
                 Margin="5,5,5,9"
                 HorizontalAlignment="Center"
                 VerticalAlignment="Center"
                 Orientation="Horizontal">
                 <Button
                     Width="60"
                     Height="25"
                     Margin="5"
                     Command="{Binding DataContext.ClickAcceptCommand, RelativeSource={RelativeSource AncestorType=ListBox}}"
                     CommandParameter="{Binding}"
                     Content="接受"
                     Style="{StaticResource BlueButtonStyle}"
                     Visibility="{Binding Path=InvitationType, Converter={StaticResource BooleanToVisibilityConverter}}" />
                 <Button
                     Width="60"
                     Height="25"
                     Margin="5"
                     Command="{Binding DataContext.ClickRefuseCommand, RelativeSource={RelativeSource AncestorType=ListBox}}"
                     CommandParameter="{Binding}"
                     Content="忽略"
                     Style="{StaticResource BlueButtonStyle}"
                     Visibility="{Binding Path=InvitationType, Converter={StaticResource BooleanToVisibilityConverter}}" />
                 <Button
                     Width="60"
                     Height="25"
                     Margin="5"
                     Command="{Binding DataContext.ClickAgreeCommand, RelativeSource={RelativeSource AncestorType=ListBox}}"
                     CommandParameter="{Binding}"
                     Content="确认"
                     Style="{StaticResource BlueButtonStyle}"
                     Visibility="{Binding Path=NoticeType, Converter={StaticResource BooleanToVisibilityConverter}}" />
             </StackPanel>
         </StackPanel>
     </Border>
 </DataTemplate>
```

3. 消息分类

这里就是在 Model 层定义好具体的枚举即可。

```csharp
/// <summary>
/// 消息处理结果
/// </summary>
public enum SysMessageResult
{
    /// <summary>
    /// 未处理
    /// </summary>
    Unhandled = 0,
    /// <summary>
    /// 同意
    /// </summary>
    Processed = 1
}

/// <summary>
/// 消息类型
/// </summary>
public enum SysMessageType
{
    /// <summary>
    /// 通知类型
    /// </summary>
    NoticeType = 0,
    /// <summary>
    /// 其他类型
    /// </summary>
    OtherType = 1
}
```

4. 消息处理

消息处理指的是，“确定”、“接受”、“忽略”这三个按钮对消息内容处理的逻辑，如果小伙伴需要可根据自己的需要修改。我这里定义如下：

- 确定：通常处理通知消息，处理仅仅是从消息列表中移除该项不做其他行为。
- 接受：是处理交互类型的按钮，处理从消息列表中移除该项且触发其他业务处理行为。
- 忽略：处理所有类型消息，只是不显示在 UI 中但还会存在于消息列表中下次或空闲时间处理消息。
