Core Graphics教程：起步
---

>* 原文链接 : [Core Graphics Tutorial Part 1: Getting Started](http://www.raywenderlich.com/90690/modern-core-graphics-with-swift-part-1)
* 原文作者 : [Caroline Begbie ](http://www.raywenderlich.com/u/caroline)
* [译文出自 :  开发技术前线 www.devtf.cn](www.devtf.cn)
* 译者 : [sdq](https://github.com/sdq/)  [StormXX](https://github.com/StormXX/)
* 校对者:  暂无
* 状态 :   待校对

Imagine you’ve finished your app and it works just fine, but the interface lacks style. You could draw several sizes of all your custom control images in Photoshop and hope that Apple doesn’t come out with a @4x retina screen…

想象一下...当你完成了你的应用，它能够很好地运行，但是界面还缺少个人的风格。这时，你可以选择用PS画出一系列尺寸的图片，并希望Apple不会再推出一个**@4x**的retina显示尺寸...

Or, you could think ahead and use Core Graphics to create one image in code that scales crisply for any size device.

或者，你可以领先一步，采用Core Graphics进行绘图，保证一个图片可以适应各种尺寸的屏幕。

Core Graphics is Apple’s vector drawing framework – it’s a big, powerful API and there’s a lot to learn. But never fear – this three-part series will ease you into it by starting out simple, and by the end you’ll be able to create stunning graphics ready to use in your apps.

Core Graphics是苹果的一个矢量图绘制框架 - 它是一个大型且给力的API、有很多需要去学习。不过别慌，这个系列的文章会带你由简入深地学习Core Graphics，最后你将可以在你的应用中创作令人惊叹的图片。

This is a brand new series, with a modern approach to teaching Core Graphics. The series is fully up-to-date with Xcode 6 and Swift, and covers cool new features like @IBDesignable and @IBInspectable that make learning Core Graphics fun and easy.

这是一个全新的系列，采用最新的方式来学习Core Graphics。这个系列完全和Xcode6与Swift保持同步，并且包含了一些很酷的新特性，比如**@IBDesignable**和**@IBInspectable**，这让学习Core Graphics变得更有趣也更容易。

So grab your favorite beverage and let’s begin!

拿起可乐，让我们开始吧！

## Introducing Flo – One glass at a time

## 介绍Flo - 一款记录喝水App

You’ll be creating a complete app to track your drinking habits.

你将会完成一个追踪你喝水习惯的app。

Specifically, it makes it easy to track how much water you drink. “They” tell us that drinking eight glasses of water a day is healthy, but it’s easy to lose track after a few glasses. This is where Flo comes in; every time you polish off a refreshing glass of water, tap the counter. You’ll also see a graph of your previous seven days’ consumption.

Flo可以让你很轻松地追踪到你喝了多少水。人们说一天喝8杯水才是健康的，但是往往喝了几杯水之后我们就忘了记录了。这就是Flo使用的时候了，每当你喝完一杯水以后，你就可以在Flo上点击计数，Flo会为你记录喝水情况。你也可以在Flo上查看到过去7天的喝水状况。

![demo](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-CompletedApp.gif)

In the first part of this series, you’ll create three controls using UIKit’s drawing methods.

在这个系列的第一部分，你将会通过UIKit的画图方法创建三个控件。

Then in part two , you’ll have a deeper look at Core Graphics contexts and draw the graph.

第二部分中，你将会深入了解Core Graphics的内容并且学习绘图。

In part three , you’ll create a patterned background and award yourself a homemade Core Graphics medal. :]

最后第三部分，你将会制作一个样式背景，并且获得自制的Core Graphics奖牌 ：）

## Getting Started

## 让我们开始吧

Your first task is to create your very own Flo app. There is no download to get you going, because you’ll learn more if you build it from the ground up.

你的第一个任务是新建你自己的Flo应用。这边不会提供任何下载，因为只有从零开始，你才能学得更多。

Create a new project (File\New\Project…), select the template iOS\Application\Single View Application and click Next.

创建一个新项目**(File\New\Project…)**，选择模板**iOS\Application\Single View Application**，然后点击**Next**。

Fill out the project options. Set the Product Name to Flo, the Language to Swift and Devices to iPhone, and click Next.

弹出一个项目选项框，把项目名设为Flo，语言设置为Swift，设备选为iPhone，然后点击**Next**。

![start](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-Xcode.png)

On the final screen, uncheck Create Git repository and click Create.

在最后一步，不要选择Git repository，并点击**Create**。

You now have a starter project with a storyboard and a view controller.

现在你已经完成了一个拥有storyboard和view controller的初始项目

## Custom Drawing on Views

## 在View上画图

Whenever you want to do some custom drawing, you just need to take three steps:

1. Create a UIView subclass.
2. Override <code>drawRect(_:)</code> and add some Core Graphics drawing code.
3. There is no step 3 – that’s it! :]

Let’s try this out by making a custom-drawn plus button.

在画图的时候，你需要实行以下三个步骤：

1. 创建一个UIView子类。
2. 重写<code>drawRect(_:)</code>并添加一些Core Graphics绘图代码。
3. 想什么呢！没有第三步了 ：）

让我们尝试一下画一个自制的加号按钮。

![button](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-AddButtonFinal.png)

Create a new file **(File\New\File…)**, choose **iOS\Source\Cocoa Touch Class** and name the new class **PushButtonView**. Make it a subclass of **UIButton**, and ensure the language is **Swift**. Click **Next** and then **Create**.

创建一个新文件**(File\New\File…)**，选择**iOS\Source\Cocoa Touch Class**，取名为**PushButtonView**。让它继承于**UIButton**，确保选择的语言是**Swift**。点击**Next**并完成**Create**。

**UIButton** is a subclass of **UIView**, so all methods in **UIView**, such as <code>drawRect(_:)</code>, are also available in UIButton.

由于**UIButton**是**UIView**的子类，**UIView**的所有方法，像<code>drawRect(_:)</code>，在UIButton里同样存在。

In **Main.storyboard**, drag a **UIButton** into the view controller’s view, and select the button in the **Document Outline**.

在**Main.storyboard**里，将**UIButton**拖到View Controller的view里，并选择**Document Outline**。

In the **Identity Inspector**, change the class to use your own **PushButtonView**.

在**Identity Inspector**把类改成你自己的**PushButtonView**。

![pushButtonView](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-PushButtonViewClass.png)

In the Size Inspector, set X=250, Y=350, Width=100, and Height=100:

在**Size Inspector**里设置X=250, Y=350, Width=100, Height=100：

![sizeInspector](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-PlusButtonCoordinates.png)

###Auto Layout Constraints

###Auto Layout Constraints

Now you’ll set up the Auto Layout constraints (text instructions follow):

现在你将要设置Auto Layout constraints（文字指导在后面）:

![setUpAutoLayout](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-AutoLayout.gif)

* With the button selected, control-drag from the center of the button slightly left (still within the button), and choose Width from the popup menu.
* Similarly, with the button selected, control-drag from the center of the button slightly up (still within the button), and choose Height from the popup menu.
* Control-drag left from inside the button to outside the button, and choose Center Vertically in Container.
* Finally control-drag up from inside the button to outside the button and choose Center Horizontally in Container.

* 选中button，按住control从按钮的中心轻轻拖到左侧（仍旧在按钮内），在弹出的菜单中选择Width。
* 同样地，用上述方式从中间向上拖动（仍旧在按钮内），在弹出的菜单中选择Height。
* 按住control从按钮内向左拖动，直到按钮外，选择Center Vertically in Container。
* 最后，同样地由下至上从按钮内到按钮外，选择Center Horizontally in Container。
（以上几步中按住control和点击鼠标右键拖动的效果是一样的）


This will create the four required auto layout constraints, and you can now see them in the Size Inspector:

这几步会创建四个需要的auto layout constraints，你可以在Size Inspector看到他们：

![sizeInspector](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-AutoLayoutConstraints-247x500.png)

In the Attributes Inspector, remove the default title “Button”.

在Attributes Inspector里移除默认的文字“Button”。

![Attributes](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-RemoveTitle2.png)

You can build and run at this point if you’d like, but right now you’ll just see a blank screen at that point. Let’s fix that up!

你可以在此刻编译运行程序，不过现在你只能看到一片空白，让我们来继续吧！

## Drawing the Button

## 画一个按钮

Recall the button you’re trying to make is circular:

回忆一下，你试着画的按钮是一个圆形：

![button](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-AddButtonFinal.png)

To draw a shape in Core Graphics, you define a path that tells Core Graphics the line to trace (like two straight lines for the plus) or the line to fill (like the circle which should be filled here). If you’re familiar with Illustrator or the vector shapes in Photoshop, then you’ll easily understand paths.

为了实现在Core Graphics画一个图形，你需要定义一个路径来告诉Core Graphics如何画线（就像画一个加号需要两条直线）或者是需要填充的线（像这里的圆）。如果你很熟悉Illustrator或者Photoshop里的矢量图形，你可以很容易地就理解路径。

There are three fundamentals to know about paths:

这里有三个路径的基本原则：

* A path can be stroked and filled.
* A stroke outlines the path in the current stroke color.
* A fill will fill up a closed path with the current fill color.

* 路径可以被描边或者填充。
* 可以用当前的描边颜色为路径勾边。
* 可以用当前的填充颜色为闭合路径围起来的区域进行填充。


One easy way to create a Core Graphics path is through a handy class called UIBezierPath. This lets you easily create paths with a user-friendly API, whether you want to create paths based on lines, curves, rectangles, or a series of connected points.

创建Core Graphics路径的一个简单方法是采用一个好用的类UIBezierPath。这个类可以让你通过一个友好的API来创建路径，无论是你想基于线段、曲线、长方形或者是一系列的连接点。

Let’s try using UIBezierPath to create a path, and then fill it with a green color. To do this, open PushButtonView.swift and add this method:

让我们尝试用UIBezierPath来创建一个路径，然后用绿色来填充它。打开PushButtonView.swift，并添加以下方法：

<pre><code>override func drawRect(rect: CGRect) {
  var path = UIBezierPath(ovalInRect: rect)
  UIColor.greenColor().setFill()
  path.fill()
}</code></pre>

First you created an oval-shaped UIBezierPath that is the size of the rectangle passed to it. In this case, it’ll be the 100×100 button you defined in the storyboard, so the “oval” will actually be a circle.

首先，你创建了一个椭圆型的UIBezierPath，尺寸由长方形的大小决定。在本文的例子中，由于我们在storyboard里定义了100x100的按钮，所以“椭圆”其实是一个圆。

Paths themselves don’t draw anything. You can define paths without an available drawing context. To draw the path, you gave the current context a fill color, and then fill the path.

路径本身不会画任何东西。即使没有可绘图的上下文，你也可以定义路径。如果要绘制路径，你需要给当前的上下文一个填充颜色，然后填充路径。

Build and run the application, and you’ll see the green circle.

编译并运行应用，你会看到一个绿色的圆。

![haha](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-SimGreenButton2-192x320.png)

So far, you’ve discovered how easy it is to make custom-shaped views. You’ve done this by creating a UIButton subclass, overriding drawRect(_:) and adding the UIButton to your storyboard.

到这里，你会发现做一个自定义的view是如此简单。你通过创建一个UIButton的子类、重写<code>drawRect(_:)</code>、把UIButton放进你的storyboard中这几个简单的步骤完成了所有工作。

![haha](http://cdn4.raywenderlich.com/wp-content/uploads/2015/01/custom-views-not-bad-e1422223029562.png)

## Behind the Scenes in Core Graphics

## Core Graphics的背后实现

Each UIView has a graphics context, and all drawing for the view renders into this context before being transferred to the device’s hardware.

每一个UIView都会有一个图形上下文，所有针对这个UIView的绘图都会先绘制到此上下文，然后再转换到设备的屏幕上。

iOS updates the context by calling drawRect(_:) whenever the view needs to be updated. This happens when:

iOS更新上下文的方法是<code>drawRect(\_:)</code>，这个方法会在以下四种情况被调用：

* The view is new to the screen.
* Other views on top of it are moved.
* The view’s hidden property is changed.
* Your app explicitly calls the setNeedsDisplay() or setNeedsDisplayInRect() methods on the view.

* 屏幕第一次显示这个view。
* 它顶部的view被移除。
* view的hidden属性被修改。
* 在view上调用了了setNeedsDisplay()与setNeedsDisplayInRect()方法。

Note: Any drawing done in <code>drawRect(\_:)</code> goes into the view’s graphics context. Be aware that if you start drawing outside of <code>drawRect(_:)</code>, as you’ll do in the final part of this tutorial, you’ll have to create your own graphics context.

注意：所有图形上下文的绘画都需要在<code>drawRect(\_:)</code>中完成。最后一章中，你需要创建自己的图形上下文，你需要注意是不是在<code>drawRect(_:)</code>以外进行绘图了。

You haven’t used Core Graphics yet in this tutorial because UIKit has wrappers around many of the Core Graphics functions. A UIBezierPath, for example, is a wrapper for a CGMutablePath, which is the lower-level Core Graphics API.

你还没有在本教程中使用Core Graphics，因为UIKit封装了很多Core Graphics的方法。比如UIBezierPath就是对CGMutablePath的封装，CGMutablePath是Core Graphics的底层API。

Note: Never call <code>drawRect(_:)</code> directly. If your view is not being updated, then call setNeedsDisplay() on the view.

setNeedsDisplay() does not itself call <code>drawRect(\_:)</code>, but it flags the view as ‘dirty’, triggering a redraw using <code>drawRect(\_:)</code> on the next screen update cycle. Even if you call setNeedsDisplay() five times in the same method you’ll only ever actually call <code>drawRect(_:)</code> once.

注意：不要直接调用<code>drawRect(\_:)</code>，如果你的view并没有被更新，你可以在view上调用setNeedsDisplay()。setNeedsDisplay()不会自己调用<code>drawRect(\_:)</code>，但它会把view标记为“未刷新”，并触发<code>drawRect(\_:)</code>。所以，即使你在一个方法中调用了五次setNeedsDisplay()，但实际中只会运行一次<code>drawRect(_:)</code>。

## @IBDesignable – Interactive Drawing

## @IBDesignable – 可交互绘图

Creating code to draw a path and then running the app to see what it looks like can be about as exciting as watching paint dry, but you’ve got options. Live Rendering, a terrific new feature in Xcode 6, allows you to give a view to the @IBDesignable attribute. As you update the view in drawRect(_:), it’ll immediately update on the storyboard.

除了写绘图代码然后运行app看效果之外，你还有其他选择。一个Xcode6的新功能是Live Rendering，你可以设置view为@IBDesignable属性，当你在<code>drawRect(_:)</code>更新view的时候，它会马上在storyboard上展示出来。

Still in PushButtonView.swift, just before the class declaration, add:
<pre><code>@IBDesignable</code></pre>
This opens up Live Rendering to you.

在PushButtonView.swift里, 在类的声明前加上:
<pre><code>@IBDesignable</code></pre>
这样就可以开启Live Rendering。

Now set up your screen so that you have the storyboard and the code side by side.
Do this by selecting PushButtonView.swift to show the code, then at the top right, click the Assistant Editor — the icon that looks like two intertwined rings. The storyboard should then show on the right hand pane. If it doesn’t, you’ll have to choose the storyboard in the breadcrumb trail at the top of the pane:

现在把你的屏幕设置为同时可以看代码和storyboard。
方法是：选择PushButtonView.swift现实代码，在顶部右边点击Assistant Editor（它的图标是两个环绕的圆环）。storyboard会在右边展示。如果不是的话，你需要顶部选择到storyboard。

![3](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-Breadcrumbs-700x161.png)

Close the document outline at the left of the storyboard to free up some room. Do this either by dragging the edge of the document outline pane or clicking the button at the bottom of the storyboard:

关闭storyboard左边的文件导航栏，这样可以多留出一些空间。可以通过拖动边界或者点击storyboard左下方的按钮实现：

![1](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-DocumentOutline.png)

When you’re all done, your screen should look like this:

一切都完成后，你看到的是这样的：

![2](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-SideBySide-638x500.png)

In PushButtonView‘s drawRect(_:), change
UIColor.greenColor().setFill()
to
UIColor.blueColor().setFill()
and you’ll immediately see the change in the storyboard. Pretty cool!

在PushButtonView中把<code>drawRect(_:)</code>
<pre>UIColor.greenColor().setFill()</pre>
改成
<pre>UIColor.blueColor().setFill()</pre>
你会直接在storyboard上看到改变。非常酷~

![3](http://cdn1.raywenderlich.com/wp-content/uploads/2015/01/LiveRendering-700x477.png)

## Drawing Into the Context
## 在上下文进行绘图

Core Graphics uses a “painter’s model.”

When you draw into a context, it’s exactly like making a painting. You lay down a path and fill it, and then lay down another path on top and fill it. You can’t change the pixels that have been laid down, but you can “paint” over them.

This image from Apple’s documentation describes how this works. Just as it is when you’re painting on a canvas, the order in which you draw is critical.

Core Graphics使用的是“绘画者模型”。

当你在上下文中绘图时，就和真实世界中的绘图一样。你先画一条路径，然后填充颜色，接着你再画一条线，再继续填充。你不能改变已经画好的像素点，但是你可以在他们的上面继续作画。

这张苹果官方文档的图片描述了是如何运作的，当你在画板上绘图时，你画图的顺序是非常重要的

![4](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-PaintersModel.gif)

Your plus sign is going on top of the blue circle, so first you code the blue circle and then the plus sign.

你的加号符号应该在蓝色圆圈的上面，所以你需要先画圆，再画加号。

You could draw two rectangles for the plus sign, but it’s easier to draw a path and then stroke it with the desired thickness.

你可以画两个长方形来完成加号，但如果用粗线绘制路径会更简单一点。

Add this code to the end of drawRect(_:) to draw the horizontal dash of the plus sign:

把这些代码加在<code>drawRect(_:)</code>的最后，用来画加号的水平线：

<pre><code>
//set up the width and height variables
//for the horizontal stroke
let plusHeight: CGFloat = 3.0
let plusWidth: CGFloat = min(bounds.width, bounds.height) * 0.6
 
//create the path
var plusPath = UIBezierPath()
 
//set the path's line width to the height of the stroke
plusPath.lineWidth = plusHeight
 
//move the initial point of the path
//to the start of the horizontal stroke
plusPath.moveToPoint(CGPoint(
  x:bounds.width/2 - plusWidth/2,
  y:bounds.height/2))
 
//add a point to the path at the end of the stroke
plusPath.addLineToPoint(CGPoint(
  x:bounds.width/2 + plusWidth/2,
  y:bounds.height/2))
 
//set the stroke color
UIColor.whiteColor().setStroke()
 
//draw the stroke
plusPath.stroke()
</code></pre>

In this block, you set up a UIBezierPath, give it a start position (left side of the circle) and draw to the end position (right side of the circle). Then you stroke the path outline in white. At this point, you should see this in the Storyboard:

在这一部分中，你先设置了一个UIBezierPath，给它了一个起始为止（圆圈的左侧），然后画到重点（圆圈的右侧）。然后你设置画壁颜色为白色，这个时候你可以在Storyboard中看到如下：

In your storyboard, you’ll now have a blue circle with a dash in the middle of it:

在Storyboard中，你将看到蓝色圆圈中包含了一条水平的白线：

![1](http://cdn2.raywenderlich.com/wp-content/uploads/2015/01/Dash.png)

Note: Remember that a path simply consists of points. Here’s an easy way to grasp the concept: when creating the path imagine that you have a pen in hand. Put two dots on a page, then place the pen at the starting point, and then draw a line to the next point by drawing a line.
That’s essentially what you do with the above code by using moveToPoint(\_:) and addLineToPoint(_:).

注意：记住一条路径可以由点组成。这里有一个比较容易理解的理论：当你在创建路径时，想象此时你的手中握着笔。放两个点在纸上，然后把笔放在一个起点，然后往另一个点画线。这就是上述代码中moveToPoint(\_:)和addLineToPoint(_:)所做的事。

Now run the application on either an iPad 2 or an iPhone 6 Plus simulator, and you’ll notice the dash is not as crisp as it should be. It has a pale blue line encircling it.

现在在ipad2和iphone6+的模拟器上运行代码，你会发现横线没有之前显示的那么好了，在它周围产生了淡蓝色的边框。

![1](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-PixelledLine-700x170.png)

## Points and Pixels
## Points和Pixels

Back in the days of the very first iPhones, points and pixels occupied the same space and were the same size, making them essentially the same thing. When retina iPhones came into existence, suddenly there were quadruple the amounts of pixels on the screen for the same number of points.

在早期的iphone中，Points和Pixels所占的空间与大小是相同的。当retina屏幕的iphone问世后，屏幕上Pixels的数量变成了Points的四倍。

Similarly, the iPhone 6 Plus has once again increased the amount of pixels for the same points.

同样地，iphone6+在相同points的基础上再一次增加了pixels的数量。

Note: The following is conceptual – the actual hardware pixels may differ. For example, after rendering 3x, the iPhone 6 Plus downsamples to display the full image on the screen. To learn more about iPhone 6 Plus downsampling, check out this [great post](http://www.paintcodeapp.com/news/iphone-6-screens-demystified).

注意：以下只是概念 - 真实的硬件像素可能是不同的。如果要了解更多iphone6+的案例，可以看下这个[文章](http://www.paintcodeapp.com/news/iphone-6-screens-demystified)。

Here’s a grid of 12×12 pixels, where points are shown in gray and white. The first (iPad 2) is a direct mapping of points to pixels. The second (iPhone 6) is a 2x retina screen, where there are 4 pixels to a point, and the third (iPhone 6 Plus) is a 3x retina screen, where there are 9 pixels to a point.

这里是一个12x12的pixel矩阵，其中point以灰色和白色表示。第一个是ipad2，一个pixel对应一个point。第二个是iphone6，是一个2x的retina屏幕，4个pixel对应一个point。第三个是iphone6+，是一个3x的retina屏幕，9个pixel对应一个point。

![1](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-Pixels-700x263.png)

The line you’ve just drawn is 3 points high. Lines stroke from the center of the path, so 1.5 points will draw on either side of the center line of the path.

你画的线的高度是3points，两边的宽度各是1.5points。

This picture shows drawing a 3-point line on each of the devices. You can see that the iPad 2 and the iPhone 6 Plus result in the line being drawn across half a pixel — which of course can’t be done. So, iOS anti-aliases the half-filled pixels with a color half way between the two colors, and the line looks fuzzy.

这张图展示了3-point的线在各个设备上的情况。你可以看到ipad2和iphone6+都发生了覆盖一半pixel的情况，这是无法实现的。所以在实际中iOS会采用两种颜色的中间色，所以线看起来会模糊。

![1](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-PixelLineDemonstrated-700x263.png)

In reality, the iPhone 6 Plus has so many pixels, that you probably won’t notice the fuzziness, although you should check this for your own app on the device. But if you’re developing for non-retina screens like the iPad 2 or iPad mini, you should do anything you can to avoid anti-aliasing.

在实际中，iPhone6+有很多pixels，可能你不会注意到这些模糊的情况，但是你应该在你的app里注意尽量避免。不过如果你是为了非retina屏的设备进行开发，那么你就需要避免这类情况的发生。

If you have oddly sized straight lines, you’ll need to position them at plus or minus 0.5 points to prevent anti-aliasing. If you look at the diagrams above, you’ll see that a half point on the iPad 2 will move the line up half a pixel, on the iPhone 6, up one whole pixel, and on the iPhone 6 Plus, up one and a half pixels.

如果你要画奇数的直线，你需要加上或减去0.5points来避免模糊。如果你看一下上面的图表，你会看到增加0.5points相当于是在ipad上增加0.5pixels，在iphone6的线顶部上增加1pixel，在iphone6+的顶部增加1.5pixels。

In drawRect(_:), replace the moveToPoint and addLineToPoint code lines with:

在drawRect(_:)中，把moveToPoint与addLineToPoint的代码替换为：

<pre><code>
//move the initial point of the path
//to the start of the horizontal stroke
plusPath.moveToPoint(CGPoint(
  x:bounds.width/2 - plusWidth/2 + 0.5,
  y:bounds.height/2 + 0.5))
 
//add a point to the path at the end of the stroke
plusPath.addLineToPoint(CGPoint(
  x:bounds.width/2 + plusWidth/2 + 0.5,
  y:bounds.height/2 + 0.5))
</code></pre>

iOS will now render the lines sharply on all three devices because you’re now shifting the path by half a point.

现在在三种设备上都能很清晰地展示线条了，因为你对其进行了半个point的调整。

Note: For pixel perfect lines, you can draw and fill a UIBezierPath(rect:) instead of a line, and use the view’s contentScaleFactor to calculate the width and height of the rectangle. Unlike strokes that draw outwards from the center of the path, fills only draw inside the path.

注意：为了线条的完美，你可以采用填充UIBezierPath(rect:)方法来代替画线条，并通过view的contentScaleFactor计算出长方形的高和宽。和画线方法会从路径的中间点开始向外计算宽度，而填充只会在路径内部进行。

Add the vertical stroke of the plus just after the previous two lines of code, and before setting the stroke color in drawRect(_:). I bet you can figure out how to do this on your own, since you’ve already drawn a horizontal stroke:

在上述两行代码后面继续加上一条垂直线段，最后在drawRect(_:)里设置画线的颜色。我想在上述步骤之后，你应该已经知道该怎么完成了：

You should now see the live rendering of the plus button in your storyboard. This completes the drawing for the plus button.

你可以在storyboard中实时看到最新的图案，这是最终完成的加号按钮：

![1](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-FinishedPlus.png)

## @IBInspectable – Custom Storyboard Properties

## @IBInspectable – 自定义Storyboard属性

So you know that frantic moment when you tap a button more than needed, just to make sure it registers? Well, you need to provide a way for the user to reverse such overzealous tapping — you need a minus button.

你知道，必须要点击一个没有需求的按钮，却只是为了确认保存是一件多么令人抓狂的事情吗？所以，你需要提供一种方式让用户从这种过于泛滥的点击中解脱出来——你需要一个减号按钮。

A minus button is identical to the plus button except that it has no vertical bar and sports a different color. You’ll use the same PushButtonView class for the minus button, and declare what sort of button it is and its color when you add it to your storyboard.

减号按钮与加号按钮基本一致，除了它没有一条竖线和并且是另外一种颜色。你需要用同样的PushButtonView类来定义减号按钮，并且在你将按钮添加到storyboard时，需要声明这个按钮的类型和颜色。

@IBInspectable is an attribute you can add to a property that makes it readable by Interface Builder. This means that you will be able to configure the color for the button in your storyboard instead of in code.

为了使得按钮可以被Interfase Builder读取，你需要添加@IBInspectable这一变量给按钮的属性。这也意味着，你可以在storyboard里面定义按钮的颜色，而不是在代码中编写。

At the top of the PushButtonView class, add these two properties:

在PushButtonView类的顶部，添加两个属性：

<pre><code>
@IBInspectable var fillColor: UIColor = UIColor.greenColor()
@IBInspectable var isAddButton: Bool = true
</code></pre>

Change the fill color code at the top of drawRect(_:) from

从drawRect(_:)的顶部到

<pre><code>
UIColor.blueColor().setFill()
</code></pre>

to

更改和填充颜色相关的代码

<pre><code>
fillColor.setFill()
</code></pre>

The button will turn green in your storyboard view.

在storyboard的视图中，按钮就会变成绿色。

Surround the vertical line code in drawRect(_:) with an if statement:

和垂直相关的代码在drawRect(_:)中用if语句表示

<pre><code>
//Vertical Line
 
if isAddButton {
  //vertical line code moveToPoint(_:) and addLineToPoint(_:)
}
//existing code
//set the stroke color
UIColor.whiteColor().setStroke()
 
//draw the stroke
plusPath.stroke()
</code></pre>

This makes it so you only draw the vertical line if isAddButton is set – this way the button can be either a plus or a minus button.

这些代码让你只有在isAddButton被设置的时候才能够画垂直线，用这样的方式，一个按钮就可以既是加法按钮，又可以是减法按钮。

The completed PushButtonView looks like this:

完整的PushButtonView如下列所示:

<pre><code>
import UIKit
 
@IBDesignable
class PushButtonView: UIButton {
 
  @IBInspectable var fillColor: UIColor = UIColor.greenColor()
  @IBInspectable var isAddButton: Bool = true
 
  override func drawRect(rect: CGRect) {
 
    var path = UIBezierPath(ovalInRect: rect)
    fillColor.setFill()
    path.fill()
 
    //set up the width and height variables
    //for the horizontal stroke
    let plusHeight: CGFloat = 3.0
    let plusWidth: CGFloat = min(bounds.width, bounds.height) * 0.6     
 
    //create the path
    var plusPath = UIBezierPath()
 
    //set the path's line width to the height of the stroke
    plusPath.lineWidth = plusHeight
 
    //move the initial point of the path
    //to the start of the horizontal stroke
    plusPath.moveToPoint(CGPoint(
      x:bounds.width/2 - plusWidth/2 + 0.5,
      y:bounds.height/2 + 0.5))
 
    //add a point to the path at the end of the stroke
    plusPath.addLineToPoint(CGPoint(
      x:bounds.width/2 + plusWidth/2 + 0.5,
      y:bounds.height/2 + 0.5))
 
    //Vertical Line
    if isAddButton {
      //move to the start of the vertical stroke
      plusPath.moveToPoint(CGPoint(
        x:bounds.width/2 + 0.5,
        y:bounds.height/2 - plusWidth/2 + 0.5))
 
      //add the end point to the vertical stroke
      plusPath.addLineToPoint(CGPoint(
        x:bounds.width/2 + 0.5,
        y:bounds.height/2 + plusWidth/2 + 0.5))
    }
 
    //set the stroke color
    UIColor.whiteColor().setStroke()
 
    //draw the stroke
    plusPath.stroke()
 
  }
 
}
</code></pre>

In your storyboard, select the push button view. The two properties you declared with @IBInspectable appear at the top of the Attributes Inspector:

在storyboard中，选择push button view.你可以看到两个通过@IBInspectable声明的属性出现在Attributes Inspector的顶部。

Change Fill Color to RGB(87, 218, 213), and change the Is Add Button to off.

更改填充颜色为RGB(87, 218, 213),并且设置Is Add Button为off。

![1](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-InspectableFillColor.png)

The changes will take place immediately in the storyboard:

你可以立即在storyboard中看到相应的变化。

![1](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-InspectableMinusButton.png)

Pretty cool, eh? Now change Is Add Button back to on to return the button to a plus button.

很酷吧？现在把Is Add Button设为on来把按钮设为加号。


### A Second Button

### 第二个按钮

Add a new UIButton to the storyboard, select it, and update the size and position in the Size Inspector to set X=275, Y=480, Width=50, and Height=50:

添加一个UIButton到StoryBoard,选中，并在尺寸查看器（size inspector）中更新按钮的位置和大小，这里设置为X=275，Y=480，Width=50，以及Height=50：

![1](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-PushButtonMinusCoords.png)

In the Identity Inspector, change the UIButton class to PushButtonView.

在Identity Inspector中，选择类型（Class）的下拉菜单，将UIButton更改为PushButtonView。

![1](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-PushButtonMinusClass.png)

The green plus button will be drawn under your old plus button.
In the Attributes Inspector, change Fill Color to RGB(238, 77, 77) and change Is Add Button to off.
Remove the default title Button.

一个绿色的plus button功能按钮就会覆盖掉之前的按钮。
在Attributes inspector中，将填充颜色设置为RGB（238，77，77），并将 Is Add Button设置为off，将default title button取消。

![1](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-MinusButtonColor.png)

Add the auto layout constraints for the new view similarly to how you did before:

按照之前的步骤，给新的视图添加自动布局的约束条件。

* With the button selected, control-drag from the center of the button slightly left (still within the button), and choose Width from the popup menu.
* Similarly, with the button selected, control-drag from the center of the button slightly up (still within the button), and choose Height from the popup menu.
* Control-drag up from inside the button to outside the button and choose Center Horizontally in Container.
* Control-drag up from the bottom button to the top button, and choose Vertical Spacing.

* 选中按钮，按住"Ctrl"键鼠标拖动按钮的中间位置向左（拖动范围在按钮以内），在弹出菜单中选择Width。
* 相似的，在按钮选中状态下，按住"Ctrl"键鼠标拖动按钮的中间位置向上（拖动范围在按钮以内），在弹出菜单中选择Height。
* 按住"Ctrl"键鼠标向上拖动，从按钮里面拖动到按钮范围外，选择Center Horizontally in Containner。
* 按住"Ctrl"键，鼠标从最底部的按钮拖动到顶部按钮，可以选择Vertical Spacing。

Build and run the application. You now have a reusable customizable view that you can add to any app. It’s also crisp and sharp on any size device. Here it is on the iPhone 4S.

编译并运行应用，你就可以获得一个可复用的自定义视图，并可以添加到任意app中。并且它还可以适用于任意设备的屏幕尺寸。这里给大家展示的是在iPhone 4S上面的效果。

![1](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-SimPushButtons-339x500.png)

## Arcs with UIBezierPath
## 用UIBezierPath来画弧

The next customized view you’ll create is this one:

下一个你要自定义的View就是下面这个：

![1-CompletedCounterView](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-CompletedCounterView.png)


This looks like a filled shape, but the arc is actually just a fat stroked path. The outlines are another stroked path consisting of two arcs.

这个看起来像是一个被填满的弧形，但是这个弧确确实实仅仅是一个粗笔画路径。而轮廓又是另一个由两个弧组成的粗笔画路径。

Create a new file, File\New\File…, choose Cocoa Touch Class, and name the new class CounterView. Make it a subclass of UIView, and ensure the language is Swift. Click Next, and then click Create.
Replace the code with:

新建一个文件，File\New\File...，选择Cocoa Touch Class，然后命名这个类为CounterView。同时，让它为UIView的一个子类，并确认一下编程语言选择的是Swift。点击Next按钮，然后点击创建。然后用下面的代码把生成的代码覆盖掉：

	import UIKit
 
	let NoOfGlasses = 8
	let π:CGFloat = CGFloat(M_PI)
 
	@IBDesignable class CounterView: UIView {
 
 		@IBInspectable var counter: Int = 5 
  		@IBInspectable var outlineColor: UIColor = UIColor.blueColor()
  		@IBInspectable var counterColor: UIColor = UIColor.orangeColor()
 
  		override func drawRect(rect: CGRect) {
  		
 		}
	}
	
Note: Now that Apple allows Unicode characters in constant and variable names, you can use π as a constant for pi that will make your code more readable. You type in π by pressing Alt and P at the same time.

注意：既然Apple允许在常量(let)和变量(var)中使用Unicode编码的字符，你可以把π作为一个常量的名字，用它来储存pi，这样能提高代码的可读性。如何输入π：同时按下Alt和P即可。

Here you create two constants. NoOfGlasses is the target number of glasses to drink per day. When this figure is reached, the counter will be at its maximum.
You also create three @IBInspectable properties that you can update in the storyboard. The variable counter keeps track of the number of glasses consumed, and it’s useful to have the ability to change it in the storyboard, especially for testing the counter view.

在这个代码中，你创建了两个常量。NoOfGlasses 代表着每天需要喝水的目标杯数。当计数到达了这个目标杯数的时候，这个计数器就到了它的最大值。
同时你也创建了3个你可以在storyboard里修改的@IBInspectable属性。这个counter变量用来记录当前已经喝的杯数。对于这个变量，能在storyboard里面修改它是很有必要的，尤其是当测试这个CounterView的时候。

Go to Main.storyboard and add a UIView above the plus PushButtonView.

In the Size Inspector, set X=185, Y=70, Width=230, and Height=230:

打开Main.storyboard然后在“加号”按钮上添加一个UIView

在右边的Size Insepector面板里，设置X=185, Y=70, Width=230, 还有 Height=230：

![1-CounterViewCoords](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-CounterViewCoords.png)

Add the auto layout constraints for the new view similarly to how you did before:
给这个新的view添加几个自动布局的约束，就像你之前做的那样：

- With the view selected, control-drag from the center of the view(原文这里是button，应该是错误的) slightly left (still within the view), and choose Width from the popup menu.
- Similarly, with the view selected, control-drag from the center of the view(同上) slightly up (still within the view), and choose Height from the popup menu.
- Control-drag up from inside the view to outside the view and choose Center Horizontally in Container.
- Control-drag down from the view to the top button, and choose Vertical Spacing.

- 选中这个View,按住control从这个view的中心轻轻的向左边拖动鼠标，然后释放（仍然在这个view内），从弹出来的菜单中选择Width
- 同理，选中这个View，按住control从这个view的中心轻轻的向上面拖动鼠标，然后释放（仍然在这个view内），从弹出来的菜单中选择Height
- 按住control然后从这个view中拖动鼠标到外面的view然后释放，选择Center Horizontally in Container。
- 按住control然后从这个view往下拖动鼠标，一直到上面一个button然后释放，选择Vertical Spacing.

In the Identity Inspector, change the class of the UIView to CounterView. Any drawing that you code in drawRect(_:) will now show up in the view.

在右边的Identity Inspector面板中，把这个UIView所属的类改成CounterView。现在你在drawRect(_:)中的代码将会生效。


## Impromptu Math Lesson
## 温习数学知识

We interrupt this tutorial for a brief, and hopefully un-terrifying look back at high school level math. As Douglas Adams would say – Don’t Panic! :]

我们稍微打断一下这个教程，然后我们回顾一下高中级别的数学知识，千万不要害怕哦！就像Douglas Adams所说 - 不要慌张! :]

Drawing in the context is based on this unit circle. A unit circle is a circle with a radius of 1.0.

在这个context里画任何东西，都基于这个单元圆。一个单元圆就是半径为1的圆。

![1-FloUnitCircle](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-FloUnitCircle.png)

The red arrow shows where your arc will start and end, drawing in a clockwise direction. You’ll draw an arc from the position 3π / 4 radians — that’s the equivalent of 135º, clockwise to π / 4 radians – that’s 45º.

红色的箭头表示你的弧从哪里开始和结束，并且是按顺时针方向。接下来，你将从 3π/4弧度(135º)的位置开始画弧，然后顺时针到 π/4弧度（45º）

Radians are generally used in programming instead of degrees, and it’s useful to be able to think in radians so that you don’t have to convert to degrees every time you want to work with circles. Later on you’ll need to figure out the arc length, which is when radians will come into play.

在程序中，弧度经常被使用，而不是用度数。用弧度是非常有利的，因为你不必每次跟圆打交道的时候都要转换为度数。接下来，你需要计算一下这个弧的长度，当然是用弧度来算。

An arc’s length in a unit circle (where the radius is 1.0) is the same as the angle’s measurement in radians. For example, looking at the diagram above, the length of the arc from 0º to 90º is π/2. To calculate the length of the arc in a real situation, take the unit circle arc length and multiply it by the actual radius.

在单元圆（半径为1）中，弧的长度等于这个角度的用弧度表示的数值。比如说，看上面的哪个图，从0º到90º的弧的长度是 π/2。在真实场景中计算弧的长度，用单位圆的弧的长度乘以真实的圆的半径。

To calculate the length of the red arrow above, you would simply need to calculate the number of radians it spans:

计算上面的红色箭头的长度，只要计算它所跨过的弧度数值。

          2π – end of arrow (3π/4) + point of arrow (π/4) = 3π/2
          
In degrees that would be:

用角度表示就是：

          360º – 135º + 45º = 270º
          
![celebrate-all-the-maths](http://cdn1.raywenderlich.com/wp-content/uploads/2015/01/celebrate-all-the-maths-e1422223051445.png)

## Back to Drawing Arcs
## 回到画弧

In CounterView.swift, add this code to drawRect(_:) to draw the arc:

	// 1
	let center = CGPoint(x:bounds.width/2, y: bounds.height/2)
 
	// 2
	let radius: CGFloat = max(bounds.width, bounds.height)
 
	// 3
	let arcWidth: CGFloat = 76
 
	// 4
	let startAngle: CGFloat = 3 * π / 4
	let endAngle: CGFloat = π / 4
 
	// 5
	var path = UIBezierPath(arcCenter: center,
	  radius: radius/2 - arcWidth/2,
	  startAngle: startAngle,
	  endAngle: endAngle,
	  clockwise: true)
 
	// 6
	path.lineWidth = arcWidth
	counterColor.setStroke()
	path.stroke()


Imagine drawing this with a compass — you’d put the point of the compass in the center, open the arm to the radius you need, load it with a thick pen and spin it to draw your arc.
In this code, center is the point of the compass, the radius is the width that the compass is open (less half the width of the pen) and the arc width is the width of the pen.

想象一下用圆规来画这个，首先把圆规的一个点固定在中间，然后打开圆规臂选择你需要的半径，然后装上一个细港币，然后旋转它来画弧。
在这个代码中，center变量代表着圆规的不动点，半径就是圆规打开的宽度（小于钢笔宽度的一半）并且这个弧的宽度就是这个钢笔笔头的宽度。

The following explains what each section does:
下面的列表解释了上面代码中每个小节的意思：

1. Define the center point of the view where you’ll rotate the arc around.
2. Calculate the radius based on the max dimension of the view.
3. Define the thickness of the arc.
4. Define the start and end angles for the arc.
5. Create a path based on the center point, radius, and angles you just defined.
6. Set the line width and color before finally stroking the path.

---
1. 定义这个view的中点，然后你将会基于这个点来通过旋转生成弧。
2. 通过view的宽和高的最大值来计算弧的半径。
3. 定义这个弧的粗细
4. 为这个弧定义开始和结束的角度。
5. 基于刚刚创建的中点，半径和角度来定义个一个弧的轨道。
6. 在给这个轨道上色之前，设置这个弧的轨道的宽度和颜色。

Note: When you’re drawing arcs, this is generally all you need to know, but if you want to dive further into drawing arcs, then Ray’s (older) Core Graphics Tutorial on Arcs and Paths will help.

注意：当你在尝试画弧的时候，这些一般是你需要知道的，如果你想在画弧方面研究更深层次一点，你可以尝试阅读Raywenderlich老版本的 [Core Graphics Tutorial on Arcs and Paths](http://www.raywenderlich.com/33193/core-graphics-tutorial-arcs-and-paths)

In the storyboard and when you run your application, this is what you’ll see:

在storyboard里或者是你运行你的应用的时候，下面这个就是你可以看到的

![1-SimArcStroke](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-SimArcStroke.png)

## Outlining the Arc
## 如何给弧画轮廓

When the user indicates they’ve enjoyed a glass of water, an outline on the counter shows the progress towards the goal of eight glasses.

当用户表示他们已经享受了一杯圣水之后（=。=），这个计数器外面的线条用来表示进度--离8杯还有多少。

This outline will consist of two arcs, one outer and one inner, and two lines connecting them.
In CounterView.swift , add this code to the end of drawRect(_:):

这个外面的线由两个弧组成，一个外部一个内部，并且还有两条线来闭合他们。
在CounterView.swift里，把下面的代码加在drawRect(_:):的最后面：

	//Draw the outline
 
	//1 - first calculate the difference between the two angles
	//ensuring it is positive
	let angleDifference: CGFloat = 2 * π - startAngle + endAngle
 
	//then calculate the arc for each single glass
	let arcLengthPerGlass = angleDifference / CGFloat(NoOfGlasses)
 
	//then multiply out by the actual glasses drunk
	let outlineEndAngle = arcLengthPerGlass * CGFloat(counter) + startAngle
 
	//2 - draw the outer arc
	var outlinePath = UIBezierPath(arcCenter: center, 
   	                               radius: bounds.width/2 - 2.5,
   	                           startAngle: startAngle, 
   	                             endAngle: outlineEndAngle,
   	                            clockwise: true)
 
	//3 - draw the inner arc
	outlinePath.addArcWithCenter(center, 
   	                  radius: bounds.width/2 - arcWidth + 2.5,
   	              startAngle: outlineEndAngle, 
   	                endAngle: startAngle, 
   	               clockwise: false)
 
	//4 - close the path
	outlinePath.closePath()
 
	outlineColor.setStroke()
	outlinePath.lineWidth = 5.0
	outlinePath.stroke()
	
A few things to go through here:
下面解释一下各个小节代码的意思：

1. outlineEndAngle is the angle where the arc should end, calculated using the current counter value.
2. outlinePath is the outer arc. The radius is given to UIBezierPath() to calculate the actual length of the arc, as this arc is not a unit circle.
3. Adds an inner arc to the first arc. This has the same angles but draws in reverse (clockwise is set to false). Also, this draws a line between the inner and outer arc automatically.
4. Closing the path automatically draws a line at the other end of the arc.

---
1. oulineEndAngle表示这个弧应该结束的角度，用当前的counter的值来计算。
2. outlinePath 就是外面的弧。由于这个弧不是基于单元圆的，所以把半径传给UIBezierPath()这个方法来计算这个弧的真实长度。
3. 在第一个弧的基础上再加一个内部的弧。这个和前面一个弧的角度一样但是是反向的（clockwise的值置为false）。同时，这一段会自动生成一个直线连接内部弧还外部的弧。
4. 在另一个弧的末尾自动画一条直线来关闭整个弧的路径。

With the counter property in CounterView.swift set to 5, your CounterView should now look like this in the storyboard:

因为你在CounterView.swift中将counter属性设置为5，你的CounterView现在应该在storyboard中看起来像下面这样：

![1-ArcOutline](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-ArcOutline.png)

Open Main.storyboard, select the CounterView and in the Attributes Inspector, change the Counter property to check out your drawing code. You’ll find that it is completely interactive. Try adjusting the counter to be more than eight and less than zero. You’ll fix that up later on.

打开 Main.storyboard, 选择CounterView然后在右边的Attributes Insepector里，改变Counter的数值来检查你写在drawRect里的代码是否正确。然后你惊喜的发现这个storyboard里面也跟着改变了，它竟然是可以交互的。尝试改变counter的数值去超过8或者小于0。接下来你会修改这个。

Change the Counter Color to RGB(87, 218, 213), and change the Outline Color to RGB(34, 110, 100).
改变这个Counter的颜色为RGB(87, 218, 213),并且把外面一圈的颜色改为RGB(34, 110, 100)。

![1-CounterView](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-CounterView.png)

## Making it All Work
## 让一切都能够正常工作

Congrats! You have the controls, now all you have to do is wire them up so the plus button increments the counter, and the minus button decrements the counter.

祝贺你！你现在已经能够掌控这些了，现在你需要做的就是点击加号按钮来增加这个计数器的数值，或者是点击减号按钮来减少这个计数器的数值。

In Main.storyboard, drag a UILabel to the center of the Counter View, and make sure it is a subview of the Counter View.

In the Size Inspector, set X=93, Y=93, Width=44, and Height=44:

在Main.storyboard中，拖拽一个UILable控件，放置在CounterView的中间。让它成为CounterView的子View。

然后在右边的Size Inspector中，设置X=93, Y=93, Width=44, 还有Height=44：

![1-LabelCoords](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-LabelCoords.png)

In the Attributes Inspector, change Alignment to center, font size to 36 and the default Label title to 8.

在右边的Attributes Inspector，改变Alignment参数为center,font size为36并且默认的Label title为8.

![1-LabelAttributes](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-LabelAttributes.png)

Go to ViewController.swift and add these properties to the top of the class:

打开ViewController.swift然后添加下面的属性到这个类的顶部：

	//Counter outlets
	@IBOutlet weak var counterView: CounterView!
	@IBOutlet weak var counterLabel: UILabel!
	
Still in ViewController.swift, add this method to the end of the class:

还是在ViewController.swift里，添加下面的方法到这个类的最下面。

	@IBAction func btnPushButton(button: PushButtonView) {
  	if button.isAddButton {
   	 counterView.counter++
  	} else {
   	 if counterView.counter > 0 {
   	   counterView.counter--
   	 }
  	}
  	counterLabel.text = String(counterView.counter)
	}
	
Here you increment or decrement the counter depending on the button’s isAddButton property, make sure the counter doesn’t drop below zero — nobody can drink negative water. :] You also update the counter value in the label.

现在，你对计数器的增加或者减少都依赖于按钮的isAddButton属性，确保计数器不会降到0以下-没人会喝负数杯数的水。:] 同时你也要在更新label上面的计数器数值。

Also add this code to the end of viewDidLoad() to ensure that the initial value of the counterLabel will be updated:
把下面这段代码也加入到viewDidLoad()的底部，确保这个counterLabel的初始数值也被赋值到。

	counterLabel.text = String(counterView.counter)
	
In Main.storyboard, connect the CounterView outlet and UILabel outlet. Connect the method to the Touch Up Inside event of the two PushButtonViews.

在Main.storyboard中，连接CounterView outlet和UILabel outlet到ViewController中，同时连接两个按钮的Touch Up Inside事件到ViewController里面的相关方法上。

![1-ConnectingOutlets2](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-ConnectingOutlets2.gif)

Run the application and see if your buttons update the counter label. They should.

运行这个应用，然后看看是否你的按钮能够更新这个label上面的数字，他们应该能行的。

But wait, why isn’t the counter view updating?
等等！为什么这个CounterView上面的进度没有更新？

Think way back to the beginning of this tutorial, and how you only call drawRect(_:) when other views on top of it are moved, or its hidden property is changed, or the view is new to the screen, or your app calls the setNeedsDisplay() or setNeedsDisplayInRect() methods on the view.

回想一下在这个教程的开始，drawRect(_:)方法只有在某些情况下会被调用，比如：遮住它的view被移开的时候，或者是它的hidden属性被改变的时候，或者是这个view第一次出现在屏幕上，或者是你的app调用了setNeedsDisplay()或者setNeedsDisplayInRect()方法。

However, the Counter View needs to be updated whenever the counter property is updated, otherwise the user will think your app is busted.

然而，一旦counter属性改变的时候，CounterView也必须改变它的进度条。否则，用户就会认为你的app毫无用处。

Go to CounterView.swift and change the counter property declaration to:

打开CounterView.swift 然后改变这个counter属性声明为下面的代码：

	@IBInspectable var counter: Int = 5 {
  		didSet {
   	 		if counter <=  NoOfGlasses {
   	   		//the view needs to be refreshed
   	   		setNeedsDisplay()
   	 		}
  		}
	}
	
This code makes it so that the view refreshes only when the counter is less than or equal to the user’s targeted glasses, as the outline only goes up to 8.

这段代码使CounterView仅仅在counter的数值小于或者等于用户的每天目标杯数的时候才会刷新，因为外面的轮廓只会最高到8.

Run your app again. Everything should now be working properly.

再一次运行你的app，一切都正常工作起来。

![1-Part1Finished](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-Part1Finished.png)

## Where to Go From Here?
## 接下来何去何从？

You’ve covered basic drawing in this tutorial, and you should now be able to change the shape of views in your UIs. But wait – there’s more! In Part 2 of this tutorial , you’ll explore Core Graphics contexts in more depth and create a graph of your water consumption over time.

在这个教程你已经学习到了基本的绘图，现在你应该能够在你的UI里改变你的view的形状。但是等等，还有很多需要学习！在这篇教程的[第二部分](http://www.raywenderlich.com/?p=90693)，你将会尝试更深层次探索Core Graphics context并且创造一个记录你的喝水杯数的图表。

You can download the project with all the code up to this point.
If you have any questions or comments please join the forum discussion below.

你可以[下载](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part1-6.31.zip)这个项目的所有代码，如果你有任何问题和评论，在下面的讨论中提出来。