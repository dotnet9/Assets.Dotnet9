\---

title: 谈谈C# 以管理员方式启动实现过程

slug: talk-about-csharp-starting-as-an-administrator

description: 以管理员方式不只是简单的启动一个进程，在实际开发过程中遇到的情况可能会复杂的多。

date: 2024-03-10 21:47:12

lastmod: 2024-03-10 22:05:27

copyright: Contributes

banner: false

author: 段xx

originaltitle: 谈谈C# 以管理员方式启动实现过程

draft: False

cover: https://img1.dotnet9.com/2024/03/cover_05.png

categories: .NET

tags: 管理员

\---



## 前言

本文由网友(@段xx)投稿，欢迎留言技术讨论。

以管理员方式不只是简单的启动一个进程，在实际开发过程中遇到的情况可能会复杂的多。比如用户打开应用程序就是以管理员方式启动的，那这个时候就不需要再以管理员方式自启；比如用户是在无人值守的情况下使用，就需要考虑管理员提权的提示行为，只有在”不提示，直接提升“的情况下才以管理员方式启动；比如管理员启动方式会进行传递，比如应用A以管理员方式启动，那应用A启动应用B通常情况下，应用B默认获取了应用A的管理员权限等。

本文主要介绍在无人值守情况下，以管理员方式启动的实现过程。其他情况，只要进行灵活组合就应该能够实现。

无人值守的主要特点是应用程序开机自启、崩溃重启，程序自动执行。程序中不能有阻断程序启动或是执行的操作，比如弹窗提示让用户确认或是让用户输入账号密码。解决无人值守的情况，主要解决思路如下图：

![](https://img1.dotnet9.com/2024/03/0501.png)

需要注意的是，如果用户提权行为非”不提示，直接提升“，而一定要以管理员方式启动，就一定要更改提权行为。可以通过运行”gpedit.msc“→计算机配置→windows设置→安全设置→本地策略→安全选项→用户帐户控制: 在管理审批模式下管理员的提升提示行为 来进行更改。

## 实现步骤

下面为流程中设计的步骤代码实现方法：

1. 判断当前应用程序是否是以管理员方式启动，代码如下：

```csharp
public static bool IsRunAsAdmin()
{
    WindowsIdentity windowsIdentity = WindowsIdentity.GetCurrent();
    WindowsPrincipal windows = new WindowsPrincipal(windowsIdentity);
    if (windows.IsInRole(WindowsBuiltInRole.Administrator))
    {
        return true;
    }
    return false;
}
```

**note：此代码是判断用户是否以管理员方式启动，不是用户是否是管理员。因为虽然用户是管理员，但是启动应用程序的时候，不一定是以管理员方式启动的。网上搜索判断用户是否管理员，搜索出来的大部分都是这个代码。**

2. 以管理员方式启动, 这个是最基本的代码，代码如下：

```csharp
public static void RunAsAdmin()
{
    ProcessStartInfo startInfo = new ProcessStartInfo();
    //设置以管理员方式启动标记
    startInfo.Verb = "runas";
    //使用shell启动进程
    startInfo.UseShellExecute = true;
    startInfo.FileName = Process.GetCurrentProcess().MainModule.FileName;
    Process.Start(startInfo);
}
```

**note：Verb是以管理员启动的标识，除了设置Verb，还需要设置UseShellExecute=true，使用shell启动进程，不然启动时管理员权限会进行传递，即如果原先的应用程序不是以管理员方式启动的，那么传递以后也不会以管理员方式启动，以管理员方式启动就会失败。启动对象还有很多属性可以设置，读者可以自行研究。**

3. 判断当前是否开启UAC（用户账号控制），关闭UAC表示当前用户有管理员权限，代码如下：

```csharp
public static bool IsUACEnabled()
{
    //注册表项
    RegistryKey key = Registry.LocalMachine.OpenSubKey(@"SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System", false);
    if (key != null)
    {
        //获取子键EnableLUA的值，1表示开启了UAC
        object value = key.GetValue("EnableLUA");
        if (value != null && value.ToString() == "1")
        {
            return true;
        }
    }
    return false;
}
```

4. 判断当前用户是否在管理员权限组，代码如下：

```csharp
public static bool IsInAdminGroup()
{
    WindowsIdentity windowsIdentity = WindowsIdentity.GetCurrent();
    WindowsPrincipal windowsPrincipal = new WindowsPrincipal(windowsIdentity);
    var claims = windowsPrincipal.Claims;
    //声明集合中有用户组的信息，s-1-5-32-544代码管理员组
    return claims.Any(c => c.Type == "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid" && c.Value == "S-1-5-32-544");
}
```

5. 判断UAC管理员提升权限提示行为，这里需要判断行为是否为“不提示，直接提升”，代码如下：

```csharp
public static bool IsUpPermissionWithOutTip()
{
    //注册表项
    RegistryKey key = Registry.LocalMachine.OpenSubKey(@"SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System", false);
    if (key != null)
    {
        //获取子键ConsentPromptBehaviorAdmin的值，0表示开启了不提示直接提升，就不会造成阻断
        object value = key.GetValue("ConsentPromptBehaviorAdmin");
        if (value != null && value.ToString() == "0")
        {
            return true;
        }
    }
    return false;
}
```

上述是根据主要流程提取出的5个方法。在实际开发过程中。可能还要考虑以管理员方式启动失败后无限重启的问题。方法中也没考虑异常情况，用户需要根据自己的需求，做异常处理。

## 总结

本文主要是根据作者的开发经验整理的无人值守情况下的实现方式，读者在开发过程中要根据实际需求进行灵活组装，或是添加上述没有的功能或是逻辑。作者相关知识也可能存在盲区，或是逻辑考虑不周的，欢迎指正。
