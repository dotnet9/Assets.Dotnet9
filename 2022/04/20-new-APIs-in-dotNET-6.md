---
title: 20 个 .NET 6 新增的 API
slug: 20-new-APIs-in-dotNET-6
description: 20 个
date: 2022-04-24 20:15:29
copyright: Reprinted
author: Oleg Kyrylchuk 全球技术精选
originaltitle: 20 个 .NET 6 新增的 API
originallink: https://mp.weixin.qq.com/s/LBNPyVAACUEc786QF_GMqg
draft: False
cover: https://img1.dotnet9.com/2022/04/cover_32.png
categories: .NET
tags: .NET
---

![](https://img1.dotnet9.com/2022/04/cover_32.png)

## 1. DateOnly & TimeOnly

.NET 6 引入了两种期待已久的类型 - DateOnly 和 TimeOnly, 它们分别代表DateTime的日期和时间部分。

```csharp
DateOnly dateOnly = new(2021, 9, 25);
Console.WriteLine(dateOnly);
TimeOnly timeOnly = new(19, 0, 0);
Console.WriteLine(timeOnly); 
DateOnly dateOnlyFromDate = DateOnly.FromDateTime(DateTime.Now);
Console.WriteLine(dateOnlyFromDate); 
TimeOnly timeOnlyFromDate = TimeOnly.FromDateTime(DateTime.Now);
Console.WriteLine(timeOnlyFromDate); 
```

## 2. Parallel.ForEachAsync

它可以控制多个异步任务的并行度。

```csharp
var userHandlers = new[]
{
    "users/okyrylchuk",
    "users/jaredpar",
    "users/davidfowl"
};
using HttpClient client = new()
{
    BaseAddress = new Uri("https://api.github.com"),
};
client.DefaultRequestHeaders.UserAgent.Add(new ProductInfoHeaderValue("DotNet", "6"));
ParallelOptions options = new()
{
    MaxDegreeOfParallelism = 3
};
await Parallel.ForEachAsync(userHandlers, options, async (uri, token) =>
{
    var user = await client.GetFromJsonAsync<GitHubUser>(uri, token);
    Console.WriteLine($"Name: {user.Name}\nBio: {user.Bio}\n");
});
public class GitHubUser
{
    public string Name { get; set; }
    public string Bio { get; set; }
}
// Output:
// Name: David Fowler
// Bio: Partner Software Architect at Microsoft on the ASP.NET team, Creator of SignalR
// 
// Name: Oleg Kyrylchuk
// Bio: Software developer | Dotnet | C# | Azure
// 
// Name: Jared Parsons
// Bio: Developer on the C# compiler
```

## 3. ArgumentNullException.ThrowIfNull()

ArgumentNullException 的小改进, 在抛出异常之前不需要在每个方法中检查 null, 现在只需要写一行, 和 `response.EnsureSuccessStatusCode();` 类似。

```csharp
ExampleMethod(null);
void ExampleMethod(object param)
{
    ArgumentNullException.ThrowIfNull(param);
    // Do something
}
```

## 4. PriorityQueue

.NET 6 新增的数据结构, `PriorityQueue`, 队列每个元素都有一个关联的优先级，它决定了出队顺序, 编号小的元素优先出列。

```csharp
PriorityQueue<string, int> priorityQueue = new();
priorityQueue.Enqueue("Second", 2);
priorityQueue.Enqueue("Fourth", 4);
priorityQueue.Enqueue("Third 1", 3);
priorityQueue.Enqueue("Third 2", 3);
priorityQueue.Enqueue("First", 1);
while (priorityQueue.Count > 0)
{
    string item = priorityQueue.Dequeue();
    Console.WriteLine(item);
}
// Output:
// First
// Second
// Third 2
// Third 1
// Fourth
```

## 5. RandomAccess

提供基于偏移量的 API，用于以线程安全的方式读取和写入文件。

```csharp
using SafeFileHandle handle = File.OpenHandle("file.txt", access: FileAccess.ReadWrite);
// Write to file
byte[] strBytes = Encoding.UTF8.GetBytes("Hello world");
ReadOnlyMemory<byte> buffer1 = new(strBytes);
await RandomAccess.WriteAsync(handle, buffer1, 0);
// Get file length
long length = RandomAccess.GetLength(handle);
// Read from file
Memory<byte> buffer2 = new(new byte[length]);
await RandomAccess.ReadAsync(handle, buffer2, 0);
string content = Encoding.UTF8.GetString(buffer2.ToArray());
Console.WriteLine(content); // Hello world
```

## 6. PeriodicTimer

认识一个完全异步的`“PeriodicTimer”`, 更适合在异步场景中使用, 它有一个方法 `WaitForNextTickAsync`。

```csharp
// One constructor: public PeriodicTimer(TimeSpan period)
using PeriodicTimer timer = new(TimeSpan.FromSeconds(1));
while (await timer.WaitForNextTickAsync())
{
    Console.WriteLine(DateTime.UtcNow);
}
// Output:
// 13 - Oct - 21 19:58:05 PM
// 13 - Oct - 21 19:58:06 PM
// 13 - Oct - 21 19:58:07 PM
// 13 - Oct - 21 19:58:08 PM
// 13 - Oct - 21 19:58:09 PM
// 13 - Oct - 21 19:58:10 PM
// 13 - Oct - 21 19:58:11 PM
// 13 - Oct - 21 19:58:12 PM
// ...
```

## 7. Metrics API

.NET 6 实现了 `OpenTelemetry Metrics API` 规范, 内置了指标API, 通过 `Meter` 类创建下面的指标

- Counter
- Histogram
- ObservableCounter
- ObservableGauge

使用的方法如下：

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();
// Create Meter
var meter = new Meter("MetricsApp", "v1.0");
// Create counter
Counter<int> counter = meter.CreateCounter<int>("Requests");
app.Use((context, next) =>
{
    // Record the value of measurement
    counter.Add(1);
    return next(context);
});
app.MapGet("/", () => "Hello World");
StartMeterListener();
app.Run();
// Create and start Meter Listener
void StartMeterListener()
{
    var listener = new MeterListener();
    listener.InstrumentPublished = (instrument, meterListener) =>
    {
        if (instrument.Name == "Requests" && instrument.Meter.Name == "MetricsApp")
        {
            // Start listening to a specific measurement recording
            meterListener.EnableMeasurementEvents(instrument, null);
        }
    };
    listener.SetMeasurementEventCallback<int>((instrument, measurement, tags, state) =>
    {
        Console.WriteLine($"Instrument {instrument.Name} has recorded the measurement: {measurement}");
    });
    listener.Start();
}
```

## 8. 检查元素是否可为空的反射API

它提供来自反射成员的可空性信息和上下文：

- ParameterInfo 参数
- FieldInfo 字段
- PropertyInfo 属性
- EventInfo 事件

```csharp
var example = new Example();
var nullabilityInfoContext = new NullabilityInfoContext();
foreach (var propertyInfo in example.GetType().GetProperties())
{
    var nullabilityInfo = nullabilityInfoContext.Create(propertyInfo);
    Console.WriteLine($"{propertyInfo.Name} property is {nullabilityInfo.WriteState}");
}
// Output:
// Name property is Nullable
// Value property is NotNull
class Example
{
    public string? Name { get; set; }
    public string Value { get; set; }
}
```

## 9. 检查嵌套元素是否可为空的反射API

它允许您获取嵌套元素的可为空的信息, 您可以指定数组属性必须为非空，但元素可以为空，反之亦然。

```csharp
Type exampleType = typeof(Example);
PropertyInfo notNullableArrayPI = exampleType.GetProperty(nameof(Example.NotNullableArray));
PropertyInfo nullableArrayPI = exampleType.GetProperty(nameof(Example.NullableArray));
NullabilityInfoContext nullabilityInfoContext = new();
NullabilityInfo notNullableArrayNI = nullabilityInfoContext.Create(notNullableArrayPI);
Console.WriteLine(notNullableArrayNI.ReadState);              // NotNull
Console.WriteLine(notNullableArrayNI.ElementType.ReadState);  // Nullable
NullabilityInfo nullableArrayNI = nullabilityInfoContext.Create(nullableArrayPI);
Console.WriteLine(nullableArrayNI.ReadState);                // Nullable
Console.WriteLine(nullableArrayNI.ElementType.ReadState);    // Nullable
class Example
{
    public string?[] NotNullableArray { get; set; }
    public string?[]? NullableArray { get; set; }
}
```

## 10. ProcessId & ProcessPath

直接通过 Environment 获取进程ID和路径。

```csharp
int processId = Environment.ProcessId
string path = Environment.ProcessPath;
Console.WriteLine(processId);
Console.WriteLine(path);
```

## 11. Configuration 新增 GetRequiredSection()

和 DI 的 `GetRequiredService()` 是一样的, 如果缺失, 则会抛出异常。

```csharp
WebApplicationBuilder builder = WebApplication.CreateBuilder(args);
WebApplication app = builder.Build();
MySettings mySettings = new();
// Throws InvalidOperationException if a required section of configuration is missing
app.Configuration.GetRequiredSection("MySettings").Bind(mySettings);
app.Run();
class MySettings
{
    public string? SettingValue { get; set; }
}
```

## 12. CSPNG 密码安全伪随机数生成器

您可以从密码安全伪随机数生成器 (`CSPNG`) 轻松生成随机值序列。

它对于以下场景中很有用：

- 密钥生成
- 随机数
- 某些签名方案中的盐

```csharp
// Fills an array of 300 bytes with a cryptographically strong random sequence of values.
// GetBytes(byte[] data);
// GetBytes(byte[] data, int offset, int count)
// GetBytes(int count)
// GetBytes(Span<byte> data)
byte[] bytes = RandomNumberGenerator.GetBytes(300);
```

## 13. Native Memory API

.NET 6 引入了一个新的 API 来分配本机内存, `NativeMemory` 有`分配和释放内存`的方法。

```csharp
unsafe
{
    byte* buffer = (byte*)NativeMemory.Alloc(100);
    NativeMemory.Free(buffer);
    /* This class contains methods that are mainly used to manage native memory.
    public static class NativeMemory
    {
        public unsafe static void* AlignedAlloc(nuint byteCount, nuint alignment);
        public unsafe static void AlignedFree(void* ptr);
        public unsafe static void* AlignedRealloc(void* ptr, nuint byteCount, nuint alignment);
        public unsafe static void* Alloc(nuint byteCount);
        public unsafe static void* Alloc(nuint elementCount, nuint elementSize);
        public unsafe static void* AllocZeroed(nuint byteCount);
        public unsafe static void* AllocZeroed(nuint elementCount, nuint elementSize);
        public unsafe static void Free(void* ptr);
        public unsafe static void* Realloc(void* ptr, nuint byteCount);
    }*/
}
```

## 14. Power of 2

.NET 6 引入了用于处理 2 的幂的新方法。

- 'IsPow2' 判断指定值是否为 2 的幂。
- 'RoundUpToPowerOf2' 将指定值四舍五入到 2 的幂。

```csharp
// IsPow2 evaluates whether the specified Int32 value is a power of two.
Console.WriteLine(BitOperations.IsPow2(128));            // True
// RoundUpToPowerOf2 rounds the specified T:System.UInt32 value up to a power of two.
Console.WriteLine(BitOperations.RoundUpToPowerOf2(200)); // 256
```

## 15. WaitAsync on Task

您可以更轻松地等待异步任务执行, 如果超时会抛出 “TimeoutException”

```csharp
Task operationTask = DoSomethingLongAsync();
await operationTask.WaitAsync(TimeSpan.FromSeconds(5));
async Task DoSomethingLongAsync()
{
    Console.WriteLine("DoSomethingLongAsync started.");
    await Task.Delay(TimeSpan.FromSeconds(10));
    Console.WriteLine("DoSomethingLongAsync ended.");
}
// Output:
// DoSomethingLongAsync started.
// Unhandled exception.System.TimeoutException: The operation has timed out.
```

## 16. 新的数学API

新方法:

- SinCos
- ReciprocalEstimate
- ReciprocalSqrtEstimate

新的重载：

- Min, Max, Abs, Sign, Clamp 支持 nint 和 nuint
- DivRem 返回一个元组, 包括商和余数。

```csharp
// New methods SinCos, ReciprocalEstimate and ReciprocalSqrtEstimate
// Simultaneously computes Sin and Cos
(double sin, double cos) = Math.SinCos(1.57);
Console.WriteLine($"Sin = {sin}\nCos = {cos}");
// Computes an approximate of 1 / x
double recEst = Math.ReciprocalEstimate(5);
Console.WriteLine($"Reciprocal estimate = {recEst}");
// Computes an approximate of 1 / Sqrt(x)
double recSqrtEst = Math.ReciprocalSqrtEstimate(5);
Console.WriteLine($"Reciprocal sqrt estimate = {recSqrtEst}");
// New overloads
// Min, Max, Abs, Clamp and Sign supports nint and nuint
(nint a, nint b) = (5, 10);
nint min = Math.Min(a, b);
nint max = Math.Max(a, b);
nint abs = Math.Abs(a);
nint clamp = Math.Clamp(abs, min, max);
nint sign = Math.Sign(a);
Console.WriteLine($"Min = {min}\nMax = {max}\nAbs = {abs}");
Console.WriteLine($"Clamp = {clamp}\nSign = {sign}");
// DivRem variants return a tuple
(int quotient, int remainder) = Math.DivRem(2, 7);
Console.WriteLine($"Quotient = {quotient}\nRemainder = {remainder}");
// Output:
// Sin = 0.9999996829318346
// Cos = 0.0007963267107331026
// Reciprocal estimate = 0.2
// Reciprocal sqrt estimate = 0.4472135954999579
// Min = 5
// Max = 10
// Abs = 5
// Clamp = 5
// Sign = 1
// Quotient = 0
// Remainder = 2
```

## 17. CollectionsMarshal.GetValueRefOrNullRef

这个是在字典中循环或者修改结可变结构体时用, 可以减少结构的副本复制, 也可以避免字典重复进行哈希计算，这个有点晦涩难懂，有兴趣的可以看看这个

- [https://github.com/dotnet/runtime/issues/27062](https://github.com/dotnet/runtime/issues/27062)

```csharp
Dictionary<int, MyStruct> dictionary = new()
{
    { 1, new MyStruct { Count = 100 } }
};
int key = 1;
ref MyStruct value = ref CollectionsMarshal.GetValueRefOrNullRef(dictionary, key);
// Returns Unsafe.NullRef<TValue>() if it doesn't exist; check using Unsafe.IsNullRef(ref value)
if (!Unsafe.IsNullRef(ref value))
{
    Console.WriteLine(value.Count); // Output: 100
    // Mutate in-place
    value.Count++;
    Console.WriteLine(value.Count); // Output: 101
}
struct MyStruct
{
    public int Count { get; set; }
}
```

## 18. ConfigureHostOptions

`IHostBuilder` 上的新 `ConfigureHostOptions API`, 可以更简单的配置应用。

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CreateHostBuilder(args).Build().Run();
    }
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureHostOptions(o =>
            {
                o.ShutdownTimeout = TimeSpan.FromMinutes(10);
            });
}
```

## 19. Async Scope

.NET 6 引入了一种新的`CreateAsyncScope`方法, 当您处理 `IAsyncDisposable` 的服务时现有的`CreateScope`方法会`引发异常`, 使用 `CreateAsyncScope` 可以`完美解决`。

```csharp
await using var provider = new ServiceCollection()
        .AddScoped<Example>()
        .BuildServiceProvider();
await using (var scope = provider.CreateAsyncScope())
{
    var example = scope.ServiceProvider.GetRequiredService<Example>();
}
class Example : IAsyncDisposable
{
    public ValueTask DisposeAsync() => default;
}
```

## 20. 加密类简化

- DecryptCbc
- DecryptCfb
- DecryptEcb
- EncryptCbc
- EncryptCfb
- EncryptEcb

```csharp
static byte[] Decrypt(byte[] key, byte[] iv, byte[] ciphertext)
{
    using (Aes aes = Aes.Create())
    {
        aes.Key = key;
        return aes.DecryptCbc(ciphertext, iv, PaddingMode.PKCS7);
    }
}
```

全文完...

>作者: [Oleg Kyrylchuk](https://hashnode.com/@okyrylchuk)
>
>原文: [https://blog.okyrylchuk.dev/](https://blog.okyrylchuk.dev/)