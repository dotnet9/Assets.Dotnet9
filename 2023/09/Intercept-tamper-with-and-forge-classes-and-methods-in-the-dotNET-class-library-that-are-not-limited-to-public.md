---
title: 拦截|篡改|伪造.NET类库中不限于public的类和方法
slug: Intercept-tamper-with-and-forge-classes-and-methods-in-the-dotNET-class-library-that-are-not-limited-to-public
description: 本文除了回顾拦截.NET类库中的方法，实现方法参数的篡改、方法返回结果的伪造，再着重介绍.NET类库中非public类及方法如何拦截。
date: 2023-09-22 20:49:57
lastmod: 2023-09-22 23:58:32
copyright: Default
draft: false
cover: https://img1.dotnet9.com/2023/09/cover_06.gif
categories: .NET相关
---

大家好，我是沙漠尽头的狼。

本文首发于[Dotnet9](https://dotnet9.com/2023/09/Intercept-tamper-with-and-forge-classes-and-methods-in-the-dotNET-class-library-that-are-not-limited-to-public)，介绍使用`Lib.Harmony`库拦截第三方`.NET`库方法，达到不修改其源码并能实现修改方法逻辑、预期行为的效果，行文目录：

1. 什么是方法拦截？
2. 示例程序拦截
3. 非public方法怎么拦截？
4. 总结

## 1. 什么是方法拦截？

方法拦截是指在方法被调用之前或之后，通过插入自定义的代码来修改方法的行为。通过方法拦截，开发人员可以在不修改原始代码的情况下，对方法的输入参数进行验证、修改方法的返回值、记录方法的调用日志等操作。

本文使用`Lib.Harmony`库实现第三方库方法的拦截，关于该库站长写过[快学会这个技能-.NET API拦截技法]([快学会这个技能-.NET API拦截技法 - Dotnet9](https://dotnet9.com/2023/02/Learn-this-skill-Dotnet-API-interception-technique))，这里不再赘述。

## 2. 示例程序拦截

### 2.1. 编写取数字段落的程序

创建一个`.NET`库`TestDll`，添加工具类`TestTool`：

```csharp
namespace TestDll;

public class TestTool
{
    /// <summary>
    /// 带数字的优美段落
    /// </summary>
    private readonly List<string> _sentences = new()
    {
        "一，是孤独的象征，寂寞的代言人， 它独自站在诗句的起点，引人遐想。",
        "二，是相对的存在，对立的伴侣， 它们如影随形，互相依存。",
        "三，是完美的数字，三角的稳定， 它给诗歌带来了和谐的节奏。",
        "四，是平衡的象征，四季的轮回， 它让诗歌的结构更加坚实。",
        "五，是生机勃勃的数字，五彩斑斓的花朵， 它们在诗歌中绽放出美丽的画面。 ",
        "六，是平凡的数字，六边形的形状， 它们给诗歌带来了一种稳定的感觉。",
        "七，是神秘的数字，七色的虹霓， 它们在诗歌中散发出神奇的光芒。",
        "八，是无限的数字，八方的宇宙， 它们让诗歌的想象力无限延伸。",
        "九，是完美的数字，九曲的江河， 它们给诗歌带来了一种流动的美感。 ",
        "十，是圆满的数字，十全十美的象征， 它们让诗歌的结尾更加完美。"
    };

    /// <summary>
    /// 取对应数字的段落
    /// </summary>
    /// <param name="number"></param>
    /// <returns></returns>
    public string GetNumberSentence(int number)
    {
        var mo = number % _sentences.Count;

        // 个位为0，取最后一
        if (mo == 0)
        {
            mo = 10;
        }

        if (mo == 6)
        {
            mo = 1;
        }

        var sentencesIndex = mo - 1;
        return _sentences[sentencesIndex];
    }
}
```

上面的方法`GetNumberSentence`逻辑：传入一个整型`number`参数，除10取模，返回10以内的数字美文，其中如果模为6会取数字1的段落（这是为了验证拦截逻辑添加的）。

下面是写的一个`AvaloniaUI`程序测试界面，UI不是本文重点，这里就直接贴动图和代码截图了，文末也有源码链接：

![](https://img1.dotnet9.com/2023/09/0701.gif)

![](https://img1.dotnet9.com/2023/09/0702.png)

### 2.2. 为什么个位数字为6时，总是显示数字1的段落呢？

我们可以使用`Lib.Harmony`拦截`GetNumberSentence`方法

## 3. 非public方法怎么拦截？
## 4. 总结

- 技术交流加群请添加站长微信号：dotnet9com
- 文中示例代码：[MultiVersionLibrary](https://github.com/dotnet9/TerminalMACS.ManagerForWPF/tree/master/src/Demo/MultiVersionLibrary)

