---
title: SwiftUI @State @Published @ObservedObject 深入理解和使用
slug: swiftui-state-published-observadobject-in-depth-understanding-and-use
description: SwiftUI 是Apple 新出面向未来、跨多端解决方案、声明式编程
date: 2021-10-18 16:51:27
copyright: Reprinted
author: Renew全栈工程师
originalTitle: SwiftUI @State @Published @ObservedObject 深入理解和使用
originalLink: https://www.jianshu.com/p/e589181b14db
draft: False
cover: https://img1.dotnet9.com/2021/10/cover_04.jpeg
categories: 
    - Swift UI
tags: 
    - State
    - Published
    - ObservedObject
---

> 1.SwiftUI 是 Apple 新出面向未来、跨多端解决方案、声明式编程
>
> SwiftUI 最新版本 2.0 但是需要 IOS 14 支持，多数现在还用的是 IOS 13 所以很多不完善的东西都用 SwiftUIX 以及各种库代替，bug 也是层出不穷

> 2.下面是鄙人对 @State @Published @ObservedObject 理解，如有不对，还请指出

## 1.@State 介绍

因为 SwiftUI View 采用的是结构体，当创建想要更改属性的结构体方法时，我们需要添加 mutating 关键字,例如：

```Swift
mutating func doSomeWork()
```

然而，Swift 不允许我们创建可变计算属性，这意味着我们不能编写`mutating var body: some View`——这是不允许的。

@State 允许我们绕过结构体的限制：我们知道不能更改它们的属性，因为结构是固定的，但是@State 允许 SwiftUI 将该值单独存储在可以修改的地方。

是的，这感觉有点像作弊，你可能想知道为什么我们不使用类-它们可以自由修改。但是相信我，这是值得的：随着你的进步，你会了解到 SwiftUI 经常破坏和重新创建你的结构体，所以保持它们的小而简单的结构对性能很重要。

提示：在 SwiftUI 中存储程序状态有几种方法，您将学习所有这些方法。@State 是专门为存储在一个视图中的简单属性而设计的。因此，苹果建议我们向这些属性添加私有访问控制，比如：@State private var tapCount = 0。

## 2.@Published + @ObservedObject 介绍

@Published 是 SwiftUI 最有用的包装之一，允许我们创建出能够被自动观察的对象属性，SwiftUI 会自动监视这个属性，一旦发生了改变，会自动修改与该属性绑定的界面。

比如我们定义的数据结构 Model，前提是 @Published 要在 ObservableObject 下使用
然后用 @ObservedObject 来引用这个对象，当然@State 不会报错，但是无法更新

```Swift
class BaseModel: ObservableObject{
    @Published var name:String = ""
}
struct ContentView: View{
  @ObservedObject var baseModel:BaseModel = BaseModel()
  var body: some View{
      Text("用户名\(baseModel.name)")
      Button(action: {
              baseModel.name  = "Renew"
            }, label: {
                Text("更新视图")
            })
  }
}
```

## 3.最重要的部分 （代码注释部分最为主要，务必看完）

> 虽然上面案例运行中什么都正常展示加载，但是到了实际项目中，却一堆 bug，这是如何导致的，如果对 这三种状态跟 View 绑定的关系不了解，很可能给自己留下隐患

**先来看组案例**

```Swift
//// MASK - 先定义两个Model 继承 ObservableObject
class WorkModel: ObservableObject {

    @Published var name = "name"

    @Published var count = 1
}
class UserModel: ObservableObject {

    @Published var nickname = "nickname"

    @Published var header = "http://www.baidu.com"
}
//// MASK - View显示层
struct ContentView: View {
    @ObservedObject var workModel:WorkModel = WorkModel()

    @ObservedObject var userModel:UserModel = UserModel()
    var body: some View {
        VStack{
            Text("work.count \(workModel.count)")
            Text("work.name \(workModel.name)")
            Text("user.nickname \(userModel.nickname)")
            Text("user.header \(userModel.header)")

            Button(action: {
               userModel.nickname = "Renew"
               userModel.header = "http://..."
               workModel.name = "work name"

               workModel.count += 1
            }, label: {
                Text("更新数据")
            })
        }
     }
}
```

不出意外上面代码点击按钮就会更新数据，但是如果我们有个包装类呢

```Swift
class WrapperModel: ObservableObject{

    @ObservedObject var workModel:WorkModel = WorkModel()

    @ObservedObject var userModel:UserModel = UserModel()
}
struct ContentView: View {
    @ObservedObject var wrapperModel:WrapperModel = WrapperModel()
    var body: some View {
        VStack{
            Text("work.count \(wrapperModel.workModel.count)")
            Text("work.name \(wrapperModel.workModel.name)")
            Text("user.nickname \(wrapperModel.userModel.nickname)")
            Text("work.header \(wrapperModel.userModel.header)")

            Button(action: {
               wrapperModel.userModel.nickname = "Renew"
               wrapperModel.userModel.header = "http://..."
               wrapperModel.workModel.name = "work name"

               wrapperModel.workModel.count += 1
            }, label: {
                Text("更新数据")
            })
        }
     }
}
```

这时候点击按钮还会更新数据吗，答案是否定的，那这个是为啥呀？？？

```shell
因为SwiftUI更新数据的前提是触发
第一层 绑定的对象 wrapperModel下的属性（字段）发生更新才会调用视图层更新数据
但是 第一次下绑定的对象还绑定了 @ObservedObject 或者其他类型的对象呢?
还会触发第一次对象属性更新吗，答案是不能的
你可以在 didSet 事件里面捕捉，是捕捉不到的，所以视图是不会更新的，那这还有其他解决方案吗
```

> 有:
>
> 调用对象 wrapperModel.objectWillChange.send() 方法告诉 View 层 我更新
> 但是这个就是绝对的了吗？：不是 如果层次再深一点的 model 还是有 bug，触发不了

## 4.总结以及解决方案

```Swift
/// 既然我们知道View 跟 状态绑定的关系
/// 是以第一继承ObservableObject 类 下的属性（字段）更新来更新视图的
/// 那我们可以给 ObservableObject 加一个 无关紧要的字段，然后编写一个方法，来通知更新
class BaseobservableObject: ObservableObject {
    ///
    /// 注意
    /// 接收 子类model 时候要用   @ObservedObject 不能用 @Published
    /// 因为SwiftUI 更新机制是当前对象有 @Published 字段更新 就会调用View视图进行更新
    /// 在BaseModel里面实现 notifyUpdate 更新当前对象 _lastUpdateTime 字段，实现自身全部字段更新
    @Published private var _lastUpdateTime: Date = Date()
    ///
    /// 通知更新
    public func notifyUpdate() {
        _lastUpdateTime = Date()
    }
}
/// 那当我们 包装类下的对象更新的时候
/// 可以直接 调用包装类 notifyUpdate() 方法更新当前对象属性，来达到更新View 的效果
/// 顾忌：如果多次调用 notifyUpdate() View会刷新两边吗
/// 答案是否定的，再一次函数栈里面 多次调用 notifyUpdate() View也只更新一次
```

```shell
/// 当子类继承了 BaseobservableObject 对象
/// 那么该对象下面属性其实可以不需要在写 @ObservedObject 或者 @Published 了
/// 因为更新属性之后调用了 notifyUpdate() 达到了更新整个对象的效果，所以可以省略了
```

## 5.其他知识

```Swift
/// MASK - 实现一个基础Model类,其他Model继承该类
class BaseModel: ObservableObject {

   @Published var isLoading = false
}
class SonModel: BaseModel {

    @Published var name = "name"

    @Published var count = 1
}
struct ContentView: View {
    @ObservedObject var sonModel:SonModel = SonModel()
    var body: some View {
        VStack{
            Text("name \(sonModel.name)")
            Button(action: {
               sonModel.name = "Renew"
            }, label: {
                Text("加载")
            })
        }
     }
}
/// 问题来了，现在我View 层我直接引用
/// 照说这时候应该 Text 提示信息是 name Renew 但是点击没反应
/// 啥原因，问题其实还是跟上面的问题有点相似
/// SonModel 不是直接继承于 ObservableObject 类的
/// 所以，直接继承 ObservableObject 下的属性（字段）没更新，就不会更新View
/// 最简单的解决办法就是 更新直接继承 ObservableObject（父对象） 里面的随便一个属性
```
