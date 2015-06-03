# iOS8 今日扩展
---

>* 原文链接 : [iOS 8: Creating a Today Widget](http://code.tutsplus.com/tutorials/ios-8-creating-a-today-widget--cms-22379)
* 译者 : [Harries Chen](https://github.com/mrchenhao) 
* 校对者: [Mr.Simple](https://github.com/bboyfeiyu)  
* 状态 :  校对中

创建扩展是iOS8升级后的一个重要的特性，在本次介绍中，我将通过创建一个自定义的今日组件在通知中心来讲解。首先我们短暂的来了解一下有关扩展来明白组件这个重要的概念。

#1.扩展是什么？

扩展是一个特殊的程序。但是它并不属于一个完整的APP，它需要有一个容器APP（containing app）来进行发布。容器可以是一个已经存在的APP，也可以创建一个新的。一个容器可以有一个或一个以上的扩展。扩展不能独立的进行发布，它需要有一个容器。

扩展被它所属的宿主程序（host app）所加载和控制。举个例子，它可以像Safari一样有分享扩张，或者像通知中心的今日摘要和其他组件一样。系统的每一个支持扩展的地方叫做扩展插入点。

为了创建扩展，你需要添加一个target到容器（cotaining app）的工程文件之中。Xcode提供的模板已经包括扩展接入点所需要的框架，

#2.今日扩展接入点

扩展提供了今日扩展接入点，就是所谓的组件，可以提供简单快捷的访问信息。组件关联到通知中心。设计一个简单易用的的组件是非常重要的，因为太多的交互可能会导致用户的操作困难。要注意千万不要使用键盘。

我们都希望组件能够有好看的界面和及时更新，保证它稳定的工作尤为重要，你需要让你的组件又快又节省资源。避免影响这个用户体验，系统将终止组件的运行如果组件占用了太多内存。组件需要的是简单，专注于显示他们需要显示的内容。

理论说够了，让我们来创建一个自己的今日组件，我们用它来显示磁盘的用量，通过一个进度条来提示用户。在这个过程中，我们也会涉及其他的扩展。

#3.实现步骤

##步骤1：创建项目

如果你想要创建一个扩展作为一个已经存在的app，打开Xcode工程，然后到步骤2。如果你像我一样从零开始，你需要先创建一个容器程序（containing app）。

打开Xcode在File目录选择`New > Project...` 我们使用`objective-C`作为开发语言，选择 `Single View Application` 模板。

![这里写图片描述](http://img.blog.csdn.net/20150505211111854)

##步骤2：添加一个target

打开 `File` 目录，选择 `New > Target....` 在 `Application Extension` 的类别中选择 `Today Extension` 模板。

![这里写图片描述](http://img.blog.csdn.net/20150505211012076)

你会注意到target被添加到我们当前的工程，扩展将会被嵌入到容器程序。然后扩展会有一个标识符类似容器程序 `com.tutsplus.Today.Used-Space`.

![这里写图片描述](http://img.blog.csdn.net/20150505211128844)

点击 `Next`, 给组件取个名字,例如`Used Space`, 点击 `Finish` 创建一个`target..`,Xcode将为你创建一个新的scheme，然后询问你是否激活，点击`Activate`继续。

![这里写图片描述](http://img.blog.csdn.net/20150505211405506)

Xcode将为组件创建一个名叫`Space Used`的分组 然后在其中生成一些文件，一个`UIViewController`和一个`storyboard`，组件就只是一个`UIViewController`和一个`storyboard`，如果你打开视图控制器的头文件，你会发现它就是一个普通的`UIViewController`的子类。

如果你选择扩展的target，打开 `Build Phases`并展开`Link Binary With Libraries`，你会发现它添加了 `Notification Centre`得框架。

![这里写图片描述](http://img.blog.csdn.net/20150505211245628)


#4.用户界面

我们现在来创建一个基本的用户界面，首先需要确定组件的大小，有两种方式来告诉系统我们所需的大小。第一是是采用自动布局（Auto Layout），第二是使用`viewcontroller`的`preferredContentSize`属性。

自动布局的概念在组件上依然是可用的，不仅需要注意iPhone的各种宽度（以及iPad和未来的设备）也需要注意它可能会在横屏模式下现实。如果界面是用的是自动布局来约束，对开发者来说是非常方便的。调节组件的高度可以通过`setPreferredContentSize`来实现。

##步骤1：添加元素

在Xcode编辑器中打开 `MainInterface.storyboard`. 你会发现已经有一个HelloewWorld的label已经在视图中了，选中并删除它，因为我们不会用到，添加一个新的label并且让它靠右对齐像图中那样。

![这里写图片描述](http://img.blog.csdn.net/20150505211321445)

在 `Attributes Inspector` 中设置颜色为白色, 字体对齐方式为靠右对齐, 文本占 50.0%。

![这里写图片描述](http://img.blog.csdn.net/20150505211554018)

在编辑器中选择 `Size to Fit Content` 来让label自动调节大小。

接着添加一个`UIProgressView`对象在label的左边位置，如图所示。

![这里写图片描述](http://img.blog.csdn.net/20150505211623066)

选中进度条，在`Attributes Inspector`改变 `Progress Tint `为白色, 改变`Track Tint` 为深褐色.这将会使得进度条更加明显，接下来添加一些约束。

##步骤2：添加约束

选中label添加如图所示的上下约束。不要勾选`Constrain to margins` 的选项。

![这里写图片描述](http://img.blog.csdn.net/20150505211458134)


选择进度条添加如图所示的上左右的约束，同时不要忘记反选` Constrain to margins`。

![这里写图片描述](http://img.blog.csdn.net/20150505211735276)

因为我们改变了约束，所以我们会有一个小问题需要解决，现在的进度条大小并不能正确的反应约束后的进度条大小，选择进度条， 点击 `Resolve Auto Layout Issues` 按钮，选择 `Update Frames` 选择 `Selected Views`.这将会根据约束来更新进度条的大小。

![这里写图片描述](http://img.blog.csdn.net/20150505230954758)

##步骤3：构建与运行


是时候来看一下我们的组件了，选`择Used Space schem`e，选择`Prodect`目录中得`Run`或者直接按下 `Command-R`，下拉显示通知中心，然后点击下方的`edit`按钮，你的今日组件将会出现在可添加的地方，点击它左边的添加按钮。

![这里写图片描述](http://img.blog.csdn.net/20150505231028449)

这就是我们的扩展的样子。

![这里写图片描述](http://img.blog.csdn.net/20150505231126645)

看起来不错，但是为什么在下面有那么多空隙呢？为什么操作系统没有遵循进度条的约束呢？

这个问题都是操作系统的标准间距导致的，我们接下来将会改变它，请注意，无论如何，保证进度条的左边距与名字对齐的。

如果你选择你的设备或者在其它设备运行，你会发现组件会调整大小，这得益于我们使用了自动布局（ Auto Layout）。

##步骤4：修正按钮边距

打开 `TodayViewController.m`. 可以看到控制器实现了`NCWidgetProviding`协议. 这意味着我们需要实现`widgetMarginInsetsForProposedMarginInsets：`方法来返回一个自定义的边距(`UIEdgeInsets`结构)，修改方法如下。

再次运行程序查看结果，发现组件的下边距变小了，你可以定制这些边距达到任意你想要的效果。

![这里写图片描述](http://img.blog.csdn.net/20150505231004344)

##步骤5：连接Outlets

最后，我们来添加两个Outlets，打开`storyboard`，切换到双编辑器模式，确保现实`TodayViewController.m`。

按住`Control`并拖动label到`viewcontroller`的接口部分来创建label的接口，命名为`percentLabel`，重复这个过程为`UIProgressView`创建`barView`。

![这里写图片描述](http://img.blog.csdn.net/20150505231035076)

# 5.显示真实数据

我们将使用 `NSFileManageer`类来计算设备的可用空间，那么我们改如何更新组件的数据呢？

这就就是`NCWidgetProviding`协议的另外一个方法。操作系统将调用`widgetPerformUpdateWithCompletionHandler: `方法当组件被载入的时候或者从后台进入的时候。即使组件不可见，系统还是有可能载入和更新它并保存快照，快照将在组件下次出现的时候显示，这是因为在组件显示之前会有一小段的时间。

通过处理完成句柄来判断是否需要调用新的内容或数据更新。block有一个参数NCUpdateResult来描述是否我们要更新内容。如果不是的话，操作系统会知道没有必要保存一个新的快照。

##步骤1：定义属性

我们需要先定义一些属性来保存剩余、已使用和总得空间，把这些属性添加到TodayViewController.m。

##步骤2：实现updateSizes

创建一个辅助方法`updateSizes`来拉取必要的数据和计算设备的空间使用率。

```
- (void)updateSizes
{
    // Retrieve the attributes from NSFileManager
    NSDictionary *dict = [[NSFileManager defaultManager]
                attributesOfFileSystemForPath:NSHomeDirectory()
                                        error:nil];
 
    // Set the values
    self.fileSystemSize = [[dict valueForKey:NSFileSystemSize]
                              unsignedLongLongValue];
    self.freeSize       = [[dict valueForKey:NSFileSystemFreeSize]
                              unsignedLongLongValue];
    self.usedSize       = self.fileSystemSize - self.freeSize;
}
```


##步骤3：存储

我们可以方便的使用`NSUserDefaults`来存储数据。组件的生命周期很短，所以我们需要缓存这个值，我们可以给界面设置一个初始的值，然后再计算真实的值。

这对我们是否要更新组件的快照是非常有帮助的，让我们来创建一个快捷方法来访问`NSUserdefaults`。

注意我们使用了一个宏定义`RATE_KEY`,别忘了把它加入`TodayViewController.m`。

##步骤4：更新界面

因为我们的组件是一个视图控制器，`viewDidLoad`方法很适合用来更新用户界面，我们创建一个帮助方法`updateInterface`来更新界面。

##步骤5：调用完成句柄

可用空间的变化导致跟新的过于频繁，为了判断我们是否真的需要刷新界面，我们计算已使用量并且设置了一个更新的下限0.1%，而不是一改变就刷新界面。修改
`widgetPerformUpdateWithCompletionHandler: `如下：

```
- (void)widgetPerformUpdateWithCompletionHandler:(void (^)(NCUpdateResult))completionHandler
{
     
    [self updateSizes];
     
    double newRate = (double)self.usedSize / (double)self.fileSystemSize;
     
    if (newRate - self.usedRate < 0.0001) {
        completionHandler(NCUpdateResultNoData);
    } else {
        [self setUsedRate:newRate];
        [self updateInterface];
        completionHandler(NCUpdateResultNewData);
    }
}
```

在我们每次重新计算之后，如果与上次有明显的变化，则保存值并更新界面，告诉操作系统。如果没有明显的变化，我们就不需要新的快照。如果有错误发生将会在`NCUpdateResultFailed `来报告错误的发生，但是在这个例子中没有出现错误。

##步骤6：构建与运行

再一次运行你的程序，它将正确的显示您已经使用了多少空间。

#6 总结

我们来回顾一下组件的生命周期，当我们打开今日面板，系统可能会显示上次的快照知道准备完成。界面会被加载，组件将取出缓存在NSUserDefaults的来更新用户界面。

接着`widgetPerformUpdateWithCompletionHandler:`会被调用，重新计算真实的值，如果缓存的值和真实值相近，不会发生任何事情，如果存在不同，那么新的值会被缓存界面会被刷新。


在后台的时候，组件可能会被系统调用，如果返回`NCUpdateResultNewData`，新的快照就会被创建为了下次的出现。

#7添加更多得信息和动画

虽然我们已经完成了显示使用空间的功能，它会有一个精确的数字。为了避免复杂的用户界面，使交互更加友好。如果点击百分比标签，组件将额外显示一个新的标签。这也是一个很好的机会去学习如何在组件中使用动画。

##步骤1:改变用户界面

打开`MainInterface.storyboard`选择label，在`In the Attributes Inspector`,在`View`选项下，找到`User Interaction Enabled`选项并启用它。

接下来，我们需要去除label的底部约束。label视图的底部的距离会改变，这意味着约束将成为无效。

选择label，在`Size Inspector`展开'Size'，选择底部的空间约束，并点击删除。你也可以手动选择视图中的约束引导并删除它。label现在只有顶部的约束，如下图所示。

![这里写图片描述](http://img.blog.csdn.net/20150505231328699)

通过点击画面上方的三个图标的第一个选择视图控制器。在`Size Inspector`的`Size`中，将高度设置为`106`。

![这里写图片描述](http://img.blog.csdn.net/20150505231355775)

添加一个新的label，，在`Attributes Inspector`设置其颜色为白色。此外，设置行数`3`，高度`61`，宽度`200`。这应该足以容纳三条信息。让它保存靠左和靠下对齐。

![这里写图片描述](http://img.blog.csdn.net/20150505231418568)

最后一步是打开助理编辑和创建名为detailslabel的outlet。

##步骤2：安装


控件将只会在一瞬间扩大。我们可以在`NSUserDefaults `保存一个布尔变量来记住上一次的状态，但是，为了简单，每次组件加载它时将关闭。当点击百分比label的时候，额外的信息将会出现。

让我们首先定义两个宏在`TodayViewController.m `来帮助我们设置大小。
在viewDidLoad，添加两行代码来设置控件的初始高度，让label透明。我们将在百分比label被点击的时候使详细label出现。

请注意，我们设置控件的宽度为0，因为宽度将由操作系统决定。

##更新详情标签

在详情label中，我们使用NSByteCountFormatter来展示可用、已使用和总的值。添加以下代码的视图控制器。

```
-(void)updateDetailsLabel
{
    NSByteCountFormatter *formatter =
                           [[NSByteCountFormatter alloc] init];
    [formatter setCountStyle:NSByteCountFormatterCountStyleFile];
 
    self.detailsLabel.text =
      [NSString stringWithFormat:
          @"Used:\t%@\nFree:\t%@\nTotal:\t%@",
           [formatter stringFromByteCount:self.usedSize],
           [formatter stringFromByteCount:self.freeSize],
           [formatter stringFromByteCount:self.fileSystemSize]];
}

```

##步骤4：检测点击事件

为了检测点击事件，我们在试图中重写了`touchesBegan:withEvent:`方法。想法很简单，当检测到触摸事件，展开详情标签并更新。请注意，插件的大小的是在`callingsetPreferredContentSize`更新的。

##步骤5：添加动画

虽然这个组件运行的不错,我们还是可以通过在收展详情标签使用渐变效果来提升用户体验，这个可以通过实现`viewWillTransitionToSize:withTransitionCoordinator:`来完成。这个方法会在组件的高度变化的时候被调用。因为这是通过渐变协调对象来完成，所以我们可以添加一些额外的动画。

你可以看到的，我们改变了标签的alpha值，但是你可以添加任何类型的动画来增强的用户体验。

##步骤6：构建与运行

我们再一次运行程序。通过点击百分比label来现实详情label。

![这里写图片描述](http://img.blog.csdn.net/20150505231256241)

#结论

这些逻辑似乎过于复杂对于这样一个简单的任务，但是你将熟悉创建今日扩展的的全过程。记住这些原则在设计和开发你的插件的时候。记得要保持它的简单和明了，同时也别忘了性能。

缓存在一些快速操作也许不需要，但你要做耗时的处理它就变得尤为重要。用你的视图控制器的知识来检查它是否能够在各种屏幕尺寸下工作。建议你避免滚动视图或复杂的触摸识别。

虽然扩展将有一个单独的容器，正如我们前面看到的，它可以使应用程序和包含扩展之间的数据共享。你也可以使用`NSExtensionContext `的`OpenURL：completionhandler：`通过一个自定义的URL scheme来快速启动你的程序从组件。如果你需要分享你的延伸，创建一个框架来使用您的应用程序和扩展。

我希望这里介绍的知识是有用的，帮助创建你的下一个伟大的今天扩展。