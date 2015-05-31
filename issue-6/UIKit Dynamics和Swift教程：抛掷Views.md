UIKit Dynamics 和 Swift 教程：抛掷视图 (Tossing Views)
---

> * 原文链接 : [UIKit Dynamics and Swift Tutorial:Tossing Views](http://www.raywenderlich.com/94719/uikit-dynamics-swift-tutorial-tossing-views)
* 原文作者 : [Ray Wenderlich](http://www.raywenderlich.com)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [samw00](https://github.com/samw00) 
* 校对者: 
* 状态 : 校对中

In this UIKit Dynamics tutorial, you’ll learn how to toss views off-screen with gestures in a natural feeling way

在这篇UIKit Dynamics教程中，你将学会如何通过添加手势，用一种很自然的方式将视图抛掷出屏幕的显示区域。如下图所示：

![效果预览图](http://cdn1.raywenderlich.com/wp-content/uploads/2014/07/UIKitDynamics3.gif)

You may have seen this technique popularized in the popular app Tweetbot.

你可能已经在[Tweetbot](https://itunes.apple.com/us/app/tweetbot-3-for-twitter-iphone/id722294701)这个流行的app里看到这一个技巧被大量的运用。

This tutorial is a good fit for intermediate developers because it covers some neat effects, such as rotation and fly-away animations using the native UIKit framework.

这篇教程特别适合中级水平的开发人员，应为内容包括如何实现一些特别棒的特效，比如用原生的UIKit框架实现旋转rotation和飞离fly-away等动画效果。

But if you’re completely new to UIKit dynamics, don’t fear – this tutorial will guide you through step by step.

如果你刚刚接触UIKit dynamics, 也别紧张 － 因为这篇教程会一步一步引导你如何去做。

Without further ado, let’s get to tossing!

废话不多说，让我们直切主题吧！

#让我们开始吧

> Note: This section is optional for those who want to make the project from scratch. Experienced iOS developers might want to skip this section and proceed to the next section, “UIDynamicAnimator and UIAttachmentBehavior” where we have a starter project waiting for you.

> 注意: 这一节是为那些想从头开始写这个项目的开发员所准备的。有经验的开发者可以直接跳过这一节去下一节"UIDynamicAnimator and UIAttachmentBehavior", 在那已有一个准备停当的项目等着你

Start up Xcode, select File\New\Project…, choose iOS\Application\Single View Application template, and click Next. Name the project DynamicToss, make sure the selected language is Swift, and set the device family to iPhone.

运行你的Xcode，选择`File\New\Project...`, 选择`iOS\Application\Single View Application template`然后点击`Next`。将项目命名为DynamicToss，确保语言用的是Swfit然后将设备设为iPhone.

Now select the project name on the left and select the General at the top of the Xcode window. Under Deployment Info/Device Orientation uncheck the Landscape Left and Landscape Right checkboxes, because your app will only run in portrait mode.

接下来在左边点选项目名称并在Xcode顶部位置点击**General**。在**Deployment Info/Device Orientation**中不要勾选**Landscape Left**和**Landscape Right**，因为你的app只有竖屏这一种显示模式。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/02/dynamictoss-orientation.png)

然后，下载你这个项目需要的[图片](http://cdn2.raywenderlich.com/wp-content/uploads/2014/05/flying_goldfish.zip)，照片提供者：[gameartguppy.com](http://www.vickiwenderlich.com/)。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/05/goldfish_feature.jpg)

Unzip the file and add it to the Images asset catalog in your project.

将图片解压后添加到你项目中Images asset目录里。

Next, select Main.storyboard and go to the File Inspector in the utilities area on the right. To keep the scope of this tutorial small you will not use auto layout and size classes, so untick the Use Auto Layout checkbox. This will also disable size classes. In the dialog box that appears, choose to keep size class data for iPhone and select Disable Size Classes.

接着，点击**Main.storyboard**然后再右边的工具栏中选择**File Inspector**。在本片教程中你不需要用到auto layout和size classes，所以勾掉**Use Auto Layout**选项。这会自动勾掉size classes。 在弹出的对话框中，在Keep size class data for：这一选项选iPhone，然后点击**Disable Size Classes**。

In the storyboard, add an image view to the default view controller and set the image to goldfish_feature. In the size inspector for the image view, set the following values: X=33, Y=137, Width=254, Height=172. Also, change the autoresizing mask to disable flexible height, width, right, and bottom margins. That means only the top and left margins stay “selected”.

在storyboard中，在默认视图控制器中添加一个image view，然后将图片设为**goldfish_feature**。在image view的尺寸观察器中设置下列数值：X=33, y=137, Width=254, Height=172。紧接着在autoresizing mask中消掉弹性高度，宽度，右边距和底边距。这意味着只有顶边距和左边距是“被点选“的。

Your screen should now look like this:

你的屏幕看起来应该像这样：

![](http://cdn1.raywenderlich.com/wp-content/uploads/2015/02/dynamictoss-imageview.png)

Next, drag two plain Views into your view controller that you will use to help track your gestures. Set them to the following settings:

然后，在视图控制器中拖入两个view用以跟踪你的手势，并设置为下列数值

* View 1: (X=156, Y=219, Width=8, Height=8, Background=red)
* View 2: (X=80, Y=420, Width=8, Height=8. Background=blue)

When done your view should look like this:

当你完成之后，你的视图看起来应该是这样的：

![](http://cdn3.raywenderlich.com/wp-content/uploads/2015/02/dynamictoss-views.png)

That covers the views you’ll need to set up in the storyboard. Next: on to some code!

基本上那就是你在storyboard中要做的所有准备，接下来就是代码了！

##Touch Handling 触摸操作

Open ViewController.swift and add the following properties to the class:

打开**ViewController.swift**并在类中加入下列属性：

```swift
@IBOutlet weak var imageView: UIImageView!
@IBOutlet weak var redSquare: UIView!
@IBOutlet weak var blueSquare: UIView!
 
private var originalBounds = CGRect.zeroRect
private var originalCenter = CGPoint.zeroPoint
 
private var animator: UIDynamicAnimator!
private var attachmentBehavior: UIAttachmentBehavior!
private var pushBehavior: UIPushBehavior!
private var itemBehavior: UIDynamicItemBehavior!
```

You’ll connect the outlets in a moment. The other properties will control the image view’s movement and keep track of its state as the touch events go on.

你将在等一会连接这些外部引用。其他的属性用来控制image view的运行轨迹和在触摸事件中跟踪image view的状态。

Open Main.storyboard and right click (or control-click) on the View Controller. Drag from the circle to the right of the outlet named blueSquare onto the small blue square view and release the mouse. This links the property to the view object.

打开**Main.storyboard**然后右键（或者按住control）点击**View Controller**。在外部引用列表中的**blueSquare**右边有一个空心的小圈圈，点选后不要松开鼠标左键，然后拖拽到blue square的视图上来再松手。这会讲属性和视图对象绑定起来。

Do the same for the red square, and finally for the property named imageView, drag onto your jetpack-equipped goldfish. Your three view properties should now be linked, like this:

用同样的方式绑定red square和属性**imageView**。现在三个view属性应该绑定好了，如下图所示：

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/05/finallinking.png)

The red and blue squares will represent points that the UIKit dynamics physics engine uses to animate the image.

红色和蓝色的正方形代表着UIKit dynamics物理引擎用来时图像运动起来的点。

The blue square will simply represent where your touch began, i.e., where your finger first made contact with the screen. The red square will track your finger as it moves.

蓝色的正方形就代表着你触摸开始的那个点，也就是当你手指第一次触碰到屏幕的那个点。红色的正方形会跟踪你手指滑动的轨迹。

You’ll configure the dynamics so that when you move that point around, the image view physically animates to move in tandem.

待会你将对dynamics做一些配置，使得当你在移动那个点的时候，image view也会相应的进行物理运动。

There’s one final thing you need to do – set up a gesture recognizer for the view. Open ViewController.swift and add this new method to the file:

最后你还有一件事要做 － 给view添加一个手势识别。打开**ViewController.swift**然后在文件中加入下列方法：

```swift
@IBAction func handleAttachmentGesture(sender:UIPanGestureRecognizer) {

	let location = sender.locationInView(self.view)
	let boxLocation = sender.locationInView(self.imageView)
	
	switch sender.state {
	case .Began:
		println("Your touch start position is \(location)")
		println("Start location in image is \(boxLocation)")
		
	case .Ended:
		println("Your touch end position is \(location)")
		println("End location in image is \(boxLocation)")
	
	default:
		break
	}
}
```

You’re going add a gesture recognizer to detect dragging, aka panning, and call this method when panning occurs. For now, this method simply displays the position of your finger in two coordinate systems (the view, and the image view).

你还要添加一个拖拽手势识别，也就是panning，然后当panning触发时，调用这个方法。现阶段，这个方法就是简单的现实你的手指在两个坐标系(view, image view)中的位置而已。

To add the gesture recognizer, open Main.storyboard and drag a Pan Gesture Recognizer onto the View. Then control-drag from the Pan Gesture Recognizer up to your View Controller, and connect it to the handleAttachmentGesture: action.

打开**Main.storyboard**，往视图上拖拽一个**Pan Gesture Recognizer**。然后按住control点住**Pan Gesture Recognizer**拖到你的**View Controller**，然后连接**handleAttachmentGesture:**。

Now build and run. Swipe or drag across the screen and you should see messages coming out on the debug window:

现在编译运行。在屏幕上滑动或者拖拽，你应该能在控制台上看到：

```
Your touch start position is (125.0,227.0)
Start location in image is (92.0,90.0)
Your touch end position is (195.5,374.0)
End location in image is (162.5,237.0)
```

Great! You’ve got the basic UI set up – now it’s time to make it dynamic.

棒极了！你已经将一切都准备好了－接下来就是添加动态特效了。

#UIDynamicAnimator 和 UIAttachmentBehavior

> Note: If you skipped ahead from the previous section, download the [starter project]() to continue from this point.


> 注意：如果你略过了之前的内容，先下载[项目](http://cdn4.raywenderlich.com/wp-content/uploads/2015/02/DynamicToss-Starter.zip)然后继续下面的内容。

The first thing you want to do is make the image view move as you drag it. You will do this with a type of UIKit Dynamics class called a UIAttachmentBehavior.

你要做的第一件事就是当你拖拽时，让你的image view也跟着动起来。你会用到UIKit Dynamics类中一个叫UIAttachmentBehavior的类型。

Open ViewController.swift and place the following code in viewDidLoad(), below super.viewDidLoad().

打开**ViewController.swift**，然后在**viewDidLoad()**里面，将下边的代码添加到**super.viewDidLoad()**下面。

```swift
animator = UIDynamicAnimator(referenceView: view)
originalBounds = imageView.bounds
originalCenter = imageView.center
```

The above code sets up a UIDynamicAnimator, which is UIKit’s engine for physics-based animation. You provide the view controller’s view as a reference view which defines the coordinate system for the animator.

上面的代码设立了一个UIDynamicAnimator，这是UIKit的一个引擎，就是为实现物理动画而准本的。这个引擎所参照的坐标系就是你提供的这个视图控制器的view。

You add behaviors to an animator, which allow you to do things like attaching views, pushing views, making them be affected by gravity, and more.

通过给animator添加各种行为(behaviors)，你可以实现attaching views, pushing views, 让视图受重力影响等等。

You’ll start with a UIAttachmentBehavior, to make the image view track your finger when you make a pan gesture.

让我们先从UIAttachmentBehavior开始，当你做了一个拖拽手势时让image view跟随你的指尖移动。

为了实现这效果，在**handleAttachmentGesture(sender:)**中添加如下代码，在**case .Began**中的两个**println**语句之后：

```swift
// 1
animator.removeAllBehaviors()
 
// 2
let centerOffset = UIOffset(horizontal: boxLocation.x - imageView.bounds.midX,
                            vertical: boxLocation.y - imageView.bounds.midY)
attachmentBehavior = UIAttachmentBehavior(item: imageView,
    offsetFromCenter: centerOffset, attachedToAnchor: location)
 
// 3
redSquare.center = attachmentBehavior.anchorPoint
blueSquare.center = location
 
// 4
animator.addBehavior(attachmentBehavior)
```

Let’s go over this section by section:

1. First you remove any existing animation behaviors that might be hanging around.
2. Next, you create a UIAttachmentBehavior that attaches the the point inside the image view where the user taps to an anchor point (which happens to be the exact same point). Later on, you will change the anchor point, which will cause the image view to move.
Attaching an anchor point to a view is like installing an invisible rod that connects the anchor point to a fixed attachment position on the view.
3. Update the red square to indicate the anchor point, and the blue square to indicate the point inside the image view that it is attached to. When the gesture starts, these will be the same point.
4. Add this behavior to the animator to make it take effect.

让我们来看看每一步都做了些什么：

1. 第一步，先移除animtor中任何可能残留的动画行为。
2. 然后，你创建了一个**UIAttachmentBehavior**，用来连接用户在image view内点击的点和一个锚点（刚好这两个点事重合的）。晚点你将改变这个锚点，从而使image view跟着移动。给视图添加一个锚点，就好比在view上一个固定的点和锚点之间添加了一个隐形的拉杆。
3. 更新red square来表示锚点，而blue square就是表示在image view中和锚点相连的点。当手势刚开始时，这两个点是重合的。
4. 在引擎animator添加这个行为使其生效。

Next you need to tell the anchor point itself to follow your finger. In handleAttachmentGesture(_:), replace the break statement in the default: case with the following code:

下一步你要告诉锚点跟随你的指尖。在**handleAttachmentGesture(_:)**，替换掉**default:**中的**break**语句为下列代码：

```swift
attachmentBehavior.anchorPoint = sender.locationInView(view)
redSquare.center = attachmentBehavior.anchorPoint
```

The default case handles the gesture update calls as the user pans around. The code here simply aligns the anchor point and red square to the finger’s current position. When the user’s finger moves, the gesture recognizer calls this method to update the anchor point to follow the touch. In addition, the animator automatically updates the view to follow the anchor point.

当用户拖拽时候，default里面的代码内容来响应手势一直更新这个事件。上面的代码就是简单的将锚点，red square和手指当前位置合为一体。当用户手指移动时，手势识别则调用这个方法更新锚点至指尖移动到的位置。同时，animator也会随着锚点的移动自动更新view。

Build and run, and you are now able to drag the view around:

编译并运行，现在你可以把view拖来拖去了：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2014/07/004_DraggedView-281x500.png)

Notice how the view isn’t just translated around the screen; if you start the gesture in a corner of the image, the view will rotate as you move it because of the anchor point.

注意观察view并不是在屏幕中简单的变动；如果手势起于图像的某一个角，你会发现当你移动时view会旋转，这就要归功于锚点了。

However it would be nice to return the view back to its original position when you’re done dragging. To fix this, add this new method to the class:

不过，当你拖拽完后，视图最好能回到自己的初始位置。我们类中添加一个新的方法来完善这个功能：

```swift
func resetDemo() {
	animator.removeAllBehaviors()
	
	UIView.animateWithDuration(0.45) {
		self.imageView.bounds = self.originalBounds
		self.imageView.center = self.originalCenter
		self.imageView.transform = CGAffineTransformIdentiy
	}
}
```

Then call this in handleAttachmentGesture(_:), below the println calls in the .Ended: section:

然后在**handelAttachmentGesture(_:)**中，在**.Ended**中的**println**语句下添加:

```swift
resetDemo()
```

Build and run, and now after you drag an image it should revert to its original position.

编译运行，现在当你拖拽完之后，视图就应该回到它自己初始的位置了。

#UIPushBehavior

Next, you need to detach the view when you stop dragging, and endow it with momentum so that it can continue its trajectory when you release it while in motion. You will do this with a UIPushBehavior.

接下来，我们要实现的是：当你停止拖拽时，使你的view飞离(detach)，我们会给这个view赋予动能，所以当你的手松开时，它能会延续它的既定轨道去运动。我们将通过**UIPushBehavior**来实现这一点。

First, you’ll need two constants. Add these to the top of the file:

首先，你需要两个常量。在文件头部添加它们：

```
let ThrowingThreshold: CGFloat = 1000
let ThrowingVelocityPadding: CGFloat = 35
```

ThrowingThreshhold indicates how fast the view must be moving in order to have the view continue moving (versus immediately returning to its original spot). ThrowingVelocityPadding is a magic constant that affects how fast or slow the toss should be (this was chosen by trial and error).

**ThrowingThreshold**表明view必须移动多块才能在手指松开后继续移动(而不是马上回到它的初始位置)。**ThrowingVelocityPadding**是一个神奇的常量，它对抛掷view速度快慢产生影响(这个数值是经过反复试错而得出的）。

Finally, inside handleAttachmentGesture(_:), replace the call to resetDemo() inside the .Ended case with the following:

最后，在**handleAttachmentGesture(_:)**中，在**.Ended**分支下，用下面的代码替换原来的**resetDemo()**:

```swift
animator.removeAllBehaviors()
 
// 1
let velocity = sender.velocityInView(view)
let magnitude = sqrt((velocity.x * velocity.x) + (velocity.y * velocity.y))
 
if magnitude > ThrowingThreshold {
  	// 2
  	let pushBehavior = UIPushBehavior(items: [imageView], mode: .Instantaneous)
  	pushBehavior.pushDirection = CGVector(dx: velocity.x / 10, dy: velocity.y / 10)
  	pushBehavior.magnitude = magnitude / ThrowingVelocityPadding
 
  	self.pushBehavior = pushBehavior
  	animator.addBehavior(pushBehavior)
 
  	// 3
  	let angle = Int(arc4random_uniform(20)) - 10
 
  	itemBehavior = UIDynamicItemBehavior(items: [imageView])
  	itemBehavior.friction = 0.2
  	itemBehavior.allowsRotation = true
  	itemBehavior.addAngularVelocity(CGFloat(angle), forItem: imageView)
  	animator.addBehavior(itemBehavior)
 
  	// 4
  	let timeOffset = Int64(0.4 * Double(NSEC_PER_SEC))
  	dispatch_after(dispatch_time(DISPATCH_TIME_NOW, timeOffset), dispatch_get_main_queue()) {
    	self.resetDemo()
  	}
} else {
  	resetDemo()
}
```

Let’s go over this section by section:

让我们再来看看每一步干了些什么：

1. Ask the gesture for the velocity of the drag.
Using velocity and your old friend the Pythagorean theorem, you compute the magnitude of the velocity — which is the hypotenuse of the triangle formed from the x direction velocity and the y direction velocity.
To understand the theory behind this check out this Trigonometry for Game Programming tutorial.

2. Assuming the gesture magnitude exceeds your minimum threshold set up for the action, you set up a push behavior.
A push behavior applies a force to the specified items. In this case, it’s an instantaneous force against the image.
The desired direction is composed of the x and y velocities converted to a vector that gives the directional portion. Once you have the push behavior set up, you add it to the animation sequence.

3. This section sets up some rotations to make the image “fly away”. You can read up on the complicated math here.
Some of this depends on how close to the edge your finger is when it initiates the gesture.
Play around with the values here and watch how the movements change the effects. The values used give a nice, flowing rotation with a cool spinning effect!

4. After a specified interval of time, the animation resets by sending the image back to its destination, so it zips off and returns to the screen — just like a ball bouncing off a wall!

让我们再来看看每一步干了些什么：

1. 向手势获取拖拽的速度。通过速度和你的老朋友勾股定理，你可以计算出速度的量级 － 也就是x轴方向的速度和y轴方向的速度所构成的三角形的斜边(弦）。如果想了解这个定理背后的知识，可以瞅瞅[Trigonometry for Game Programming Tutorial](http://www.raywenderlich.com/?p=90520).

2. 当速度的量级超过了你设定的临界值，则创建一个push behavior。Push behavior会给特定的对象施加一种力，在这里，我们通过push behavior给图像施加一种瞬间，猝发的力。而运行的方向则是由x轴速度和y轴速度合起来转换出的一个方向向量来决定的。当你创建好push behavior之后，将其添加到animator的行为序列中。

3. 这一步则将添加一下旋转特效，让图像看起来像是“飞离”一样。你可以在这里了解背后复杂的[数学知识](https://github.com/u10int/URBMediaFocusViewController/blob/master/URBMediaFocusViewController.m#L636-L686)。其中有部分效果取决于当你开始手势时，你的手指离边界的距离。你可以尝试设置其他的值然后观察它们的移动效果。这里用的值，会给出一个非常流畅平滑和酷毙了的旋转效果。

4. 在指定的一段时间过后，图像将会被重置回原来初始的位置，所以“颼”的一下，图像又回到屏幕中 － 就想一个撞到墙弹回来的球一样。

Build and run, and you should now be able to toss your view offscreen in a pleasing manner!

编译并运心，这回你应该可以愉快地把你的view随意的拖拽了。

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/07/UIKitDynamics3.gif)

#接下来可以做什么？

Here is the final example project from this UIKit Dynamics tutorial.

这篇UIKit Dynamics教程中的项目可以在这里[下载](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/DynamicToss-Final-1.2.zip)。

Congratulations, you’ve now learned how to do some fancy UIKit dynamic animations that can give an app’s UI some pretty sweet effects.

恭喜你，你现在已经学了如何添加一些UIKit Dynamics动画特效让你的app的UI看起来更加的酷炫。

If you want learn more about UIKit Dynamics, check out the two UIKit Dynamics chapters in iOS 7 by Tutorials.

如果你想学更多UIKit Dynamics的技巧, 记得去看看[iOS 7 by Tutorials](http://www.raywenderlich.com/?page_id=48020)中有两章节是专门讲UIKit Dynamics的。

Drop by the forums or leave a comment below to share your successes or ask questions about making cool animations in iOS. And use your new powers wisely!

没事就到论坛去留个言分享一下你的成功经验或者问些关于如何在iOS中实现一些酷的动画特效的问题等等。然后善用你新get的技能！