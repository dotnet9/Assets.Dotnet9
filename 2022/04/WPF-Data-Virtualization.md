---
title: WPF：数据虚拟化
slug: WPF-Data-Virtualization
description: 这篇文章不错，本来借助谷歌翻译，站长想再人工检查一遍，发现里面专业术语挺多的，个人英语也太渣，直接原文照搬了，希望你的英文可以的。
date: 2022-04-13 21:14:35
copyright: Reprinted
author: Paul McClean
originalTitle: WPF：数据虚拟化
originalLink: https://www.codeproject.com/Articles/34405/WPF-Data-Virtualization
draft: False
cover: https://img1.dotnet9.com/2022/04/cover_14.png
categories: .NET
tags: UI虚拟化,数据虚拟化
---

> 原文标题：WPF: Data Virtualization
>
> 原文链接：https://www.codeproject.com/Articles/34405/WPF-Data-Virtualization
>
> 原文作者：Paul McClean

> 这篇文章不错，本来借助谷歌翻译，站长想再人工检查一遍，发现里面专业术语挺多的，个人英语也太渣，直接原文照搬了，希望你的英文可以的。

![](https://img1.dotnet9.com/2022/04/1401.png)

以下为原文：

A collection class providing data virtualization with large data sets.

[Download source code - 15.7 KB](https://www.codeproject.com/KB/WPF/WpfDataVirtualization/datavirtualization.zip)

## Introduction

WPF provides some clever UI virtualization features for dealing efficiently with large collections, at least from a UI perspective. What isn’t provided is a generic method for achieving data virtualization. While several posts on internet forums discuss data virtualization, no one has (to my knowledge) published a solution. This article presents one such solution.

## Background

### UI Virtualization

When a WPF `ItemsControl` is bound to a large collection data source, with UI virtualization enabled, the control will only create visual containers for the items that are actually visible (plus a few above and below). This is typically only a small fraction of the entire collection. When the user scrolls, new visual containers are created as items become visible, and old containers are disposed when items are no longer visible. When container recycling is enabled, it will reuse visual containers instead of creating and disposing, avoiding the object instantiation and garbage collection overheads.

UI virtualization means that controls can be bound to large collections without incurring a large memory footprint due to visual containers. There is, however, a potentially large memory footprint due to the actual data objects in the collection.

### Data Virtualization

Data virtualization is a term that means achieving virtualization for the actual data objects that are bound to the `ItemsControl`. Data virtualization is not provided by WPF. For relatively small collections of basic data objects, the memory consumption is not significant; however, for large collections, the memory consumption can become very significant. In addition, actually retrieving the data (e.g., from a database) and instantiating all the objects could be time consuming, particularly if a network operation is involved. For these reasons, it is desirable to use some sort of data virtualization mechanism to limit the amount of data objects that need to be retrieved and instantiated in memory.

## Solution

### Overview

This solution makes use of the fact that when an `ItemsControl` is bound to an `IList` implementation, rather than an `IEnumerable` implementation, it will not enumerate the entire list, and instead only accesses the items required for display. It uses the `Count` property to determine the size of the collection, presumably to set the scroll extents. It will then iterate through the onscreen items using the list indexer. Thus, it is possible to create an `IList` that can report to have a large number of items, and yet only actually retrieve the items when required.

### IItemsProvider<T>

In order to utilize this solution, the underlying data source must be able to provide the number of items in the collection, and be able to provide small chunks (or pages) of the entire collection. This requirement is encapsulated in the `IItemsProvider` interface.

```csharp
/// <summary>
/// Represents a provider of collection details.
/// </summary>
/// <typeparam name="T">The type of items in the collection.</typeparam>
public interface IItemsProvider<T>
{
    /// <summary>
    /// Fetches the total number of items available.
    /// </summary>
    /// <returns></returns>
    int FetchCount();

    /// <summary>
    /// Fetches a range of items.
    /// </summary>
    /// <param name="startIndex">The start index.</param>
    /// <param name="count">The number of items to fetch.</param>
    /// <returns></returns>
    IList<T> FetchRange(int startIndex, int count);
}
```

If the underlying data source is a database query, it is relatively simple to implement the `IItemsProvider` interface using the `COUNT()` aggregate function, and the `OFFSET` and `LIMIT` expressions, provided by most database vendors.

### VirtualizingCollection<T>

This is the `IList` implementation that performs the data virtualization. The `VirtualizingCollection(T)` divides the entire collection space into a number of pages. Pages are then loaded into memory as required, and released when no longer required.

The interesting parts are discussed below. For all the details, please refer to the attached source code project.

The first aspect of the `IList` implementation is the implementation of the Count property. This is used to by the `ItemsControl` to gauge the size of the collection and render the scrollbar appropriately.

```csharp
private int _count = -1;

public virtual int Count
{
    get
    {
        if (_count == -1)
        {
            LoadCount();
        }
        return _count;
    }
    protected set
    {
        _count = value;
    }
}

protected virtual void LoadCount()
{
    Count = FetchCount();
}

protected int FetchCount()
{
    return ItemsProvider.FetchCount();
}
```

The `Count` property is implemented using the deferred or lazy loading pattern. It uses the special value of -1 to indicate that it has not loaded. The first time it is accessed, it will load the actual count from the `ItemsProvider`.

The other important aspect of the IList interface is the indexer implementation.

```csharp
public T this[int index]
{
    get
    {
        // determine which page and offset within page
        int pageIndex = index / PageSize;
        int pageOffset = index % PageSize;

        // request primary page
        RequestPage(pageIndex);

        // if accessing upper 50% then request next page
        if ( pageOffset > PageSize/2 && pageIndex < Count / PageSize)
            RequestPage(pageIndex + 1);

        // if accessing lower 50% then request prev page
        if (pageOffset < PageSize/2 && pageIndex > 0)
            RequestPage(pageIndex - 1);

        // remove stale pages
        CleanUpPages();

        // defensive check in case of async load
        if (_pages[pageIndex] == null)
            return default(T);

        // return requested item
        return _pages[pageIndex][pageOffset];
    }
    set { throw new NotSupportedException(); }
}
```

The indexer performs the clever bit of the solution. First, it must determine which page the requested item is in (`pageIndex`), and its offset (`pageOffset`) within that page. It then calls the `RequestPage()` method for the required page.

The additional step is then to load the next page or the previous page based upon the `pageOffset`. This is based upon the assumption that if the user is viewing page 0, there is a good chance they will scroll down to view page 1. Fetching it earlier results in no gap in the display.

`CleanUpPages()` is then called to clean up (or unload) any pages that are no longer in use.

Finally, a defensive check is in place in case the page is not yet available, which is necessary if `RequestPage` does not operate synchronously as in the case of the derived class `AsyncVirtualizingCollection<T>`.

```csharp
// ...

private readonly Dictionary<int, IList<T>> _pages =
        new Dictionary<int, IList<T>>();
private readonly Dictionary<int, DateTime> _pageTouchTimes =
        new Dictionary<int, DateTime>();

protected virtual void RequestPage(int pageIndex)
{
    if (!_pages.ContainsKey(pageIndex))
    {
        _pages.Add(pageIndex, null);
        _pageTouchTimes.Add(pageIndex, DateTime.Now);
        LoadPage(pageIndex);
    }
    else
    {
        _pageTouchTimes[pageIndex] = DateTime.Now;
    }
}

protected virtual void PopulatePage(int pageIndex, IList<T> page)
{
    if (_pages.ContainsKey(pageIndex))
        _pages[pageIndex] = page;
}

public void CleanUpPages()
{
    List<int> keys = new List<int>(_pageTouchTimes.Keys);
    foreach (int key in keys)
    {
        // page 0 is a special case, since the WPF ItemsControl
        // accesses the first item frequently
        if ( key != 0 && (DateTime.Now -
             _pageTouchTimes[key]).TotalMilliseconds > PageTimeout )
        {
            _pages.Remove(key);
            _pageTouchTimes.Remove(key);
        }
    }
}
```

The pages are stored in a `Dictionary`, where the page index is used as the key. An additional `Dictionary` is used to store touch times. Touch times record the time each page was last accessed. This is used by the `CleanUpPages()` method to remove pages that have not been accessed for a considerable amount of time.

```csharp
protected virtual void LoadPage(int pageIndex)
{
    PopulatePage(pageIndex, FetchPage(pageIndex));
}

protected IList<T> FetchPage(int pageIndex)
{
    return ItemsProvider.FetchRange(pageIndex*PageSize, PageSize);
}
```

To complete the solution, `FetchPage()` performs the fetch from the `ItemsProvider`, and the `LoadPage()` method does the work of getting the page call the `PopulatePage` method to store it in the `Dictionary`.

It may seem that there are a few too many inconsequential methods, but they have been designed in that way for a reason. Each method performs exactly one task. This helps to keep the code readable, and also makes it easier to extend or modify functionality in derived classes, as will be observed below.

The `VirtualizingCollection<T>` class achieves the primary objective to implement data virtualization. Unfortunately, when in use, this class has one severe drawback: the data fetch methods are all executed synchronously. This means they will be executed by the UI thread, resulting, potentially, in a sluggish application.

### AsyncVirtualizingCollection<T>

The `AsyncVirtualizingCollection<T>` class is derived from `VirtualizingCollection<T>`, and overrides the Load methods in order to perform the data loading asynchronously.

The key behind an asynchronous data source in WPF is that it must then notify the UI via data binding when the data has been fetched. In a regular object, this is usually achieved with the `INotifyPropertyChanged` interface. For a collection implementation, however, it is necessary to use its close relative, `INotifyCollectionChanged`. This is the interface used by `ObservableCollection<T>`.

```csharp
public event NotifyCollectionChangedEventHandler CollectionChanged;

protected virtual void OnCollectionChanged(NotifyCollectionChangedEventArgs e)
{
    NotifyCollectionChangedEventHandler h = CollectionChanged;
    if (h != null)
        h(this, e);
}

private void FireCollectionReset()
{
    NotifyCollectionChangedEventArgs e =
      new NotifyCollectionChangedEventArgs(NotifyCollectionChangedAction.Reset);
    OnCollectionChanged(e);
}

public event PropertyChangedEventHandler PropertyChanged;

protected virtual void OnPropertyChanged(PropertyChangedEventArgs e)
{
    PropertyChangedEventHandler h = PropertyChanged;
    if (h != null)
        h(this, e);
}

private void FirePropertyChanged(string propertyName)
{
    PropertyChangedEventArgs e = new PropertyChangedEventArgs(propertyName);
    OnPropertyChanged(e);
}
```

The `AsyncVirtualizingCollection<T>` implements both `INotifyCollectionChanged` and `INotifyPropertyChanged`, providing maximum data binding flexibility. There is nothing really of note in this implementation.

```csharp
protected override void LoadCount()
{
    Count = 0;
    IsLoading = true;
    ThreadPool.QueueUserWorkItem(LoadCountWork);
}

private void LoadCountWork(object args)
{
    int count = FetchCount();
    SynchronizationContext.Send(LoadCountCompleted, count);
}

private void LoadCountCompleted(object args)
{
    Count = (int)args;
    IsLoading = false;
    FireCollectionReset();
}
```

In the overridden `LoadCount()` method, the fetch is invoked asynchronously via the `ThreadPool`. Once completed, the new `Count` is set, and the `FireCollectionReset()` method is called to update the UI via the `INotifyCollectionChanged` interface. Note that the `LoadCountCompleted` method is invoked on the UI thread again, by using the `SynchronizationContext`. This `SynchronizationContext` property is set in the constructor, with the assumption that the collection instance will be created on the UI thread.

```csharp
protected override void LoadPage(int index)
{
    IsLoading = true;
    ThreadPool.QueueUserWorkItem(LoadPageWork, index);
}

private void LoadPageWork(object args)
{
    int pageIndex = (int)args;
    IList<T> page = FetchPage(pageIndex);
    SynchronizationContext.Send(LoadPageCompleted, new object[]{ pageIndex, page });
}

private void LoadPageCompleted(object args)
{
    int pageIndex = (int)((object[]) args)[0];
    IList<T> page = (IList<T>)((object[])args)[1];

    PopulatePage(pageIndex, page);
    IsLoading = false;
    FireCollectionReset();
}
```

The asynchronous load of the page data follows the same convention, and again the `FireCollectionReset()` method is used to update the UI.

Note also the `IsLoading` property. This is a simple flag, which can be used to indicate to the UI that the collection is loading. When the `IsLoading` property is changed, the `FirePropertyChanged()` method is called to update the UI via the `INotifyPropertyChanged` mechanism.

```csharp
public bool IsLoading
{
    get
    {
        return _isLoading;
    }
    set
    {
        if ( value != _isLoading )
        {
            _isLoading = value;
            FirePropertyChanged("IsLoading");
        }
    }
}
```

## Demo Project

In order to demonstrate this solution, I have created a simple demo project (included in the attached source code project).

Firstly, an implementation of `IItemsProvider` was created, which provides dummy data with a thread sleep used to simulate delays in fetching data due to network or disk activity.

```csharp
public class DemoCustomerProvider : IItemsProvider<Customer>
{
    private readonly int _count;
    private readonly int _fetchDelay;

    public DemoCustomerProvider(int count, int fetchDelay)
    {
        _count = count;
        _fetchDelay = fetchDelay;
    }

    public int FetchCount()
    {
        Thread.Sleep(_fetchDelay);
        return _count;
    }

    public IList<Customer> FetchRange(int startIndex, int count)
    {
        Thread.Sleep(_fetchDelay);

        List<Customer> list = new List<Customer>();
        for( int i=startIndex; i<startIndex+count; i++ )
        {
            Customer customer = new Customer {Id = i+1, Name = "Customer " + (i+1)};
            list.Add(customer);
        }
        return list;
    }
}
```

The ubiquitous `Customer` object is used as the items in the collection.

A simple WPF window with a `ListView` was created to allow the user to experiment the different list implementations.

```XML
<Window x:Class="DataVirtualization.DemoWindow"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
    Title="Data Virtualization Demo - By Paul McClean" Height="600" Width="600">

    <Window.Resources>
        <Style x:Key="lvStyle" TargetType="{x:Type ListView}">
            <Setter Property="VirtualizingStackPanel.IsVirtualizing" Value="True"/>
            <Setter Property="VirtualizingStackPanel.VirtualizationMode" Value="Recycling"/>
            <Setter Property="ScrollViewer.IsDeferredScrollingEnabled" Value="True"/>
            <Setter Property="ListView.ItemsSource" Value="{Binding}"/>
            <Setter Property="ListView.View">
                <Setter.Value>
                    <GridView>
                        <GridViewColumn Header="Id" Width="100">
                            <GridViewColumn.CellTemplate>
                                <DataTemplate>
                                    <TextBlock Text="{Binding Id}"/>
                                </DataTemplate>
                            </GridViewColumn.CellTemplate>
                        </GridViewColumn>
                        <GridViewColumn Header="Name" Width="150">
                            <GridViewColumn.CellTemplate>
                                <DataTemplate>
                                    <TextBlock Text="{Binding Name}"/>
                                </DataTemplate>
                            </GridViewColumn.CellTemplate>
                        </GridViewColumn>
                    </GridView>
                </Setter.Value>
            </Setter>
            <Style.Triggers>
                <DataTrigger Binding="{Binding IsLoading}" Value="True">
                    <Setter Property="ListView.Cursor" Value="Wait"/>
                    <Setter Property="ListView.Background" Value="LightGray"/>
                </DataTrigger>
            </Style.Triggers>
        </Style>
    </Window.Resources>

    <Grid Margin="5">

        <Grid.RowDefinitions>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="Auto"/>
            <RowDefinition Height="*"/>
        </Grid.RowDefinitions>


        <GroupBox Grid.Row="0" Header="ItemsProvider">
            <StackPanel Orientation="Horizontal" Margin="0,2,0,0">
                <TextBlock Text="Number of items:" Margin="5"
                  TextAlignment="Right" VerticalAlignment="Center"/>
                <TextBox x:Name="tbNumItems" Margin="5"
                  Text="1000000" Width="60" VerticalAlignment="Center"/>
                <TextBlock Text="Fetch Delay (ms):" Margin="5"
                  TextAlignment="Right" VerticalAlignment="Center"/>
                <TextBox x:Name="tbFetchDelay" Margin="5"
                  Text="1000" Width="60" VerticalAlignment="Center"/>
            </StackPanel>
        </GroupBox>

        <GroupBox Grid.Row="1" Header="Collection">
            <StackPanel>
                <StackPanel Orientation="Horizontal" Margin="0,2,0,0">
                    <TextBlock Text="Type:" Margin="5"
                      TextAlignment="Right" VerticalAlignment="Center"/>
                    <RadioButton x:Name="rbNormal" GroupName="rbGroup"
                      Margin="5" Content="List(T)" VerticalAlignment="Center"/>
                    <RadioButton x:Name="rbVirtualizing" GroupName="rbGroup"
                      Margin="5" Content="VirtualizingList(T)"
                      VerticalAlignment="Center"/>
                    <RadioButton x:Name="rbAsync" GroupName="rbGroup"
                      Margin="5" Content="AsyncVirtualizingList(T)"
                      IsChecked="True" VerticalAlignment="Center"/>
                </StackPanel>
                <StackPanel Orientation="Horizontal" Margin="0,2,0,0">
                    <TextBlock Text="Page size:" Margin="5"
                      TextAlignment="Right" VerticalAlignment="Center"/>
                    <TextBox x:Name="tbPageSize" Margin="5"
                      Text="100" Width="60" VerticalAlignment="Center"/>
                    <TextBlock Text="Page timeout (s):" Margin="5"
                      TextAlignment="Right" VerticalAlignment="Center"/>
                    <TextBox x:Name="tbPageTimeout" Margin="5"
                      Text="30" Width="60" VerticalAlignment="Center"/>
                </StackPanel>
             </StackPanel>
        </GroupBox>

        <StackPanel Orientation="Horizontal" Grid.Row="2">
            <TextBlock Text="Memory Usage:" Margin="5"
              VerticalAlignment="Center"/>
            <TextBlock x:Name="tbMemory" Margin="5"
              Width="80" VerticalAlignment="Center"/>

            <Button Content="Refresh" Click="Button_Click"
              Margin="5" Width="100" VerticalAlignment="Center"/>

            <Rectangle Name="rectangle" Width="20" Height="20"
                     Fill="Blue" Margin="5" VerticalAlignment="Center">
                <Rectangle.RenderTransform>
                    <RotateTransform Angle="0" CenterX="10" CenterY="10"/>
                </Rectangle.RenderTransform>
                <Rectangle.Triggers>
                    <EventTrigger RoutedEvent="Rectangle.Loaded">
                        <BeginStoryboard>
                            <Storyboard>
                                <DoubleAnimation Storyboard.TargetName="rectangle"
                                   Storyboard.TargetProperty=
                                     "(TextBlock.RenderTransform).(RotateTransform.Angle)"
                                   From="0" To="360" Duration="0:0:5"
                                   RepeatBehavior="Forever" />
                            </Storyboard>
                        </BeginStoryboard>
                    </EventTrigger>
                </Rectangle.Triggers>
            </Rectangle>

            <TextBlock Margin="5" VerticalAlignment="Center"
              FontStyle="Italic" Text="Pause in animation indicates UI thread stalled."/>

        </StackPanel>

        <ListView Grid.Row="3" Margin="5" Style="{DynamicResource lvStyle}"/>

    </Grid>
</Window>
```

It is not necessary to go into details on the XAML. The only point to notice is the use of a custom ListView style to change the background and mouse cursor in response to the `IsLoading` property.

```csharp
public partial class DemoWindow
{
    /// <summary>
    /// Initializes a new instance of the <see cref="DemoWindow"/> class.
    /// </summary>
    public DemoWindow()
    {
        InitializeComponent();

        // use a timer to periodically update the memory usage
        DispatcherTimer timer = new DispatcherTimer();
        timer.Interval = new TimeSpan(0, 0, 1);
        timer.Tick += timer_Tick;
        timer.Start();
    }

    private void timer_Tick(object sender, EventArgs e)
    {
        tbMemory.Text = string.Format("{0:0.00} MB",
                             GC.GetTotalMemory(true)/1024.0/1024.0);
    }

    private void Button_Click(object sender, RoutedEventArgs e)
    {
        // create the demo items provider according to specified parameters
        int numItems = int.Parse(tbNumItems.Text);
        int fetchDelay = int.Parse(tbFetchDelay.Text);
        DemoCustomerProvider customerProvider =
                       new DemoCustomerProvider(numItems, fetchDelay);

        // create the collection according to specified parameters
        int pageSize = int.Parse(tbPageSize.Text);
        int pageTimeout = int.Parse(tbPageTimeout.Text);

        if ( rbNormal.IsChecked.Value )
        {
            DataContext = new List<Customer>(customerProvider.FetchRange(0,
                                                   customerProvider.FetchCount()));
        }
        else if ( rbVirtualizing.IsChecked.Value )
        {
            DataContext = new VirtualizingCollection<Customer>(customerProvider, pageSize);
        }
        else if ( rbAsync.IsChecked.Value )
        {
            DataContext = new AsyncVirtualizingCollection<Customer>(customerProvider,
                              pageSize, pageTimeout*1000);
        }
    }
}
```

The window layout is pretty basic, but sufficient to demonstrate the solution.

The user can configure the number of items in the `DemoCustomerProvider`, and the simulated fetch delay.

The demo allows the user to compare a standard `List(T)` implementation to the `VirtualizingCollection(T)` and `AsyncVirtualizingCollection(T)` implementations. With the `VirtualizingCollection(T)` and `AsyncVirtualizingCollection(T)`, the user can specify the page size and page timeout. These should be chosen to suit the characteristics of the control and the expected usage patterns.

![WpfDataVirtualization/screenshot.png](https://img1.dotnet9.com/2022/04/1402.png)

The total (managed) memory usage is shown to offer comparisons in the memory footprint in the different IList implementations. The rotating square animation is used to indicate stalls on the UI thread. In a fully asynchronous solution, the animation should not stutter or pause.

## Points of Interest

Incidentally, during the course of creating this solution, I discovered that the implementation must implement the `IList` interface (rather than the generic `IList<T>` interface). This is in contradiction to the current MSDN documentation (link). However, it is good practice to implement both the `IList` and `IList<T>` interfaces in any generic list implementation.

In practice, it would appear that the `ItemsControl` binding will also invoke the `IndexOf()` method. I can’t explain why this is required, and obviously, if a correct implementation were required, this solution would not be possible. Fortunately, it was found that simply returning –1 from the `IndexOf()` implementation was sufficient.

## Known Issues and Future Extensions

- The above solution assumes that the source collection is readonly and does not change. Ideally, the solution should periodically (or on demand) re-load the Count and pages.
- The `IItemsProvider` interface could be extended to provide support for editing and sorting.

## Final Remarks

This is my first article on CodeProject. Having lurked in the shadows for several years, it was finally time to come out into the open. I hope you find this article useful, and I would appreciate any comments or suggestions. If you find any errors, please accept my apologies and leave a comment; I will endeavor to fix any errors promptly.

## Update

Since I first published this article, [Bea Stollnitz](http://www.zagstudio.com/blog/498#.UVSu2KUbBcA) has published a much more comprehensive and complete solution to data virtualization. I would refer readers to her [solution](http://www.zagstudio.com/blog/498#.UVSu2KUbBcA).

I am greatly encouraged by all the positive comments this article received and would like to thank all those who read it, commented on it or voted. I hope people will continue to find this article and example code useful. As such I would like to remove the license on the source code and place it in the public domain. This means anyone may use it in any application, but without any warranty.

## History

- 23 March 2009 - First submission.
- 28 March 2011 - Minor update and change of license

## License

This article, along with any associated source code and files, is licensed under [A Public Domain dedication](http://creativecommons.org/licenses/publicdomain/)
