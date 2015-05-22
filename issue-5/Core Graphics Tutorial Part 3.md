* 原文链接：[Core Graphics Tutorial Part 3: Patterns and Playgrounds](http://www.raywenderlich.com/90695/modern-core-graphics-with-swift-part-3)
* 原文作者：[ Caroline Begbie ](http://www.raywenderlich.com/u/caroline)
* [译文出自：开发者前线 www.devtf.cn](www.devtf.cn)
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

欢迎回到Core Graphics 教学系列的第三部分和最后一部分！Flo，你的water drinking tracking app将使用Core Graphics 进行最终的改进。

在[在第一部分]([part one](http://www.raywenderlich.com/90690/modern-core-graphics-with-swift-part-1))，你画了三个自定义形状的控件以及UIKit,[在第二部分](http://www.raywenderlich.com/90693/modern-core-graphics-with-swift-part-2)你创建了一个视图用来显示用户在过去一周的喝水量，并且研究了transforming the context transformation matrix (CTM)

在这第三部分和最后一部分，你将使用它的最终格式，特别的是你将使用：

* 创建一个重复的图案背景
* 从开始到结束，为成功每天喝掉8杯水的用户画一枚奖章

如果你不想做，从本系列的第二部分下载[**Flo工程**](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part2-6.3.zip)

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
注意：如果你需要对速度进行优化，接下来的工作就是通过[Core Graphics Tutorial](http://www.raywenderlich.com/33496/core-graphics-tutorial-patterns)：
演示一个基本的方法去创建一个格局使用Object－c和Core Graphics.针对实用性问题，
当背景仅被描绘一次，使用UIKit封装好的简易方法应该是可以被接受的

Go to **File\New\File…**and select the iOS **iOS\Source\Cocoa Touch Class**template to create a class called **BackgroundView** with a subclass of UIView. Click Next and then Create.

Go to **Main.storyboard**, select the main view of ViewController, and change the class to **BackgroundView** in the **Identity Inspector.**

点击**File\New\File…**并且选择IOS **iOS\Source\Cocoa Touch Class** 模版创建一个累起名为**BackgroundView**的UIView。点击下一步然后创建

进入**Main.storyboard**，在 **Identity Inspector**选择ViewController主界面，改变为**BackgroundView**
![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-BackgroundViewStoryboard3-480x187.png)

Set up **BackgroundView.swift** and **Main.storyboard** so they are side-by-side, using the **Assistant Editor**.
Replace the code in **BackgroundView.swift** with:

建立**BackgroundView.swift**和**Main.storyboard**，所以他们是并排的，使用**Assistant Editor**
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

1. **ightColor** and **darkColor** have **@IBInspectable** attributes so it’s easier to configure background colors later on. You’re using orange and yellow as temporary colors, just so you can see what’s happening. **patternSize**controls the size of the repeating pattern. It’s initially set to large, so it’s easy to see what’s happening.
2. **UIGraphicsGetCurrentContext()** gives you the view’s context and is also where **drawRect(_:)**draws.
3. Use the Core Graphics method **CGContextSetFillColorWithColor()** to set the current fill color of the context. Notice that you need to use **CGColor**, a property of **darkColor** when using Core Graphics.
4. Instead of setting up a rectangular path,**CGContextFillRect()** fills the entire context with the current fill color.

You’re now going to draw these three orange triangles using **UIBezierPath()**. The numbers correspond to the points in the following code:

1. **ightColor**和**darkColor**拥有**@IBInspectable**属性，所以他更容易去配置背景颜色。你使用橙色和黄色作为临时色，你可以看看发生了什么。**patternSize**控件控制着重复形式的大小。它的初始值设置的大一些，所以更容易看清发生了什么。
2. **UIGraphicsGetCurrentContext()**让你了解上下部分哪些**drawRect(_:)**被描述。
3. 使用Core Graphics的方法**CGContextSetFillColorWithColor()** 去设置当前上下文的填充色。注意当你使用Core Graphics需要用到**darkColor**的一个属性
4. 而不是建立一个矩形路径，**CGContextFillRect()**使用当前填充色填充当前上下文的整个背景

你现在需要使用**UIBezierPath()**画三个橙色的三角形。数字对应一下代码中的点
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

注意你如何使用一个路径画三个三角形，就像在纸上抬起你的笔从一个地方绘制和移动到另一个地方

Your storyboard should now have an orange and yellow image at the top left of your background view. 

你的storyboard现在应该有一个橙色和黄的的图像在你背景的左上方

So far, you’ve drawn directly into the view’s drawing context. To be able to repeat this pattern, you need to create an image outside of the context, and then use that image as a pattern in the context.

到目前为止，你已经直接描绘了视图上下部分，可以重复此模式，你需要创建一个外部图像，然后使用这个图片作为背景图案

Find the following. It’s close to the top of drawRect(_:), but after:
找到一一下部分，它接近顶部，接着

```
et drawSize = CGSize(width: patternSize, height: patternSize)
```
Add the following code where it conveniently says **Insert code here**:

然后插入一下代码

```
UIGraphicsBeginImageContextWithOptions(drawSize, true, 0.0)
let drawingContext = UIGraphicsGetCurrentContext()
 
//set the fill color for the new context
darkColor.setFill()
CGContextFillRect(drawingContext,
      CGRectMake(0, 0, drawSize.width, drawSize.height))
```

Hey! Those orange triangles disappeared from the storyboard. Where’d they go?
嘿，那些橘黄色三角形从面板中消失了，他们去哪了？

**UIGraphicsBeginImageContextWithOptions()**creates a new context and sets it as the current drawing context, so you’re now drawing into this new context. The parameters of this method are:

*  The size of the context.
*  Whether the context is opaque — if you need transparency, then this needs to be false.
*  The scale of the context. If you’re drawing to a retina screen, this should be 2.0, and if to an iPhone 6 Plus, it should be 3.0. However, this uses 0.0, which ensures the correct scale for the device is automatically applied.

**UIGraphicsBeginImageContextWithOptions()**创建了一个新的视图，并且将其设置为当前的绘图。刚方法的参数为：

*  context大小
*  背景是否透明— 如果你需要透明的, 这里设置为false.
*  context的大小. 如果你话一个视网膜屏幕, 这里应该设置为2, 对于 iPhone 6 Plus, 他应该为3.0. 然而这里使用0将自动适配屏幕

Then you used **UIGraphicsGetCurrentContext()** to get a reference to this new context.

然后使用 **UIGraphicsGetCurrentContext()**得到这个新的context

You then filled the new context with yellow. You could have let the original background show through by setting the context opacity to false, but it’s faster to draw opaque contexts than it is to draw transparent, and that’s argument enough to go opaque.

将这个新的context设置为黄色。你可以通过设这context的属性opacity为false让原来的背景色显示。

Add this code to the end of **drawRect(_:)**:
在**drawRect(_:)**添加一下代码

```
let image = UIGraphicsGetImageFromCurrentImageContext()
UIGraphicsEndImageContext()

```

This extracts a UIImage from the current context. When you end the current context with **UIGraphicsEndImageContext()**, the drawing context reverts to the view’s context, so any further drawing in **drawRect(_:)** happens in the view.

在当前的context提取。当你使用**UIGraphicsEndImageContext()**结束context，描绘的context还原视图的context，所以在任何未来在**drawRect(_:)** 中的的绘图都发生在view

To draw the image as a repeated pattern, add this code to the end of drawRect(_:):

把图像作为重复图案，在drawRect(_:)末尾添加代码：

```
UIColor(patternImage: image).setFill()
CGContextFillRect(context, rect)
```

This creates a new UIColor by using an image as a color instead of a solid color.

创建了一个新的UIColor通过使用图像作为背景颜色，而不是固定色。

Build and run the app. You should now have a rather bright background for your app. :

构建并且运行app，你现在的app应该有一个明亮的背景：
![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/3-BoldBackground2.png)

Go to Main.storyboard, select the background view, and in the Attributes Inspector change the @IBInspectable values to the following:

去Main.storyboard，选择background view，并且在Attributes Inspector改变IBInspectable属性的值为以下：

* Light Color: RGB(255, 255, 242)
* Dark Color: RGB(223, 255, 247)
* Pattern Size: 30
![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-BackgroundColors2-473x320.png)

Experiment a little more with drawing background patterns. See if you can get a polka dot pattern as a background instead of the triangles.

尝试去画一些背景图案，看看能不能得到一个园作为背景图案，而不是三角形。

And of course, you can substitute your own non-vector images as repeating patterns.

当然，你可以使用自己的非矢量图作为背景图案

##Drawing Images 绘制图像

In the final stretch of this tutorial, you’ll make a medal to handsomely reward users for drinking enough water. This medal will appear when the counter reaches the target of eight glasses.

在本教程的最后阶段，你将为和足够水的人送上一个奖牌，当计数器纪录的数量达到8杯的时候奖牌将出现

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/3-MedalFinal.png)

I know that’s certainly not a museum-worthy piece of art, so please know that I won’t be offended if you improve it, or even take it to the next level by draw a trophy instead of a medal. ;]

我知道那并会像博物馆里的一件艺术品，所以请你知道，如果你提升它，我并不会生气，甚至你可以通过画一个奖杯而不是奖牌

Instead of using **@IBDesignable**, you’ll draw it in a **Swift Playground**, and then copy the code to a **UIImageView** subclass. Though interactive storyboards are often useful, they have limitations; they only draw simple code, and storyboards often time out when you create complex designs.

你描绘它将使用swift，然后复制这段代码到**UIImageView**子集。虽然与storyboards互动通长是很有用的，但他们有局限性。他们仅能描绘简单的代码，storyboards通长很耗时间当你创建一个复杂的设计。

In this particular case, you only need to draw the image once when the user drinks eight glasses of water. If the user never reaches the target, there’s no need to make a medal.

在这种情况下，当用户喝满八杯水的时候仅需要画一个图像。如果用户从来没有到达到过目标，根本不需要绘制奖牌。

Once drawn, it also doesn’t need to be redrawn with drawRect(_:) and setNeedsDisplay().

一旦绘制，他也不需要使用drawRect(_:)和setNeedsDisplay().进行重绘。

Time to put the brush to the canvas. First return Xcode to single viewing, rather than side-by-side, by clicking the Standard Editor icon:

时间画笔。首先返回Xcode界面点击编辑图标，操作并不是并行的。

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-StandardEditor.png)

Go to **File\New\File…** and choose the iOS Playground template. Click Next, name the playground MedalDrawing and then click Create.

进入 **File\New\File…**并且选择IOS Playground 模版。点击 Next 命名为 playground 为MedalDrawing 然后点击Create

Replace the playground code with:
添加一下代码：

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

创建描绘一个context，就像你做了一个重叠图片

Take note of these last two lines; you always need them at the bottom of the playground so you can preview the image in the playground.

注意最后两行。你总是需要他们在playground底部，所以你可以预览palyground的图片。

Next, in the gray results column click the + button to the right of this code:

其次在灰色的结果栏中单击代码右边的＋按钮：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/ShowFinishedImage.gif)

this will place a preview image underneath the code. The image will update with every change that you make to the code.

这将在代码的地步预览你图像。图像将根据你代码的每次更新而更新。

It’s often best to do a sketch to wrap your head around the order you’ll need to draw the elements — look at the “masterpiece” I made while conceptualizing this tutorial:

我么最好做一个草图，把头命令藏在你需绘制的图像中，看看“masterpiece”我构思的教程：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-Sketch.png)

This is the order to draw the medal:

1. The back ribbon (red) 
2. The medallion (gold gradient) 
3. The clasp (dark gold) 
4. The front ribbon (blue) 
5. The number 1 (dark gold) 

这是为了绘制奖牌:

1. 回丝带（红色）
2. 奖章（金梯）
3. 扣子（暗金色）
4. 前带（蓝色）
5. 1号（暗金色）

Remember to keep the last two lines of the playground (where you extract the image from the context at the very end), and add this drawing code to the playground before those lines:

记住改变playground最后两行（在你最后提取背景图像的地方），并且在这之前添加描绘代码到playground：

First, set up the non-standard colors you need.
首先设置你要下的非标准颜色。

```
//Gold colors
let darkGoldColor = UIColor(red: 0.6, green: 0.5, blue: 0.15, alpha: 1.0)
let midGoldColor = UIColor(red: 0.86, green: 0.73, blue: 0.3, alpha: 1.0)
let lightGoldColor = UIColor(red: 1.0, green: 0.98, blue: 0.9, alpha: 1.0)
```

This should all look familiar by now. Notice that the colors appear in the right margin of the playground as you declare them.

由现在看起开这应该很熟悉，当你声明的时候，注意到的颜色出现在palyground右边。

Add the drawing code for the red part of the ribbon:

在回丝带部分添加绘图带代码：

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

没有什么新东西，只是创建一个路径并且填充它。你应该看见红色的路径出现在右侧的窗格中。

Add the code for the clasp:

添加扣子描绘代码：

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

在这里你将使用 **UIBezierPath(roundedRect:)** ，圆角半通过使用**cornerRadius**.
扣子应该在右侧显示。

Add the code for the medallion:

添加奖章代码：

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

注意注释部分，这些都是暂时显示梯度用的：
![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-SquareGradient.png)

To put the gradient on a slant, so that it goes from top-left to bottom-right, change the end x coordinate of the gradient. Alter the CGContextDrawLinearGradient() code to:

把梯度斜着，使它从左上到右下，高变梯度，结束x坐标。改变 **CGContextDrawLinearGradient()**

```
CGContextDrawLinearGradient(context,
                            gradient, 
                            CGPointMake(40, 40), 
                            CGPointMake(100,160), 
                             0)
``` 
![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-SkewedGradient.png)

Now uncomment those three lines in the medallion drawing code to create a clipping path to constrain the gradient within the medallion’s circle.

现在取消徽章那部分的三行注释并且创建一个剪切路径约束在奖章圈的梯度范围内

Just as you did when drawing the graph in part two, you save the context’s drawing state before adding the clipping path, and restore it after the gradient is drawn so that the context is no longer clipped.

就像你在绘制图形的第二部分的时候你保存了context之前的剪切路径。恢复后的绘图不会再重叠

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-ClippedGradient.png)

To draw the solid internal line of the medal, use the medallion’s circle path, but scale it before drawing. Instead of transforming the whole context, you’ll just apply the transform to one path.

画奖牌的内部线，使用奖牌的圆圈路径，但是在之前规定的区域绘制，而不是转变整个context。你只会应用变换的一个路径。

Add this code after the medallion drawing code:

添加奖章绘制代码：

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

这个尺寸的路径为原始尺寸的80%，然后将路径保存为中心梯度的内景色。

Add the upper ribbon drawing code after the internal line code:
添加丝带编码：

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

对于添加低位丝带是非常相似的代码，制作一个bezier路径并且填充他。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/3-UpperRibbon.png)

The last step is to draw the number one on the medal. Add this code after //Upper Ribbon:

最后一步是在丝带上画 1，代码如下：

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

在这里你定义了一个字符串属性，并且使用**drawInRect(_:).**在context进行了重绘

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-NumberOne.png)

Looking good!

看上去不错

You’re getting close, but it’s looking a little two-dimensional — it would be nice to have some drop shadows.

你已经很接近了，但他看起来有一些二维。它会有一些阴影会更好。

##Shadows 阴影

To create a shadow, you need three elements: the color, the offset (distance and direction of the shadow) and the blur.

创建一个阴影，需要三个要素：颜色，偏移（阴影的距离和方向）和模糊

At the top of the playground, after defining the gold colors but just before the //Lower Ribbon, insert this shadow code:

在 playground 顶部，位于金色之后插入阴影。

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

ok，做了一个阴影后，但结果并不是我们所想象的那样。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-MessyShadows.png)

When you draw an object into the context, this code creates a shadow for each object.

当你画一个对象为背景，该代码创建了每一个物体的影子。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/3-IndividualShadows.png)

Ah-ha! Your medal comprises five objects. No wonder it looks a little fuzzy.
Fortunately, it’s pretty easy to fix. Simply group drawing objects with a transparency layer, and you’ll only draw one shadow for the whole group.

啊啊啊啊啊，你的奖章，包括五个物体，难怪看起来有点模糊。幸运的是我们更改起来比较容易。简单的透明对象组对象，你只需要对整个对象组绘制一个阴影就可以。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-GroupedShadow.png)

Add the code to make the group after the shadow code. Start with this:

添加阴影代码从这里开始：

```
CGContextBeginTransparencyLayer(context, nil)
```

When you begin a group you also need to end it, so add this next block at the end of the playground, but still before retrieving the final image:

当你开始一组，你也需要结束它。所以在playground末端添加这一块。但在这之前仍然需要检索图像。

```
CGContextEndTransparencyLayer(context)
```
Now you’ll have a completed medal image with clean, tidy shadows:

现在你将有一个完整的奖牌图像，整洁的阴影。

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/3-MedalFinal.png)

##Image View Using Core Graphics Image创建一个新的图像视图

Create a new file for the Image View.

Click **File\New\File…**and choose the **Cocoa Touch Class** template. Click Next , and name the class MedalView. Make it a subclass of UIImageView, then click Next, then click Create.

单击**File\New\File…** ,并且选择 **Cocoa Touch Class** 模版，单击Next，并且将类命名为
**MedalView**，子类为**UIImageView**，然后点击Next，点击Creat

Go to **Main.storyboard** and add a **UIImageView** as a subview of **Counter View**. Select the UIImageView, and in the **Identity Inspector** change the class to **MedalView**.

进入**Main.storyboard**并且添加一个**UIImageView** **Counter View**视图。选择UIImageView，并且在**Identity Inspector**改编为 **MedalView**。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-MedalViewClass.png)

In the **Size Inspector**, give the Image View the coordinates X=76, Y=147, Width=80, and Height=80:

在**Size Inspector**给视图设置坐标为x=76,y=147,宽度为80,高度为80

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-MedalViewCoordinates.png)

In the **Attributes Inspector**, change **Image Mode**to **Aspect Fit**, so that the image automatically resizes to fit the view.

在Attributes Inspector，改变**Image Mode**为**Aspect Fit**，因此图像自动调整大小适应视图。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/3-MedalAspectFit.png)

Go to MedalView.swift and add a method to create the medal:

去medalview.swift并添加一个方法用来创建的奖章：

```
func createMedalImage() -> UIImage {
  println("creating Medal Image")
 
}
```

This makes a log so that you know when the image is being created.
Go to **MedalDrawing** playground, highlight and copy the entire code except for the initial import UIKit.
Go back to **MedalView.swift** and paste the playground code into createMedalImage(),
At the end of **createMedalImage()**, add:

制作一个日志，纪录当图像被开始创建的时候。

进入**MedalDrawing** playground，复制整个代码出了出使导入的UIKit。

返回**MedalView.swift**并且粘贴代码到playground代码中的**createMedalImage()**。

在**createMedalImage()**尾部添加：

```
return image
```

That should squash the compile error.
At the top of the class, add a property to hold the medal image:

应该编译错误。
在累的顶部添加一个属性保持奖牌图像：

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

在**ViewController.swift**类顶部添加一个外部接口：

```
@IBOutlet weak var medalView: MedalView!
```

Go to Main.storyboard and connect the new MedalView to this outlet.

去Main.storyboard连接一个新MedalView通过这个外部接口

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

在结束的时候调用**viewDidLoad()** 和 **btnPushButton(_:)**这两个方法:

```
checkTotal()
```

Build and run the application. It should look like this:

构建并运行应用，他应该像这样：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/3-CompletedApp.png)

In the debug console, you’ll see the creating Medal Image log only outputs when the counter reaches eight and displays the medal, and this is because medalImage uses a lazy declaration.

在调试控制台，当计数器达到8时，你仅仅能看见奖牌图像日志被输出，这是因为medalImage使用了一个lazy declaration。

##Where to Go From Here? 何去何从

You’ve come a long way in this epic tutorial series. You’ve mastered the basics of Core Graphics: drawing paths, creating patterns and gradients, and transforming the context. To top it all off, you learned how to put it all together in a useful app.

你已经在这部系列教程中走了很长的路，你已经掌握了Core Graphics的基本知识，创建模式和梯度。并且转换context，最高端的是，你学会了怎么把它添加到app中使用。

Download the complete version of [Flo right here](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part3-6.31.zip). This version also includes extra sample data and radial gradients to give the buttons a nice UI touch so they respond when pressed.

下载完整的版本， [Flo right here](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part3-6.31.zip).。这个版本也包括提取简单的数据和一个当你按下的时候有很好渐变的效果按钮。

I hope you enjoyed making Flo, and that you’re now able to make some stunning UIs using nothing but Core Graphics and UIKit! If you have any questions, comments, or you want to hash out how to draw a trophy instead of a medal, please join the forum discussion below.

我希望你能很享受使用Flo，你现在可以使用Core Graphics和UIKit做一些漂亮的UI！你有任何问题，意见，或者你想讨论如何绘制一个奖杯，而不是一个奖章，请加入论坛讨论下。














