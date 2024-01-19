---
title: 基于 .NET 的 FluentValidation 验证教程 
slug: Based-on-FluentValidation-Verification-Tutorial-for-dotNET
description: FluentValidation 是一个基于 .NET 开发的验证框架，开源免费，而且优雅，支持链式操作，易于理解，功能完善，还是可与 MVC5、WebApi2 和 ASP.NET CORE 深度集成，组件内提供十几种常用验证器，可扩展性好，支持自定义验证器，支持本地化多语言。
date: 2024-01-19 05:26:49
lastmod: 2024-01-19 05:41:34
copyright: Reprinted
banner: false
author: 零度
originaltitle: 基于 .NET 的 FluentValidation 验证教程
originallink: https://www.xcode.me/post/5849
draft: false
cover: https://img1.dotnet9.com/2024/01/cover_07.png
categories: .NET相关
tags: 技术更新
---

FluentValidation 是一个基于 .NET 开发的验证框架，开源免费，而且优雅，支持链式操作，易于理解，功能完善，还是可与 MVC5、WebApi2 和 ASP.NET CORE 深度集成，组件内提供十几种常用验证器，可扩展性好，支持自定义验证器，支持本地化多语言。

虽然 FluentValidation 是一个非常强大的验证框架，但针对该框架的中文资料并不完善，零度在学习的过程中，将官方文档进行了翻译，由此产生本文，可供参阅。

要使用验证框架, 需要在项目中添加对 FluentValidation.dll 的引用，支持 netstandard2.0 库和 .NET4.5 平台，支持.NET Core 平台，最简单的方法是使用 NuGet 包管理器引用组件。

```csharp
Install-Package FluentValidation
```

若要在 ASP.NET Core 中使用 FluentValidation 扩展，可引用以下包：

```csharp
Install-Package FluentValidation.AspNetCore
```

若要在 ASP.NET MVC 5 或 WebApi 2 项目集成, 可以使用分别使用 FluentValidation.Mvc5 和 FluentValidation.WebApi 程序包。

```csharp
Install-Package FluentValidation.Mvc5
Install-Package FluentValidation.WebApi
```

## 创建第一个验证程序

若要为特定对象定义一组验证规则, 您需要创建一个从 `AbstractValidator<T>` 继承的类, 其中泛型T参数是要验证的类的类型。假设您有一个客户类别:

```csharp
public class Customer {
  public int Id { get; set; }
  public string Surname { get; set; }
  public string Forename { get; set; }
  public decimal Discount { get; set; }
  public string Address { get; set; }
}
```

接下来自定义继承于 AbstractValidator 泛型类的验证器，然后在构造函数中使用 LINQ 表达式编写 RuleFor 验证规则。

```csharp
using FluentValidation;

public class CustomerValidator : AbstractValidator<Customer> {
  public CustomerValidator() {
    RuleFor(customer => customer.Surname).NotNull();
  }
}
```

若要执行验证程序，我们通过定义好的 CustomerValidator 验证器传入实体类 Customer 即可。

```csharp
Customer customer = new Customer();
CustomerValidator validator = new CustomerValidator();

ValidationResult result = validator.Validate(customer);
```

该验证方法返回一个 ValidationResult 对象，表示验证结果，ValidationResult 包含两个属性：IsValid属性是布尔值, 它表示验证是否成功，Errors属性包含验证失败的详细信息。

下面的代码演示向控制台输出验证失败的详细信息:

```csharp
Customer customer = new Customer();
CustomerValidator validator = new CustomerValidator();

ValidationResult results = validator.Validate(customer);

if(! results.IsValid) {
  foreach(var failure in results.Errors) {
    Console.WriteLine("Property " + failure.PropertyName + " Error was: " + failure.ErrorMessage);
  }
}
```

## 链接规则写法

您可以将对象的同一属性用多个验证程序链在一起，以下代码将验证 Surname 属性不为 Null 的同时且不等于foo字符串。

```csharp
using FluentValidation;

public class CustomerValidator : AbstractValidator<Customer> {
  public CustomerValidator() {
    RuleFor(customer => customer.Surname).NotNull().NotEqual("foo");
  }
}
```

## 引发异常

如果验证失败, 不想返回 ValidationResult 对象，而是想直接抛出异常，可通过调用验证器的 ValidateAndThrow 进行验证。

```csharp
Customer customer = new Customer();
CustomerValidator validator = new CustomerValidator();

validator.ValidateAndThrow(customer);
```

如果验证失败，将引发一个 ValidationException 类型的异常，这个异常可以被上层程序捕获，并从异常中找到详细错误信息。

## 集合

当针对一个集合进行验证时，只需要定义集合项类型的规则即可，以下规则将对集合中的每个元素运行 NotNull 检查。

```csharp
public class Person {
  public List<string> AddressLines {get;set;} = new List<string>();
}
public class PersonValidator : AbstractValidator<Person> {
  public PersonValidator() {
    RuleForEach(x => x.AddressLines).NotNull();
  }
}
```

## 复杂属性

验证程序可以用于复杂属性，假设您有两个类：客户和地址。

```csharp
public class Customer {
  public string Name { get; set; }
  public Address Address { get; set; }
}

public class Address {
  public string Line1 { get; set; }
  public string Line2 { get; set; }
  public string Town { get; set; }
  public string County { get; set; }
  public string Postcode { get; set; }
}
```

然后定义一个基于地址的 AddressValidator 验证器件：

```csharp
public class AddressValidator : AbstractValidator<Address> {
  public AddressValidator() {
    RuleFor(address => address.Postcode).NotNull();
    //etc
  }
}
```

然后定义一个基于客户的 CustomerValidator 验证器件，对地址验证时使用地址验证器。

```csharp
public class CustomerValidator : AbstractValidator<Customer> {
  public CustomerValidator() {
    RuleFor(customer => customer.Name).NotNull();
    RuleFor(customer => customer.Address).SetValidator(new AddressValidator());
  }
} 
```

另外，还可以在集合属性上使用验证程序，假设客户对象包含订单集合属性：

```csharp
public class Customer {
   public IList<Order> Orders { get; set; }
}

public class Order {
  public string ProductName { get; set; }
  public decimal? Cost { get; set; }
}

var customer = new Customer();
customer.Orders = new List<Order> {
  new Order { ProductName = "Foo" },
  new Order { Cost = 5 } 
};
```

定义了一个 OrderValidator 验证器件：

```csharp
public class OrderValidator : AbstractValidator<Order> {
    public OrderValidator() {
        RuleFor(x => x.ProductName).NotNull();
        RuleFor(x => x.Cost).GreaterThan(0);
    }
}
```

此验证程序可在 CustomerValidator 中通过 SetCollectionValidator 方法使用:

```csharp
public class CustomerValidator : AbstractValidator<Customer> {
    public CustomerValidator() {
        RuleFor(x => x.Orders).SetCollectionValidator(new OrderValidator());
    }
}

var validator = new CustomerValidator();
var results = validator.Validate(customer);
```

执行行验证程序时, 通过验证结果，可以输出订单每个属性验证错误的详细信息：

```csharp
foreach(var result in results.Errors) {
   Console.WriteLine("Property name: " + result.PropertyName);
   Console.WriteLine("Error: " + result.ErrorMessage);
   Console.WriteLine("");
}
Property name: Orders[0].Cost
Error: 'Cost' must be greater than '0'.

Property name: Orders[1].ProductName
Error: 'Product Name' must not be empty.
```

在编写验证规则时，可以通过 Where 关键字排除或者筛选不需要验证的对象。

```csharp
 RuleFor(x => x.Orders).SetCollectionValidator(new OrderValidator())
        .Where(x => x.Cost != null);
```

## 支持规则集

规则集允许您将验证规则组合在一起, 作为一个组一起执行, 同时忽略其他规则，假如：Person 类有3个属性分别是 Id、Surname 和 Forename，我们将 Id 单独验证， Surname 和 Forename 作为一组 Names 规则集进行验证。

```csharp
public class PersonValidator : AbstractValidator<Person> {
  public PersonValidator() {
     RuleSet("Names", () => {
        RuleFor(x => x.Surname).NotNull();
        RuleFor(x => x.Forename).NotNull();
     });
 
     RuleFor(x => x.Id).NotEqual(0);
  }
}
```

然后，我们可以使用验证器提供的扩展方法，针对指定的规则集执行验证，以下代码将不验证 Id 属性。

```csharp
var validator = new PersonValidator();
var person = new Person();
var result = validator.Validate(person, ruleSet: "Names");
```

执行多个规则集验证，可以使用逗号分隔的字符串列表。

```csharp
validator.Validate(person, ruleSet: "Names,MyRuleSet,SomeOtherRuleSet")
```

通过*号匹配所有规则, 可以强制执行所有规则, 不管它们是否在规则集中:

```csharp
validator.Validate(person, ruleSet: "*")
```

## 配置方法

通过在验证程序上调用 WithMessage 方法, 可以覆盖验证程序的默认验证错误消息：

```csharp
RuleFor(customer => customer.Surname).NotNull().WithMessage("姓名不能为空");
```

错误提示中，可以通过 {PropertyName} 占位符替换属性名：

```csharp
RuleFor(customer => customer.Surname).NotNull().WithMessage("{PropertyName}不能为空");
```

除了 {PropertyName} 占位符，框架还内置了：{PropertyValue}、{ComparisonValue}、{MinLength}、{MaxLength}和{TotalLength} 占位符，关于更多内置占位符，可以参阅官方文档。

默认情况下，错误消息会输出属性名称，以下规则验证错误时，输出 Surname 不能为空。

```csharp
RuleFor(customer => customer.Surname).NotNull();
```

验证程序支持通过 WithName 方法来指定属性别名，以下代码输出姓名不能为空。

```csharp
RuleFor(customer => customer.Surname).NotNull().WithName("姓名");
```

FluentValidation 还支持通过 DisplayNameResolver 自定义别名获取逻辑：

```csharp
ValidatorOptions.DisplayNameResolver = (type, member) => {
  if(member != null) {
     return member.Name + "Foo";
  }
  return null;
};
```

也可以使用 DisplayName 特性指定属性别名。

```csharp
public class Person {
  [Display(Name="Last name")]
  public string Surname { get; set; }
}
```

某些条件下，可通过 When 方法按条件设置验证规则，如下代码：GreaterThan 规则仅在 IsPreferredCustomer 为 true 时才执行。

```csharp
RuleFor(customer => customer.CustomerDiscount).GreaterThan(0).When(customer => customer.IsPreferredCustomer);
```

如果需要为多个规则指定相同的条件, 则可以在方法顶层调用，而不是在规则结束时将调:

```csharp
When(customer => customer.IsPreferred, () => {
   RuleFor(customer => customer.CustomerDiscount).GreaterThan(0);
   RuleFor(customer => customer.CreditCardNumber).NotNull();
});
```

默认情况，编写多个链式验证规则时，下无论前一个规则失败与否，后一个规则都将执行，以下代码检查 Surname 是否为空，然后检查 Surname 不等于零度，如多 NotNull 验证失败，则仍将调用 NotEqual 验证。

```csharp
RuleFor(x => x.Surname).NotNull().NotEqual("零度");
```

我们可以通过 StopOnFirstFailure 方法制定级联模式。

```csharp
RuleFor(x => x.Surname).Cascade(CascadeMode.StopOnFirstFailure).NotNull().NotEqual("foo");
```

现在, 如果 NotNull 验证程序失败, NotEqual 验证程序将不会执行。

CascadeMode 是一个枚举，Continue 表示始终调用规则定义中的所有验证程序，StopOnFirstFailure 表示一旦验证程序失败, 就停止执行后边的规则。

若要全局设置级联模式, 可以在应用程序启动时修改 ValidatorOptions 类型的 CascadeMode 属性:

```csharp
ValidatorOptions.CascadeMode = CascadeMode.StopOnFirstFailure;
```

若要为单个验证程序类设置级联模式，可以这样写：

```csharp
public class PersonValidator : AbstractValidator<Person> {
  public PersonValidator() {
    
    // First set the cascade mode
    CascadeMode = CascadeMode.StopOnFirstFailure;
    
    // Rule definitions follow
    RuleFor(...) 
    RuleFor(...)

   }
}
```

默认情况下, FluentValidation 中的所有规则都是独立运行的，无相互影响，有助于异步验证提高性能，但是, 有些情况下, 您希望某些规则仅在另一个规则验证完成后执行，通过 DependentRules 指定规则依赖，当依赖规则验证完成时，当前规则则会执行。

```csharp
RuleFor(x => x.Surname).NotNull().DependentRules(() => {
  RuleFor(x => x.Forename).NotNull();
});
```

我们可以通过 ValidationContext 上下文的 RootContextData 字典向验证程序传递数据。

```csharp
var instanceToValidate = new Person();
var context = new ValidationContext(person);
context.RootContextData["MyCustomData"] = "Test"; 
var validator = new PersonValidator();
validator.Validate(context);
```

然后，可以通过调用 Custom 方法在任何自定义属性验证程序内访问 RootContextData 字典。

```csharp
RuleFor(x => x.Surname).Custom((x, context) => {
  if(context.ParentContext.RootContextData.ContainsKey("MyCustomData")) {
    context.AddFailure("My error message");
  }
});
```

如果每次调用验证程序前，都需要运行特定的代码, 可以通过重写 PreValidate 方法来拦截验证程序，该方法返回 true 验证程序将继续，返回 false 将终止继续验证。

```csharp
public class MyValidator : AbstractValidator<Person> {
  public MyValidator() {
    RuleFor(x => x.Name).NotNull();
  }

  protected override bool PreValidate(ValidationContext<Person> context, ValidationResult result) {
    if (context.InstanceToValidate == null) {
      result.Errors.Add(new ValidationFailure("", "Please ensure a model was supplied."));
      return false;
    }
    return true;
  }
}
```

## 内置验证程序

FluentValidation 有几个内置的验证器，这些验证器的错误消息都可以使用特定占位符。

### NotNull 验证程序

说明：确保指定的属性不是 null。

```csharp
RuleFor(customer => customer.Surname).NotNull();
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{PropertyValue} = 属性的当前值

### NotEmpty 验证程序

说明: 确保指定的属性不是 null、空字符串或空格 (或值类型的默认值, 例如 int 0)。

```csharp
RuleFor(customer => customer.Surname).NotEmpty();
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{PropertyValue} = 属性的当前值

### NotEqual 验证程序

说明: 确保指定属性的值不等于特定值 (或不等于其他属性的值)

```csharp
//Not equal to a particular value
RuleFor(customer => customer.Surname).NotEqual("Foo");

//Not equal to another property
RuleFor(customer => customer.Surname).NotEqual(customer => customer.Forename);
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{ComparisonValue} = 属性不应等于的值

### Equal 相等验证程序

说明: 确保指定属性的值等于特定值 (或等于另一个属性的值)

```csharp
//Equal to a particular value
RuleFor(customer => customer.Surname).Equal("Foo");

//Equal to another property
RuleFor(customer => customer.Password).Equal(customer => customer.PasswordConfirmation);
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{ComparisonValue} = 属性应相等的值
{PropertyValue} = 属性的当前值

### Length 长度验证程序

确保特定字符串属性的长度位于指定范围内。但是, 它不能确保字符串属性是否为 null。

```csharp
RuleFor(customer => customer.Surname).Length(1, 250); //must be between 1 and 250 chars (inclusive)
```

可用的格式参数占位符：

{PropertyName} = 正在验证的属性的名称
{MinLength} = 最小长度
{MaxLength} = 最大长度
{TotalLength} = 输入的字符数
{PropertyValue} = 属性的当前值

### MaxLength 最大长度验证程序

说明：确保特定字符串属性的长度不超过指定的值。

```csharp
RuleFor(customer => customer.Surname).MaximumLength(250); //must be 250 chars or fewer
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{MaxLength} = 最大长度
{TotalLength} = 输入的字符数
{PropertyValue} = 属性的当前值

### MinLength 最小长度验证程序

说明：确保特定字符串属性的长度不能小于指定的值。

```csharp
RuleFor(customer => customer.Surname).MinimumLength(10); //must be 10 chars or more
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{MinLength} = 最小长度
{TotalLength} = 输入的字符数
{PropertyValue} = 属性的当前值

### LessThan 小于验证程序

说明: 确保指定属性的值小于特定值 (或小于另一个属性的值)

```csharp
//Less than a particular value
RuleFor(customer => customer.CreditLimit).LessThan(100);

//Less than another property
RuleFor(customer => customer.CreditLimit).LessThan(customer => customer.MaxCreditLimit);
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{ComparisonValue}-属性比较的值
{PropertyValue} = 属性的当前值

### LessThanOrEqualTo 小于等于验证程序

说明: 确保指定属性的值小于等于特定值 (或小于等于另一个属性的值)

```csharp
//Less than a particular value
RuleFor(customer => customer.CreditLimit).LessThanOrEqualTo(100);

//Less than another property
RuleFor(customer => customer.CreditLimit).LessThanOrEqualTo(customer => customer.MaxCreditLimit);
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{ComparisonValue}-属性比较的值
{PropertyValue} = 属性的当前值

### GreaterThan 大于验证程序

说明: 确保指定属性的值大于特定值 (或大于另一个属性的值)

```csharp
//Greater than a particular value
RuleFor(customer => customer.CreditLimit).GreaterThan(0);

//Greater than another property
RuleFor(customer => customer.CreditLimit).GreaterThan(customer => customer.MinimumCreditLimit);
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{ComparisonValue}-属性比较的值
{PropertyValue} = 属性的当前值

### GreaterThanOrEqualTo 大于等于验证程序

说明: 确保指定属性的值大于等于特定值 (或大于等于另一个属性的值)

```csharp
//Greater than a particular value
RuleFor(customer => customer.CreditLimit).GreaterThanOrEqualTo(1);

//Greater than another property
RuleFor(customer => customer.CreditLimit).GreaterThanOrEqualTo(customer => customer.MinimumCreditLimit);
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{ComparisonValue}-属性比较的值
{PropertyValue} = 属性的当前值

### Must 验证程序

描述: 将指定属性的值传递到一个委托中, 可以对该值执行自定义验证逻辑

```csharp
RuleFor(customer => customer.Surname).Must(surname => surname == "Foo");
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{PropertyValue} = 属性的当前值

请注意, 委托参数不仅传递参数，还支持直接传递验证对象参数:

```csharp
RuleFor(customer => customer.Surname).Must((customer, surname) => surname != customer.Forename)
```

### 正则表达式验证程序

说明: 确保指定属性的值与给定的正则表达式匹配，正则表达式可参阅[正则表达式教程](https://www.xcode.me/book/regular-expressions-tutorial-30-minutes)（站长注：链接已失效）这篇文章

```csharp
RuleFor(customer => customer.Surname).Matches("some regex here");
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{PropertyValue} = 属性的当前值

### Email 电子邮件验证程序

说明: 确保指定属性的值是有效的电子邮件地址格式。

```csharp
RuleFor(customer => customer.Email).EmailAddress();
```

可用的格式参数占位符：
{PropertyName} = 正在验证的属性的名称
{PropertyValue} = 属性的当前值

## 自定义验证程序

### Must 验证程序

实现自定义验证程序的最简单方法是使用方法 Must 方法，假设我们有以下类：

```csharp
public class Person {
  public IList<Person> Pets {get;set;} = new List<Person>();
}
```

为了确保列表中至少包含10个元素, 我们可以这样做:

```csharp
public class PersonValidator:AbstractValidator<Person> {
  public PersonValidator() {
   RuleFor(x => x.Pets).Must(list => list.Count <= 10).WithMessage("The list must contain fewer than 10 items");
  }
}
```

为了使这种逻辑可重用, 我们可以将其封装为扩展方法。

```csharp
public static class MyCustomValidators {
  public static IRuleBuilderOptions<T, IList<TElement>> ListMustContainFewerThan<T, TElement>(this IRuleBuilder<T, IList<TElement>> ruleBuilder, int num) {
	return ruleBuilder.Must(list => list.Count < num).WithMessage("The list contains too many items");
  }
}
```

在这里，我们通过为 IRuleBuilder 创建扩展方法实现可重用逻辑，使用方法很简单。

```csharp
RuleFor(x => x.Pets).ListMustContainFewerThan(10);
```

### 编写自定义验证程序

如果您想灵活控制可重用的验证器, 则可以使用 Must 方法编写自定义规则，此方法允许您手动创建与验证错误关联的实例。

```csharp
public class PersonValidator:AbstractValidator<Person> {
  public PersonValidator() {
   RuleFor(x => x.Pets).Custom((list, context) => {
     if(list.Count > 10) {
       context.AddFailure("The list must contain 10 items or fewer");
     }
   });
  }
}
```

此方法的优点是它允许您为同一规则返回多个错误。

```csharp
context.AddFailure("SomeOtherProperty", "The list must contain 10 items or fewer");
// Or you can instantiate the ValidationFailure directly:
context.AddFailure(new ValidationFailure("SomeOtherProperty", "The list must contain 10 items or fewer");
```

### 自定义属性验证程序

在某些情况下, 针对某些属性的验证逻辑非常复杂, 我们希望将基于属性的自定义逻辑移动到单独的类中，可通过重写 PropertyValidator 类来完成。

```csharp
using System.Collections.Generic;
using FluentValidation.Validators;
public class ListCountValidator<T> : PropertyValidator {
        private int _max;

	public ListCountValidator(int max)
		: base("{PropertyName} must contain fewer than {MaxElements} items.") {
		_max = max;
	}

	protected override bool IsValid(PropertyValidatorContext context) {
		var list = context.PropertyValue as IList<T>;

		if(list != null && list.Count >= _max) {
			context.MessageFormatter.AppendArgument("MaxElements", _max);
			return false;
		}

		return true;
	}
}
```

继承 PropertyValidator 时, 必须重写 IsValid 方法，此方法接受一个对象, 并返回一个布尔值, 指示验证是否成功，可通过 PropertyValidatorContext 属性访问：
Instance-正在验证的对象
PropertyDescription-属性的名称 (或者是由调用 WithName 的自定义的别名)
PropertyValue-正在验证的属性值
Member-描述正在验证的属性的 MemberInfo 信息

若要使用自定义的属性验证程序, 可以在定义验证规则时调用：

```csharp
public class PersonValidator : AbstractValidator<Person> {
    public PersonValidator() {
       RuleFor(person => person.Pets).SetValidator(new ListCountValidator<Pet>(10));
    }
}
```

## 本地化与多语言

FluentValidation 为默认验证消息提供几种语言的翻译，默认情况下, 会根据当前线程的 CurrentUICulture 语言文化来选择语言，你也可以使用 WithMessage 和 WithLocalizedMessage 来指定错误提示。

### WithMessage

如果使用 Visual Studio 的内置的 resx 格式资源文件， 则可以通过调用 WithMessage 本地化错误消息。

```csharp
RuleFor(x => x.Surname).NotNull().WithMessage(x => MyLocalizedMessages.SurnameRequired);
```

当然，您可以将多种语言存储在数据库中，通过 lambda 表达式来读取多语言消息。

### WithLocalizedMessage

您可以通过调用 WithLocalizedMessage 方法，传递资源类型和资源名称，使错误消息支持本地多语言。

```csharp
RuleFor(x => x.Surname).NotNull().WithLocalizedMessage(typeof(MyLocalizedMessages), "SurnameRequired");
```

### 默认错误消息

如果您要替换 FluentValidation 的默认错误提示，可以实现 ILanguageManager 接口，该接口支持本地化多语言。

```csharp
public class CustomLanguageManager : FluentValidation.Resources.LanguageManager {
  public CustomLanguageManager() {
    AddTranslation("en", "NotNullValidator", "'{PropertyName}' is required.");
  }
}
```

以上代码为 NotNullValidator 验证器自定义英文错误提示消息，当然也可以实现更多的语言，定义好 LanguageManager 后，需要在启动时进行配置。

```csharp
ValidatorOptions.LanguageManager = new CustomLanguageManager();
```

### 禁用本地化多语言

您可以完全禁用 FluentValidation 对本地化的支持, 这将强制使用默认的英文语言, 而不考虑线程的 CurrentUICulture。这可以通过静态类 ValidatorOptions 在应用程序的启动例程中完成。

```csharp
ValidatorOptions.LanguageManager.Enabled = false; 
```

还可以强制指定默认语言，始终以特定语言显示:

```csharp
ValidatorOptions.LanguageManager.Culture = new CultureInfo("fr");
```

## ASP.NET Core 集成

FluentValidation 可以与 ASP.NET Core 集成, 要启用 MVC 集成, 您需要通过 NuGet 包来添加对程序集 FluentValidation.AspNetCore 的引用:

```csharp
Install-Package FluentValidation.AspNetCore
```

安装后, 您需要引用命名空间 using FluentValidation.AspNetCore，然后，在应用程序的启动类 Startup 中通过调用 services 的扩展方法 AddFluentValidation 进行配置。

```csharp
public void ConfigureServices(IServiceCollection services) {
    services.AddMvc(setup => {
      //...mvc setup...
    }).AddFluentValidation();
}
```

为了让 ASP.NET Core 发现您的验证程序, 必须在服务集容器中注册它们。

```csharp
public void ConfigureServices(IServiceCollection services) {
    services.AddMvc(setup => {
      //...mvc setup...
    }).AddFluentValidation();

    services.AddTransient<IValidator<Person>, PersonValidator>();
    //etc
}
```

或使用 AddFromAssemblyContaining 方法自动注册特定程序集中的所有验证器。

```csharp
services.AddMvc()
  .AddFluentValidation(fv => fv.RegisterValidatorsFromAssemblyContaining<PersonValidator>());
```

本示例定义 PersonValidator 验证器，用于验证 Person 类型。

```csharp
public class Person {
	public int Id { get; set; }
	public string Name { get; set; }
	public string Email { get; set; }
	public int Age { get; set; }
}

public class PersonValidator : AbstractValidator<Person> {
	public PersonValidator() {
		RuleFor(x => x.Id).NotNull();
		RuleFor(x => x.Name).Length(0, 10);
		RuleFor(x => x.Email).EmailAddress();
		RuleFor(x => x.Age).InclusiveBetween(18, 60);
	}
}
```

定义控制器 PeopleController 用于创建并保存 Person 实例，使用 ModelState.IsValid 判断用户的输入是否验证通过。

```csharp
public class PeopleController : Controller {
	public ActionResult Create() {
		return View();
	}

	[HttpPost]
	public IActionResult Create(Person person) {

		if(! ModelState.IsValid) { // re-render the view when validation failed.
			return View("Create", person);
		}

		Save(person); //Save the person to the database, or some other logic

		TempData["notice"] = "Person successfully created";
		return RedirectToAction("Index");

	}
}
```

这是我们创建 Person 的视图代码：

```html
@model Person

<div asp-validation-summary="ModelOnly"></div>

<form asp-action="Create">
  Id: <input asp-for="Id" /> <span asp-validation-for="Id"></span>
  <br />
  Name: <input asp-for="Name" /> <span asp-validation-for="Name"></span>
  <br />
  Email: <input asp-for="Email" /> <span asp-validation-for="Email"></span>
  <br />
  Age: <input asp-for="Age" /> <span asp-validation-for="Age"></span>

  <br /><br />
  <input type="submit" value="submtit" />
</form>
```

现在, 当您提交表单时, MVC 的模型绑定系统将使用 PersonPersonValidator 验证输入，并将验证结果保存至 ModelState 用于判断。

### 与 ASP.NET Core 内置验证的兼容性

默认情况下, 在执行 FluentValidation 验证之后, MVC原生内置的 DataAnnotations 验证方式也可能执行，这意味着您可以将 FluentValidation 与 DataAnnotations 验证混合在一起使用。如果要禁用此行为, 将 FluentValidation 设为唯一的验证库, 可以将 RunDefaultMvcValidationAfterFluentValidationExecutes 属性设为 false 值：

```csharp
services.AddMvc().AddFluentValidation(fv => {
 fv.RunDefaultMvcValidationAfterFluentValidationExecutes = false;
});
```

### 隐式与显式子属性验证

在验证复杂对象时, 默认情况下, 您必须手动通过 SetValidator 方法指定复杂属性的子验证器。运行 ASP.NET MVC 应用程序时, 还可以选择为子属性启用隐式验证，启用此功能时, MVC 的验证基础结构将会自动查找每个属性的验证程序, 而不必指定的子验证程序，这可以通过将 SetValidatorImplicitlyValidateChildProperties 设置为 true 来完成:

```csharp
services.AddMvc().AddFluentValidation(fv => {
 fv.ImplicitlyValidateChildProperties = true;
});
```

请注意, 如果启用此行为, 则不应手动通过 SetValidator 指定子属性验证程序, 否则，验证程序将执行两次。

### 客户端验证

FluentValidation 是服务端验证框架, 不直接提供任何客户端验证。但是, 它可以生成 jQuery 验证框架支持的 HTML 元素元数据，用于支持 jquery.validate 框架的自动验证。

### 手动验证

有时您可能需要手动验证 MVC 项目中的对象，在这种情况下， 我们可以将验证结果复制到 MVC 的 ModelState 字典中，便可用于前端错误提示。

```csharp
public ActionResult DoSomething() {
  var customer = new Customer();
  var validator = new CustomerValidator();
  var results = validator.Validate(customer);

  results.AddToModelState(ModelState, null);
  return View();
}
```

AddToModelState 方法是作为扩展方法实现的, 需要引用 FluentValidation 命名空间，请注意, 第二个参数是可选的模型名称前缀, 该参数可设置对象属性在 ModelState 字典中的前缀。

### 验证程序自定义

您可以使用 CustomizeValidatorAttribute 为模型指定验证程序，也支持为验证器指定规则集。

```csharp
public ActionResult Save([CustomizeValidator(RuleSet="MyRuleset")] Customer cust) {
  // ...
}
```

这相当于为验证指定规则集，等同于将规则集传递给验证程序：

```csharp
var validator = new CustomerValidator();
var customer = new Customer();
var result = validator.Validate(customer, ruleSet: "MyRuleset");
```

该属性还可用于调用单个属性的验证:

```csharp
public ActionResult Save([CustomizeValidator(Properties="Surname,Forename")] Customer cust) {
  // ...
}
```

这相当于对验证程序指定特定属性，其它属性将不被验证：

```csharp
var validator = new CustomerValidator();
var customer = new Customer();
var result = validator.Validate(customer, properties: new[] { "Surname", "Forename" });
```

也可以使用 CustomizeValidatorAttribute 特性跳过某些类型的验证。

```csharp
public ActionResult Save([CustomizeValidator(Skip=true)] Customer cust) {
  // ...
}
```

### 验证器拦截器

您可以使用拦截器进一步自定义验证过程，拦截器必须实现 FluentValidation.Mvc 命名空间中的 IValidatorInterceptor 接口：

```csharp
public interface IValidatorInterceptor	{
  ValidationContext BeforeMvcValidation(ControllerContext controllerContext, ValidationContext validationContext);
  ValidationResult AfterMvcValidation(ControllerContext controllerContext, ValidationContext validationContext, ValidationResult result);
}
```

此接口有两个方法：BeforeMvcValidation 和 AfterMvcValidation，分别可拦截验证前和验证后的过程。除了在验证程序类中直接实现此接口外, 我们还可以在外部实现该接口, 通过 CustomizeValidatorAttribute 特性指定拦截器:

```csharp
public ActionResult Save([CustomizeValidator(Interceptor=typeof(MyCustomerInterceptor))] Customer cust) {
 //...
}
```

在这种情况下, 拦截器必须是一个实现 IValidatorInterceptor 接口，并具有公共无参数构造函数的类。请注意, 拦截器是高级方案，大多数情况下, 您可能不需要使用拦截器, 但如果需要, 可以选择它。

### 为客户端指定规则集

默认情况下 FluentValidation 不会为客户端生成基于规则集的验证代码, 但您可以通过 RuleSetForClientSideMessagesAttribute 为客户端指定规则集。

```csharp
[RuleSetForClientSideMessages("MyRuleset")]
public ActionResult Index() {
   return View(new PersonViewModel());
}
```

也可以在控制器中使用 SetRulesetForClientsideMessages 扩展方法 (需要引用 FluentValidation 命名空间)为客户端指定规则集。

```csharp
public ActionResult Index() {
   ControllerContext.SetRulesetForClientsideMessages("MyRuleset");
   return View(new PersonViewModel());
}
```

## ASP.NET MVC 5 集成

FluentValidation 还可以与旧的 ASP.NET MVC 5 项目集成，需要通过 NuGet 添加对程序集 FluentValidation.Mvc5 的引用，然后引用 FluentValidation.Mvc 命名空间：

```csharp
Install-Package FluentValidation.Mvc5
```

安装后, 需要在应用程序全局文件 (Global.asax) 中配置 Application_Start 事件，以便于 FluentValidation 执行初始化。

```csharp
protected void Application_Start() {
    AreaRegistration.RegisterAllAreas();

    RegisterGlobalFilters(GlobalFilters.Filters);
    RegisterRoutes(RouteTable.Routes);

    FluentValidationModelValidatorProvider.Configure();
}
```

在MVC内部, FluentValidation 利用验证器工厂为特定类型创建验证程序，默认情况下使用 AttributedValidatorFactory 工厂，通过特性的方式，允许您为指定的模型设置验证程序：

```csharp
[Validator(typeof(PersonValidator))]
public class Person {
	public int Id { get; set; }
	public string Name { get; set; }
	public string Email { get; set; }
	public int Age { get; set; }
}
 
public class PersonValidator : AbstractValidator<Person> {
	public PersonValidator() {
		RuleFor(x => x.Id).NotNull();
		RuleFor(x => x.Name).Length(0, 10);
		RuleFor(x => x.Email).EmailAddress();
		RuleFor(x => x.Age).InclusiveBetween(18, 60);
	}
}
```

您还可以使用MVC自带的 IoC 依赖注入容器实现自定义的验证器工厂, 而不是使用上述特性标记的方法。

```csharp
FluentValidationModelValidatorProvider.Configure(provider => {
  provider.ValidatorFactory = new MyCustomValidatorFactory();
});
```

最后, 我们可以创建控制器和与之关联的视图:

```csharp
public class PeopleController : Controller {
	public ActionResult Create() {
		return View();
	}
 
	[HttpPost]
	public ActionResult Create(Person person) {
 
		if(! ModelState.IsValid) { // re-render the view when validation failed.
			return View("Create", person);
		}
 
		TempData["notice"] = "Person successfully created";
		return RedirectToAction("Index");
 
	}
}
```

这是相应的视图代码：

```html
@Html.ValidationSummary()
 
@using (Html.BeginForm()) {
	Id: @Html.TextBoxFor(x => x.Id) @Html.ValidationMessageFor(x => x.Id)
	<br />
	Name: @Html.TextBoxFor(x => x.Name) @Html.ValidationMessageFor(x => x.Name) 		
	<br />
	Email: @Html.TextBoxFor(x => x.Email) @Html.ValidationMessageFor(x => x.Email)
	<br />
	Age: @Html.TextBoxFor(x => x.Age) @Html.ValidationMessageFor(x => x.Age)
 
	<br /><br />
 
	<input type="submit" value="submit" />
}
```

现在, 当您提交表单时， MVC 的将使用 FluentValidation 框架验证模型。

### 特别说明

在 ASP.NET MVC 5 中使用 FluentValidation 框架，和 ASP.NET Core 基本相同，所以，客户端验证、手动验证、验证程序自定义、验证器拦截器和客户端规则集都类似于上文，由于文章篇幅，这里将不再赘述。

## ASP.NET WebApi 2 集成

FluentValidation 的 WebApi 的集成 与 MVC 5 集成 (上面) 相同, 但您需要通过 NuGet 引用 FluentValidation.WebApi 程序包。

 出处：https://www.xcode.me/post/5849（站长注：该链接已失败，可在博客园浏览转载文章：https://www.cnblogs.com/mq0036/p/14548370.html）

