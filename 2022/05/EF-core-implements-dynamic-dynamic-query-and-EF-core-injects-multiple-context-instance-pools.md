---
title: EF Core实现dynamic动态查询和EF Core注入多个上下文实例池
slug: EF-core-implements-dynamic-dynamic-query-and-EF-core-injects-multiple-context-instance-pools
description: 无论是在EF 6.x还是EF Core中对于原始查询的APi都比较鸡肋
date: 2022-05-04 16:43:26
copyright: Reprinted
author: Jeffcky
originalTitle: EF Core实现dynamic动态查询和EF Core注入多个上下文实例池
originalLink: https://www.cnblogs.com/CreateMyself/p/8921881.html
draft: False
cover: https://img1.dotnet9.com/2022/05/cover_11.jpg
categories: .NET
tags: .NET, ORM, EF Core
---

## 前言

很长一段时间没有写博客了，今天补上一篇吧，偶尔发现不太愿意写博客了，太耗费时间，不过还是在坚持当中，毕竟或许写出来的东西能帮到一些童鞋吧，接下来我们直奔主题。无论是在 EF 6.x 还是 EF Core 中对于原始查询的 APi 都比较鸡肋，比如我们只想查询单个值，它们是不支持的，比如我们只想有些列，它们也是不支持的，太多太多不支持，唯一支持的是只能返回表中所有列即类中所有字段。所以大部分情况下我都是写原生 SQL，原始查询都没怎么用到过，最近有对热爱 EF 的同行问到怎么利用 SqlQuery 实现动态查询，我没有答案，压根没想过用这个方法，私下看了看，还是给出一点点思考吧。若对您有帮助就好，没有用就当是我补上了一篇博客吧。

## EF 6.x 和 EF Core 实现动态查询

```csharp
public static IEnumerable<dynamic> SqlQueryDynamic(this DbContext db, string Sql, params SqlParameter[] parameters)
{
    using (var cmd = db.Database.Connection.CreateCommand())
    {
        cmd.CommandText = Sql;

        if (cmd.Connection.State != ConnectionState.Open)
        {
            cmd.Connection.Open();
        }

        foreach (var p in parameters)
        {
            var dbParameter = cmd.CreateParameter();
            dbParameter.DbType = p.DbType;
            dbParameter.ParameterName = p.ParameterName;
            dbParameter.Value = p.Value;
            cmd.Parameters.Add(dbParameter);
        }

        using (var dataReader = cmd.ExecuteReader())
        {
            while (dataReader.Read())
            {
                var row = new ExpandoObject() as IDictionary<string, object>;
                for (var fieldCount = 0; fieldCount < dataReader.FieldCount; fieldCount++)
                {
                    row.Add(dataReader.GetName(fieldCount), dataReader[fieldCount]);
                }
                yield return row;
            }
        }
    }
}
```

那么最终如上查询后返回动态集合，我们该如何转换为集合对象呢？我想都没想如下直接先序列化然后反序列化，若您有更好的解决方案，请自行实现即可。

```csharp
using (var ctx = new EfDbContext())
{
    ctx.Database.Log = Console.WriteLine;

    var dynamicOrders = ctx.SqlQueryDynamic("select * from dbo.Orders");
    var ordersJson = JsonConvert.SerializeObject(dynamicOrders);
    var orders = JsonConvert.DeserializeObject<List<Order>>(ordersJson);
};
```

![](https://img1.dotnet9.com/2022/05/1101.png)

当然上述我只是简单查询了一个表，若您有多个表也是好使的，最后反序列化为不同的对象即可，未经测试，您可自行测试。

## EF Core 使用多个上下文实例池

有很多人无论是在 EF 6.x 还是在 EF Core 中一直以来都是使用一个上下文，但是不知我们是否有想过使用多个上下文呢？比如在电商项目中，对于产品相关操作我们可以使用产品上下文，对于加入购物车操作使用购物车上下文，对于订单操作使用订单上下文。这么做的好处是什么呢？我们可以将数据库表也就说将实体拆分成不同的业务。至今我还没看到有人这么做过，如果是我的话，至少我会这么做。

```csharp
//Add DbContext
var dbConnetionString = Configuration.GetConnectionString("DbConnection");
services.AddDbContextPool<ShopCartDbContext>(options =>
{
    options.UseSqlServer(dbConnetionString);
}).AddDbContextPool<BookDbContext>(options =>
{
    options.UseSqlServer(dbConnetionString);
}).AddDbContextPool<OrderDbContext>(options =>
{
    options.UseSqlServer(dbConnetionString);
});
```

在 EF Core 2.0 中有了上下文实例池，类似于 ADO.NET 中的连接池一样，但是这玩意你从表面理解那你就大错特错了，有关上下文实例池（从去年开始我着手写了一本关于 EF 6.x 和 EF Core 的书籍最近会出版）实现本质，只能说它和 ADO.NET 中的连接池不是一样的哦。那么如上述使用多个上下文实例池是否就一定好使呢？不好意思，这样配置是错误的。但运行程序你会发现抛出类似如下异常：

```shell
Exception message:
System.ArgumentException: Expression of type 'Microsoft.EntityFrameworkCore.DbContextOptions`1[MultiContext.Contexts.BContext]' cannot be used for constructor parameter of type 'Microsoft.EntityFrameworkCore.DbContextOptions`1[MultiContext.Contexts.AContext]' Parameter name: arguments[0]
Stack trace:
...........
```

在此特性出来时大家都在欢呼能够提高性能，对不起上下文实例池虽然可能在一定程度上提高性能，但是我只能讲只能有可能的性能改进，如果你知道或者看过 EF Core 实现上下文实例池的原理，就明白了其实现的本质从而恍然大悟我所说的可能的性能上的改进是什么意思。至于为何不能注册多个上下文实例池，我也是私下写项目遇见的，具体请参看 github：

https://github.com/aspnet/EntityFrameworkCore/issues/9433。

## 总结

好了今天就到这里，没有过多的解释和叙述，上来就是直奔主题，最近思想放飞中，对写博客慢慢失去了很大的兴趣，偶尔感性中，待我满血复活调节好心情再来和大家继续分享技术，我一直在，一段时间没写博客可能是因为累了，又或者是私下在学习 IdentityServer 或者其他技术中，干咱这行的，除非转行那就老老实实积累经验和多学点技术吧，年轻不奋斗，那什么时候奋斗呢。今天说了啥，胡思乱想中，莫见怪。

你所看到的并非事物本身，而是经过诠释后所赋予的意义
