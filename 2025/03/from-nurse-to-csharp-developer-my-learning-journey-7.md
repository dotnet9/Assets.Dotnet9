---
title: (7)从护士到C#开发者--面向对象编程基础
slug: from-nurse-to-csharp-developer-my-learning-journey-7
description: 作为一名从护理行业转行的程序员，我将分享如何通过医护工作经验来理解面向对象编程的概念。本文将介绍类、对象、属性、方法等面向对象的核心概念，并结合医疗保健领域的实例来加深理解。
date: 2025-03-14 09:54:12
lastmod: 2025-03-18 21:15:23
copyright: Contributes
author: 勇敢的天使
draft: false
cover: https://img1.dotnet9.com/2025/02/cover_02.png
categories:
  - 分享
  - 编程学习
  - 面向对象编程
albums:
  - 从护士到C#开发者
tags:
  - 护士
  - 编程
  - C#
  - OOP
  - 面向对象
---

## 引言

作为一名从护理行业转行到编程领域的新手，我发现面向对象编程（OOP）概念实际上与医疗实践有许多相似之处。在这篇文章中，我将分享我如何通过医护工作经验来理解面向对象编程的基本概念，希望能帮助其他正在学习编程的医护人员。

## 面向过程 vs 面向对象

### 面向过程思维

面向过程编程关注的是**完成事情的过程和步骤**，强调的是动作和流程。这就像我们在医院执行的标准操作流程（SOP）。

举个例子，测量病人血糖的面向过程思维：

```csharp
// 面向过程思维：不同护士执行相同的血糖测量流程
void NurseLiMeasureBloodSugar()  // 李护士(经验少)
{
    PrepareEquipment();     // 准备设备
    CallForHelp();          // 寻求帮助
    ExplainToPatient();     // 向患者解释
    DisinfectFinger();      // 消毒手指
    PrickFinger();          // 采血
    ApplyBloodToStrip();    // 血液滴到试纸
    ReadResult();           // 读取结果
    RecordData();           // 记录数据
    DisposeMaterials();     // 处理医疗废物
}

void NurseZhangMeasureBloodSugar()  // 张护士(经验丰富)
{
    PrepareEquipment();     // 准备设备
    ExplainToPatient();     // 向患者解释
    DisinfectFinger();      // 消毒手指
    PrickFingerExpertly();  // 熟练采血
    ApplyBloodToStrip();    // 血液滴到试纸
    ReadResultAccurately(); // 精确读取结果
    RecordDataInDetail();   // 详细记录数据
    DisposeMaterials();     // 处理医疗废物
}
```

可以看出，面向过程的方法需要我们为不同的执行者编写不同的代码，每位护士都需要自己的一套完整流程，导致代码重复且难以维护。

### 面向对象思维

面向对象编程则是**找个对象帮你做事**。针对同样的血糖测量任务，我们把注意力从"谁怎么做"转移到"用什么设备做"，将操作封装到相应的对象中。

```csharp
/// <summary>
/// 血糖仪类 - 负责测量和记录血糖值的医疗设备
/// </summary>
public class GlucoseMeter
{
    // 属性 - 表示血糖仪的状态
    /// <summary>
    /// 表示血糖仪是否已校准
    /// </summary>
    public bool IsCalibrated { get; private set; }

    /// <summary>
    /// 表示血糖仪当前电量
    /// </summary>
    public double BatteryLevel { get; private set; }

    // 方法 - 表示血糖仪可执行的操作
    /// <summary>
    /// 校准血糖仪，确保测量准确
    /// </summary>
    public void Calibrate()
    {
        IsCalibrated = true;
        Console.WriteLine("血糖仪已校准");
    }

    /// <summary>
    /// 执行血糖测量
    /// </summary>
    /// <returns>测量的血糖值，若设备未校准则返回-1</returns>
    public double MeasureBloodSugar()
    {
        if(!IsCalibrated)
        {
            Console.WriteLine("错误：血糖仪未校准");
            return -1; // 错误值
        }

        Console.WriteLine("测量血糖中...");
        // 执行测量逻辑
        return 5.6; // 返回血糖值(示例)
    }
}

/// <summary>
/// 护士类 - 代表执行医疗操作的护理人员
/// </summary>
public class Nurse
{
    /// <summary>
    /// 护士姓名
    /// </summary>
    public string Name { get; private set; }

    /// <summary>
    /// 工作经验年限
    /// </summary>
    public int ExperienceYears { get; private set; }

    /// <summary>
    /// 创建一个新的护士对象
    /// </summary>
    /// <param name="name">护士姓名</param>
    /// <param name="experienceYears">工作经验年限</param>
    public Nurse(string name, int experienceYears)
    {
        Name = name;
        ExperienceYears = experienceYears;
    }

    /// <summary>
    /// 为患者测量血糖的综合操作
    /// </summary>
    /// <param name="patient">需要测量的患者</param>
    /// <param name="meter">使用的血糖仪</param>
    public void MeasurePatientBloodSugar(Patient patient, GlucoseMeter meter)
    {
        Console.WriteLine($"{Name}护士准备为{patient.Name}测量血糖");

        // 准备设备
        Console.WriteLine("准备设备");

        // 校准设备（由血糖仪完成）
        meter.Calibrate();

        // 向患者解释
        Console.WriteLine($"向{patient.Name}解释测量过程");

        // 消毒和采血（护士的专业操作）
        Console.WriteLine("消毒手指并采血");

        // 测量血糖（由血糖仪完成）
        double bloodSugar = meter.MeasureBloodSugar();

        // 记录结果
        if(bloodSugar > 0)
        {
            patient.RecordBloodSugar(bloodSugar);
            Console.WriteLine($"血糖值: {bloodSugar}mmol/L，已记录到患者档案");
        }

        // 处理医疗废物
        Console.WriteLine("处理医疗废物");
    }
}

/// <summary>
/// 患者类 - 代表医院中的病人
/// </summary>
public class Patient
{
    // 字段 - 私有数据，不直接对外暴露
    /// <summary>
    /// 患者姓名
    /// </summary>
    private string _name;

    /// <summary>
    /// 患者年龄
    /// </summary>
    private int _age;

    /// <summary>
    /// 患者的历史血糖记录
    /// </summary>
    private List<double> _bloodSugarReadings;

    /// <summary>
    /// 创建一个新的患者对象
    /// </summary>
    /// <param name="name">患者姓名</param>
    /// <param name="age">患者年龄</param>
    public Patient(string name, int age)
    {
        _name = name;
        _age = age;
        _bloodSugarReadings = new List<double>(); // 初始化空的血糖记录列表
    }

    // 属性 - 对外提供的访问器
    /// <summary>
    /// 获取患者姓名
    /// </summary>
    public string Name
    {
        get { return _name; }
        private set { _name = value; } // 只允许在类内部修改
    }

    /// <summary>
    /// 获取或设置患者年龄（包含验证）
    /// </summary>
    public int Age
    {
        get { return _age; }
        private set { _age = value > 0 ? value : 0; } // 确保年龄为正数
    }

    // 方法 - 对象可执行的操作
    /// <summary>
    /// 记录新的血糖测量值
    /// </summary>
    /// <param name="value">血糖值</param>
    public void RecordBloodSugar(double value)
    {
        if(value > 0)
        {
            _bloodSugarReadings.Add(value);
        }
    }

    /// <summary>
    /// 获取最近一次血糖记录
    /// </summary>
    /// <returns>最近的血糖值，如无记录则返回-1</returns>
    public double GetLatestBloodSugar()
    {
        if(_bloodSugarReadings.Count > 0)
            return _bloodSugarReadings[_bloodSugarReadings.Count - 1];
        else
            return -1; // 表示没有记录
    }

    /// <summary>
    /// 获取患者的所有血糖记录历史
    /// </summary>
    /// <returns>血糖值列表的副本</returns>
    public List<double> GetBloodSugarHistory()
    {
        return new List<double>(_bloodSugarReadings); // 返回副本以保护原始数据
    }
}

// 使用示例 - 演示对象之间的协作
/// <summary>
/// 血糖测量的综合示例
/// </summary>
void BloodSugarMeasurementExample()
{
    // 创建各种对象
    Patient patient = new Patient("王小明", 65);
    GlucoseMeter meter = new GlucoseMeter();

    // 不同经验的护士使用相同的方法和设备
    Nurse nurseLi = new Nurse("李护士", 1);  // 经验少
    Nurse nurseZhang = new Nurse("张护士", 10);  // 经验丰富

    // 两位护士执行相同的操作，调用相同的方法
    nurseLi.MeasurePatientBloodSugar(patient, meter);
    nurseZhang.MeasurePatientBloodSugar(patient, meter);

    // 检查患者的血糖历史
    List<double> history = patient.GetBloodSugarHistory();
    Console.WriteLine($"患者{patient.Name}的血糖记录次数: {history.Count}");
}
```

通过面向对象的方法，我们:

1. 创建了表示不同角色和设备的对象(患者、护士、血糖仪)
2. 每个对象负责自己的功能(血糖仪负责测量，护士负责操作流程)
3. 无论是哪位护士，都使用相同的方法，减少了代码重复
4. 将责任分配给了合适的对象，使代码更加模块化和易于维护

这就像现实中的医疗工作一样：不同的护士使用相同的医疗设备和标准流程为患者服务，而不是每位护士都发明自己的流程。

## 理解类与对象

在医院工作时，我们有"护理记录单"这个模板，根据它我们可以为每个病人创建具体的记录。在编程中：

- **类**：就像护理记录单模板，定义了对象应有的特征(属性)和功能(方法)
- **对象**：就像根据模板填写的具体病人记录

### 类的概念

类就像是一个图纸或者模子，就像我之前做护士时使用的护理评估表格。这个表格定义了需要记录的内容（属性）和操作流程（方法）。

在 C#中，类的基本语法是：

```csharp
[public] class 类名
{
    // 字段
    private string _name;

    // 属性
    public string Name
    {
        get { return _name; }
        set { _name = value; }
    }

    // 方法
    public void DoSomething()
    {
        // 方法实现
    }
}
```

### 类和对象的关系

如果把类比作病历模板，那么对象就是根据这个模板创建的具体病人记录。类本身不占用内存（除静态成员外），而对象是要占用内存的。

医护实例：

```csharp
// Patient类（病人类）
public class Patient
{
    // 字段
    private string _name;
    private int _age;
    private string _diagnosis;

    // 属性
    public string Name
    {
        get { return _name; }
        set { _name = value; }
    }

    public int Age
    {
        get { return _age; }
        set { _age = value > 0 ? value : 0; }  // 验证年龄必须为正数
    }

    // 方法
    public void TakeMedicine()
    {
        Console.WriteLine($"{Name}正在服药...");
    }
}

// 创建Patient对象
Patient patient1 = new Patient();
patient1.Name = "张三";
patient1.Age = 45;
patient1.TakeMedicine();  // 输出：张三正在服药...
```

## 理解属性和字段

### 字段与属性的区别

在护理工作中，我们有很多数据需要记录，但有些数据需要经过验证或特殊处理。

- **字段**：相当于病历上的原始数据，通常应该被保护，不允许外部直接修改
- **属性**：相当于对这些数据的管控机制，可以添加验证逻辑

```csharp
public class Patient
{
    // 字段（私有）
    private double _temperature;

    // 属性（公开）
    public double Temperature
    {
        get { return _temperature; }
        set
        {
            // 添加验证逻辑
            if(value >= 35 && value <= 42)
                _temperature = value;
            else
                throw new ArgumentException("体温数值不合理!");
        }
    }
}
```

属性的本质是两个方法：`get()`和`set()`。属性可以是：

- 可读可写属性：有 get 和 set
- 只读属性：只有 get
- 只写属性：只有 set

## 访问修饰符

在医院信息系统中，不同级别的人员能够访问的信息是不同的。这与 C#中的访问修饰符概念类似：

- `public`：公开的，就像病人的基本信息，所有医护人员都可以查看
- `private`：私有的，只能在当前类内部访问，就像某些敏感检查结果，只有主治医生可以查看
- `protected`：受保护的，可以被派生类访问，就像某些治疗方案，只有医疗团队成员可以查看

## 对象的初始化

当新病人入院时，我们需要填写一系列表格来记录其基本信息。在编程中，我们通过**构造函数**来完成对象的初始化工作。

### 构造函数

构造函数是一个特殊的方法，它在对象创建时自动执行，用于初始化对象。

```csharp
public class Patient
{
    // 字段
    private string _name;
    private int _age;

    // 构造函数
    public Patient(string name, int age)
    {
        _name = name;
        _age = age;
        Console.WriteLine($"创建了一个新病人: {name}, {age}岁");
    }

    // 属性
    public string Name
    {
        get { return _name; }
        set { _name = value; }
    }

    public int Age
    {
        get { return _age; }
        set { _age = value; }
    }
}

// 使用构造函数创建对象
Patient newPatient = new Patient("李四", 50);
```

构造函数的特点：

1. 没有返回值，连 void 都不能写
2. 名称必须与类名相同
3. 可以有多个重载版本
4. 类中默认有一个无参构造函数，但如果自定义了构造函数，默认的无参构造函数就会消失

### new 关键字的作用

当我们使用`new`创建对象时，它做了三件事：

1. 在内存中开辟空间
2. 在这个空间中创建对象
3. 调用对象的构造函数进行初始化

就像医院为新病人分配床位、准备病历，然后录入基本信息。

## 静态与非静态

在医院中，有些信息是每个病人都不同的（如姓名、年龄），有些信息则是所有病人共享的（如医院名称、科室医生）。

### 静态成员与实例成员

- **实例成员**：每个对象独有的属性或方法，就像每个病人都有自己的体温、血压等数据
- **静态成员**：属于类本身而非对象的属性或方法，所有对象共享，就像医院的名称、治疗规范等

```csharp
/// <summary>
/// 医院类(静态) - 包含所有科室共享的医院级信息
/// </summary>
public static class Hospital
{
    // 静态属性 - 所有科室共享

    /// <summary>
    /// 医院名称 - 所有科室共享
    /// </summary>
    public static string Name { get; } = "和平医院";

    /// <summary>
    /// 总床位数 - 由所有科室共用
    /// </summary>
    public static int TotalBeds { get; private set; } = 500;

    /// <summary>
    /// 医院地址 - 所有科室共享
    /// </summary>
    public static string Address { get; } = "和平路123号";

    /// <summary>
    /// 急诊电话 - 所有科室共享
    /// </summary>
    public static string EmergencyNumber { get; } = "120";

    // 静态方法 - 医院级别的操作
    /// <summary>
    /// 患者入院流程 - 分配到特定科室
    /// </summary>
    /// <param name="patient">入院患者</param>
    /// <param name="department">目标科室</param>
    public static void AdmitPatient(Patient patient, Department department)
    {
        if(TotalBeds > 0)
        {
            department.AssignBed(patient);
            TotalBeds--;
            Console.WriteLine($"{patient.Name}已入院到{department.Name}科");
        }
        else
        {
            Console.WriteLine("医院床位已满，无法入院");
        }
    }

    /// <summary>
    /// 患者出院流程 - 从特定科室出院
    /// </summary>
    /// <param name="patient">出院患者</param>
    /// <param name="department">来源科室</param>
    public static void DischargePatient(Patient patient, Department department)
    {
        department.ReleaseBed(patient);
        TotalBeds++;
        Console.WriteLine($"{patient.Name}已从{department.Name}科出院");
    }

}

/// <summary>
/// 科室类 - 表示医院中的具体科室，每个科室有自己的特性
/// </summary>
public class Department
{
    // 字段 - 科室特有数据
    /// <summary>
    /// 科室名称
    /// </summary>
    private string _name;

    /// <summary>
    /// 科室总床位数
    /// </summary>
    private int _totalBeds;

    /// <summary>
    /// 科室可用床位数
    /// </summary>
    private int _availableBeds;

    /// <summary>
    /// 科室医护人员ID列表
    /// </summary>
    private List<string> _staffIds;

    /// <summary>
    /// 科室当前病人列表
    /// </summary>
    private List<Patient> _patients;

    /// <summary>
    /// 创建一个新的科室
    /// </summary>
    /// <param name="name">科室名称</param>
    /// <param name="totalBeds">总床位数</param>
    public Department(string name, int totalBeds)
    {
        _name = name;
        _totalBeds = totalBeds;
        _availableBeds = totalBeds;
        _staffIds = new List<string>();
        _patients = new List<Patient>();
    }

    // 属性 - 科室对外暴露的信息
    /// <summary>
    /// 获取科室名称
    /// </summary>
    public string Name { get { return _name; } }

    /// <summary>
    /// 获取科室可用床位数
    /// </summary>
    public int AvailableBeds { get { return _availableBeds; } }

    // 方法 - 科室可执行的操作
    
    /// <summary>
    /// 为患者分配床位
    /// </summary>
    /// <param name="patient">需要床位的患者</param>
    public void AssignBed(Patient patient)
    {
        if(_availableBeds > 0)
        {
            _patients.Add(patient);
            _availableBeds--;
            Console.WriteLine($"{patient.Name}已分配到{_name}科床位");
        }
        else
        {
            Console.WriteLine($"{_name}科床位已满");
        }
    }

    /// <summary>
    /// 患者出院释放床位
    /// </summary>
    /// <param name="patient">出院患者</param>
    public void ReleaseBed(Patient patient)
    {
        if(_patients.Contains(patient))
        {
            _patients.Remove(patient);
            _availableBeds++;
            Console.WriteLine($"{patient.Name}已释放{_name}科床位");
        }
    }

    /// <summary>
    /// 添加医护人员到本科室
    /// </summary>
    /// <param name="staffId">医护人员ID</param>
    public void AddStaff(string staffId)
    {
        _staffIds.Add(staffId);
    }

    /// <summary>
    /// 显示科室当前状态信息
    /// </summary>
    public void DisplayDepartmentStatus()
    {
        // 这里同时访问静态成员(医院名)和实例成员(科室信息)
        Console.WriteLine($"{Hospital.Name} - {_name}科");
        Console.WriteLine($"总床位：{_totalBeds}，可用床位：{_availableBeds}");
        Console.WriteLine($"当前患者数：{_patients.Count}");
    }

}
```

## this 关键字

`this`关键字表示当前类的实例，在医护工作中，可以理解为"当前正在处理的这个病人"。

this 的用途：

1. 区分局部变量和成员变量
2. 在构造函数中调用其他构造函数

```csharp
public class Patient
{
    private string name;
    private int age;

    // 使用this区分同名变量
    public Patient(string name, int age)
    {
        this.name = name;  // this.name是字段，name是参数
        this.age = age;
    }

    // 使用this调用其他构造函数
    public Patient(string name) : this(name, 0)
    {
        // 这个构造函数会先调用上面的构造函数
    }
}
```

## 用药记录示例

下面是一个用药记录类的完整示例，演示了面向对象编程在医疗场景中的应用：

```csharp
/// <summary>
/// 用药记录类 - 记录患者用药的详细信息
/// </summary>
public class MedicationRecord
{
    // 字段 - 记录的内部数据

    /// <summary>
    /// 药品名称
    /// </summary>
    private string _medicationName;

    /// <summary>
    /// 药品剂量(数值部分)
    /// </summary>
    private double _dosage;
    
    /// <summary>
    /// 剂量单位(如mg, ml等)
    /// </summary>
    private string _unit;
    
    /// <summary>
    /// 给药时间
    /// </summary>
    private DateTime _administrationTime;
    
    /// <summary>
    /// 执行给药的护士ID
    /// </summary>
    private string _nurseId;
    
    /// <summary>
    /// 接受给药的患者ID
    /// </summary>
    private string _patientId;
    
    /// <summary>
    /// 给药途径(口服、静脉注射等)
    /// </summary>
    private string _route;
    
    /// <summary>
    /// 创建新的用药记录
    /// </summary>
    /// <param name="patientId">患者ID</param>
    /// <param name="medicationName">药品名称</param>
    /// <param name="dosage">药品剂量</param>
    /// <param name="unit">剂量单位</param>
    /// <param name="route">给药途径</param>
    /// <param name="nurseId">执行护士ID</param>
    public MedicationRecord(string patientId, string medicationName,
                           double dosage, string unit, string route, string nurseId)
    {
        _patientId = patientId;
        _medicationName = medicationName;
        _dosage = dosage;
        _unit = unit;
        _route = route;
        _nurseId = nurseId;
        _administrationTime = DateTime.Now; // 记录当前时间为给药时间
    }
    
    // 属性 - 对外部提供的数据访问接口
    
    /// <summary>
    /// 获取药品名称(只读)
    /// </summary>
    public string MedicationName { get { return _medicationName; } }
    
    /// <summary>
    /// 获取剂量(只读)
    /// </summary>
    public double Dosage { get { return _dosage; } }
    
    /// <summary>
    /// 获取剂量单位(只读)
    /// </summary>
    public string Unit { get { return _unit; } }
    
    /// <summary>
    /// 获取给药时间(只读)
    /// </summary>
    public DateTime AdministrationTime { get { return _administrationTime; } }
    
    // 方法 - 记录相关的操作
    
    /// <summary>
    /// 获取完整的剂量信息
    /// </summary>
    /// <returns>格式化的剂量和给药途径信息</returns>
    public string GetFullDosageInfo()
    {
        return $"{_dosage} {_unit} {_medicationName} 通过{_route}给药";
    }
    
    /// <summary>
    /// 获取完整的给药记录
    /// </summary>
    /// <returns>格式化的完整给药记录</returns>
    public string GetAdministrationRecord()
    {
        return $"患者ID: {_patientId}, 药品: {_medicationName}, " +
               $"剂量: {_dosage}{_unit}, 途径: {_route}, " +
               $"时间: {_administrationTime.ToString("yyyy-MM-dd HH:mm:ss")}, " +
               $"执行护士: {_nurseId}";
    }
    
    /// <summary>
    /// 检查是否在指定时间阈值内给药
    /// </summary>
    /// <param name="hoursThreshold">小时阈值</param>
    /// <returns>如果在阈值内返回true，否则返回false</returns>
    public bool IsAdministeredRecently(int hoursThreshold)
    {
        return (DateTime.Now - _administrationTime).TotalHours < hoursThreshold;
    }

}

// 使用示例
public class MedicationExample
{
    public static void Main()
    {
        // 创建用药记录
        MedicationRecord record = new MedicationRecord(
        "P001", "阿司匹林", 100, "mg", "口服", "N007");

        // 获取完整记录
        string fullRecord = record.GetAdministrationRecord();
        Console.WriteLine(fullRecord);
    
        // 检查是否最近给药(6小时内)
        bool isRecent = record.IsAdministeredRecently(6);
        Console.WriteLine($"是否6小时内给药: {isRecent}");
    }

}

```

## 总结

作为一名从护士转行到编程的新手，我发现面向对象编程的概念与医疗实践有许多相似之处：

- **类**就像是病历表格模板
- **对象**就像是具体病人的病历记录
- **属性**就像是病人的各项指标，有些需要数据验证
- **方法**就像是对病人进行的各种医疗操作
- **构造函数**就像是病人入院登记
- **静态成员**就像是医院的共享资源和规范

通过这些类比，我更容易理解和记忆面向对象编程的概念。在下一篇文章中，我将继续深入探讨面向对象编程的进阶概念，如继承、多态和接口。

期待与大家分享更多从医护到编程的学习心得！
