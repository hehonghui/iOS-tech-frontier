Core Graphics教程：起步
---

>* 原文链接 : [Core Graphics Tutorial Part 1: Getting Started](http://www.raywenderlich.com/90690/modern-core-graphics-with-swift-part-1)
* 原文作者 : [Caroline Begbie ](http://www.raywenderlich.com/u/caroline)
* 译文出自 :  [开发技术前线](www.devtf.cn)
* 译者 : [sdq](https://github.com/sdq/)  [StormXX](https://github.com/StormXX/)
* 校对者:  [StormXX](https://github.com/StormXX/)


想象一下...当你完成了你的应用，它能够很好地运行，但是界面还缺少个人的风格。这时，你可以选择用PS画出一系列尺寸的图片，并希望Apple不会再推出一个**@4x**的retina显示尺寸...   

或者，你可以领先一步，采用Core Graphics进行绘图，保证一个图片可以适应各种尺寸的屏幕。   

Core Graphics是苹果的一个矢量图绘制框架 - 它是一个大型且给力的API、有很多需要去学习。不过别慌，这个系列的文章会带你由简入深地学习Core Graphics，最后你将可以在你的应用中创作令人惊叹的图片。   

这是一个全新的系列，采用最新的方式来学习Core Graphics。这个系列完全和Xcode6与Swift保持同步，并且包含了一些很酷的新特性，比如**@IBDesignable**和**@IBInspectable**，这让学习Core Graphics变得更有趣也更容易。

拿起可乐，让我们开始吧！

## 介绍Flo - 一款记录喝水杯数的App

你将会完成一个追踪你喝水习惯的app。   

Flo可以让你很轻松地追踪到你喝了多少水。人们说一天喝8杯水才是健康的，但是往往喝了几杯水之后我们就忘了记录了。这就是Flo使用的时候了，每当你喝完一杯水以后，你就可以在Flo上点击计数，Flo会为你记录喝水情况。你也可以在Flo上查看到过去7天的喝水状况。

![demo](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-CompletedApp.gif)

在这个系列的第一部分，你将会通过UIKit的画图方法创建三个控件。
第二部分中，你将会深入了解Core Graphics的内容并且学习绘图。
最后第三部分，你将会制作一个样式背景，并且获得自制的Core Graphics奖牌 ：）

## 让我们开始吧

你的第一个任务是新建你自己的Flo应用。这边不会提供任何下载，因为只有从零开始，你才能学得更多。   

创建一个新项目**(File\New\Project…)**，选择模板**iOS\Application\Single View Application**，然后点击**Next**。   
 
弹出一个项目选项框，把项目名设为Flo，语言设置为Swift，设备选为iPhone，然后点击**Next**。   

![start](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-Xcode.png)

在最后一步，不要选择Git repository，并点击**Create**。   
现在你已经完成了一个拥有storyboard和view controller的初始项目

## 在View上画图

在画图的时候，你需要实行以下三个步骤：

1. 创建一个UIView子类。
2. 重写<code>drawRect(_:)</code>并添加一些Core Graphics绘图代码。
3. 想什么呢！没有第三步了 ：）

让我们尝试一下画一个自制的加号按钮。

![button](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-AddButtonFinal.png)

创建一个新文件**(File\New\File…)**，选择**iOS\Source\Cocoa Touch Class**，取名为**PushButtonView**。让它继承于**UIButton**，确保选择的语言是**Swift**。点击**Next**并完成**Create**。   
由于**UIButton**是**UIView**的子类，**UIView**的所有方法，像<code>drawRect(_:)</code>，在UIButton里同样存在。   
在**Main.storyboard**里，将**UIButton**拖到View Controller的view里，并选择**Document Outline**。   
在**Identity Inspector**把类改成你自己的**PushButtonView**。

![pushButtonView](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-PushButtonViewClass.png)

在**Size Inspector**里设置X=250, Y=350, Width=100, Height=100：

![sizeInspector](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-PlusButtonCoordinates.png)

## 自动布局约束

现在你将要设置自动布局约束（文字指导在后面）:

![setUpAutoLayout](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-AutoLayout.gif)

* 选中button，按住control从按钮的中心轻轻拖到左侧（仍旧在按钮内），在弹出的菜单中选择Width。
* 同样地，用上述方式从中间向上拖动（仍旧在按钮内），在弹出的菜单中选择Height。
* 按住control从按钮内向左拖动，直到按钮外，选择Center Vertically in Container。
* 最后，同样地由下至上从按钮内到按钮外，选择Center Horizontally in Container。
（以上几步中按住control和点击鼠标右键拖动的效果是一样的）
这几步会创建四个需要的auto layout constraints，你可以在Size Inspector看到他们：

![sizeInspector](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-AutoLayoutConstraints-247x500.png)

在Attributes Inspector里移除默认的文字“Button”。

![Attributes](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-RemoveTitle2.png)

你可以在此刻编译运行程序，不过现在你只能看到一片空白，让我们来继续吧！

## 画一个按钮

回忆一下，你试着画的按钮是一个圆形：

![button](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-AddButtonFinal.png)

为了实现在Core Graphics画一个图形，你需要定义一个路径来告诉Core Graphics如何画线（就像画一个加号需要两条直线）或者是需要填充的线（像这里的圆）。如果你很熟悉Illustrator或者Photoshop里的矢量图形，你可以很容易地就理解路径。
这里有三个路径的基本原则：

* 路径可以被描边或者填充。
* 可以用当前的描边颜色为路径勾边。
* 可以用当前的填充颜色为闭合路径围起来的区域进行填充。

创建Core Graphics路径的一个简单方法是采用一个好用的类UIBezierPath。这个类可以让你通过一个友好的API来创建路径，无论是你想基于线段、曲线、长方形或者是一系列的连接点。   
让我们尝试用UIBezierPath来创建一个路径，然后用绿色来填充它。打开PushButtonView.swift，并添加以下方法：

	override func drawRect(rect: CGRect) {
	  var path = UIBezierPath(ovalInRect: rect)
	  UIColor.greenColor().setFill()
 	 path.fill()
	}

首先，你创建了一个椭圆型的UIBezierPath，尺寸由长方形的大小决定。在本文的例子中，由于我们在storyboard里定义了100x100的按钮，所以“椭圆”其实是一个圆。   
路径本身不会画任何东西。即使没有可绘图的上下文，你也可以定义路径。如果要绘制路径，你需要给当前的上下文一个填充颜色，然后填充路径。   

编译并运行应用，你会看到一个绿色的圆。

![haha](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-SimGreenButton2-192x320.png)

到这里，你会发现做一个自定义的view是如此简单。你通过创建一个UIButton的子类、重写<code>drawRect(_:)</code>、把UIButton放进你的storyboard中这几个简单的步骤完成了所有工作。

![haha](http://cdn4.raywenderlich.com/wp-content/uploads/2015/01/custom-views-not-bad-e1422223029562.png)

## Core Graphics的背后实现

每一个UIView都会有一个图形上下文，所有针对这个UIView的绘图都会先绘制到此上下文，然后再转换到设备的屏幕上。

iOS更新上下文的方法是<code>drawRect(\_:)</code>，这个方法会在以下四种情况被调用：

* 屏幕第一次显示这个view。
* 它顶部的view被移除。
* view的hidden属性被修改。
* 在view上调用了了setNeedsDisplay()与setNeedsDisplayInRect()方法。

注意：所有图形上下文的绘画都需要在<code>drawRect(\_:)</code>中完成。最后一章中，你需要创建自己的图形上下文，你需要注意是不是在<code>drawRect(_:)</code>以外进行绘图了。

你还没有在本教程中使用Core Graphics，因为UIKit封装了很多Core Graphics的方法。比如UIBezierPath就是对CGMutablePath的封装，CGMutablePath是Core Graphics的底层API。

Note: Never call <code>drawRect(_:)</code> directly. If your view is not being updated, then call setNeedsDisplay() on the view.

注意：不要直接调用<code>drawRect(\_:)</code>，如果你的view并没有被更新，你可以在view上调用setNeedsDisplay()。setNeedsDisplay()不会自己调用<code>drawRect(\_:)</code>，但它会把view标记为“未刷新”，并触发<code>drawRect(\_:)</code>。所以，即使你在一个方法中调用了五次setNeedsDisplay()，但实际中只会运行一次<code>drawRect(_:)</code>。

## @IBDesignable – 可交互绘图

除了写绘图代码然后运行app看效果之外，你还有其他选择。一个Xcode6的新功能是Live Rendering，你可以设置view为@IBDesignable属性，当你在<code>drawRect(_:)</code>更新view的时候，它会马上在storyboard上展示出来。

在PushButtonView.swift里, 在类的声明前加上:
<pre><code>@IBDesignable</code></pre>
这样就可以开启Live Rendering。

现在把你的屏幕设置为同时可以看代码和storyboard。
方法是：选择PushButtonView.swift现实代码，在顶部右边点击Assistant Editor（它的图标是两个环绕的圆环）。storyboard会在右边展示。如果不是的话，你需要顶部选择到storyboard。

![3](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-Breadcrumbs-700x161.png)

关闭storyboard左边的文件导航栏，这样可以多留出一些空间。可以通过拖动边界或者点击storyboard左下方的按钮实现：

![1](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-DocumentOutline.png)

一切都完成后，你看到的是这样的：

![2](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-SideBySide-638x500.png)

在PushButtonView中把<code>drawRect(_:)</code>
<pre>UIColor.greenColor().setFill()</pre>
改成
<pre>UIColor.blueColor().setFill()</pre>
你会直接在storyboard上看到改变。非常酷~

![3](http://cdn1.raywenderlich.com/wp-content/uploads/2015/01/LiveRendering-700x477.png)

## 在上下文进行绘图

Core Graphics使用的是“绘画者模型”。

当你在上下文中绘图时，就和真实世界中的绘图一样。你先画一条路径，然后填充颜色，接着你再画一条线，再继续填充。你不能改变已经画好的像素点，但是你可以在他们的上面继续作画。

这张苹果官方文档的图片描述了是如何运作的，当你在画板上绘图时，你画图的顺序是非常重要的

![4](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-PaintersModel.gif)

你的加号符号应该在蓝色圆圈的上面，所以你需要先画圆，再画加号。

你可以画两个长方形来完成加号，但如果用粗线绘制路径会更简单一点。

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

在这一部分中，你先设置了一个UIBezierPath，给它了一个起始为止（圆圈的左侧），然后画到重点（圆圈的右侧）。然后你设置画壁颜色为白色，这个时候你可以在Storyboard中看到如下：

在Storyboard中，你将看到蓝色圆圈中包含了一条水平的白线：

![1](http://cdn2.raywenderlich.com/wp-content/uploads/2015/01/Dash.png)

注意：记住一条路径可以由点组成。这里有一个比较容易理解的理论：当你在创建路径时，想象此时你的手中握着笔。放两个点在纸上，然后把笔放在一个起点，然后往另一个点画线。这就是上述代码中moveToPoint(\_:)和addLineToPoint(_:)所做的事。

现在在ipad2和iphone6+的模拟器上运行代码，你会发现横线没有之前显示的那么好了，在它周围产生了淡蓝色的边框。

![1](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-PixelledLine-700x170.png)

## Points和Pixels

在早期的iphone中，Points和Pixels所占的空间与大小是相同的。当retina屏幕的iphone问世后，屏幕上Pixels的数量变成了Points的四倍。

同样地，iphone6+在相同points的基础上再一次增加了pixels的数量。

注意：以下只是概念 - 真实的硬件像素可能是不同的。如果要了解更多iphone6+的案例，可以看下这个[文章](http://www.paintcodeapp.com/news/iphone-6-screens-demystified)。

这里是一个12x12的pixel矩阵，其中point以灰色和白色表示。第一个是ipad2，一个pixel对应一个point。第二个是iphone6，是一个2x的retina屏幕，4个pixel对应一个point。第三个是iphone6+，是一个3x的retina屏幕，9个pixel对应一个point。

![1](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-Pixels-700x263.png)

你画的线的高度是3points，两边的宽度各是1.5points。

这张图展示了3-point的线在各个设备上的情况。你可以看到ipad2和iphone6+都发生了覆盖一半pixel的情况，这是无法实现的。所以在实际中iOS会采用两种颜色的中间色，所以线看起来会模糊。

![1](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-PixelLineDemonstrated-700x263.png)

在实际中，iPhone6+有很多pixels，可能你不会注意到这些模糊的情况，但是你应该在你的app里注意尽量避免。不过如果你是为了非retina屏的设备进行开发，那么你就需要避免这类情况的发生。

如果你要画奇数的直线，你需要加上或减去0.5points来避免模糊。如果你看一下上面的图表，你会看到增加0.5points相当于是在ipad上增加0.5pixels，在iphone6的线顶部上增加1pixel，在iphone6+的顶部增加1.5pixels。

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

现在在三种设备上都能很清晰地展示线条了，因为你对其进行了半个point的调整。

注意：为了线条的完美，你可以采用填充UIBezierPath(rect:)方法来代替画线条，并通过view的contentScaleFactor计算出长方形的高和宽。和画线方法会从路径的中间点开始向外计算宽度，而填充只会在路径内部进行。

在上述两行代码后面继续加上一条垂直线段，最后在drawRect(_:)里设置画线的颜色。我想在上述步骤之后，你应该已经知道该怎么完成了：

你可以在storyboard中实时看到最新的图案，这是最终完成的加号按钮：

![1](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-FinishedPlus.png)

## @IBInspectable – 自定义Storyboard属性

你知道，必须要点击一个没有需求的按钮，却只是为了确认保存是一件多么令人抓狂的事情吗？所以，你需要提供一种方式让用户从这种过于泛滥的点击中解脱出来——你需要一个减号按钮。

减号按钮与加号按钮基本一致，除了它没有一条竖线和并且是另外一种颜色。你需要用同样的PushButtonView类来定义减号按钮，并且在你将按钮添加到storyboard时，需要声明这个按钮的类型和颜色。

为了使得按钮可以被Interfase Builder读取，你需要添加@IBInspectable这一变量给按钮的属性。这也意味着，你可以在storyboard里面定义按钮的颜色，而不是在代码中编写。

在PushButtonView类的顶部，添加两个属性：

<pre><code>
@IBInspectable var fillColor: UIColor = UIColor.greenColor()
@IBInspectable var isAddButton: Bool = true
</code></pre>

从drawRect(_:)的顶部到

<pre><code>
UIColor.blueColor().setFill()
</code></pre>

更改和填充颜色相关的代码

<pre><code>
fillColor.setFill()
</code></pre>

在storyboard的视图中，按钮就会变成绿色。

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

这些代码让你只有在isAddButton被设置的时候才能够画垂直线，用这样的方式，一个按钮就可以既是加法按钮，又可以是减法按钮。

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

在storyboard中，选择push button view.你可以看到两个通过@IBInspectable声明的属性出现在Attributes Inspector的顶部。

更改填充颜色为RGB(87, 218, 213),并且设置Is Add Button为off。

![1](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-InspectableFillColor.png)

你可以立即在storyboard中看到相应的变化。

![1](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-InspectableMinusButton.png)

很酷吧？现在把Is Add Button设为on来把按钮设为加号。

### 第二个按钮

添加一个UIButton到StoryBoard,选中，并在尺寸查看器（size inspector）中更新按钮的位置和大小，这里设置为X=275，Y=480，Width=50，以及Height=50：

![1](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-PushButtonMinusCoords.png)

在Identity Inspector中，选择类型（Class）的下拉菜单，将UIButton更改为PushButtonView。

![1](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-PushButtonMinusClass.png)

一个绿色的plus button功能按钮就会覆盖掉之前的按钮。
在Attributes inspector中，将填充颜色设置为RGB（238，77，77），并将 Is Add Button设置为off，将default title button取消。

![1](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-MinusButtonColor.png)

按照之前的步骤，给新的视图添加自动布局的约束条件。

* 选中按钮，按住"Ctrl"键鼠标拖动按钮的中间位置向左（拖动范围在按钮以内），在弹出菜单中选择Width。
* 相似的，在按钮选中状态下，按住"Ctrl"键鼠标拖动按钮的中间位置向上（拖动范围在按钮以内），在弹出菜单中选择Height。
* 按住"Ctrl"键鼠标向上拖动，从按钮里面拖动到按钮范围外，选择Center Horizontally in Containner。
* 按住"Ctrl"键，鼠标从最底部的按钮拖动到顶部按钮，可以选择Vertical Spacing。

编译并运行应用，你就可以获得一个可复用的自定义视图，并可以添加到任意app中。并且它还可以适用于任意设备的屏幕尺寸。这里给大家展示的是在iPhone 4S上面的效果。

![1](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-SimPushButtons-339x500.png)

## 用UIBezierPath来画弧

下一个你要自定义的View就是下面这个：

![1-CompletedCounterView](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-CompletedCounterView.png)

这个看起来像是一个被填满的弧形，但是这个弧确确实实仅仅是一个粗笔画路径。而轮廓又是另一个由两个弧组成的粗笔画路径。

新建一个文件，***File\New\File...***，选择***Cocoa Touch Class***，然后命名这个类为CounterView。同时，让它为UIView的一个子类，并确认一下编程语言选择的是***Swift***。点击***Next***按钮，然后点击创建。然后用下面的代码把生成的代码覆盖掉：

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

注意：既然Apple允许在常量(let)和变量(var)中使用Unicode编码的字符，你可以把π作为一个常量的名字，用它来储存pi，这样能提高代码的可读性。如何输入π：同时按下Alt和P即可。

在这个代码中，你创建了两个常量。NoOfGlasses 代表着每天需要喝水的目标杯数。当计数到达了这个目标杯数的时候，这个计数器就到了它的最大值。
同时你也创建了3个你可以在storyboard里修改的@IBInspectable属性。这个counter变量用来记录当前已经喝的杯数。对于这个变量，能在storyboard里面修改它是很有必要的，尤其是当测试这个CounterView的时候。

打开***Main.storyboard***然后在“加号”按钮上添加一个UIView

在右边的***Size Insepector***面板里，设置X=185, Y=70, Width=230, 还有 Height=230：

![1-CounterViewCoords](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-CounterViewCoords.png)

给这个新的view添加几个自动布局的约束，就像你之前做的那样：

- 选中这个View,按住control从这个view(**原文这里是button，应该是view的**)的中心轻轻的向左边拖动鼠标，然后释放（仍然在这个view内），从弹出来的菜单中选择Width
- 同理，选中这个View，按住control从这个view的中心轻轻的向上面拖动鼠标，然后释放（仍然在这个view内），从弹出来的菜单中选择Height
- 按住control然后从这个view中拖动鼠标到外面的view然后释放，选择Center Horizontally in Container。
- 按住control然后从这个view往下拖动鼠标，一直到上面一个button然后释放，选择Vertical Spacing.

在右边的Identity Inspector面板中，把这个UIView所属的类改成CounterView。现在你在<code>drawRect(_:)</code>中的代码将会生效。

## 温习数学知识

我们稍微打断一下这个教程，然后我们回顾一下高中级别的数学知识，千万不要害怕哦！就像Douglas Adams所说 - 不要慌张! :]

在这个context里画任何东西，都基于这个单元圆。一个单元圆就是半径为1的圆。

![1-FloUnitCircle](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-FloUnitCircle.png)

红色的箭头表示你的弧从哪里开始和结束，并且是按顺时针方向。接下来，你将从 3π/4弧度(135º)的位置开始画弧，然后顺时针到 π/4弧度（45º）

在程序中，弧度经常被使用，而不是用度数。用弧度是非常有利的，因为你不必每次跟圆打交道的时候都要转换为度数。接下来，你需要计算一下这个弧的长度，当然是用弧度来算。

在单元圆（半径为1）中，弧的长度等于这个角度的用弧度表示的数值。比如说，看上面的哪个图，从0º到90º的弧的长度是 π/2。在真实场景中计算弧的长度，用单位圆的弧的长度乘以真实的圆的半径。

计算上面的红色箭头的长度，只要计算它所跨过的弧度数值。

          2π – end of arrow (3π/4) + point of arrow (π/4) = 3π/2

用角度表示就是：

          360º – 135º + 45º = 270º
          
![celebrate-all-the-maths](http://cdn1.raywenderlich.com/wp-content/uploads/2015/01/celebrate-all-the-maths-e1422223051445.png)

## 让我们继续来画弧

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

想象一下用圆规来画这个，首先把圆规的一个点固定在中间，然后打开圆规臂选择你需要的半径，然后装上一个细港币，然后旋转它来画弧。
在这个代码中，center变量代表着圆规的不动点，半径就是圆规打开的宽度（小于钢笔宽度的一半）并且这个弧的宽度就是这个钢笔笔头的宽度。

下面的列表解释了上面代码中每个小节的意思：

1. 定义这个view的中点，然后你将会基于这个点来通过旋转生成弧。
2. 通过view的宽和高的最大值来计算弧的半径。
3. 定义这个弧的粗细
4. 为这个弧定义开始和结束的角度。
5. 基于刚刚创建的中点，半径和角度来定义个一个弧的轨道。
6. 在给这个轨道上色之前，设置这个弧的轨道的宽度和颜色。

注意：当你在尝试画弧的时候，这些一般是你需要知道的，如果你想在画弧方面研究更深层次一点，你可以尝试阅读Raywenderlich老版本的 [Core Graphics Tutorial on Arcs and Paths](http://www.raywenderlich.com/33193/core-graphics-tutorial-arcs-and-paths)

在storyboard里或者是你运行你的应用的时候，下面这个就是你可以看到的

![1-SimArcStroke](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-SimArcStroke.png)

## 如何给弧加轮廓

当用户表示他们已经享受了一杯圣水之后（=。=），这个计数器外面的线条用来表示进度--离8杯还有多少。

这个外面的线由两个弧组成，一个外部一个内部，并且还有两条线来闭合他们。
在CounterView.swift里，把下面的代码加在<code>drawRect(_:):</code>的最后面：

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
	
下面解释一下各个小节代码的意思：

1. oulineEndAngle表示这个弧应该结束的角度，用当前的counter的值来计算。
2. outlinePath 就是外面的弧。由于这个弧不是基于单元圆的，所以把半径传给UIBezierPath()这个方法来计算这个弧的真实长度。
3. 在第一个弧的基础上再加一个内部的弧。这个和前面一个弧的角度一样但是是反向的（clockwise的值置为false）。同时，这一段会自动生成一个直线连接内部弧还外部的弧。
4. 在另一个弧的末尾自动画一条直线来关闭整个弧的路径。

因为你在CounterView.swift中将counter属性设置为5，你的CounterView现在应该在storyboard中看起来像下面这样：

![1-ArcOutline](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-ArcOutline.png)

打开 ***Main.storyboard***, 选择CounterView然后在右边的***Attributes Insepector***里，改变Counter的数值来检查你写在drawRect里的代码是否正确。然后你惊喜的发现这个storyboard里面也跟着改变了，它竟然是可以交互的。尝试改变counter的数值去超过8或者小于0。接下来你会修改这个。

![1-CounterView](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-CounterView.png)

## 让一切都能够正常工作

祝贺你！你现在已经能够掌控这些了，现在你需要做的就是点击加号按钮来增加这个计数器的数值，或者是点击减号按钮来减少这个计数器的数值。

在***Main.storyboard***中，拖拽一个UILable控件，放置在CounterView的中间。让它成为CounterView的子View。

然后在右边的***Size Inspector***中，设置X=93, Y=93, Width=44, 还有Height=44：

![1-LabelCoords](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-LabelCoords.png)

在右边的***Attributes Inspector***，改变Alignment参数为center,font size为36并且默认的Label title为8.

![1-LabelAttributes](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-LabelAttributes.png)

打开ViewController.swift然后添加下面的属性到这个类的顶部：

	//Counter outlets
	@IBOutlet weak var counterView: CounterView!
	@IBOutlet weak var counterLabel: UILabel!

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

现在，你对计数器的增加或者减少都依赖于按钮的***isAddButton***属性，确保计数器不会降到0以下-没人会喝负数杯数的水。:] 同时你也要在更新label上面的计数器数值。

把下面这段代码也加入到<code>viewDidLoad()</code>的底部，确保这个counterLabel的初始数值也被赋值到。

	counterLabel.text = String(counterView.counter)

在***Main.storyboard***中，连接***CounterView*** outlet和***UILabel*** outlet到ViewController中，同时连接两个按钮的***Touch Up Inside***事件到ViewController里面的相关方法上。

![1-ConnectingOutlets2](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-ConnectingOutlets2.gif)

运行这个应用，然后看看是否你的按钮能够更新这个label上面的数字，他们应该能行的。

等等！为什么这个CounterView上面的进度没有更新？

回想一下在这个教程的开始，<code>drawRect(_:)</code>方法只有在某些情况下会被调用，比如：遮住它的view被移开的时候，或者是它的hidden属性被改变的时候，或者是这个view第一次出现在屏幕上，或者是你的app调用了<code>setNeedsDisplay()</code>或者<code>setNeedsDisplayInRect()</code>方法。

然而，一旦counter属性改变的时候，CounterView也必须改变它的进度条。否则，用户就会认为你的app毫无用处。

打开CounterView.swift 然后改变这个counter属性声明为下面的代码：

	@IBInspectable var counter: Int = 5 {
  		didSet {
   	 		if counter <=  NoOfGlasses {
   	   		//the view needs to be refreshed
   	   		setNeedsDisplay()
   	 		}
  		}
	}

这段代码使CounterView仅仅在counter的数值小于或者等于用户的每天目标杯数的时候才会刷新，因为外面的轮廓只会最高到8.

再一次运行你的app，一切都正常工作起来。

![1-Part1Finished](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-Part1Finished.png)

## 接下来何去何从？

在这个教程你已经学习到了基本的绘图，现在你应该能够在你的UI里改变你的view的形状。但是等等，还有很多需要学习！在这篇教程的[第二部分](http://www.raywenderlich.com/?p=90693)，你将会尝试更深层次探索Core Graphics context并且创造一个记录你的喝水杯数的图表。

你可以[下载](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part1-6.31.zip)这个项目的所有代码，如果你有任何问题和评论，在下面的讨论中提出来。