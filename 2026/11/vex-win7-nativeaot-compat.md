---
title: "Vex 2.2.0.0：让 .NET 11 NativeAOT 跑在 Windows 7 x64 上"
slug: vex-win7-nativeaot-compat
description: ".NET 11 NativeAOT 在 Win7 上启动报丢失 api-ms-win-core-synch-l1-2-0.dll——用 PE 导入表定位根因，VC-LTL + YY-Thunks + 105KB 垫片三层方案解决"
date: 2026-11-21 20:12:00
lastmod: 2026-11-21 23:35:14
cover: 2026/11/vex-win7-nativeaot-compat-cover.svg
categories:
  - .NET
  - Vex
tags:
  - .NET 11
  - NativeAOT
  - Windows 7
  - VC-LTL
  - YY-Thunks
  - Avalonia
draft: true
---

Vex 主分支用 .NET 11 NativeAOT 发布 win-x64——单文件、启动快、用户机器上不用装任何运行时。代价是老系统跟不上：Windows 7 x64 用户双击 Vex.exe，弹出来的不是主界面，而是一个系统对话框——**丢失 api-ms-win-core-synch-l1-2-0.dll**。静态导入由加载器在进程第一行代码运行前解析，找不到就直接终止，主界面长什么样，用户没机会见。

这个缺失的 DLL 并不是一个真实文件。`api-ms-` 前缀的名字来自 Windows 8 引入的 **API Set** 机制：程序声明一个"契约名"，加载器按系统内置的映射表把它翻译成真正的系统 DLL。Win8 之后的映射表里有 `api-ms-win-core-synch-l1-2-0` 这一条，Win7 的表里压根没有这个契约，加载器只能报"丢失"。网上流传的老药方是装 [KB2999226](https://support.microsoft.com/help/2999226)，但那个补丁补的是 UCRT（`api-ms-win-crt-*` 一族），管不到这一条。

此前 Win7 用户唯一的选择是一条冻结的 win7-archive 分支（.NET 10 + 旧 SkiaSharp），停在 1.20.1.0，与主线的功能差距只增不减，也不再维护。这次 V2.2.0.0 把兼容做回主线：**CRT 换轨、新系统 API 走 thunk、预编译库加垫片，三层各解决一段**。下面按我排查和动手的顺序讲。

## 根因定位：PE 导入表

报错只告诉你缺哪个模块，"是谁把它引进来的"要靠证据。我写了个 Python 脚本，把 Vex.exe 和目录下全部 DLL 的 PE 导入表逐模块扫了一遍，专盯 Win8+ 才有的函数名。结果三个模块静态引入了 `WaitOnAddress` / `WakeByAddressSingle`：

1. **Vex.exe 本体**——.NET 11 AOT 运行时 PAL（平台抽象层）的线程等待原语；
2. **libSkiaSharp.dll**——SkiaSharp 官方预编译，Avalonia 的渲染引擎；
3. **av_libglesv2.dll**——GLES 加载器，渲染链路的另一环。

我又对比了 v2.1.0.1 与 v2.1.0.2 的导入表，逐项一致——**这不是某次升级引入的回归，而是 .NET AOT 运行时的固有关联**。微软对 .NET 的 Windows 门槛从 .NET 8 起就抬到了 Windows 10（见[官方系统要求](https://learn.microsoft.com/dotnet/core/install/windows)），AOT 运行时不再为老系统保留兼容路径，PAL 直接静态链接新系统的同步原语。只要用这个运行时，导入表里就有它，链接期之外无从回避。

## 三层方案

三层各管一段：主程序的 CRT、主程序的新系统 API、预编译库的新系统 API。

### 第一层：VC-LTL 5.3.1，CRT 换回 Win7 自带的那份

主程序对 UCRT 的依赖以 `api-ms-win-crt-*` 的形式躺在导入表里，Win7 上这套契约同样不存在。[VC-LTL](https://github.com/Chuyu-Team/VC-LTL5) 做的事一句话讲完：**把 CRT 的链接目标从 UCRT 换成 msvcrt.dll**——Win7 系统自带，不用装任何 KB 或运行库包。效果直接落在导入表上：主程序引入的模块从 19 个降到 12 个，`api-ms-win-crt-*` 全部消失。

### 第二层：YY-Thunks 1.2.4-beta.2，Win8+ 的 API 链接期配转发

CRT 换完，运行时 PAL 还静态引用着 `WaitOnAddress` 这类 Win8+ 函数。[YY-Thunks](https://github.com/Chuyu-Team/YY-Thunks) 在**链接期**把这类 API 的引用替换成自带的 thunk 代码：Win7 上走它的模拟实现，新系统上直接转发原生 API。这是链接器层面的事，源码一行不用改。

接入就是 Vex.csproj 里两个 PackageReference，版本定在 build/Versions.props：

```xml
<PackageReference Include="VC-LTL" />
<PackageReference Include="YY-Thunks" />
```

两层叠加后，主程序导入表里的 api-ms 清零（实测）。

### 第三层：垫片 DLL，预编译库管不到的部分

VC-LTL 和 YY-Thunks 只作用在自己参与链接的模块上。libSkiaSharp.dll 和 av_libglesv2.dll 是官方预编译的二进制，既不能重链 CRT，也塞不进 thunk——它们导入表里的 `api-ms-win-core-synch-l1-2-0` 原样保留。

办法是自己写一个**同名垫片 DLL**（105KB），顶到这个名字上。导出函数名与系统 API 一致，shim.def 做映射：

```c
LIBRARY api-ms-win-core-synch-l1-2-0
EXPORTS
    WaitOnAddress = shim_wait
    WakeByAddressSingle = shim_wake_one
    WakeByAddressAll = shim_wake_all
```

`WaitOnAddress` 的语义一句话讲清：**盯住一块内存，值变了就醒**。调用方给出地址和比较值，当前值一致就睡、不一致立刻返回；别的线程改了这块内存，就把睡着的人叫醒（[官方文档](https://learn.microsoft.com/windows/win32/api/synchapi/nf-synchapi-waitonaddress)）。.NET 运行时在 Windows 上实现锁，靠的就是这套原语。

Win7 没有这套 API，但有 SRWLOCK 和 CONDITION_VARIABLE——Win7 自带的老原语，足够拼出同样的语义。整个垫片约 100 行 C：全局一把 SRWLOCK 保护 64 个等待块，等待方锁内比对，没变就带着剩余超时睡在条件变量上，被唤醒后回循环重新比对：

```c
BOOL woke = SleepConditionVariableSRW(&blk->cv, &gate, remaining, 0);
blk->waiters--;
if (blk->waiters == 0) blk->used = FALSE;
ReleaseSRWLockExclusive(&gate);
if (!woke) return FALSE;
```

唤醒方按地址找到等待块，`WakeConditionVariable` 唤一个、`WakeAllConditionVariable` 唤全部，对应 `WakeByAddressSingle` / `WakeByAddressAll`。垫片自身只依赖 kernel32，导入表 77 个函数全是 Win7 原生导出——它自己不会引入新的兼容问题。完整实现在仓库 `native/win7-shim/synch_shim.c`。

## 垫片放哪：一个文件伺候两代加载器

垫片放 exe 同目录，靠两代加载器的一条分叉同时伺候两边：

- **Windows 8+**：导入名先经 API Set schema 翻译再落到物理 DLL，exe 目录里的同名文件根本不参与解析——本机 Win11 带着垫片实测运行正常；
- **Windows 7**：schema 里没有这个契约，加载器退回标准搜索顺序，exe 同目录排第一，正好命中垫片。

好处是不用判断系统版本——加载器自己会分叉。复制也不需要手动，Vex.csproj 里一个 Content 项限定 Windows RID，构建、发布输出连同 publish 脚本与 CI 全部自动带上：

```xml
<Content Include="..\..\native\win7-shim\api-ms-win-core-synch-l1-2-0.dll"
         CopyToOutputDirectory="PreserveNewest"
         Link="api-ms-win-core-synch-l1-2-0.dll"
         Condition="'$(RuntimeIdentifier)' == 'win-x64' or '$(RuntimeIdentifier)' == 'win-x86' or '$(RuntimeIdentifier)' == ''" />
```

Condition 把 linux、macOS 的 RID 排除在外，非 Windows 发布不含这个 DLL。只有改 `synch_shim.c` 时才需要重跑 native/win7-shim/build.cmd 重新预构建。

## 验证

三道验证收尾：

- **PE 导入表扫描**：Vex.exe 与垫片合计 0 个 `api-ms-win-core-synch-l1-2-0` 导入——主程序的两处由 VC-LTL 与 YY-Thunks 消掉，libSkiaSharp / av_libglesv2 残留的两条由垫片接住；
- **Win11 冒烟**：AOT 发布产物带垫片，`--verify-ui` 64 次渲染零操作错误——新系统路径没有被兼容改动碰坏；
- **Win7 x64 真机**：启动进入主界面（用户实测确认）。

本地门禁 scripts/verify-release.ps1 与 CI 同源，此后每次推 tag 前都会重复整套 Win11 侧验证；Win7 真机验收随版本发布由用户实测覆盖。

win7-archive 分支随后正式删除，文档口径同步更新——主线已经兼容，冻结分支没有存在的理由。

一条主线，从 Win7 跑到 Win11，维护成本回到一份代码。

---

> 本文在 Vex（基于 .NET 与 Avalonia 的开源 Markdown 编辑器）中写作完成。
