---
title: WPF中XAML中使用String.Format格式化字符串示例
slug: Using-string-In-XAML-in-WPF-Format-format-string-example
description: 字符串格式化
date: 2022-05-22 21:50:47
copyright: Reprinted
author: 眾尋
originalTitle: WPF中XAML中使用String.Format格式化字符串示例
originalLink: https://www.cnblogs.com/ZXdeveloper/p/15513657.html
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_51.jpeg
categories: 
    - .NET
tags: 
    - WPF
---

1. 货币格式

```xml
<TextBlock Text="{Binding Price, StringFormat={}{0:C}}" /> // $123.46
```

2. 货币格式，一位小数

```xml
<TextBox Text="{Binding Price, StringFormat={}{0:C1}}" /> // $123.5
```

3. 前文字

```xml
<TextBox Text="{Binding Price, StringFormat=单价：{0:C}}" /> //单价：$123.46
```

4. 后文字

```xml
<TextBox Text="{Binding Price, StringFormat={}{0}元}" /> // 123.45678元
```

5. 固定的位数，位数不能少于未格式化前，仅支持整型

```xml
<TextBox Text="{Binding Count, StringFormat={}{0:D6}}" /> // 086723
```

6. 指定小数点后的位数

```xml
<TextBox Text="{Binding Total, StringFormat={}{0:F4}}" /> // 28768234.9329
```

7. 用分号隔开的数字，并指定小数点后的位数

```xml
<TextBox Text="{Binding Total, StringFormat={}{0:N3}}" /> // 28,768,234.933
```

8. 格式化百分比

```xml
<TextBox Text="{Binding Persent, StringFormat={}{0:P1}}" /> // 78.9 %
```

9. 占位符

```xml
<TextBox Text="{Binding Price, StringFormat={}{0:0000.00}}" /> // 0123.46
```

```xml
<TextBox Text="{Binding Price, StringFormat={}{0:####.##}}" /> // 123.46
```

10. 日期/时间

```xml
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:d}}" /> // 5/4/2015
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:D}}" /> // Monday, May 04, 2015
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:f}}" /> // Monday, May 04, 2015 5:46 PM
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:F}}" /> // Monday, May 04, 2015 5:46:56 PM
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:g}}" /> // 5/4/2015 5:46 PM
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:G}}" /> // 5/4/2015 5:46:56 PM
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:m}}" /> // May 04
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:M}}" /> // May 04
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:t}}" /> // 5:46 PM
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:T}}" /> // 5:46:56 PM
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:yyyy年MM月dd日}}" /> // 2015年05月04日
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:yyyy-MM-dd}}" /> // 2015-05-04
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:yyyy-MM-dd HH:mm}}" /> // 2015-05-04 17:46
<TextBox Text="{Binding DateTimeNow, StringFormat={}{0:yyyy-MM-dd HH:mm:ss}}" /> // 2015-05-04 17:46:56
```

11. 多重绑定

```xml
<TextBox.Text>
    <MultiBinding StringFormat="姓名：{0}{1}">
         <Binding Path="FristName" />
         <Binding Path="LastName" />
    </MultiBinding>
 </TextBox.Text>
```

```shell
// 姓名：AAbb
```

12. 多重绑定中的特殊字符

```xml
<TextBox.Text>
     <MultiBinding StringFormat="姓名：{0}&#x09;{1}">
           <Binding Path="FristName" />
           <Binding Path="LastName" />
     </MultiBinding>
 </TextBox.Text>

<!--
\a  &#x07;  BEL
\b  &#x08;  BS - Backspace
\f  &#x0c;  FF - Formfeed
\n  &#x0a;  LF, NL - Linefeed, New Line
\r  &#x0d;  CR - Carriage return
\t  &#x09;  HT - Tab, Horizontal Tabelator
\v  &#x0b;  VT - Vertical Tabelator
-->
```

---

> 原文作者：眾尋
>
> 原文链接：https://www.cnblogs.com/ZXdeveloper/p/15513657.html
