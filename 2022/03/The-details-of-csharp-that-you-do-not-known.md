---
title: 你所不知道的 C# 中的细节
slug: The-details-of-csharp-that-you-do-not-known
description: 有一个东西叫做鸭子类型，所谓鸭子类型就是，只要一个东西表现得像鸭子那么就能推出这玩意就是鸭子。
date: 2022-03-29 08:23:33
copyright: Reprinted
author: hez2010
originalTitle: 你所不知道的 C# 中的细节
originalLink: https://www.cnblogs.com/hez2010/p/12606419.html
draft: False
cover: https://img1.dotnet9.com/2022/03/cover_26.png
categories: .NET
tags: C#
---

## 前言

有一个东西叫做鸭子类型，所谓鸭子类型就是，只要一个东西表现得像鸭子那么就能推出这玩意就是鸭子。

C# 里面其实也暗藏了很多类似鸭子类型的东西，但是很多开发者并不知道，因此也就没法好好利用这些东西，那么今天我细数一下这些藏在编译器中的细节。

## 不是只有 Task 和 ValueTask 才能 await

在 C# 中编写异步代码的时候，我们经常会选择将异步代码包含在一个 Task 或者 ValueTask 中，这样调用者就能用 await 的方式实现异步调用。

西卡西，并不是只有 Task 和 ValueTask 才能 await。Task 和 ValueTask 背后明明是由线程池参与调度的，可是为什么 C# 的 async/await 却被说成是 coroutine 呢？

因为你所 await 的东西不一定是 Task/ValueTask，在 C# 中只要你的类中包含 GetAwaiter() 方法和 bool IsCompleted 属性，并且 GetAwaiter() 返回的东西包含一个 GetResult() 方法、一个 bool IsCompleted 属性和实现了 INotifyCompletion，那么这个类的对象就是可以 await 的 。

因此在封装 I/O 操作的时候，我们可以自行实现一个 Awaiter，它基于底层的 epoll/IOCP 实现，这样当 await 的时候就不会创建出任何的线程，也不会出现任何的线程调度，而是直接让出控制权。而 OS 在完成 I/O 调用后通过 CompletionPort (Windows) 等通知用户态完成异步调用，此时恢复上下文继续执行剩余逻辑，这其实就是一个真正的 stackless coroutine。

```csharp
public class MyTask<T>
{
    public MyAwaiter<T> GetAwaiter()
    {
        return new MyAwaiter<T>();
    }
}

public class MyAwaiter<T> : INotifyCompletion
{
    public bool IsCompleted { get; private set; }
    public T GetResult()
    {
        throw new NotImplementedException();
    }
    public void OnCompleted(Action continuation)
    {
        throw new NotImplementedException();
    }
}

public class Program
{
    static async Task Main(string[] args)
    {
        var obj = new MyTask<int>();
        await obj;
    }
}
```

事实上，.NET Core 中的 I/O 相关的异步 API 也的确是这么做的，I/O 操作过程中是不会有任何线程分配等待结果的，都是 coroutine 操作：I/O 操作开始后直接让出控制权，直到 I/O 操作完毕。而之所以有的时候你发现 await 前后线程变了，那只是因为 Task 本身被调度了。

UWP 开发中所用的 IAsyncAction/IAsyncOperation<T> 则是来自底层的封装，和 Task 没有任何关系但是是可以 await 的，并且如果用 C++/WinRT 开发 UWP 的话，返回这些接口的方法也都是可以 co_await 的。

## 不是只有 IEnumerable 和 IEnumerator 才能被 foreach

经常我们会写如下的代码：

```csharp
foreach (var i in list)
{
    // ......
}
```

然后一问为什么可以 foreach，大多都会回复因为这个 list 实现了 IEnumerable 或者 IEnumerator。

但是实际上，如果想要一个对象可被 foreach，只需要提供一个 GetEnumerator() 方法，并且 GetEnumerator() 返回的对象包含一个 bool MoveNext() 方法加一个 Current 属性即可。

```csharp
class MyEnumerator<T>
{
    public T Current { get; private set; }
    public bool MoveNext()
    {
        throw new NotImplementedException();
    }
}

class MyEnumerable<T>
{
    public MyEnumerator<T> GetEnumerator()
    {
        throw new NotImplementedException();
    }
}

class Program
{
    public static void Main()
    {
        var x = new MyEnumerable<int>();
        foreach (var i in x)
        {
            // ......
        }
    }
}
```

## 不是只有 IAsyncEnumerable 和 IAsyncEnumerator 才能被 await foreach

同上，但是这一次要求变了，GetEnumerator() 和 MoveNext() 变为 GetAsyncEnumerator() 和 MoveNextAsync()。

其中 MoveNextAsync() 返回的东西应该是一个 Awaitable<bool>，至于这个 Awaitable 到底是什么，它可以是 Task/ValueTask，也可以是其他的或者你自己实现的。

```csharp
class MyAsyncEnumerator<T>
{
    public T Current { get; private set; }
    public MyTask<bool> MoveNextAsync()
    {
        throw new NotImplementedException();
    }
}

class MyAsyncEnumerable<T>
{
    public MyAsyncEnumerator<T> GetAsyncEnumerator()
    {
        throw new NotImplementedException();
    }
}

class Program
{
    public static async Task Main()
    {
        var x = new MyAsyncEnumerable<int>();
        await foreach (var i in x)
        {
            // ......
        }
    }
}
```

## ref struct 要怎么实现 IDisposable

众所周知 ref struct 因为必须在栈上且不能被装箱，所以不能实现接口，但是如果你的 ref struct 中有一个 void Dispose() 那么就可以用 using 语法实现对象的自动销毁。

```csharp
ref struct MyDisposable
{
    public void Dispose() => throw new NotImplementedException();
}

class Program
{
    public static void Main()
    {
        using var y = new MyDisposable();
        // ......
    }
}
```

## 不是只有 Range 才能使用切片

C# 8 引入了 Ranges，允许切片操作，但是其实并不是必须提供一个接收 Range 类型参数的 indexer 才能使用该特性。

只要你的类可以被计数（拥有 Length 或 Count 属性），并且可以被切片（拥有一个 Slice(int, int) 方法），那么就可以用该特性。

```csharp
class MyRange
{
    public int Count { get; private set; }
    public object Slice(int x, int y) => throw new NotImplementedException();
}

class Program
{
    public static void Main()
    {
        var x = new MyRange();
        var y = x[1..];
    }
}
```

## 不是只有 Index 才能使用索引

C# 8 引入了 Indexes 用于索引，例如使用 ^1 索引倒数第一个元素，但是其实并不是必须提供一个接收 Index 类型参数的 indexer 才能使用该特性。

只要你的类可以被计数（拥有 Length 或 Count 属性），并且可以被索引（拥有一个接收 int 参数的索引器），那么就可以用该特性。

```csharp
class MyIndex
{
    public int Count { get; private set; }
    public object this[int index]
    {
        get => throw new NotImplementedException();
    }
}

class Program
{
    public static void Main()
    {
        var x = new MyIndex();
        var y = x[^1];
    }
}
```

## 给类型实现解构

如何给一个类型实现解构呢？其实只需要写一个名字为 Deconstruct() 的方法，并且参数都是 out 的即可。

```csharp
class MyDeconstruct
{
    private int A => 1;
    private int B => 2;
    public void Deconstruct(out int a, out int b)
    {
        a = A;
        b = B;
    }
}

class Program
{
    public static void Main()
    {
        var x = new MyDeconstruct();
        var (o, u) = x;
    }
}
```

## 不是只有实现了 IEnumerable 才能用 LINQ

LINQ 是 C# 中常用的一种集成查询语言，允许你这样写代码：

```csharp
from c in list where c.Id > 5 select c;
```

但是上述代码中的 list 的类型不一定非得实现 IEnumerable，事实上，只要有对应名字的扩展方法就可以了，比如有了叫做 Select 的方法就能用 select，有了叫做 Where 的方法就能用 where。

```C#
class Just<T> : Maybe<T>
{
    private readonly T value;
    public Just(T value) { this.value = value; }

    public override Maybe<U> Select<U>(Func<T, Maybe<U>> f) => f(value);
    public override string ToString() => $"Just {value}";
}

class Nothing<T> : Maybe<T>
{
    public override Maybe<U> Select<U>(Func<T, Maybe<U>> _) => new Nothing<U>();
    public override string ToString() => "Nothing";
}

abstract class Maybe<T>
{
    public abstract Maybe<U> Select<U>(Func<T, Maybe<U>> f);

    public Maybe<V> SelectMany<U, V>(Func<T, Maybe<U>> k, Func<T, U, V> s)
        => Select(x => k(x).Select(y => new Just<V>(s(x, y))));

    public Maybe<U> Where(Func<Maybe<T>, bool> f) => f(this) ? this : new Nothing<T>();
}

class Program
{
    public static void Main()
    {
        var x = new Just<int>(3);
        var y = new Just<int>(7);
        var z = new Nothing<int>();

        var u = from x0 in x from y0 in y select x0 + y0;
        var v = from x0 in x from z0 in z select x0 + z0;
        var just = from c in x where true select c;
        var nothing = from c in x where false select c;
    }
}
```
