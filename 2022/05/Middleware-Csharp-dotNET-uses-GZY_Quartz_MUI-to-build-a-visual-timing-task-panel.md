---
title: 【中间件】C#/.NET使用GZY.Quartz.MUI搭建可视化的定时任务面板
slug: Middleware-Csharp-dotNET-uses-GZY_Quartz_MUI-to-build-a-visual-timing-task-panel
description: 帮助开发人员通过面板来设置定时任务，主要想做的就是像SwaggerUI一样,项目入侵量小,仅需要在Startup中注入的UI组件
date: 2022-05-26 20:17:45
copyright: Reprinted
author: 黑哥聊dotNet
originaltitle: 【中间件】C#/.NET使用GZY.Quartz.MUI搭建可视化的定时任务面板
originallink: https://mp.weixin.qq.com/s/6Ik5Igi2ei7QTQxanVayCQ
draft: False
cover: https://img1.dotnet9.com/2022/05/5502.png
categories: .NET相关
---

## 前言

GZY.Quartz.MUI是在github上开源的 ASP.NET Core 项目, 它旨在帮助开发人员通过面板来设置定时任务，主要想做的就是像SwaggerUI一样, 项目入侵量小, 仅需要在Startup中注入的UI组件。

**官方地址**: [https://www.cnblogs.com/GuZhenYin/p/15745002.html](https://www.cnblogs.com/GuZhenYin/p/15745002.html)

**主要功能**

1. 增加本地json持久化调度任务, 无需数据库。

2. 增加直接调用本地类方法, 无需通过WebAPI接口。

## 如何使用？

第一步：打开 VS 新建.NET项目，我这里用的是.NET Core Web API 进行演示。

第二步：使用 Nuget 安装 `GZY.Quartz.MUI` 包：

![](https://img1.dotnet9.com/2022/05/5501.png)

第三步：在 StartUp.cs 中的 ConfigureServices 添加 GZY.Quartz.MUI 服务

```csharp
 public void ConfigureServices(IServiceCollection services)
{
	
	services.AddControllers();
	services.AddQuartzUI();
	services.AddQuartzClassJobs(); //添加本地调度任务访问
	//  services.AddSingleton<TestJob>();//注入
}
```

第四步：启用该中间件

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
		if (env.IsDevelopment())
		{
			app.UseDeveloperExceptionPage();
		}


		app.UseRouting();
		app.UseQuartz(); //添加这行代码
		app.UseEndpoints(endpoints =>
		{
			endpoints.MapGet("/", async context =>
			{
				await context.Response.WriteAsync("Hello World!");
			});
		});
	}
```

![](https://img1.dotnet9.com/2022/05/5502.png)

最后运行项目，在浏览器中导航到 `[您的域名]/QuartzUI` 就可以看到该项目已经搭建成功了。

## 简单说明

设置定时任务一共有两种类型：

- 一种是直接调用接口

 输入你想定时启动的接口，我这里用我写的test接口

- 一种是调用本地类

通过调用本地dll的方式，新建的类继承`IJobService`即可

![](https://img1.dotnet9.com/2022/05/5503.png)

## 总结

本篇博客描述了GZY.Quartz.MUI搭建可视化的定时任务面板。