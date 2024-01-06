---
title: 码界工坊“文章标题URL别名生成器”上线
slug: code-world-workshop-article-title-url-alias-generator-launched
description: 码界工坊是站长新开的一个提供网页在线工具、跨平台桌面和手机应用的开源项目。站长将终致力于为你带来更高效、更便捷的使用体验。今天，站长荣幸地推出“文章标题URL别名生成器”，帮助你轻松创建文章标题的URL别名，提升SEO效果和用户体验。快来码界工坊，探索更多实用工具吧！
date: 2024-01-06 22:42:19
lastmod: 2024-01-07 00:12:17
copyright: Original
draft: false
cover: https://img1.dotnet9.com/2024/01/cover_01.png
categories: .NET相关
tags: 码界工坊,在线工具,Avalonia UI
---

大家好，我是沙漠尽头的狼。今天，我要向大家介绍一个非常实用的工具，码界工坊。

码界工坊是站长新开发的一个开源项目，提供网页在线工具，同时提供跨平台桌面和手机App版本。我们的目标是为你带来更高效、更便捷的在线工具使用体验。今天，我非常荣幸地推出“文章标题URL别名生成器”，这个工具可以帮助你轻松创建文章标题的URL别名，提升SEO效果和用户体验。

## 这是什么工具？

你是否曾经遇到过这样的问题：你写了一篇很好的文章，但却因为标题过长或者包含特殊字符而导致URL看起来不美观，甚至影响SEO效果？现在，有了“文章标题URL别名生成器”，这些问题都将得到解决。

这个工具提供了三大功能：

1. 中英互译：将中文标题翻译成英文，或将英文标题翻译成中文。
2. 英文转URL别名：将英文标题转换为简洁的URL别名，方便在网站上使用。
3. 中文转URL别名：将中文标题转换为简洁的URL别名，方便在网站上使用。

特别适合文章取好中文标题后，需要在网站发布取文章访问链接生成使用。比如将本文标题《`码界工坊“文章标题URL别名生成器”上线`》在工具中转换为`code-world-workshop-article-title-url-alias-generator-launched`，转换效果如下：

![标题别名转换演示](https://img1.dotnet9.com/2024/01/0101.gif)

使用该工具在[Dotnet9](https://dotnet9.com)网站发布后，在网站上的访问地址是`https://dotnet9.com/2024/01/code-world-workshop-article-title-url-alias-generator-launched`。

## 中英互译、别名转换实现

这个工具的实现原理很简单。我们使用了`GTranslate`包的`YandexTranslator`类来进行中英互译，因为它使用了神经网络机器翻译技术，可以提供更高质量的翻译结果。对于URL别名的转换，我们使用了`Slugify.Core`包，用法简单，转换效果也很好。

来看看实现代码吧，我们先定义转换接口：

```csharp
namespace CodeWF.Core;

/// <summary>
/// 文章标题翻译
/// </summary>
public interface ITranslationService
{
	/// <summary>
	/// 中英文翻译
	/// </summary>
	/// <param name="chineseText"></param>
	/// <returns></returns>
	public Task<string> ChineseToEnglishAsync(string? chineseText);

	/// <summary>
	/// 英中文翻译
	/// </summary>
	/// <param name="englishText"></param>
	/// <returns></returns>
	public Task<string> EnglishToChineseAsync(string? englishText);

	/// <summary>
	/// 英文与URL别名转换
	/// </summary>
	/// <param name="englishText"></param>
	/// <returns></returns>
	public string EnglishToUrlSlug(string? englishText);
}
```

实现转换接口：

```csharp
namespace CodeWF.Core;

/// <summary>
/// 中英互译使用Yandex Translation，Yandex使用了神经网络机器翻译技术（NMT），
/// 以提供更高质量的翻译结果。Yandex Translation 支持多种语言对，包括一些
/// 较少见的语言，并且特别擅长处理欧洲语言之间的翻译。
/// </summary>
public class TranslationService : ITranslationService
{
    private readonly YandexTranslator _translator = new();
    private readonly SlugHelper _slugHelper = new();

    /// <summary>
    /// 中英文翻译
    /// </summary>
    /// <param name="chineseText"></param>
    /// <returns></returns>
    public async Task<string> ChineseToEnglishAsync(string? chineseText)
    {
        return string.IsNullOrWhiteSpace(chineseText)
            ? string.Empty
            : (await _translator.TranslateAsync(chineseText, "en")).Translation;
    }

    /// <summary>
    /// 英中文翻译
    /// </summary>
    /// <param name="englishText"></param>
    /// <returns></returns>
    public async Task<string> EnglishToChineseAsync(string? englishText)
    {
        return string.IsNullOrWhiteSpace(englishText)
            ? string.Empty
            : (await _translator.TranslateAsync(englishText, "zh-CN")).Translation;
    }

    /// <summary>
    /// 英文与URL别名转换
    /// </summary>
    /// <param name="englishText"></param>
    /// <returns></returns>
    public string EnglishToUrlSlug(string? englishText)
    {
        return string.IsNullOrWhiteSpace(englishText) ? string.Empty : _slugHelper.GenerateSlug(englishText);
    }
}
```

来个单元测试验证：

```csharp
namespace CodeWF.Core.Test;

[TestClass]
public class TranslationServiceUnitTest
{
	private readonly ITranslationService _translationService = new TranslationService();

	[TestMethod]
	public async Task Test_ChineseToEnglishAsync_SUCCESS()
	{
		const string chineseText = "码界工坊";

		var englishText = await _translationService.ChineseToEnglishAsync(chineseText);

		Assert.AreEqual(englishText, "Code World Workshop");
	}

	[TestMethod]
	public async Task Test_EnglishToChineseAsync_SUCCESS()
	{
		const string englishText = "Code World Workshop";

		var chineseText = await _translationService.EnglishToChineseAsync(englishText);

		Assert.AreEqual(chineseText, "代码世界工作坊");
	}

	[TestMethod]
	public void Test_EnglishToSlug_SUCCESS()
	{
		const string englishText = "Code World Workshop";

		var urlSlug = _translationService.EnglishToUrlSlug(englishText);

		Assert.AreEqual(urlSlug, "code-world-workshop");
	}

	[TestMethod]
	public async Task Test_ChineseToSlugAsync_SUCCESS()
	{
		const string chineseText = "码界工坊";

		var englishText = await _translationService.ChineseToEnglishAsync(chineseText);

		var urlSlug = _translationService.EnglishToUrlSlug(englishText);

		Assert.AreEqual(urlSlug, "code-world-workshop");
	}
}
```

## 最后的话

文中截图是跨平台桌面版本，采用[Avalonia UI](https://avaloniaui.net/)和控件库[Neumorphism.Avalonia](https://github.com/flarive/Neumorphism.Avalonia)开发，待功能开发到一定程度，站长会介绍工具框架的搭建，下面是工具效果图：

![工具效果图](https://img1.dotnet9.com/2024/01/0103.png)

![工具效果图](https://img1.dotnet9.com/2024/01/0102.gif)

如果你对这个工具感兴趣，可以访问码界工坊官网https://codewf.com，查看更多实用工具。码界工坊的源码也在[Github](https://github.com/dotnet9/CodeWF)开源，欢迎大家关注和贡献。

感谢大家的支持，是你们的支持和鼓励让我不断前进。如果你有任何问题或建议，欢迎随时联系我们。让我们一起打造更美好的码界工坊！