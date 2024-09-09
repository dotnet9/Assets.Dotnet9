---
title: C#使用Task执行并行任务的原理和详细举例
slug: The-principle-and-detailed-examples-of-using-Task-to-execute-parallel-tasks-in-Csharp
description: 在C#中，使用Task可以很方便地执行并行任务。
date: 2023-03-28 20:17:14
copyright: Original
cover: https://img1.dotnet9.com/2023/03/cover_17.png
categories: 
    - .NET
tags: 
    - .NET
---

>本文通过与ChatGPT的连续对话完成，代码都经过验证。

在C#中，使用Task可以很方便地执行并行任务。Task是一个表示异步操作的类，它提供了一种简单、轻量级的方式来创建多线程应用程序。

## 一、Task执行并行任务的原理

使用Task执行并行任务的原理是将任务分成多个小块，每个小块都可以在不同的线程上运行。然后，使用Task.Run方法将这些小块作为不同的任务提交给线程池。线程池会自动管理线程的创建和销毁，并根据系统资源的可用情况来自动调整线程数量，从而实现最大化利用CPU资源的效果。

## 二、5个示例展示

### 示例1

下面是一个简单的示例，展示如何使用Task来执行并行任务：

```csharp
void Task1()
{
    // 创建任务数组
    var tasks = new Task[10];

    for (var i = 0; i < tasks.Length; i++)
    {
        var taskId = i + 1;

        // 使用Task.Run方法提交任务
        tasks[i] = Task.Run(() =>
        {
            Console.WriteLine("任务 {0} 运行在线程 {1} 中", taskId, Task.CurrentId);
            // 执行任务逻辑
        });
    }

    // 等待所有任务完成
    Task.WaitAll(tasks);

    Console.WriteLine("所有任务运行完成。");
    Console.ReadKey();
}
```

在这个示例中，我们创建了一个长度为10的任务数组，然后使用Task.Run方法将每个任务提交给线程池。在任务执行时，使用Task.CurrentId属性获取当前任务的ID，并打印出来以方便观察。最后，我们使用Task.WaitAll方法等待所有任务完成并打印出一条完成信息。

运行的结果：

```shell
任务 3 运行在线程 11 中
任务 4 运行在线程 12 中
任务 8 运行在线程 16 中
任务 1 运行在线程 9 中
任务 2 运行在线程 10 中
任务 5 运行在线程 13 中
任务 6 运行在线程 14 中
任务 7 运行在线程 15 中
任务 9 运行在线程 17 中
任务 10 运行在线程 18 中
所有任务运行完成。
```

值得注意的是，在实际开发中，需要根据具体情况来评估任务的大小和数量，以确保并行任务的效率和可靠性。

### 示例2

另一个使用Task的示例是计算斐波那契数列。我们可以将斐波那契数列的每一项看成一个任务，然后使用Task.WaitAll方法等待所有任务完成。

```csharp
void Task2()
{
    static long Fib(int n)
    {
        if (n is 0 or 1)
        {
            return n;
        }
        else
        {
            return Fib(n - 1) + Fib(n - 2);
        }
    }

    const int n = 10; // 计算斐波那契数列的前n项

    var tasks = new Task<long>[n];

    for (var i = 0; i < n; i++)
    {
        var index = i; // 需要在闭包内使用循环变量时需要赋值给另外一个变量

        if (i < 2)
        {
            tasks[i] = Task.FromResult((long)i);
        }
        else
        {
            tasks[i] = Task.Run(() => Fib(index));
        }
    }

    // 等待所有任务完成
    Task.WaitAll(tasks);

    // 打印结果
    for (var i = 0; i < n; i++)
    {
        Console.Write("{0} ", tasks[i].Result);
    }

    Console.ReadKey();
}
```

在这个示例中，我们使用Task数组来存储所有的任务。如果需要计算的是前两项，则直接使用Task.FromResult创建完成任务，否则使用Task.Run方法创建异步任务并调用Fib方法计算结果。在等待所有任务完成后，我们遍历Task数组，并使用Task.Result属性获取每个任务的结果并打印出来。

运行的结果：

```shell
0 1 1 2 3 5 8 13 21 34
```

需要注意的是，在创建异步任务时，由于循环变量在闭包内的值是不确定的，因此需要将其赋值给另外一个变量，并在闭包内使用该变量。否则，所有任务可能会使用同一个循环变量的值，导致结果错误。

### 示例3

除了使用Task数组存储所有任务，还可以使用Task.Factory.StartNew方法创建并行任务。这个方法与Task.Run方法类似，都可以创建异步任务并提交给线程池。

```csharp
void Task3()
{
    long Factorial(int n)
    {
        if (n == 0) return 1;
        return n * Factorial(n - 1);
    }

    const int n = 5; // 计算阶乘的数

    var task = Task.Factory.StartNew(() => Factorial(n));

    Console.WriteLine("计算阶乘...");

    // 等待任务完成
    task.Wait();

    Console.WriteLine("{0}! = {1}", n, task.Result);
    Console.ReadKey();
}
```

在这个示例中，我们使用Task.Factory.StartNew方法创建一个计算阶乘的异步任务，并等待任务完成后打印结果。

运行结果：

```shell
计算阶乘...
5! = 120
```

需要注意的是，尽管Task.Run和Task.Factory.StartNew方法都可以创建异步任务，但它们的行为略有不同。特别是，Task.Run方法总是使用TaskScheduler.Default作为任务调度器，而Task.Factory.StartNew方法可以指定任务调度器、任务类型和其他选项。因此，在选择使用哪种方法时，需要根据具体情况进行评估。

### 示例4

另一个使用Task的示例是异步读取文件。在这个示例中，我们使用Task.FromResult方法创建一个完成任务，并将文件内容作为结果返回。

```csharp
void Task4()
{
    const string filePath = "test.txt";

    var task = Task.FromResult(File.ReadAllText(filePath)); // 只是方便举例，更好的代码应该是：File.ReadAllTextAsync(filePath); 

    Console.WriteLine("读取文件内容...");

    // 等待任务完成
    task.Wait();

    Console.WriteLine("文件内容: {0}", task.Result);
    Console.ReadKey();
}
```

在这个示例中，我们使用Task.FromResult方法创建一个完成任务，并通过File.ReadAllText方法读取文件内容并将其作为结果返回。在等待任务完成后，我们可以通过调用Task.Result属性来获取任务的结果。

文中记事本请随意创建，下面是测试结果：

```shell
读取文件内容...
文件内容: Dotnet9，专注ASP.NET Core网站开发、MAUI跨平台应用开发、WPF客户端开发，同时以 https://Dotnet9.com 网站分享—些 技术类文章，欢迎交流学习。
```

需要注意的是，在实际开发中，如果需要处理大型文件或需要执行长时间的I/O操作，则应该使用异步代码来避免阻塞UI线程。例如，在读取大型文件时，我们可以使用异步代码来避免阻塞UI线程，从而提高应用程序的性能和响应速度。

### 示例5

最后一个示例是使用Task和async/await实现异步任务。在这个示例中，我们将一个耗时的操作封装为异步方法，并使用async/await关键字来等待该操作完成。

```csharp
async Task Task5()
{
    async Task<string> LongOperationAsync()
    {
        // 模拟耗时操作
        await Task.Delay(TimeSpan.FromSeconds(3));

        return "完成";
    }

    Console.WriteLine($"{DateTime.Now:yyyy-MM-dd HH:mm:ss.fff}开始耗时操作...");

    // 等待异步方法完成
    var result = await LongOperationAsync();

    Console.WriteLine($"{DateTime.Now:yyyy-MM-dd HH:mm:ss.fff}耗时操作完成: {result}");
    Console.ReadKey();
}
```

在这个示例中，我们使用async/await关键字将LongOperationAsync方法声明为异步方法，并使用await关键字等待Task.Delay操作完成。在主程序中，我们可以使用await关键字等待LongOperationAsync完成并获取其结果。

```shell
2023-03-28 20:54:09.111开始耗时操作...
2023-03-28 20:54:12.143耗时操作完成: 完成
```

需要注意的是，在使用async/await关键字时，应该避免在异步方法内部使用阻塞线程的操作，否则可能会导致UI线程被阻塞。如果必须执行阻塞操作，可以将其放在不同的线程上执行，或者使用异步IO操作来避免阻塞线程。

## 三、使用async/await关键字注意

在使用async/await关键字时，还有一些细节需要注意，再给出两个示例。

### 示例1

示例代码如下所示：

```csharp
async Task Task6()
{
    async Task<string> LongOperationAsync(int id)
    {
        // 模拟耗时操作
        await Task.Delay(TimeSpan.FromSeconds(1 + id));

        return $"{DateTime.Now:ss.fff}完成 {id}";
    }

    Console.WriteLine($"{DateTime.Now:yyyy-MM-dd HH:mm:ss.fff}开始耗时操作...");

    // 等待多个异步任务完成
    var task1 = LongOperationAsync(1);
    var task2 = LongOperationAsync(2);
    var task3 = LongOperationAsync(3);

    var results = await Task.WhenAll(task1, task2, task3);
    var resultStr = string.Join(",", results);

    Console.WriteLine($"{DateTime.Now:yyyy-MM-dd HH:mm:ss.fff}耗时操作完成: {resultStr}");
    Console.ReadKey();
}
```

在这个示例中，我们使用Task.WhenAll方法等待多个异步任务完成，并使用Join方法将所有任务的结果连接起来作为最终结果。

```shell
2023-03-28 21:15:42.855开始耗时操作...
2023-03-28 21:15:46.894耗时操作完成: 44.888完成 1,45.883完成 2,46.893完成 3
```

### 示例2

另一个需要注意的问题是，在使用`async/await`关键字时，应该尽可能避免使用`ConfigureAwait(false)`方法。这个方法可以让异步操作不必恢复到原始的`SynchronizationContext`上，从而减少线程切换的开销和提高性能。

然而，在某些情况下，如果在异步操作完成后需要返回到原始的`SynchronizationContext`上，使用`ConfigureAwait(false)`会导致调用者无法正确处理结果。因此，建议仅在确定不需要返回到原始的`SynchronizationContext`上时才使用`ConfigureAwait(false)`方法。

示例代码: 假设我们有一个控制台应用程序，其中有两个异步方法：MethodAAsync()和MethodBAsync()。MethodAAsync()会等待1秒钟，然后返回一个字符串。MethodBAsync()会等待2秒钟，然后返回一个字符串。代码如下所示：

```csharp
async Task<string> MethodAAsync()
{
    await Task.Delay(1000);
    return $"{DateTime.Now:ss.fff}>Hello";
}

async Task<string> MethodBAsync()
{
    await Task.Delay(2000);
    return $"{DateTime.Now:ss.fff}>World";
}
```

现在，我们想要同时调用这两个方法，并将它们的结果合并成一个字符串。我们可以像下面这样编写代码：

```csharp
async Task<string> CombineResultsAAsync()
{
    var resultA = await MethodAAsync();
    var resultB = await MethodBAsync();
    return $"{DateTime.Now:yyyy-MM-dd HH:mm:ss.fff}: {resultA} | {resultB}";
}
```

这个代码看起来非常简单明了，但是它存在一个性能问题。当我们调用CombineResultsAAsync()方法时，第一个await操作将使执行上下文切换回原始SynchronizationContext（即主线程），因此我们的异步操作将在UI线程上运行。由于我们要等待1秒钟才能从MethodAAsync()中返回结果，因此UI线程将被阻塞，直到异步操作完成并且结果可用为止。

这种情况下，我们可以使用ConfigureAwait(false)方法来指定不需要保留当前上下文的线程执行状态，从而让异步操作在一个线程池线程上运行。这可以通过下面的代码实现：

```csharp
async Task<string> CombineResultsBAsync()
{
    var resultA = await MethodAAsync().ConfigureAwait(false);
    var resultB = await MethodBAsync().ConfigureAwait(false);
    return $"{DateTime.Now:yyyy-MM-dd HH:mm:ss.fff}: {resultA} | {resultB}";
}
```

通过使用ConfigureAwait(false)方法，我们告诉异步操作不需要保留当前上下文的线程执行状态，这样异步操作就会在一个线程池线程上运行，而不是在UI线程上运行。这样做可以避免一些潜在的性能问题，因为我们的UI线程不会被阻塞，并且异步操作可以在一个新的线程池线程上运行。

## 四、总结

在使用async/await关键字时，应该遵循一些最佳实践，以提高代码的可读性、可维护性和性能。下面是一些常见的最佳实践：

1. 尽可能将异步方法声明为`Task`或`Task<TResult>`类型，以便可以使用await关键字等待其完成。如果异步方法不返回任何内容，则应将其声明为Task类型。

2. 在异步方法内部尽可能避免使用阻塞线程的操作，而应该使用非阻塞操作来模拟延迟。如果必须执行阻塞操作，可以将其放在不同的线程上执行，或者使用异步IO操作来避免阻塞线程。

3. 在异步方法内部不要捕获异常并立即处理，因为这会导致代码变得复杂难以维护。应该让调用者自行处理异常。如果必须在异步方法内部捕获异常，也应该将其包装成`AggregateException`异常，并将其传递给调用者。

4. 在使用`ConfigureAwait(false)`方法时要小心，只有在确定不需要返回到原始的`SynchronizationContext`上时才使用，否则可能会导致调用者无法正确处理结果。

5. 尽量避免在异步方法中使用不安全的线程API，例如`Thread.Sleep`或`Thread.Join`等方法，以确保代码的可移植性和稳定性。应该使用非阻塞的异步方法来模拟延迟。

6. 在使用async/await关键字时，应该遵循一些命名约定，例如异步方法的名称应该以`Async`结尾，以便于区分同步和异步方法。

7. 在需要同时等待多个异步任务完成时，可以使用`Task.WhenAll`方法等待所有任务完成。如果只需要等待其中一个任务完成，则可以使用`Task.WhenAny`方法等待任意一个任务完成。

8. 在异步方法内部，应该将耗时的操作封装为另外的异步方法，并在需要的地方使用`async/await`关键字调用它们，以提高代码的可读性和可维护性。

9. 在使用async/await关键字时，应该尽可能避免使用线程同步机制，例如`lock`关键字或`Monitor`类，因为这会导致UI线程被阻塞。而应该使用异步锁或其他非阻塞的线程同步机制。

总之，使用Task和async/await可以大大简化异步编程，提高代码的可读性、可维护性和性能。但是，需要注意一些细节和最佳实践，以确保代码的正确性和稳定性。