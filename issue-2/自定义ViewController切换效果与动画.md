
# 使用Swift自定义ViewController切换效果与动画
>* 原文链接 : [custom-view-controller-transitions-tutorial](http://www.appcoda.com/custom-view-controller-transitions-tutorial/)
* 原文作者 : [joyce echessa](http://www.appcoda.com/custom-view-controller-transitions-tutorial/)
* [译文出自 :  开发技术前线 www.devtf.cn](www.devtf.cn)
* 译者 : [Shopie](https:www.devtd.cn)  

Looking at the built in apps from Apple on your iOS device, you will notice the various animated transitions as you move from one view to another for example the way view controllers are presented in master-detail views with a swipe that slides the detail view controller over the master view controller as seen in the Messages app or Settings app and the various transitions that represent a segue to another view controller.

当我们把从Apple iOS设备上内置的app从一个view切换到另一个的时候，我们可以看到各种不同的动态切换。比如，把详细view controller滑动到主view controller时呈现出的切换动画（通常在短信app或设置app中可以看到），以及代表了不同view controller之间切换的各种不间断过渡。

iOS 7 introduced custom view controller transitions which make it possible for developers to create their own animated transitions from one view controller to the next in their apps. In this tutorial, we’ll take a look at how to do this. We’ll also look at how to create gesture driven transitions called interactive transitions. To follow along, download the starter project which we’ll be using throughout the tutorial.

iOS 7引入了自定义view controller切换，使得开发人员能够在他们的app中实现不同 view controller之间的自定义切换动画。我们通过本教程来看看如何自定义切换动画。本教程还会讲到如何创建手势切换，也就是交互式切换。首先，大家需要下载starter项目，我们在后续的整个教程中将会用到。

![pic-1](http://www.appcoda.com/wp-content/uploads/2015/03/custom-view-transition.jpg)

## Getting Started

## 开始着手

To create custom transitions you have to follow three steps:

我们需要按照以下步骤来创建自定义切换：

* Create a class that implements the `UIViewControllerAnimatedTransitioning` protocol. Here you will write code that performs the animation. This class is referred to as the animation controller.

* 创建一个实现了`UIViewControllerAnimatedTransitioning`协议的类。这里我们需要写执行动画的代码。我们把这个类叫做animation controller。

* Before presenting a view controller, set a class as its transitioning delegate. The delegate will get a callback for the animation controller to be used when presenting the view controller.

* 在展示view controller之前，我们需要建立一个类作为其transitioning delegate。这个delegate会从animation controller得到一个回调信号用于展示view controller。

* Implement the callback method to return an instance of the animation controller from the first step.

* 实现回调方法返回步骤一的animation controller实例。

Run the starter project and you will be presented with a table view of a list of items. There is an Action button on the navigation bar and when you tap it you’ll be presented with another view that appears in the usual modal style of sliding up from the bottom. We will write a custom transition for this view.

运行starter项目后我们可以看到关于条目列表的表格视图。导航条上有一个Action按钮，点击这个按钮我们可以看到一个从底部上滑的以普通模态呈现的view。我们接下来就要为这个view写一个自定义切换程序。

![pic-2](http://www.appcoda.com/wp-content/uploads/2015/03/vid01.gif)

## Custom Present Transition

## 自定义Present Transition

The first thing to do as stated previously, is to create the animation controller. Create a new class called `CustomPresentAnimationController` and make it a subclass of NSObject. Change its declaration as shown.

上文提到，我们首先要做的是创建一个animation controller。创建一个继承自NSObject的`CustomPresentAnimationController`类。根据图示更改其定义。

```
class CustomPresentAnimationController: NSObject, UIViewControllerAnimatedTransitioning {
	// ....
}
```

`UIViewControllerAnimatedTransitioning` protocol has two required methods which we’ll add next. Add the following methods to the class.

`UIViewControllerAnimatedTransitioning`协议中有两个我们接下来需要添加的方法。将以下方法添加到类中。

```
func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -&gt; NSTimeInterval {
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

第一个方法指定了切换动画的时长。在demo app中我们将其设置为2.5秒，但在实际情况中的app大家将这个值设置为更短一些可能会更好。

In the second method we use the transitionContext to get the view controller we are navigating from, to, the final frame the transition context should have after the animation completes and the container view which houses the views corresponding to the to and from view controllers.

在第二个方法中我们使用transitionContext来获得我们从...导航、导航到的view controller，动画完成后transition context应得到的最终框架，以及存储与to view controller和from view controller相关的view的container view。

We then position the `to view` just below the bottom of the screen. Then we add the `to view` to the container view and in the animate closure, we animate the `to view` by setting its final frame to the location given by the transition context. We also animate the `from view`‘s alpha value so that as the `to view` is sliding up the screen over the `from view`, the `from view` will be faded out. The duration of the animation used is the one set in `transitionDuration(transitionContext:)`. In the completion closure, we notify the transition context when the animation completes and then change the `from view`‘s alpha back to normal. The framework will then remove the `from view` from the container.

然后我们把`to view`放在屏幕正下方。之后把它添加到container view和animate closure中，通过将其最终框架设置在transition context给出的位置来实现`to view`的动画效果。我们还会实现`from view`的alpha值的动画效果，以便`to view`往屏幕上方`from view`滑动时，`from view`会淡出。该动画效果设置的时长根据`transitionDuration(transitionContext:)`中的设置而定。在completion closure中，当动画完成时transition context会收到通知，然后将`from view`的alpha值恢复正常。之后框架会将`from view`从container中移出。

With the animation controller completed, we need to link it to a storyboard segue.

animation controller完成之后，我们需要将其链接到storyboard segue。

Open the ItemsTableViewController.swift file and change the class declaration as shown.

打开ItemsTableViewController.swift文件，按图所示更改类定义。

```
class ItemsTableViewController: UITableViewController, UIViewControllerTransitioningDelegate {
```

UIViewController has a property named `transitionDelegate` that supports custom transitions. When transitioning to a view controller, the framework checks this property to see if a custom transition should be used. `UIViewControllerTransitioningDelegate` supplies custom transitions.

UIViewController有一个叫做`transitionDelegate`的属性，这个属性用于支持自定义切换。当切换到一个view controller时，框架会检查这个属性以便确定是否需要使用自定义切换。

Open Main.storyboard and select the `Present modally segue to Action View Controller` and in the Attributes Inspector, set its Identifier to `showAction`.

打开Main.storyboard并选择`Present modally segue to Action View Controller`，在Attributes Inspector中将其Identifier设置为`showAction`。

![pic-3](http://www.appcoda.com/wp-content/uploads/2015/03/pic02.png)

Back in ItemsTableViewController add the following to the class.

返回ItemsTableViewController并将下列代码添加到类中。

```
let customPresentAnimationController = CustomPresentAnimationController()

override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
        
    if segue.identifier == "showAction" {
        let toViewController = segue.destinationViewController as UIViewController
        toViewController.transitioningDelegate = self
    }
}
```

Here we create an instance of our animation controller and then in the `prepareForSegue()` function, we detect the segue for the Action screen and set the `transitionDelegate` property of the destination view controller.

这里我们将创建一个animation controller的实例，之后我们在`prepareForSegue()`功能中发现Action屏幕的segue，并设置destination view controller的`transitionDelegate`属性。

Add the following `UIViewControllerTransitioningDelegate` method to the class. This returns the instance of our custom animation controller.

将下列`UIViewControllerTransitioningDelegate`方法添加到类中。这一步会返回我们的自定义animation controller实例。

```
func animationControllerForPresentedController(presented: UIViewController, presentingController presenting: UIViewController, sourceController source: UIViewController) -&gt; UIViewControllerAnimatedTransitioning? {
    return customPresentAnimationController
}
```

Run the application and you should see the Action view slide up slowly from the screen and bounce a little before settling.

运行app后我们可以看到屏幕中的Action view缓慢往上滑动，最后稳定下来之前出现些许弹跳。

![pic-4](http://www.appcoda.com/wp-content/uploads/2015/03/vid02.gif)

If you want a slightly different effect, then change this statement in CustomPresentAnimationController.swift

如果大家想看到一个稍微不同的结果，那么我们可以在CustomPresentAnimationController.swift稍作改变：

```
toViewController.view.frame = CGRectOffset(finalFrameForVC, 0, bounds.size.height)
```

to the statement below, which changes the original position of the to view controller to be above the screen.

改变后如下所示，其中我们将to view controller的原始位置放到了屏幕上方。

```
toViewController.view.frame = CGRectOffset(finalFrameForVC, 0, -bounds.size.height)
```

Run the app and the Action view should fall from above.

运行app，Action view会从上往下落。

![pic-5](http://www.appcoda.com/wp-content/uploads/2015/03/vid03.gif)

##自定义消隐(Dismiss)转场效果

We’ve set a custom transition for presenting our view, but when it is dismissed, it uses the default transition set by Apple.

我们之前为了呈现view设置了一个自定义切换效果，但当View消隐时，view会使用Apple设定的默认切换。

![pic-6](http://www.appcoda.com/wp-content/uploads/2015/03/vid04.gif)

The `UIViewControllerTransitioningDelegate` also allows you to specify an animation controller to use when dismissing a view controller as well as when presenting one. We’ll create this next.

`UIViewControllerTransitioningDelegate`也可以使我们在解散以及呈现view controller时指定一个animation controller来使用。下面我们就来创建。

Create a class named `CustomDismissAnimationController` that is a subclass of `NSObject`. Modify its declaration as shown.

创建一个名为 `CustomDismissAnimationController` 的类，作为`NSObject`的子类。按照以下代码更改其定义。

```
class CustomDismissAnimationController: NSObject, UIViewControllerAnimatedTransitioning {
```

Add the following to the class.

将以下代码添加到类中。

```
func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -&gt; NSTimeInterval {
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

This is similar to the implementation of the presentation transition. In the `animateTransition()` function, we get the to and from view controllers. The `to view controller` here is the table view controller. We change its view’s alpha value so that it will start off as being faded when we start animating. We then add the view to the container and place it behind the from view controller’s view so that it won’t be visible just yet.

这与实现presentation transition非常相似。在`animateTransition()`功能中，我们得到to view controller和from view controller。这里的 `to view controller`作为table view controller。我们改变其view的alpha值，以便我们开始制作动画效果时它会在淡出时迅速消失。之后我们把view添加到container并把它放置在from view controller的view后面以保证其不可视。

In the animation block, we animate the `from view`‘s size to have a width and height of 0, maintaining its center. This will have an effect of shrinking the `from view` to nothingness. We also animate the `to view`‘s alpha to being completely visible.

在动画块中，我们为`from view`的尺寸制作动画效果，使其宽和高为0并保持居中。这样我们就可以得到一个将`from view`缩减至无的效果。我们还会把`to view`的alpha进行动态化以便使其完全可视。

In ItemsTableViewController add the following property.

添加以下属性至ItemsTableViewController中。

```
let customDismissAnimationController = CustomDismissAnimationController()
```

Add the following function to the class.

添加以下功能至类中。

```
func animationControllerForDismissedController(dismissed: UIViewController) -&gt; UIViewControllerAnimatedTransitioning? {
    return customDismissAnimationController
}
```

The `UIViewControllerTransitioningDelegate` protocol provides the above function which retrieves the animation controller of a dismissed view controller.

`UIViewControllerTransitioningDelegate`协议提供了以上功能，把一个解散的view controller中的animation controller收回。

Run the app. You should see the following animation.

运行app我们可以看到以下效果。

![pic-7](http://www.appcoda.com/wp-content/uploads/2015/03/vid05.gif)

The animation isn’t what we expected. You can see the white frame of the from view shrinks as expected, but the image on the view doesn’t change in size. This is because changing the view’s frame doesn’t affect its children. We’ll fix this by using UIView snapshotting.

这个动画效果并不是我们预期的。大家可以看到，from view的白色框架如我们所预期的缩小了，但是view中的image的尺寸并没有变化。这是因为view的边框改变并不影响其子框架。那么我们将使用UIView snapshotting来修复。

UIView snapshotting works by taking a snapshot of an existing UIView and rendering it into a lightweight UIView. We will then use this snapshot in out animation and not the actual view.

UIView snapshotting的工作原理是通过给现有的UIView生成快照并将其放入一个轻量级UIView。之后我们会在淡出动画，而不是实际view中使用这张快照。

Replace the `animateTransition()` function with the following.

用以下代码替换`animateTransition()`功能。

```
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

Here, we create a snapshot of the `from view controller`‘s view, add it to the container and remove the `from view` from the container. We then shrink this snapshot in our animation and when the animation completes, we remove the snapshot view from the container.

这里我们会创建一个`from view controller`的view的快照，将其添加到container并把container中的`from view`移出。之后我们在动画中缩小这张快照，动画完成后，将snapshot view从container中移出。

Run it and the animation should now run smoothly.

再次运行时动画应该会很顺畅了。

![pic-8](http://www.appcoda.com/wp-content/uploads/2015/03/vid06.gif)

##Navigation controller transitions

##Navigation controller transitions

We’ve looked at adding a custom transition for modal view controller presentation where we added a transitioning delegate to the presenting view controller. However, setting a delegate on every view controller can get tiresome when working with a `UITabBarController` or `UINavigationController`.

我们已经在modal view controller presentation中添加了一个自定义切换，这个自定义切换中的presenting view controller添加了transitioning delegate。但是，当我们使用`UITabBarController`或`UINavigationController`来工作时，每个view controller都设置一个delegate就显得非常麻烦。

These controllers give a simpler approach whereby the animation controller for a transition is supplied via the `UITabBarControllerDelegate` or `UINavigationControllerDelegate`.

这些controller提供了一个更简单的方法，通过这个方法transition的animationcontroller会通过`UITabBarControllerDelegate` 或 `UINavigationControllerDelegate`来提供。

We’ll see this in action by adding a custom transition to a navigation controller.

在navigation controller中添加一个自定义切换后我们可以看到其运转状态。

To start off, we create an animation controller. Create a class called `CustomNavigationAnimationController`, make it a subclass of `NSObject` and change its declaration as follows.

首先，创建一个animation controller。创建一个名为`CustomNavigationAnimationController`的类，将其作为`NSObject`的子类并按下列方式更改其定义。

```
class CustomNavigationAnimationController: NSObject, UIViewControllerAnimatedTransitioning {
```

Add the following to the class. I use a simplified version of [this cube animation](https://github.com/andresbrun/ABCustomUINavigationController#cube) for this animation controller. The animation controller is set up as usual just like we’ve seen with the previous two animation controllers. Notice the reverse class variable. We use this to determine the direction of the animation, depending on whether we are moving from master to detail view or vice versa.

将以下代码添加到类中。对于这个animation controller，我本人使用的是一个简版的[this cube animation](https://github.com/andresbrun/ABCustomUINavigationController#cube)。我们只需要像前面提到的两个animation controller一样建立这个animation controller。需要注意逆类变量。我们要用它来决定动画的方向，取决于我们是否从主视图过渡到详细视图或从详细视图过渡到主视图。

```
var reverse: Bool = false
    
func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -&gt; NSTimeInterval {
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

打开ItemsTableViewController并按照以下内容修改类定义。

```
class ItemsTableViewController: UITableViewController, UIViewControllerTransitioningDelegate, UINavigationControllerDelegate {
```

`UINavigationControllerDelegate` supplies the animation controller.

`UINavigationControllerDelegate`提供animation controller。

Add the following property to the class.

将以下属性添加到类中。

```
let customNavigationAnimationController = CustomNavigationAnimationController()
```

Add the following at the end of `viewDidLoad()`.

在`viewDidLoad()`末尾添加以下代码。

```
navigationController?.delegate = self
```

The above sets the host navigation controller’s delegate so that the new transition delegate methods can be received.

以上步骤设置了host navigation controller的delegate以便新的transition delegate方法能够被收到。

Then add the following to the class.

然后将以下代码添加到类中。

```
func navigationController(navigationController: UINavigationController, animationControllerForOperation operation: UINavigationControllerOperation, fromViewController fromVC: UIViewController, toViewController toVC: UIViewController) -&gt; UIViewControllerAnimatedTransitioning? {
    customNavigationAnimationController.reverse = operation == .Pop
    return customNavigationAnimationController
}
```

The above function is called to request for an animation controller to navigate between the from and to view controllers and it returns an instance of our animation controller. The direction of the transition is based on whether this is a push or pop navigation operation.

以上功能称之为请求animation controller在from view controller和to view controller之间进行导航，它会返回animation controller的实例。切换的方向取决于这是一个push navigation还是一个pop navigation操作。

Run the app. Select a table view cell and you should see the animation below.

运行app。选择一个table view cell后大家看到的动画效果如下。

![pic-9](http://www.appcoda.com/wp-content/uploads/2015/03/vid07.gif)

##Making it Interactive

##使其更具交互式特点

We’ll make the above transition interactive, i.e. the user will be able to control the transition with gestures.

接下来我们将使以上切换更具交互式特点，也就是说，用户能够通过手势控制切换。

iOS built-in apps come with this feature. As an alternative to the back button, you can initiate a transition by swiping from the left side of the screen. You can use a short swipe to briefly view the master view and then cancel the transition. A long swipe initiates the pop navigation operation.

iOS内置app具有这个特点。我们可以从屏幕左侧滑动来发起切换，代替后退按钮的功能。大家可以进行短暂滑动以下来简单查看主视图，然后取消切换。滑动距离稍长就会触发pop navigation操作。

![pic-10](http://www.appcoda.com/wp-content/uploads/2015/03/vid08.gif)

To get started, we need an interaction controller. Interactive controllers use the `UIViewControllerInteractiveTransitioning` protocol. The navigation controller delegate or the transitioning delegate requests for an optional interaction controller after requesting an animation controller.

首先，我们需要一个interaction controller。交互式controller使用`UIViewControllerInteractiveTransitioning`协议。navigation controller delegate 或 transitioning delegate在请求animation controller后会要求一个可选的interaction controller。

Let’s create the interaction controller. Create a new class and name it `CustomInteractionController` and make it a subclass of `UIPercentDrivenInteractiveTransition`.

我们来创建一个interaction controller。创建一个新的类，取名为`CustomInteractionController`并使其作为`UIPercentDrivenInteractiveTransition`的子类。

`UIPercentDrivenInteractiveTransition` implements the `UIViewControllerInteractiveTransitioning` protocol so we wont have to add that to our class.

由于`UIPercentDrivenInteractiveTransition` 实现 `UIViewControllerInteractiveTransitioning`协议，所以我们不需要添加到类中。

To use `UIPercentDrivenInteractiveTransition`, your animation controller must use a single UIView animation, so that the animation will be able to be stopped, reversed and played.

要想使用`UIPercentDrivenInteractiveTransition`，animation controller必须使用单个UIView动画，以便动画能够被停止、翻转及使用。

Add the following to the class.

把以下代码添加到类中。

```
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
        shouldCompleteTransition = const &gt; 0.5
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

The `attachToViewController()` method is passed a reference of the navigation controller which it uses to initiate a pop transition when a gesture occurs. We then set up a gesture recognizer, which will call the `handlePanGesture()` method when a swipe is made. This checks the gesture recognizer state and does the following at each stage:

`attachToViewController()`方法获得navigation controller在发生手势操作时用来发起pop transition的引用。下一步，我们设立一个手势识别器，滑动操作时识别器会调用`handlePanGesture()`方法。这检查了手势识别器的状态并在每一个阶段执行下列操作：

* Began: It sets `transitionInProgress` to true and initiates a pop navigation.

* 开始：把`transitionInProgress` 设为真，并发起pop navigation。

* Changed: Here the gesture is in progress, so it determines the percentage of the transition. A 200 point swipe will cause the transition to be 100% complete. It then determines if the transition should complete depending on where the gesture finishes. Here we check if the user swiped to at least half the screen before releasing.

* 更改：此时手势正在进行，所以它决定了切换的百分比。一个200点滑动会是切换达到100%。之后它将通过手势停止的地方来判断切换是否完成。此时我们要确定用户是否在释放前滑到了屏幕的一半处。

* Cancelled/Ended: Sets the `transitionInProgress` to false and cancels the transition if `shouldCompleteTransition` was set to false or if the gesture was cancelled. Otherwise, the transition is completed.

* 取消/结束：如果`shouldCompleteTransition`被设为假或者手势被取消，把`transitionInProgress`设为假，并取消切换。否则，切换完成。

We used a computed property to determine the completion speed. `completionSeed` is a `UIPercentDrivenInteractiveTransition` property that informs the framework how much of the animation remains when a gesture completes. A larger number will make the view controller snap back quicker if the interaction is cancelled.

我们使用计算属性来确定完成速度。`completionSeed`具有`UIPercentDrivenInteractiveTransition`属性，它会在手势完成时通知框架还剩多少动画。如果互动取消，数字更大会使view controller更迅速返回。

To use our interaction controller, open ItemsTableViewController.swift and add the following to the class.

打开ItemsTableViewController.swift 并把下列代码添加到类中之后，大家就可以使用我们的interaction controller了。

```
let customInteractionController = CustomInteractionController()
```

Add the following at the beginning of the `navigationController(_:animationControllerForOperation:
fromViewController:toViewController:)` function.

在`navigationController(_:animationControllerForOperation:
fromViewController:toViewController:)`功能起始处添加以下代码。

```
if operation == .Push {
    customInteractionController.attachToViewController(toVC)
}
```

This calls the CustomInteractionController’s `attachToViewController()` method and passes it a reference of the to view controller when it detects a push navigation operation.

它会调用CustomInteractionController的`attachToViewController()`方法并在发现push navigation操作时给这个方法传递to view controller的一个引用。

Then add the following to the class.

然后添加以下代码到类中。

```
func navigationController(navigationController: UINavigationController, interactionControllerForAnimationController animationController: UIViewControllerAnimatedTransitioning) -&gt; UIViewControllerInteractiveTransitioning? {
    return customInteractionController.transitionInProgress ? customInteractionController : nil
}
```

After the framework asks for and gets an animation controller, it asks for an interaction controller using the method above. The above returns an instance of our interaction controller if a transition is in progress.

框架请求并获得animation controller后，它会使用以上方法请求interaction controller。如果切换正在进行，那么将会返回interaction controller的一个实例。

Run the app and you should see the transition shown.

运行app后我们将看到如图所示的切换。

![pic-11](http://www.appcoda.com/wp-content/uploads/2015/03/vid09.gif)

## Conclusion

## 总结

We’ve looked at how to create custom view controller transitions and how to make a transition interactive. With these features, developers now have full control over the animations used in their application as it transitions from one view controller to the next and thus can build unique experiences to delight their users. You can [download the completed project here](https://github.com/appcoda/CustomViewTransitionDemo).

大家已经知道如何创建自定义view controller切换以及如何使切换具有交互性。有了这些特性之后，开发者可以完全掌控app中从一个view切换到另一个view时使用的动画，由此带给用户更好的体验。大家可以在[此处下载项目](https://github.com/appcoda/CustomViewTransitionDemo)。


