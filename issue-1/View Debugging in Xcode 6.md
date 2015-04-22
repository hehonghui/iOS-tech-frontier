# View Debugging in Xcode 6

# Xcode 6中的View调试

When you’re developing an app, sometimes there may be a bug with your views or auto layout constraints that isn’t easy to find just by looking through your code. 

我们在开发app的时候经常会遇到一些在代码中难以发现的view中的bug或者自动布局限制。
   
It pays to know the technique of view debugging – and this has never been easier with the advent of Xcode 6.    

这时候，掌握view调试的技巧就显得很重要—但是在Xcode 6 中的调试也并非易事。


Instead of printing frames to the console and trying to visualize layouts in your head, you’re now able to inspect an entire view hierarchy visually – right from within Xcode. What an incredible time to be alive!   

我们现在可以直接在Xcode中直观地看到view的整个层级，而不是在console控制接口打印框架或者试图在脑海里凭空描绘布局。

 
This tutorial will take you through all the different options that are at your disposal. So, are you ready to write some code? That’s too bad, because you won’t. :]    
Instead, you’ll inspect the view hierarchy of an open source library to better understand how it was written — without even looking at any code.

本教程会带大家一览各种选择，并自行决定选择适合自己的。那么，大家准备好写几行代码了吗？告诉大家一个好消息，其实不必写代码。相反，我们只需要观看开源库中的view层级来更好地了解它是怎么写出来的—我们甚至可以连代码都不用看。


![pic-1](http://cdn1.raywenderlich.com/wp-content/uploads/2015/04/app-view.jpg) 

*Debugging views in Xcode has never been easier*

*在Xcode中调试view不是件容易的事*

##Getting Started

##入门指南


The library you’ll use in this tutorial is JSQMessagesViewController, written by Jesse Squires. The UI for this library should look very familiar, as Jesse built it to look similar to the Messages app.

本教程将使用到的开源库是由Jesse Squires编写的JSQMessagesViewController。大家应该觉得这个库的UI看起来很熟悉吧，因为Jesse特意把它设计得像Messages应用。

To get started, head over to the [GitHub project page](https://github.com/jessesquires/JSQMessagesViewController), download the source code, and unzip into a directory.

首先我们要跳转到[GitHub project page](https://github.com/jessesquires/JSQMessagesViewController)页面，下载源码并解压到相应路径中。

>Note: The library uses CocoaPods to manage its dependencies on other libraries. If you’re unfamiliar with how CocoaPods works, please take a look at this [CocoaPods tutorial](http://www.raywenderlich.com/64546/introduction-to-cocoapods-2) here on the site before proceeding.

>注：这个库使用CocoaPods来管理其对其他库的依赖性。如果大家不熟悉CocoaPods的工作原理，我建议大家在进行下一步之前先点击此处CocoaPods tutorial了解一下。


Next, navigate to the unzipped project directory in Terminal and run `pod install` to install the required dependencies. Then, open `JSQMessages.xcworkspace` and build and run the app on the `iPhone 5s simulator`. (You can use any simulator, but dimensions in this tutorial are based on a 4-inch display, so choosing the same will make it easier to follow.)

下一步，找到终端中刚才解压的项目路径然后运行`pod install`来安装所需的依赖关系。然后打开`JSQMessages.xcworkspace` 并在`iPhone 5s simulator`上运行这个app。（大家也可以使用其他的simulator，但是本教程是基于4英寸显示屏的尺寸，所以选择同样的尺寸比较容易推进。）

>Note: Live View Debugging is only available if you run your app on iOS 8. You cannot use this tool on iOS 7, even if you’re working in Xcode 6.

>注：实时View调试仅在iOS 8上运行app是有效。本工具即使在Xcode 6环境下，在iOS 7上仍无法使用。

Tap on `Push via storyboard` and you’ll notice you’re now in a text messaging thread with Steve Jobs and Tim Cook. (This may cause a flutter in your heart and make you question your perception of reality, but it’s not really them.) This is the view you’ll inspect.

点击`Push via storyboard`，然后我们就来到了与乔布斯和库克的短信对话线程。（哈哈，看到这里大家心里可能有点发毛，甚至怀疑自己是不是在做梦。额，这不是他俩本人好吗！）以下就是大家看到的view。

![pic-2](http://cdn4.raywenderlich.com/wp-content/uploads/2015/04/Screen-Shot-2015-04-07-at-12.03.28-PM.png)

*Even Steve Wozniak joins the fun!*

*Steve Wozniak也来凑热闹啦！*

Go back to Xcode and click on the `Debug View Hierarchy` button in the Debug bar. Alternatively, go to `Debug\View Debugging\Capture View Hierarchy`.

返回Xcode后点击调试条上的`Debug View Hierarchy`按钮。或者，直接转到`Debug\View Debugging\Capture View Hierarchy`。

![pic-3](http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-1.png)

Xcode is now interrupting your app and handing the reigns to the debugger, just as if you had paused your app with the pause button on the Debug bar. 
   
In addition, Xcode replaces the code editor with a canvas in which it draws the entire view hierarchy of the key window of your app, including thin lines (called wireframes) that indicate the boundaries of every view.

此时Xcode正在阻断app并将主动权交给调试程序，正如我们按了调试条上的暂停键把app给暂停了。

另外，Xcode使用画布代替了代码编辑器，并在画布上绘制app主窗口的完整view层级，包括用来表明每个view的边界的细线部分（我们称之为线框）。


![pic-4](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-2.png)

You may know that when you add a subview to a view hierarchy, you’re adding a layer on top of the current stack of views. Because most views don’t overlap, it looks like all views are just part of one big layer when you run your app. The screen you’re currently looking at is pretty close to that, but with a bunch of extra lines.

我们知道，当我们向view层级中添加一个subview时，我们其实是在向现有的view堆栈上添加一个层次。因为大部分的view不会重叠，当我们运行app时所有的view看起来只是一个大层次的一部分而已。此刻我们看到的屏幕与此非常相似，只不过多了几条线而已。

So how is that useful? Well, right now you’re seeing a visual of the view stack from overhead, but what if you could visualize where the layers fall within the stack? Click and drag in the canvas, and you’ll see that instead of a flat 2D view, you’re actually interacting with a 3D model of your view hierarchy.

那这几条线有什么用呢？此刻我们看到的是一个从上往下看的view堆栈视觉图，想象一下，如果我们能想象出各层次在堆栈中的位置的话会怎么样呢？点击并拖拽画布，我们就可以与View层级的3D视觉效果模型进行互动，而不再是平面的2D效果。

![pic-5](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-3.gif)

You can view the hierarchy from the side, top, bottom, a corner and even from the back!

我们可以从侧面、顶部、底部、从各个角度甚至从后部观察整个层级。

![pic-6](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/views.png)

>Note: It’s possible your canvas isn’t showing the same views as this tutorial assumes. To make sure you’re on the same page, press `cmd + 6` to get to the Debug navigator.

>注：大家的画布呈现的效果和本教程的效果可能会有些差异。为了确保进度统一，请大家按住`cmd+6`跳转到调试导航器。
>
At the bottom of the pane, you’ll see two buttons on the left. Deselect both of these buttons, as seen in this image. If not, some views will be hidden on the canvas.

在边框底部我们可以看到左侧有两个按钮。如图所示，同时取消选定两个按钮。如果未取消选定，画布可能就会隐藏了某些view。

![pic-7](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/filter-image1.png)

##Exploring the View Hierarchy

##探索View层级 

The most natural and common perspective of this 3D model is to look at it from the left side — you’ll see why a little later in this tutorial — so manipulate it to get a point of view like the one below.

这个3D模型最自然最通用的一面就是从左侧看到的面，如下图所示（稍后我会在本教程讲到原因）。

![pic-8](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-4.png)

This gives your view hierarchy some perspective that’s very useful if you want to visualize how it’s built. However, there seems to be many empty views at the “bottom” (on the left) of the stack. What’s that all about?

这个图给了我们一些有用的切入面，如果我们想把其建造原理形象化，这个切入点非常重要。但是，堆栈的“底部”（左侧）似乎存在许多空白的view。这是为什么呢？

Click on the left-most view (i.e. the one in the very back) and you’ll see that Xcode highlights it to indicate your selection. You’ll also see the Jump Bar (just above the Canvas) update to show a UIWindow as the last item — that last item will always reflect the currently selected view and its class type.

点击最左侧的view（也就是最后面那个）后，我们可以看到Xcode会将其显示高亮来标示我们选择的地方。我们还可以看到Jump Bar（就在画布的上方）把UIWindow显示为最后一项--最后一项会反应当前选择的view及其类的类型。

![pic-9](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-5.png)

Since this app uses only one window, it’s safe to assume the `UIWindow` at the start of the Jump Bar is the app’s key window, or in other words, the `window` property on `AppDelegate`.

由于这款app只用了一个窗口，我们大可以假设Jump Bar开始处的`UIWindow`就是app的主窗口，换句话说，是`AppDelegate`的`Window`属性。

OK, it’s good to know how to find the window, but it’s unlikely that you’ll need to inspect that. What about the next view? In the canvas, click on the view to the right (i.e. on top) of the window and look at the Jump Bar again. `UILayoutContainerView`. That’s not even a public class!

好了，这样我们就知道如何找到窗口了，但是我们似乎并不需要查看它。下一个view又该怎么弄呢？在画布上点击窗口右侧的view（也就是顶部）并重新观察Jump Bar。`UILayoutContainerView`对吧？这特么连一个公共类都算不上！

![pic-10](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/iw21s.jpg)

From there, the view hierarchy looks like this:

那一步之后，view层级是这样的：

1. `UINavigationTransitionView`: The container view in which navigation controller transitions happen

    `UINavigationTransitionView`：是指导航控制器过渡的container view

2. `UIViewControllerWrapperView`: A wrapper view that contains the view controller’s `view` property

    `UIViewControllerWrapperView`：是指含有view controller的`view`属性的wrapper view
3. `UIView`: The top-level view of a view controller (the same as a view controller’s `view` property)

    `UIView`：是指一个view controller的一个最高层view（与view controller的`view`属性相同）

4. `JSQMessagesCollectionView`: The collection view used by this project to display all messages

    `JSQMessagesCollectionView`：是指该项目用来显示所有短信息的collection view

##Focusing on Views of Interest

In debugging this particular view hierarchy, the first four views (starting with the window) are really just visual noise. They don’t bring anything meaningful; they distract in trying to understand what else is going on in this view hierarchy. It would sure be nice if you could filter out that visual noise…

在调试这一独特的view层级时，前四个view（从窗口处开始）只是视觉噪声，没有实际意义；它们的存在可能会干扰大家去了解这个view层级中的其他信息。如果大家能够拨开迷雾，过滤掉这些视觉噪声，那才是极好的...

And you can. :] Look at the double-thumbed slider in the bottom right of the canvas. By default, the thumbs are all the way on the left and right side of the slider.

大家还可以：观察画布右下角的双向滑块。默认情况下两个滑块分别分布在最左和最右。

![pic-11](http://cdn2.raywenderlich.com/wp-content/uploads/2015/04/Screen-Shot-2015-04-07-at-12.24.14-PM.png)

Drag the left thumb slowly to the right a little bit; you’ll see the wireframe that represents the app’s window disappear from the canvas. If you drag a little further, the `UINavigationTransitionView` disappears as well.

将左边的滑块稍微向右移动一点点，我们可以看到代表app窗口的线框从画布上消失了。再向右移动一点，我们可以发现，`UINavigationTransitionView`也消失了。

Drag the left thumb as far as needed to hide all the parent views of `JSQMessagesCollectionView`. Your canvas should now look similar to this:

把左边的滑块尽量拖拽至隐藏`JSQMessagesCollectionView`的所有父视图，那么画布应该如下图所示：

![pic-12](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-6.png)

On the right side, you might notice the navigation bar is not just distracting, but it’s actually laid out on top of the collection view, making it hard to see what’s going on underneath. Fortunately, you can hide it.

我们看右边的导航条就没有那么分散，但它其实是被展示在了collection view的顶部，我们难以看到底部发生了什么变化。还好，我们可以把它隐藏起来。

Because you’re focusing on a smaller area of the screen with many smaller views that comprise the navigation bar, it’s a good idea to zoom in on the nav bar so you can see what exactly you’re doing.

由于我们现在关注的是包含了许多组成导航条的小视图的屏幕区域，所以在这里我把导航条放大，以便大家看清楚我到底怎么操作。

Use the zoom control buttons, which are in a group of three buttons centered at the bottom of the canvas:

使用画布下方靠中间部分的三个缩放控制按钮：

![pic-13](http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/Screen-Shot-2015-03-15-at-5.03.26-PM.png)

As you would expect, the + button zooms in, the – zooms out, and the = button resets the zoom to the normal zoom level. Zoom in to get a good visual of the navigation bar.

大家肯呢过已经猜到了，+按钮代表放大，—按钮代表缩小，=代表将画面重置到正常尺寸。放大以后我们可以更清楚地观看导航条。

![pic-14](http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-7.png)


>Note: If you use a trackpad, pinch gestures will also zoom. A trackpad is also useful to move around in the canvas if all parts of the screen can’t be shown at once because you zoom in really far. You can also zoom with option-mousewheel.

>注：如果大家用的是触摸屏，收放手势则代表画面的缩放。如果由于放大倍数太高导致画面不能全屏显示完整，我们可以上下左右移动触摸屏以查看未显示的部分。我们还可以使用鼠标滚轮来操作。

The extra detail you get by zooming in on the toolbar is nice, but the views still slightly overlap, so it’s not easy to tell which view is which.

放大之后的工具条很容易看到细节部分，但是view之间会有些重叠，所以要区分哪个view是哪个并不容易。

To solve that problem, use the spacing slider in the bottom left corner of the canvas. The further you drag the thumb of the spacing slider to the right, the more spacing Xcode shows between different views.

要解决这个问题，我们需要使用画布左下角的间距滑块。滑块越往右滑，Xcode中不同view之间的间距越大。

![pic-15](http://cdn3.raywenderlich.com/wp-content/uploads/2015/04/Screen-Shot-2015-04-07-at-12.29.20-PM.png)

In this particular case, move the slider to the right as much as needed to avoid overlapping views in the toolbar. You might have to play around with the perspective by click-dragging on the canvas to get the desired result.

在这一特例中，我们需要将滑块尽量往右移动，避免view与工具条重叠。要想达到理想的效果，我们可能会需要在画布上单击并拖曳鼠标慢慢实现。

![pic-16](http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-8.png)

The 3D model is now perfectly manipulated so you can easily hide the navigation bar.

现在我们就完美地操纵了3D模型，从而轻而易举地将导航条隐藏起来。

In the slider on the right (the one that hides views), drag the right thumb slowly to the left, up to the `UINavigationBar`. Remember that you can use the Jump Bar to identify each view’s class by selecting the topmost layer as you go. You’ll see the navigation items disappear first, then the buttons that contain them, followed by a few private views, and lastly, the navigation bar.

将右边滑动条的右滑块慢慢往左拖曳，直到`UINavigationBar`处。记住，我们可以使用Jump Bar来选择最上层从而判定每个view的类。我们会看到先消失的是导航条目，然后是包含它们的按钮，接下来是几个私有view，最后是导航条。

![pic-17](http://cdn3.raywenderlich.com/wp-content/uploads/2015/04/HideNavBar.png)

*Look ma, no nav bar!*

*看，导航条不见了吧！*

>Note: If you rotate the canvas to look at the 3D view hierarchy model with the top layer on the left, the slider’s left thumb still removes views from the bottom of the stack, which is now on the right. Similarly, the right thumb removes views from the left.
>
Moving a slider from the left to the right and having views disappear from the right to the left (and vice versa) is counterintuitive, so that’s why looking at the model with the top layer on the right is the most natural perspective.

>注：如果我们把画布翻转以下让顶层处在左边，然后再观察这个3Dview层级，滑动条的左滑块移动右边堆栈底部的view。同样地，右滑块会移动左边的view。

>把滑动条从左移到右，从而使view由右向左消失（反之亦然）是反直观的，这也是为什么我们在观看顶层在右侧的模型时觉得这是最自然的角度。

Unfortunately, hiding the nav bar (with the root view of `_UIBackdropView`) view also causes the toolbar items’ content at the bottom of the screen to disappear. To see this, you may need to adjust the zoom level or move down the canvas.

很遗憾，隐藏导航条（root view为`_UIBackdropView`）的同时，view会导致屏幕底部的工具条条目内容消失。我们调节缩放尺寸或者将画布往下移看看是什么情况。

You want to see the toolbar items as they are an important part of the screen, so only hide the views up until (but not including) the `_UIBackdropView`. The navbar stack should look something like the following once you’re done.

由于工具条条目是屏幕的一个重要部分，所以我想大家有必要看看，所以我只隐藏了到`_UIBackdropView`（不含）的这一部分的view。操作完成后导航条堆栈应该是下图的样子。

![pic-18](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-9.png)

##More View Options

##更多View选项

Now that irrelevant views are hidden, it’s time to take a look at the screen from the front again. You can try to drag the model back into place, but sometimes it’s difficult to get it just right. Fortunately, there is a simpler way.

不相关的view被隐藏之后，我们得重新看一遍屏幕。我们可以把模型拖曳到原位，但是有时候确实很难百分百回到原位。别急，我们还留了一招。

Look at the group of four buttons to the left of the zoom buttons. The third button from the left is the `ResetViewing Area` button. It undoes rotations and gives you the front perspective of the view hierarchy, just like in the simulator or on a device.

看到缩放按钮左边的那silent按钮了吗？做起第三个是`ResetViewing Area`按钮。它的作用是撤销屏幕旋转并显示view层级的正面构造，就像simulator或设备上的一样。

![pic-19](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/Screen-Shot-2015-03-15-at-5.03.26-PM-copy.png)

Your canvas should look similar to the following at this point:

此时画布看起来应如下图所示:

![pic-20](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/head-on-view-with-wireframes.png)

You probably noticed that what you see in the debugger is still not exactly what you see when the app actually runs.

大家可能注意到了，我们在调试器里看到的东西并不完全是app实际运行是我们所看到的。

First of all, you still have wireframes around every single view; they allow you to see transparent views or views without any content, but if you don’t need the detail they can make things pretty noisy.

首先，每个单独的view仍然有线框；通过这些线框我们看到透明的或者无内容的view，但是如果我们不看细节的话，它们会变得非常模糊。

You can turn this off with the `View Mode button` — the one to the right of the `Reset Viewing Area` button. When you click the view mode button, you can specify if you want to see the views’ wireframes, their contents or both.

我们可以通过`View Mode button`（`Reset Viewing Area`按钮右边那个）将其关闭。当我们点击view Mode按钮时，我们可以决定要不要显示view的线框和/或其内容。

![pic-21](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/wireframes.gif)

A wireframes-only view is particularly useful if you’re mostly interested in positioning, and not so much about what a view looks like. Showing only the views’ contents is useful when you’re trying to debug a view’s appearance.

一个只有线框的view在定位时非常有用，view的外形并不那么重要。而当我们想调试view的外观时，view的内容就非常有用了。

To reduce some of the clutter the wireframes cause in this case (especially near the navigation bar and toolbar), change the view mode to `Contents` to remove all the wireframes and leaves you with the core of the app.

为了减少在这种情况下线框引起的杂乱（尤其是导航条和工具条附近），我们要把view Mode切换到`Contents`来去除所有的线框，只留下app的核心部分。

![pic-22](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/no-wireframes.png)

Next, a couple of things are missing from the current view. When you run the app, you’ll see labels above the text bubbles that indicate the sender’s name or the message’s timestamp, as well as an image of the Golden Gate Bridge in the last bubble. But the debugger isn’t showing those labels and that image!

下一步，当前的view缺少了几样东西。运行app时，我们可以看到文本泡泡上方显示发送者姓名的标签或短信的时间戳，以及最后一个文本泡泡显示的金门大桥图像。但调试器却没有显示这些标签和图像！

To solve this, look at the first button in the center row of buttons on the canvas. This is a toggle to show or hide clipped views. These are views that have their `clipsToBounds` property set to YES.

要想解决这个问题，首先请大家看到画布中间那行按钮的第一个按钮。它是一个显示或隐藏省略view的触发器。这些省略的view都已经将它们的`clipsToBounds`属性设置成了YES。

![pic-23](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/clip-to-bounds-button.png)

That is exactly what is happening with these labels, presumably because long names and dates should not extend beyond the bounds of their labels. The same applies to the image, which uses a corner radius and clipping to produce the rounded corners. Click on the toggle and you’ll notice these views now show up in Xcode.

这恰恰就是那些标签里面所含的内容，大概是因为姓名或日期太长，不能延伸到标签范围以外吧。图像也是同样的原理，使用圆角半径剪裁来达到圆角的效果。点击触发器我们就会看到Xcode中显示了view。

![pic-24](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-11.png)

>Note: You may notice wireframes around the newly visible items. If that’s the case, toggle wireframes on and then off with the View Mode button you used previously. That should resolve the issue.

>注：大家可能会看到新的可视条目的线框。如果真有的话，可以使用我们之前用过的View Mode按钮将线框先打开后关闭，问题应该就解决了。

And there you have it: a near-perfect replica of your view hierarchy right within Xcode.

这下大家看到了吧：我们在Xcode里复刻了一个近乎完美的view层级。

![pic-25](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/ragecomic1.png)

*That was easy!*

*也不难吧？*

##Inspecting Views

##查看View

Now that the important parts are easily accessible, it’s time to look at layouts for these different views.

既然重要的部分都搞定了，那我们来看看这些不同的view的布局吧。

You already knew the collection view is what makes all these views come together, but wouldn’t it be great if you could just have an overview of the different elements that are at play here? Good news – you can!

大家已经知道，collection view可以把所有view集合起来。但是大家想想，如果我们能把各个起作用的元素作一个概观，那岂不是更棒？我要告诉大家的是，我们当然可以实现！

Press `cmd + 6` to go to the Debug navigator. Since this is a debugging session just like any other, the Debug navigator provides contextual information about the current session. For view debugging, this means Xcode provides a view tree of all the views for all windows. Expand the tree in the Debug navigator to look like this:

按住`cmd + 6`转到调试导航器。由于这是一个普通的调试会话，所以调试导航器会提供关于当前会话的上下文信息。对于view调试来说，这意味着Xcode会为所有的窗口提供一个包含所有视图的树形View。树形view展开之后如下图：

![pic-26](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-12.png)

>Note: At the bottom of the Debug navigator, you’ll see options that give a little bit of control over what kinds of items display in this tree. Apple’s documentation claims the button on the left filters private elements out of system view implementations, though this button does not seem to work as of Xcode 6.2.
>
The button to the right hides views that have their hidden properties set to YES, and the search bar only displays views and constraints that match the search criteria.

>For the purposes of this tutorial, you should have both buttons deselected and no search filter applied.

>注：我们可以看到调试导航器的底部有一些控制树形view显示何种条目的选项。Apple的文件材料显示，左侧的按钮会把私有元素从view实现系统中过滤出来，不过这个按钮在Xcode 6.2环境下似乎不管用。

>右侧按钮则把那些将属性设置为YES的view隐藏起来，搜索栏只显示符合搜索条件的view和constrait。

>本教程将两个按钮都取消了选择且不使用搜索过滤。

This is a good point to start exploring a little bit. Expand the last `JSQMessagesCollectionViewCellOutgoing`. It only has one subview: a `UIView`.

从这里开始我们可以开始稍微往深一点探索了。将最后一个`JSQMessagesCollectionViewCellOutgoing`展开，下面只有一个子视图： `UIView`。

If you’ve worked with collection views before, you know this makes sense because any `UICollectionViewCell` has a `contentView` property that contains the cell’s contents.

如果大家以前接触过collection view，大家就会知道这其实是讲得通的。因为任何一个`UICollectionViewCell`都会有一个包含单元内容的`contentView`属性。

Click — don’t expand — on that `UIView` in the Debug navigator and you’ll see that Xcode has now highlighted it in the canvas so you know exactly where it is on the screen.

点击（但不要展开）调试导航器的`UIView`之后我们可以看到，此时Xcode已经在画布中将其显示高亮，这样大家可以很容易找到它在哪儿。

![pic-27](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-13.png)

To really understand how iOS positions that cell, open the Size Inspector with `cmd + option + 4`. The top part of the visualizes the view’s bounds, position and anchor point.

想要真正明白iOS怎样定位那个单元，我们需要按住`cmd + option + 4`打开Size Inspector。顶部显示的是view的边界、定位以及锚定点。

![pic-28](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-14.png)

However, the really interesting part is the list of Auto Layout constraints that apply to this view. You can immediately tell that the cell’s content view is 312 points wide, 170 points tall and centered in its parent. The containing cell is also 312 by 170 points, so you know the content view takes up the entire cell.

但是，真正有意思的是该view中应用的Auto Layout约束列表。大家可以立刻知道单元内容的宽为312点，高为170点，并且位于其父视图的中心。内含的单元也是312*170，所以大家明白了吧，content view占了整个单元的空间。

Below are constraints colored in gray that indicate those are constraints that dictate relationships between this view and its subviews.

下方是灰色的约束，表明这些约束决定了view及其子视图之间的关系。

To get more details about a particular constraint, first expand that view in the view tree and then the Constraints item. You’ll also see the same constraints you saw in the Size navigator listed here.

要想了解某个特定约束的细节，首先我们要展开树形view中的那个view，然后是约束条目。大家看到的是跟Size导航器中列出的相同的约束。

![pic-29](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-15.png)

Click on the first constraint (for me it’s a constraint on self.midX) and switch to the Object inspector by pressing `cmd + option + 3`. You’ll see a constraint overview with the items, multiplier, constant and priority. This is much like the summary you see in Interface Builder when you edit a constraint.

点击第一个约束（对我个人来说这是一个self.midX约束）并按住`cmd + option + 3`切换到Object inspector。此时可以看到约束和条目、倍增器、常数以及优先级重叠了view。这非常像我们在在Interface Builder里面编辑一个约束时看到的summary。

![pic-30](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-16.png)

In addition to sizing and constraint information, you can see other details related to the display of a particular view in the object inspector. Back in the Debug navigator, expand the `UIView` in the tree and you’ll see there are three `JSQMessageLabel`s and two `UIView`s under it. Select the first `JSQMessageLabel` (the one with the timestamp), and open the Object inspector.

除了尺寸大小和约束信息以外，我们还可以看到与object inspector中特定view显示有关的其他细节。返回至调试导航器，展开树形结构上的`UIView`之后我们看到它包含了三个`JSQMessageLabel`，两个`UIView`。选择第一个`JSQMessageLabel`（与时间戳一起的那个），然后打开Object inspector。

![pic-31](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-17.png)

The first section indicates the object’s class name and memory address, and the second shows values for various public properties of the object that pertain to its appearance.

第一部分显示的是object的类名称和存储地址，第二部分显示的是从属于其外表的object的各公共属性的值。

Here you can see the label’s text color is 0.67 gray with no alpha, and its font size is 12pt.

这里我们可以看到标签的文本颜色为不含alpha的0.67灰，字体大小为12pt。

Other classes have useful information specific to how they’re visualized as well. Back in the Debug navigator, expand the second UIView under the cell’s root `UIView` and you’ll see a `UIImageView`.

其他类中也包含了一些有用的信息，比如界面是如何可视化的。回到调试导航器，展开单元根`UIView`下的第二个UIView，可以看到一个`UIImageView`。

![pic-32](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-18A.png)

Select the image view from the tree and check out the Object inspector.

选择树形结构中的image view后查看Object inspector。

![pic-33](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-18B.png)

Here you’re looking at the view that shows the user’s avatar — in this case, the author’s initials, JSQ. You can see the normal image, conveniently labeled Image, as well as the darker image, labeled Highlighted, that shows when the user taps on the cell.

此时我们看到，view显示了用户的化身--在这种情况下，指的是作者的姓名首字母大写，JSQ。我们既可以查看正常图像，也可以查看带标记的图像以及颜色偏暗的图像，带标记高亮图像，这些图像可以反映用户何时点击单元。

The other two instances of `JSQMessageLabel` in this cell’s root view don’t currently have text, but they are used for the sender’s name in incoming messages and an error message when sending an outgoing message fails.

单元根view下的另外两个`JSQMessageLabel`目前没有文本，但是它们拟用于显示收件箱新消息的发送人姓名以及短信发送失败时提示错误信息。

And that’s how easy it is to do view debugging in Xcode! To continue running the app, just click the Continue button on the Debug bar, or go to **`Debug\Continue`**, just like you would if you were in a normal debug session.

以上就是在Xcode中调试view的步骤，够简单吧？想要继续运行app，我们只需要点击调试条上的Continue按钮，或者跳转到**`Debug\Continue`**，就和我们在普通调试中的操作一样。

##Live Modifications

##实时修改

Now that you know the basics of view debugging in Xcode 6, it’s time to put that knowledge to work with a small exercise: For the collection view you’ve been exploring throughout the tutorial, make its vertical scroll indicator red by only using the debugger.

到目前为止，大家都了解了在Xcode中调试view的基本方法，那么现在我们就来练练手：我们在教程中探索过了collection view，现在通过调试器将其垂直滚动条指示器标红。

Here are two tips to get you started:

下面提出两点建议：

1. Since view debugging is just like any other debugging session, you can use expr and other commands in the console, but keep in mind changes won’t appear until you resume execution. For more information on these commands, take a look at this [debugging apps in Xcode](http://www.raywenderlich.com/28289/debugging-ios-apps-in-xcode-4-5) tutorial.

    view调试和其他普通程序调试一样，我们可以在控制台使用expr或者其他命令，但是要记住，在恢复执行前这些改变不会显示出来。关于其他命令的更多信息，大家可以参考这个教程[debugging apps in Xcode](http://www.raywenderlich.com/28289/debugging-ios-apps-in-xcode-4-5)。

2. Because pointers in Objective-C are really just memory addresses, you’re simply messaging a memory address when you message an object. This works in the debugger as well, so a command like `po 0x123456abcdef` would print the description of the object at that memory address.

    由于Objective-C的指针其实就是存储地址，所以我们发送一个object时实际上就是发送一个存储地址。这在调试器中是一样的道理，所以像这样的命令会打印出存储地址中的object的描述。

Give it some solid attempts before you break down and look at the solution below!

故障前先做几次尝试总是没错的，请看以下解决方法！

Solution Inside: Solution	Show

内部解决方法：解决方法  显示/隐藏

First, make sure the view mode is set to “Contents”.

首先将view mode设置为“Contents”

![pic-34](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-19.png)

In the Debug navigator, expand the view tree of the collection view so you can see all its subviews.

在调试导航器中展开树形view中的collection view查看其子视图。

![pic-35](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-20.png)

The last views of the collection views are two instances of `UIImageView`. These are the horizontal and vertical scroll indicators. Click on the second one, and you’ll see the vertical indicator highlighted in the canvas.

collection view里最后两个view就是的两个例子。它们分别是水平滚动条和垂直滚动条的指示器。点击第二个后我们可以看到垂直指示器在画布上被显示为高亮。

![pic-36](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-21.png)

Copy the image view’s memory address from the Object inspector.

将Object inspector的image view存储地址复制下来。

![pic-37](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/Screenshot-22.png)

In this case, the memory address of the scroll indicator was `0x7fde6c484640`. All that’s needed to color the scroll view indicator is to send a `setBackgroundColor`: message to the object at that address. The following will do just that:

此处滚动条指示器的存储地址是`0x7fde6c484640`。给滚动条指示器上色所需要的步骤是发送一个`setBackgroundColor`信息到该地址的object。以下是具体操作：

```expr (void)[0x7fde6c484640 setBackgroundColor:[UIColor redColor]]```

Continue running the app, and you’ll notice the scroll indicator is red when you scroll the through the collection view.

继续运行app，大家可以注意到当我们滚动collection view里的滚动条时，滚动条指示器变红。


Congratulations, you now know the basics of view debugging with Xcode 6!

恭喜大家掌握了在Xcode 6中调试view的基本知识！

##Old School Debugging

##老式调试方法

Live view debugging made debugging views in Xcode 6 a lot easier, but that doesn’t mean that your favorite old school tricks are now useless. In fact, iOS 8 introduces a very welcome addition to the family of view debugging tricks: `_printHierarchy`.

实时view调试使得在Xcode 6中调试view变得更简单，但这并不意味着我们之前偏好的老式技巧一无是处。其实，iOS8为view调试技能包中引入了一个很受欢迎的附加物：`_printHierarchy`。

>Note: You’ve already learned the basics of view debugging with Xcode 6, so feel free to skip this optional section if you’d like. However if you’re curious about some handy old-school techniques, read on!

>注：大家已经掌握了在Xcode 6中调试view的基本知识，所以如果大家愿意，尽可以把这些可选部分省略掉。但是，大家如果对一些好用的老式技巧感兴趣的话，接着往下看！

###Printing the View Controller Hierarchy

###打印View Controller Hierarchy

`_printHierarchy` is a private method on `UIViewController` that you can use to print the **`view controller`** hierarchy to the console. Build and Run, select **`Push via storyboard`** and then hit the pause button in the Debug bar.

`_printHierarchy`是`UIViewController`的一个私有方法，我们可以用它来打印控制台的**`view controller`**层级。建立之后运行，选择**`view controller`**然后点击调试条上的暂停按钮。

Now type this in the console and press return:

现在在控制台输入这些信息，然后按return。

```po [[[[UIApplication sharedApplication] keyWindow] rootViewController] _printHierarchy]```

You’ll get something very similar to this:

我们得到的结果应该与以下结果类似：

```<UINavigationController 0x7fdf539216c0>, state: appeared, view: <UILayoutContainerView 0x7fdf51e33bc0>
   | <TableViewController 0x7fdf53921f10>, state: disappeared, view: <UITableView 0x7fdf5283fc00> not in the window
   | <DemoMessagesViewController 0x7fdf51d7d520>, state: appeared, view: <UIView 0x7fdf53c0b990>```

This tells you that there is a `UINavigationController` whose first view controller is a `TableViewController` — the one where you chose how to push the controller. The second view controller is the `DemoMessagesViewController`, or the view controller you have been debugging.

这表明，`UINavigationController`的第一个view controller是`TableViewController`--也就是我们选择如何push controller时的那个。第二个view controller是`DemoMessagesViewController`，也就是我们正在调试的view controller。

It doesn’t seem too exciting in this particular example, but if you have several child view controllers within a navigation controller, and a tab bar controller in a popover in a modal view controller, (I’m not proud of some of my apps’ UI…) it can be immensely useful for figuring out exactly how the view controller hierarchy works.

也许我举的这个特例并未让大家感到欣喜，但是如果大家的导航控制器中同时包含几个子视图控制器，并且modal view controller 中的popover包含一个tab bar controller（我其实也觉得我app里面的某些UI不是很好...），那么这对理解view controller层级的运作原理非常有帮助。

###Printing the View Hierarchy

###打印View层级

If you’re not a very visual person and prefer a textual overview of a view hierarchy, you can always use the age-old, and also private, `recursiveDescription` on `UIView`. This prints a view hierarchy very similar to the view controller hierarchy as demonstrated above.

如果大家的空间想象力不是很强并且倾向于直观地观看view层级的文本概述，大家可以使用`UIView`上传统的私有的`recursiveDescription` 。这里打印出的view层级与上述view controller层级非常相似。

Open **`Views\JSQMessagesCollectionViewCellOutgoing.m`** and add a breakpoint in `awakeFromNib`.

打开**`Views\JSQMessagesCollectionViewCellOutgoing.m`**并在`awakeFromNib`中添加一个断点。

![pic-34](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/breakpoint.png)

Build and Run, then select **`Push via Storyboard`**. The debugger should break as a `JSQMessagesCollectionViewCellOutgoing` is loaded. Now type the following into the console:

建立并运行，然后选择**`Push via Storyboard`**。`JSQMessagesCollectionViewCellOutgoing`加载的同时调试器会分裂。然后输入以下内容至控制台：

```po [self.contentView recursiveDescription]```

This will print the hierarchy of the `JSQMessagesCollectionViewCellOutgoing`’s `contentView`, which will look something like this:

这一步会打印出`JSQMessagesCollectionViewCellOutgoing`的`contentView`的层级，如下所示：

```<UIView: 0x7fde6c475de0; frame = (0 0; 312 170); gestureRecognizers = <NSArray: 0x7fde6c484fe0>; layer = <CALayer: 0x7fde6c474750>>
   | <JSQMessagesLabel: 0x7fde6c475eb0; baseClass = UILabel; frame = (0 0; 312 20); text = 'Today 10:58 PM'; clipsToBounds = YES; opaque = NO; autoresize = RM+BM; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x7fde6c476030>>
   | <JSQMessagesLabel: 0x7fde6c476400; baseClass = UILabel; frame = (0 20; 312 0); clipsToBounds = YES; opaque = NO; autoresize = RM+BM; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x7fde6c476580>>
   | <UIView: 0x7fde6c476b50; frame = (70 20; 210 150); autoresize = RM+BM; layer = <CALayer: 0x7fde6c474dd0>>
   |    | <UIImageView: 0x7fde6c482880; frame = (0 0; 210 150); opaque = NO; userInteractionEnabled = NO; layer = <CALayer: 0x7fde6c476ae0>> - (null)
   | <UIView: 0x7fde6c482da0; frame = (282 140; 30 30); autoresize = RM+BM; layer = <CALayer: 0x7fde6c482d00>>
   |    | <UIImageView: 0x7fde6c482e70; frame = (0 0; 30 30); opaque = NO; autoresize = RM+BM; userInteractionEnabled = NO; layer = <CALayer: 0x7fde6c482f70>> - (null)
   | <JSQMessagesLabel: 0x7fde6c483390; baseClass = UILabel; frame = (0 170; 312 0); clipsToBounds = YES; opaque = NO; autoresize = RM+BM; userInteractionEnabled = NO; layer = <_UILabelLayer: 0x7fde6c483510>>```

It’s rudimentary, but can be helpful when you want to debug a view hierarchy pre iOS 8.

这看起来非常基础，但是我们想要在pre iOS 8中调试view层级的话它还是非常有用的。

###Using debugQuickLookObject

###使用debugQuickLookObject

Lastly, Xcode 5.1 introduced a feature called **`Debug Quick Look`**. This feature is most useful when you’re already debugging and aren’t wondering what an object looks like at a certain point in your code.

最后，Xcode 5.1引入了一个叫做**`Debug Quick Look`**的特征。这个特征在我们已经进行调试并且知晓了代码中特定点的object的外形时非常有用。

Your custom class can implement the method `debugQuickLookObject` and return anything that is visually presentable by Xcode. Then, when you’re debugging and you have an object you want to inspect, you can use quick look and Xcode will show you a visual representation of that object.

我们的自定义类可以执行`debugQuickLookObject`方法并且返回Xcode中可视觉上显示的一切信息。然后，当我们在进行调试并且需要查看object时，我们可以使用quick look，Xcode会将该object视觉再现。

For example, `NSURL`’s implementation of `debugQuickLookObject` returns a `UIWebView` with that URL so you can actually see what’s behind the URL.

比如，`NSURL`执行 `debugQuickLookObject`时会返回一个`UIWebView`，这个`UIWebView`中含有URL，所以我们可以看到URL背后所包含的内容。

For more information on debugging using Quick Look, take a look at the [documentation](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/CustomClassDisplay_in_QuickLook/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014001-CH1-SW1).

更多关于使用Quick Look的调试信息，请参阅[文件材料](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/CustomClassDisplay_in_QuickLook/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014001-CH1-SW1)。

##Where To Go From Here?

##未来趋势在何方？

And that’s it for Live View Debugging. It’s an easy tool and can save hours of manually sifting through a view hierarchy trying to understand how and where it’s drawing views.

以上就是实时View调试的全部内容。这个工具简单实用，并且能够节省我们手动筛选view层级并试图理解其绘制视图的原理和地址的时间。

If you’re looking for a more advanced and comprehensive tool than just Xcode, then take a look at Reveal. While it’s a paid app, it’s also more powerful than Xcode’s view debugging. You can view our Tech Talk on the subject [over here](http://www.raywenderlich.com/99594/view-debugging-with-xcode-and-reveal-tech-talk-video).

如果大家想找一个比Xcode更先进更全面的工具，我建议大家看看Reveal。这是一个付费app，但是它的功能比Xcode的view调试更加强大。大家可以[点击此处](http://www.raywenderlich.com/99594/view-debugging-with-xcode-and-reveal-tech-talk-video)查看我们关于这方面的技术讲座。

We hope you enjoyed this tutorial and feel a little more comfortable debugging your UI. If you have comments or questions, please use the forum discussion below! 

希望大家喜欢本教程并且通过本教程的学习在调试UI时更得心应手。如果大家有任何建议或问题，欢迎在下方的论坛讨论区发言！













