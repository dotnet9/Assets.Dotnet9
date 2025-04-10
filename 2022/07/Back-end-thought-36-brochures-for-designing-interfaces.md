---
title: 后端思想篇：设计好接口的36个锦囊！
slug: Back-end-thought-36-brochures-for-designing-interfaces
description: 作为后端开发，不管是什么语言，Java、Go还是C++，其背后的后端思想都是类似的。
date: 2022-07-17 19:51:17
copyright: Reprinted
author: 捡田螺的小男孩
originalTitle: 后端思想篇：设计好接口的36个锦囊！
originalLink: https://mp.weixin.qq.com/s/RqsJ_1Ubqpgfv6FCwJmIsA
draft: False
cover: https://img1.dotnet9.com/2022/07/cover_20.jpeg
categories: 
    - 分享
tags: 
    - 架构设计
---

## 前言

大家好，我是捡田螺的小男孩。作为后端开发，不管是什么语言，`Java`、`Go`还是`C++`，其背后的后端思想都是类似的。后面打算出一个后端思想的技术专栏，主要包括后端的一些设计、或者后端规范相关的，希望对大家日常工作有帮助哈。

我们做后端开发工程师，主要工作就是：**如何把一个接口设计好**。所以，今天就给大家介绍，设计好接口的 36 个锦囊。本文就是后端思想专栏的第一篇哈。

![](https://img1.dotnet9.com/2022/07/2001.png)

## 1. 接口参数校验

入参出参校验是每个程序员必备的基本素养。你设计的接口，必须先校验参数。比如入参是否允许为空，入参长度是否符合你的预期长度。这个要养成习惯哈，日常开发中，很多低级 bug 都是不校验参数导致的。

> 比如你的数据库表字段设置为`varchar(16)`,对方传了一个 32 位的字符串过来，如果你不校验参数，**插入数据库直接异常了**。

出参也是，比如你定义的接口报文，参数是不为空的，但是你的接口返回参数，没有做校验，因为程序某些原因，直接返回别人一个`null`值。。。

![](https://img1.dotnet9.com/2022/07/2002.gif)

## 2. 修改老接口时，注意接口的兼容性

很多 bug 都是因为修改了对外旧接口，但是却**不做兼容**导致的。关键这个问题多数是比较严重的，可能直接导致系统发版失败的。新手程序员很容易犯这个错误哦~

![](https://img1.dotnet9.com/2022/07/2003.png)

所以，如果你的需求是在原来接口上修改，尤其这个接口是对外提供服务的话，一定要考虑接口兼容。举个例子吧，比如 dubbo 接口，原本是只接收 A，B 参数，现在你加了一个参数 C，就可以考虑这样处理：

```java
//老接口
void oldService(A,B){
  //兼容新接口，传个null代替C
  newService(A,B,null);
}

//新接口，暂时不能删掉老接口，需要做兼容。
void newService(A,B,C){
  ...
}
```

## 3. 设计接口时，充分考虑接口的可扩展性

要根据实际业务场景设计接口，充分考虑接口的可扩展性。

比如你接到一个需求：是用户添加或者修改员工时，需要刷脸。那你是反手提供一个员工管理的提交刷脸信息接口？还是先思考：提交刷脸是不是通用流程呢？比如转账或者一键提现需要接入刷脸的话，你是否需要重新实现一个接口呢？还是当前按业务类型划分模块，复用这个接口就好，保留接口的可扩展性。

如果按模块划分的话，未来如果其他场景比如一键提现接入刷脸的话，不用再搞一套新的接口，只需要新增枚举，然后复用刷脸通过流程接口，实现一键提现刷脸的差异化即可。

![](https://img1.dotnet9.com/2022/07/2004.png)

## 4.接口考虑是否需要防重处理

如果前端重复请求，你的逻辑如何处理？是不是考虑接口去重处理。

当然，如果是查询类的请求，其实不用防重。如果是更新修改类的话，尤其金融转账类的，就要过滤重复请求了。简单点，你可以使用 Redis 防重复请求，同样的请求方，一定时间间隔内的相同请求，考虑是否过滤。当然，转账类接口，并发不高的话，**推荐使用数据库防重表，以唯一流水号作为主键或者唯一索引**。

![](https://img1.dotnet9.com/2022/07/2005.png)

## 5. 重点接口，考虑线程池隔离。

一些登陆、转账交易、下单等重要接口，考虑线程池隔离哈。如果你所有业务都共用一个线程池，有些业务出 bug 导致线程池阻塞打满的话，那就杯具了，`所有业务都影响了`。因此进行线程池隔离，重要业务分配多一点的核心线程，就更好保护重要业务。

![](https://img1.dotnet9.com/2022/07/2006.png)

## 6. 调用第三方接口要考虑异常和超时处理

如果你调用第三方接口，或者分布式远程服务的的话，需要考虑：

- 异常处理

比如，你调别人的接口，如果异常了，怎么处理，是重试还是当做失败还是告警处理。

- 接口超时

没法预估对方接口一般多久返回，一般设置个超时断开时间，以保护你的接口。**之前见过一个生产问题**，就是 http 调用不设置超时时间，最后响应方进程假死，请求一直占着线程不释放，拖垮线程池。

- 重试次数

你的接口调失败，需不需要重试？重试几次？需要站在业务上角度思考这个问题

![](https://img1.dotnet9.com/2022/07/2007.gif)

## 7. 接口实现考虑熔断和降级

当前互联网系统一般都是分布式部署的。而分布式系统中经常会出现某个基础服务不可用，最终导致整个系统不可用的情况, 这种现象被称为**服务雪崩效应**。

比如分布式调用链路`A->B->C....`，下图所示：

![](https://img1.dotnet9.com/2022/07/2008.png)

> 如果服务 C 出现问题，比如是**因为慢 SQL 导致调用缓慢**，那将导致 B 也会延迟，从而 A 也会延迟。堵住的 A 请求会消耗占用系统的线程、IO 等资源。当请求 A 的服务越来越多，占用计算机的资源也越来越多，最终会导致系统瓶颈出现，造成其他的请求同样不可用，最后导致业务系统崩溃。

为了应对服务雪崩, 常见的做法是**熔断和降级**。最简单是加开关控制，当下游系统出问题时，开关降级，不再调用下游系统。还可以选用开源组件`Hystrix`。

## 8. 日志打印好，接口的关键代码，要有日志保驾护航。

关键业务代码无论身处何地，都应该有足够的日志保驾护航。比如：你实现转账业务，转个几百万，然后转失败了，接着客户投诉，然后你还没有打印到日志，想想那种水深火热的困境下，你却毫无办法。。。

那么，你的转账业务都需要哪些日志信息呢？至少，方法调用前，入参需要打印吧，接口调用后，需要捕获一下异常吧，同时打印异常相关日志吧，如下：

```java
public void transfer(TransferDTO transferDTO){
    log.info("invoke tranfer begin");
    //打印入参
    log.info("invoke tranfer,paramters:{}",transferDTO);
    try {
      res=  transferService.transfer(transferDTO);
    }catch(Exception e){
     log.error("transfer fail,account：{}",
     transferDTO.getAccount（）)
     log.error("transfer fail,exception:{}",e);
    }
    log.info("invoke tranfer end");
}
```

之前写过一篇打印日志的 15 个建议，大家可以看看哈：[工作总结！日志打印的 15 个建议](https://mp.weixin.qq.com/s/D7rye88cki8rXMg0v1-dVw)

## 9. 接口的功能定义要具备单一性

单一性是指接口做的事情比较单一、专一。比如一个登陆接口，它做的事情就只是校验账户名密码，然后返回登陆成功以及`userId`即可。**但是如果你为了减少接口交互，把一些注册、一些配置查询等全放到登陆接口，就不太妥。**

其实这也是微服务一些思想，接口的功能单一、明确。比如订单服务、积分、商品信息相关的接口都是划分开的。将来拆分微服务的话，是不是就比较简便啦。

## 10.接口有些场景，使用异步更合理

举个简单的例子，比如你实现一个用户注册的接口。用户注册成功时，发个邮件或者短信去通知用户。这个邮件或者发短信，就更适合异步处理。因为总不能一个通知类的失败，导致注册失败吧。

至于做异步的方式，简单的就是**用线程池**。还可以使用消息队列，就是用户注册成功后，生产者产生一个注册成功的消息，消费者拉到注册成功的消息，就发送通知。

![](https://img1.dotnet9.com/2022/07/2009.png)

不是所有的接口都适合设计为同步接口。比如你要做一个转账的功能，如果你是单笔的转账，你是可以把接口设计同步。用户发起转账时，客户端在静静等待转账结果就好。如果你是批量转账，一个批次一千笔，甚至一万笔的，你则可以把接口设计为异步。就是用户发起批量转账时，持久化成功就先返回受理成功。然后用户隔十分钟或者十五分钟等再来查转账结果就好。又或者，批量转账成功后，再回调上游系统。

![](https://img1.dotnet9.com/2022/07/2010.png)

## 11. 优化接口耗时，远程串行考虑改并行调用

假设我们设计一个 APP 首页的接口，它需要查用户信息、需要查 banner 信息、需要查弹窗信息等等。那你是一个一个接口串行调，还是并行调用呢？

![](https://img1.dotnet9.com/2022/07/2011.png)

如果是串行一个一个查，比如查用户信息 200ms，查 banner 信息 100ms、查弹窗信息 50ms，那一共就耗时`350ms`了，如果还查其他信息，那耗时就更大了。这种场景是可以改为并行调用的。也就是说查用户信息、查 banner 信息、查弹窗信息，可以同时发起。

![](https://img1.dotnet9.com/2022/07/2012.png)

在 Java 中有个异步编程利器：`CompletableFuture`，就可以很好实现这个功能。有兴趣的小伙伴可以看我之前这个文章哈：[CompletableFuture 详解](https://mp.weixin.qq.com/s/Q5GJkaHucMEtNPJ-XO7A4A)

## 12. 接口合并或者说考虑批量处理思想

数据库操作或或者是远程调用时，能批量操作就不要 for 循环调用。

![](https://img1.dotnet9.com/2022/07/2013.png)

一个简单例子，我们平时一个列表明细数据插入数据库时，不要在 for 循环一条一条插入，建议一个批次几百条，进行批量插入。同理远程调用也类似想法，比如你查询营销标签是否命中，可以一个标签一个标签去查，也可以批量标签去查，那批量进行，效率就更高嘛。

```java
//反例
for(int i=0;i<n;i++){
  remoteSingleQuery(param)
}

//正例
remoteBatchQuery(param);
```

小伙伴们是否了解过`kafka`为什么这么快呢？其实其中一点原因，就是**kafka 使用批量消息**提升服务端处理能力。

## 13. 接口实现过程中，恰当使用缓存

哪些场景适合使用缓存？**读多写少且数据时效要求越低的场景**。

缓存用得好，可以承载更多的请求，提升查询效率，减少数据库的压力。

> 比如一些平时变动很小或者说几乎不会变的商品信息，可以放到缓存，请求过来时，先查询缓存，如果没有再查数据库，并且把数据库的数据更新到缓存。但是，使用缓存增加了需要考虑这些点：缓存和数据库一致性如何保证、集群、缓存击穿、缓存雪崩、缓存穿透等问题。

- 保证数据库和缓存一致性：**缓存延时双删、删除缓存重试机制、读取 biglog 异步删除缓存**
- 缓存击穿：设置数据永不过期
- 缓存雪崩：Redis 集群高可用、均匀设置过期时间
- 缓存穿透：接口层校验、查询为空设置个默认空值标记、布隆过滤器。

一般用`Redis`分布式缓存，当然有些时候也可以考虑使用本地缓存，如`Guava Cache、Caffeine`等。使用本地缓存有些缺点，就是无法进行大数据存储，并且应用进程的重启，缓存会失效。

## 14. 接口考虑热点数据隔离性

瞬时间的高并发，可能会打垮你的系统。可以做一些热点数据的隔离。比如**业务隔离、系统隔离、用户隔离、数据隔离**等。

- 业务隔离性，比如 12306 的分时段售票，将热点数据分散处理，降低系统负载压力。
- 系统隔离：比如把系统分成了用户、商品、社区三个板块。这三个块分别使用不同的域名、服务器和数据库，做到从接入层到应用层再到数据层三层完全隔离。
- 用户隔离：重点用户请求到配置更好的机器。
- 数据隔离：使用单独的缓存集群或者数据库服务热点数据。

## 15. 可变参数配置化，比如红包皮肤切换等

假如产品经理提了个红包需求，圣诞节的时候，红包皮肤为圣诞节相关的，春节的时候，为春节红包皮肤等。

如果在代码写死控制，可有类似以下代码：

```Java
if(duringChristmas){
   img = redPacketChristmasSkin;
}else if(duringSpringFestival){
   img =  redSpringFestivalSkin;
}
```

如果到了元宵节的时候，运营小姐姐突然又有想法，红包皮肤换成灯笼相关的，这时候，是不是要去修改代码了，重新发布了？

从一开始接口设计时，可以实现**一张红包皮肤的配置表**，将红包皮肤做成配置化呢？更换红包皮肤，只需修改一下表数据就好了。

当然，还有一些场景适合一些配置化的参数：一个分页多少数量控制、某个抢红包多久时间过期这些，都可以搞到参数配置化表里面。**这也是扩展性思想的一种体现**。

## 16.接口考虑幂等性

接口是需要考虑幂等性的，尤其抢红包、转账这些重要接口。最直观的业务场景，就是**用户连着点击两次**，你的接口有没有`hold住`。或者消息队列出现重复消费的情况，你的业务逻辑怎么控制？

回忆下，**什么是幂等？**

> 计算机科学中，幂等表示一次和多次请求某一个资源应该具有同样的副作用，或者说，多次请求所产生的影响与一次请求执行的影响效果相同。

大家别搞混哈，**防重和幂等设计其实是有区别的**。防重主要为了避免产生重复数据，把重复请求拦截下来即可。而幂等设计除了拦截已经处理的请求，还要求每次相同的请求都返回一样的效果。不过呢，很多时候，它们的处理流程、方案是类似的哈。

![](https://img1.dotnet9.com/2022/07/2014.gif)

接口幂等实现方案主要有 8 种：

- select+insert+主键/唯一索引冲突
- 直接 insert + 主键/唯一索引冲突
- 状态机幂等
- 抽取防重表
- token 令牌
- 悲观锁
- 乐观锁
- 分布式锁

大家可以看我这篇文章哈：[聊聊幂等设计](https://mp.weixin.qq.com/s/P3GVyHxrSLN4FV2xwnP71g)

## 17. 读写分离，优先考虑读从库，注意主从延迟问题

我们的数据库都是集群部署的，有主库也有从库，当前一般都是读写分离的。比如你写入数据，肯定是写入主库，但是对于读取实时性要求不高的数据，则优先考虑读从库，因为可以分担主库的压力。

如果读取从库的话，需要考虑主从延迟的问题。

## 18.接口注意返回的数据量，如果数据量大需要分页

一个接口返回报文，不应该包含过多的数据量。过多的数据量不仅处理复杂，并且数据量传输的压力也非常大。因此数量实在是比较大，可以分页返回，如果是功能不相关的报文，那应该考虑接口拆分。

## 19. 好的接口实现，离不开 SQL 优化

我们做后端的，写好一个接口，离不开 SQL 优化。

SQL 优化从这几个维度思考：

- explain 分析 SQL 查询计划（重点关注 type、extra、filtered 字段）
- show profile 分析，了解 SQL 执行的线程的状态以及消耗的时间
- 索引优化 （覆盖索引、最左前缀原则、隐式转换、order by 以及 group by 的优化、join 优化）
- 大分页问题优化（延迟关联、记录上一页最大 ID）
- 数据量太大（**分库分表**、同步到 es，用 es 查询）

## 20.代码锁的粒度控制好

什么是加锁粒度呢？

> 其实就是就是你要锁住的范围是多大。比如你在家上卫生间，你只要锁住卫生间就可以了吧，不需要将整个家都锁起来不让家人进门吧，卫生间就是你的加锁粒度。

我们写代码时，如果不涉及到共享资源，就没有必要锁住的。这就好像你上卫生间，不用把整个家都锁住，锁住卫生间门就可以了。

比如，在业务代码中，有一个 ArrayList 因为涉及到多线程操作，所以需要加锁操作，假设刚好又有一段比较耗时的操作（代码中的`slowNotShare`方法）不涉及线程安全问题，你会如何加锁呢？

反例：

```java
//不涉及共享资源的慢方法
private void slowNotShare() {
    try {
        TimeUnit.MILLISECONDS.sleep(100);
    } catch (InterruptedException e) {
    }
}

//错误的加锁方法
public int wrong() {
    long beginTime = System.currentTimeMillis();
    IntStream.rangeClosed(1, 10000).parallel().forEach(i -> {
        //加锁粒度太粗了，slowNotShare其实不涉及共享资源
        synchronized (this) {
            slowNotShare();
            data.add(i);
        }
    });
    log.info("cosume time:{}", System.currentTimeMillis() - beginTime);
    return data.size();
}
```

正例：

```java
public int right() {
    long beginTime = System.currentTimeMillis();
    IntStream.rangeClosed(1, 10000).parallel().forEach(i -> {
        slowNotShare();//可以不加锁
        //只对List这部分加锁
        synchronized (data) {
            data.add(i);
        }
    });
    log.info("cosume time:{}", System.currentTimeMillis() - beginTime);
    return data.size();
}
```

## 21.接口状态和错误需要统一明确

提供必要的接口调用状态信息。比如你的一个转账接口调用是成功、失败、处理中还是受理成功等，需要明确告诉客户端。如果接口失败，那么具体失败的原因是什么。这些必要的信息都必须要告诉给客户端，因此需要定义明确的错误码和对应的描述。同时，尽量对报错信息封装一下，不要把后端的异常信息完全抛出到客户端。

![](https://img1.dotnet9.com/2022/07/2015.png)

## 22.接口要考虑异常处理

实现一个好的接口，离不开优雅的异常处理。对于异常处理，提十个小建议吧：

- 尽量不要使用`e.printStackTrace()`,而是使用`log`打印。因为`e.printStackTrace()`语句可能会导致内存占满。
- `catch`住异常时，建议打印出具体的`exception`，利于更好定位问题
- 不要用一个`Exception`捕捉所有可能的异常
- 记得使用`finally`关闭流资源或者直接使用 try-with-resource
- 捕获异常与抛出异常必须是完全匹配，或者捕获异常是抛异常的父类
- 捕获到的异常，不能忽略它，至少打点日志吧
- 注意异常对你的代码层次结构的侵染
- 自定义封装异常，不要丢弃原始异常的信息`Throwable cause`
- 运行时异常`RuntimeException` ，不应该通过 catch 的方式来处理，而是先预检查，比如：`NullPointerException`处理
- 注意异常匹配的顺序，优先捕获具体的异常

小伙伴们有兴趣可以看下我之前写的这篇文章哈：[Java 异常处理的十个建议](https://mp.weixin.qq.com/s/3mqY77c8iXWvJFzkVQi9Og)

## 23. 优化程序逻辑

优化程序逻辑这块还是挺重要的，也就是说，你实现的业务代码，**如果是比较复杂的话，建议把注释写清楚**。还有，代码逻辑尽量清晰，代码尽量高效。

> 比如，你要使用用户信息的属性，你根据 session 已经获取到`userId`了，然后就把用户信息从数据库查询出来，使用完后，后面可能又要用到用户信息的属性，有些小伙伴没想太多，反手就把`userId`再传进去，再查一次数据库。。。我在项目中，见过这种代码。。。直接把用户对象传下来不好嘛。。

反例伪代码：

```java
public Response test(Session session){
    UserInfo user = UserDao.queryByUserId(session.getUserId());

    if(user==null){
       reutrn new Response();
    }

    return do(session.getUserId());
}

public Response do(String UserId){
  //多查了一次数据库
  UserInfo user = UserDao.queryByUserId(session.getUserId());
  ......
  return new Response();
}
```

正例：

```java
public Response test(Session session){
    UserInfo user = UserDao.queryByUserId(session.getUserId());

    if(user==null){
       reutrn new Response();
    }

    return do(session.getUserId());
}

//直接传UserInfo对象过来即可，不用再多查一次数据库
public Response do(UserInfo user){
  ......
  return new Response();
}
```

当然，这只是一些很小的一个例子，还有很多类似的例子，需要大家开发过程中，多点思考的哈。

## 24. 接口实现过程中，注意大文件、大事务、大对象

- 读取大文件时，不要`Files.readAllBytes`直接读取到内存，这样会 OOM 的，建议使用 BufferedReader 一行一行来。
- 大事务可能导致死锁、回滚时间长、主从延迟等问题，开发中尽量避免大事务。
- 注意一些大对象的使用，因为大对象是直接进入老年代的，可能会触发 fullGC

## 25. 你的接口，需要考虑限流

如果你的系统每秒扛住的请求是 1000，如果一秒钟来了十万请求呢？换个角度就是说，高并发的时候，流量洪峰来了，超过系统的承载能力，怎么办呢？

如果不采取措施，所有的请求打过来，系统 CPU、内存、Load 负载飚的很高，最后请求处理不过来，所有的请求无法正常响应。

针对这种场景，我们可以采用限流方案。就是为了保护系统，多余的请求，直接丢弃。

限流定义：

> 在计算机网络中，限流就是控制网络接口发送或接收请求的速率，它可防止 DoS 攻击和限制 Web 爬虫。限流，也称流量控制。是指系统在面临高并发，或者大流量请求的情况下，限制新的请求对系统的访问，从而保证系统的稳定性。

可以使用 Guava 的`RateLimiter`单机版限流，也可以使用`Redis`分布式限流，还可以使用阿里开源组件`sentinel`限流

大家可以看下我之前这篇文章哈：[4 种经典限流算法讲解](https://mp.weixin.qq.com/s/tlaL0ByrWVQ0qiDssSPWnQ)

## 26.代码实现时，注意运行时异常（比如空指针、下标越界等）

日常开发中，我们需要采取措施**规避数组边界溢出，被零整除，空指针**等运行时错误。类似代码比较常见：

```java
String name = list.get(1).getName(); //list可能越界，因为不一定有2个元素哈
```

应该采取措施，预防一下数组边界溢出。正例如下：

```java
if(CollectionsUtil.isNotEmpty(list)&& list.size()>1){
  String name = list.get(1).getName();
}
```

![](https://img1.dotnet9.com/2022/07/2016.png)

## 27.保证接口安全性

如果你的 API 接口是对外提供的，需要保证接口的安全性。保证接口的安全性有**token 机制和接口签名**。

**token 机制身份验证**方案还比较简单的，就是

![](https://img1.dotnet9.com/2022/07/2017.png)

1. 客户端发起请求，申请获取 token。
2. 服务端生成全局唯一的 token，保存到 redis 中（一般会设置一个过期时间），然后返回给客户端。
3. 客户端带着 token，发起请求。
4. 服务端去 redis 确认 token 是否存在，一般用 redis.del(token)的方式，如果存在会删除成功，即处理业务逻辑，如果删除失败不处理业务逻辑，直接返回结果。

**接口签名**的方式，就是把接口请求相关信息（请求报文，包括请求时间戳、版本号、appid 等），客户端私钥加签，然后服务端用公钥验签，验证通过才认为是合法的、没有被篡改过的请求。

有关于加签验签的，大家可以看下我这篇文章哈：[程序员必备基础：加签验签](https://mp.weixin.qq.com/s/LH4D7hOTXcyhYRCLdgQwRw)

除了**加签验签和 token 机制，接口报文一般是要加密的**。当然，用 https 协议是会对报文加密的。如果是我们服务层的话，如何加解密呢？

> 可以参考 HTTPS 的原理，就是服务端把公钥给客户端，然后客户端生成对称密钥，接着客户端用服务端的公钥加密对称密钥，再发到服务端，服务端用自己的私钥解密，得到客户端的对称密钥。这时候就可以愉快传输报文啦，客户端用**对称密钥加密请求报文，服务端用对应的对称密钥解密报文**。

有时候，接口的安全性，还包括**手机号、身份证等信息的脱敏**。就是说，**用户的隐私数据，不能随便暴露**。

## 28.分布式事务，如何保证

> 分布式事务：就是指事务的参与者、支持事务的服务器、资源服务器以及事务管理器分别位于不同的分布式系统的不同节点之上。简单来说，分布式事务指的就是分布式系统中的事务，它的存在就是为了保证不同数据库节点的数据一致性。

分布式事务的几种解决方案：

- 2PC(二阶段提交)方案、3PC
- TCC（Try、Confirm、Cancel）
- 本地消息表
- 最大努力通知
- seata

大家可以看下这篇文章哈：[看一遍就理解：分布式事务详解](https://mp.weixin.qq.com/s/3r9MfIz2RAtdFhYzwwZxjA)

## 29. 事务失效的一些经典场景

我们的接口开发过程中，经常需要使用到事务。所以需要避开事务失效的一些经典场景。

- 方法的访问权限必须是 public，其他 private 等权限，事务失效
- 方法被定义成了 final 的，这样会导致事务失效。
- 在同一个类中的方法直接内部调用，会导致事务失效。
- 一个方法如果没交给 spring 管理，就不会生成 spring 事务。
- 多线程调用，两个方法不在同一个线程中，获取到的数据库连接不一样的。
- 表的存储引擎不支持事务
- 如果自己 try...catch 误吞了异常，事务失效。
- 错误的传播特性

推荐大家看下这篇文章：[聊聊 spring 事务失效的 12 种场景，太坑了](https://mp.weixin.qq.com/s/03zYJln4VVe52JNbPJSQyQ)

## 30. 掌握常用的设计模式

把代码写好，还是需要熟练常用的设计模式，比如策略模式、工厂模式、模板方法模式、观察者模式等等。设计模式，是代码设计经验的总结。使用设计模式可以可重用代码、让代码更容易被他人理解、保证代码可靠性。

我之前写过一篇总结工作中常用设计模式的文章，写得挺不错的，大家可以看下：[实战！工作中常用到哪些设计模式](https://mp.weixin.qq.com/s/5OSKWsesOoottffNHEDBWQ)

## 31. 写代码时，考虑线性安全问题

在**高并发**情况下，`HashMap`可能会出现死循环。因为它是非线性安全的，可以考虑使用`ConcurrentHashMap`。所以这个也尽量养成习惯，不要上来反手就是一个`new HashMap()`;

> - Hashmap、Arraylist、LinkedList、TreeMap 等都是线性不安全的；
> - Vector、Hashtable、ConcurrentHashMap 等都是线性安全的

![](https://img1.dotnet9.com/2022/07/2018.png)

## 32.接口定义清晰易懂，命名规范。

我们写代码，不仅仅是为了实现当前的功能，也要有利于后面的维护。说到维护，代码不仅仅是写给自己看的，也是给别人看的。所以接口定义要清晰易懂，命名规范。

## 33. 接口的版本控制

接口要做好版本控制。就是说，请求基础报文，应该包含`version`接口版本号字段，方便未来做接口兼容。其实这个点也算接口扩展性的一个体现点吧。

比如客户端 APP 某个功能优化了，新老版本会共存，这时候我们的`version`版本号就派上用场了，对`version`做升级，做好版本控制。

## 34. 注意代码规范问题

注意一些常见的代码坏味道：

- 大量重复代码（抽共用方法，设计模式）
- 方法参数过多（可封装成一个 DTO 对象）
- 方法过长（抽小函数）
- 判断条件太多（优化 if...else）
- 不处理没用的代码
- 不注重代码格式
- 避免过度设计

代码的坏味道，这里我都写到啦：[25 种代码坏味道总结+优化示例](https://mp.weixin.qq.com/s/Da8V0iu4jMgs5qv0jXNm2w)

## 35.保证接口正确性，其实就是保证更少的 bug

保证接口的正确性，换个角度讲，就是保证更少的 bug，甚至是没有 bug。所以接口开发完后，一般需要开发**自测一下**。然后的话，接口的正确还体现在，多线程并发的时候，**保证数据的正确性**, 等等。比如你做一笔转账交易，扣减余额的时候，可以通过 CAS 乐观锁的方式保证余额扣减正确吧。

如果你是实现秒杀接口，得防止超卖问题吧。你可以使用 Redis 分布式锁防止超卖问题。使用 Redis 分布式锁，有几个注意要点，大家可以看下我之前这篇文章哈：[七种方案！探讨 Redis 分布式锁的正确使用姿势](https://mp.weixin.qq.com/s/dHw_7HALEaNMQso0wEXEEw)

## 36.学会沟通，跟前端沟通，跟产品沟通

我把这一点放到最后，学会沟通是非常非常重要的。比如你开发定义接口时，**一定不能上来就自己埋头把接口定义完了，需要跟客户端先对齐接口**。遇到一些难点时，跟技术 leader 对齐方案。实现需求的过程中，有什么问题，及时跟产品沟通。

总之就是，开发接口过程中，一定要沟通好~

## 最后(求关注，别白嫖我)

如果这篇文章对您有所帮助，或者有所启发的话，求一键三连：点赞、转发、在看，您的支持是我坚持写作最大的动力。

![](https://img1.dotnet9.com/2022/07/2019.png)
