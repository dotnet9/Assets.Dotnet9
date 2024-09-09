---
title: 使用C#实现23种常见的设计模式
slug: Using-Csharp-to-Implement-23-Common-Design-Patterns
description: 这些模式是用于解决常见的对象导向设计问题的最佳实践。
date: 2023-06-08 21:51:48
copyright: Reprinted
author: Token
originalTitle: 使用C#实现23种常见的设计模式
originalLink: https://blog.tokengo.top/blog?id=28
draft: false
cover: https://img1.dotnet9.com/2023/06/cover_01.png
categories: 
    - .NET
tags: 
    - .NET
---

设计模式通常分为三个主要类别：

- 创建型模式

- 结构型模式

- 行为型模式。

这些模式是用于解决常见的对象导向设计问题的最佳实践。

以下是 23 种常见的设计模式并且提供`c#代码案例`：

## 创建型模式：

### 1. 单例模式（Singleton）

```csharp
public sealed class Singleton
{
    //创建一个只读的静态Singleton实例
    private static readonly Singleton instance = new Singleton();

    // 记录Singleton的创建次数
    private static int instanceCounter = 0;

    // 单例实例的公共访问点
    public static Singleton Instance
    {
        get
        {
            return instance;
        }
    }

    // 私有构造函数
    private Singleton()
    {
        instanceCounter++;
        Console.WriteLine("Instances Created " + instanceCounter);
    }

    // 在此处添加其他的Singleton类方法
    public void LogMessage(string message)
    {
        Console.WriteLine("Message: " + message);
    }
}

```

在这个例子中，我们有一个名为`Singleton`的类，它有一个私有的构造函数和一个静态的只读属性`Instance`，用于访问`Singleton`类的唯一实例。我们还有一个`LogMessage`方法，用于模拟`Singleton`类的某个行为。

以下是一个使用这个`Singleton`类的控制台应用程序：

```csharp
class Program
{
    static void Main(string[] args)
    {
        Singleton fromEmployee = Singleton.Instance;
        fromEmployee.LogMessage("Message from Employee");

        Singleton fromBoss = Singleton.Instance;
        fromBoss.LogMessage("Message from Boss");
        Console.ReadLine();
    }
}

```

### 2. 工厂方法模式（Factory Method）

工厂方法模式是一种创建型设计模式，它提供了一种创建对象的接口，但允许子类决定实例化哪个类。工厂方法让类的实例化推迟到子类中进行。

下面是一个使用 C#实现的工厂方法模式的简单示例：

```csharp
// 抽象产品
public interface IProduct
{
    string Operation();
}

// 具体产品A
public class ProductA : IProduct
{
    public string Operation()
    {
        return "{Result of ProductA}";
    }
}

// 具体产品B
public class ProductB : IProduct
{
    public string Operation()
    {
        return "{Result of ProductB}";
    }
}

// 抽象创建者
public abstract class Creator
{
    public abstract IProduct FactoryMethod();
}

// 具体创建者A
public class CreatorA : Creator
{
    public override IProduct FactoryMethod()
    {
        return new ProductA();
    }
}

// 具体创建者B
public class CreatorB : Creator
{
    public override IProduct FactoryMethod()
    {
        return new ProductB();
    }
}

```

以上代码中定义了两个产品`ProductA`和`ProductB`，这两个产品都实现了`IProduct`接口。接着我们有两个 Creator 类，`CreatorA`和`CreatorB`，它们都继承自抽象基类`Creator`。`CreatorA`工厂创建`ProductA`，`CreatorB`工厂创建`ProductB`。

以下是一个使用这些工厂和产品的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        // 创建工厂对象
        Creator creatorA = new CreatorA();
        Creator creatorB = new CreatorB();

        // 通过工厂方法创建产品对象
        IProduct productA = creatorA.FactoryMethod();
        IProduct productB = creatorB.FactoryMethod();

        // 打印结果
        Console.WriteLine("ProductA says: " + productA.Operation());
        Console.WriteLine("ProductB says: " + productB.Operation());

        Console.ReadLine();
    }
}

```

当你运行这个程序时，它会显示出`ProductA`和`ProductB`的`Operation`方法返回的结果。这说明我们已经成功地使用工厂方法模式创建了产品实例。每个工厂类决定了它创建哪个产品的实例。这种方式使得客户端代码不需要直接实例化产品类，而只需要依赖工厂接口，增加了程序的灵活性。

### 3. 抽象工厂模式（Abstract Factory）

抽象工厂模式是一种创建型设计模式，它提供了一种接口，用于创建相关或依赖对象的系列，而不指定这些对象的具体类。在这个模式中，客户端通过他们的抽象接口使用类，允许该模式在不影响客户端的情况下替换实现类。

以下是一个简单的抽象工厂模式的 C#实现：

```csharp
// 抽象产品：动物
public interface IAnimal
{
    string Speak();
}

// 具体产品：狗
public class Dog : IAnimal
{
    public string Speak()
    {
        return "Bark Bark";
    }
}

// 具体产品：猫
public class Cat : IAnimal
{
    public string Speak()
    {
        return "Meow Meow";
    }
}

// 抽象工厂
public abstract class IAnimalFactory
{
    public abstract IAnimal CreateAnimal();
}

// 具体工厂：狗工厂
public class DogFactory : IAnimalFactory
{
    public override IAnimal CreateAnimal()
    {
        return new Dog();
    }
}

// 具体工厂：猫工厂
public class CatFactory : IAnimalFactory
{
    public override IAnimal CreateAnimal()
    {
        return new Cat();
    }
}

```

以上代码定义了两种动物`Dog`和`Cat`，它们都实现了`IAnimal`接口。然后我们有两个工厂类，`DogFactory`和`CatFactory`，它们都继承自`IAnimalFactory`。`DogFactory`生产`Dog`，而`CatFactory`生产`Cat`。

以下是一个使用这些工厂和产品的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        // 创建工厂
        IAnimalFactory dogFactory = new DogFactory();
        IAnimalFactory catFactory = new CatFactory();

        // 使用工厂创建产品
        IAnimal dog = dogFactory.CreateAnimal();
        IAnimal cat = catFactory.CreateAnimal();

        // 打印结果
        Console.WriteLine("Dog says: " + dog.Speak());
        Console.WriteLine("Cat says: " + cat.Speak());

        Console.ReadLine();
    }
}

```

当你运行这个程序时，会打印出 Dog 和 Cat 的 Speak 方法的结果，这显示了我们已经成功地使用了抽象工厂模式创建了产品实例。这种方式使得客户端代码不需要直接实例化产品类，而只需要依赖工厂接口，增加了程序的灵活性和扩展性。

### 4. 建造者模式（Builder）

建造者模式是一种创建型设计模式，它提供了一种创建对象的接口，但是允许使用相同的构建过程来创建不同的产品。

以下是在 C#中实现建造者模式的一个简单示例：

```csharp
// 产品
public class Car
{
    public string Engine { get; set; }
    public string Wheels { get; set; }
    public string Doors { get; set; }
}

// 建造者抽象类
public abstract class CarBuilder
{
    protected Car car;

    public void CreateNewCar()
    {
        car = new Car();
    }

    public Car GetCar()
    {
        return car;
    }

    public abstract void SetEngine();
    public abstract void SetWheels();
    public abstract void SetDoors();
}

// 具体建造者
public class FerrariBuilder : CarBuilder
{
    public override void SetEngine()
    {
        car.Engine = "V8";
    }

    public override void SetWheels()
    {
        car.Wheels = "18 inch";
    }

    public override void SetDoors()
    {
        car.Doors = "2";
    }
}

// 指挥者
public class Director
{
    public Car Construct(CarBuilder carBuilder)
    {
        carBuilder.CreateNewCar();
        carBuilder.SetEngine();
        carBuilder.SetWheels();
        carBuilder.SetDoors();
        return carBuilder.GetCar();
    }
}

```

以上代码中，`Car`是我们要创建的产品，`CarBuilder`是抽象的建造者，定义了制造一个产品所需要的各个步骤，`FerrariBuilder`是具体的建造者，实现了`CarBuilder`定义的所有步骤，`Director`是指挥者，它告诉建造者应该按照什么顺序去执行哪些步骤。

以下是一个使用这个建造者模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        Director director = new Director();
        CarBuilder builder = new FerrariBuilder();
        Car ferrari = director.Construct(builder);

        Console.WriteLine($"Engine: {ferrari.Engine}, Wheels: {ferrari.Wheels}, Doors: {ferrari.Doors}");
        Console.ReadLine();
    }
}

```

当你运行这个程序时，会看到我们已经成功地创建了一个`Car`实例，它的各个部分是按照`FerrariBuilder`所定义的方式创建的。这说明我们使用建造者模式成功地将一个复杂对象的构造过程解耦，使得同样的构造过程可以创建不同的表示。

### 5. 原型模式（Prototype）

原型模式是一种创建型设计模式，它实现了一个原型接口，该接口用于创建当前对象的克隆。当直接创建对象的代价比较大时，则采用这种模式。例如，一个对象需要在一个高代价的数据库操作后被创建。

以下是在 C#中实现原型模式的一个简单示例：

```csharp
// 抽象原型
public interface IPrototype
{
    IPrototype Clone();
}

// 具体原型
public class ConcretePrototype : IPrototype
{
    public string Name { get; set; }
    public int Value { get; set; }

    public IPrototype Clone()
    {
        // 实现深拷贝
        return (ConcretePrototype)this.MemberwiseClone(); // Clones the concrete object.
    }
}

```

以上代码定义了一个`ConcretePrototype`类，它实现了`IPrototype`接口。接口定义了一个`Clone`方法，用于复制对象。在`ConcretePrototype`类中，我们使用了`MemberwiseClone`方法来创建一个新的克隆对象。

以下是一个使用原型模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        ConcretePrototype prototype = new ConcretePrototype();
        prototype.Name = "Original";
        prototype.Value = 10;

        Console.WriteLine("Original instance: " + prototype.Name + ", " + prototype.Value);

        ConcretePrototype clone = (ConcretePrototype)prototype.Clone();
        Console.WriteLine("Cloned instance: " + clone.Name + ", " + clone.Value);

        Console.ReadLine();
    }
}

```

在这个例子中，我们创建了一个`ConcretePrototype`对象，并为其属性赋值，然后我们调用`Clone`方法创建了一个新的`ConcretePrototype`对象。当我们运行这个程序时，会看到原始对象和克隆对象的属性是相同的，这表明我们已经成功地克隆了一个对象。

执行流程如下：

1. 创建一个具体的原型对象，为其属性赋值。
2. 调用原型对象的`Clone`方法，创建一个新的对象，该对象的属性与原型对象的属性相同。
3. 打印原型对象和克隆对象的属性，验证它们是否相同。

## 结构型模式：

### 1. 适配器模式（Adapter）

适配器模式(Adapter Pattern)的目标是将一个类的接口转换成客户端期望的另一个接口。适配器使得原本由于接口不兼容而不能在一起工作的那些类可以在一起工作。下面是一个使用 C#实现的适配器模式的例子：

在这个例子中，我将创建一个`ITarget`接口和一个`Adaptee`类。然后我将创建一个适配器`Adapter`，它会实现`ITarget`接口并使用`Adaptee`类的方法来满足`ITarget`的要求。

```csharp
// 目标接口，或者客户端期望的接口。
public interface ITarget
{
    string GetRequest();
}

// 需要适配的类。
public class Adaptee
{
    public string GetSpecificRequest()
    {
        return "Specific request.";
    }
}

// 适配器类，它通过在内部封装一个Adaptee对象来满足ITarget的接口。
public class Adapter : ITarget
{
    private readonly Adaptee _adaptee;

    public Adapter(Adaptee adaptee)
    {
        this._adaptee = adaptee;
    }

    public string GetRequest()
    {
        return $"This is '{this._adaptee.GetSpecificRequest()}'";
    }
}

// 客户端代码，它与所有符合ITarget接口的对象兼容。
public class Client
{
    public void MakeRequest(ITarget target)
    {
        Console.WriteLine(target.GetRequest());
    }
}
```

执行流程如下：

```csharp
class Program
{
    static void Main(string[] args)
    {
        Adaptee adaptee = new Adaptee();
        ITarget target = new Adapter(adaptee);
        Client client = new Client();

        // 由于适配器可以使得Adaptee与Client兼容，所以我们可以调用Client的MakeRequest方法
        client.MakeRequest(target);

        // 等待用户输入，以防止控制台程序立即退出。
        Console.ReadKey();
    }
}
```

当运行上述代码时，将在控制台上打印出以下输出：

```
This is 'Specific request.'
```

此例中，`Adapter`是将`Adaptee`适配到`ITarget`接口的类。当客户端（在这种情况下是`Client`类）调用`Adapter`的`GetRequest`方法时，`Adapter`会将这个请求转发给`Adaptee`的`GetSpecificRequest`方法。

在此实例中，我们通过使用`Adapter`类，让原本接口不兼容的`Client`类和`Adaptee`类能够顺利地一起工作。

### 2. 桥接模式（Bridge）

桥接模式是一种结构型设计模式，用于将抽象部分与其实现部分分离，使它们都可以独立地变化。

以下是在 C#中实现桥接模式的一个简单示例：

```csharp
// 实现类接口
public interface IImplementor
{
    void OperationImp();
}

// 具体实现类A
public class ConcreteImplementorA : IImplementor
{
    public void OperationImp()
    {
        Console.WriteLine("Concrete Implementor A");
    }
}

// 具体实现类B
public class ConcreteImplementorB : IImplementor
{
    public void OperationImp()
    {
        Console.WriteLine("Concrete Implementor B");
    }
}

// 抽象类
public abstract class Abstraction
{
    protected IImplementor implementor;

    public Abstraction(IImplementor implementor)
    {
        this.implementor = implementor;
    }

    public virtual void Operation()
    {
        implementor.OperationImp();
    }
}

// 扩充的抽象类
public class RefinedAbstraction : Abstraction
{
    public RefinedAbstraction(IImplementor implementor) : base(implementor) { }

    public override void Operation()
    {
        Console.WriteLine("Refined Abstraction is calling implementor's method:");
        base.Operation();
    }
}

```

在这个代码中，`Abstraction`是抽象类，它有一个`IImplementor`接口的实例，通过这个实例调用实现类的方法。`RefinedAbstraction`是扩充的抽象类，它继承自`Abstraction`。`ConcreteImplementorA`和`ConcreteImplementorB`是实现类，它们实现了`IImplementor`接口。

以下是一个使用这个模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        IImplementor implementorA = new ConcreteImplementorA();
        Abstraction abstractionA = new RefinedAbstraction(implementorA);
        abstractionA.Operation();

        IImplementor implementorB = new ConcreteImplementorB();
        Abstraction abstractionB = new RefinedAbstraction(implementorB);
        abstractionB.Operation();

        Console.ReadLine();
    }
}

```

在这个例子中，我们创建了两个实现类的实例，然后创建了两个抽象类的实例，每个抽象类的实例都有一个实现类的实例。当我们调用抽象类的`Operation`方法时，它会调用实现类的`OperationImp`方法。

执行流程如下：

1. 创建实现类的实例。
2. 创建抽象类的实例，抽象类的实例有一个实现类的实例。
3. 调用抽象类的`Operation`方法，该方法会调用实现类的`OperationImp`方法。

### 3. 组合模式（Composite）

组合模式（Composite pattern）是一种结构型设计模式，它可以使你将对象组合成树形结构，并且能像使用独立对象一样使用它们。这种模式的主要目的是使单个对象和组合对象具有一致性。

以下是在 C#中实现组合模式的一个简单示例：

```csharp
// 抽象组件类
public abstract class Component
{
    protected string name;

    public Component(string name)
    {
        this.name = name;
    }

    public abstract void Add(Component c);
    public abstract void Remove(Component c);
    public abstract void Display(int depth);
}

// 叶节点类
public class Leaf : Component
{
    public Leaf(string name) : base(name) { }

    public override void Add(Component c)
    {
        Console.WriteLine("Cannot add to a leaf");
    }

    public override void Remove(Component c)
    {
        Console.WriteLine("Cannot remove from a leaf");
    }

    public override void Display(int depth)
    {
        Console.WriteLine(new String('-', depth) + name);
    }
}

// 构件容器类
public class Composite : Component
{
    private List<Component> _children = new List<Component>();

    public Composite(string name) : base(name) { }

    public override void Add(Component component)
    {
        _children.Add(component);
    }

    public override void Remove(Component component)
    {
        _children.Remove(component);
    }

    public override void Display(int depth)
    {
        Console.WriteLine(new String('-', depth) + name);

        // 显示每个节点的子节点
        foreach (Component component in _children)
        {
            component.Display(depth + 2);
        }
    }
}

```

在这个代码中，`Component`是组件抽象类，它有一个名字，并定义了添加、删除和显示操作。`Leaf`是叶子节点，它实现了`Component`的操作。`Composite`是组件容器，它可以添加、删除和显示其子节点。

以下是一个使用这个模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        Composite root = new Composite("root");
        root.Add(new Leaf("Leaf A"));
        root.Add(new Leaf("Leaf B"));

        Composite comp = new Composite("Composite X");
        comp.Add(new Leaf("Leaf XA"));
        comp.Add(new Leaf("Leaf XB"));

        root.Add(comp);

        Composite comp2 = new Composite("Composite XY");
        comp2.Add(new Leaf("Leaf XYA"));
        comp2.Add(new Leaf("Leaf XYB"));

        comp.Add(comp2);

        root.Add(new Leaf("Leaf C"));

        // 在组合中添加和删除
        Leaf leaf = new Leaf("Leaf D");
        root.Add(leaf);
        root.Remove(leaf);

        // 显示树形结构
        root.Display(1);

        Console.ReadLine();
    }
}

```

在这个例子中，我们创建了一个根节点，并在其中添加了两个叶子节点。然后我们创建了一个复合节点，并在其中添加了两个叶子节点，然后我们把复合节点添加到根节点中。我们还在复合节点中添加了另一个复合节点。最后，我们又在根节点中添加和删除了一个叶子节点，然后显示了树的结构。

执行流程如下：

1. 创建组合和叶子对象。
2. 通过调用组合对象的`Add`方法将叶子对象和其他组合对象添加到组合对象中。
3. 通过调用组合对象的`Remove`方法将叶子对象从组合对象中移除。
4. 调用组合对象的`Display`方法显示组合对象的结构。

### 4. 装饰模式（Decorator）

装饰模式是一种结构型设计模式，它允许在运行时动态地将功能添加到对象中，这种模式提供了比继承更有弹性的解决方案。

以下是在 C#中实现装饰模式的一个简单示例：

```csharp
// 抽象组件
public abstract class Component
{
    public abstract string Operation();
}

// 具体组件
public class ConcreteComponent : Component
{
    public override string Operation()
    {
        return "ConcreteComponent";
    }
}

// 抽象装饰器
public abstract class Decorator : Component
{
    protected Component component;

    public Decorator(Component component)
    {
        this.component = component;
    }

    public override string Operation()
    {
        if (component != null)
        {
            return component.Operation();
        }
        else
        {
            return string.Empty;
        }
    }
}

// 具体装饰器A
public class ConcreteDecoratorA : Decorator
{
    public ConcreteDecoratorA(Component comp) : base(comp) { }

    public override string Operation()
    {
        return $"ConcreteDecoratorA({base.Operation()})";
    }
}

// 具体装饰器B
public class ConcreteDecoratorB : Decorator
{
    public ConcreteDecoratorB(Component comp) : base(comp) { }

    public override string Operation()
    {
        return $"ConcreteDecoratorB({base.Operation()})";
    }
}

```

在这个代码中，`Component`是一个抽象组件，它定义了一个`Operation`方法。`ConcreteComponent`是具体组件，它实现了`Component`的`Operation`方法。`Decorator`是一个抽象装饰器，它包含一个`Component`对象，并重写了`Operation`方法。`ConcreteDecoratorA`和`ConcreteDecoratorB`是具体的装饰器，它们继承了`Decorator`并重写了`Operation`方法，以添加新的功能。

以下是一个使用这个模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        // 基本组件
        Component component = new ConcreteComponent();
        Console.WriteLine("Basic Component: " + component.Operation());

        // 装饰后的组件
        Component decoratorA = new ConcreteDecoratorA(component);
        Console.WriteLine("A Decorated: " + decoratorA.Operation());

        Component decoratorB = new ConcreteDecoratorB(decoratorA);
        Console.WriteLine("B Decorated: " + decoratorB.Operation());

        Console.ReadLine();
    }
}

```

在这个例子中，我们首先创建了一个`ConcreteComponent`对象，并调用它的`Operation`方法。然后我们创建了一个`ConcreteDecoratorA`对象，它装饰了`ConcreteComponent`，并调用它的`Operation`方法。最后，我们创建了一个`ConcreteDecoratorB`对象，它装饰了`ConcreteDecoratorA`，并调用它的`Operation`方法。这样，我们就可以在运行时动态地添加功能。

执行流程如下：

1. 创建一个具体组件对象并调用其操作。
2. 创建一个装饰器对象，该对象装饰了具体组件，并调用其操作。在操作中，装饰器首先调用具体组件的操作，然后执行额外的操作。
3. 创建另一个装饰器对象，装饰前一个装饰器，并调用其操作。在操作中，这个装饰器首先调用前一个装饰器的操作，然后执行额外的操作。

### 5. 外观模式（Facade）

外观模式是一种结构型设计模式，提供了一个统一的接口，用来访问子系统中的一群接口。外观模式定义了一个高层接口，让子系统更容易使用。

以下是在 C#中实现外观模式的一个简单示例：

```csharp
// 子系统A
public class SubSystemA
{
    public string OperationA()
    {
        return "SubSystemA, OperationA\n";
    }
}

// 子系统B
public class SubSystemB
{
    public string OperationB()
    {
        return "SubSystemB, OperationB\n";
    }
}

// 子系统C
public class SubSystemC
{
    public string OperationC()
    {
        return "SubSystemC, OperationC\n";
    }
}

// 外观类
public class Facade
{
    private SubSystemA a = new SubSystemA();
    private SubSystemB b = new SubSystemB();
    private SubSystemC c = new SubSystemC();

    public string OperationWrapper()
    {
        string result = "Facade initializes subsystems:\n";
        result += a.OperationA();
        result += b.OperationB();
        result += c.OperationC();
        return result;
    }
}

```

在这个代码中，`SubSystemA`，`SubSystemB`和`SubSystemC`都是子系统，每个子系统都有一个操作。`Facade`是一个外观类，它封装了对子系统的操作，提供了一个统一的接口。

以下是一个使用这个模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        Facade facade = new Facade();
        Console.WriteLine(facade.OperationWrapper());

        Console.ReadLine();
    }
}

```

在这个例子中，我们创建了一个`Facade`对象，并调用了它的`OperationWrapper`方法。这个方法封装了对子系统的操作，使得客户端可以不直接操作子系统，而是通过外观类操作子系统。

执行流程如下：

1. 创建一个外观对象。

2. 通过调用外观对象的方法，间接地操作子系统。

3. 子系统的操作被封装在外观对象的方法中，客户端不需要直接操作子系统。

### 6. 享元模式（Flyweight）

享元模式（Flyweight Pattern）是一种结构型设计模式，该模式主要用于减少创建对象的数量，以减少内存占用和提高性能。这种类型的设计模式属于结构型模式，它提供了一种减少对象数量从而改善应用所需的对象结构的方式。

以下是在 C#中实现享元模式的一个简单示例：

```csharp
// 享元类
public class Flyweight
{
    private string intrinsicState;

    // 构造函数
    public Flyweight(string intrinsicState)
    {
        this.intrinsicState = intrinsicState;
    }

    // 业务方法
    public void Operation(string extrinsicState)
    {
        Console.WriteLine($"Intrinsic State = {intrinsicState}, Extrinsic State = {extrinsicState}");
    }
}

// 享元工厂类
public class FlyweightFactory
{
    private Dictionary<string, Flyweight> flyweights = new Dictionary<string, Flyweight>();

    public Flyweight GetFlyweight(string key)
    {
        if (!flyweights.ContainsKey(key))
        {
            flyweights[key] = new Flyweight(key);
        }

        return flyweights[key];
    }

    public int GetFlyweightCount()
    {
        return flyweights.Count;
    }
}

```

在这个代码中，`Flyweight`是享元类，它有一个内在状态`intrinsicState`，这个状态是不变的。`FlyweightFactory`是享元工厂类，它维护了一个享元对象的集合。

以下是一个使用这个模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        FlyweightFactory factory = new FlyweightFactory();

        Flyweight flyweightA = factory.GetFlyweight("A");
        flyweightA.Operation("A operation");

        Flyweight flyweightB = factory.GetFlyweight("B");
        flyweightB.Operation("B operation");

        Flyweight flyweightC = factory.GetFlyweight("A");
        flyweightC.Operation("C operation");

        Console.WriteLine($"Total Flyweights: {factory.GetFlyweightCount()}");

        Console.ReadLine();
    }
}

```

在这个例子中，我们创建了一个`FlyweightFactory`对象，并通过它创建了两个享元对象。注意，当我们试图创建第三个享元对象时，工厂实际上返回了第一个享元对象的引用，因为这两个对象的内在状态是相同的。

执行流程如下：

1. 创建一个享元工厂对象。
2. 通过享元工厂获取享元对象。如果对象已经存在，则返回现有对象；否则，创建新对象。
3. 执行享元对象的操作。
4. 显示当前享元对象的数量。

### 7. 代理模式（Proxy）

代理模式是一种结构型设计模式，它提供了一个对象代替另一个对象来控制对它的访问。代理对象可以在客户端和目标对象之间起到中介的作用，并添加其他的功能。

以下是在 C#中实现代理模式的一个简单示例：

```csharp
// 抽象主题接口
public interface ISubject
{
    void Request();
}

// 真实主题
public class RealSubject : ISubject
{
    public void Request()
    {
        Console.WriteLine("RealSubject: Handling Request.");
    }
}

// 代理
public class Proxy : ISubject
{
    private RealSubject _realSubject;

    public Proxy(RealSubject realSubject)
    {
        this._realSubject = realSubject;
    }

    public void Request()
    {
        if (this.CheckAccess())
        {
            this._realSubject.Request();
            this.LogAccess();
        }
    }

    public bool CheckAccess()
    {
        // 检查是否有权限访问
        Console.WriteLine("Proxy: Checking access prior to firing a real request.");
        return true;
    }

    public void LogAccess()
    {
        // 记录请求
        Console.WriteLine("Proxy: Logging the time of request.");
    }
}

```

在这个代码中，`ISubject`是一个接口，定义了`Request`方法。`RealSubject`是实现了`ISubject`接口的类，`Proxy`是代理类，它也实现了`ISubject`接口，并持有一个`RealSubject`对象的引用。

以下是一个使用这个模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Client: Executing the client code with a real subject:");
        RealSubject realSubject = new RealSubject();
        realSubject.Request();

        Console.WriteLine();

        Console.WriteLine("Client: Executing the same client code with a proxy:");
        Proxy proxy = new Proxy(realSubject);
        proxy.Request();

        Console.ReadLine();
    }
}

```

在这个例子中，我们首先直接调用了`RealSubject`的`Request`方法，然后我们通过代理调用了相同的方法。注意，在通过代理调用`Request`方法时，代理还执行了其他的操作，如检查访问权限和记录日志。

执行流程如下：

1. 创建一个真实主题对象，并直接调用其`Request`方法。
2. 创建一个代理对象，代理对象包含一个真实主题的引用。
3. 通过代理对象调用`Request`方法。在这个方法中，代理首先检查访问权限，然后调用真实主题的`Request`方法，最后记录日志。

## 行为型模式：

### 1. 责任链模式（Chain of Responsibility）

责任链模式（Chain of Responsibility Pattern）是一种行为设计模式，该模式为请求创建了一个接收对象的链。这种模式给予请求的对象更多的自由度，并允许请求沿着链路传递，直到有一个对象处理它为止。

在这个例子中，我将创建一个`Handler`抽象类和两个具体的处理器`ConcreteHandler1`和`ConcreteHandler2`。每个处理器都检查它是否可以处理请求，如果可以，它就处理该请求。如果不行，它就将请求传递给链中的下一个处理器。

```csharp
public abstract class Handler
{
    protected Handler successor;

    public void SetSuccessor(Handler successor)
    {
        this.successor = successor;
    }

    public abstract void HandleRequest(int request);
}

public class ConcreteHandler1 : Handler
{
    public override void HandleRequest(int request)
    {
        if (request >= 0 && request < 10)
        {
            Console.WriteLine($"{this.GetType().Name} handled request {request}");
        }
        else if (successor != null)
        {
            successor.HandleRequest(request);
        }
    }
}

public class ConcreteHandler2 : Handler
{
    public override void HandleRequest(int request)
    {
        if (request >= 10 && request < 20)
        {
            Console.WriteLine($"{this.GetType().Name} handled request {request}");
        }
        else if (successor != null)
        {
            successor.HandleRequest(request);
        }
    }
}
```

执行流程如下：

```csharp
class Program
{
    static void Main(string[] args)
    {
        // 设置责任链
        Handler h1 = new ConcreteHandler1();
        Handler h2 = new ConcreteHandler2();

        h1.SetSuccessor(h2);

        // 生成并处理请求
        int[] requests = { 2, 5, 14, 22, 18, 3, 27, 20 };

        foreach (int request in requests)
        {
            h1.HandleRequest(request);
        }

        Console.ReadKey();
    }
}
```

当运行上述代码时，将在控制台上打印出以下输出：

```shell
ConcreteHandler1 handled request 2
ConcreteHandler1 handled request 5
ConcreteHandler2 handled request 14
ConcreteHandler2 handled request 18
ConcreteHandler1 handled request 3
```

在这个例子中，`ConcreteHandler1`和`ConcreteHandler2`都是处理器，它们决定是否处理传入的请求。如果`ConcreteHandler1`无法处理请求，它将请求传递给`ConcreteHandler2`。如果`ConcreteHandler2`也无法处理请求，那么请求就会被忽略。

这个例子显示了责任链模式的核心思想，即创建一个对象链来处理一个请求。在这个链中，每个处理器决定是否处理该请求。如果它不能处理该请求，它就将请求传递给链中的下一个处理器。

### 2. 命令模式（Command）

命令模式（Command Pattern）是一种数据驱动的设计模式，它属于行为型模式。在命令模式中，请求在对象中封装成为一个操作或行为，这些请求被送到调用对象，调用对象寻找可以处理该命令的合适的对象，并把命令直接送达到对应的对象，该对象会执行这些命令。

以下是在 C#中实现命令模式的一个简单示例：

```csharp
// 命令接口
public interface ICommand
{
    void Execute();
}

// 具体命令类
public class ConcreteCommand : ICommand
{
    private Receiver receiver;

    public ConcreteCommand(Receiver receiver)
    {
        this.receiver = receiver;
    }

    public void Execute()
    {
        receiver.Action();
    }
}

// 接收者类
public class Receiver
{
    public void Action()
    {
        Console.WriteLine("Receiver performs an action");
    }
}

// 调用者或发送者类
public class Invoker
{
    private ICommand command;

    public void SetCommand(ICommand command)
    {
        this.command = command;
    }

    public void ExecuteCommand()
    {
        command.Execute();
    }
}

```

在这个代码中，`ICommand`是命令接口，定义了`Execute`方法。`ConcreteCommand`是具体的命令类，它实现了`ICommand`接口，并持有一个`Receiver`对象的引用。`Invoker`是调用者或发送者类，它持有一个`ICommand`对象的引用，并可以通过`SetCommand`方法设置命令，通过`ExecuteCommand`方法执行命令。

以下是一个使用这个模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        Receiver receiver = new Receiver();
        ICommand command = new ConcreteCommand(receiver);
        Invoker invoker = new Invoker();

        invoker.SetCommand(command);
        invoker.ExecuteCommand();

        Console.ReadLine();
    }
}

```

在这个例子中，我们创建了一个`Receiver`对象、一个`ConcreteCommand`对象和一个`Invoker`对象。然后我们通过`Invoker`的`SetCommand`方法设置了命令，并通过`ExecuteCommand`方法执行了命令。

执行流程如下：

1. 创建一个接收者对象。
2. 创建一个具体命令对象，并将接收者对象传递给它。
3. 创建一个调用者或发送者对象。
4. 通过调用者对象的`SetCommand`方法设置命令。
5. 通过调用者对象的`ExecuteCommand`方法执行命令。

### 3. 解释器模式（Interpreter）

解释器模式（Interpreter Pattern）是一种行为型设计模式，用于解决一些固定语法格式的需求。它定义了如何在语言中表示和解析语法。

以下是在 C#中实现解释器模式的一个简单示例：

```csharp
// 抽象表达式
public interface IExpression
{
    bool Interpret(string context);
}

// 终结符表达式
public class TerminalExpression : IExpression
{
    private string data;

    public TerminalExpression(string data)
    {
        this.data = data;
    }

    public bool Interpret(string context)
    {
        if (context.Contains(data))
        {
            return true;
        }

        return false;
    }
}

// 非终结符表达式
public class OrExpression : IExpression
{
    private IExpression expr1 = null;
    private IExpression expr2 = null;

    public OrExpression(IExpression expr1, IExpression expr2)
    {
        this.expr1 = expr1;
        this.expr2 = expr2;
    }

    public bool Interpret(string context)
    {
        return expr1.Interpret(context) || expr2.Interpret(context);
    }
}

```

在这个代码中，`IExpression`是抽象表达式，定义了`Interpret`方法。`TerminalExpression`是终结符表达式，它实现了`IExpression`接口。`OrExpression`是非终结符表达式，它也实现了`IExpression`接口。

以下是一个使用这个模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        IExpression isMale = GetMaleExpression();
        IExpression isMarriedWoman = GetMarriedWomanExpression();

        Console.WriteLine($"John is male? {isMale.Interpret("John")}");
        Console.WriteLine($"Julie is a married women? {isMarriedWoman.Interpret("Married Julie")}");

        Console.ReadLine();
    }

    // 规则：Robert 和 John 是男性
    public static IExpression GetMaleExpression()
    {
        IExpression robert = new TerminalExpression("Robert");
        IExpression john = new TerminalExpression("John");
        return new OrExpression(robert, john);
    }

    // 规则：Julie 是一个已婚的女性
    public static IExpression GetMarriedWomanExpression()
    {
        IExpression julie = new TerminalExpression("Julie");
        IExpression married = new TerminalExpression("Married");
        return new OrExpression(julie, married);
    }
}

```

在这个例子中，我们定义了两个规则，"Robert 和 John 是男性"和"Julie 是一个已婚的女性"。我们然后创建了两个表达式对象，分别表示这两个规则，并使用这两个对象来解析输入。

执行流程如下：

1. 创建终结符表达式对象和非终结符表达式对象，用于表示规则。
2. 调用表达式对象的`Interpret`方法，解析输入的字符串。
3. 输出解析结果。

### 4. 迭代器模式（Iterator）

迭代器模式（Iterator Pattern）是一种行为型设计模式，它提供了一种方法来访问一个对象的元素，而不需要暴露该对象的内部表示。以下是在 C#中实现迭代器模式的一个简单示例：

```csharp
// 抽象聚合类
public interface IAggregate
{
    IIterator CreateIterator();
    void Add(string item);
    int Count { get; }
    string this[int index] { get; set; }
}

// 具体聚合类
public class ConcreteAggregate : IAggregate
{
    private List<string> items = new List<string>();

    public IIterator CreateIterator()
    {
        return new ConcreteIterator(this);
    }

    public int Count
    {
        get { return items.Count; }
    }

    public string this[int index]
    {
        get { return items[index]; }
        set { items.Insert(index, value); }
    }

    public void Add(string item)
    {
        items.Add(item);
    }
}

// 抽象迭代器
public interface IIterator
{
    string First();
    string Next();
    bool IsDone { get; }
    string CurrentItem { get; }
}

// 具体迭代器
public class ConcreteIterator : IIterator
{
    private ConcreteAggregate aggregate;
    private int current = 0;

    public ConcreteIterator(ConcreteAggregate aggregate)
    {
        this.aggregate = aggregate;
    }

    public string First()
    {
        return aggregate[0];
    }

    public string Next()
    {
        string ret = null;
        if (current < aggregate.Count - 1)
        {
            ret = aggregate[++current];
        }

        return ret;
    }

    public string CurrentItem
    {
        get { return aggregate[current]; }
    }

    public bool IsDone
    {
        get { return current >= aggregate.Count; }
    }
}

```

在这个代码中，`IAggregate`是抽象聚合类，定义了`CreateIterator`等方法，`ConcreteAggregate`是具体聚合类，实现了`IAggregate`接口。`IIterator`是抽象迭代器，定义了`First`、`Next`等方法，`ConcreteIterator`是具体迭代器，实现了`IIterator`接口。

以下是一个使用这个模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        IAggregate aggregate = new ConcreteAggregate();
        aggregate.Add("Item A");
        aggregate.Add("Item B");
        aggregate.Add("Item C");
        aggregate.Add("Item D");

        IIterator iterator = aggregate.CreateIterator();

        Console.WriteLine("Iterating over collection:");

        string item = iterator.First();
        while (item != null)
        {
            Console.WriteLine(item);
            item = iterator.Next();
        }

        Console.ReadLine();
    }
}

```

在这个例子中，我们创建了一个`ConcreteAggregate`对象，并添加了几个元素。然后我们通过`CreateIterator`方法创建了一个迭代器，并使用这个迭代器遍历了集合中的所有元素。

执行流程如下：

1. 创建一个聚合对象，并添加一些元素。
2. 通过聚合对象的`CreateIterator`方法创建一个迭代器。
3. 通过迭代器的`First`方法获取第一个元素，然后通过`Next`方法获取后续的元素，直到获取不到元素为止。

### 5. 中介者模式（Mediator）

中介者模式是一种行为设计模式，它让你能减少一组对象之间复杂的通信。它提供了一个中介者对象，此对象负责在组中的对象之间进行通信，而不是这些对象直接进行通信。

首先，让我们定义一个中介者接口和一个具体的中介者：

```csharp
// Mediator 接口声明了与组件交互的方法。
public interface IMediator
{
    void Notify(object sender, string ev);
}

// 具体 Mediators 实现协作行为，它负责协调多个组件。
public class ConcreteMediator : IMediator
{
    private Component1 _component1;
    private Component2 _component2;

    public ConcreteMediator(Component1 component1, Component2 component2)
    {
        _component1 = component1;
        _component1.SetMediator(this);
        _component2 = component2;
        _component2.SetMediator(this);
    }

    public void Notify(object sender, string ev)
    {
        if (ev == "A")
        {
            Console.WriteLine("Mediator reacts on A and triggers following operations:");
            this._component2.DoC();
        }
        if (ev == "D")
        {
            Console.WriteLine("Mediator reacts on D and triggers following operations:");
            this._component1.DoB();
            this._component2.DoC();
        }
    }
}

```

接着，我们定义一个基础组件类和两个具体组件：

```csharp
public abstract class BaseComponent
{
    protected IMediator _mediator;

    public BaseComponent(IMediator mediator = null)
    {
        _mediator = mediator;
    }

    public void SetMediator(IMediator mediator)
    {
        this._mediator = mediator;
    }
}

// 具体 Components 实现各种功能。它们不依赖于其他组件。
// 它们也不依赖于任何具体 Mediator 类。
public class Component1 : BaseComponent
{
    public void DoA()
    {
        Console.WriteLine("Component 1 does A.");
        this._mediator.Notify(this, "A");
    }

    public void DoB()
    {
        Console.WriteLine("Component 1 does B.");
        this._mediator.Notify(this, "B");
    }
}

public class Component2 : BaseComponent
{
    public void DoC()
    {
        Console.WriteLine("Component 2 does C.");
        this._mediator.Notify(this, "C");
    }

    public void DoD()
    {
        Console.WriteLine("Component 2 does D.");
        this._mediator.Notify(this, "D");
    }
}

```

最后，我们来创建一个客户端代码：

```csharp
class Program
{
    static void Main(string[] args)
    {
        // The client code.
        Component1 component1 = new Component1();
        Component2 component2 = new Component2();
        new ConcreteMediator(component1, component2);

        Console.WriteLine("Client triggers operation A.");
        component1.DoA();

        Console.WriteLine();

        Console.WriteLine("Client triggers operation D.");
        component2.DoD();
    }
}

```

这个示例中的各个组件通过中介者来进行通信，而不是直接通信，这样就可以减少组件之间的依赖性，使得它们可以更容易地被独立修改。当一个组件发生某个事件（例如"Component 1 does A"）时，它会通过中介者来通知其他组件，这样其他组件就可以根据这个事件来做出响应（例如"Component 2 does C"）。

### 6. 备忘录模式（Memento）

备忘录模式是一种行为设计模式，它能保存对象的状态，以便在后面可以恢复它。在大多数情况下，这种模式可以让你在不破坏对象封装的前提下，保存和恢复对象的历史状态。

以下是一个简单的备忘录模式的实现，其中有三个主要的类：`Originator`（保存了一个重要的状态，这个状态可能会随着时间改变），`Memento`（保存了`Originator`的一个快照，这个快照包含了`Originator`的状态），以及`Caretaker`（负责保存`Memento`）。

```csharp
// Originator 类可以生成一个备忘录，并且可以通过备忘录恢复其状态。
public class Originator
{
    private string _state;

    public Originator(string state)
    {
        this._state = state;
        Console.WriteLine($"Originator: My initial state is: {_state}");
    }

    public void DoSomething()
    {
        Console.WriteLine("Originator: I'm doing something important.");
        _state = GenerateRandomString(30);
        Console.WriteLine($"Originator: and my state has changed to: {_state}");
    }

    private string GenerateRandomString(int length = 10)
    {
        string allowedSymbols = "abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ";
        string result = string.Empty;

        while (length > 0)
        {
            result += allowedSymbols[new Random().Next(0, allowedSymbols.Length)];

            length--;
        }

        return result;
    }

    public IMemento Save()
    {
        return new ConcreteMemento(_state);
    }

    public void Restore(IMemento memento)
    {
        _state = memento.GetState();
        Console.WriteLine($"Originator: My state has changed to: {_state}");
    }
}

// 备忘录接口提供了获取备忘录和原发器状态的方法。但在该接口中并未声明所有的方法，一些方法只在原发器中声明。
public interface IMemento
{
    string GetName();

    string GetState();

    DateTime GetDate();
}

// Concrete Memento 存储原发器状态，并通过原发器实现备份。备忘录是不可变的，因此，没有 set 方法。
public class ConcreteMemento : IMemento
{
    private string _state;
    private DateTime _date;

    public ConcreteMemento(string state)
    {
        _state = state;
        _date = DateTime.Now;
    }

    public string GetState()
    {
        return _state;
    }

    public string GetName()
    {
        return $"{_date} / ({_state.Substring(0, 9)})...";
    }

    public DateTime GetDate()
    {
        return _date;
    }
}

// Caretaker 不依赖于具体备忘录类。结果，它不会有任何访问原发器状态的权利，它只能获取备忘录的元数据。
public class Caretaker
{
    private List<IMemento> _mementos = new List<IMemento>();
    private Originator _originator = null;

    public Caretaker(Originator originator)
    {
        this._originator = originator;
    }

    public void Backup()
    {
        Console.WriteLine("\nCaretaker: Saving Originator's state...");
        _mementos.Add(_originator.Save());
    }

    public void Undo()
    {
        if (_mementos.Count == 0)
        {
            return;
        }

        var memento = _mementos.Last();
        _mementos.Remove(memento);

        Console.WriteLine("Caretaker: Restoring state to: " + memento.GetName());
        try
        {
            _originator.Restore(memento);
        }
        catch (Exception)
        {
            Undo();
        }
    }

    public void ShowHistory()
    {
        Console.WriteLine("Caretaker: Here's the list of mementos:");

        foreach (var memento in _mementos)
        {
            Console.WriteLine(memento.GetName());
        }
    }
}

// 客户端代码
class Program
{
    static void Main(string[] args)
    {
        Originator originator = new Originator("Super-duper-super-puper-super.");
        Caretaker caretaker = new Caretaker(originator);

        caretaker.Backup();
        originator.DoSomething();

        caretaker.Backup();
        originator.DoSomething();

        caretaker.Backup();
        originator.DoSomething();

        Console.WriteLine();
        caretaker.ShowHistory();

        Console.WriteLine("\nClient: Now, let's rollback!\n");
        caretaker.Undo();

        Console.WriteLine("\nClient: Once more!\n");
        caretaker.Undo();
    }
}

```

以上的代码中，Originator 持有一些重要的状态，并且提供了方法去保存它的状态到一个备忘录对象以及从备忘录对象中恢复它的状态。Caretaker 负责保存备忘录，但是它不能操作备忘录对象中的状态。当用户执行操作时，我们先保存当前的状态，然后执行操作。如果用户后来不满意新的状态，他们可以方便地从旧的备忘录中恢复状态。

### 7. 观察者模式（Observer）

观察者模式（Observer Pattern）是一种行为型设计模式，当一个对象的状态发生变化时，依赖它的所有对象都会得到通知并被自动更新。以下是在 C#中实现观察者模式的一个简单示例：

```csharp
// 抽象观察者
public interface IObserver
{
    void Update();
}

// 具体观察者
public class ConcreteObserver : IObserver
{
    private string name;

    public ConcreteObserver(string name)
    {
        this.name = name;
    }

    public void Update()
    {
        Console.WriteLine($"{name} received an update!");
    }
}

// 抽象主题
public interface ISubject
{
    void RegisterObserver(IObserver observer);
    void RemoveObserver(IObserver observer);
    void NotifyObservers();
}

// 具体主题
public class ConcreteSubject : ISubject
{
    private List<IObserver> observers = new List<IObserver>();

    public void RegisterObserver(IObserver observer)
    {
        observers.Add(observer);
    }

    public void RemoveObserver(IObserver observer)
    {
        if (observers.Contains(observer))
        {
            observers.Remove(observer);
        }
    }

    public void NotifyObservers()
    {
        foreach (var observer in observers)
        {
            observer.Update();
        }
    }

    public void ChangeState()
    {
        // 触发状态变化，通知所有观察者
        NotifyObservers();
    }
}

```

在这个代码中，`IObserver`是抽象观察者，定义了`Update`方法，`ConcreteObserver`是具体观察者，实现了`IObserver`接口。`ISubject`是抽象主题，定义了`RegisterObserver`、`RemoveObserver`和`NotifyObservers`方法，`ConcreteSubject`是具体主题，实现了`ISubject`接口。

以下是一个使用这个模式的示例：

```csharp
class Program
{
    static void Main(string[] args)
    {
        ConcreteSubject subject = new ConcreteSubject();

        subject.RegisterObserver(new ConcreteObserver("Observer 1"));
        subject.RegisterObserver(new ConcreteObserver("Observer 2"));
        subject.RegisterObserver(new ConcreteObserver("Observer 3"));

        subject.ChangeState();

        Console.ReadLine();
    }
}

```

在这个例子中，我们创建了一个`ConcreteSubject`对象，并注册了三个观察者。然后我们通过`ChangeState`方法改变了主题的状态，这会触发主题通知所有观察者。

执行流程如下：

1. 创建一个具体主题对象。
2. 创建几个具体观察者对象，并通过主题的`RegisterObserver`方法将这些观察者注册到主题中。
3. 通过主题的`ChangeState`方法改变主题的状态，这会触发主题通知所有观察者。

### 8. 状态模式（State）

状态模式在面向对象编程中，是一种允许对象在其内部状态改变时改变其行为的设计模式。这种类型的设计模式属于行为型模式。在状态模式中，我们创建对象表示各种状态，以及一个行为随状态改变而改变的上下文对象。

以下是一个状态模式的示例。这个示例中，我们将创建一个银行账户，它有两个状态：正常状态（NormalState）和透支状态（OverdrawnState）。当用户执行操作（存款和取款）时，账户状态将相应地进行更改。

首先，我们定义一个表示状态的接口：

```csharp
public interface IAccountState
{
    void Deposit(Action addToBalance);
    void Withdraw(Action subtractFromBalance);
    void ComputeInterest();
}

```

然后，我们创建两个表示具体状态的类：

```csharp
public class NormalState : IAccountState
{
    public void Deposit(Action addToBalance)
    {
        addToBalance();
        Console.WriteLine("Deposit in NormalState");
    }

    public void Withdraw(Action subtractFromBalance)
    {
        subtractFromBalance();
        Console.WriteLine("Withdraw in NormalState");
    }

    public void ComputeInterest()
    {
        Console.WriteLine("Interest computed in NormalState");
    }
}

public class OverdrawnState : IAccountState
{
    public void Deposit(Action addToBalance)
    {
        addToBalance();
        Console.WriteLine("Deposit in OverdrawnState");
    }

    public void Withdraw(Action subtractFromBalance)
    {
        Console.WriteLine("No withdraw in OverdrawnState");
    }

    public void ComputeInterest()
    {
        Console.WriteLine("Interest and fees computed in OverdrawnState");
    }
}

```

然后，我们创建一个 Context 类，它使用这些状态来执行其任务：

```csharp
public class BankAccount
{
    private IAccountState _state;
    private double _balance;

    public BankAccount(IAccountState state)
    {
        _state = state;
        _balance = 0;
    }

    public void Deposit(double amount)
    {
        _state.Deposit(() => _balance += amount);
        StateChangeCheck();
    }

    public void Withdraw(double amount)
    {
        _state.Withdraw(() => _balance -= amount);
        StateChangeCheck();
    }

    public void ComputeInterest()
    {
        _state.ComputeInterest();
    }

    private void StateChangeCheck()
    {
        if (_balance < 0.0)
            _state = new OverdrawnState();
        else
            _state = new NormalState();
    }
}

```

现在，你可以创建一个实例并运行一个 Demo 来测试这个状态模式的代码：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        var account = new BankAccount(new NormalState());

        account.Deposit(1000); // Deposit in NormalState
        account.Withdraw(2000); // Withdraw in NormalState; No withdraw in OverdrawnState
        account.Deposit(100); // Deposit in OverdrawnState

        account.ComputeInterest(); // Interest and fees computed in OverdrawnState

        Console.ReadKey();
    }
}

```

这个程序首先在正常状态下进行存款操作，然后尝试进行取款操作。由于取款金额超过账户余额，所以账户进入透支状态，并阻止进一步的取款操作。但存款仍然被允许，以使账户回归到正常状态。计算利息的行为也根据账户的状态变化而变化。

### 9. 策略模式（Strategy）

策略模式定义了一系列的算法，并将每一个算法封装起来，使得它们可以相互替换。策略模式让算法独立于使用它的客户而独立变化。

以下是一个简单的策略模式的 C#实现。这个例子中，我们将创建一个排序策略，比如快速排序和冒泡排序，它们实现同一个接口，然后创建一个 Context 类，它使用这些策略来执行排序操作。

首先，我们定义一个表示排序策略的接口：

```csharp
public interface ISortStrategy
{
    void Sort(List<int> list);
}

```

然后，我们创建两个表示具体策略的类：

```csharp
public class QuickSort : ISortStrategy
{
    public void Sort(List<int> list)
    {
        list.Sort();  // Quick sort is in-place but here we are using built-in method
        Console.WriteLine("QuickSorted list ");
    }
}

public class BubbleSort : ISortStrategy
{
    public void Sort(List<int> list)
    {
        int n = list.Count;
        for (int i = 0; i < n - 1; i++)
            for (int j = 0; j < n - i - 1; j++)
                if (list[j] > list[j + 1])
                {
                    // swap temp and list[i]
                    int temp = list[j];
                    list[j] = list[j + 1];
                    list[j + 1] = temp;
                }

        Console.WriteLine("BubbleSorted list ");
    }
}

```

然后，我们创建一个 Context 类，它使用这些策略来执行其任务：

```csharp
public class SortedList
{
    private List<int> _list = new List<int>();
    private ISortStrategy _sortstrategy;

    public void SetSortStrategy(ISortStrategy sortstrategy)
    {
        this._sortstrategy = sortstrategy;
    }

    public void Add(int num)
    {
        _list.Add(num);
    }

    public void Sort()
    {
        _sortstrategy.Sort(_list);

        // Print sorted list
        foreach (int num in _list)
        {
            Console.Write(num + " ");
        }
        Console.WriteLine();
    }
}

```

现在，你可以创建一个实例并运行一个 Demo 来测试这个策略模式的代码：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        SortedList sortedList = new SortedList();

        sortedList.Add(1);
        sortedList.Add(5);
        sortedList.Add(3);
        sortedList.Add(4);
        sortedList.Add(2);

        sortedList.SetSortStrategy(new QuickSort());
        sortedList.Sort();  // Output: QuickSorted list 1 2 3 4 5

        sortedList.SetSortStrategy(new BubbleSort());
        sortedList.Sort();  // Output: BubbleSorted list 1 2 3 4 5

        Console.ReadKey();
    }
}

```

这个程序首先创建了一个未排序的列表，然后它首先使用快速排序策略进行排序，接着又使用冒泡排序策略进行排序。

### 10. 模板方法模式（Template Method）

模板方法模式定义了一个操作中算法的骨架，将这些步骤延迟到子类中。模板方法使得子类可以不改变算法的结构即可重定义该算法的某些特定步骤。

以下是一个模板方法模式的示例。这个示例中，我们将创建一个烹饪食物的过程，这个过程有一些固定的步骤（例如准备材料，清理），但是具体的烹饪步骤则取决于具体的食物。

首先，我们定义一个抽象的模板类：

```csharp
public abstract class CookingProcedure
{
    // The 'Template method'
    public void PrepareDish()
    {
        PrepareIngredients();
        Cook();
        CleanUp();
    }

    public void PrepareIngredients()
    {
        Console.WriteLine("Preparing the ingredients...");
    }

    // These methods will be overridden by subclasses
    public abstract void Cook();

    public void CleanUp()
    {
        Console.WriteLine("Cleaning up...");
    }
}

```

然后，我们创建两个具体的子类，它们分别实现了具体的烹饪步骤：

```csharp
public class CookPasta : CookingProcedure
{
    public override void Cook()
    {
        Console.WriteLine("Cooking pasta...");
    }
}

public class BakeCake : CookingProcedure
{
    public override void Cook()
    {
        Console.WriteLine("Baking cake...");
    }
}

```

现在，你可以创建一个实例并运行一个 Demo 来测试这个模板方法模式的代码：

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        CookingProcedure cookingProcedure = new CookPasta();
        cookingProcedure.PrepareDish();

        Console.WriteLine();

        cookingProcedure = new BakeCake();
        cookingProcedure.PrepareDish();

        Console.ReadKey();
    }
}

```

在这个程序中，我们首先创建了一个`CookPasta`对象，然后调用其`PrepareDish`方法。然后，我们创建了一个`BakeCake`对象，再次调用其`PrepareDish`方法。这两个对象虽然具有不同的`Cook`方法，但是它们的`PrepareDish`方法的结构（即算法的骨架）是相同的。

### 11. 访问者模式（Visitor）

访问者模式（Visitor Pattern）是一种将算法与对象结构分离的软件设计模式。这种模式的基本想法就是通过所谓的"访问者"来改变元素的操作。这样一来，元素的类可以用于表示元素结构，而具体的操作则可以在访问者类中定义。

以下是一个使用 C#实现的访问者模式示例，包括了详细的注释和执行流程。

这个示例中有三个主要部分：访问者（IVisitor）、可访问元素（IElement）和元素结构（ObjectStructure）。同时有具体访问者（ConcreteVisitor）和具体元素（ConcreteElement）。

```csharp
// 访问者接口
public interface IVisitor
{
    void VisitConcreteElementA(ConcreteElementA concreteElementA);
    void VisitConcreteElementB(ConcreteElementB concreteElementB);
}

// 具体访问者A
public class ConcreteVisitorA : IVisitor
{
    public void VisitConcreteElementA(ConcreteElementA concreteElementA)
    {
        Console.WriteLine($"{concreteElementA.GetType().Name} is being visited by {this.GetType().Name}");
    }

    public void VisitConcreteElementB(ConcreteElementB concreteElementB)
    {
        Console.WriteLine($"{concreteElementB.GetType().Name} is being visited by {this.GetType().Name}");
    }
}

// 具体访问者B
public class ConcreteVisitorB : IVisitor
{
    public void VisitConcreteElementA(ConcreteElementA concreteElementA)
    {
        Console.WriteLine($"{concreteElementA.GetType().Name} is being visited by {this.GetType().Name}");
    }

    public void VisitConcreteElementB(ConcreteElementB concreteElementB)
    {
        Console.WriteLine($"{concreteElementB.GetType().Name} is being visited by {this.GetType().Name}");
    }
}

// 元素接口
public interface IElement
{
    void Accept(IVisitor visitor);
}

// 具体元素A
public class ConcreteElementA : IElement
{
    public void Accept(IVisitor visitor)
    {
        visitor.VisitConcreteElementA(this);
    }
}

// 具体元素B
public class ConcreteElementB : IElement
{
    public void Accept(IVisitor visitor)
    {
        visitor.VisitConcreteElementB(this);
    }
}

// 对象结构
public class ObjectStructure
{
    private List<IElement> _elements = new List<IElement>();

    public void Attach(IElement element)
    {
        _elements.Add(element);
    }

    public void Detach(IElement element)
    {
        _elements.Remove(element);
    }

    public void Accept(IVisitor visitor)
    {
        foreach (var element in _elements)
        {
            element.Accept(visitor);
        }
    }
}

```

执行流程如下：

1. 创建具体元素 ConcreteElementA 和 ConcreteElementB 的实例。
2. 创建对象结构 ObjectStructure 的实例，并将步骤 1 创建的具体元素添加到对象结构中。
3. 创建具体访问者 ConcreteVisitorA 和 ConcreteVisitorB 的实例。
4. 调用对象结构的 Accept 方法，传入步骤 3 创建的具体访问者，使具体访问者访问对象结构中的所有元素。

以下是一个使用上述代码的示例：

```csharp
public class Program
{
    public static void Main()
    {
        ObjectStructure objectStructure = new ObjectStructure();

        objectStructure.Attach(new ConcreteElementA());
        objectStructure.Attach(new ConcreteElementB());

        ConcreteVisitorA visitorA = new ConcreteVisitorA();
        ConcreteVisitorB visitorB = new ConcreteVisitorB();

        objectStructure.Accept(visitorA);
        objectStructure.Accept(visitorB);

        Console.ReadKey();
    }
}

```

这个程序会打印出访问者 A 和访问者 B 分别访问具体元素 A 和具体元素 B 的信息。

## 技术交流

.NET Core 交流群：737776595

来自 token 的分享
