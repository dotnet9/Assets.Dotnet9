---
title: (5)从护士到C#开发者-C#基础进阶:异常处理与程序控制
slug: from-nurse-to-csharp-developer-my-learning-journey-5
description: 在C#编程学习的第五天，我学习了异常处理、变量作用域、switch-case语句和循环结构等内容。作为一名护士转行开发者，我尝试将这些编程概念与护理工作经验相结合。
date: 2025-03-01 09:47:25
lastmod: 2025-03-06 20:43:17
copyright: Contributes
author: 勇敢的天使
draft: False
cover: https://img1.dotnet9.com/2025/02/cover_02.png
categories:
  - 分享
  - 编程学习
albums:
  - 从护士到C#开发者
tags:
  - 护士
  - 编程
  - C#
  - 异常处理
  - 循环结构
---

在上一节课中，我学习了类型转换、算术运算符、关系运算符、逻辑运算符以及各种分支结构，了解了这些语法的含义和执行过程。今天的课程聚焦于如何让我们编写的程序更具健壮性，减少出错的可能性。我学习了另一种分支结构 `switch - case`，并将其与上节课所学的分支结构进行了对比。此外，还学习了循环结构，这是目前所学内容中较为重要且复杂的一部分。为了更好地吸收和理解这些知识，我放慢了学习脚步，通过不断编写代码来巩固和加深理解。下面是我这节课所学的具体内容：

## 一、异常捕获

在护理工作中，我们经常需要处理各种意外情况。比如给病人测量体温时温度计可能故障，输液时可能会出现堵管等情况。同样，在编程中，我们也需要处理各种异常情况。C#提供了异常处理机制来帮助我们优雅地处理这些问题。

### 1. try-catch 基本语法

```csharp
try
{
    // 可能出现异常的代码
}
catch (Exception ex)
{
    // 处理异常的代码
}
```

### 2. 实际应用示例

以下是一个在护理工作中录入病人生命体征数据的示例：

```csharp
try
{
    Console.Write("请输入病人体温: ");
    double temperature = Convert.ToDouble(Console.ReadLine());

    if (temperature < 35 || temperature > 42)
    {
        throw new Exception("体温数据异常，请重新检查");
    }

    Console.Write("请输入收缩压: ");
    int systolicPressure = Convert.ToInt32(Console.ReadLine());

    if (systolicPressure < 60 || systolicPressure > 200)
    {
        throw new Exception("血压数据异常，请重新测量");
    }

    Console.WriteLine($"记录的体温为: {temperature}°C");
    Console.WriteLine($"记录的收缩压为: {systolicPressure}mmHg");
}
catch (FormatException)
{
    Console.WriteLine("输入格式错误，请输入有效的数字");
}
catch (OverflowException)
{
    Console.WriteLine("输入的数值超出范围");
}
catch (Exception ex)
{
    Console.WriteLine($"发生错误: {ex.Message}");
    // 可以在这里记录错误日志
}
finally
{
    Console.WriteLine("数据录入操作完成");
    // 无论是否发生异常，都会执行的清理工作
}
```

### 3. 常见异常类型

在护理信息系统中，我们经常会遇到以下几种异常：

- **FormatException**: 当输入的数据格式不正确时抛出，如输入字母而不是数字
- **OverflowException**: 当数值超出类型范围时抛出
- **ArgumentException**: 当参数值不符合要求时抛出
- **NullReferenceException**: 试图访问空对象时抛出

### 4. 自定义异常

有时我们需要根据业务逻辑抛出自定义异常：

```csharp
public class VitalSignException : Exception
{
    public VitalSignException(string message) : base(message)
    {
    }
}

try
{
    int heartRate = 150;
    if (heartRate > 120)
    {
        throw new VitalSignException("心率异常升高，需要立即处理！");
    }
}
catch (VitalSignException ex)
{
    Console.WriteLine($"生命体征异常: {ex.Message}");
    // 这里可以添加紧急处理流程
}
```

### 5. 最佳实践

1. 只捕获预期的异常，避免捕获所有异常
2. 在适当的层级处理异常
3. 记录异常信息以便后续分析
4. 使用 finally 块进行清理工作
5. 提供有意义的错误信息

## 二、变量的作用域

变量的作用域指的是能够使用该变量的范围。就像在医院里，不同科室的护士只能查看自己科室的病人信息一样，变量也有其使用范围的限制。

### 1. 局部变量

局部变量的作用域从声明它的括号开始，到该括号对应的结束括号结束。这就像护士站内部使用的临时记录单：

```csharp
void RecordPatientVitals()
{
    // temperature 只在这个方法内有效
    double temperature = 36.5;

    if (temperature > 37.3)
    {
        // fever 只在if语句块内有效
        string fever = "发热";
        Console.WriteLine(fever);
    }
    // 这里无法使用 fever 变量

    {
        // pulse 只在这个代码块内有效
        int pulse = 80;
    }
    // 这里无法使用 pulse 变量
}
```

### 2. 类级变量（成员变量）

类级变量的作用域在整个类内部都是可见的，就像病房的护理记录单可以被所有班次的护士访问：

```csharp
public class Patient
{
    // 这些变量在整个类中都可以访问
    private string patientName;
    private int patientAge;
    private string bedNumber;

    public void AdmitPatient(string name, int age)
    {
        patientName = name;  // 可以访问类级变量
        patientAge = age;    // 可以访问类级变量
    }

    public void AssignBed(string bed)
    {
        bedNumber = bed;     // 可以访问类级变量
    }
}
```

### 3. 全局变量（静态变量）

静态变量可以被整个程序访问，类似于医院的通用规章制度：

```csharp
public class HospitalConstants
{
    // 这些静态变量可以在任何地方访问
    public static readonly double NORMAL_TEMPERATURE = 37.0;
    public static readonly int NORMAL_SYSTOLIC_PRESSURE = 120;
    public static readonly int NORMAL_DIASTOLIC_PRESSURE = 80;
}

public class NursingRecord
{
    public void CheckTemperature(double temperature)
    {
        // 可以在任何类中访问 HospitalConstants 的静态变量
        if (temperature > HospitalConstants.NORMAL_TEMPERATURE)
        {
            Console.WriteLine("体温偏高");
        }
    }
}
```

### 4. 变量遮蔽

当局部变量和类级变量同名时，局部变量会"遮蔽"类级变量：

```csharp
public class VitalSigns
{
    private double temperature = 36.5; // 类级变量

    public void UpdateTemperature(double temperature) // 参数
    {
        // 这里的 temperature 指的是参数，而不是类级变量
        Console.WriteLine($"新的体温: {temperature}");

        // 使用 this 关键字访问类级变量
        this.temperature = temperature;
    }
}
```

### 5. 作用域的最佳实践

1. 变量的作用域应该尽可能小，这样可以减少出错的可能
2. 避免使用全局变量，因为它们可能被任何代码修改
3. 使用有意义的变量名，反映其用途
4. 及时释放不再使用的资源
5. 注意变量的生命周期，避免访问已经超出作用域的变量

## 三、`switch - case` 语句

在护理工作中，我们经常需要根据不同的情况作出不同的处理决定。`switch - case` 语句就像我们在护理工作中使用的标准处理流程，根据不同的情况选择相应的处理方案。

### 1. 支持的类型

`switch` 语句支持以下类型的表达式：

- 整数类型（`int`、`long`、`byte`等）
- 字符类型（`char`）
- 字符串类型（`string`）
- 枚举类型（`enum`）
- 布尔类型（`bool`）- C# 7.0 及以上版本
- 模式匹配（C# 7.0 及以上版本）
  - 类型模式
  - 常量模式
  - var 模式
  - 属性模式（C# 8.0 及以上）

示例：

```csharp
// 枚举类型示例
enum PatientStatus
{
    Normal,
    Fever,
    Pain,
    Critical
}

PatientStatus status = PatientStatus.Fever;
switch (status)
{
    case PatientStatus.Normal:
        Console.WriteLine("继续观察");
        break;
    case PatientStatus.Fever:
        Console.WriteLine("进行降温处理");
        break;
    case PatientStatus.Pain:
        Console.WriteLine("给予止痛治疗");
        break;
    case PatientStatus.Critical:
        Console.WriteLine("立即通知医生");
        break;
}

// 模式匹配示例（C# 7.0+）
object obj = "护理记录";
switch (obj)
{
    case string s:
        Console.WriteLine($"这是一个字符串：{s}");
        break;
    case int n:
        Console.WriteLine($"这是一个整数：{n}");
        break;
    case null:
        Console.WriteLine("对象为空");
        break;
    default:
        Console.WriteLine("未知类型");
        break;
}
```

### 2. 基本语法

```csharp
switch (表达式)
{
    case 常量1:
        语句1;
        break;
    case 常量2:
        语句2;
        break;
    default:
        默认语句;
        break;
}
```

### 3. 实际应用示例

例如，根据病人的疼痛等级采取不同的护理措施：

```csharp
int painLevel = 3; // 疼痛等级（0-10）
switch (painLevel)
{
    case 0:
        Console.WriteLine("无需止痛处理");
        break;
    case 1:
    case 2:
    case 3:
        Console.WriteLine("建议非药物治疗，如按摩、热敷等");
        break;
    case 4:
    case 5:
    case 6:
        Console.WriteLine("考虑口服止痛药");
        break;
    case 7:
    case 8:
    case 9:
    case 10:
        Console.WriteLine("需要立即处理，考虑注射止痛药");
        break;
    default:
        Console.WriteLine("无效的疼痛等级");
        break;
}
```

### 4. 注意事项

1. **break 语句的重要性**：每个 case 分支都必须以 break 结束，否则会继续执行下一个 case
2. **case 的合并**：多个 case 可以共用同一个处理逻辑，如示例中的疼痛等级分组
3. **default 分支**：用于处理所有未明确指定的情况，类似于护理中的应急预案

### 5. 分支结构的对比

#### if、if-else 和 switch 的区别

1. **条件类型**：

   - `if`：可以判断任何返回布尔值的条件
   - `if-else`：同样可以判断任何布尔条件，但提供了替代方案
   - `switch`：只能判断相等性，且要求使用常量表达式

2. **适用场景**：

   - `if`：适合单一条件判断
   - `if-else`：适合两种及以上情况的判断
   - `switch`：适合多个等值条件的判断

3. **性能考虑**：
   - 当分支较少时，三者性能相近
   - 当分支较多时，`switch` 通常比多个 `if-else` 性能更好，因为编译器会优化成跳转表

#### if-else if 和 switch-case 的详细对比

```csharp
// 使用 if-else if
if (patientStatus == "发热")
{
    Console.WriteLine("进行物理降温");
    CheckTemperature();
}
else if (patientStatus == "疼痛")
{
    Console.WriteLine("评估疼痛等级");
    PainAssessment();
}
else if (patientStatus == "出血")
{
    Console.WriteLine("立即止血");
    StopBleeding();
}
else
{
    Console.WriteLine("继续观察");
}

// 使用 switch-case
switch (patientStatus)
{
    case "发热":
        Console.WriteLine("进行物理降温");
        CheckTemperature();
        break;
    case "疼痛":
        Console.WriteLine("评估疼痛等级");
        PainAssessment();
        break;
    case "出血":
        Console.WriteLine("立即止血");
        StopBleeding();
        break;
    default:
        Console.WriteLine("继续观察");
        break;
}
```

主要区别：

1. **语法结构**：

   - `if-else if` 结构更灵活，可以处理复杂的条件判断
   - `switch-case` 结构更规范，代码更整洁

2. **条件限制**：

   - `if-else if` 可以使用任何条件表达式
   - `switch-case` 只能使用相等性比较

3. **执行流程**：

   - `if-else if` 会逐个判断条件
   - `switch-case` 直接跳转到匹配的 case

4. **代码维护**：

   - 当分支较多时，`switch-case` 的可读性和可维护性通常更好
   - `if-else if` 适合处理复杂的逻辑判断

5. **性能考虑**：
   - 对于大量分支的情况，`switch-case` 通常性能更好
   - 对于少量分支，两者性能差异不大

### 6. 使用建议

1. 当需要根据一个变量的不同值执行不同操作时，优先使用 switch-case
2. 当分支较多时，switch-case 的可读性通常优于 if-else
3. 对于复杂的条件判断（如范围判断），使用 if-else 更合适
4. 确保所有可能的情况都有相应的处理逻辑

## 四、循环结构

在护理工作中，我们经常需要重复执行某些操作，比如每小时测量一次生命体征，或者给病房的每个病人查房。在编程中，循环结构就是用来处理这种重复性工作的。

### 1. while 循环

`while` 循环会在条件为真时重复执行代码块。就像我们在护理工作中，需要持续监测病人的体温直到体温恢复正常：

```csharp
double temperature = 39.0;
while (temperature > 37.3)
{
    Console.WriteLine($"当前体温：{temperature}°C，需要继续降温");
    // 模拟降温处理
    temperature -= 0.2;
    Console.WriteLine("进行物理降温...");
    Thread.Sleep(1000); // 模拟等待一段时间
}
Console.WriteLine("体温已恢复正常");
```

### 2. do-while 循环

`do-while` 循环至少会执行一次代码块，然后再判断条件。这类似于我们必须先给病人测量一次体温，然后才能决定是否需要继续监测：

```csharp
int painLevel;
do
{
    Console.WriteLine("请评估疼痛等级（0-10）：");
    painLevel = Convert.ToInt32(Console.ReadLine());

    if (painLevel > 0)
    {
        Console.WriteLine("实施止痛措施...");
        // 进行止痛处理
    }
} while (painLevel > 3); // 当疼痛等级大于3时继续监测
```

### 3. for 循环

`for` 循环通常用于知道具体循环次数的情况。比如查房时需要检查每个病床的病人：

```csharp
int bedCount = 6; // 假设病房有6张床
for (int bedNumber = 1; bedNumber <= bedCount; bedNumber++)
{
    Console.WriteLine($"正在查看{bedNumber}号床的病人");
    // 进行查房操作
    CheckPatientStatus(bedNumber);
}
```

### 4. foreach 循环

`foreach` 循环用于遍历集合中的每个元素。例如，查看所有待处理的护理任务：

```csharp
List<string> nursingTasks = new List<string>
{
    "测量生命体征",
    "更换药液",
    "伤口护理",
    "病历记录"
};

foreach (string task in nursingTasks)
{
    Console.WriteLine($"执行护理任务：{task}");
    // 执行护理任务
    PerformNursingTask(task);
}
```

### 5. 循环控制语句

1. **break 语句**：立即退出循环

```csharp
while (true)
{
    double temperature = MeasureTemperature();
    if (temperature <= 37.3)
    {
        Console.WriteLine("体温正常，停止监测");
        break; // 体温正常时退出循环
    }
    // 继续监测
}
```

2. **continue 语句**：跳过当前循环的剩余部分，继续下一次循环

```csharp
for (int bedNumber = 1; bedNumber <= 6; bedNumber++)
{
    if (IsBedEmpty(bedNumber))
    {
        continue; // 如果床位空着，跳过当前循环
    }
    // 对有病人的床位进行护理操作
    ProvideNursing(bedNumber);
}
```

### 6. 循环的最佳实践

1. **选择合适的循环类型**：

   - 不确定循环次数时使用 `while`
   - 至少需要执行一次时使用 `do-while`
   - 知道具体循环次数时使用 `for`
   - 遍历集合时使用 `foreach`

2. **避免无限循环**：

   - 确保循环条件最终会变为 false
   - 在适当的时候使用 break 语句退出循环

3. **性能考虑**：

   - 避免在循环中进行不必要的计算
   - 尽可能减少循环嵌套的层数
   - 合理使用 continue 语句跳过不需要的操作

4. **代码可读性**：

   - 使用有意义的循环变量名
   - 适当添加注释说明循环的目的
   - 保持循环体的简洁

5. **异常处理**：
   - 在循环中加入适当的异常处理
   - 考虑循环过程中可能出现的错误情况

### 7. 循环的应用场景

1. **数据处理**：

```csharp
List<Patient> patients = GetAllPatients();
foreach (Patient patient in patients)
{
    UpdatePatientRecord(patient);
}
```

2. **输入验证**：

```csharp
string input;
do
{
    Console.Write("请输入有效的体温数值（35-42）：");
    input = Console.ReadLine();
} while (!IsValidTemperature(input));
```

3. **定时任务**：

```csharp
while (isNightShift)
{
    // 每两小时查房一次
    CheckPatients();
    Thread.Sleep(TimeSpan.FromHours(2));
}
```

通过这些循环结构，我们可以更高效地处理重复性的护理工作，提高工作效率和准确性。在编程中，合理使用循环结构可以让我们的代码更简洁、更易维护。

## 五、程序调试

在护理工作中，我们经常需要核对医嘱执行情况，检查护理记录是否准确。同样，在编程中，我们也需要检查程序是否按照预期执行。程序调试就是这样一个核查和纠错的过程。

### 1. 调试方法

1. **F11 逐语句调试（单步调试）**

   - 逐行执行代码，详细查看每一步的执行情况
   - 就像我们一步步核对医嘱执行过程
   - 适合定位具体问题所在的代码行

2. **F10 逐过程调试**

   - 以过程为单位执行代码，跳过函数内部的详细执行过程
   - 类似于查房时关注重点项目，暂时略过次要细节
   - 适合快速了解程序整体执行流程

3. **断点调试**
   - 在关键代码行设置断点，程序运行到断点处会暂停
   - 就像在交接班前重点检查某些特殊病人的情况
   - 方便查看特定位置的变量值和程序状态

### 2. 调试示例

```csharp
public class PatientMonitor
{
    public void MonitorVitalSigns(Patient patient)
    {
        // 设置断点，检查病人基本信息
        var temperature = MeasureTemperature(patient);

        if (temperature > 37.3)
        {
            // 使用F11可以进入函数内部查看测量过程
            // 使用F10可以跳过处理函数的内部细节
            HandleFever(patient, temperature);
        }

        // 继续监测其他生命体征
        CheckBloodPressure(patient);
        CheckHeartRate(patient);
    }
}
```

### 3. 调试技巧

1. **合理设置断点**

   - 在可能出问题的代码处设置断点
   - 在关键业务逻辑的起始点设置断点
   - 在异常处理代码处设置断点

2. **使用监视窗口**

   - 添加关键变量到监视窗口
   - 实时观察变量值的变化
   - 验证数据处理的正确性

3. **条件断点**
   - 设置只在特定条件下触发的断点
   - 例如，只在体温超过 39 度时暂停程序

## 总结

在这一课中，我们学习了以下重要内容：

1. **异常处理**

   - try-catch 结构的使用
   - 不同类型异常的处理方法
   - 自定义异常的创建和使用

2. **变量作用域**

   - 局部变量、类级变量和静态变量的区别
   - 变量遮蔽现象
   - 作用域的最佳实践

3. **分支结构**

   - switch-case 语句的基本用法
   - 支持的数据类型和模式匹配
   - 与 if-else 结构的对比和选择

4. **循环结构**

   - while、do-while、for、foreach 的使用场景
   - 循环控制语句（break、continue）
   - 循环的最佳实践和性能考虑

5. **程序调试**
   - 单步调试和逐过程调试
   - 断点的设置和使用
   - 调试工具的有效利用

作为一名从护士转行到程序开发的学习者，我发现编程概念与护理工作有很多相似之处。就像护理工作需要严谨的操作规程、清晰的记录和及时的异常处理一样，编程也需要规范的代码结构、清晰的逻辑和完善的错误处理。

通过这些基础知识的学习，我逐渐建立起了编程思维，能够更好地理解和解决编程问题。在接下来的学习中，我将继续深入探索 C#编程的更多特性，为成为一名优秀的开发者打下坚实的基础。
