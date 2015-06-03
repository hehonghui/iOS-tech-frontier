Core Graphics 教程第三部分(Swift) - Patterns 和 Playgrounds
---
>* 原文链接：[Core Graphics Tutorial Part 3: Patterns and Playgrounds](http://www.raywenderlich.com/90695/modern-core-graphics-with-swift-part-3)
* 原文作者：[ Caroline Begbie ](http://www.raywenderlich.com/u/caroline)
* 译文出自：[开发者前线](www.devtf.cn)
* 译者：[MrLoong](https://github.com/MrLoong)
* 校对者：[samw00](https://github.com/samw00)

欢迎回到Core Graphics 教学系列的第三也是最后一部分！Flo，你的water drinking tracking app将使用Core Graphics 进行最终的改进。

在[第一部分](http://www.raywenderlich.com/90690/modern-core-graphics-with-swift-part-1)，你通过UIKit画了三个自定义形状的控件,[在第二部分](http://www.raywenderlich.com/90693/modern-core-graphics-with-swift-part-2)你创建了一个视图用来显示用户在过去一周的喝水量，并且研究了transforming the context transformation matrix (CTM)

在这第三也是最后一部分，你将使**Flo**升级到它的最终形态，特别的是你将：

* 创建一个重复的背景图案
* 为成功每天喝掉8杯水的用户从头到尾画一枚奖章

如果你还没有这个项目的源代码，从本系列的第二部分下载[**Flo工程**](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part2-6.3.zip)

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/FinalApp-192x320.png)

##Background Repeating Pattern 背景重复模式
你在这部分的任务是使用UIKit的方式创建这个背景图案：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-FinalBackground-300x500.png)

~~~
Note: If you need to optimize for speed, then work through
Core Graphics Tutorial: Patterns which demonstrates a basic 
way to create patterns with Objective-C and Core Graphics. 
For most purposes, like when the background is only drawn 
once, UIKit’s easier wrapper methods should be acceptable.
~~~
~~~
注意：如果你需要对速度进行优化，可以去看[Core Graphics Tutorial:Patters](http://www.raywenderlich.com/33496/core-graphics-tutorial-patterns)里面演示一个使用Object－c和Core Graphics的基本方法去创建一个格局.在大多数情况下当背景仅被描绘一次，使用UIKit封装好的简易方法应该是可以接受的。
~~~

点击**File\New\File…**并且选择IOS **iOS\Source\Cocoa Touch Class** 模版创建一个类名为**BackgroundView**的UIView。点击下一步然后创建

进入**Main.storyboard**，在 **Identity Inspector**选择ViewController主界面，改变为**BackgroundView**

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-BackgroundViewStoryboard3-480x187.png)

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

现在你的storyboard背景应该是黄色的，更多的细节参考上面的代码：

1. **lightColor**和**darkColor**拥有**@IBInspectable**属性，所以更容易去配置背景颜色。你使用橙色和黄色作为临时色，这样你就能直到到底在发生什么事。**patternSize**控件控制着重复形式的大小。它的初始值设置为large，所以更容易看清发生了什么。
2. **UIGraphicsGetCurrentContext()**让你了解视图上下文同样也是**drawRect(_:)**将会绘画的区域。
3. 使用Core Graphics的方法**CGContextSetFillColorWithColor()** 去设置当前上下文的填充色。注意当你使用Core Graphics时，需要用到**darkColor**的一个属性**CGColor**。
4. **CGContextFillRect()**使用当前填充色填充当前上下文的整个背景，而不是建立一个矩形路径。

你现在需要使用**UIBezierPath()**画这三个橙色的三角形。图中的点对应代码中的数字。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-GridPattern.png)

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

注意你是如何使用一个路径画三个三角形的，**moveToPoint(_:)**就像在纸上抬起你的笔从一个地方绘制和移动到另一个地方。

你的storyboard现在应该有一个橙色和黄的的图像在你背景的左上方。

到目前为止，你都是直接在一个视图的上下文中直接绘画。为了能重复这个图案，你需要在上下文外创建一个图像，然后用这个图像作为上下文中的背景图案。

找到下面这段代码，在drawRect(_:)的顶部，但是在下面这段代码之后：

```
et drawSize = CGSize(width: patternSize, height: patternSize)
```

然后插入下面的代码

```
UIGraphicsBeginImageContextWithOptions(drawSize, true, 0.0)
let drawingContext = UIGraphicsGetCurrentContext()
 
//set the fill color for the new context
darkColor.setFill()
CGContextFillRect(drawingContext,
      CGRectMake(0, 0, drawSize.width, drawSize.height))
```

嘿！那些橘黄色三角形从面板中消失了，它们去哪了？

**UIGraphicsBeginImageContextWithOptions()**创建了一个新的上下文，并且将其设置为当前的绘图上下文，所以你在这个新的上下文中作画。刚才方法的参数为：

*  context的大小
*  背景是否透明— 如果你需要透明的, 这里设置为false.
*  context scale的大小. 如果要适应视网膜屏幕, 这里应该设置为2, 如果要适应iPhone 6 Plus, 应该为3.0. 然而这里使用0.0将自动适配屏幕

然后你用 **UIGraphicsGetCurrentContext()**得到这个新的上下文的引用。

然后你将这个新的context设置为黄色。你可以通过设这context的属性opacity为false让原来的背景色显示，但是画一个不透明的context比画一个透明的要快得多，凭这一点就应该选opaque。

在**drawRect(_:)**的末尾添加下面的代码：

```
let image = UIGraphicsGetImageFromCurrentImageContext()
UIGraphicsEndImageContext()

```

在当前的context提取一个UIImage。当你使用**UIGraphicsEndImageContext()**结束当前context时，描绘context还原为视图context，所以任何未来在**drawRect(_:)** 中的的绘图都会反应在viwe中。

为了使图像作为一个可重复的图案，在drawRect(_:)末尾添加代码：

```
UIColor(patternImage: image).setFill()
CGContextFillRect(context, rect)
```

这会让图片当作UIColor中的一个新的颜色来用，而不是一个纯色。

编译并且运行app，你现在的app应该有一个相当亮眼的背景：

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/3-BoldBackground2.png)

去Main.storyboard，选择background view，并且在Attributes Inspector改变IBInspectable属性的值为：

* Light Color: RGB(255, 255, 242)
* Dark Color: RGB(223, 255, 247)
* Pattern Size: 30

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-BackgroundColors2-473x320.png)

尝试去画一些不同的背景图案，看看除了三角形，你能不能把背景图案变成polka dot。

当然，你可以使用自己的非矢量图作为背景图案

##Drawing Images 绘制图像

在本教程的最后阶段，你将为喝够水的人送上一个奖牌，当计数器纪录的数量达到8杯的时候这个奖牌将出现。

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/3-MedalFinal.png)

我知道这肯定比不上博物馆里的一件艺术品，但是请你知道，如果你改进它，我并不会生气，甚至你可以通过画一个奖杯而不是奖牌来升华一下。

你将在**Swift Playground**中绘制这个奖牌，而不是用**@IBDesignable**，然后把代码复制到**UIImageView**的子类中。虽然与storyboards互动通长是很有用的，但他们有局限性。他们仅能描绘简单的代码，当你创建一个复杂的设计时，storyboards通长很耗时间。

在这种情况下，只有当用户喝满八杯水的时候才需要画一个图像。如果用户从来没有到达到过目标，那就根本不需要绘制奖牌。

一旦绘制，他也不需要使用drawRect(_:)和setNeedsDisplay().进行重绘。

是时候作画了。首先点击Standard Editor按钮，让Xcode返回单一界面编辑模式。

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-StandardEditor.png)

点击**File\New\File…**并且选择IOS Playground模版。点击 Next 命名playground为MedalDrawing然后点击Create。

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

这将创建一个绘制上下文，就像你之前刚给重复背景图片做的是一回事。

注意最后两行。你总是需要它们在playground底部，这样你就可以在playground中预览图片。

下一步，在灰色的结果栏中单击代码右边的＋按钮：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/ShowFinishedImage.gif)

这将在代码的底部预览你的图像。图像将根据你代码的每次更新而更新。

在你作画之前，最好能在你的脑海里先有一张绘制草图，看看我在构造这篇tutorial时所构想的“杰作”：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-Sketch.png)

下面是绘制奖牌的顺序:

1. 后丝带（红色）
2. 奖章（金色渐变）
3. 扣环（暗金色）
4. 前丝带（蓝色）
5. 数字1（暗金色）

记住不要变动playground的最后两行（在你最后提取背景图像的地方），并且在那两行代码前添加描绘代码：

首先设置你要的非标准颜色。

```
//Gold colors
let darkGoldColor = UIColor(red: 0.6, green: 0.5, blue: 0.15, alpha: 1.0)
let midGoldColor = UIColor(red: 0.86, green: 0.73, blue: 0.3, alpha: 1.0)
let lightGoldColor = UIColor(red: 1.0, green: 0.98, blue: 0.9, alpha: 1.0)
```

这些现在应该看起来非常熟悉了。注意当你声明颜色的时候，颜色会出现在palyground的右边。

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

没有什么新东西，就是创建一个路径并且填充它。你应该看见红色的路径出现在右侧。

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

这里你将通过**UIBezierPath(roundedRect:)**中的**cornerRadius**这个参数来设置圆角。
扣环应该在右侧显示了。

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
注意注释部分，这些都是暂时显示颜色渐变是如何写的：

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-SquareGradient.png)

为了使颜色从左上角至右下角渐变，改变渐变最终点的x坐标，修改**CGContextDrawLinearGradient()**中的代码：

```
CGContextDrawLinearGradient(context,
                            gradient, 
                            CGPointMake(40, 40), 
                            CGPointMake(100,160), 
                             0)
``` 
![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-SkewedGradient.png)

现在取消奖章那部分代码中的三行注释从而创建了一个剪切路径把渐变图案约束在奖章的圈圈内。

就像你在第二部分绘制图形是一样，你在添加剪切路径前保存了绘画上下文，在渐变画好自后你有将上下文恢复了回去，所以上下文不再是被剪切的状态了。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-ClippedGradient.png)

用奖牌的圆圈路径来绘制奖牌上的实线，但绘制前要改变比例。你只将改变应用到一个路径上而不是转变整个context。

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

这将路径的尺寸缩小至原始尺寸的80%，并转换路径使其人在渐变视图中居中。

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

这和你之前给后丝带添加的代码非常相似，创建一个bezier路径并且填充。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/3-UpperRibbon.png)

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

在这里你定义了一个字符串并设置了字体属性，然后使用**drawInRect(_:).**在context进行了重绘

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-NumberOne.png)

看上去不错

你已经很接近成功了，但它看起来有一些二维 － 如果加上一些阴影会更好。

##Shadows 阴影

创建一个阴影需要三个要素：颜色，偏移（阴影的距离和方向）和模糊

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

Okay，这能给我们一个阴影，但结果并不是我们所想象的那样。为什么呢？

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-MessyShadows.png)

当你在上下文中绘制一个对象时，该代码会给所有的对象都创建阴影。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/3-IndividualShadows.png)

啊！你的奖章由五个对象所组成。难怪看起来有点模糊。幸运的是这改起来比较容易。就是简单的把所有的对象通过一个透明的layer组起来，然后你只需要对整个对象组绘制一个阴影就可以。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-GroupedShadow.png)

在阴影代码后添加组合对象的代码。以这段开头：

```
CGContextBeginTransparencyLayer(context, nil)
```

当你开启一组，你也需要结束它。所以在playground末端添加这一段代码，但仍然在截取最后的图像之前：

```
CGContextEndTransparencyLayer(context)
```

现在你将有一个完整的奖牌图像，整洁的阴影。

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/3-MedalFinal.png)

##Image View Using Core Graphics Image创建一个新的图像视图

给这个图像视图创建一个新的文件。

单击**File\New\File…** ,并且选择 **Cocoa Touch Class** 模版，单击Next，并且将类命名为**MedalView**，父类为**UIImageView**，然后点击Next，点击Creat

进入**Main.storyboard**，把**UIImageView** 添加为**Counter View**的一个子视图。然后选择UIImageView，在**Identity Inspector**把类改为 **MedalView**。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/3-MedalViewClass.png)

在**Size Inspector**给视图设置坐标为x=76,y=147,宽度为80,高度为80：

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/3-MedalViewCoordinates.png)

在**Attributes Inspector**中改变**Image Mode**为**Aspect Fit**，这样图像可以自动调整大小适应视图。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/3-MedalAspectFit.png)

去medalview.swift并添加一个方法用来创建奖章：

```
func createMedalImage() -> UIImage {
  println("creating Medal Image")
 
}
```

这会打印一句话，这样你就能直到图像什么时候被创建。

进入**MedalDrawing** playground，复制整个代码除了开始的 import UIKit。

返回**MedalView.swift**把playground中的代码黏贴到**createMedalImage()**中去。

在**createMedalImage()**尾部添加：

```
return image
```

应该会编译错误。
在类的顶部添加一个属性来持有奖牌图像：

```
lazy var medalImage:UIImage = self.createMedalImage()
```

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

在**ViewController.swift**类顶部添加一个外部应用：

```
@IBOutlet weak var medalView: MedalView!
```

去Main.storyboard将new MedalView和这个外部引用相连。

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

如果你今天喝了足够的水，这个奖牌将显示。

在**viewDidLoad()** 和 **btnPushButton(_:)**这两个方法的底部调用:

```
checkTotal()
```

编译并运行应用，它应该像这样：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/3-CompletedApp.png)

在调试控制台中，只有当计数器达到8时，你才会看到创建奖章的那句log，这是因为medalImage使用了一个延迟声明lazy declaration。

##Where to Go From Here? 何去何从

你已经在这部系列教程中走了很长的路。你应该已经掌握了Core Graphics的基本知识，创建图案和渐变还有转换context，最为重要的是，你学会了如何在app中融汇地使用它们。

在这[Flo right here](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part3-6.31.zip)下载完整的版本。这个版本还包括一些额外地数据和辐射渐变让按钮在被按下的时候能给用户一个更好的UI体验。

我希望在制作Flo时你乐在其中，并且你能只用Core Graphics和UIKit做一些惊叹漂亮的UI！如果你有任何问题，意见，或者你想讨论如何绘制一个奖杯，而不是一个奖章，请加入论坛讨论下。
