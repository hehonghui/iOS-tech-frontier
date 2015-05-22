* 原文链接：[Core Graphics Tutorial Part 3: Patterns and Playgrounds](http://www.raywenderlich.com/90695/modern-core-graphics-with-swift-part-3)
* 原文作者：[ Caroline Begbie ](http://www.raywenderlich.com/u/caroline)
* 译文出自：[开发者前线 www.devtf.cn](www.devtf.cn)
* 译者：[MrLoong](https://github.com/MrLoong)
* 校对者：
* 状态：




#Core Graphics Tutorial Part 3: Patterns and Playgrounds
**Update 04/15/2015:**Updated for Xcode 6.3 and Swift 1.2

Welcome back to the third and final part of the Core Graphics tutorial series! Flo, your water drinking tracking app, is ready for its final evolution, which you’ll make happen with Core Graphics.

In the [part one](http://www.raywenderlich.com/90690/modern-core-graphics-with-swift-part-1), you drew three custom-shaped controls with UIKit. Then in the [part two](http://www.raywenderlich.com/90693/modern-core-graphics-with-swift-part-2), you created a graph view to show the user’s water consumption over a week, and you explored transforming the context transformation matrix (CTM).

In this third and final part, you’ll take **Flo** to its 
final form. Specifically, you’ll:


* Create a repeating pattern for the background.
- Draw a medal from start to finish to award the users for successfully drinking eight glasses of water a day.

If you don’t have it already, [download a copy of the **Flo** project](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part2-6.3.zip) from the second part of this series.

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/FinalApp-192x320.png)

欢迎回到Core Graphics 教学系列的第三也是最后一部分！Flo，你的water drinking tracking app将使用Core Graphics 进行最终的改进。

在[在第一部分](http://www.raywenderlich.com/90690/modern-core-graphics-with-swift-part-1)，你通过UIKit画了三个自定义形状的控件,[在第二部分](http://www.raywenderlich.com/90693/modern-core-graphics-with-swift-part-2)你创建了一个视图用来显示用户在过去一周的喝水量，并且研究了transforming the context transformation matrix (CTM)

在这第三也是最后一部分，你将使**Flo**升级到它的最终形态，特别的是你将：

* 创建一个重复的背景图案
* 为成功每天喝掉8杯水的用户从头到尾画一枚奖章

如果你还没有这个项目的源代码，从本系列的第二部分下载[**Flo工程**](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part2-6.3.zip)

##Background Repeating Pattern 背景重复模式

Your mission in this section is to use UIKit’s pattern methods to create this background pattern:

你在这部分的任务是使用UIKit的方式创建这个背景图案：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-FinalBackground-300x500.png)

~~~
Note: If you need to optimize for speed, then work through
Core Graphics Tutorial: Patterns which demonstrates a basic 
way to create patterns with Objective-C and Core Graphics. 
For most purposes, like when the background is only drawn 
once, UIKit’s easier wrapper methods should be acceptable.
~~~
注意：如果你需要对速度进行优化，可以去看[Core Graphics Tutorial:Patters](http://www.raywenderlich.com/33496/core-graphics-tutorial-patterns)里面
演示一个使用Object－c和Core Graphics的基本方法去创建一个格局.针对实用性问题，
当背景仅被描绘一次，使用UIKit封装好的简易方法应该是可以接受的。

Go to **File\New\File…**and select the iOS **iOS\Source\Cocoa Touch Class**template to create a class called **BackgroundView** with a subclass of UIView. Click Next and then Create.

Go to **Main.storyboard**, select the main view of ViewController, and change the class to **BackgroundView** in the **Identity Inspector.**

点击**File\New\File…**并且选择IOS **iOS\Source\Cocoa Touch Class** 模版创建一个类名为**BackgroundView**的UIView。点击下一步然后创建

进入**Main.storyboard**，在 **Identity Inspector**选择ViewController主界面，改变为**BackgroundView**
![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-BackgroundViewStoryboard3-480x187.png)

Set up **BackgroundView.swift** and **Main.storyboard** so they are side-by-side, using the **Assistant Editor**.
Replace the code in **BackgroundView.swift** with:

点击**Assistant Editor**，让**BackgroundView.swift**和**Main.storyboard**并排显示。
替换**BackgroundView.swift**的代码：

```
import UIKit
 
@IBDesignable
 
class BackgroundView: UIView {
 
  //1 
  @IBInspectable var lightColor: UIColor = UIColor.orangeColor()
  @IBInspectable var darkColor: UIColor = UIColor.yellowColor()
  @IBInspectable var patternSize:CGFloat = 200
 
  override func drawRect(rect: CGRect) {
    //2
    let context = UIGraphicsGetCurrentContext()
 
    //3
    CGContextSetFillColorWithColor(context, darkColor.CGColor)
 
    //4
    CGContextFillRect(context, rect)
  }
}
```
The background view of your storyboard should now be yellow. More detail on the above code:
现在你的storyboard背景应该是黄色的，更多的细节参考上面的代码：

1. **ightColor** and **darkColor** have **@IBInspectable** attributes so it’s easier to configure background colors later on. You’re using orange and yellow as temporary colors, just so you can see what’s happening. **patternSize**controls the size of the repeating pattern. It’s initially set to large, so it’s easy to see what’s happening.
2. **UIGraphicsGetCurrentContext()** gives you the view’s context and is also where **drawRect(_:)**draws.
3. Use the Core Graphics method **CGContextSetFillColorWithColor()** to set the current fill color of the context. Notice that you need to use **CGColor**, a property of **darkColor** when using Core Graphics.
4. Instead of setting up a rectangular path,**CGContextFillRect()** fills the entire context with the current fill color.

You’re now going to draw these three orange triangles using **UIBezierPath()**. The numbers correspond to the points in the following code:

1. **lightColor**和**darkColor**拥有**@IBInspectable**属性，所以更容易去配置背景颜色。你使用橙色和黄色作为临时色，这样你就能直到到底在发生什么事。**patternSize**控件控制着重复形式的大小。它的初始值设置为large，所以更容易看清发生了什么。
2. **UIGraphicsGetCurrentContext()**让你了解视图上下文同样也是**drawRect(_:)**将会绘画的区域。
3. 使用Core Graphics的方法**CGContextSetFillColorWithColor()** 去设置当前上下文的填充色。注意当你使用Core Graphics时，需要用到**darkColor**的一个属性**CGColor**。
4. **CGContextFillRect()**使用当前填充色填充当前上下文的整个背景，而不是建立一个矩形路径。

你现在需要使用**UIBezierPath()**画这三个橙色的三角形。图中的点对应代码中的数字。
![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-GridPattern.png)

Still in **BackgroundView.swift**, add this code to the end of **drawRect(_:)**:
仍然在**BackgroundView.swift**中操作，在 **drawRect(_:)**末尾添加如下代码：

```
let drawSize = CGSize(width: patternSize, height: patternSize)
 
//insert code here
 
 
let trianglePath = UIBezierPath()
//1
trianglePath.moveToPoint(CGPoint(x:drawSize.width/2, 
                                 y:0)) 
//2
trianglePath.addLineToPoint(CGPoint(x:0, 
                                    y:drawSize.height/2)) 
//3
trianglePath.addLineToPoint(CGPoint(x:drawSize.width, 
                                    y:drawSize.height/2)) 
 
//4
trianglePath.moveToPoint(CGPoint(x: 0, 
                                 y: drawSize.height/2)) 
//5
trianglePath.addLineToPoint(CGPoint(x: drawSize.width/2, 
                                    y: drawSize.height)) 
//6
trianglePath.addLineToPoint(CGPoint(x: 0, 
                                    y: drawSize.height)) 
 
//7
trianglePath.moveToPoint(CGPoint(x: drawSize.width, 
                                 y: drawSize.height/2)) 
//8
trianglePath.addLineToPoint(CGPoint(x:drawSize.width/2, 
                                    y:drawSize.height)) 
//9
trianglePath.addLineToPoint(CGPoint(x: drawSize.width, 
                                    y: drawSize.height)) 
 
lightColor.setFill()
trianglePath.fill()

```
Notice how you use one path to draw three triangles. **moveToPoint(_:)** is just like lifting your pen from the paper when you’re drawing and moving it to a new spot.

注意你是如何使用一个路径画三个三角形的，**moveToPoint(_:)**就像在纸上抬起你的笔从一个地方绘制和移动到另一个地方。

Your storyboard should now have an orange and yellow image at the top left of your background view. 

你的storyboard现在应该有一个橙色和黄的的图像在你背景的左上方。

So far, you’ve drawn directly into the view’s drawing context. To be able to repeat this pattern, you need to create an image outside of the context, and then use that image as a pattern in the context.

到目前为止，你都是直接在一个视图的上下文中直接绘画。为了能重复这个图案，你需要在上下文外创建一个图像，然后用这个图像作为上下文中的背景图案。

Find the following. It’s close to the top of drawRect(_:), but after:
找到下面这段代码，在drawRect(_:)的顶部，但是在下面这段代码之后：

```
et drawSize = CGSize(width: patternSize, height: patternSize)
```
Add the following code where it conveniently says **Insert code here**:

然后插入下面的代码

```
UIGraphicsBeginImageContextWithOptions(drawSize, true, 0.0)
let drawingContext = UIGraphicsGetCurrentContext()
 
//set the fill color for the new context
darkColor.setFill()
CGContextFillRect(drawingContext,
      CGRectMake(0, 0, drawSize.width, drawSize.height))
```

Hey! Those orange triangles disappeared from the storyboard. Where’d they go?
嘿！那些橘黄色三角形从面板中消失了，它们去哪了？

**UIGraphicsBeginImageContextWithOptions()**creates a new context and sets it as the current drawing context, so you’re now drawing into this new context. The parameters of this method are:

*  The size of the context.
*  Whether the context is opaque — if you need transparency, then this needs to be false.
*  The scale of the context. If you’re drawing to a retina screen, this should be 2.0, and if to an iPhone 6 Plus, it should be 3.0. However, this uses 0.0, which ensures the correct scale for the device is automatically applied.

**UIGraphicsBeginImageContextWithOptions()**创建了一个新的上下文，并且将其设置为当前的绘图上下文，所以你在这个新的上下文中作画。刚才方法的参数为：

*  context的大小
*  背景是否透明— 如果你需要透明的, 这里设置为false.
*  context scale的大小. 如果要适应视网膜屏幕, 这里应该设置为2, 如果要适应iPhone 6 Plus, 应该为3.0. 然而这里使用0.0将自动适配屏幕

Then you used **UIGraphicsGetCurrentContext()** to get a reference to this new context.

然后你用 **UIGraphicsGetCurrentContext()**得到这个新的上下文的引用。

You then filled the new context with yellow. You could have let the original background show through by setting the context opacity to false, but it’s faster to draw opaque contexts than it is to draw transparent, and that’s argument enough to go opaque.

然后你将这个新的context设置为黄色。你可以通过设这context的属性opacity为false让原来的背景色显示，但是画一个不透明的context比画一个透明的要快得多，凭这一点就应该选opaque。

Add this code to the end of **drawRect(_:)**:
在**drawRect(_:)**的末尾添加下面的代码：

```
let image = UIGraphicsGetImageFromCurrentImageContext()
UIGraphicsEndImageContext()

```

This extracts a UIImage from the current context. When you end the current context with **UIGraphicsEndImageContext()**, the drawing context reverts to the view’s context, so any further drawing in **drawRect(_:)** happens in the view.

在当前的context提取一个UIImage。当你使用**UIGraphicsEndImageContext()**结束当前context时，描绘context还原为视图context，所以任何未来在**drawRect(_:)** 中的的绘图都会反应在viwe中。

To draw the image as a repeated pattern, add this code to the end of drawRect(_:):

为了使图像作为一个可重复的图案，在drawRect(_:)末尾添加代码：

```
UIColor(patternImage: image).setFill()
CGContextFillRect(context, rect)
```

This creates a new UIColor by using an image as a color instead of a solid color.

这会让图片当作UIColor中的一个新的颜色来用，而不是一个纯色。

Build and run the app. You should now have a rather bright background for your app. :

编译并且运行app，你现在的app应该有一个相当亮眼的背景：
![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/3-BoldBackground2.png)

Go to Main.storyboard, select the background view, and in the Attributes Inspector change the @IBInspectable values to the following:

去Main.storyboard，选择background view，并且在Attributes Inspector改变IBInspectable属性的值为：

* Light Color: RGB(255, 255, 242)
* Dark Color: RGB(223, 255, 247)
* Pattern Size: 30
![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-BackgroundColors2-473x320.png)

Experiment a little more with drawing background patterns. See if you can get a polka dot pattern as a background instead of the triangles.

尝试去画一些不同的背景图案，看除了三角形，你能不能把背景图案变成polka dot。

And of course, you can substitute your own non-vector images as repeating patterns.

当然，你可以使用自己的非矢量图作为背景图案

##Drawing Images 绘制图像

In the final stretch of this tutorial, you’ll make a medal to handsomely reward users for drinking enough water. This medal will appear when the counter reaches the target of eight glasses.

在本教程的最后阶段，你将为喝够水的人送上一个奖牌，当计数器纪录的数量达到8杯的时候这个奖牌将出现。

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/3-MedalFinal.png)

I know that’s certainly not a museum-worthy piece of art, so please know that I won’t be offended if you improve it, or even take it to the next level by draw a trophy instead of a medal. ;]

我知道这肯定比不上博物馆里的一件艺术品，但是请你知道，如果你改进它，我并不会生气，甚至你可以通过画一个奖杯而不是奖牌来升华一下。

Instead of using **@IBDesignable**, you’ll draw it in a **Swift Playground**, and then copy the code to a **UIImageView** subclass. Though interactive storyboards are often useful, they have limitations; they only draw simple code, and storyboards often time out when you create complex designs.

你将在**Swift Playground**中绘制这个奖牌，而不是用**@IBDesignable**，然后把代码复制到**UIImageView**的子类中。虽然与storyboards互动通长是很有用的，但他们有局限性。他们仅能描绘简单的代码，当你创建一个复杂的设计时，storyboards通长很耗时间。

In this particular case, you only need to draw the image once when the user drinks eight glasses of water. If the user never reaches the target, there’s no need to make a medal.

在这种情况下，只有当用户喝满八杯水的时候才需要画一个图像。如果用户从来没有到达到过目标，那就根本不需要绘制奖牌。

Once drawn, it also doesn’t need to be redrawn with drawRect(_:) and setNeedsDisplay().

一旦绘制，他也不需要使用drawRect(_:)和setNeedsDisplay().进行重绘。

Time to put the brush to the canvas. First return Xcode to single viewing, rather than side-by-side, by clicking the Standard Editor icon:

是时候作画了。首先点击Standard Editor按钮，让Xcode返回单一界面编辑模式。

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-StandardEditor.png)

Go to **File\New\File…** and choose the iOS Playground template. Click Next, name the playground MedalDrawing and then click Create.

点击**File\New\File…**并且选择IOS Playground模版。点击 Next 命名playground为MedalDrawing然后点击Create。

Replace the playground code with:
把playground中的代码替换为：

```
import UIKit
 
let size = CGSize(width: 120, height: 200)
 
UIGraphicsBeginImageContextWithOptions(size, false, 0.0)
let context = UIGraphicsGetCurrentContext()
 
 
 
//This code must always be at the end of the playground
let image = UIGraphicsGetImageFromCurrentImageContext()
UIGraphicsEndImageContext()
```
This creates a drawing context, just as you did for the patterned image.

这将创建一个绘制上下文，就像你之前刚给重复背景图片做的是一回事。

Take note of these last two lines; you always need them at the bottom of the playground so you can preview the image in the playground.

注意最后两行。你总是需要它们在playground底部，这样你就可以在playground中预览图片。

Next, in the gray results column click the + button to the right of this code:

下一步，在灰色的结果栏中单击代码右边的＋按钮：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/ShowFinishedImage.gif)

this will place a preview image underneath the code. The image will update with every change that you make to the code.

这将在代码的底部预览你的图像。图像将根据你代码的每次更新而更新。

It’s often best to do a sketch to wrap your head around the order you’ll need to draw the elements — look at the “masterpiece” I made while conceptualizing this tutorial:

在你作画之前，最好能在你的脑海里先有一张绘制草图，看看我在构造这篇tutorial时所构想的“杰作”：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-Sketch.png)

This is the order to draw the medal:

1. The back ribbon (red) 
2. The medallion (gold gradient) 
3. The clasp (dark gold) 
4. The front ribbon (blue) 
5. The number 1 (dark gold) 

下面是绘制奖牌的顺序:

1. 后丝带（红色）
2. 奖章（金色渐变）
3. 扣环（暗金色）
4. 前丝带（蓝色）
5. 数字1（暗金色）

Remember to keep the last two lines of the playground (where you extract the image from the context at the very end), and add this drawing code to the playground before those lines:

记住不要变动playground的最后两行（在你最后提取背景图像的地方），并且在那两行代码前添加描绘代码：

First, set up the non-standard colors you need.
首先设置你要的非标准颜色。

```
//Gold colors
let darkGoldColor = UIColor(red: 0.6, green: 0.5, blue: 0.15, alpha: 1.0)
let midGoldColor = UIColor(red: 0.86, green: 0.73, blue: 0.3, alpha: 1.0)
let lightGoldColor = UIColor(red: 1.0, green: 0.98, blue: 0.9, alpha: 1.0)
```

This should all look familiar by now. Notice that the colors appear in the right margin of the playground as you declare them.

这些现在应该看起来非常熟悉了。注意当你声明颜色的时候，颜色会出现在palyground的右边。

Add the drawing code for the red part of the ribbon:

下面的代码将绘制奖章的红色丝带：

```
//Lower Ribbon
var lowerRibbonPath = UIBezierPath()
lowerRibbonPath.moveToPoint(CGPointMake(0, 0))
lowerRibbonPath.addLineToPoint(CGPointMake(40,0))
lowerRibbonPath.addLineToPoint(CGPointMake(78, 70))
lowerRibbonPath.addLineToPoint(CGPointMake(38, 70))
lowerRibbonPath.closePath()
UIColor.redColor().setFill()
lowerRibbonPath.fill()
```

Nothing too new here, just creating a path and filling it. You should see the red path appear in the right hand pane.

没有什么新东西，就是创建一个路径并且填充它。你应该看见红色的路径出现在右侧。

Add the code for the clasp:

添加描绘扣环的代码：

```
//Clasp
 
var claspPath = UIBezierPath(roundedRect: 
                           CGRectMake(36, 62, 43, 20), 
                           cornerRadius: 5)
claspPath.lineWidth = 5
darkGoldColor.setStroke()
claspPath.stroke()
```

Here you make use of **UIBezierPath(roundedRect:)** with rounded corners by using the **cornerRadius** parameter. The clasp should draw in the right pane.

这里你将通过**UIBezierPath(roundedRect:)**中的**cornerRadius**这个参数来设置圆角。
扣环应该在右侧显示了。

Add the code for the medallion:

添加绘制奖章的代码：

```
//Medallion
 
var medallionPath = UIBezierPath(ovalInRect: 
                    CGRect(origin: CGPointMake(8, 72), 
                             size: CGSizeMake(100, 100)))
//CGContextSaveGState(context)
//medallionPath.addClip()
let gradient = CGGradientCreateWithColors(
                      CGColorSpaceCreateDeviceRGB(), 
                      [darkGoldColor.CGColor, 
                       midGoldColor.CGColor, 
                       lightGoldColor.CGColor],
                      [0, 0.51, 1])
CGContextDrawLinearGradient(context,
                            gradient, 
                            CGPointMake(40, 40), 
                            CGPointMake(40,162), 
                             0)
//CGContextRestoreGState(context)
```
Notice the commented out the lines. These are here to temporarily show how the gradient is drawn:

注意注释部分，这些都是暂时显示颜色渐变是如果写的：
![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-SquareGradient.png)

To put the gradient on a slant, so that it goes from top-left to bottom-right, change the end x coordinate of the gradient. Alter the CGContextDrawLinearGradient() code to:

为了使颜色从左上角至右下角渐变，改变渐变最终点的x坐标，修改**CGContextDrawLinearGradient()**中的代码：

```
CGContextDrawLinearGradient(context,
                            gradient, 
                            CGPointMake(40, 40), 
                            CGPointMake(100,160), 
                             0)
``` 
![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-SkewedGradient.png)

Now uncomment those three lines in the medallion drawing code to create a clipping path to constrain the gradient within the medallion’s circle.

现在取消奖章那部分代码中的三行注释从而创建了一个剪切路径把渐变图案约束在奖章的圈圈内。

Just as you did when drawing the graph in part two, you save the context’s drawing state before adding the clipping path, and restore it after the gradient is drawn so that the context is no longer clipped.

就像你在第二部分绘制图形是一样，你在添加剪切路径前保存了绘画上下文，在渐变画好自后你有将上下文恢复了回去，所以上下文不再是被剪切的状态了。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-ClippedGradient.png)

To draw the solid internal line of the medal, use the medallion’s circle path, but scale it before drawing. Instead of transforming the whole context, you’ll just apply the transform to one path.

用奖牌的圆圈路径来绘制奖牌上的实线，但绘制前要改变比例。你只将改变应用到一个路径上而不是转变整个context。

Add this code after the medallion drawing code:

在绘制奖章的代码下添加：

```
//Create a transform
//Scale it, and translate it right and down
var transform = CGAffineTransformMakeScale(0.8, 0.8)
transform = CGAffineTransformTranslate(transform, 15, 30)
 
medallionPath.lineWidth = 2.0
 
//apply the transform to the path
medallionPath.applyTransform(transform)
medallionPath.stroke()
```
![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-MedalOutline.png)

This scales the path down to 80 percent of its original size, and then translates the path to keep it centered within the gradient view.

这将路径的尺寸缩小至原始尺寸的80%，并转换路径使其人在渐变视图中居中。

Add the upper ribbon drawing code after the internal line code:
之后添加前丝带的绘制代码：

```
//Upper Ribbon
 
var upperRibbonPath = UIBezierPath()
upperRibbonPath.moveToPoint(CGPointMake(68, 0))
upperRibbonPath.addLineToPoint(CGPointMake(108, 0))
upperRibbonPath.addLineToPoint(CGPointMake(78, 70))
upperRibbonPath.addLineToPoint(CGPointMake(38, 70))
upperRibbonPath.closePath()
 
UIColor.blueColor().setFill()
upperRibbonPath.fill()
```
This is very similar to the code you added for the lower ribbon – making a bezier path and filling it.

这和你之前给后丝带添加的代码非常相似，创建一个bezier路径并且填充。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/3-UpperRibbon.png)

The last step is to draw the number one on the medal. Add this code after //Upper Ribbon:

最后一步是在奖章上画数字1，代码如下：

```
//Number One
 
//Must be NSString to be able to use drawInRect()
let numberOne = "1"
let numberOneRect = CGRectMake(47, 100, 50, 50)
let font = UIFont(name: "Academy Engraved LET", size: 60)
let textStyle = NSMutableParagraphStyle.defaultParagraphStyle()
let numberOneAttributes = [
  NSFontAttributeName: font!,
  NSForegroundColorAttributeName: darkGoldColor]
numberOne.drawInRect(numberOneRect, 
                     withAttributes:numberOneAttributes)
```
Here you define a **String** with text attributes, and draw it into the drawing context using drawInRect(_:).

在这里你定义了一个字符串并设置了字体属性，然后使用**drawInRect(_:).**在context进行了重绘

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-NumberOne.png)

Looking good!

看上去不错

You’re getting close, but it’s looking a little two-dimensional — it would be nice to have some drop shadows.

你已经很接近成功了，但它看起来有一些二维 － 如果加上一些阴影会更好。

##Shadows 阴影

To create a shadow, you need three elements: the color, the offset (distance and direction of the shadow) and the blur.

创建一个阴影需要三个要素：颜色，偏移（阴影的距离和方向）和模糊

At the top of the playground, after defining the gold colors but just before the //Lower Ribbon, insert this shadow code:

在 playground 顶部，位于自定义颜色之后但在//Lower Ribbon之前，插入实现阴影的代码：

```
//Add Shadow
let shadow:UIColor = UIColor.blackColor().colorWithAlphaComponent(0.80)
let shadowOffset = CGSizeMake(2.0, 2.0)
let shadowBlurRadius: CGFloat = 5
 
CGContextSetShadowWithColor(context, 
                            shadowOffset, 
                            shadowBlurRadius, 
                            shadow.CGColor)
```

Okay, that makes a shadow, but the result is probably not what you pictured. Why is that?

Okay，这能给我们一个阴影，但结果并不是我们所想象的那样。为什么呢？

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-MessyShadows.png)

When you draw an object into the context, this code creates a shadow for each object.

当你在上下文中绘制一个对象时，该代码会给所有的对象都创建阴影。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/3-IndividualShadows.png)

Ah-ha! Your medal comprises five objects. No wonder it looks a little fuzzy.
Fortunately, it’s pretty easy to fix. Simply group drawing objects with a transparency layer, and you’ll only draw one shadow for the whole group.

啊啊啊啊啊，你的奖章由五个对象所组成。难怪看起来有点模糊。幸运的是这改起来比较容易。就是简单的把所有的对象通过一个透明的layer组起来，然后你只需要对整个对象组绘制一个阴影就可以。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-GroupedShadow.png)

Add the code to make the group after the shadow code. Start with this:

在阴影代码后添加组合对象的代码。以这段开头：

```
CGContextBeginTransparencyLayer(context, nil)
```

When you begin a group you also need to end it, so add this next block at the end of the playground, but still before retrieving the final image:

当你开始一组，你也需要结束它。所以在playground末端添加这一段代码，但仍然在截取最后的图像之前：

```
CGContextEndTransparencyLayer(context)
```
Now you’ll have a completed medal image with clean, tidy shadows:

现在你将有一个完整的奖牌图像，整洁的阴影。

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/3-MedalFinal.png)

##Image View Using Core Graphics Image创建一个新的图像视图

Create a new file for the Image View.
给这个图像视图创建一个新的文件。

Click **File\New\File…**and choose the **Cocoa Touch Class** template. Click Next , and name the class MedalView. Make it a subclass of UIImageView, then click Next, then click Create.

单击**File\New\File…** ,并且选择 **Cocoa Touch Class** 模版，单击Next，并且将类命名为
**MedalView**，父类为**UIImageView**，然后点击Next，点击Creat

Go to **Main.storyboard** and add a **UIImageView** as a subview of **Counter View**. Select the UIImageView, and in the **Identity Inspector** change the class to **MedalView**.

进入**Main.storyboard**，把**UIImageView** 添加为**Counter View**的一个子视图。然后选择UIImageView，在**Identity Inspector**把类改为 **MedalView**。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-MedalViewClass.png)

In the **Size Inspector**, give the Image View the coordinates X=76, Y=147, Width=80, and Height=80:

在**Size Inspector**给视图设置坐标为x=76,y=147,宽度为80,高度为80：

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-MedalViewCoordinates.png)

In the **Attributes Inspector**, change **Image Mode**to **Aspect Fit**, so that the image automatically resizes to fit the view.

在**Attributes Inspector**中改变**Image Mode**为**Aspect Fit**，这样图像可以自动调整大小适应视图。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/3-MedalAspectFit.png)

Go to MedalView.swift and add a method to create the medal:

去medalview.swift并添加一个方法用来创建奖章：

```
func createMedalImage() -> UIImage {
  println("creating Medal Image")
 
}
```

This makes a log so that you know when the image is being created.
Go to **MedalDrawing** playground, highlight and copy the entire code except for the initial import UIKit.
Go back to **MedalView.swift** and paste the playground code into createMedalImage(),
At the end of **createMedalImage()**, add:

这会打印一句话，这样你就能直到图像什么时候被创建。

进入**MedalDrawing** playground，复制整个代码除了开始的 import UIKit。

返回**MedalView.swift**把playground中的代码黏贴到**createMedalImage()**中去。

在**createMedalImage()**尾部添加：

```
return image
```

That should squash the compile error.
At the top of the class, add a property to hold the medal image:

应该会编译错误。
在类的顶部添加一个属性来持有奖牌图像：

```
lazy var medalImage:UIImage = self.createMedalImage()
```

Add a method to show the medal:

添加一个方法去显示奖牌：

```
func showMedal(show:Bool) {
  if show {
    image = medalImage
  } else {
    image = nil
  }
}
```

Go to **ViewController.swift** and add an outlet at the top of the class:

在**ViewController.swift**类顶部添加一个外部应用：

```
@IBOutlet weak var medalView: MedalView!
```

Go to Main.storyboard and connect the new MedalView to this outlet.

去Main.storyboard将new MedalView和这个外部引用相连。

Go back to **ViewController.swift** and add this method to the class:

返回**ViewController.swift**并且添加这个方法：

```
func checkTotal() {
  if counterView.counter >= 8 {
    medalView.showMedal(true)
  } else {
    medalView.showMedal(false)
  }
}
```

This shows the medal if you drink enough water for the day.

如果你今天喝了足够的水，这个奖牌将显示。

Call this method at both the end of **viewDidLoad()** and **btnPushButton(_:)**:

在**viewDidLoad()** 和 **btnPushButton(_:)**这两个方法底部调用:

```
checkTotal()
```

Build and run the application. It should look like this:

编译并运行应用，它应该像这样：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/3-CompletedApp.png)

In the debug console, you’ll see the creating Medal Image log only outputs when the counter reaches eight and displays the medal, and this is because medalImage uses a lazy declaration.

在调试控制台中，只有当计数器达到8时，你才会看到创建奖章的那句log，这是因为medalImage使用了一个延迟声明lazy declaration。

##Where to Go From Here? 何去何从

You’ve come a long way in this epic tutorial series. You’ve mastered the basics of Core Graphics: drawing paths, creating patterns and gradients, and transforming the context. To top it all off, you learned how to put it all together in a useful app.

你已经在这部系列教程中走了很长的路。你应该已经掌握了Core Graphics的基本知识，创建图案和渐变还有转换context，最为重要的是，你学会了如何在app中融汇地使用它们。

Download the complete version of [Flo right here](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part3-6.31.zip). This version also includes extra sample data and radial gradients to give the buttons a nice UI touch so they respond when pressed.

在这[Flo right here](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part3-6.31.zip)下载完整的版本。这个版本还包括一些额外地数据和辐射渐变让按钮在被按下的时候能给用户一个更好的UI体验。

I hope you enjoyed making Flo, and that you’re now able to make some stunning UIs using nothing but Core Graphics and UIKit! If you have any questions, comments, or you want to hash out how to draw a trophy instead of a medal, please join the forum discussion below.

我希望在制作Flo时你乐在其中，并且你能只用Core Graphics和UIKit做一些惊叹漂亮的UI！如果你有任何问题，意见，或者你想讨论如何绘制一个奖杯，而不是一个奖章，请加入论坛讨论下。
