
# 核心绘图教程2: 渐变与上下文
---

> * 原文链接 : [Core Graphics Tutorial Part 2: Gradients and Contexts](http://www.raywenderlich.com/90693/modern-core-graphics-with-swift-part-2)
* 原文作者 : [caroline](http://www.raywenderlich.com/u/caroline)
* [译文出自 :  开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [Mr.Simple,HarriesChen](https://github.com/bboyfeiyu) 
* 校对者: [xxx](https://github.com/)
* 状态 :  

![FinalApp](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/FinalApp-192x320.png)

*Update 04/15/2015*: Updated for Xcode 6.3 and Swift 1.2
*更新时间  04/15/2015* 为Xcode 6.3 和 Swift1.2更新


Welcome back to our modern Core Graphics with Swift tutorial series!

欢迎回到我们的Swift核心绘图教程系列！

In the [first part of the tutorial
series](http://www.raywenderlich.com/?p=90690), you learned about
drawing lines and arcs and using Xcode’s interactive storyboard
features.

在[第一部分](http://www.raywenderlich.com/?p=90690)中,你学习到了使用storyboard绘制线条和矩形.

In this second part, you’ll delve further into Core Graphics, learning about drawing gradients and manipulating `CGContext` with
transformations.

在第二部部分中，你将深入核心绘图，使用CGContext实现渐变效果

Core Graphics
-------------
核心绘图
-------------

You’re now going to leave the comfortable world of UIKit and enter the underworld of Core Graphics.

你现在已经从简单的UIKit深入到核心绘图。

This image from Apple describes the relevant frameworks conceptually:

下图是各个框架的关系图：

[![2-Architecture](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/2-Architecture-433x320.png)](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/2-Architecture.png)

UIKit is the top layer, and it’s also the most approachable. You’ve used `UIBezierPath`, which is a UIKit wrapper of the Core Graphics `CGPath`.

UIKit是在最顶层，使用最友好的框架。你使用过UIBezierPath的就是在UIKit层中对Core Graphics层中CGPath的封装。

One thing to know about lower layer Core Graphics objects and functions is that they always have the prefix CG, so they are easy to recognize.

你可以看到 Core Graphics 的对象和方法都是CG开头的，非常容易辨认。

Another fun fact: CG functions are C functions, so you don’t call them with explicit parameter names, which is different than when you call Swift functions.

另外CG方法都是C方法，在调用的时候不需要明确的指定参数名，和一般Swift调用方法不一样。

Getting Going with Graph View
-----------------------------
从Graph View开始
---------------

By the time you’re at the end of this session, you’ll create a graph
view that looks like this by using sample historical data:

本次的目标就是使用历史数据创建一个图表。

[![2-ResultGraphView](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-ResultGraphView-383x320.png)](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/2-ResultGraphView.png)

Before drawing on the graph view, you’ll set it up in the storyboard and create the code that animates the transition to show the graph view.

在绘图之前，你需要创建一个storyboard，写一些显示图表的代码。

The complete view hierarchy will look like this:

完成后的视图结构如下图：

[![2-ViewHierarchy](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-ViewHierarchy-547x500.png)](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/2-ViewHierarchy.png)

If you don’t have it already, [download a copy of
Flo](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part1-6.31.zip)
from the first Core Graphics tutorial.

如果你还没有，从[这里](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part1-6.31.zip)下载。

Go to *File\\New\\File…*, choose the *iOS\\Source\\Cocoa Touch Class*
template and click *Next*. Enter the name *GraphView* as the class name,choose the subclass *UIView* and set the language to *Swift*. Click *Next* then *Create*.

打开 *File\\New\\File…*, 选择 *iOS\\Source\\Cocoa Touch Class*
模板然后点击 *Next*. 输入名称 *GraphView*,选择成为 *UIView* 的子类，选择语言 *Swift*。 点击 *Next* 和 *Create*.

Go to *Main.storyboard* and drag a *UIView* on to the view controller’s view.

打开 *Main.storyboard* 拖一个 *UIView* 到控制器的视图。

This will contain the Graph and Counter Views, so make it a subview of
ViewController’s main view, but place it in front of Counter View.

视图将包含一个`Graph `和`Counter Views`，确定他们是视图控制器的主视图的子视图，并且`Graph `在`Counter Views`之上。

Your Document Outline should look like this:
此时结构应该是这样的：

[![2-DocumentOutline](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/2-DocumentOutline.png)](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/2-DocumentOutline.png)

In the *Size Inspector*, set *X*=*150*, *Y*=*50*, *Width*=*300*, and
*Height*=*300*:

在 *Size Inspector*, 设置 *X*=*150*, *Y*=*50*, *Width*=*300*,
*Height*=*300*:

[![2-ContainerCoordinates](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-ContainerCoordinates.png)](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-ContainerCoordinates.png)

Create the AutoLayout constraints similarly to how you did in part 1:

创建自动布局约束非常简单，在第一部分中有讲到。

-   With the view selected, Control-drag slightly left, and choose
    *Width* from the popup menu.
-   Then control-drag slightly up and choose *Height* from the popup
    menu.
-   Next, control-drag left from inside the view to outside the view,
    and choose *Center Vertically in Container*.
-   Finally, control-drag up from inside the view to outside the view
    and choose *Center Horizontally in Container*.
    
-  选中视图, 按住Control轻轻的向左拖动，在弹出的菜单中选择*Width*.
-  选中视图, 按住Control轻轻的向上拖动，在弹出的菜单中选择*Height*.
- 	选中视图, 按住Control从里向外拖动，在弹出的菜单中选择 *Center Vertically in Container*.
-  选中视图, 按住Control从里向外拖动，在弹出的菜单中选择 *Center Horizontally in Container*.


When creating views, it’s often helpful to give the view you’re working with a temporary color, so that you can easily see what you’re doing.

当创建了视图，通常我们我们会设置一个临时的背景颜色，这样做非常有用，可以让我们清楚的看到我们做了什么。

In the *Attributes Inspector*, color the background yellow.

在 *Attributes Inspector*, 将背景颜色设置为黄色.

[![2-ContainerViewBackground](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-ContainerViewBackground.png)](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-ContainerViewBackground.png)

Drag another *UIView* onto the yellow view, and make sure that it
becomes a subview.

再拖一个 *UIView* 到黄色的视图作为它的子视图.

In the *Identity Inspector*, change the class of the new view to
*GraphView*.

在 *Identity Inspector*, 将新创建的视图的class设置为*GraphView*.

[![2-GraphViewClass](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-GraphViewClass.png)](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-GraphViewClass.png)

In the *Size Inspector*, set *X*=*0*, *Y*=*25*, *Width*=*300*, and
*Height*=*250*:

在 *Size Inspector*, 设置 *X*=*0*, *Y*=*25*, *Width*=*300*,
*Height*=*250*:

[![2-GraphViewCoordinates](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-GraphViewCoordinates.png)](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-GraphViewCoordinates.png)

In the *Document Outline*, drag the *Counter View* to make it a subview of the yellow view, and make sure it’s positioned behind its sibling Graph View.

在*Document Outline*，将 *Counter View*拖到黄色的视图中作为它的子视图，这里需要注意需要放置在*Graph View*的后方。

After moving the Counter View, the auto layout constraints will turn
orange. Select the *Counter View*, and look at the bottom right of the storyboard. Find and click *Resolve Auto Layout Issues*, and choose *Selected Views: Clear Constraints*.

在移动 *Counter View*之后, 自动布局约束会变红. 选择 *Counter View*, 在storyboard 右下方找到并点击 *Resolve Auto Layout Issues*, 选择
*Selected Views: Clear Constraints*.


[![2-ClearAutoLayout](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/2-ClearAutoLayout.png)](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/2-ClearAutoLayout.png)

You can reset to default constraints, because the Counter View is now
snug inside the Container View.

你可以重置为默认的约束，因为现在`Counter View `被充满整个`Container View`

在 *Document Outline* 双击黄色视图来重命名为 *Container View*. 现在 *Document Outline* 是这样子的:

[![Flo2-Outline](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/Flo2-Outline-303x320.png)](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/Flo2-Outline.png)

The reason you need a Container View is to make an animated transition between the Counter View and the Graph View.

你需要一个`Container View`是因为在 `Counter View` 和 `Graph View`的渐变动画中需要用到。

Go to *ViewController.swift* and add property outlets for the Container and Graph Views:

打开 *ViewController.swift* ,为`Container` 和 `Graph Views` 添加`outloets`:

 ```                               
 @IBOutlet weak var containerView: UIView!                                
 @IBOutlet weak var graphView: GraphView!                                 
 ```                                                                      

This creates an outlet for the container view and graph view. Now let’s hook them up to the views you created in the storyboard.

这一步为`container view` 和 `graph view`，现在我们将他们和`storyboard`的视图关联起来。

Go back to *Main.storyboard* and hook up the *Graph View* and the
*Container View* to the outlets:

打开 *Main.storyboard*， 将 *Graph View*
*Container View* 和 outlets关联起来:

![Flo2-ConnectGraphViewOutlet](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/Flo2-ConnectGraphViewOutlet.gif)

Set up the Animated Transition
------------------------------

设置渐变动画
----------

Still in *Main.storyboard*, drag a *UITapGestureRecognizer* from the
*Object Library* to the *Container View* in the *Document Outline*:

仍然在 *Main.storyboard*中, 从*Object Library* 拖一个*UITapGestureRecognizer* 到 *Container View*:

![Flo2-AddTapGesture](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/Flo2-AddTapGesture.gif)

Go to *ViewController.swift* and add this property to the top of the
class:

打开 *ViewController.swift* 在开头添加属性：

 ```                               
 var isGraphViewShowing = false                                           
 ```                                                                      

This simply marks whether the graph view is currently showing.

这个将简单的标记当前`graph view`是否显示；

Now add the tap method to do the transition:

为点击事件添加过渡效果的方法：


 ```                               
 @IBAction func counterViewTap(gesture:UITapGestureRecognizer?) {         
   if (isGraphViewShowing) {                                              
                                                                          
     //hide Graph                                                         
     UIView.transitionFromView(graphView,                                 
         toView: counterView,                                             
         duration: 1.0,                                                   
         options: UIViewAnimationOptions.TransitionFlipFromLeft           
            UIViewAnimationOptions.ShowHideTransitionViews,              
         completion:nil)                                                  
   } else {                                                               
                                                                          
     //show Graph                                                         
     UIView.transitionFromView(counterView,                               
       toView: graphView,                                                 
       duration: 1.0,                                                     
       options: UIViewAnimationOptions.TransitionFlipFromRight            
          UIViewAnimationOptions.ShowHideTransitionViews,                
       completion: nil)                                                   
   }                                                                      
   isGraphViewShowing = !isGraphViewShowing                               
 }                                                                        
 ```                                                                      

`UIView.transitionFromView(_:toView:duration:options:completion:)`
performs a horizontal flip transition. Other transitions are cross
dissolve, vertical flip and curl up or down. The transition masks the
*ShowHideTransitionViews* constant, so you don’t have to remove the view to prevent it from being shown.

`UIView.transitionFromView(_:toView:duration:options:completion:)`
设置了一个水平翻动的效果. 另外还有扩散，溶解，竖直翻动，弯曲和下降的。 过渡效果使用
*ShowHideTransitionViews* 系数, 你不必通过移除视图来使它消失。

Add this code at the end of *btnPushButton(\_:)*:

在*btnPushButton(\_:)*的最后添加以下代码：

 ```                               
 if isGraphViewShowing {                                                  
   counterViewTap(nil)                                                    
 }                                                                        
 ```                                                                      

This makes it so that if the user presses the plus button while the
graph is showing, the display will swing back to show the counter.

这是为了防止在图标正在显示的过程中点击，将闪回计数器视图。


Lastly, to get this transition working, go back to *Main.storyboard* and hook up your tap gesture to the newly added `counterViewTap(gesture:)`:

最后，为了让这个效果生效，打开 *Main.storyboard*，将点击手势与`counterViewTap(gesture:)`关联起来。

![Flo2-TapGestureConnection](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/Flo2-TapGestureConnection.gif)

Build and run the application. Currently you’ll see the graph view when you start the app. Later on, you’ll set the graph view hidden, so the counter view will appear first. Tap it, and you’ll see the transition flipping.

构建并运行程序，你可以看到` graph view `在你打开程序的时候。点击它，`graph view`会消失，`counter view`现实，中间有过度动画。

[![2-ViewTransition](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/2-ViewTransition-270x500.png)](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/2-ViewTransition.png)

Analysis of the Graph View
--------------------------

分析Graph View
-------------

[![2-AnalysisGraphView](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-AnalysisGraphView-383x320.png)](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/2-AnalysisGraphView.png)

Remember the Painter’s Model from Part 1? It explains that drawing with Core Graphics is done from the back to the front, so you need an order in mind before you code. For Flo’s graph, that would be:

还记得第一部分的绘图模型嘛？它描述了用Core Graphics绘图从里到外的过程，你需要明白这里的顺序在你写代码之前：

1.  Gradient background view
2.  Clipped gradient under the graph
3.  The graph line
4.  The circles for the graph points
5.  Horizontal graph lines
6.  The graph labels


--------
1.  渐变的背景
2.  Clipped gradient under the graph
3.  折线
4.  折线点
5.  横线
6.  标签

Drawing a Gradient
------------------

绘制渐变
------

You’ll now draw a gradient in the Graph View’s context.

现在我们在`Graph View` 的 `context`中绘制渐变效果。

Go to *GraphView.swift* and replace the code with:

打开 *GraphView.swift* 将里面带代码替换为以下:


 ```                               
 import UIKit                                                             
                                                                          
 @IBDesignable class GraphView: UIView {                                  
                                                                          
   //1 - the properties for the gradient                                  
   @IBInspectable var startColor: UIColor = UIColor.redColor()            
   @IBInspectable var endColor: UIColor = UIColor.greenColor()            
                                                                          
     override func drawRect(rect: CGRect) {                               
                                                                          
       //2 - get the current context                                      
       let context = UIGraphicsGetCurrentContext()                        
       let colors = [startColor.CGColor, endColor.CGColor]                
                                                                          
       //3 - set up the color space                                       
       let colorSpace = CGColorSpaceCreateDeviceRGB()                     
                                                                          
       //4 - set up the color stops                                       
       let colorLocations:[CGFloat] = [0.0, 1.0]                          
                                                                          
       //5 - create the gradient                                          
       let gradient = CGGradientCreateWithColors(colorSpace,              
                                                 colors,                  
                                                 colorLocations)          
                                                                          
       //6 - draw the gradient                                            
       var startPoint = CGPoint.zeroPoint                                 
       var endPoint = CGPoint(x:0, y:self.bounds.height)                  
       CGContextDrawLinearGradient(context,                               
                                   gradient,                              
                                   startPoint,                            
                                   endPoint,                              
                                   0)                                     
     }                                                                    
 }                                                                        
 ```                                                                      


There are a few things to go over here:

这里有以下步骤

1.  You set up the start and end colors for the gradient as
    `@IBInspectable` properties, so that you’ll be able to change them in the storyboard.
2.  CG drawing functions need to know the context in which they will
    draw, so you use the UIKit method `UIGraphicsGetCurrentContext()` to
    obtain the current context. That’s the one that `drawRect(_:)` draws
    into.
3.  All contexts have a color space. This could be CMYK or grayscale,
    but here you’re using the RGB color space.
4.  The color stops describe where the colors in the gradient change
    over. In this example, you only have two colors, red going to green,
    but you could have an array of three stops, and have red going to
    blue going to green. The stops are between 0 and 1, where 0.33 is a
    third of the way through the gradient.
5.  Create the actual gradient, defining the color space, colors and
    color stops.
6.  Finally, you draw the gradient. `CGContextDrawLinearGradient()`
    takes the following parameters:
    -   The `CGContext` in which to draw
    -   The `CGGradient` with color space, colors and stops
    -   The start point
    -   The end point
    -   Option flags to extend the gradient



----

1.  将渐变的开始和结束颜色设置为`@IBInspectable` 属性,这样就可以在storyboard中实时看到效果.
2.  CG 绘图方法需要知道他们在哪个上下文（context）中绘图，,调用 `UIGraphicsGetCurrentContext()` 来获取当前上下文. 那就是 `drawRect(_:)` 绘制的地方.
3.  所有的上下文都有一个色域. 可以是 CMYK 也可以是 grayscale,
    这里使用的是RGB色域.
4.  颜色的起止描述了颜色的变化效果. 在这个例子中, 你只有两种颜色，从红到绿,
    你也可以又一个数组的颜色变化, 颜色从红到蓝在到绿. 每种颜色的起止都是0.33.
5.  创建渐变效果，定义色域，颜色起止。
6.  最后，绘制渐变效果通过方法`CGContextDrawLinearGradient()`
    有以下几个参数:
    -   `CGContext`表示在哪个上下文
    -   `CGGradient` 色域颜色起止。
    -   开始点
    -   结束点
    -   其他延伸选项


The gradient will fill the entire `rect` of `drawRect(_:)`.

渐变效果想充满整个`rect`通过`drawRect(_:)`。

Set up Xcode so that you have a side-by-side view of your code and the storyboard using the *Assistant Editor*, and you’ll see the gradient appear on the Graph View.

在Xcode你通过设置代码或者在storyboard的*Assistant Editor*调节，可以直接在`Graph View`看到效果。

![2-InitialGradient](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-InitialGradient1.png)

In the storyboard, select the Graph View. Then in the *Attributes
Inspector*, change *Start Color* to RGB(250, 233, 222), and *End Color* RGB(252, 79, 8):

在storyboard中, 选择 `Graph View`. 在 *Attributes
Inspector*设置 *Start Color* 为 RGB(250, 233, 222), 设置 *End Color* 为RGB(252, 79, 8):

[![2-FirstGradient](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-FirstGradient.png)](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-FirstGradient.png)

Now for some clean up duty. In *Main.storyboard*, select each view in
turn, except for the main ViewController view, and set the *BackgroundColor* to clear color. You don’t need the yellow color any more, and the push button views should have a transparent background too.

在 *Main.storyboard*, 一次选择所有的视图,除了控制器自带的,将他们的*BackgroundColor* 设置为clear color. 现在已经不需要黄色背景了,同样将按钮的背景颜色设置为透明.


Run the application, and you’ll notice the transition is now a lot more stylish.

运行程序，你会发现现在变得好看很多。

Clipping areas
--------------

裁剪区域
------

When you used the gradient just now, you filled the whole of the view’s context area. However, you can create paths to use as clipping areas instead of being used for drawing.

你刚才渐变填充了整个上下文的区域。你可以创建路径来裁剪当前的绘图区域。

Go to *GraphView.swift*, and add this code to the **top** of

打开*GraphView.swift*, 在`drawRect(_:)`的顶部加入以下代码:

 ```                               
 let width = rect.width                                                   
 let height = rect.height                                                 
                                                                          
 //set up background clipping area                                        
 var path = UIBezierPath(roundedRect: rect,                               
     byRoundingCorners: UIRectCorner.AllCorners,                          
     cornerRadii: CGSize(width: 8.0, height: 8.0))                        
 path.addClip()                                                           
 ```
                                                                       
This will create a clipping area that constrains the gradient. You’ll
use this same trick shortly to draw a second gradient under the graph
line.

这个将创建一个裁剪区域来限制渐变效果，你可以用相同的方式绘制渐变效果在第二梯度线


Build and run the application and see that your graph view has nice,
rounded corners:

构建运行程序，你会发现又漂亮的圆角效果：

[![2-RoundedCorners2](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-RoundedCorners2.png)](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-RoundedCorners2.png)


*Speed Note:* Drawing static views Core Graphics is generally quick
enough, but if your views move around or need frequent redrawing, you
should use Core Animation layers. It’s optimized so that the GPU, not
the CPU, handles most of the processing. In contrast, the CPU processes view drawing performed by `drawRect(_:)`.

*注意:* 绘制静态的图像使用`Core Graphics`是绰绰有余的，但是当你的图像需要频繁的重绘时，你应当使用`Core Animation layers`，它充分利用了GPU而不是CPU。CPU是通过调用 `drawRect(_:)`绘制的。


Instead of using a clipping path, you can create rounded corners using the `cornerRadius` property of a CALayer, but you should optimize for your situation. For a good lesson on this concept, check out [Custom Control Tutorial for iOS and Swift: A Reusable
Knob](http://www.raywenderlich.com/82058/custom-control-tutorial-ios-swift-reusable-knob)
by Mikael Konutgan and Sam Davies, where you’ll use Core Animation to
create a custom control.

除了使用裁剪路径，你还可以通过使用CALayer的`cornerRadius`属性来创建圆角，但是你需要优化。为了更好的理解，可以参考Mikael Konutgan and Sam Davies写的[Custom Control Tutorial for iOS and Swift: A Reusable
Knob](http://www.raywenderlich.com/82058/custom-control-tutorial-ios-swift-reusable-knob)，使用`Core Animation`来创建自定义控件。



Tricky Calculations for Graph Points
------------------------------------
计算坐标点
--------

Now you’ll take a short break from drawing to make the graph. You’ll
plot 7 points; the x-axis will be the ‘Day of the Week’ and the y-axis
will be the ‘Number of Glasses Drunk’.

现在可以短暂了停一下画图，我们需要绘制7个点，横坐标为一周的七天，纵坐标为

First, set up sample data for the week.

首先, 设置几个简单的值。

Still in *GraphView.swift*, at the top of the class, add this property:

在 *GraphView.swift*, 在顶部添加属性:

 ```                               
 //Weekly sample data                                                     
 var graphPoints:[Int] = [4, 2, 6, 4, 5, 8, 3]                            
 ```                                                                      

This holds sample data that represents seven days.

这里简单的数据代表了七天的数据。

Next, add this code to the end of `drawRect(_:)`:

接着，在`drawRect(_:)`的最后添加代码：

 ```                               
 //calculate the x point                                                  
                                                                          
 let margin:CGFloat = 20.0                                                
 var columnXPoint = { (column:Int) -> CGFloat in                          
   //Calculate gap between points                                         
   let spacer = (width - margin*2 - 4) /                                  
         CGFloat((self.graphPoints.count - 1))                            
   var x:CGFloat = CGFloat(column) * spacer                               
   x += margin + 2                                                        
   return x                                                               
 }                                                                        
 ```                                                                      

The x-axis points consist of 7 equally spaced points. The above code
above is a closure expression. It could have been added as a function,but for small calculations like this, it’s logical to keep them inline.
横坐标点包括了7个等距离的点。上述代码在一个闭包表达式中，应该是要独立的封装成一个方法，但是对于这个小的运算量，是可以内嵌在代码之中。

`columnXPoint` takes a column as a parameter, and returns a value where the point should be on the x-axis.

`columnXPoint` 接受 column作为参数, 返回点所在的横坐标的值.

Add the code to calculate the y-axis points to the end of
`drawRect(_:)`:

接着，在`drawRect(_:)`的最后添加代码来计算纵坐标的点：

 ```                               
 // calculate the y point                                                 
                                                                          
 let topBorder:CGFloat = 60                                               
 let bottomBorder:CGFloat = 50                                            
 let graphHeight = height - topBorder - bottomBorder                      
 let maxValue = maxElement(graphPoints)                                   
 var columnYPoint = { (graphPoint:Int) -> CGFloat in                      
   var y:CGFloat = CGFloat(graphPoint) /                                  
           CGFloat(maxValue) * graphHeight                                
   y = graphHeight + topBorder - y // Flip the graph                      
   return y                                                               
 }                                                                        
 ```                                                                      

`columnYPoint` is also a closure expression that takes the value from
the array for the day of the week as its parameter. It returns the y
position, between 0 and the greatest number of glasses drunk.

`columnYPoint` 同样的也在一个闭包表达式中，将数组中得每一天作为参数.返回纵坐标的位置, between 0 and the greatest number of glasses drunk.

Because the origin is in the top-left corner and you draw a graph from an origin point in the bottom-left corner, `columnYPoint` adjusts its return value so that the graph is oriented as you would expect.

因为起始点为左上角，但是你是从左下角开始绘制， `columnYPoint`会调整它的返回值来

Continue by adding line drawing code to the end of `drawRect(_:)`:

继续在`drawRect(_:)`的底部添加以下代码：

 ```                               
 // draw the line graph                                                   
                                                                          
 UIColor.whiteColor().setFill()                                           
 UIColor.whiteColor().setStroke()                                         
                                                                          
 //set up the points line                                                 
 var graphPath = UIBezierPath()                                           
 //go to start of line                                                    
 graphPath.moveToPoint(CGPoint(x:columnXPoint(0),                         
                               y:columnYPoint(graphPoints[0])))           
                                                                          
 //add points for each item in the graphPoints array                      
 //at the correct (x, y) for the point                                    
 for i in 1..<graphPoints.count {                                         
   let nextPoint = CGPoint(x:columnXPoint(i),                             
                           y:columnYPoint(graphPoints[i]))                
   graphPath.addLineToPoint(nextPoint)                                    
 }                                                                        
                                                                          
 graphPath.stroke()                                                       
 ```                                                                      


In this block, you create the path for the graph. The `UIBezierPath` is built up from the x and y points for each element in `graphPoints`.

在这个代码快中，你创建了图标的路径， `UIBezierPath`用来连接`graphPoints`种的每一个点。

The Graph View in the storyboard should now look like this:
现在，storyboard中的图标看起来是这样的：

[![2-FirstGraphLine](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-FirstGraphLine.png)](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-FirstGraphLine.png)

Now that you verified the line draws correctly, remove this from the end of `drawRect(_:)`:


现在你验证了线已经正确绘制，从`drawRect(_:)`移除最后一行代码

 ```                               
 graphPath.stroke()                                                       
 ```                                                                      

That was just so that you could check out the line in the storyboard and verify that the calculations are correct.



### A Gradient Graph

### 图表渐变

You’re now going to create a gradient underneath this path by using the path as a clipping path.

First set up the clipping path at the end of `drawRect(_:)`:


 ```                               
 //Create the clipping path for the graph gradient                        
                                                                          
 //1 - save the state of the context (commented out for now)              
 //CGContextSaveGState(context)                                           
                                                                          
 //2 - make a copy of the path                                            
 var clippingPath = graphPath.copy() as! UIBezierPath                     
                                                                          
 //3 - add lines to the copied path to complete the clip area             
 clippingPath.addLineToPoint(CGPoint(                                     
        x: columnXPoint(graphPoints.count - 1),                           
        y:height))                                                        
 clippingPath.addLineToPoint(CGPoint(                                     
        x:columnXPoint(0),                                                
        y:height))                                                        
 clippingPath.closePath()                                                 
                                                                          
 //4 - add the clipping path to the context                               
 clippingPath.addClip()                                                   
                                                                          
 //5 - check clipping path - temporary code                               
 UIColor.greenColor().setFill()                                           
 let rectPath = UIBezierPath(rect: self.bounds)                           
 rectPath.fill()                                                          
 //end temporary code                                                     
 ```                                                                      




A section-by-section breakdown of the above code:

1.  `CGContextSaveGState` is commented out for now — you’ll come back to
    this in a moment once you understand what it does.
2.  Copy the plotted path to a new path that defines the area to fill
    with a gradient.
3.  Complete the area with the corner points and close the path. This
    adds the bottom-right and bottom-left points of the graph.
4.  Add the clipping path to the context. When the context is filled,
    only the clipped path is actually filled.
5.  Fill the context. Remember that `rect` is the area of the context
    that was passed to `drawRect(_:)`.

Your Graph View in the storyboard should now look like this:
现在storyboard中的图标看起来应该是这样的：

[![2-GraphClipping](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-GraphClipping.png)](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-GraphClipping.png)

Next, you’ll replace that lovely green with a gradient you create from the colors used for the background gradient.

下一步，你会将可爱的绿色换成你为背景创建的渐变的效果

Remove the temporary code with the green color fill from the end of
`drawRect(_:)`, and add this code instead:

移除`drawRect(_:)`底部填充绿色的临时代码，换成以下代码：


 ```                               
 let highestYPoint = columnYPoint(maxValue)                               
 startPoint = CGPoint(x:margin, y: highestYPoint)                         
 endPoint = CGPoint(x:margin, y:self.bounds.height)                       
                                                                          
 CGContextDrawLinearGradient(context, gradient, startPoint, endPoint, 0)  
 //CGContextRestoreGState(context)                                        
 ```                                                                      


In this block, you find the highest number of glasses drunk and use that as the starting point of the gradient.

在这里，你将找到最大的数字作为渐变的起点。

You can’t fill the whole `rect` the same way you did with the green
color. The gradient would fill from the top of the context instead of
from the top of the graph, and the desired gradient wouldn’t show up.

你不能填充这个`rect`就像填充绿色一样，


Take note of the commented out *CGContextRestoreState* — you’ll remove the comments after you draw the circles for the plot points.


At the end of `drawRect(_:)`, add this:

 ```                               
 //draw the line on top of the clipped gradient                           
 graphPath.lineWidth = 2.0                                                
 graphPath.stroke()                                                       
 ```                                                                      


This code draws the original path.
这里绘制了原始的路径

Your graph is really taking shape now:
现在图标看起来是这样的：

[![2-SecondGraphLine](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-SecondGraphLine.png)](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-SecondGraphLine.png)

At the end of `drawRect(_:)`, add this:

在`drawRect(_:)`的底部添加:

 ```                               
 //Draw the circles on top of graph stroke                                
 for i in 0..<graphPoints.count {                                         
   var point = CGPoint(x:columnXPoint(i), y:columnYPoint(graphPoints[i])) 
   point.x -= 5.0/2                                                       
   point.y -= 5.0/2                                                       
                                                                          
   let circle = UIBezierPath(ovalInRect:                                  
                CGRect(origin: point,                                     
                         size: CGSize(width: 5.0, height: 5.0)))          
   circle.fill()                                                          
 }                                                                        
 ```                                                                      


This code draws the plot points and is nothing new. It fills a circle
path for each of the elements in the array at the calculated x and y
points.

绘制小圆点的代码并不是新的。这里将为每一个数组中的元素的位置绘制一个圆点。

[![2-GraphWithFlatCircles](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/2-GraphWithFlatCircles.png)](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/2-GraphWithFlatCircles.png)

Hmmm…but what’s showing up in the storyboard are not nice, round circle points! Whaaaaaaat? Press on, it’ll all come together.

Context States
--------------

上下文状态
--------

Graphics contexts can save states. When you set many context properties,such as fill color, transformation matrix, color space or clip region,you’re actually setting them for the current graphics state.

图形上下文可以保存状态。当你设置了许多上下文属性，如填充颜色，色域,变换矩阵，或裁剪区域，你实际上设置为当前图形状态。

You can save a state by using `CGContextSaveGState()`, which pushes a
copy of the current graphics state onto the state stack. You can also
make changes to context properties, but when you call
`CGContextRestoreGState()`, the original state is taken off the stack
and the context properties revert.

你可以使用`CGContextSaveGState()`保存状态，这会就爱那个当前状态的拷贝压进状态栈。你可以改变上下文的属性，但是当你调用`CGContextRestoreGState()`原来的状态将会出栈，还原当前的状态。

Still in *GraphView.swift*, in `drawRect(_:)`, uncomment the
`CGContextSaveGState()` that takes place before creating the clipping
path, and uncomment the `CGContextRestoreGState()` that takes place
after the clipping path has been used.

在 *GraphView.swift*的`drawRect(_:)`方法中，注意`CGContextSaveGState()` 发生在创建裁剪路径之前， `CGContextRestoreGState()`发生在裁剪路径之后。

By doing this, you:


1.  Push the original graphics state onto the stack with
    `CGContextSaveGState()`.
2.  Add the clipping path to a new graphics state.
3.  Draw the gradient within the clipping path.
4.  Restore the original graphics state with `CGContextRestoreGState()`，this was the state before you added the clipping path.

-----
1.  通过`CGContextSaveGState()`将原始状态压入栈.
2.  添加裁剪路径到新的状态.
3.  在裁剪路径中绘图.
4.  通过`CGContextRestoreGState()`还原状态，这是在添加裁剪路径前的状态。

Your graph line and circles should be much clearer now:

现在的图标和圆点看起来更加的清晰：

[![2-GraphWithCircles](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-GraphWithCircles.png)](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-GraphWithCircles.png)

At the end of `drawRect(_:)`, add the code to draw the three horizontal lines:

在 `drawRect(_:)`后添加代码来添加三条横线：


 ```                               
 //Draw horizontal graph lines on the top of everything                   
 var linePath = UIBezierPath()                                            
                                                                          
 //top line                                                               
 linePath.moveToPoint(CGPoint(x:margin, y: topBorder))                    
 linePath.addLineToPoint(CGPoint(x: width - margin,                       
                                 y:topBorder))                            
                                                                          
 //center line                                                            
 linePath.moveToPoint(CGPoint(x:margin,                                   
                              y: graphHeight/2 + topBorder))              
 linePath.addLineToPoint(CGPoint(x:width - margin,                        
                                 y:graphHeight/2 + topBorder))            
                                                                          
 //bottom line                                                            
 linePath.moveToPoint(CGPoint(x:margin,                                   
                              y:height - bottomBorder))                   
 linePath.addLineToPoint(CGPoint(x:width - margin,                        
                                 y:height - bottomBorder))                
 let color = UIColor(white: 1.0, alpha: 0.3)                              
 color.setStroke()                                                        
                                                                          
 linePath.lineWidth = 1.0                                                 
 linePath.stroke()                                                        
 ```                                                                      


Nothing in this code is new. All you’re doing is moving to a point and drawing a horizontal line.

没有新的代码，你需要做的只是移动到一个点然后水平画线。

[![2-GraphWithAxisLines](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-GraphWithAxisLines.png)](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-GraphWithAxisLines.png)

Adding the Graph Labels
-----------------------

添加图标的标签
-----------

Now you’ll add the labels to make the graph user-friendly.
现在你可以添加标签来让图标的用户体验更好。

Go to *ViewController.swift* and add these outlet properties for the
labels:

在*ViewController.swift*添加 outlets 属性：

 ```                               
 //Label outlets                                                          
 @IBOutlet weak var averageWaterDrunk: UILabel!                           
 @IBOutlet weak var maxLabel: UILabel!                                    
 ```                                                                      

This adds outlets for the two labels that you want to dynamically change text for (the average water drunk label, and the max water drunk label).
这两个标签将会动态的改变内容（饮水量的平均值和最大值）。

Now go to *Main.storyboard* and add the following *UILabels* as subviews of the *Graph View*:

在 *Main.storyboard* 添加以下的 *UILabels* 作为 *Graph View*的子视图:

1.  **“Water Drunk”**
2.  **“Average:”**
3.  **“2”** (averageWaterDrunk)
4.  **“99”** (maxLabel). Right aligned
5.  **“0”**. Right aligned
6.  Labels for each day of a week — the text for each will be changed in code. Center aligned.

Shift-select all the labels, and then change the fonts to custom *Avenir Next Condensed, Medium style*.

通过Shift全选所有标签，改变字体为*Avenir Next Condensed, Medium style*.

[![2-LabelledGraph](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-LabelledGraph.png)](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-LabelledGraph.png)

Connect `averageWaterDrunk` and `maxLabel` to the corresponding labels in *Main.storyboard*. Control-drag from View Controller to the correct label and choose the outlet from the pop up:

在*Main.storyboard*关联`averageWaterDrunk` 和 `maxLabel` .按住Control从View Controller拖分别拖到相应的标签。


[![2-ConnectLabels](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-ConnectLabels.gif)](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-ConnectLabels.gif)

For each weekday label, go into the *Attributes Inspector* and change
the *View’s Tag* corresponding to the day, so that the first one is 1
and the last one is 7.

对于每天得标签来说，在*Attributes Inspector* 改变 *View’s Tag* 属性，分别食从1到7

[![2-LabelViewTag](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-LabelViewTag.png)](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-LabelViewTag.png)

Now that you’ve finished setting up the graph view, in *Main.storyboard* select the *Graph View* and check *Hidden* so the graph doesn’t appear when the app first runs.

现在已经完成了图标的设置，在*Main.storyboard* 选择 *Graph View*并选择*Hidden*，让程序第一次出现的时候不显示图标。

[![2-GraphHidden](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-GraphHidden.png)](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-GraphHidden.png)

Go to *ViewController.swift* and add this method to set up the labels:

在 *ViewController.swift* 添加以下方法来设置标签:


```
                          
 func setupGraphDisplay() {                                               
                                                                          
   //Use 7 days for graph - can use any number,                           
   //but labels and sample data are set up for 7 days                     
   let noOfDays:Int = 7                                                   
                                                                          
   //1 - replace last day with today's actual data                        
   graphView.graphPoints[graphView.graphPoints.count-1] = counterView.cou 
 nter                                                                     
                                                                          
   //2 - indicate that the graph needs to be redrawn                      
   graphView.setNeedsDisplay()                                            
                                                                          
   maxLabel.text = "\(maxElement(graphView.graphPoints))"                 
                                                                          
   //3 - calculate average from graphPoints                               
   let average = graphView.graphPoints.reduce(0, combine: +)              
             / graphView.graphPoints.count                                
   averageWaterDrunk.text = "\(average)"                                  
                                                                          
   //set up labels                                                        
   //day of week labels are set up in storyboard with tags                
   //today is last day of the array need to go backwards                  
                                                                          
   //4 - get today's day number                                           
   let dateFormatter = NSDateFormatter()                                  
   let calendar = NSCalendar.currentCalendar()                            
   let componentOptions:NSCalendarUnit = .CalendarUnitWeekday             
   let components = calendar.components(componentOptions,                 
                                        fromDate: NSDate())               
   var weekday = components.weekday                                       
                                                                          
   let days = ["S", "S", "M", "T", "W", "T", "F"]                         
                                                                          
   //5 - set up the day name labels with correct day                      
   for i in reverse(1...days.count) {                                     
     if let labelView = graphView.viewWithTag(i) as? UILabel {            
       if weekday == 7 {                                                  
         weekday = 0                                                      
       }                                                                  
       labelView.text = days[weekday--]                                   
       if weekday < 0 {                                                   
         weekday = days.count - 1                                         
       }                                                                  
     }                                                                    
   }                                                                      
 }
                                                                         
```                                                                      


This looks a little burly, but it’s required to set up the calendar and retrieve the current day of the week. Take it in sections:


1.  You set today’s data as the last item in the graph’s data array. In the final project, which you can download at the end of [Part
    3](http://www.raywenderlich.com/?p=90695), you’ll expand on this by replacing it with 60 days of sample data, and you’ll include a
    method that splits out the last x number of days from an array, but that is beyond the scope of this session. :]
2.  Redraws the graph in case there are any changes to today’s data.
3.  Here you use Swift’s `reduce` to calculate the average glasses drunk
    for the week; it’s a very useful method to sum up all the elements
    in an array.
4.  This section put the current day’s number from the iOS calendar into
    the property weekday.
5.  This loop just goes from 7 to 1, gets the view with the
    corresponding tag number and extracts the correct day title from the *days* array.
    
    
-----

1.  你设置今天的数据将会作为数组的最后一个元素插入. 在最后的项目中,[Part
    3](http://www.raywenderlich.com/?p=90695),你可以扩展到60天的数据, 将会有一个方法来选取起止时间,但是这超出了本章的讲解内容 :]
2.  如果今天有变化，重绘数据.
3.  这里你使用 Swift的 `reduce` 来计算一周的平局饮水量; 这个在计算数组的总和的时候非常有用。
4.  这一部分将当前的日期作为iOS的日历一周的结束.
5.  这个循环从7到1，通过tag来获取相应的视图，这是正确的标题从*days*数组中。

Still in *ViewController.swift*, call this new method from
`counterViewTap(_:)`. In the `else` part of the conditional, where the comment says *show graph*, add this code:

还是在*ViewController.swift*，在`counterViewTap(_:)`方法的`else`分支中调用新方法来显示图表:

 ```                               
 setupGraphDisplay()
                                                       
 ```                                                                      


Run the application, and click the counter. Hurrah! The graph swings
into view in all its glory!

运行程序，点击计数器，万岁，图表将显示所有的数据。


[![2-GraphFinished](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/2-GraphFinished.gif)](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/2-GraphFinished.gif)

Mastering the Matrix
--------------------

掌握矩阵
------

Your app is looking really sharp! The counter view you created in part one could be improved though, like by adding markings to indicate each glass to be drunk:

现在程序看起来有点突兀，我们子啊第一部分创建的计数器还需要提升一下，在饮水量上添加指示。


[![2-Result](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-Result.png)](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-Result.png)

Now that you’ve had a bit of practice with CG functions, you’ll use them to rotate and translate the drawing context.

现在已经有CG方法绘图的一些经验了，你将会使用旋转和平移。

Notice that these markers radiate from the center:
可以看出这个标志都是从中心辐射的:

[![2-LinesExpanded](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-LinesExpanded.png)](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-LinesExpanded.png)

As well as drawing into a context, you have the option to manipulate the context by rotating, scaling and translating the context’s
transformation matrix.

和在上下文绘图一样，你可以通过旋转，缩放，和平移来进行矩阵变幻。

At first, this can seem confusing, but after you work through these
exercises, it’ll make more sense. The order of the transformations is
important, so first I’ll outline what you’ll be doing with diagrams.

一开始这可能是令你费解的，但是在经过一些练习之后，将会变得更有意义，变换的顺序非常重要，我先说画出你需要做的示意图。

The following diagram is the result of rotating the context and then
drawing a rectangle in the center of the context.

下面的示意图就是通过旋转然后在中间绘制矩形的结果。

[![2-RotatedContext](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-RotatedContext.png)](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-RotatedContext.png)

The black rectangle is drawn before rotating the context, then the green one, then the red one. Two things to notice:

黑色的矩形食旋转前的上下文。然后是绿的，再是红得。有两件事需要注意。

1.  The context is rotated at the top left (0,0)
2.  The rectangle is still being drawn in the center of the context, but after the context has been rotated.


-----

1.  上下文根据左上角进行旋转。
2.  矩形依然绘制在中心在旋转之后.

When you’re drawing the counter view’s markers, you’ll translate the
context first, then you’ll rotate it.

当你绘制计数器的标志的时候，你需要先旋转上下文，然后再旋转它。

[![2-RotatedTranslatedContext](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-RotatedTranslatedContext.png)](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/2-RotatedTranslatedContext.png)

In this diagram, the rectangle marker is at the very top left of the
context. The blue lines outline the translated context, then the context rotates (red dashed line) and is translated again.

在这个示意图中，矩形的标记在左上方，蓝色轮廓就是变换的上下问，然后上下文旋转（红色虚线），然后再次旋转。

When the red rectangle marker is finally drawn into the context, it’ll appear in the view at an angle.



After the context is rotated and translated to draw the red marker, it needs to be reset to the center so that the context can be rotated and translated again to draw the green marker.

在上下文旋转和平移得到红色标记之后，它需要回到中心来进行下一次的变换得到绿的标志。


Just as you saved the context state with the clipping path in the Graph View, you’ll save and restore the state with the transformation matrix each time you draw the marker.

就像你在裁剪的时候保存上下文一样，你需要保存和恢复上下文状态在每次矩阵变换的时候。

Go to *CounterView.swift* and add this code to the end of `drawRect(_:)`to add the markers to the counter:

打开*CounterView.swift* 在`drawRect(_:)`的最后添加代码来增加标记:

```                             
 //Counter View markers                                                   
                                                                          
 let context = UIGraphicsGetCurrentContext()                              
                                                                          
 //1 - save original state                                                
 CGContextSaveGState(context)                                             
 outlineColor.setFill()                                                   
                                                                          
 let markerWidth:CGFloat = 5.0                                            
 let markerSize:CGFloat = 10.0                                            
                                                                          
 //2 - the marker rectangle positioned at the top left                    
 var markerPath = UIBezierPath(rect:                                      
        CGRect(x: -markerWidth/2,                                         
        y: 0,                                                             
        width: markerWidth,                                               
        height: markerSize))                                              
                                                                          
 //3 - move top left of context to the previous center position           
 CGContextTranslateCTM(context,                                           
                       rect.width/2,                                      
                       rect.height/2)                                     
                                                                          
 for i in 1...NoOfGlasses {                                               
   //4 - save the centred context                                         
   CGContextSaveGState(context)                                           
                                                                          
   //5 - calculate the rotation angle                                     
   var angle = arcLengthPerGlass * CGFloat(i) + startAngle - π/2          
                                                                          
   //rotate and translate                                                 
   CGContextRotateCTM(context, angle)                                     
   CGContextTranslateCTM(context,                                         
                         0,                                               
                         rect.height/2 - markerSize)                      
                                                                          
   //6 - fill the marker rectangle                                        
   markerPath.fill()                                                      
                                                                          
   //7 - restore the centred context for the next rotate                  
   CGContextRestoreGState(context)                                        
 }                                                                        
                                                                          
 //8 - restore the original state in case of more painting                
 CGContextRestoreGState(context) 
                                          
```                                                                      


Here’s what you’ve just done:

这里你做了下面几点：

1.  Before manipulating the context’s matrix, you save the original
    state of the matrix.
2.  Define the position and shape of the path — but you’re not drawing
    it yet.
3.  Move the context so that rotation happens around the context’s
    original center. (Blue lines in the previous diagram.)
4.  For each mark, you first save the centered context state.
5.  Using the individual angle previously calculated, you determine the
    angle for each marker and rotate and translate the context.
6.  Draw the marker rectangle at the top left of the rotated and
    translated context.
7.  Restore the centered context’s state.
8.  Restore the original state of the context that had no rotations or translations.


------

1.  矩阵变换之前，保存上下文状态.
2.  得到位路劲的位置和形状，但是现在还不绘制.
3.  旋转能够围绕原始的中心点(上面的蓝色线)
4.  对于每个标志, 首先保存居中上下文状态.
5.  根据先前计算的值来进行旋转和平移上下文.
6.  在旋转后的左上角绘制矩形并平移.
7.  读取居中上下文状态
8.  读取最原始的上下文状态.

Whew! Nice job hanging in there for that. Now build and run the
application, and admire Flo’s beautiful and informative UI:

喔，干的不错，现在来运行程序，欣赏这个漂亮的界面。
[![2-FinalPart2](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/2-FinalPart2-270x500.gif)](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/2-FinalPart2.gif)

Where to Go to From Here?
-------------------------

接下来该做什么？
------------

[Here is
Flo](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part2-6.3.zip),
complete with all of the code you’ve developed so far.

[这里](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part2-6.3.zip),
食到现在为止为止所有的代码.

At this point, you’ve learned how to draw paths, gradients and how to
change the context’s transformation matrix.

现在你已经学会了绘制路径，渐变效果和通过上下文的矩阵变化

[In the third and final part](http://www.raywenderlich.com/?p=90695) of this Core Graphics tutorial, you’ll create a patterned background and draw a vector medal image.

[在第三部分](http://www.raywenderlich.com/?p=90695)的核心绘图教程中,
你将创建图案的背景和绘制矢量图。


