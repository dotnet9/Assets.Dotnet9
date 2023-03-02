---
title: .NET近期面试题分享与总结
slug: Dotnet-recent-interview-questions-sharing-and-summary
description: 以下内容不是NET面试的全部，而是写一些我认为可能会遗漏的。
date: 2023-03-01 23:02:16
copyright: Reprint
author: 少年知有
originaltitle: .NET近期面试题分享与总结
originallink: https://www.cnblogs.com/chumochen/p/17134303.html
draft: false
cover: https://img1.dotnet9.com/2023/03/cover_01.png
categories: .NET相关
---

以下内容不是NET面试的全部，而是写一些我认为可能会遗漏的。

## 一、面试总结

避坑：深圳龙岗李朗YH股份会鸽offer

因为offer被鸽重新找工作，从8号开始面试到12号（11家公司），整体感觉面试难度不大，就是很多公司都是走流程，并不是真的需要人，有些甚至聊一两句就让回去等通知。值得注意的是面试之前要了解公司是做什么的，大概的业务是怎么样的，因为简历通过一般是技术符合或者某个项目是类似的，这样可以避免在二面的时候回答不了，而且同一个城市的面试尽量真诚，因为彼此的hr和管理可能都是认识的。

## 二、面试内容总结：

后端技术要点：面向对象、常见算法和实现、常见设计模式、C#基础、..NET Core（中间件、与.NET异同、IOC/DI、AOP、国产框架（furion））、EFcore和ORM相关、多线程、性能优化、缓存（Redis、MongoDB ）、MQ、微服务、分布式（少）、Docker（少）、CI/CD（少）、Linux（少）、分库分表（少）。

数据库技术要点：sql语法、索引、查询优化、存储过程、主流数据库的异同（SQL server、MySQL、postgresql（少））、死锁、事务、函数、B+树、红黑树、B树（这部分很少）。

前端技术要点：vue、jQuery、uniapp。

业务相关：1. 是否和客户或者现场直接沟通需求，是怎么做的。2. 讲解一下项目的功能和场景，是否遇到问题，是怎么解决的。

面试题(记得的部分)：

1. 说一下你对面向对象的理解，有什么特点

参考答案：https://blog.csdn.net/weixin_51201930/article/details/122652397

答：面向对象指的是将现实世界中的事物抽象为一个个对象，每个对象都用对应的方法和属性，面向对象的三大特性：继承、封装、多态
 
2. 平时工作中有用到什么算法吗？

参考答案：https://blog.csdn.net/dreame_life/article/details/104342490 https://www.runoob.com/w3cnote/ten-sorting-algorithm.html

答：递归、冒泡、二分。（这个问题我觉得是在问数据结构，可以参考这个https://blog.csdn.net/heyuchang666/article/details/49891635）
 
3. 常见设计模式有哪些？工作中用到了哪些？

参考答案：https://www.cnblogs.com/abcdwxc/archive/2007/10/30/942834.html

答：适配器、装饰器、抽象工厂
 
4. C#的值类型和引用类型有哪些？区别是什么

参考：https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/builtin-types/value-types
答：值类型有整数型、浮点型、布尔型、结构、枚举、元组、char，引用类型有class、interface、delegate、record、dynamic、object、string。

值类型的变量包含类型的实例。 它不同于引用类型的变量，后者包含对类型实例的引用。

值类型均隐式派生自System.ValueType
 
5. C#集合有哪些，区别是什么？

参考：https://learn.microsoft.com/zh-cn/dotnet/standard/collections/commonly-used-collection-types

答：用的比较多的是：ArrayList、List、Queue、Hashtable、Dictionary，区别是在线程安全和应用场景上，像list的读取是线程安全而arrayList不是，list可以用于泛型进行排序，搜索，Dictionary用于键值对的字典集合。Queue代表了一个先进先出的对象集合。当需要对各项进行先进先出的访问时，则使用队列。哈希表中的每一项都有一个键/值对。可以直接用哈希键访问集合中的项目。
 
6. char、string、stringbuild有什么区别。

参考：https://learn.microsoft.com/zh-cn/dotnet/csharp/programming-guide/strings/ https://blog.csdn.net/qq_44034384/article/details/106739003

答：char用于存储单个字符，string是char对象的依序只读集合，每次对string的修改都会创建一个新的string对象，StringBuilder类则不同，每次操作都是对自身对象进行操作，而不是生成新的对象，其所占空间会随着内容的增加而扩充，在做大量的修改操作时，不会因生成大量匿名对象而影响系统性能。
 
7. .NET Framework、.NET Standard、 ..NET Core、.NET 5/6/7 区别

答：.NET Framework和.NET Core都可以用于生成多种类型的应用程序。.NET Framework框架只能在windows上运行。

.NET Core 是一个:适用于windows、linux、macos操作系统的免费开源托管的框架。

.net5/6/7是.NET Core的稳定版本。

.NET Standard是一套规范，相当于一个关系表，把.NET Framework的某些程序集对应到..NET Core
 
8. 谈谈你对.NET Core 中间件和管道的理解

参考：https://learn.microsoft.com/zh-cn/aspnet/core/fundamentals/middleware/?view=aspnetcore-6.0

答：应用的完整请求处理称为管道，中间件是一种装配到应用管道以处理请求和响应的组件，常用于日志记录、异常捕获、请求拦截、缓存处理
 
9. IOC、DI、AOP是什么，为什么使用，怎么用

参考：https://learn.microsoft.com/zh-cn/dotnet/core/extensions/dependency-injection

IOC为控制反转，它是一种思想，把类的具体实现交给外部容器，而不是由类直接实例化，通过这个反转，把控制权交给了外部容器，降低了类与类之间的耦合性。

DI为依赖注入，它是IOC的具体实现，它负责把类与类之间的依赖关系结合起来，有三个生命周期：

- Transient 服务始终不同，每次检索服务时都会创建一个新实例。
- Scoped 服务仅随新范围更改，但在某个范围内是同一实例。
- Singleton 服务始终相同，新实例仅创建一次。

原生DI支持构造注入或ServiceProvider.CreateScope.GetService获取实例，如果需要拓展注入可以使用autofac
AOP面向切面编程，通过预编译方式和运行期间动态代理实现程序功能的统一维护的一种技术。我的理解是在运行时动态映射dll获取类实例
 
10. EF Core和ORM相关

参考：https://blog.csdn.net/u011854789/article/details/72783902

答：ORM指的是面向对象的对象模型和关系型数据库的数据结构之间的互相转换，EF三种编程方式：Database First、Model First、Code First。
 
11. 多线程看这个https://www.yuque.com/zhanglin-l1ak6/ll06t7/tkq97k
 
12. redis面试看这个：https://blog.csdn.net/adminpd/article/details/122934938
 
13. 什么是索引，索引有哪几类（SQL Server）

参考：https://learn.microsoft.com/zh-cn/sql/relational-databases/indexes/indexes?view=sql-server-ver16

答：索引用于加速查询的性能。它可以更快地从表中检索数据
 
14. 什么是存储过程？有哪些优缺点？

答：存储过程是一组为了完成特定功能的SQL 语句集，存储在数据库中。

存储过程的优点：

- 1. 效率高

存储过程编译一次后，就会存到数据库，每次调用时都直接执行。而普通的sql语句我们要保存到其他地方（例如：记事本 上），都要先分析编译才会执行。所以想对而言存储过程效率更高。

- 2. 降低网络流量

存储过程编译好会放在数据库，我们在远程调用时，不会传输大量的字符串类型的sql语句。

- 3. 复用性高

存储过程往往是针对一个特定的功能编写的，当再需要完成这个特定的功能时，可以再次调用该存储过程。

- 4. 可维护性高

当功能要求发生小的变化时，修改之前的存储过程比较容易，花费精力少。

- 5. 安全性高

完成某个特定功能的存储过程一般只有特定的用户可以使用，具有使用身份限制，更安全。

存储过程的缺点：

- 每个数据库的存储过程语法几乎都不一样，十分难以维护（不通用）
- 业务逻辑放在数据库上，难以迭代

手写：

```sql
create proc StuProc
@sname varchar(100)
begin
select S#,Sname,Sage,Ssex from student where sname=@sname
end
go
exec StuProc '赵雷' //执行语句
```

 
15. 什么是死锁，如何避免。

答：死锁是指两个或两个以上的进程在执行过程中，因争夺资源而造成的一种互相等待的现象，若无外力作用，它们都将无法推进下去。按同一顺序访问对象、避免事务中的用户交互，保持事务简短并在一个批处理中，合理设计索引避免全表扫描
 
16. SqlServer函数创建：https://www.cnblogs.com/Brambling/p/6686947.html
 
17. 什么是事务，有哪些特点

答：事务是一种机制、一个操作序列，包含了一组数据库操作命令。事务把所有的命令作为一个整体一起向系统提交或撤销操作请求，即这一组数据库命令要么都执行，要么都不执行，因此事务是一个不可分割的工作逻辑单元。具有 4 个特性，即原子性（Atomicity）、一致性（Consistency）、隔离性（Isolation）和持久性（Durability），这 4 个特性通常简称为 ACID。
 
前端部分：vue相关基础知识、组件通信和复用、与jQuery的区别、js语法、js基础、常用ui框架等。

还会问到缓存中间件、消息中间件、日志中间件（日志上报和统计）、分表分库、缓存持久性和一致性、主从同步、数据读写分离、数据库的B+树、红黑树、B树等。