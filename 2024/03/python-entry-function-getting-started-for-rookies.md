---
title: Python 入口函数（菜鸟入门）
slug: python-entry-function-getting-started-for-rookies
description: 本人 C# 出生，写过少许 C/C++，所以一直想着有个类似 Main() 函数的东西是整个程序的入口。然而，查遍了整个目录，没有找到任何线索，接下来就开始各种捣鼓。
date: 2024-03-12 05:18:47
lastmod: 2024-03-12 05:24:49
copyright: Reprinted
author: Ironyho
originalTitle: Python 入口函数（菜鸟入门）
originalLink: https://blog.csdn.net/Iron_Ye/article/details/80044242
draft: false
cover: https://img1.dotnet9.com/2024/03/cover_07.png
categories: Python
tags: 菜鸟,入口函数
---

## 前言

最近在组内研究专项项目，其中的一个现有工具是用 Python 开发的，我的目标是对这款工具的流程进行优化。虽然可以找到对应的开发者了解现有流程，然后结合我的研究提出优化方案，最后让 TA 去编码实现。但是程序员心理使然，什么东西都想自己琢磨明白，于是开启了摸索历程。

上网搜索资料，下载了 PyCharm 开发工具，进行了一些环境配置，于是乎就开工了。由于之前没有接触过 Python 语言，打开代码文件夹就蒙圈了，只见一大堆 .py 文件，不知道从哪里入手。本人 C# 出生，写过少许 C/C++，所以一直想着有个类似 Main() 函数的东西是整个程序的入口。然而，查遍了整个目录，没有找到任何线索，接下来就开始各种捣鼓。

需要说明的是，本文仅是一只 Python 菜鸟的学习笔记，并不一定正确或完整，欢迎大家斧正。
每每接触新工具或新语言，都有一种莫名的欣喜，乐于用旧知识来推敲，故而载之。

## 顺序执行

在 Python 世界中，每一个 .py 文件就是一个模块，在控制台中输入文件名即可调用该模块。
模块有些类似于 批处理文件（.bat） ，其中的语句是按顺序执行的。
这点和我最初的想象不一致，总想着它和 C# 等语言一样，文件中应该由 class 来组织，实则不然。

首先，在 D 盘 根目录创建一个名 Test1.py 的文件，内容如下：

```python
print("Test1 First")
print("Test1 Second")
```

然后，转到控制台，将目录切换到 D 盘 ，启动 Test1.py 模块，结果如下：

```shell
D:\>python Test1.py
Test1 First
Test1 Second
```

嗯，不错，完全符合预期，再试一下模块间调用。
在 D 盘 中创建 Test2.py 文件，在其中调用 Test1.py 模块：

```python
import Test1

print("Test2 First")
print("Test2 Second")
```

在控制台中启动 Test2.py ，输出结果：

```shell
D:\>python Test2.py
Test1 First
Test1 Second
Test2 First
Test2 Second
```

可以理解的是，在 Test2.py 文件中， `import Test1` 语句在前面，所以在导入 Test1 模块时便执行了其中的语句，因此 Test1 中的输出在前面。
如果将 import Test1 放在后面， Test1 的输出也会出现在后面：

```python
print("Test2 First")
print("Test2 Second")

import Test1
```

```shell
D:\>python Test2.py
Test2 First
Test2 Second
Test1 First
Test1 Second
```

## 函数定义

模块中的代码能否更加灵活？除了按顺序执行，还可以根据需要调用，就像 C# 语言中的函数那样。
上文中的 Print 应该就是一个内建函数，查资料，找到 Pyhton 中函数的定义：

```shell
def 函数名（参数列表）:
    函数体
```

赶紧试一下，在 Test1.py 中定义一个 SayHello 函数：

```python
print("Test1 First")
print("Test1 Second")

def SayHello():
	print("Hello World")

SayHello()
print("Test1 Third")
```

输出结果：

```shell
D:\>python Test1.py
Test1 First
Test1 Second
Hello World
Test1 Third
```

嗯，符合预期，没毛病，按顺序执行。

如果只是定义 SayHello() 函数，而没有调用的话，是不会有 Hello World 一行输出的。

接下来，尝试一下模块间函数的调用，修改 Test2.py 文件：

```python
import Test1

print("Test2 First")
print("Test2 Second")

Test1.SayHello()
```

输出如下：

```shell
D:\>python Test2.py
Test1 First
Test1 Second
Hello World
Test1 Third
Test2 First
Test2 Second
Hello World
```

哈哈，对的，对的，最后一行的 Hello World 即是 Test2.py 中的 Test1.SayHello() 语句输出的。
至于前面第三行的 Hello World 嘛，那是 import Test1 时由 Test1 模块输出的。

## `__main__`

了解了函数的定义及模块间的调用，随之而来的疑惑是，`程序\模块` 的入口在哪里。

搜索了一下资料，找到了 `__name__ `属性。先来测试一段代码，修改 Test1.py 文件：

```python
def SayHello():
	print("Hello World")

def SayBye():
	print("Bye World")


SayHello()

if(__name__=="__main__"):
	print("Main")

SayBye()
```

在控制台中直接启动 Test1.py ：

```shell
D:\>python Test1.py
Hello World
Main
Bye World
```

嗯，还好理解，按顺序执行的，且满足了 `if(__name__=="__main__") `条件，所以输出了 `Main` 。

且慢，换一种方式，通过 Test2.py 间接调用 Test1.py 试试，先修改 Test2.py 文件：

```python
import Test1

print("Test2 First")
print("Test2 Second")
```

然后启动 Test2.py 文件来看看结果：

```shell
D:\>python Test2.py
Hello World
Bye World
Test2 First
Test2 Second
```

纳尼，怎么没有输出 `Main` 呢？嗯，有点意思，找到 [菜鸟教程](http://www.runoob.com/python3/python3-module.html) 的解释：

> 每个模块都有一个 `__name__` 属性，当其值是 `__main__` 时，表明该模块自身在运行，否则是被引入

这个 `__name__` 属性还好理解，模块的保留字段（属性），但是怎么理解这个 `__main__` 呢？

这里的 `__main__` 可能可以理解为程序的入口函数，模块直接被入口函数调用，则其 `__name__ `属性值为 **main**，否则为 `模块文件名`：

```python
def SayHello():
	print("Hello World")

def SayBye():
	print("Bye World")


SayHello()

if(__name__=="__main__"):
	print("Main")
else:
	print(__name__)

SayBye()
```

```shell
D:\>python Test2.py
Hello World
Test1
Bye World
Test2 First
Test2 Second
```

## 总结

本文讲了 Python 模块的一些基本特性，涉及到的知识非常粗浅，仅为记录个人的学习过程。

每每接触新工具或新语言，都有一种莫名的欣喜，乐于用旧知识来推敲，故而载之。

最后，引用 [菜鸟教程](http://www.runoob.com/python3/python3-module.html) 关于 `模块` 的一些重要解释：

- 模块除了方法定义，还可以包括可执行的代码。这些代码一般用来初始化这个模块。
- 一个模块只会被导入一次，不管你执行了多少次 import。这样可以防止导入模块被一遍又一遍地执行。
- 模块是可以导入其他模块的。在一个模块的最前面使用 import 来导入一个模块，当然这只是一个惯例，而不是强制的。
