---
title: WPF|分享一个登录界面设计
slug: WPF-Share-a-login-interface-design
description: 分享一个WPF登录界面设计
date: 2022-05-11 07:45:42
copyright: Reprinted
author: 沙漠尽头的狼
originalTitle: WPF|分享一个登录界面设计
originalLink: https://mp.weixin.qq.com/s/seSaQkhkE7dpWqoiQ96fzQ
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_34.png
categories: 
    - WPF
tags: 
    - WPF
    - WPF Design
---

分享一个登录界面，先看效果图：

![](https://img1.dotnet9.com/2022/05/3401.gif)

## 准备

文中使用到了一些图标：

![](https://img1.dotnet9.com/2022/05/3402.png)

我们可以从 [iconfont](https://www.iconfont.cn/)免费下载：

![](https://img1.dotnet9.com/2022/05/3403.gif)

## 代码简单说明

请随手创建一个 WPF 项目（.NET Framework、.NET 5\6\7 皆可），使用`tree /f`命令看看最终的文件结构，和上面的截图一致：

```shell
C:.
│  ModernLoginPage.xaml
│  ModernLoginPage.xaml.cs
│
└─Images
        close.png
        email.png
        github.png
        google.png
        lock.png
        wechat.png
```

简单吧，一个图片目录存放前面下载的图标，一个`xaml`文件做登录界面设计，`xaml.cs`做界面按钮响应事件处理（实际项目建议使用 Mvvm)。

![](https://img1.dotnet9.com/2022/05/3404.png)

看上面的截图，重点说说这两处：

1. 左侧的图形控件

公司有设计师，做这种图片是很简单的，没有的时候，可以使用`Polygon`、`Ellipse`等实现一些简单的效果，让界面不要那么单调：

```xml
<Canvas>
    <Polygon Points="0, 20 230,140 0,270" Fill="#4EB1B6" />
    <Polygon Points="100, 400 200,370 180,470" Fill="#4EB1B6" />
    <Ellipse Margin="250 450 0 0" Width="40" Height="40" Fill="#4EB1B6" />
    <Ellipse Margin="50 400 0 0" Width="20" Height="20" Fill="#4EB1B6" />
</Canvas>
```

2. 右侧的账号文本框和密码框

作者为了演示效果，账号文本框使用的 `一张图片` + `一个标签控件` + `一个文本框` 控件组合实现：

```xml
<Border Padding="10" BorderThickness="1" BorderBrush="#acb0af" Margin="70 7" CornerRadius="5">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Image Source="/TerminalMACS.TestDemo;component/Views/ModernLogin/Images/email.png" Height="20" />
        <TextBlock x:Name="textEmail" MouseDown="textEmail_MouseDown" Text="邮箱"
                    Style="{StaticResource textHint}" />
        <TextBox x:Name="txtEmail" TextChanged="txtEmail_TextChanged" Style="{StaticResource textBox}" />
    </Grid>
</Border>
```

```csharp
private void textEmail_MouseDown(object sender, MouseButtonEventArgs e)
{
    txtEmail.Focus();
}

private void txtEmail_TextChanged(object sender, TextChangedEventArgs e)
{
    if (!string.IsNullOrEmpty(txtEmail.Text) && txtEmail.Text.Length > 0)
    {
        textEmail.Visibility = Visibility.Collapsed;
    }
    else
    {
        textEmail.Visibility = Visibility.Visible;
    }
}
```

代码比较简单，.cs 文件代码：

- 鼠标点击标签时，将账号文本框设置为焦点控件，提高用户体验
- 文本框中输入账号信息时 显示|隐藏 标签

密码框逻辑同账号文本框：

```xml
<Border Padding="10" BorderThickness="1" BorderBrush="#acb0af" Margin="70 7" CornerRadius="5">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="auto" />
            <ColumnDefinition Width="*" />
        </Grid.ColumnDefinitions>

        <Image Source="/TerminalMACS.TestDemo;component/Views/ModernLogin/Images/lock.png" Height="20" />
        <TextBlock x:Name="textPassword" MouseDown="textPassword_MouseDown" Text="密码"
                    Style="{StaticResource textHint}" />
        <PasswordBox x:Name="txtPassword" PasswordChanged="txtPassword_TextChanged"
                        Style="{StaticResource textBox}" />
    </Grid>
</Border>
```

```csharp
private void textPassword_MouseDown(object sender, MouseButtonEventArgs e)
{
    txtPassword.Focus();
}

private void txtPassword_TextChanged(object sender, RoutedEventArgs e)
{
    if (!string.IsNullOrEmpty(txtPassword.Password) && txtPassword.Password.Length > 0)
    {
        textPassword.Visibility = Visibility.Collapsed;
    }
    else
    {
        textPassword.Visibility = Visibility.Visible;
    }
}
```

## 参考：

- 油管视频：[C# WPF UI | How to Design Modern Login Page in WPF](https://www.youtube.com/watch?v=PoPUB1_q2kE&t=907s)
- 油管视频作者：[C# WPF UI Academy](https://www.youtube.com/channel/UCtVawNW7C2t6AX1vex6a_vw)
- 本文代码：[ModernLogin](https://github.com/dotnet9/TerminalMACS.ManagerForWPF/tree/master/src/TerminalMACS.TestDemo/Views/ModernLogin)
