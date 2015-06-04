使用CAShapeLayer来实现圆形图片加载动画
---

> * 原文链接 : [How To Implement A Circular Image Loader Animation with CAShapeLayer](http://www.raywenderlich.com/94302/implement-circular-image-loader-animation-cashapelayer)
* 原文作者 : [Rounak Jain](http://www.raywenderlich.com/u/rounak)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [Sam Lau](https://github.com/samlaudev) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  未完成

几个星期之前，Michael Villar在Motion试验中创建一个非常有趣的加载动画。

下面的GIF图片展示这个加载动画，它将一个圆形进度指示器和圆形渐现动画结合。这个组合的效果有趣，独一无二和有点迷人。

这个教程将会教你如何使用Swift和Core Animatoin来重新创建这个效果。让我们开始吧！

#基础

首先下载这个教程的[启动项目](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/ImageLoaderIndicator-Starter.zip)，然后编译和运行。过一会之后，你应该看到一个简单的image显示：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/StarterProject.png)

这个启动项目已经有些views和image加载逻辑在适当的地方。花一分钟来浏览来快速了解这个项目；那里有一个**ViewController**，**ViewController**里有一个命名为**CustomImageView**的**UIImageView**子类, 还有一个**SDWebImage**的方法被调用来加载image。

你可能注意到当你第一次运行这个app的时候，当image下载时这个app似乎会暂停几秒，然后image会显示在屏幕。

你会在两个步骤中创建这个动画：

1. **圆形进度**。首先，你会画一个圆形进度指示器，然后根据下载进度来更新它。
2. **扩展圆形图片**。第二，你会通过扩展的圆形窗口来揭示下载图片。

Follow along closely to prevent yourself from going in “circles”!

紧跟着下面步骤来逐步实现！

#创建圆形指示器

想一下关于进度指示器的基本设计。这个指示器一开始是空来展示0%进度，然后逐渐填满直到image完成下载。通过设置**CAShapeLayer**的**path**为circle来实现是相当简单。

> 注意：如果你不熟悉**CAShapeLayer**(或**CALayers**)的基本概念，可以查看Scott Gardner的[CALayer in iOS with Swift](http://www.raywenderlich.com/90488/calayer-in-ios-with-swift-10-examples)文章。

你可以通过**CAShapeLayer**的**strokeStart**和**strokeEnd**属性来控制开始和结束位置的外观。通过改变**strokeEnd**的值在0到1之间，你可以恰当地填充下载进度。

让我们试一下。通过**iOS\Source\Cocoa Touch Class template**来创建一个新的文件，文件名为**CircularLoaderView**。设置它为**UIView**的子类。

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/Screenshot-2015-01-25-19.25.43.png)

点击**Next**和**Create**。新的子类**UIView**将用来保存动画的代码。

打开**CircularLoaderView.swift**和添加以下属性和常量到这个类：

```
let circlePathLayer = CAShapeLayer()
let circleRadius: CGFloat = 20.0
```

**circlePathLayer**表示这个圆形路径，而**circleRadius**表示这个圆形路径的半径。

添加以下初始化代码到**CircularLoaderView.swift**来配置这个shape layer:

```
override init(frame: CGRect) {
  super.init(frame: frame)
  configure()
}
 
required init(coder aDecoder: NSCoder) {
  super.init(coder: aDecoder)
  configure()
}
 
func configure() {
  circlePathLayer.frame = bounds
  circlePathLayer.lineWidth = 2
  circlePathLayer.fillColor = UIColor.clearColor().CGColor
  circlePathLayer.strokeColor = UIColor.redColor().CGColor
  layer.addSublayer(circlePathLayer)
  backgroundColor = UIColor.whiteColor()
}
```

两个初始化方法都调用**configure**方法，**configure**方法设置一个shape layer的line width为2，fill color为clear,stroke color为red。将添加circlePathLayer添加到view's main layer。然后设置view的 **backgroundColor** 为white，那么当image加载时，屏幕的其余部分就忽略掉。

##添加路径

你会注意到你还没赋值一个path给layer。为了做到这点，添加以下方法(还是在**CircularLoaderView.swift**文件)：

```
func circleFrame() -> CGRect {
  var circleFrame = CGRect(x: 0, y: 0, width: 2*circleRadius, height: 2*circleRadius)
  circleFrame.origin.x = CGRectGetMidX(circlePathLayer.bounds) - CGRectGetMidX(circleFrame)
  circleFrame.origin.y = CGRectGetMidY(circlePathLayer.bounds) - CGRectGetMidY(circleFrame)
  return circleFrame
}
```

上面那个方法返回一个**CGRect**的实例来界定指示器的路径。这个边框是**2*circleRadius**宽和**2*circleRadius**高，放在这个view的正中心。

每次这个view的size改变时，你会需要都重新计算**circleFrame**，所以你可能将它放在一个独立的方法。

现在添加以下方法来创建你的路径：

```
func circlePath() -> UIBezierPath {
  return UIBezierPath(ovalInRect: circleFrame())
}
```

这只是根据**circleFrame**限定来返回圆形的**UIBezierPath**。由于**circleFrame()**返回一个正方形，在这种情况下”椭圆“会最终成为一个圆形。

由于layers没有**autoresizingMask**这个属性，你需要在**layoutSubviews**方法更新**circlePathLayer**的frame来恰当地响应view的size变化。

下一步，覆盖**layoutSubviews()**方法：

```
override func layoutSubviews() {
  super.layoutSubviews()
  circlePathLayer.frame = bounds
  circlePathLayer.path = circlePath().CGPath
}
```

由于改变了frame，你要在这里调用**circlePath()**方法来触发重新计算路径。

现在打开**CustomImageView.swift**文件和添加以下**CircularLoaderView**实例作为一个属性：

```
let progressIndicatorView = CircularLoaderView(frame: CGRectZero)
```

下一步，在之前下载图片的代码添加这几行代码到**init(coder:)**方法：

```
addSubview(self.progressIndicatorView)
progressIndicatorView.frame = bounds
progressIndicatorView.autoresizingMask = .FlexibleWidth | .FlexibleHeight
```

上面代码添加进度指示器作为一个subview添加到自定义的image view。**autoresizingMask**确保进度指示器view保持与image view的size一样。

编译和运行你的项目；你会看到一个红的、空心的圆形出现，就像这样：

![](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Screenshot-2015-01-25-21.44.17.png)

好的 - 你已经有进度指示器画在屏幕上。你的下一个任务就是根据下载进度变化来stroke。

#修改Stroke长度

回到**CircularLoaderView.swift**文件和在这个文件的其他属性直接添加以下代码：

```
var progress: CGFloat {
  get {
    return circlePathLayer.strokeEnd
  }
  set {
    if (newValue > 1) {
      circlePathLayer.strokeEnd = 1
    } else if (newValue < 0) {
      circlePathLayer.strokeEnd = 0
    } else {
      circlePathLayer.strokeEnd = newValue
    }
  }
}
```

以上代码创建一个**computed property** - 也就是一个属性没有任何后背的变量 - 它有一个自定义的setter和getter。这个getter只是返回**circlePathLayer.strokeEnd**，setter验证输入值要在0到1之间，然后恰当地设置layer的**strokeEnd**属性。

在第一次运行的时候，添加下面这行代码到**configure()**来初始化进度：

```
progress = 0
```

编译和运行工程；除了一个空白的屏幕，你应该什么也没看到。相信我，这是一个好消息。设置**progress**为0，反过来会设置**strokeEnd**也为0，这就意味着shape layer什么也没画。

唯一剩下要做的就是你的指示器在image下载回调方法中更新**progress**。

回到**CustomImageView.swift**文件和用以下代码来代替注释**Update progress here**：

```
self!.progressIndicatorView.progress = CGFloat(receivedSize)/CGFloat(expectedSize)
```

这主要通过**receivedSize**除以**expectedSize**来计算进度。

> 注意：你会注意到block使用weak self引用 - 这样能够避免retain cycle。

编译和运行你的工程；你会看到进度指示器像这样开始移动：


<div align="center">
<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/indicator.gif"/>
</div>

即使你自己没有添加任何动画代码，**CALayer**在layer轻松地发现任何animatable属性和当属性改变时平滑地animate。

上面已经完成第一个阶段。现在进入第二和最后阶段。

#创建Reveal动画

reveal阶段在window显示image然后逐渐扩展圆形环的形状。如果你已经读过[前面教程](http://www.raywenderlich.com/86521/how-to-make-a-view-controller-transition-animation-like-in-the-ping-app)，那个教程主要讲创建一个Ping风格的view controller动画，你就会知道这是一个很好的关于**CALayer**的**mask**属性的使用案例。

Add the following method to **CircularLoaderView.swift**:

添加以下方法到**CircularLoaderView.swift**文件：

```
func reveal() {
 
  // 1
  backgroundColor = UIColor.clearColor()
  progress = 1
  // 2
  circlePathLayer.removeAnimationForKey("strokeEnd")
  // 3
  circlePathLayer.removeFromSuperlayer()
  superview?.layer.mask = circlePathLayer
}
```

This is an important method to understand, so let’s go over this section by section:

1. Clears the view’s background color so the image behind the view isn’t hidden anymore, and sets **progress** to 1, or 100%.
2. Removes any pending implicit animations for the **strokeEnd** property, which may have otherwise interfered with the reveal animation. For more about implicit animations, check out [iOS Animations by Tutorials](http://www.raywenderlich.com/store/ios-animations-by-tutorials).
3. Removes **circlePathLayer** from its **superLayer** and assigns it instead to the **superView**’s layer mask, so the image is visible through the circular mask “hole”. This lets you reuse the existing layer and avoid duplicating code.

Now you need to call **reveal()** from somewhere. Replace the **Reveal image here** comment in **CustomImageView.swift** with the following:

```
self!.progressIndicatorView.reveal()
```

Build and run your app; once the image downloads you’ll see it partially revealed through a small ring:

![](http://cdn3.raywenderlich.com/wp-content/uploads/2015/02/Screenshot-2015-01-26-02.49.54.png)

You can see your image in the background — but just barely! 

##Expanding Rings（星期五）
Your next step is to expand this ring both inwards and outwards. You could do this with two separate, concentric **UIBezierPath**, but you can do it in a more efficient manner with just a single Bezier path.

How? You simply increase the circle’s radius (the **path** property) to expand outward, while simultaneously increasing the line’s width (the **lineWidth** property) to make the ring thicker and expand inward. Eventually, both values grow enough to reveal the entire image underneath.

Go back to **CircularLoaderView.swift** and add the following code to the end of reveal():

```
// 1
let center = CGPoint(x: CGRectGetMidX(bounds), y: CGRectGetMidY(bounds))
let finalRadius = sqrt((center.x*center.x) + (center.y*center.y))
let radiusInset = finalRadius - circleRadius
let outerRect = CGRectInset(circleFrame(), -radiusInset, -radiusInset)
let toPath = UIBezierPath(ovalInRect: outerRect).CGPath
 
// 2
let fromPath = circlePathLayer.path
let fromLineWidth = circlePathLayer.lineWidth
 
// 3
CATransaction.begin()
CATransaction.setValue(kCFBooleanTrue, forKey: kCATransactionDisableActions)
circlePathLayer.lineWidth = 2*finalRadius
circlePathLayer.path = toPath
CATransaction.commit()
 
// 4
let lineWidthAnimation = CABasicAnimation(keyPath: "lineWidth")
lineWidthAnimation.fromValue = fromLineWidth
lineWidthAnimation.toValue = 2*finalRadius
let pathAnimation = CABasicAnimation(keyPath: "path")
pathAnimation.fromValue = fromPath
pathAnimation.toValue = toPath
 
// 5
let groupAnimation = CAAnimationGroup()
groupAnimation.duration = 1
groupAnimation.timingFunction = CAMediaTimingFunction(name: kCAMediaTimingFunctionEaseInEaseOut)
groupAnimation.animations = [pathAnimation, lineWidthAnimation]
groupAnimation.delegate = self
circlePathLayer.addAnimation(groupAnimation, forKey: "strokeWidth")
```

Here’s a comment-by-comment explanation of what’s going on above:

1. Determine the radius of the circle that can fully circumscribe the image view, then calculate the **CGRect** that would fully bound this circle. **toPath**represents the final shape of the **CAShapeLayer** mask like so:
2. Set the initial values of **lineWidth** and **path** to match the current values of the layer.
3. Set **lineWidth** and **path** to their final values; this prevents them from jumping back to their original values when the animation completes. Wrapping this in a **CATransaction** with **kCATransactionDisableActions** set to **true** disables the layer’s implicit animations.
4. Create two instances of **CABasicAnimation**, one for path and the other for **lineWidth**. **lineWidth** has to increase twice as fast as the radius increases in order for the circle to expand inward as well as outward.
5. Add both animations to a **CAAnimationGroup**, and add the animation group to the layer. You also assign **self** as the delegate, as you’ll use this in just a moment.

Build and run your project; you’ll see the reveal animation kick-off once the image finishes downloading. But a portion of the circle remains on the screen once the reveal animation is done.

![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/02/StillVisible.png)

To fix this, add the following implementation of **animationDidStop(_:finished:)** to **CircularLoaderView.swift**:

```
override func animationDidStop(anim: CAAnimation!, finished flag: Bool) {
  superview?.layer.mask = nil
}
```

This code removes the mask on the super layer, which removes the circle entirely.

Build and run your project again, and now you’ll see the full effect of your animation:

![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/02/indicator-final-final.gif)

Congratulations, you have finished creating the circular image loading animation!

#Where to Go From Here?（星期五）

You can [download the completed project here](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/ImageLoaderIndicator-Final.zip).

From here, you can further tweak the timing, curves and colors of the animation to suit your needs and personal design aesthetic. One possible improvement is to use **kCALineCapRound** for the shape layer’s **lineCap** property to round off the ends of the circular progress indicator. See what improvements you can come up with on your own!

If you enjoyed this tutorial and would like to learn how to create more animations like these, check out Marin Todorov’s book [iOS Animations by Tutorials](http://www.raywenderlich.com/store/ios-animations-by-tutorials), which starts with basic view animations and moves all the way to layer animations, animating constraints, view controller transitions, and more.

If you have any questions or comments about the tutorial, please join the discussion below. I’d also love to see ways in which you’ve incorporated this cool animation in your app!