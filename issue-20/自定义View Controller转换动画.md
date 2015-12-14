> * 原文链接 : [Introduction to Custom View Controller Transitions and Animations](http://www.appcoda.com/custom-view-controller-transitions-tutorial/)
* 原文作者 : [joyce echessa](http://www.appcoda.com/author/joyceechessa/)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [kmyhy](https://github.com/kmyhy) 

Looking at the built in apps from Apple on your iOS device, you will notice the various animated transitions as you move from one view to another for example the way view controllers are presented in master-detail views with a swipe that slides the detail view controller over the master view controller as seen in the Messages app or Settings app and the various transitions that represent a segue to another view controller.

观察 iOS 自带的 App，你会看到当你从一个视图导航到另一个视图时总是会显示各种各样的转换动画，以“主-从”视图为例(类似的程序有Messages App或者系统设置程序)，一个轻扫动作能够让详情视图呈现在主视图之上，在呈现一个新的 View Controller 时还会带有丰富的转换动画。

iOS 7 introduced custom view controller transitions which make it possible for developers to create their own animated transitions from one view controller to the next in their apps. In this tutorial, we’ll take a look at how to do this. We’ll also look at how to create gesture driven transitions called interactive transitions. To follow along, download the starter project which we’ll be using throughout the tutorial.

iOS 7 新增的自定义 View Controller 转换动画，使让开发者在 App 中发生从一个 View Controller 到另一个 View Controller 的导航时，创建自定义的动画效果。在本教程中，我们将学习这个内容。此外，我们还会学习如何通过手势来发起一个导航，也就是所谓的“交互式导航”。在开始之前，请下载本教程中用到的[开始项目](https://www.dropbox.com/s/4p6kk0g3qua1kvt/CustomTransitionsStarter.zip?dl=0)。

##Getting Started
##开始

To create custom transitions you have to follow three steps:

创建自定义转换动画的，可以分成 3 个步骤进行：

* Create a class that implements the UIViewControllerAnimatedTransitioning protocol. Here you will write code that performs the animation. This class is referred to as the animation controller.

	指定一个类，实现 UIViewControllerAnimatedTransitioning 协议。在这个类中，我们编写执行动画的代码。这个类充当动画控制器。

* Before presenting a view controller, set a class as its transitioning delegate. The delegate will get a callback for the animation controller to be used when presenting the view controller.

	在呈现一个 View Controller 之前，设置这个 View Controller 的 transitioningDelegate 属性为某个对象。这个对象在呈现这个 View Controller 的过程中将被调用，用于获取转场时应该使用什么对象作为动画控制器。
	
* Implement the callback method to return an instance of the animation controller from the first step.

	实现回调方法，用于返回一个你在第一歩中创建的动画控制器对象。

Run the starter project and you will be presented with a table view of a list of items. There is an Action button on the navigation bar and when you tap it you’ll be presented with another view that appears in the usual modal style of sliding up from the bottom. We will write a custom transition for this view.

运行开始项目，你将看到一个列表。在导航栏上有一个 Action 按钮，点击它，它将呈现一个新的 View Controller（以 modal 方式从底部向上弹起）。我们将为这个转换动作自定义转换动画。	

![](http://www.appcoda.com/wp-content/uploads/2015/03/vid01.gif)

##Custom Present Transition
##实现 View Controller 呈现动画

The first thing to do as stated previously, is to create the animation controller. Create a new class called CustomPresentAnimationController and make it a subclass of NSObject. Change its declaration as shown.

如前面所述，首先需要创建动画控制器。新建一个 NSObject 子类 CustomPresentAnimationController，声明将遵守 UIViewControllerAnimatedTransitioning 协议：

```swift
class CustomPresentAnimationController: NSObject, UIViewControllerAnimatedTransitioning {
```

UIViewControllerAnimatedTransitioning protocol has two required methods which we’ll add next. Add the following methods to the class.

UIViewControllerAnimatedTransitioning 协议有两个必须实现的方法，我们来实现它们：

```swift
func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -> NSTimeInterval {
    return 2.5
}
    
func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
        
    let fromViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
    let toViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
    let finalFrameForVC = transitionContext.finalFrameForViewController(toViewController)
    let containerView = transitionContext.containerView()
    let bounds = UIScreen.mainScreen().bounds
    toViewController.view.frame = CGRectOffset(finalFrameForVC, 0, bounds.size.height)
    containerView.addSubview(toViewController.view)
        
    UIView.animateWithDuration(transitionDuration(transitionContext), delay: 0.0, usingSpringWithDamping: 0.5, initialSpringVelocity: 0.0, options: .CurveLinear, animations: {
        fromViewController.view.alpha = 0.5
        toViewController.view.frame = finalFrameForVC
        }, completion: {
            finished in
            transitionContext.completeTransition(true)
            fromViewController.view.alpha = 1.0
    })
}
```
The first method specifies the length of the transition animation. For the demo app we’ve set it at 2.5 seconds, but you probably should set it to a smaller number in a real app.

第一个方法用于指定动画播放的时长。对于本例，我们指定为 2.5 秒。当然在真正的 App 你可以把这个数字调小一些。

In the second method we use the transitionContext to get the view controller we are navigating from, to, the final frame the transition context should have after the animation completes and the container view which houses the views corresponding to the to and from view controllers.

第二个方法带有一个 transitionContext 参数，通过这个对象，我们可以获取转换过程中涉及的 from 控制器（开始控制器）、to 控制器（到达控制器）、当动画完成后的 final frame (to 视图的最终位置及大小），以及 containerView——这个容器用于包含 from 控制器和 to 控制器的 UIView。

We then position the to view just below the bottom of the screen. Then we add the to view to the container view and in the animate closure, we animate the to view by setting its final frame to the location given by the transition context. We also animate the from view‘s alpha value so that as the to view is sliding up the screen over the from view, the from view will be faded out. The duration of the animation used is the one set in transitionDuration(transitionContext:). In the completion closure, we notify the transition context when the animation completes and then change the from view‘s alpha back to normal. The framework will then remove the from view from the container.

然后，我们将 to 视图放在屏幕的下方，并将 to 视图添加到 containerView。在动画块中，我们将 to 视图移动到 final frame 的位置。同时将 from 视图的 alpha 值设置为 0.5，这样，当 to 视图向上滑入的同时 from 视图淡出。执行动画块时，第一个参数调用了transitionDuration(transitionContext:)方法，用这个方法的返回值作为动画块的执行时间。当动画完成，调用完成块，我们在完成块中通知 transitionContext 动画已经完成，同时将 from 视图的 alpha 值设回 1.0。

With the animation controller completed, we need to link it to a storyboard segue.

写好动画控制器类之后，我们需要在故事板中将动画控制器分配给某个 segue。

Open the ItemsTableViewController.swift file and change the class declaration as shown.

打开 ItemsTableViewController.swift ，修改类声明如下：

```swift
class ItemsTableViewController: UITableViewController, UIViewControllerTransitioningDelegate {
```
UIViewController has a property named transitionDelegate that supports custom transitions. When transitioning to a view controller, the framework checks this property to see if a custom transition should be used. UIViewControllerTransitioningDelegate supplies custom transitions.

UIViewController 有一个 transitionDelegate 属性，用于指定自定义转换动画。当转场到一个 View Controller 时，框架会使用这个属性进行转换。而 UIViewControllerTransitioningDelegate 属性则负责提供自定义转换对象。

Open Main.storyboard and select the Present modally segue to Action View Controller and in the Attributes Inspector, set its Identifier to showAction.

打开 Main.storyboard 选择导航到 Action View Controller 的那条 segue，在属性面板将它的 Identifier 设置为 showAction。
![](http://www.appcoda.com/wp-content/uploads/2015/03/pic02.png)

Back in ItemsTableViewController add the following to the class.

回到 ItemsTableViewController ，添加如下代码：

```swift
let customPresentAnimationController = CustomPresentAnimationController()
 
override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
        
    if segue.identifier == "showAction" {
        let toViewController = segue.destinationViewController as UIViewController
        toViewController.transitioningDelegate = self
    }
}
```
Here we create an instance of our animation controller and then in the prepareForSegue() function, we detect the segue for the Action screen and set the transitionDelegate property of the destination view controller.

这里我们创建了一个我们的动画控制器对象，然后在 prepareForSegue() 方法中，找到 ID 为 showAction 的 segue，设置它的 to 视图控制器的 transitioningDelegate 属性。

Add the following UIViewControllerTransitioningDelegate method to the class. This returns the instance of our custom animation controller.

然后在这个类中实现 UIViewControllerTransitioningDelegate 方法。并在这些方法中返回我们的自定义动画控制器。

```swift
func animationControllerForPresentedController(presented: UIViewController, presentingController presenting: UIViewController, sourceController source: UIViewController) -> UIViewControllerAnimatedTransitioning? {
    return customPresentAnimationController
}
```
Run the application and you should see the Action view slide up slowly from the screen and bounce a little before settling.

运行程序，你可以看到 Action 视图从屏幕底部缓缓弹起，并在停止前呈阻尼运动。
![](http://www.appcoda.com/wp-content/uploads/2015/03/vid02.gif)

If you want a slightly different effect, then change this statement in CustomPresentAnimationController.swift

如果想看看其他效果，在 CustomPresentAnimationController.swift 中找到如下语句：

```swift
toViewController.view.frame = CGRectOffset(finalFrameForVC, 0, bounds.size.height)
```
修改为：

```swift
toViewController.view.frame = CGRectOffset(finalFrameForVC, 0, -bounds.size.height)
```
to the statement below, which changes the original position of the to view controller to be above the screen.

这将让 to 视图从屏幕上方向下滑入。

Run the app and the Action view should fall from above.

运行 App 效果如下：
![](http://www.appcoda.com/wp-content/uploads/2015/03/vid03.gif)

##Custom Dismiss Transition
##自定义解散动画
We’ve set a custom transition for presenting our view, but when it is dismissed, it uses the default transition set by Apple.

前面我们自定义了呈现动画，但当视图解散时，仍然使用的是默认的动画效果。
![](http://www.appcoda.com/wp-content/uploads/2015/03/vid04.gif)

The UIViewControllerTransitioningDelegate also allows you to specify an animation controller to use when dismissing a view controller as well as when presenting one. We’ll create this next.

UIViewControllerTransitioningDelegate 协议还允许我们指定一个动画控制器座位解散动画，就如同呈现时我们所做的一样。我们接下来就来实现它。

Create a class named CustomDismissAnimationController that is a subclass of NSObject. Modify its declaration as shown.

创建一个 NSObject 子类 CustomDismissAnimationController，修改其类声明如下：

```swift
class CustomDismissAnimationController: NSObject, UIViewControllerAnimatedTransitioning {
```
Add the following to the class.

添加如下方法：

```swift
func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -> NSTimeInterval {
    return 2
}
 
func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
    let fromViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
    let toViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
    let finalFrameForVC = transitionContext.finalFrameForViewController(toViewController)
    let containerView = transitionContext.containerView()
    toViewController.view.frame = finalFrameForVC
    toViewController.view.alpha = 0.5
    containerView.addSubview(toViewController.view)
    containerView.sendSubviewToBack(toViewController.view)
    
    UIView.animateWithDuration(transitionDuration(transitionContext), animations: {
        fromViewController.view.frame = CGRectInset(fromViewController.view.frame, fromViewController.view.frame.size.width / 2, fromViewController.view.frame.size.height / 2)
        toViewController.view.alpha = 1.0
    }, completion: {
        finished in
        transitionContext.completeTransition(true)
    })
}
```
This is similar to the implementation of the presentation transition. In the animateTransition() function, we get the to and from view controllers. The to view controller here is the table view controller. We change its view’s alpha value so that it will start off as being faded when we start animating. We then add the view to the container and place it behind the from view controller’s view so that it won’t be visible just yet.

其实这和呈现动画的实现真的很像。在 animateTransition() 方法中，我们获取 to/from 视图控制器。在这里，to 控制器变成了表格控制器。我们修改了 to 视图在动画正式开始之前的 alpha 值。然后将 to 视图添加到 containerView，并将它放到 from 视图的后面，以便它在一开始的时候不可见。

In the animation block, we animate the from view‘s size to have a width and height of 0, maintaining its center. This will have an effect of shrinking the from view to nothingness. We also animate the to view‘s alpha to being completely visible.

在动画块中，我们让 from 视图的大小变成 0,0，但中心位置不变。这将导致 from 视图由大变小直至消失。同时将 to 视图的 alpha 值设置为 1.0 使其可见。

In ItemsTableViewController add the following property.

在 ItemsTableViewController 中添加几个属性声明：

```swift
let customDismissAnimationController = CustomDismissAnimationController()

Add the following function to the class.

然后加入如下方法：

func animationControllerForDismissedController(dismissed: UIViewController) -> UIViewControllerAnimatedTransitioning? {
    return customDismissAnimationController
}

The UIViewControllerTransitioningDelegate protocol provides the above function which retrieves the animation controller of a dismissed view controller.

UIViewControllerTransitioningDelegate 协议的这个方法返回一个动画控制器作为解散控制器。

Run the app. You should see the following animation.

运行效果如下：
![](http://www.appcoda.com/wp-content/uploads/2015/03/vid05.gif)

The animation isn’t what we expected. You can see the white frame of the from view shrinks as expected, but the image on the view doesn’t change in size. This is because changing the view’s frame doesn’t affect its children. We’ll fix this by using UIView snapshotting.

动画效果不是我们所预料的。你可以看到 from 视图的白色框架确实是如预期的缩小了，但图片的尺寸根本不会改变。这是因为仅仅改变视图的 frame 并不会影响它的 subviews。我们可以用 UIView 的截屏功能解决这个问题。

UIView snapshotting works by taking a snapshot of an existing UIView and rendering it into a lightweight UIView. We will then use this snapshot in out animation and not the actual view.

UIView 截屏功能会对一个 UIView 进行截图，将它绘制在一个“轻量级”的 UIView 中。我们将在淡入淡出动画中使用截屏进行动画，而不是直接用真正的视图进行动画。

Replace the animateTransition() function with the following.

将 animateTransition() 方法代码修改为：

```swift
func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
    let fromViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
    let toViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
    let finalFrameForVC = transitionContext.finalFrameForViewController(toViewController)
    let containerView = transitionContext.containerView()
    toViewController.view.frame = finalFrameForVC
    toViewController.view.alpha = 0.5
    containerView.addSubview(toViewController.view)
    containerView.sendSubviewToBack(toViewController.view)
        
    let snapshotView = fromViewController.view.snapshotViewAfterScreenUpdates(false)
    snapshotView.frame = fromViewController.view.frame
    containerView.addSubview(snapshotView)
        
    fromViewController.view.removeFromSuperview()
        
    UIView.animateWithDuration(transitionDuration(transitionContext), animations: {
        snapshotView.frame = CGRectInset(fromViewController.view.frame, fromViewController.view.frame.size.width / 2, fromViewController.view.frame.size.height / 2)
        toViewController.view.alpha = 1.0
    }, completion: {
        finished in
        snapshotView.removeFromSuperview()
        transitionContext.completeTransition(true)
    })  
}
```
Here, we create a snapshot of the from view controller‘s view, add it to the container and remove the from view from the container. We then shrink this snapshot in our animation and when the animation completes, we remove the snapshot view from the container.

这里，我们创建了 from 视图的截屏，将截屏加到 containerView，然后将 from 视图从 containerView 中移除。

Run it and the animation should now run smoothly.

现在，动画效果终于正常了。
![](http://www.appcoda.com/wp-content/uploads/2015/03/vid06.gif)

##Navigation controller transitions
##定制导航控制器转换动画

We’ve looked at adding a custom transition for modal view controller presentation where we added a transitioning delegate to the presenting view controller. However, setting a delegate on every view controller can get tiresome when working with a UITabBarController or UINavigationController.

前面看到，要在呈现一个模式窗口的过程中呈现自定义动画，我们需要让负责呈现的 View Controller 实现一个 Transitioning 协议。但是，为每个 View Controller 都指定一个委托对象还是太麻烦了，尤其是当我们在使用 UITabBarController 或 UINavigationController 的时候。

These controllers give a simpler approach whereby the animation controller for a transition is supplied via the UITabBarControllerDelegate or UINavigationControllerDelegate.

对于这两种控制器，有一种更简单的办法，可以使用 UITabBarControllerDelegate 或者 UINavigationControllerDelegate 协议来实现自定义转换动画。

We’ll see this in action by adding a custom transition to a navigation controller.

我们来看一下自定义导航控制器转换动画的例子。

To start off, we create an animation controller. Create a class called CustomNavigationAnimationController, make it a subclass of NSObject and change its declaration as follows.

首先，创建一个动画控制器。新建一个 NSObject 子类 CustomNavigationAnimationController。修改其类声明如下：

```swift
class CustomNavigationAnimationController: NSObject, UIViewControllerAnimatedTransitioning {
```

Add the following to the class. I use a simplified version of this cube animation for this animation controller. The animation controller is set up as usual just like we’ve seen with the previous two animation controllers. Notice the reverse class variable. We use this to determine the direction of the animation, depending on whether we are moving from master to detail view or vice versa.

然后编写如下代码。这里我准备实现一个简单的魔方动画。这个动画控制器和前面实现的两个动画控制器类似。注意 reverse 变量，我们用这个变量来指定动画的方向，也就是说要进行的动画是主视图->从视图,抑或相反方向。

```swift
var reverse: Bool = false
    
func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -> NSTimeInterval {
    return 1.5
}
    
func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
    let containerView = transitionContext.containerView()
    let toViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
    let fromViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
    let toView = toViewController.view
    let fromView = fromViewController.view
    let direction: CGFloat = reverse ? -1 : 1
    let const: CGFloat = -0.005
        
    toView.layer.anchorPoint = CGPointMake(direction == 1 ? 0 : 1, 0.5)
    fromView.layer.anchorPoint = CGPointMake(direction == 1 ? 1 : 0, 0.5)
        
    var viewFromTransform: CATransform3D = CATransform3DMakeRotation(direction * CGFloat(M_PI_2), 0.0, 1.0, 0.0)
    var viewToTransform: CATransform3D = CATransform3DMakeRotation(-direction * CGFloat(M_PI_2), 0.0, 1.0, 0.0)
    viewFromTransform.m34 = const
    viewToTransform.m34 = const
        
    containerView.transform = CGAffineTransformMakeTranslation(direction * containerView.frame.size.width / 2.0, 0)
    toView.layer.transform = viewToTransform
    containerView.addSubview(toView)
        
    UIView.animateWithDuration(transitionDuration(transitionContext), animations: {
        containerView.transform = CGAffineTransformMakeTranslation(-direction * containerView.frame.size.width / 2.0, 0)
        fromView.layer.transform = viewFromTransform
        toView.layer.transform = CATransform3DIdentity
    }, completion: {
        finished in
        containerView.transform = CGAffineTransformIdentity
        fromView.layer.transform = CATransform3DIdentity
        toView.layer.transform = CATransform3DIdentity
        fromView.layer.anchorPoint = CGPointMake(0.5, 0.5)
        toView.layer.anchorPoint = CGPointMake(0.5, 0.5)
    
        if (transitionContext.transitionWasCancelled()) {
            toView.removeFromSuperview()
        } else {
            fromView.removeFromSuperview()
        }
        transitionContext.completeTransition(!transitionContext.transitionWasCancelled())
    })        
}
```
Open ItemsTableViewController.swift and modify the class declaration as follows.

打开 ItemsTableViewController.swift，将类声明修改为：

```swift
class ItemsTableViewController: UITableViewController, UIViewControllerTransitioningDelegate, UINavigationControllerDelegate {
```
UINavigationControllerDelegate supplies the animation controller.

UINavigationControllerDelegate 协议负责提供动画控制器对象。

Add the following property to the class.

添加如下属性：

```swift
let customNavigationAnimationController = CustomNavigationAnimationController()
```
Add the following at the end of viewDidLoad().

在 viewDidLoad 方法最后添加：

```swift
navigationController?.delegate = self
```
The above sets the host navigation controller’s delegate so that the new transition delegate methods can be received.

这句代码将导航控制器的 delegate 设置为 self，这样 ItemsTableViewController 就必须实现新的转换委托方法。

Then add the following to the class.

新增如下方法：

```swift
func navigationController(navigationController: UINavigationController, animationControllerForOperation operation: UINavigationControllerOperation, fromViewController fromVC: UIViewController, toViewController toVC: UIViewController) -> UIViewControllerAnimatedTransitioning? {
    customNavigationAnimationController.reverse = operation == .Pop
    return customNavigationAnimationController
}
```

The above function is called to request for an animation controller to navigate between the from and to view controllers and it returns an instance of our animation controller. The direction of the transition is based on whether this is a push or pop navigation operation.

这个方法会在导航控制器发生导航时调用，导航控制器会要求这个方法返回一个动画控制器实例。转换的方向根据动画的类型（Push或Pop)而定。

Run the app. Select a table view cell and you should see the animation below.

运行程序。点击表格单元格，效果如下：
![](http://www.appcoda.com/wp-content/uploads/2015/03/vid07.gif)

## 交互式转换
##Making it Interactive
We’ll make the above transition interactive, i.e. the user will be able to control the transition with gestures.

我们将让上述转换变成“交互式转换”，这样用户可以用手势来进行视图控制器的转换。

iOS built-in apps come with this feature. As an alternative to the back button, you can initiate a transition by swiping from the left side of the screen. You can use a short swipe to briefly view the master view and then cancel the transition. A long swipe initiates the pop navigation operation.

iOS 有许多 App 支持这个特性。通过交互式转换，你可以从左向右滑动来替代返回按钮的功能。你还可以用小幅度的轻扫手势查看 master 视图的内容，然后中途取消转换。大幅度的轻扫手势则是进行 Pop 导航操作。
![](http://www.appcoda.com/wp-content/uploads/2015/03/vid08.gif)

To get started, we need an interaction controller. Interactive controllers use the UIViewControllerInteractiveTransitioning protocol. The navigation controller delegate or the transitioning delegate requests for an optional interaction controller after requesting an animation controller.

首先，需要创建一个交互式控制器。交互式控制器需要实现 UIViewControllerInteractiveTransitioning 协议。UINavigationControllerDelegate 或 Transitioning 委托在请求完一个动画控制器之后还会请求一个交互式控制器。

Let’s create the interaction controller. Create a new class and name it CustomInteractionController and make it a subclass of UIPercentDrivenInteractiveTransition.

接下来创建交互式控制器。新建一个 UIPercentDrivenInteractiveTransition 子类，名为 CustomInteractionController。

UIPercentDrivenInteractiveTransition implements the UIViewControllerInteractiveTransitioning protocol so we wont have to add that to our class.

UIPercentDrivenInteractiveTransition 类已经实现了 UIViewControllerInteractiveTransitioning 协议，因此我们的类就没有必要再声明对这个协议的实现了。

To use UIPercentDrivenInteractiveTransition, your animation controller must use a single UIView animation, so that the animation will be able to be stopped, reversed and played.

对于 UIPercentDrivenInteractiveTransition 子类，我们必须使用单独的 UIView 动画，以便动画能够被停止、返回和播放。

Add the following to the class.

继续添加如下代码：

```swift
var navigationController: UINavigationController!
var shouldCompleteTransition = false
var transitionInProgress = false
var completionSeed: CGFloat {
    return 1 - percentComplete
}
   
func attachToViewController(viewController: UIViewController) {
    navigationController = viewController.navigationController
    setupGestureRecognizer(viewController.view)
}
    
private func setupGestureRecognizer(view: UIView) {
        view.addGestureRecognizer(UIPanGestureRecognizer(target: self, action: "handlePanGesture:"))
}
    
func handlePanGesture(gestureRecognizer: UIPanGestureRecognizer) {
    let viewTranslation = gestureRecognizer.translationInView(gestureRecognizer.view!.superview!)
    switch gestureRecognizer.state {
    case .Began:
        transitionInProgress = true
        navigationController.popViewControllerAnimated(true)
    case .Changed:
        var const = CGFloat(fminf(fmaxf(Float(viewTranslation.x / 200.0), 0.0), 1.0))
        shouldCompleteTransition = const > 0.5
        updateInteractiveTransition(const)
    case .Cancelled, .Ended:
        transitionInProgress = false
        if !shouldCompleteTransition || gestureRecognizer.state == .Cancelled {
            cancelInteractiveTransition()
        } else {
            finishInteractiveTransition()
        }
    default:
        println("Swift switch must be exhaustive, thus the default")
    }
}
``` 

The attachToViewController() method is passed a reference of the navigation controller which it uses to initiate a pop transition when a gesture occurs. We then set up a gesture recognizer, which will call the handlePanGesture() method when a swipe is made. This checks the gesture recognizer state and does the following at each stage:

attachToViewController方法中，引用了一个导航控制器并保存到实例变量，以便当手势发生时用于初始化 Pop 转换。然后在视图中添加了一个手势识别器，并将手势处理方法指定为 handlePanGesture()方法。手势处理方法将处理手势的每一个状态，包括：

* Began: It sets transitionInProgress to true and initiates a pop navigation.

	开始：将 transitionInProgress 设置为 true，然后开始进行 Pop 导航。

* Changed: Here the gesture is in progress, so it determines the percentage of the transition. A 200 point swipe will cause the transition to be 100% complete. It then determines if the transition should complete depending on where the gesture finishes. Here we check if the user swiped to at least half the screen before releasing.

	改变：这个状态表示手势尚处于进行过程中，因此需要计算出转换的进度（百分比）。这里假设扫动距离>= 200像素视同手势100%的完成。如果小于此距离则计算手势划过的距离占200像素的百分之几，并以此作为动画完成的进度。同时我们检查用户在释放手指前，划过的距离是否超过了一半，并将判断结果保存到 shouldCompleteTransition 变量。

* Cancelled/Ended: Sets the transitionInProgress to false and cancels the transition if shouldCompleteTransition was set to false or if the gesture was cancelled. Otherwise, the transition is completed.

	取消/结束：将 transitionInProgress 设置为 false，同时判断 shouldCompleteTransition 变量是否为 false 或者手势已取消，如果是则取消动画，否则让动画完成。

We used a computed property to determine the completion speed. completionSeed is a UIPercentDrivenInteractiveTransition property that informs the framework how much of the animation remains when a gesture completes. A larger number will make the view controller snap back quicker if the interaction is cancelled.

我们使用一个计算属性来衡量完成速度。 UIPercentDrivenInteractiveTransitio 有一个 completionSeed 属性，这个属性用于告诉框架当手势已经完成时，还剩下多少动画需要播放。当手势取消时，如果这个数值越大，则 View Controller 弹回去的速度就越快。

To use our interaction controller, open ItemsTableViewController.swift and add the following to the class.

要使用这个交互式控制器，打开 ItemsTableViewController.swift，添加如下属性：

```swift
let customInteractionController = CustomInteractionController()
```

Add the following at the beginning of the navigationController(_:animationControllerForOperation:
fromViewController:toViewController:) function.

在 navigationController(_:animationControllerForOperation:fromViewController:toViewController:)方法开始部分加入：

```swift
if operation == .Push {
    customInteractionController.attachToViewController(toVC)
}
```

This calls the CustomInteractionController’s attachToViewController() method and passes it a reference of the to view controller when it detects a push navigation operation.

如果是 Push 动画，则调用 CustomInteractionController 的 attachToViewController() 方法，将 toViewController 传递给动画控制器。

Then add the following to the class.

然后添加如下方法：

```swift
func navigationController(navigationController: UINavigationController, interactionControllerForAnimationController animationController: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
    return customInteractionController.transitionInProgress ? customInteractionController : nil
}
```

After the framework asks for and gets an animation controller, it asks for an interaction controller using the method above. The above returns an instance of our interaction controller if a transition is in progress.

当框架请求一个动画控制器之时，它也会用上面的这个方法请求一个交互式控制器。在这个方法中，我们判断是否转换开始，如果是，则返回一个交互式控制器给它。

Run the app and you should see the transition shown.

运行 App ，查看转换动画效果：
![](http://www.appcoda.com/wp-content/uploads/2015/03/vid09.gif)

##Conclusion
##结论
We’ve looked at how to create custom view controller transitions and how to make a transition interactive. With these features, developers now have full control over the animations used in their application as it transitions from one view controller to the next and thus can build unique experiences to delight their users. You can download the completed project here.

我们学习了如何创建一个自定义的 View Controller 转换，以及如何创建交互式转换。通过这些特性，开发者可以在 App 中彻底控制从一个视图控制器切换到另一个视图控制器时的转换动画，并通过创建一系列独特的体验来取悦他们的用户。你可以[从此](https://github.com/appcoda/CustomViewTransitionDemo)下载完整的示例项目。

Note: This tutorial is also available in Chinese. We’re going to support other languages soon. If you want to join our translation team, please contact us.


注意: 本教程有一个中文版本（台湾繁体）。我们还将支持更多语言。如果你想参加我们的翻译小组，请和[我们](http://www.appcoda.com/contact/)联系。