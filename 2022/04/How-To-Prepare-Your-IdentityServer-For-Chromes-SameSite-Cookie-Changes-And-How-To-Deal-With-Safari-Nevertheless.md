---
title: 使用IdentityServer出现过SameSite Cookie这个问题吗？
slug: How-To-Prepare-Your-IdentityServer-For-Chromes-SameSite-Cookie-Changes-And-How-To-Deal-With-Safari-Nevertheless
description: 如果您为 Web 应用程序和身份验证服务器使用单独的域，那么 Chrome 中的这种更改很可能会破坏部分用户的会话体验
date: 2022-04-28 06:47:21
copyright: Reprint
author: Sebastian Gingter
originaltitle: 使用IdentityServer出现过SameSite Cookie这个问题吗？
originallink: https://www.thinktecture.com/en/identityserver/prepare-your-identityserver/
draft: False
cover: https://img1.dotnet9.com/2022/04/cover_41.jpg
categories: .NET相关
---

>原文作者：Sebastian Gingter
>
>原文链接：https://www.thinktecture.com/en/identityserver/prepare-your-identityserver/
>
>译者：沙漠尽头的狼
>
>译文链接：https://dotnet9.com/2022/04/How-To-Prepare-Your-IdentityServer-For-Chromes-SameSite-Cookie-Changes-And-How-To-Deal-With-Safari-Nevertheless

>本文是作者2019年的一篇分享，里面的一些观点和使用的技术，对我们现在的开发依然有效，建议查看原文阅读，对本文翻译如有疑问，欢迎[提PR](https://github.com/dotnet9/Assets.Dotnet9/pulls)。

首先，好消息：Google 将在 2020 年 2 月发布 Chrome 80时，包括 Google 实施的“渐进式更好的 Cookie”(Incrementally better Cookies)，这将使网络成为一个更安全的地方，并有助于确保用户获得更好的隐私（站长注：现在是2022年4月28号，Chrome已经发布了多个更新版本）。

坏消息是，这个新实现是浏览器决定如何向服务器发送 cookie 的重大变化。首先，如果您为 Web 应用程序和身份验证服务器使用单独的域，那么 Chrome 中的这种更改很可能会破坏部分用户的会话体验。第二个问题是它还可能使您的部分用户无法再次正确注销您的系统。

## 1. 首先，这个 SameSite 是关于什么的？

Web 是一个非常开放的平台：Cookie 是在大约 20 年前设计的，以及 2011 年在 [RFC 6265](https://tools.ietf.org/html/rfc6265/)中重新审视该设计时，跨站请求伪造 (CSRF) 攻击和过度用户跟踪还不是什么大事。

简而言之，正常的 Cookie 规范说，如果为特定域设置了 Cookie，它将在浏览器发出的每个请求时带上Cookie发送到该域。无论您是否直接导航到该域，如果浏览器只是从该域加载资源（即图像），向其发送 POST 请求或将其中的一部分嵌入到 iframe 中。但也许对于后一种可能性，您不希望浏览器自动将用户会话 Cookie 发送到您的服务器，因为这将允许任何网站在该用户的上下文中执行针对您的服务器的请求的 JavaScript，而不会引起他们的注意。

为了防止这种情况发生，  [SameSite cookie 规范](https://tools.ietf.org/html/draft-west-first-party-cookies-07/) 是在 2016 年起草的。它让您可以更好地控制何时应该或不应该发送 cookie：当您设置 cookie 时，您现在可以为每个 cookie 明确指定浏览器何时应将其添加到请求。为此，当浏览器位于您自己的域中时，它引入了同站点 cookie 的概念，而当浏览器在不同域中导航但向您的域发送请求时，它引入了跨站点 cookie 的概念。

为了向后兼容，相同站点 cookie 的默认设置并没有改变以前的行为。您必须选择加入该新功能并明确设置您的 cookie `SameSite=Lax` 或 `SameSite=Strict` 使其更安全。这已在 .NET Framework(包括.NET CORE) 和所有常见浏览器中实现。 Lax 意味着，cookie 将在初始导航时发送到服务器， Strict 意味着 cookie 只会在您已经在该域上时发送（即初始导航后的第二个请求）。

遗憾的是，这项新功能的采用速度很慢（根据 2019 年 3 月 Chrome 的遥测数据 【[来源](https://tools.ietf.org/html/draft-west-http-state-tokens-00#section-1.2/) 】，全球范围内 Chrome 上处理的所有 cookie 中只有 0.1% 使用 SameSite 标志）。

谷歌决定推动采用该功能。为了强制执行，他们决定更改世界上最常用的浏览器的默认设置：Chrome 80 将 `必须` 指定一个新的设置 `SameSite=None` 来保留处理 cookie 的旧方式，如果您像旧规范建议的那样省略 SameSite 字段，它将 cookie 视为使用 `SameSite=Lax`.

*请注意： 该设置 `SameSite=None` 仅在 cookie 也被标记为 `Secure` 并需要 HTTPS 连接时才有效。*

*更新：* 如果您想了解有关 SameSite cookie 的更多背景信息，有一篇包含 [所有细节的新文章](https://www.thinktecture.com/en/identityserver/samesite-cookies-in-a-nutshell/)。

## 2. 这对我有影响吗？如果是，怎么做？

如果您有一个单页面 Web 应用程序 (SPA)，它针对托管在不同域上的身份提供者（IdP，例如 [IdentityServer 4](https://identityserver.io/)）进行身份验证，并且该应用程序使用所谓的静默令牌刷新，您就会受到影响。

登录 IdP 时，它会为您的用户设置一个会话 cookie，该 cookie 来自 IdP 域。在身份验证流程结束时，来自不同域的应用程序会收到某种访问令牌，这些令牌通常不会很长时间。当该令牌过期时，应用程序将无法再访问资源服务器 (API)，如果每次发生这种情况时用户都必须重新登录，这将是非常糟糕的用户体验。

为防止这种情况，您可以使用静默令牌刷新。在这种情况下，应用程序会创建一个用户不可见的 iframe，并在该 iframe 中再次启动身份验证过程。IdP 的网站在 iframe 中加载，如果浏览器沿 IdP 发送会话 cookie，则识别用户并发出新令牌。

现在 iframe 存在于托管在应用程序域中的 SPA 中，其内容来自 IdP 域。如果 cookie 明确指出 `SameSite=None`，Chrome 80 只会将该 cookie 从 iframe 发送到 IdP，这被认为是跨站点请求。 如果不是这种情况，您的静默令牌刷新将在 2 月 Chrome 80 发布时中断。

还有其他情况可能会给您带来问题：首先，如果您在 Web 应用程序或网站中嵌入源自另一个域的元素，例如视频的自动播放设置，并且这些需要 cookie 才能正常运行，这些也会需要设置 SameSite 策略。如果您的应用程序需要从依赖于 cookie 身份验证的浏览器请求第 3 方 API，这同样适用。

*注意：* 显然您只能更改您自己的服务器关于cookie设置的cookie 行为。如果您碰巧使用了不受您控制的其他域中的元素，您需要联系第 3 方，并在出现问题时要求他们更改 cookie。

## 3. 好的，我将更改我的代码并将 SameSite 设置为 None。我现在可以了，对吧？

不幸的是，Safari [有一个“错误”](https://bugs.webkit.org/show_bug.cgi?id=198181)。此错误导致 Safari 无法将新引入的值 `None` 识别为 SameSite 设置的有效值。当 Safari 遇到无效值时，它会将 `SameSite=Strict` 当作已指定的设置，并且不会将会话 cookie 发送到 IdP。此错误已在 iOS 13 和 macOS 10.15 Catalina 上的 Safari 13 中修复，但不会向后移植到 macOS 10.14 Mojave 和 iOS 12，它们仍然拥有非常大的用户群。

所以，我们现在陷入了两难境地：要么我们忽略 SameSite 策略，我们的 Chrome 用户无法进行静默刷新，要么我们设置 `SameSite=None` 并锁定 iPhone、iPad 和 Mac 用户无法更新，或者旧设备无法更新到最新版本的 iOS 和 macOS。

## 4. 有没有办法确定我受到影响？

幸运的是，是的。如果您已经设置 `SameSite=None`，您可能已经注意到您的应用程序或网站在 iOS 12 和 macOS 10.4 上的 Safari 中无法正常工作。如果没有，请确保在这些版本的 Safari 中测试您的应用程序或网站。

如果您根本不设置 SameSite 值，您只需在 Chrome 中打开您的应用程序并打开开发人员工具即可。您将看到以下警告：

```shell
A cookie associated with a cross-site resource at {cookie domain} was set without the `SameSite` attribute.
A future release of Chrome will only deliver cookies with cross-site requests if they are set with `SameSite=None` and `Secure`.
You can review cookies in developer tools under Application>Storage>Cookies and see more details at
https://www.chromestatus.com/feature/5088147346030592 and
https://www.chromestatus.com/feature/5633521622188032.
```

如果您已经设置 `SameSite=None` 但忘了设置 `Secure` 标志，您将收到以下警告：

```shell
A cookie associated with a resource at {cookie domain} was set with `SameSite=None` but without `Secure`.
A future release of Chrome will only deliver cookies marked `SameSite=None` if they are also marked `Secure`.
You can review cookies in developer tools under Application>Storage>Cookies and
see more details at https://www.chromestatus.com/feature/5633521622188032.
```

## 5. 那么，我该如何真正解决这个问题？我需要 Chrome 和 Safari 正常使用。

我们，也就是我的同事 Boris Wilhelms 和我自己，对该主题进行了一些研究，并找到且验证了解决方案。微软的 Barry Dorrans也有一篇 [关于这个问题的好博文](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)。该解决方案并不美观，遗憾的是需要在服务器端进行浏览器嗅探，但这是一个简单的解决方案，在过去的几周里，我们已经在我们的几个客户项目中成功实现了这一点。

要解决这个问题，我们首先需要确保需要通过跨站点请求传输的 cookie（例如我们的会话 cookie）设置为 `SameSite=None` 和 `Secure`。我们需要在项目代码中找到该 cookie 的选项并进行相应调整。这解决了 Chrome 的问题并引入了 Safari 问题。

然后我们将以下类和代码片段添加到项目中。这会在 ASP.NET Core Web 应用程序中添加和配置 cookie 策略。此策略将检查是否设置了 cookie 为 `SameSite=None` 。如果是这种情况，它将检查浏览器的用户代理，并确定这是否是一个浏览器的设置有问题，比如我们受影响的 Safari 版本。如果也是这种情况，它会将 cookies SameSite 值设置为`unspecified`(未指定)，这反过来将完全阻止设置 SameSite，从而为这些浏览器重新创建当前默认行为。

*请注意*： 此处提供的解决方案适用于 `.NET Core`。对于完整的基于 `.NET Framework` 的项目，您需要查看[Barry Dorran 的帖子](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)中指定的版本之一 。


### 5.1 要添加到项目中的类
 
```C#
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Http;

namespace Microsoft.Extensions.DependencyInjection
{
   public static class SameSiteCookiesServiceCollectionExtensions
   {
      /// <summary>
      /// -1 defines the unspecified value, which tells ASPNET Core to NOT
      /// send the SameSite attribute. With ASPNET Core 3.1 the
      /// <seealso cref="SameSiteMode" /> enum will have a definition for
      /// Unspecified.
      /// </summary>
      private const SameSiteMode Unspecified = (SameSiteMode) (-1);

      /// <summary>
      /// Configures a cookie policy to properly set the SameSite attribute
      /// for Browsers that handle unknown values as Strict. Ensure that you
      /// add the <seealso cref="Microsoft.AspNetCore.CookiePolicy.CookiePolicyMiddleware" />
      /// into the pipeline before sending any cookies!
      /// </summary>
      /// <remarks>
      /// Minimum ASPNET Core Version required for this code:
      ///   - 2.1.14
      ///   - 2.2.8
      ///   - 3.0.1
      ///   - 3.1.0-preview1
      /// Starting with version 80 of Chrome (to be released in February 2020)
      /// cookies with NO SameSite attribute are treated as SameSite=Lax.
      /// In order to always get the cookies send they need to be set to
      /// SameSite=None. But since the current standard only defines Lax and
      /// Strict as valid values there are some browsers that treat invalid
      /// values as SameSite=Strict. We therefore need to check the browser
      /// and either send SameSite=None or prevent the sending of SameSite=None.
      /// Relevant links:
      /// - https://tools.ietf.org/html/draft-west-first-party-cookies-07#section-4.1
      /// - https://tools.ietf.org/html/draft-west-cookie-incrementalism-00
      /// - https://www.chromium.org/updates/same-site
      /// - https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/
      /// - https://bugs.webkit.org/show_bug.cgi?id=198181
      /// </remarks>
      /// <param name="services">The service collection to register <see cref="CookiePolicyOptions" /> into.</param>
      /// <returns>The modified <see cref="IServiceCollection" />.</returns>
      public static IServiceCollection ConfigureNonBreakingSameSiteCookies(this IServiceCollection services)
      {
         services.Configure<CookiePolicyOptions>(options =>
         {
            options.MinimumSameSitePolicy = Unspecified;
            options.OnAppendCookie = cookieContext =>
               CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
            options.OnDeleteCookie = cookieContext =>
               CheckSameSite(cookieContext.Context, cookieContext.CookieOptions);
         });

         return services;
      }

      private static void CheckSameSite(HttpContext httpContext, CookieOptions options)
      {
         if (options.SameSite == SameSiteMode.None)
         {
            var userAgent = httpContext.Request.Headers["User-Agent"].ToString();

            if (DisallowsSameSiteNone(userAgent))
            {
               options.SameSite = Unspecified;
            }
         }
      }

      /// <summary>
      /// Checks if the UserAgent is known to interpret an unknown value as Strict.
      /// For those the <see cref="CookieOptions.SameSite" /> property should be
      /// set to <see cref="Unspecified" />.
      /// </summary>
      /// <remarks>
      /// This code is taken from Microsoft:
      /// https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/
      /// </remarks>
      /// <param name="userAgent">The user agent string to check.</param>
      /// <returns>Whether the specified user agent (browser) accepts SameSite=None or not.</returns>
      private static bool DisallowsSameSiteNone(string userAgent)
      {
         // Cover all iOS based browsers here. This includes:
         //   - Safari on iOS 12 for iPhone, iPod Touch, iPad
         //   - WkWebview on iOS 12 for iPhone, iPod Touch, iPad
         //   - Chrome on iOS 12 for iPhone, iPod Touch, iPad
         // All of which are broken by SameSite=None, because they use the
         // iOS networking stack.
         // Notes from Thinktecture:
         // Regarding https://caniuse.com/#search=samesite iOS versions lower
         // than 12 are not supporting SameSite at all. Starting with version 13
         // unknown values are NOT treated as strict anymore. Therefore we only
         // need to check version 12.
         if (userAgent.Contains("CPU iPhone OS 12")
            || userAgent.Contains("iPad; CPU OS 12"))
         {
            return true;
         }

         // Cover Mac OS X based browsers that use the Mac OS networking stack.
         // This includes:
         //   - Safari on Mac OS X.
         // This does not include:
         //   - Chrome on Mac OS X
         // because they do not use the Mac OS networking stack.
         // Notes from Thinktecture:
         // Regarding https://caniuse.com/#search=samesite MacOS X versions lower
         // than 10.14 are not supporting SameSite at all. Starting with version
         // 10.15 unknown values are NOT treated as strict anymore. Therefore we
         // only need to check version 10.14.
         if (userAgent.Contains("Safari")
            && userAgent.Contains("Macintosh; Intel Mac OS X 10_14")
            && userAgent.Contains("Version/"))
         {
            return true;
         }

         // Cover Chrome 50-69, because some versions are broken by SameSite=None
         // and none in this range require it.
         // Note: this covers some pre-Chromium Edge versions,
         // but pre-Chromium Edge does not require SameSite=None.
         // Notes from Thinktecture:
         // We can not validate this assumption, but we trust Microsofts
         // evaluation. And overall not sending a SameSite value equals to the same
         // behavior as SameSite=None for these old versions anyways.
         if (userAgent.Contains("Chrome/5") || userAgent.Contains("Chrome/6"))
         {
            return true;
         }

         return false;
      }
   }
}
```

### 5.2 配置和启用 cookie 策略

要使用此 cookie 策略，您需要将以下内容添加到您的启动代码中：

```C#
public void ConfigureServices(IServiceCollection services)
{
   // Add this
   services.ConfigureNonBreakingSameSiteCookies();
}

public void Configure(IApplicationBuilder app)
{
   // Add this before any other middleware that might write cookies
   app.UseCookiePolicy();

   // This will write cookies, so make sure it's after the cookie policy
   app.UseAuthentication();
}
```

## 6. 好的。我现在完成了吗？

除了彻底的测试，特别是在 Chrome 79 中激活了“默认 cookie 的 SameSite”标志以及 macOS 和 iOS 上受影响的 Safari 版本，是的，你现在应该没事了。要在 Chrome 79 中进行测试，请导航到 `chrome://flags`、搜索 `samesite` 并启用该 `SameSite by default cookies` 标志。重新启动浏览器，您可以立即测试即将发生的更改。

严肃的说：确保您的静默刷新 - 或者通常是需要 cookie 的跨站点请求 - 仍然可以在这些设备和浏览器上运行。

## 7. 我不能简单地等待我的身份验证服务器供应商为我解决这个问题吗？

这是不太可能的。在我们这里的具体示例中，实际上管理 cookie 的不是 IdentityServer 本身。IdentityServer 依赖于 ASP.NET Core 框架的内置身份验证系统，这是管理会话 cookie 的地方。虽然 ASP.NET Core 框架已更新以支持新 `SameSite` 值 `None` 和技术设置 `Unspecified` （不发送 `SameSite` ）， 但[微软表示](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) 他们不能直接在 ASP.NET Core 中引入用户代理嗅探。所以这真的取决于你和你现有的项目。

## 8. 总结

Chrome 将很快（2020 年 2 月）更改其处理 cookie 的默认行为。将来，它将默认 `SameSite` 被明确设置为`None`标志 和 `Secure` 标志设置，以允许将 cookie 添加到某些跨站点请求。如果你这样做，常见版本的 Safari 就会对此感到厌烦。

为确保所有浏览器都满意，您将所有受影响的 cookie 设置为 `Secure` 和 `SameSite=None`，然后添加一个 cookie 策略（如上所示的代码），该策略可以覆盖这些设置并再次为无法对 `None` 正确解释该值的浏览器删除`SameSite`标志.