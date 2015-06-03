使用CAShapeLayer来实现圆形图片加载动画
---

> * 原文链接 : [How To Implement A Circular Image Loader Animation with CAShapeLayer](http://www.raywenderlich.com/94302/implement-circular-image-loader-animation-cashapelayer)
* 原文作者 : [Rounak Jain](http://www.raywenderlich.com/u/rounak)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [Sam Lau](https://github.com/samlaudev) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  未完成

A few weeks ago, Michael Villar created a really interesting loading animation for his post on Motion Experiments.

几个星期之前，Michael Villar在Motion试验中创建一个非常有趣的加载动画。

The GIF to the right shows the loading animation, which marries a circular progress indicator with a circular reveal animation. The combined effect is fascinating, unique, and more than a little mesmerizing! 

下面的GIF图片展示这个加载动画，它将一个圆形进度指示器和圆形渐现动画结合。这个组合的效果有趣，独一无二和有点迷人。

<div align="center">
<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/02/Circle.gif"/>
</div>

This tutorial will show you how to recreate this exact effect in Swift and Core Animation. Let’s get animating!

这个教程将会教你如何使用Swift和Core Animatoin来重新创建这个效果。让我们开始吧！


#Getting Started

#基础

First [download the starter project](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/ImageLoaderIndicator-Starter.zip) for this tutorial, and build and run. After a moment, you should see a simple image displayed as follows:

首先下载这个教程的[启动项目](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/ImageLoaderIndicator-Starter.zip)，然后编译和运行。过一会之后，你应该看到一个简单的image显示：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/StarterProject.png)

The starter project already has the views and image loading logic in place. Take a minute and browse through the project once you’ve extracted it; there’s a **ViewController** that has a **UIImageView** subclass named **CustomImageView**, along with a **SDWebImage** method call to load the image.

这个启动项目已经有些views和image加载逻辑在适当的地方。花一分钟来浏览来快速了解这个项目；那里有一个**ViewController**，**ViewController**里有一个命名为**CustomImageView**的**UIImageView**子类, 还有一个**SDWebImage**的方法被调用来加载image。

You might notice when you first run the app, the app will seem to pause for a few seconds while the image downloads, and then the image will appear on the screen without fanfare. Of course, there’s no circular progress indicator at the moment – that’s what you’ll create in this tutorial!

你可能注意到当你第一次运行这个app的时候，当image下载时这个app似乎会暂停几秒，然后image会显示在屏幕。

You will create this animation in two distinct phases:

你会在两个步骤中创建这个动画：

1. **Circular progress**. First, you will draw a circular progress indicator and update it based on the progress of the download.
2. **Expanding circular image**. Second, you will reveal the downloaded image through an expanding circular window.

1. **圆形进度**。首先，你会画一个圆形进度指示器，然后根据下载进度来更新它。
2. **扩展圆形图片**。第二，你会通过扩展的圆形窗口来揭示下载图片。

Follow along closely to prevent yourself from going in “circles”!

紧跟着下面步骤来逐步实现！

#Creating the Circular Indicator

#创建圆形指示器

Think for a moment about the basic design of the progress indicator. The indicator is initially empty to show a progress of 0%, then gradually fills in as the image is downloaded. This is fairly simple to achieve with a **CAShapeLayer** whose **path** is a circle.

想一下关于进度指示器的基本设计。这个指示器一开始是空来展示0%进度，然后逐渐填满直到image完成下载。通过设置**CAShapeLayer**的**path**为circle来实现是相当简单。

> Note: If you’re new to the concept of **CAShapeLayer** (or **CALayers** in general, check out Scott Gardner’s [CALayer in iOS with Swift](http://www.raywenderlich.com/90488/calayer-in-ios-with-swift-10-examples) article.

> 注意：如果你不熟悉**CAShapeLayer**(或**CALayers**)的基本概念，可以查看Scott Gardner的[CALayer in iOS with Swift](http://www.raywenderlich.com/90488/calayer-in-ios-with-swift-10-examples)文章。

You can control the start and end position of the outline, or **stroke**, of your shape with the **CAShapeLayer** properties **strokeStart** and **strokeEnd**. By varying **strokeEnd** between 0 and 1, you can fill in the stroke appropriately to show the progress of the download.

你可以通过**CAShapeLayer**的**strokeStart**和**strokeEnd**属性来控制开始和结束位置的外观。通过改变**strokeEnd**的值在0到1之间，你可以恰当地填充下载进度。

Let’s try this out. Create a new file with the **iOS\Source\Cocoa Touch Class template**. Name it **CircularLoaderView** and set it to be a subclass of **UIView** as shown below:

让我们试一下。通过**iOS\Source\Cocoa Touch Class template**来创建一个新的文件，文件名为**CircularLoaderView**。设置它为**UIView**的子类。

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/Screenshot-2015-01-25-19.25.43.png)

Click **Next**, and then **Create**. This new subclass of **UIView** will house all of your new animation code.

点击**Next**和**Create**。新的子类**UIView**将用来保存动画的代码。

Open **CircularLoaderView.swift** and add the following property and constant to the class:

打开**CircularLoaderView.swift**和添加以下属性和常量到这个类：

```
let circlePathLayer = CAShapeLayer()
let circleRadius: CGFloat = 20.0
```

**circlePathLayer** represents the circular path, while **circleRadius**, ostensibly, will be the radius of the circular path.

**circlePathLayer**表示这个圆形路径，而**circleRadius**表示这个圆形路径的半径。

Add the following initialization code to **CircularLoaderView.swift** to configure the shape layer:

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

Both of the initializers call **configure**. **configure** sets up a shape layer to have a line width of 2 points, a clear fill color, and a red stroke color. It then adds the shape layer you configured as a sublayer of the view’s main layer, and then set the view’s **backgroundColor** to white so the rest of the screen is blanked out while the image loads.

两个初始化方法都调用**configure**方法，**configure**方法设置一个shape layer的line width为2，fill color为clear,stroke color为red。将添加circlePathLayer添加到view's main layer。然后设置view的 **backgroundColor** 为white，那么当image加载时，屏幕的其余部分就忽略掉。

##Adding the Path（星期四）

You’ll notice that you haven’t yet assigned a path to the layer. To do that, add the following method (still in **CircularLoaderView.swift**):

```
func circleFrame() -> CGRect {
  var circleFrame = CGRect(x: 0, y: 0, width: 2*circleRadius, height: 2*circleRadius)
  circleFrame.origin.x = CGRectGetMidX(circlePathLayer.bounds) - CGRectGetMidX(circleFrame)
  circleFrame.origin.y = CGRectGetMidY(circlePathLayer.bounds) - CGRectGetMidY(circleFrame)
  return circleFrame
}
```

The above method returns an instance of **CGRect** that bounds your indicator’s path. The bounding rectangle is **2*circleRadius** wide and **2*circleRadius** tall, and lies at the center of the view.

You’ll need to recalculate **circleFrame** each time the view’s size changes, so you may as well put it in a method of its own.

Now add the following method to create your path:

```
func circlePath() -> UIBezierPath {
  return UIBezierPath(ovalInRect: circleFrame())
}
```

This simply returns the circular **UIBezierPath** as bounded by **circleFrame**. Since **circleFrame()** returns a square, the “oval” in this case will end up as a circle.

Since layers don’t have an **autoresizingMask** property, you’ll need to update the **circlePathLayer**’s frame in **layoutSubviews** to respond appropriately to changes in the view’s size.

Next override **layoutSubviews()** as follows:

```
override func layoutSubviews() {
  super.layoutSubviews()
  circlePathLayer.frame = bounds
  circlePathLayer.path = circlePath().CGPath
}
```

You’re calling **circlePath()** here since a change in the frame should also trigger a recalculation of the path.

Now open **CustomImageView.swift** and add the following instance of **CircularLoaderView** as a property:

```
let progressIndicatorView = CircularLoaderView(frame: CGRectZero)
```

Next add these lines in **init(coder:)**, right before the code that downloads the image:

```
addSubview(self.progressIndicatorView)
progressIndicatorView.frame = bounds
progressIndicatorView.autoresizingMask = .FlexibleWidth | .FlexibleHeight
```

This adds the progress indicator view as a subview to the custom image view. **autoresizingMask** ensures that progress indicator view remains the same size as the image view.

Build and run your project; you’ll see a red, hollow circle appear like so:
![](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/Screenshot-2015-01-25-21.44.17.png)

Okay — you have your progress indicator drawing on the screen. Your next task is to vary the stroke as the download progresses.

##Modifying the Stroke Length（星期四）
Head back to **CircularLoaderView.swift** and add the following lines directly below the other properties in the file:

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

This creates a **computed property** — that is, a property without any backing variable — that has a custom setter and getter. The getter simply returns **circlePathLayer.strokeEnd**, and the setter validates that the input is between 0 and 1 and sets the layer’s **strokeEnd** property accordingly.

Add the following line to **configure()** to initialize progress on first run:

```
progress = 0
```

Build and run your project; you should see nothing but a blank white screen. Trust me! This is good news!  Setting **progress** to 0 in turn sets the **strokeEnd** to 0, which means no part of the shape layer was drawn.

The only thing left to do with your indicator is to update **progress** in the image download callback.

Go back to **CustomImageView.swift** and replace the comment **Update progress here** with the following:

```
self!.progressIndicatorView.progress = CGFloat(receivedSize)/CGFloat(expectedSize)
```

This calculates the progress by dividing **receivedSize** by **expectedSize**.

> Note: You’ll notice the block uses a weak reference to self – this is to avoid a retain cycle.


Build and run your project; you’ll see the progress indicator begin to move like so:

<div align="center">
<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/indicator.gif"/>
</div>

Even though you didn’t add any animation code yourself, **CALayer** handily detects any animatable property on the layer and smoothly animates is as it changes. Neat!

That takes care of the first phase. Now on to the second and final phase — the big reveal! 

#Creating the Reveal Animation（星期五）
The reveal phase gradually displays the image in a window in the shape of an expanding circular ring. If you’ve read my [previous tutorial](http://www.raywenderlich.com/86521/how-to-make-a-view-controller-transition-animation-like-in-the-ping-app) on creating a Ping-style view controller animation, you’ll know that this is a perfect use-case of the **mask** property of a **CALayer**.

Add the following method to **CircularLoaderView.swift**:

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