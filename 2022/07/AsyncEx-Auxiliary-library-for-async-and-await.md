---
title: AsyncEx - async/await 的辅助库
slug: AsyncEx-Auxiliary-library-for-async-and-await
description: async/await 的辅助库
date: 2022-07-08 07:21:19
copyright: Reprinted
author: 黑哥聊dotNet
originaltitle: AsyncEx - async/await 的辅助库
originallink: https://mp.weixin.qq.com/s/WyGsouPOZ-85NQRqpfYZzQ
draft: False
cover: https://img1.dotnet9.com/2022/07/cover_10.png
categories: .NET
tags: .NET
---

## 简介

async/await 的辅助库。

支持 netstandard1.3 (包括 .NET 4.6, .NET Core 1.0, Xamarin.iOS 10, Xamarin.Android 7, Mono 4.6, and Universal Windows 10).

## 安装

安装Nuget包 [Nito.AsyncEx](http://www.nuget.org/packages/Nito.AsyncEx)

```shell
Install-Package Nito.AsyncEx -Version 5.1.2
```

## 使用

### AsyncLock

构造`AsyncLock`函数可以采用异步等待队列；传递自定义等待队列以指定您自己的排队逻辑。

```csharp
private readonly AsyncLock _mutex = new AsyncLock();
public async Task UseLockAsync()
{
  // AsyncLock can be locked asynchronously
  using (await _mutex.LockAsync())
  {
    // It's safe to await while the lock is held
    await Task.Delay(TimeSpan.FromSeconds(1));
  }
}
```

`AsyncLock`也完全支持取消

```csharp
public async Task UseLockAsync()
{
  // Attempt to take the lock only for 2 seconds.
  var cts = new CancellationTokenSource(TimeSpan.FromSeconds(2));
  
  // If the lock isn't available after 2 seconds, this will
  //  raise OperationCanceledException.
  using (await _mutex.LockAsync(cts.Token))
  {
    await Task.Delay(TimeSpan.FromSeconds(1));
  }
}
```

`AsyncLock` 也有一个同步 API。 这允许一些线程异步获取锁，而其他线程同步获取锁（阻塞线程）。

```csharp
public async Task UseLockAsync()
{
  using (await _mutex.LockAsync())
  {
    await Task.Delay(TimeSpan.FromSeconds(1));
  }
}

public void UseLock()
{
  using (_mutex.Lock())
  {
    Thread.Sleep(TimeSpan.FromSeconds(1));
  }
}
```

### AsyncContext

该`AsyncContext`类型提供了执行异步操作的上下文。`await`关键字需要返回一个上下文。对于大多数客户端程序，这是一个 `UI 上下文`；对于大多数服务端程序，这是一个`线程池上下文`。`AsyncContextThread`是一个单独的线程或任务，它运行`AsyncContext`。 `AsyncContextThread`不是从`Thread`类派生的。`AsyncContext`线程在创建后立即开始运行。`AsyncContextThread`将一直停留在其循环中，直到另一个线程调用`JoinAsync`。 处置 an AsyncContextThread也会要求它退出。

```csharp
class Program
{
  static async Task<int> AsyncMain()
  {
    ..
  }

  static int Main(string[] args)
  {
    return AsyncContext.Run(AsyncMain);
  }
}
```

### AsyncMonitor

在监视器中，任务可能决定通过调用来等待信号`WaitAsync`。在等待期间，它会暂时离开监视器，直到收到信号并重新进入监视器。返回的任务`EnterAsync`将进入`Completed`监视器后的状态。如果在等待满足之前发出信号，则相同的任务将进入`Canceled`状态；`CancellationToken`在这种情况下，该任务不会进入监视器。

返回的任务`WaitAsync`会`Completed`在收到信号后进入状态，重新进入监视器。如果在等待满足之前发出信号，则相同的任务将进入`Canceled`状态；`CancellationToken`在这种情况下，任务将等待进入`Canceled`状态，直到它重新进入监视器。请记住，从`WaitAsync`被调用到其返回任务完成的时间，调用任务已经离开了监视器。

![](https://img1.dotnet9.com/2022/07/1001.png)

- Github地址：[https://github.com/StephenCleary/AsyncEx](https://github.com/StephenCleary/AsyncEx)

最后大家如果喜欢我的文章，还麻烦给个关注并点个赞, 希望net生态圈越来越好！