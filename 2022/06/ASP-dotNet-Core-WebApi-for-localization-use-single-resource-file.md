---
title: ASP.NET Core WebAPI实现本地化（单资源文件）
slug: ASP-dotNet-Core-WebApi-for-localization-use-single-resource-file
description: 微软默认的是一个类对应多个资源文件的方式，使用起来是比较麻烦的，本文介绍单资源文件使用方式，即整个项目所有类对应一套多语言资源文件。
date: 2022-06-22 22:37:47
copyright: Reprinted
author: HueiFeng
originalTitle: ASP.NET Core WebAPI实现本地化（单资源文件）
originalLink: https://www.cnblogs.com/yyfh/archive/2020/05/30/12995208.html
draft: False
cover: https://img1.dotnet9.com/2022/06/cover_15.jpeg
categories: 
    - .NET
tags: 
    - Web API
---

在 Startup `ConfigureServices` 注册本地化所需要的服务`AddLocalization`和 `Configure<RequestLocalizationOptions>`

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddLocalization();
    services.Configure<RequestLocalizationOptions>(options =>
    {
        var supportedCultures = new List<CultureInfo>
        {
            new CultureInfo("en-us"),
            new CultureInfo("zh-cn")
        };

        options.DefaultRequestCulture = new RequestCulture(culture: "en-us", uiCulture: "en-us");
        options.SupportedCultures = supportedCultures;
        options.SupportedUICultures = supportedCultures;
        options.RequestCultureProviders = new IRequestCultureProvider[] { new RouteDataRequestCultureProvider { IndexOfCulture = 1, IndexofUiCulture = 1 } };
    });
    services.Configure<RouteOptions>(options =>
    {
        options.ConstraintMap.Add("culture", typeof(LanguageRouteConstraint));
    });
    services.AddControllers();
}
```

在 Startup.cs 类的 `Configure` 方法中添加请求本地化中间件。

```csharp
var localizeOptions = app.ApplicationServices.GetService<IOptions<RequestLocalizationOptions>>();
            app.UseRequestLocalization(localizeOptions.Value);
```

`RequestCultureProvider` 它使用简单的委托来确定当前的本地化区域性，当然我们还可以通过`RequestCultureProvider`自定义源的请求区域信息比如说配置文件或者数据库都是可以的.或者说我们可以选用默认的一些方式让我们去获取到当前区域.

ASP.NET Core 本地化默认向我们提供了四个方式，可用于确定正在执行的请求的当前区域性：

- QueryStringRequestCultureProvider
- CookieRequestCultureProvider
- AcceptLanguageHeaderRequestCultureProvider
- CustomRequestCultureProvider

如下所示我将通过路由的方式,去确定当前区域

```csharp
public class RouteDataRequestCultureProvider : RequestCultureProvider
{
    public int IndexOfCulture;
    public int IndexofUiCulture;

    public override Task<ProviderCultureResult> DetermineProviderCultureResult(HttpContext httpContext)
    {
        if (httpContext == null)
            throw new ArgumentNullException(nameof(httpContext));
        string uiCulture;

        string culture = uiCulture = httpContext.Request.Path.Value.Split('/')[IndexOfCulture];

        var providerResultCulture = new ProviderCultureResult(culture, uiCulture);

        return Task.FromResult(providerResultCulture);
    }
}
```

通过如下代码片段实现 IRouteConstraint 对路由做相应的约束

```csharp
public class LanguageRouteConstraint : IRouteConstraint
{
    public bool Match(HttpContext httpContext, IRouter route, string routeKey, RouteValueDictionary values, RouteDirection routeDirection)
    {

        if (!values.ContainsKey("culture"))
            return false;

        var culture = values["culture"].ToString();
        return culture == "en-us" || culture == "zh-cn";
    }
}
```

添加区域资源文件

![](https://img1.dotnet9.com/2022/06/1501.png)

```csharp
[Route("{culture:culture}/[controller]")]
[ApiController]
public class HomeController : ControllerBase
{
    private readonly IStringLocalizer<Resource> localizer;
    public HomeController(IStringLocalizer<Resource> localizer)
    {
        this.localizer = localizer;
    }
    public string Get()
    {
        return localizer["Home"];
    }
}
```

![](https://img1.dotnet9.com/2022/06/1502.png)

Reference：[https://github.com/hueifeng/BlogSample/tree/master/src/LocalizationSingleResx](https://github.com/hueifeng/BlogSample/tree/master/src/LocalizationSingleResx)
