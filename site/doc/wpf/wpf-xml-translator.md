# WPF国际化包-WPFXmlTranslator

在当今全球化的软件开发环境中，应用程序的国际化变得越来越重要。它可以让你的软件适应不同地区和语言的用户，提升用户体验。本文和[《Avalonia使用XML文件实现国际化》](https://dotnet9.com/bbs/post/2024/12/avalonia-using-xml-files-for-internationalization)类似，今天介绍的包只用于WPF程序，下面我们将简单介绍其用法。

## 安装必备 NuGet 包

要在WPF程序中使用自定义XML文件实现国际化，首先需要安装一个关键的NuGet包。在Visual Studio的NuGet包管理器控制台中，执行以下命令：

```bash
Install-Package WPFXmlTranslator
```

这个包为我们提供了实现国际化所需的核心功能和工具。

## 动态获取语言列表

在一个支持多语言的应用程序中，让用户能够选择他们偏好的语言是非常重要的。通过以下代码，我们可以轻松地获取语言列表：

```csharp
List<LocalizationLanguage> languages = I18nManager.Instance.Resources.Select(kvp => kvp.Value).ToList();
```

这里的 `I18nManager` 是 `WPFXmlTranslator` 包提供的一个管理类，它负责处理国际化相关的资源。`Resources` 属性存储了所有可用的语言资源，我们通过 `Select` 方法将其转换为 `LocalizationLanguage` 对象的列表。

其中，“LocalizationLanguage” 类定义如下：

```csharp
public class LocalizationLanguage
{
    public string Language { get; set; } = (string) null;

    public string Description { get; set; } = (string) null;

    public string CultureName { get; set; } = (string) null;

    //...
}
```

`LocalizationLanguage` 类包含了语言的基本信息，如语言名称、描述和文化名称。获取到语言列表后，我们可以将其用于界面绑定，例如在下拉菜单中显示可供用户选择的语言选项，或者在其他需要展示语言信息的界面元素中进行数据绑定。

```xaml
<ComboBox ItemsSource="{Binding Languages}"
      SelectedItem="{Binding SelectLanguage}"
      DisplayMemberPath="Language" />
```

在这个XAML代码中，我们创建了一个 `ComboBox` 控件，将其 `ItemsSource` 属性绑定到 `Languages` 列表，`SelectedItem` 属性绑定到 `SelectLanguage` 属性，`DisplayMemberPath` 属性设置为 `Language`，这样就可以在下拉菜单中显示语言列表供用户选择。

## 动态切换语言

当用户在界面中选择了不同的语言后，我们需要在代码中实现语言的动态切换。以下是实现语言切换的代码示例：

```csharp
var culture = new CultureInfo(language);
I18nManager.Instance.Culture = culture;
```

这里的 `language` 参数为 `LocalizationLanguage` 类的 `CultureName` 属性值。`CultureInfo` 类是.NET框架中用于表示特定文化的类，通过设置 `I18nManager.Instance.Culture` 属性为相应的 `CultureInfo` 对象，我们可以实现界面语言的即时切换，为用户提供无缝的国际化体验。

## 代码中使用翻译字符串

在代码中，我们可以根据强类型 Key 方便地获取当前语言文化的翻译字符串。例如：

```csharp
var titleCurrentCulture = I18nManager.Instance.GetResource(Localization.Main.MainView.Title); // 获取当前语言
var titleZhCN = I18nManager.Instance.GetResource(Localization.Main.MainView.Title, "zh-CN");	// 获取中文简体
var titleEnUS = I18nManager.Instance.GetResource(Localization.Main.MainView.Title, "en-US");	// 获取英文
```

`I18nManager.Instance.GetResource` 方法用于获取指定语言的翻译字符串。第一个参数是强类型的语言Key，它指向XML语言文件中的某个具体的翻译项。第二个参数是可选的，用于指定具体的语言文化名称。通过这种方式，我们可以在代码的任何地方灵活地使用翻译文本，确保界面显示的内容与用户选择的语言相匹配。

## xaml 界面中的应用

在 xaml 界面中使用 XML 翻译文件也非常便捷。首先，需要引入相应的命名空间：

```xaml
xmlns:language="clr-namespace:Localization"
xmlns:markup="https://codewf.com"
```

其中，“markup” 为前面安装的辅助库命名空间，它提供了 “I18n” 标记扩展帮助类，用于在界面中绑定翻译文本；“language” 为 T4 文件生成的 C# 强类型语言 Key 关联类命名空间，通过它可以与 XML 语言文件的语言 Key 进行关联。

以下是在控件中使用翻译文本的示例：

```xaml
<Button Content="{markup:I18n {x:Static language:ChoiceLanguagesView.LanguageKey}}" />
```

在上述示例中，“Button” 控件的 “Content” 属性通过 “I18n” 标记扩展绑定到了 “ChoiceLanguagesView.LanguageKey” 对应的翻译文本上。这样，当界面语言发生变化时，按钮的显示文本也会自动更新为相应语言的翻译内容。

当然也支持指定语言：

```xaml
 <StackPanel>
     <StackPanel Orientation="Horizontal">
         <TextBlock Text="Current Thread" />
         <TextBlock Text="{markup:I18n {x:Static language:DevelopModule.Title2SlugView.Title}}"></TextBlock>
     </StackPanel>
     <StackPanel Orientation="Horizontal">
         <TextBlock Text="en-US" />
         <TextBlock Text="{markup:I18n {x:Static language:DevelopModule.Title2SlugView.Title}, CultureName=en-US}" />
     </StackPanel>
     <StackPanel Orientation="Horizontal">
         <TextBlock Text="ja-JP" />
         <TextBlock Text="{markup:I18n {x:Static language:DevelopModule.Title2SlugView.Title}, CultureName=ja-JP}" />
     </StackPanel>
     <StackPanel Orientation="Horizontal">
         <TextBlock Text="zh-CN" />
         <TextBlock Text="{markup:I18n {x:Static language:DevelopModule.Title2SlugView.Title}, CultureName=zh-CN}" />
     </StackPanel>
     <StackPanel Orientation="Horizontal">
         <TextBlock Text="zh-Hant" />
         <TextBlock Text="{markup:I18n {x:Static language:DevelopModule.Title2SlugView.Title}, CultureName=zh-Hant}" />
     </StackPanel>
 </StackPanel>
```

在这个示例中，我们创建了一个 `StackPanel` 布局，其中包含多个子 `StackPanel`，每个子 `StackPanel` 显示不同语言的翻译文本。通过在 `I18n` 标记扩展中指定 `CultureName` 属性，我们可以显示特定语言的翻译内容。

此外，xaml 界面还支持动态 Key 的绑定，例如：

```xaml
<u:Banner
    Content="{markup:I18n {Binding SelectedMenuItem.Description}}"
    Header="{markup:I18n {Binding SelectedMenuItem.Name}}"
    Type="{Binding SelectedType}" />
```

在这个示例中，“Banner” 控件的 “Content” 和 “Header” 属性分别绑定到了动态的 “SelectedMenuItem.Description” 和 “SelectedMenuItem.Name” 属性上，通过 “I18n” 标记扩展实现了动态翻译文本的显示。

## 总结

用法和Avalonia版本一致，下面是WPF版本源码，其中包括NuGet包源码和使用示例：

- [WPFXmlTranslator](https://github.com/dotnet9/WPFXmlTranslator)
- [WPF Prism模块应用示例](https://github.com/dotnet9/TerminalMACS.ManagerForWPF)

通过使用自定义XML文件和 `WPFXmlTranslator` 包，我们可以方便地实现WPF应用程序的国际化。这种方法不仅简单易用，而且具有很强的灵活性，可以满足不同应用场景的需求。希望本文对你有所帮助，让你的WPF应用能够走向更广阔的国际市场。