为watchOS 2而生的WatchKit: 初印象
---

> * 原文链接 : [WatchKit for watchOS 2: Initial Impressions](http://www.raywenderlich.com/108415/watchkit-for-watchos-2)
* 原文作者 : [Mic Pringle](http://www.raywenderlich.com/u/micpringle)
* 译文出自 :  [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [StormXX](https://github.com/StormXX) 
* 校对者: [Mr.Simple](https://github.com/bboyfeiyu) [Harries Chen](https://github.com/mrchenhao)
* 状态 : 已完成

和Ray一样，我也没有去今年的WWDC现场，但是我仍然花了一些时间来看WWDC的视频，读文档，并且和Jake,Nate还有Tutorial团队的人们讨论这些新闻。

你可能从播客里知道，我是一个狂热的Apple Watch粉丝。所以我对watchOS 2里的新东西感到异常的兴奋！

我在过去的几天里看完了新的Apple Watch文档，所以你可以在这篇文章里能非常快的浏览在新的watchOS 2中有什么新玩意~

![WatchKit for WatchOS 2](http://cdn4.raywenderlich.com/wp-content/uploads/2015/06/WatchKit_01.jpg)

***WatchKit for WatchOS 2***

请在下面的评论区说出你自己的想法，当然，你也可以提一些我没有提到的东西啦~

## 架构

或许在这次的发行版里，对于WatchKit最大的改变就是可以构建本地的专属手表的独立app了。

![Wait, did he just say native?](http://cdn2.raywenderlich.com/wp-content/uploads/2015/06/Spitting.png)

***Wait, did he just say native?***

不像watch的上一个版本--界面运行在手表上但是代码却在你的iPhone上被调用。在watchOS 2里，两者都在watch上被存储和调用。

这样就会产生一些积极的影响，比如说让你的app启动的更快，并且操作能够很快的得到响应。因为当交互发生的时候，手表再也不需要通过蓝牙来和你的手机通信。

对于手机上的app来说，下面的这些改变你也必须要了解一下：

- `openParentApplication(_:reply:)` 已经从SDK中删除了。但是不要担心，它被更丰富的[Watch Connectivity](https://developer.apple.com/library/prerelease/watchos/documentation/WatchConnectivity/Reference/WatchConnectivity_framework/index.html#//apple_ref/doc/uid/TP40015269)框架代替了。点击链接了解更多:)
- 通过app groups来共享文件不再是一个选项。又一次，你可以使用新的 Watch Connectivity框架来使它更容易。但是在watchOS 2中，苹果提供了对原始手表app的网络请求的支持。所以，如果你以前是使用app groups来共享从API抓取的数据，现在你可以直接在手表上使用NSURLSession
- iCloud在Watch上还是不能用！

## 手表连接

通过Watch Connectivity框架(framework)，你可以在手表和它配对的iPhone之间传送文件或者数据。如果两个app都运行在前台，那么这个连接就是活动的，否则，它将会运行在后台线程。所以，当接收的app启动的时候，传送来的数据或者文件才能被使用。

在新的框架中，比较重要的是`WCSession`类，它控制着两个设备间（手表和手机）的通信。你不需要创建这个类的实例，因为你可以使用`defaultSession`来获取它的单例。

你最好分配一个继承了`WCSessionDlegate`协议的对象，并且把它赋值给session的delegate属性，然后调用`activateSession()`。如果你计划频繁的使用Watch Connectivity，我强烈推荐你今早在你的app lifecycle代码中做上面的步骤。

一旦你把你的session配置并激活了，你可以用下列方法来转换你的数据和文件：

- `updateApplicationContext(_:error)`这个方法被用来传递一个Dictionary的数据给配对的app。但是，它不会马上生效，只有当配对的app启动或者是到前台的时候，这个传递的数据才能被利用。配对的app接收到这个Dictionary通过它的session代理(delegate)`session(_:didReceiveUpdate:)`方法。
- `sendMessageData(_:replyHandler:errorHandler:)`被用来立即传送数据给配对的app。对这个方法的调用都会存在一个队列里，然后被调用的顺序就是你调用它们的顺序。如果你从watch传送数据到iOS app，那么如果这个app不在运行中，它就会在后台被唤醒。如果你从iOS app发送数据到watch，当watch上的这个app不在运行的时候，就会调用error的这个闭包。配对的app接受数据通过session的代理(delegate)`session(_:didReceiveMessageData:replyHandler:)`方法。
- `transferFile(_:metadata:)`用来在后台传递文件，比如说图片。这个方法和前面两个方法一样，唤醒iOS app如果它不在运行的话，或者是调用error闭包如果watch app不在运行的话。你们需要实现的代理方法是`session(_:didReceiveFile:)`，它用来接受文件。

这个框架提供了很多的其他的方法，所以我强烈推荐你们看这份[指南](https://developer.apple.com/library/prerelease/watchos/documentation/WatchConnectivity/Reference/WatchConnectivity_framework/index.html#//apple_ref/doc/uid/TP40015269)

看吧，我已经告诉过你们不需要去担心失去了`openParentApplication(_:reply:)`的。:)

## 一些其他的WatchKit新东西

我已经讲了新的WatchKit中的两个最大的改变的细节，但是仍然有一些其他的东西我认为值得一提。

### 动画

WatchKit仍然保留了以前的动画方法--用预先设置好的一系列图像来做循环。**WKInterfaceController**现在提供了一些基础的动画支持，类似于iOS，通过这个新的 `animateWithDuration(_:animations:)`方法。
这个需要一个duration和一个block作为参数，然后在这个block中并且在duration时间内，对界面对象做出一些动画。例如：

```
animateWithDuration(1) { () -> Void in 
  self.label.setHorizontalAlignment(.Left)
  self.label.setAlpha(0)
}
```

你也可以对下列的**WKInterfaceObject**的属性进行动画操作。

- alpha（透明度）
- width and height（宽和高）
- vertical and horizontal alignment（垂直和水平对齐）
- background color（背景色）
- layout group insets（group的间距）

>注意：glance和自定义通知不支持动画，还有，许多你在动画方法的block中做出的改变将会立即生效而不会有动画效果。

我觉得这看起来有点初级，因为没有办法去提供一个表示时间的曲线，或者是不知道什么时候动画已经完成了。但是我确信你也会觉得Apple在对的方向走了一大步，而且我也希望Apple在将来提供这些特性。

### 更多的操作硬件的API

watchOS 2的WatchKit提供了更多的硬件和传感器接口。包括Digital Crown,Taptic Engine,心率传感器，重力加速器还有麦克风。

这些接口为你们的app打开了新世界的大门！比如说，能够通过心率传感器精确的检测心跳然后用在你的健康app里，或者是使用Taptic Engine来提供一个更加真实的游戏体验。我是真的等不及看到你们能用这些东西创造些什么了！！

### 新的界面元素

在最新的WatchKit中，Apple新加了一个控件：**WKInterfacePicker**。它提供了一个可以滚动的列表，你通过Digital Crown来和它进行交互。

picker中的item可以是文字，图片，或者是两者的混合。当用户选择了一个item，Watchkit调用picker的事件方法来传递这个item的索引。

一个picker控件有下列三种不同的方式来展示：

- List：这个方式是用来展示竖直的像栈一样的列表。
- Stacked：这个方式展示item就像一堆卡片，item从Pikcer的底部用动画的方式进入。通过Digital Crown来进行控制。
- Image Sequence：这个方式是用来从一系列图片中展示一张图片。

当然，在一个interface controller中，包含多个picker也是可行的。

讲到interface controller就不得不说，**WKInterfaceController**现在提供一个展示alerts和action sheets的方法了！

调用`presentAlertControllerWithTitle(_:message:preferredStyle:actions:)`方法来在当前interface controller展示一个alert或者是一个action sheet，展示哪种当然依赖于你传的**preferredStyle**参数。这个**action**参数或许是这四个参数中最有趣的，因为它是一个**WKAlertAction**实例的数组。这个WKAlertAction提供了一个block，当用户点击对应这个action的button的适合，这个block就会被调用。让这个alert或者是action sheet消失的按钮的block你完全不需要写任何的东西。。

### One More Thing…

在播客的前面，当Jake和我正在讨论我们在WWDC上想看到什么，有一件事是我们都想看到的。那就是自定义Complications（这个我不知道该如何翻译成中文，请校对者注意一下这里）。你可以把它想象成放在钟表界面上的小插件。我猜Tim厨子应该也是一个很好的倾听者，我们提意见，他就把它实现。

OK,现在你可以给你自己的手表app构建自定义complications，然后让你的数据显示在用户选择的表的界面上。

自定义complications是建立在一种家族的概念上，每种家族提供一个或多个模板类，然后你创建一个继承于它的子类来实现你自己的complication。在complications中，现在有五种家族:

- Modular:有一些小的和大的变体在这个家族中，可以在MODULAR表的界面上看到它们。
- Circular:在这个家族中只有一个小的变体，可以在SIMPLE表的界面的上看到它们。
- Utilitarian：像Modular一样，有一些小的和大的变体在这个家族中，可以在UTILITY表的界面上看到它们。

如果你没有一个Apple Watch或者是不熟悉这些表的界面，查阅这份[指南](https://developer.apple.com/library/prerelease/watchos/documentation/General/Conceptual/AppleWatch2TransitionGuide/DesigningaComplication.html#//apple_ref/doc/uid/TP40015234-CH11-SW1)来获取更多。阅读[watchOS 2 Transition Guide](https://developer.apple.com/library/prerelease/watchos/documentation/General/Conceptual/AppleWatch2TransitionGuide/index.html)里的[Providing Custom Complications](https://developer.apple.com/library/prerelease/watchos/documentation/General/Conceptual/AppleWatch2TransitionGuide/DesigningaComplication.html#//apple_ref/doc/uid/TP40015234-CH11-SW1)这一节来获取更多的关于构建自定义complications的细节。

自定义complications对我来说是非常棒的特性，我等不及深入了解它的api然后开始构建我自己的complication!

### 接下来何去何从？

总而言之，我对watchOS 2里的所有新特性感到非常的激动！我期待和我的Turorial团队在接下来的几个月出[WatchKit By Tutorials](http://www.raywenderlich.com/store/watchkit-by-tutorials) 的第二版书。

记住，这仅仅是watchOS 2里的WatchKit的第一版，按Apple的尿性，在接下来的会增加更多的特性和API。在这篇文章我讲到的在未来都可能会改变或者是被更好的替代。以前发生过得，以后也会发生！:)

我很好奇你的对WatchKit最新版本的初印象，请在评论区说出你的想法哦! :)