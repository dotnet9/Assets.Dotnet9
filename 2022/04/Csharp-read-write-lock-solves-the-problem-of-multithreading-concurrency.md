---
title: C#使用读写锁三行代码简单解决多线程并发写入文件时线程同步的问题
slug: Csharp-read-write-lock-solves-the-problem-of-multithreading-concurrency
description: 读写锁是以 ReaderWriterLockSlim 对象作为锁管理资源的，不同的 ReaderWriterLockSlim 对象中锁定同一个文件也会被视为不同的锁进行管理
date: 2022-04-25 20:41:27
copyright: Reprinted
author: Walter_lee2008
originalTitle: C#使用读写锁三行代码简单解决多线程并发写入文件时线程同步的问题
originalLink: https://blog.csdn.net/zdhlwt2008/article/details/80702605
draft: False
cover: https://img1.dotnet9.com/2022/04/cover_35.jpeg
categories: 
    - .NET
tags: 
    - C#
---

## 1. 知识储备

在开发程序的过程中，难免少不了写入错误日志这个关键功能。实现这个功能，可以选择使用第三方日志插件，也可以选择使用数据库，还可以自己写个简单的方法把错误信息记录到日志文件。

选择最后一种方法实现的时候，若对文件操作与线程同步不熟悉，问题就有可能出现了，因为同一个文件并不允许多个线程同时写入，否则会提示`“文件正在由另一进程使用，因此该进程无法访问此文件”`。

这是文件的并发写入问题，就需要用到线程同步。而微软也给线程同步提供了一些相关的类可以达到这样的目的，本文使用到的 `System.Threading.ReaderWriterLockSlim` 便是其中之一。

该类用于管理资源访问的锁定状态，可实现多线程读取或进行独占式写入访问。利用这个类，我们就可以避免在同一时间段内多线程同时写入一个文件而导致的并发写入问题。

读写锁是以 `ReaderWriterLockSlim` 对象作为锁管理资源的，`不同的 ReaderWriterLockSlim 对象中锁定同一个文件也会被视为不同的锁进行管理，这种差异可能会再次导致文件的并发写入问题，所以 ReaderWriterLockSlim 应尽量定义为只读的静态对象`。

ReaderWriterLockSlim 有几个关键的方法，本文仅讨论写入锁：

- 调用 `EnterWriteLock` 方法 进入写入状态，在调用线程进入锁定状态之前一直处于阻塞状态，因此可能永远都不返回。

- 调用 `TryEnterWriteLock` 方法 进入写入状态，可指定阻塞的间隔时间，如果调用线程在此间隔期间并未进入写入模式，将返回 false。

- 调用 `ExitWriteLock` 方法 退出写入状态，`应使用 finally 块执行 ExitWriteLock 方法，从而确保调用方退出写入模式`。

## 2. 多线程同时写入文件

```csharp
class Program
{
    static int LogCount = 100;
    static int WritedCount = 0;
    static int FailedCount = 0;

    static void Main(string[] args)
    {
        //迭代运行写入日志记录，由于多个线程同时写入同一个文件将会导致错误
        Parallel.For(0, LogCount, e =>
        {
            WriteLog();
        });

        Console.WriteLine(string.Format("\r\nLog Count:{0}.\t\tWrited Count:{1}.\tFailed Count:{2}.", LogCount.ToString(), WritedCount.ToString(), FailedCount.ToString()));
        Console.Read();
    }

    static void WriteLog()
    {
        try
        {
            var logFilePath = "log.txt";
            var now = DateTime.Now;
            var logContent = string.Format("Tid: {0}{1} {2}.{3}\r\n", Thread.CurrentThread.ManagedThreadId.ToString().PadRight(4), now.ToLongDateString(), now.ToLongTimeString(), now.Millisecond.ToString());
            File.AppendAllText(logFilePath, logContent);
            WritedCount++;
        }
        catch (Exception ex)
        {
            FailedCount++;
            Console.WriteLine(ex.Message);
        }
    }
}
```

![](https://img1.dotnet9.com/2022/04/3501.png)

## 3. 多线程使用读写锁同步写入文件

```csharp
class Program
{
    static int LogCount = 100;
    static int WritedCount = 0;
    static int FailedCount = 0;

    static void Main(string[] args)
    {
        //迭代运行写入日志记录
        Parallel.For(0, LogCount, e =>
        {
            WriteLog();
        });

        Console.WriteLine(string.Format("\r\nLog Count:{0}.\t\tWrited Count:{1}.\tFailed Count:{2}.", LogCount.ToString(), WritedCount.ToString(), FailedCount.ToString()));
        Console.Read();
    }

    //读写锁，当资源处于写入模式时，其他线程写入需要等待本次写入结束之后才能继续写入
    static ReaderWriterLockSlim LogWriteLock = new ReaderWriterLockSlim();
    static void WriteLog()
    {
        try
        {
            //设置读写锁为写入模式独占资源，其他写入请求需要等待本次写入结束之后才能继续写入
            //注意：长时间持有读线程锁或写线程锁会使其他线程发生饥饿 (starve)。 为了得到最好的性能，需要考虑重新构造应用程序以将写访问的持续时间减少到最小。
            //      从性能方面考虑，请求进入写入模式应该紧跟文件操作之前，在此处进入写入模式仅是为了降低代码复杂度
            //      因进入与退出写入模式应在同一个try finally语句块内，所以在请求进入写入模式之前不能触发异常，否则释放次数大于请求次数将会触发异常
            LogWriteLock.EnterWriteLock();

            var logFilePath = "log.txt";
            var now = DateTime.Now;
            var logContent = string.Format("Tid: {0}{1} {2}.{3}\r\n", Thread.CurrentThread.ManagedThreadId.ToString().PadRight(4), now.ToLongDateString(), now.ToLongTimeString(), now.Millisecond.ToString());

            File.AppendAllText(logFilePath, logContent);
            WritedCount++;
        }
        catch (Exception)
        {
            FailedCount++;
        }
        finally
        {
            //退出写入模式，释放资源占用
            //注意：一次请求对应一次释放
            //      若释放次数大于请求次数将会触发异常[写入锁定未经保持即被释放]
            //      若请求处理完成后未释放将会触发异常[此模式下允许以递归方式获取写入锁定]
            LogWriteLock.ExitWriteLock();
        }
    }
}
```

![](https://img1.dotnet9.com/2022/04/3502.png)

使用读写锁，全部日志成功写入了日志文件。

## 4. 测试复杂多线程环境下使用读写锁同步写入文件

```csharp
class Program
{
    static int LogCount = 1000;
    static int SumLogCount = 0;
    static int WritedCount = 0;
    static int FailedCount = 0;

    static void Main(string[] args)
    {
        //往线程池里添加一个任务，迭代写入N个日志
        SumLogCount += LogCount;
        ThreadPool.QueueUserWorkItem((obj) =>
        {
            Parallel.For(0, LogCount, e =>
            {
                WriteLog();
            });
        });

        //在新的线程里，添加N个写入日志的任务到线程池
        SumLogCount += LogCount;
        var thread1 = new Thread(() =>
        {
            Parallel.For(0, LogCount, e =>
            {
                ThreadPool.QueueUserWorkItem((subObj) =>
                {
                    WriteLog();
                });
            });
        });
        thread1.IsBackground = false;
        thread1.Start();

        //添加N个写入日志的任务到线程池
        SumLogCount += LogCount;
        Parallel.For(0, LogCount, e =>
        {
            ThreadPool.QueueUserWorkItem((obj) =>
            {
                WriteLog();
            });
        });

        //在新的线程里，迭代写入N个日志
        SumLogCount += LogCount;
        var thread2 = new Thread(() =>
        {
            Parallel.For(0, LogCount, e =>
            {
                WriteLog();
            });
        });
        thread2.IsBackground = false;
        thread2.Start();

        //在当前线程里，迭代写入N个日志
        SumLogCount += LogCount;
        Parallel.For(0, LogCount, e =>
        {
            WriteLog();
        });

        Console.WriteLine("Main Thread Processed.\r\n");
        while (true)
        {
            Console.WriteLine(string.Format("Sum Log Count:{0}.\t\tWrited Count:{1}.\tFailed Count:{2}.", SumLogCount.ToString(), WritedCount.ToString(), FailedCount.ToString()));
            Console.ReadLine();
        }
    }

    //读写锁，当资源处于写入模式时，其他线程写入需要等待本次写入结束之后才能继续写入
    static ReaderWriterLockSlim LogWriteLock = new ReaderWriterLockSlim();
    static void WriteLog()
    {
        try
        {
            //设置读写锁为写入模式独占资源，其他写入请求需要等待本次写入结束之后才能继续写入
            //注意：长时间持有读线程锁或写线程锁会使其他线程发生饥饿 (starve)。 为了得到最好的性能，需要考虑重新构造应用程序以将写访问的持续时间减少到最小。
            //      从性能方面考虑，请求进入写入模式应该紧跟文件操作之前，在此处进入写入模式仅是为了降低代码复杂度
            //      因进入与退出写入模式应在同一个try finally语句块内，所以在请求进入写入模式之前不能触发异常，否则释放次数大于请求次数将会触发异常
            LogWriteLock.EnterWriteLock();

            var logFilePath = "log.txt";
            var now = DateTime.Now;
            var logContent = string.Format("Tid: {0}{1} {2}.{3}\r\n", Thread.CurrentThread.ManagedThreadId.ToString().PadRight(4), now.ToLongDateString(), now.ToLongTimeString(), now.Millisecond.ToString());

            File.AppendAllText(logFilePath, logContent);
            WritedCount++;
        }
        catch (Exception)
        {
            FailedCount++;
        }
        finally
        {
            //退出写入模式，释放资源占用
            //注意：一次请求对应一次释放
            //      若释放次数大于请求次数将会触发异常[写入锁定未经保持即被释放]
            //      若请求处理完成后未释放将会触发异常[此模式不下允许以递归方式获取写入锁定]
            LogWriteLock.ExitWriteLock();
        }
    }
}
```

![](https://img1.dotnet9.com/2022/04/3503.png)

复杂多线程环境下使用读写锁，`全部日志成功写入`了日志文件，由`ThreadId`和`DateTime`可以看出是由不同的线程同步写入。

## 5. 后记

虽然读写 IO 有共享模式，也可以实现但不推荐

```csharp
static void WriteLog()
{
    try
    {
        var logFilePath = "log.txt";
        var now = DateTime.Now;
        var logContent = string.Format("Tid: {0}{1} {2}.{3}\r\n", Thread.CurrentThread.ManagedThreadId.ToString().PadRight(4), now.ToLongDateString(), now.ToLongTimeString(), now.Millisecond.ToString());
        //File.AppendAllText(logFilePath, logContent);

        var logContentBytes = Encoding.Default.GetBytes(logContent);
        using (FileStream logFile = new FileStream(logFilePath, FileMode.OpenOrCreate, FileAccess.Write, FileShare.Write))
        {
            logFile.Seek(0, SeekOrigin.End);
            logFile.Write(logContentBytes, 0, logContentBytes.Length);
        }

        WritedCount++;
    }
    catch (Exception ex)
    {
        FailedCount++;
        Console.WriteLine(ex.Message);
    }
}
```
