> * 原文链接 : [Swift Programming 101: Creating Self-Registering Swift UI Controls](http://www.iphonelife.com/blog/31369/swift-programming-101-creating-self-registering-swift-ui-controls)
* 原文作者 : [Kevin McNeish](http://www.iphonelife.com/blog/kevin-mcneish)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [kmyhy](https://github.com/kmyhy) 

What’s the best way to create controls that respond to notifications without interfering with existing messaging mechanisms? In this article I demonstrate a Notification Proxy class that helps you add functionality to existing iOS UI controls using a practical example: Adding placeholder text to the text view control.

对于自定义控件来说，在不破坏原有的消息机制的前提下，如何响应事件通知？在本文中，我将演示一个通知代理类，通过一个简单的例子，我们用该类向已有的iOS UI控件中增加了自己的新功能：为Text View控件增加placeholder文本。
 
##The Problem: Missing Placeholders
##问题：缺失的Placeholder
Placeholder text is a great way to let your users known the type of content you intend them to enter in a particular control. UIKit’s UITextField control has a placeholder attribute that lets you do this. For example, Figure 1 shows placeholder text in the Twitter settings screen. The placeholder text in the User Name field indicates you should enter your Twitter handle, including the @ sign. The placeholder text in the Password field indicates it is required.
 
Placeholder文本是用于在某些控件中提示用户输入指定类型的数据的良好方式。UIKit的UITextField控件的placeholder属性就是用来干这个的。例如，下图中，在Twitter的选项设置中，User Name字段就使用了placeholder文本。通过这个placeholder文本，用户可以知道应当在这里输入一个包含了@符号的Twitter用户名。而在Password字段的placeholder文本则标明该字段是必填的。

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig001PlaceholderText-300x533.jpg"/>
图 1 - 设置程序中的Placeholder 文本
 
Although the text field has a placeholder attribute, its cousin the text view control, does not.


尽管Text Filed有placeholder属性，但它的兄弟控件Text View却没有这个属性。
 
##Solving the Problem
##问题的解决
 
Here’s the big picture of how I planned to solve this problem:
 
 我准备这样解决这个问题：
 
1. Create an extension that adds a calculated property named placeholder to the UITextView class
2. In the extension, display the label when there is no text in the text view, and hide it when there is text.

---

1. 创建一个UITextView的扩展，添加一个placeholder的计算属性
2. 在扩展中，当TextView中没有输入任何文字的情况下，显示一个Label，如果输入有文字，则隐藏这个Label。

As mentioned in my previous post, I prefer using extensions rather than subclasses because it allows me to use the out-of-the-box UIKit controls in my apps. All I have to do is add the extension code file to my project and all my classes automatically gain the new functionality.

就像我在前面的文章中提到的，我更喜欢使用扩展，而不是子类。因为扩展允许我在App中使用“盒子之外”的UIKit控件。我需要做的仅仅是在项目中加入某个类的扩展文件，然后这个类会自动获得新的特性。
 
##Solution 1: UITextViewDelegate
##方式1：UITextViewDelegate

There are a few ways iOS can notify you when the user types text in a UITextView control. One of these is to adopt the UITextViewDelegate protocol in the extension and store a reference to the text view in its own delegate property. When the user types or deletes characters in the text view at run time, the text view’s delegate method is automatically called. You can then add code to the delegate method that hides or shows the placeholder label.

当用户在TextView中输入文本时，iOS有几种通知App的方式。其中一种就是通过UITextViewDelegate协议。在这个扩展中有一个对TextView的引用保存在它自己的delegate属性中。当用户输入或删除字符时，TextView的协议方法会被调用。我们可以在协议方法中加入隐藏和显示placeholder标签的代码。

 
The problem with this approach is that it ties up the text view’s delegate property, since you can only register a single delegate object with a UI control. If you need to add other delegates to the text view in the future, your hands are tied.

这个解决方法的缺点是，Text View太过于依赖delegate属性，因为在一个UIControl中你只能注册一个委托对象。如果你需要向TextView中加入更多的委托对象，你就会比较麻烦。
 
##Solution 2: NSNotificationCenter
##方式2：NSNotificationCenter
 
NSNotificationCenter has a UITextViewTextDidChangeNotification that alerts you when the user types or deletes characters in a text view. This is a better choice because it doesn’t tie up the text view’s delegate property. The downside of this approach is unregistering from NSNotificationCenter. Typically, you deregister an object from NSNotificationCenter by adding code to its deinit. However, in Swift you can’t add a deinit to an extension. How can we get around this limitation?

NSNotificationCenter通过UITextViewTextDidChangeNotification通知来告诉你用户在TextView中输入或删除了某些字符。这是一种更好的选择，因为它不再依赖于delegate属性。缺点是需要向通知中心进行注销。一般，我们在对象的deinit方法中向NSNotificationCenter注销该对象。但是在Swift中，我们无法在扩展中使用deinit方法。那我们怎样才能突破这个限制呢？
 
##Creating a Notification Proxy
##创建一个通知代理
We can get around the limitations of Swift extensions by creating a lightweight proxy object that is registered with NSNotificationCenter in place of the text view. 

我们通过创建一个轻量级的代理对象来突破这个限制，这个代理对象代替TextView来向通知中心进行注册。

 
I’ve created a UITextView extension along with a proxy class and included it in a project you can download from this link.

我已经在我的项目中创建了一个UITextView扩展以及一个代理类，你可以从[这里](http://www.iosappsfornonprogrammers.com/media/blog/NotificationProxyDemo.zip)下载。
 
You can open the project in Xcode by double-clicking the .xcodeproj file. To see the Notification Proxy class, select the mmTextViewExtensions.swift class file in the Project Navigator (as you can see, I included the mmDynamicTypeExtensions class from my previous article).

双击.xcodeproj文件，用Xcode打开这个项目。在项目导航窗口中选中mmTextViewExtensions.swift文件，你可以找到这个通知代理类（如你所见，其中也包含了上一篇文章中的mmDyamicTypeExtensions类）。
 
Here is the Notification Proxy class located at the top of the code file:
 
下面是位于文件头部的通知代理类：

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code1_12-695x375.jpg"/>
 
As you can see, it’s a subclass of UIView. This allows us to add it as a subview to the text view control. The addObserverForName:usingBlock: method has the same signature (name, number, and type of parameters) as NSNotificationCenter’s method. There is just a single line of code in this method that registers the text view with NSNotificationCenter and passes through the parameters including the text view’s block to be executed when a notification occurs. NSNotificationCenter returns an NSObjectProtocol object that can be later used to unregister from notifications.
 
你可以看到，它是UIView子类。这样我们就可以将它当成subview添加到TextView中。addObserverForName:usingBLock:方法通知中心的方法拥有一样的签名。这个方法中只有一行代码，就是将TextView注册到通知中心并将参数传递过去，包括TextView的闭包，这样当通知发生时该闭包被执行。通知中心会返回一个NSObjectProtocol对象，稍后我们会用来进行通知的注销操作。

The deinit has a single line of code that simply deregisters the protocol object from NSNotificationCenter.

deinit方法也只有一行代码，仅仅是向通知中心进行对象的注销。
 
The Notification Proxy class is reusable. You can use it for any notification and with any class you want to receive notifications.

通知代理类是可重用的。你可以用于任何类型的通知以及你想接收通知的任何类型的对象。
 
Below the Notification Proxy in the code file is the UITextView extension. It has a placeholderLabel property that, as you might guess, is used to contain a reference to the placeholder label.

然后是UITextView的扩展，在扩展中有一个placeholderLabel属性，如你所想，它引用了一个placeholder标签对象。
 
It also has a placeholder String property. As you will see in the next section, all the magic happens when the placeholder property is set at run time and the computed property's code is executed.

此外还有一个placeholder的字符串属性。在后面你将看到，所有的奇迹将在运行时发生，当placeholder属性被设置，这个计算属性的代码就会执行。
 
Now select the Main.storyboard file in the Project Navigator. As shown in Figure 2, there is a single scene in the storyboard with a text view located near the top of the scene.

现在打开Main.storyboard文件。如图2所示，故事板中只有一个Scene，在这个Scene的顶部，有一个Text View。
 
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig002TextViewStoryboard-695x400.jpg"/>
图 2 - 主 scene 中包含了一个 text view
 
If you select the text view and go to the Attributes Inspector, you can see the Placeholder attribute, which is added by the extension and its value is set to “Enter your text here!”. The placeholder text doesn’t appear in the design surface (we would need to take a number of extra steps to make this happen, and that’s a topic for another article) but it does appear at run time.

当你选择Text View，然后打开属性面板，你将看见一个Placeholder属性，它是扩展中增加的属性，这个属性的默认值是“Enter your text here!”。在设计时不会显示这段文本（要在设计时可见，我们需要花费大量的工作，因此这不是本文的主题），但在运行时它会显示。
 
##The Notification Proxy at Run Time
##运行时的通知代理
 
The UML sequence diagram in Figure 3 shows the order of messages passed between all objects involved in this architecture.

图3中的UML序列图显示了所有对象之间发生的消息传递的顺序。

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig003SequenceDiagram-695x847.jpg"/>
 
图 3 - 运行时对象间消息传递的顺序
 
Here is an explanation of each step:

每一步的解释如下：
 
1. When the UITextView placeholder text is set at run time it fires the extension’s placeholder computed property’s code.
2. The UITextView extensions’s addPlaceholderLabel method is called.
3. The addPlaceholderLabel method creates a placeholder label and sets its text.
4. The placeholder label is added to the UITextView.
5. A Notification Proxy object is created.
6. The UITextView extension calls the Notification Proxy’s addObserverForName:withBlock: method, specifying UITextViewTextDidChangeNotification as the name of the notification it wants to observe. It also passes a block of code to be executed when the notification occurs.
7. The Notification Proxy object registers the UITextView with NSNotificationCenter, also passing through the notification event to be observed and the UITextView block to be executed when the notification occurs.
8. The UITextView object adds the Notification Proxy to itself as a subview.
9. When a UITextViewTextDidChangeNotification occurs, NSNotificationCenter calls the block on the UITextView extension.
10. The UITextView extension sets the placeholder label’s hidden property to true or false, depending on whether or not there is any text entered in the text view.
11. When the UITextView is deallocated at run time, the placeholder label and notification proxy object are deallocated.
12. When the Notification Proxy object is deallocated, it unregisters the UITextView from NSNotificationCenter.

---

1. 当运行时，UITextView的placeholder文本被改变时，该扩展的placeholder计算属性中的代码被触发。
2. 扩展的addPlaceholderLabel方法被调用。
3. addPlaceholderLabel方法创建一个placeholder标签并设置标签文本。
4. placeholder标签被添加进UITextView。
5. 一个通知代理对象被创建。
6. 扩展调用通知代理的addObserverForName:withBlock:方法，并指定对UITextViewTextDidChangeNotification通知感兴趣。此外闭包代码会在通知发生时执行。
7. 通知代理对象将UITextView注册到通知中心，同时将通知时间传递给TextView，当通知发生时，UITextView的闭包代码将被执行。
8. UITextView以subview的方式添加通知代理对象。
9. 当UITextViewTextDidChangeNotification通知发生，通知中心调用TextView扩展的闭包。
10. UITextView扩展将placeholder标签的hidden属性设置为true和false，取决于TextView中的文本是否有内容。
11.当TextView在运行时解构时，placeholder标签和通知代理对象都被解构。
11. 当通知代理对象解构时，将TextView对象从通知中心中注销。
 
##Let’s Take it for a Test Run!
##运行测试
 
1. In Xcode’s Scheme control, select one of the simulators such as iPhone 6.
在Xcode的Scheme下拉列表中，选择一个模拟器，例如iPhone6。

2. Click Xcode’s Run button.
点击Xcode的Run按钮。

3. When the app appears in the Simulator, you can see the placeholder text (Figure 4).
当App在模拟器中运行后，我们可以看到placeholder文本（图4）。

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig004TextViewRunTime-300x534.jpg" />

图 4 - 运行时的 placeholder 文本
 
	4.Now type some code in the text view. As soon as you begin typing, the placeholder label disappears (Figure 5).
	现在在TextView中输入任意字符。此时，placeholder将消失（图5）。

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig005PlaceholderDisappears-300x534.jpg"/>

图 5 -placeholder 文字消失了

	5. If you delete all the characters you typed, the placeholder reappears.
	如果将输入的字符删除干净，placeholder文字将重新出现。

##Conclusion
##结束语
There are many ways to solve a coding problem. It’s best to take a good look at your options up front and think about the pros and cons of each. I hope through these articles you’re also learning the beauty of sequence diagrams. They allow you to document and understand object interactions that occur at run time. I don’t just use these for my articles. I use them for my real-world coding designs. You’ll find that they can help you identify potential problems as well as help you visualize new and more efficient solutions to your coding challenges.

要解决编程问题有许多方法。最好的方法是先看一下摆在你目前的选择，然后逐个分析其中的利弊。我希望你能看到本文中序列图的好处。它能让你将对象之间在运行时的交互画出来。我在编写代码时也使用了这个方法。你会发现它们能帮助你找出潜在的问题，更能帮你找出编程问题中的新的、更高效的解决方案。