#拱拱UWP开发（）：UI_刷新与加载

由于我们的App需要兼顾多设备，所以刷新与加载方式需要考虑到不同设备的交互方式。其中，在移动设备上用户会更倾向于通过手势进行刷新操作，而在PC端则需要以直观的方式显示更新功能。
##（1）下拉更新
下拉刷新是一种自然的交互方式，在任何支持触屏的应用中我们最好支持这一特性来给用户带来最好的交互体验。
[下拉刷新]（）
为了实现这个功能，我简单地引用了“PullToRefresh”Nuget包，现在然我们来看看它是如何实现的：
```XML

```
我们可以看到在控件最外部采用了一个ScrollerViewer，

```C#

```
##(2)滚动刷新
滚动刷新的方式可以带给用户不间断的浏览体验，当用户滚动至列表最底处时，就可以后台直接触发更新然后动态添加至列表。
```XML  

```
实现的方法十分简单：我们只需要响应ListView的ViewScrolled事件然后根据目前所处位置来决定是否刷新。

```C#

```








As UWP is typically designed for multi-devices, we need to focus on various kinds of inputs from a finger touch to a mouse click. In touch supported devices, customs prefer refresh by guesture,however, they need to click button to refresh when in PC.
##(1)Pull to refresh
Pull-to-refresh is a nature way to interact with app, in any device equiped with touchable screen, we'd better to add this feature to our app. The final appearance will be as follow:
[pull-to-refresh]()

To do this, I simplily used a nuget package named "PullToRefresh", as it is open source, let's just take a look inside it:
```XML

```
Here from the XAML code, we can see that the outside of the control is ScrollViewer and inside the ScrollViewer is 


```C#

```

##(2)Scroll to refresh
It will provide customs uninterrupted experience by adding scroll to refresh feature as they viewing the list of news. And it is really simple to acheve this, here is the code:
```XML

```
 As most of us will choose ListView to show a list of data, here I put ListView inside the ScrollViewer, notice that when I do this: the ScrollerViwer inside the ListView will be disabled automatically,

