---
title: C#封装FluentValidation,用了之后通篇还是AbstractValidator，真的看不下去了
slug: CSharp-encapsulates-fluentvalidation
description: FluentValidation是一个非常强大的用于构建强类型验证规则的 .NET 框架
date: 2022-06-09 22:12:35
copyright: Reprinted
author: 黑哥聊dotNet
originaltitle: C#封装FluentValidation,用了之后通篇还是AbstractValidator，真的看不下去了
originallink: https://mp.weixin.qq.com/s/u9xXyFxvpSpPBAMNhfR5Og
draft: False
cover: https://img1.dotnet9.com/2022/06/cover_08.jpeg
categories: .NET相关
---

## 讲故事

前几天看公司一个新项目使用了`FluentValidation`，大家都知道`FluentValidation`是一个非常强大的用于构建强类型验证规则的 .NET 框架，帮程序员解决了繁琐的校验问题，用起来非常爽，但我还是遇到了一件非常不爽的事情,如下代码所示：

```csharp
public class UserInformationValidator : AbstractValidator<UserInformation>
{
 public UserInformationValidator()
 {
     RuleFor(o => o.UserName).Length(2, 20).WithMessage("姓名长度输入错误");
     RuleFor(o => o.Sex).Must(o=>o=="男"||o=="女").WithMessage("性别输入错误");
     RuleFor(o => o.Age).ExclusiveBetween(0, 200).WithMessage("年龄输入错误");
     RuleFor(o => o.Email).EmailAddress().WithMessage("邮箱输入错误");
  }
}
  
  
static void Main(string[] args)
{

    UserInformation userInformation = new UserInformation();
    userInformation.UserName = "";
    userInformation.Sex = "不男不女";
    userInformation.Age = 2200;
    userInformation.Email = "xxxxx";
    UserInformationValidator validationRules = new UserInformationValidator();
    var result=   validationRules.Validate(userInformation);
    if (!result.IsValid)
    {
      Console.WriteLine( string.Join(Environment.NewLine, result.Errors.Select(x => x.ErrorMessage).ToArray()));
    }

}
```

我们每验证一个对象,就要新建一个类型的验证器 ，如上的`UserInformationValidator` ，虽然这样写逻辑上没有任何问题，但我有洁癖哈，接下来我们试着封装一下，嘿嘿，用更少的代码做更多的事情。
  
## 安装

在创建任何验证器之前，您需要在项目中添加对 `FluentValidation.dll` 的引用。最简单的方法是使用 `NuGet 包管理器`或 `dotnet CLI`。

## 模板化代码封装探索

### 将模板化的代码提取到父类中

仔细看上面的代码你会发现，我们每新建一个验证器，就必须要创建一个继承自`AbstractValidator<T>`的类，其中`T`是您希望验证的类的类型，封装一个验证器父类

```csharp
public class CommonVaildator<T> : AbstractValidator<T>
{

}
```

### 增加验证规则

真正的业务逻辑是写在`UserInformationValidator`验证器里面的，而这块代码中只需要拿到`RuleFor`即可，其它的统一封装到父类中，对不对，我们按照这个思路代码，封装一个**长度验证器规则**。

首先让我们看看RuleFor的原型

```csharp
public IRuleBuilderInitial<T, TProperty> RuleFor<TProperty>(Expression<Func<T, TProperty>> expression)
```

它的参数是一个`Func`委托，那么`Expression`是什么呢？

`Experssion`是一种`表达式树`！
  
`表达式树`是一种允许`将lambda表达式表示为树状数据结构`而不是可执行逻辑的代码。
  
在C#中是`Expression`来定义的，它是一种语法树，或者说是一种数据结构。其主要用于存储需要计算、运算的一种结构，它只提供存储功能，不进行运算。通常`Expression`是配合`Lambda`一起使用，这里就不做过多的解释了！

那么我们就能很轻易的封装出长度验证器规则了！

```csharp
public void LengthVaildator(Expression<Func<T, string>> expression, int min, int max, string Message)
{
    RuleFor(expression).Length(min, max).WithMessage(Message);
}
```

同理，我们也可以接着封装**谓词验证器规则** **邮箱验证器规则**等等

```csharp
public void MustVaildator(Expression<Func<T, string>> expression ,Func<T,string, bool> expression2, string Message)
{
    RuleFor(expression).Must(expression2).WithMessage(Message);
}
public void EmailAddressVaildator(Expression<Func<T, string>> expression, string Message)
{
    RuleFor(expression).EmailAddress().WithMessage(Message);
}
```

### 封装验证方法

上面我们把验证器封装好了，那么将  `var result=   validationRules.Validate(userInformation);`这种验证方法封装一下不是手到擒来,代码如下:

```csharp
public static string ModelValidator<T>(T source, AbstractValidator<T> sourceValidator) where T : class
{
    var results = sourceValidator.Validate(source);
    if (!results.IsValid)
        return string.Join(Environment.NewLine, results.Errors.Select(x => x.ErrorMessage).ToArray());
    else
        return "";

}
```
  
### 测试封装后的代码

```csharp
CommonVaildator<UserInformation> commonUserInformation = new CommonVaildator<UserInformation>();
commonUserInformation.LengthVaildator(o => o.UserName, 2, 30, "姓名长度输入错误");
commonUserInformation.MustVaildator(o => o.Sex, (user, _) => user.Sex =="男"||user.Sex=="女" , "性别输入错误");
commonUserInformation.ExclusiveBetweenVaildator(o=>o.Age,0, 200, "年龄输入错误");
commonUserInformation.EmailAddressVaildator(o => o.Email, "邮箱输入错误");
string msg= VaildatorHelper.ModelValidator(userInformation, commonUserInformation);
Console.WriteLine(msg);
```

这样代码看起来是不是就简洁多了，我这里就只封装了四种验证规则，其它的我就不在此封装了。

## 总结

文章来源于工作中的点点滴滴，这也是我的即兴封装，大家要是有更好的封装代码，欢迎交流，独乐乐不如众乐乐，本篇就说到这里啦，希望对您有帮助。
  
