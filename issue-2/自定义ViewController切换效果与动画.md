
# 使用Swift自定义ViewController切换效果与动画
>* 原文链接 : [custom-view-controller-transitions-tutorial](http://www.appcoda.com/custom-view-controller-transitions-tutorial/)
* 原文作者 : [joyce echessa](http://www.appcoda.com/custom-view-controller-transitions-tutorial/)
* [译文出自 :  开发技术前线 www.devtf.cn](www.devtf.cn)
* 译者 : [Shopie](https:www.devtd.cn)  


当我们把从Apple iOS设备上内置的app从一个view切换到另一个的时候，我们可以看到各种不同的动态切换。比如，把详细view controller滑动到主view controller时呈现出的切换动画（通常在短信app或设置app中可以看到），以及代表了不同view controller之间切换的各种不间断过渡。


iOS 7引入了自定义view controller切换，使得开发人员能够在他们的app中实现不同 view controller之间的自定义切换动画。我们通过本教程来看看如何自定义切换动画。本教程还会讲到如何创建手势切换，也就是交互式切换。首先，大家需要下载starter项目，我们在后续的整个教程中将会用到。

![pic-1](http://www.appcoda.com/wp-content/uploads/2015/03/custom-view-transition.jpg)

## 开始着手

我们需要按照以下步骤来创建自定义切换：

* 创建一个实现了`UIViewControllerAnimatedTransitioning`协议的类。这里我们需要写执行动画的代码。我们把这个类叫做animation controller。

* 在展示view controller之前，我们需要建立一个类作为其transitioning delegate。这个delegate会从animation controller得到一个回调信号用于展示view controller。

* 实现回调方法返回步骤一的animation controller实例。

运行starter项目后我们可以看到关于条目列表的表格视图。导航条上有一个Action按钮，点击这个按钮我们可以看到一个从底部上滑的以普通模态呈现的view。我们接下来就要为这个view写一个自定义切换程序。

![pic-2](http://www.appcoda.com/wp-content/uploads/2015/03/vid01.gif)

## 自定义Present Transition

上文提到，我们首先要做的是创建一个animation controller。创建一个继承自NSObject的`CustomPresentAnimationController`类。根据图示更改其定义。

```
class CustomPresentAnimationController: NSObject, UIViewControllerAnimatedTransitioning {
	// ....
}
```

`UIViewControllerAnimatedTransitioning`协议中有两个我们接下来需要添加的方法。将以下方法添加到类中。

```
func transitionDuration(transitionContext: UIViewControllerContextTransitioning?) -> NSTimeInterval {
    return 2.5
}
    
func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
        
    let fromViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
    let toViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
    let finalFrameForVC = transitionContext.finalFrameForViewController(toViewController)
    let containerView = transitionContext.containerView()
    let bounds = UIScreen.mainScreen().bounds
    toViewController.view.frame = CGRectOffset(finalFrameForVC, 0, bounds.size.height)
    containerView?.addSubview(toViewController.view)
        
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

第一个方法指定了切换动画的时长。在demo app中我们将其设置为2.5秒，但在实际情况中的app大家将这个值设置为更短一些可能会更好。

在第二个方法中我们使用transitionContext来获得我们从...导航、导航到的view controller，动画完成后transition context应得到的最终框架，以及存储与to view controller和from view controller相关的view的container view。

然后我们把`to view`放在屏幕正下方。之后把它添加到container view和animate closure中，通过将其最终框架设置在transition context给出的位置来实现`to view`的动画效果。我们还会实现`from view`的alpha值的动画效果，以便`to view`往屏幕上方`from view`滑动时，`from view`会淡出。该动画效果设置的时长根据`transitionDuration(transitionContext:)`中的设置而定。在completion closure中，当动画完成时transition context会收到通知，然后将`from view`的alpha值恢复正常。之后框架会将`from view`从container中移出。

animation controller完成之后，我们需要将其链接到storyboard segue。

打开ItemsTableViewController.swift文件，按图所示更改类定义。

```
class ItemsTableViewController: UITableViewController, UIViewControllerTransitioningDelegate {
```

UIViewController有一个叫做`transitionDelegate`的属性，这个属性用于支持自定义切换。当切换到一个view controller时，框架会检查这个属性以便确定是否需要使用自定义切换。

打开Main.storyboard并选择`Present modally segue to Action View Controller`，在Attributes Inspector中将其Identifier设置为`showAction`。

![pic-3](http://www.appcoda.com/wp-content/uploads/2015/03/pic02.png)

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

这里我们将创建一个animation controller的实例，之后我们在`prepareForSegue()`功能中发现Action屏幕的segue，并设置destination view controller的`transitionDelegate`属性。

将下列`UIViewControllerTransitioningDelegate`方法添加到类中。这一步会返回我们的自定义animation controller实例。

```
func animationControllerForPresentedController(presented: UIViewController, presentingController presenting: UIViewController, sourceController source: UIViewController) -> UIViewControllerAnimatedTransitioning? {
    return customPresentAnimationController
}
```

运行app后我们可以看到屏幕中的Action view缓慢往上滑动，最后稳定下来之前出现些许弹跳。

![pic-4](http://www.appcoda.com/wp-content/uploads/2015/03/vid02.gif)

如果大家想看到一个稍微不同的结果，那么我们可以在CustomPresentAnimationController.swift稍作改变：

```
toViewController.view.frame = CGRectOffset(finalFrameForVC, 0, bounds.size.height)
```

改变后如下所示，其中我们将to view controller的原始位置放到了屏幕上方。

```
toViewController.view.frame = CGRectOffset(finalFrameForVC, 0, -bounds.size.height)
```

运行app，Action view会从上往下落。

![pic-5](http://www.appcoda.com/wp-content/uploads/2015/03/vid03.gif)

##自定义消隐(Dismiss)转场效果

我们之前为了呈现view设置了一个自定义切换效果，但当View消隐时，view会使用Apple设定的默认切换。

![pic-6](http://www.appcoda.com/wp-content/uploads/2015/03/vid04.gif)

`UIViewControllerTransitioningDelegate`也可以使我们在解散以及呈现view controller时指定一个animation controller来使用。下面我们就来创建。

创建一个名为 `CustomDismissAnimationController` 的类，作为`NSObject`的子类。按照以下代码更改其定义。

```
class CustomDismissAnimationController: NSObject, UIViewControllerAnimatedTransitioning {
```

将以下代码添加到类中。

```
func transitionDuration(transitionContext: UIViewControllerContextTransitioning?) -> NSTimeInterval {
    return 2
}

func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
    let fromViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
    let toViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
    let finalFrameForVC = transitionContext.finalFrameForViewController(toViewController)
    let containerView = transitionContext.containerView()
    toViewController.view.frame = finalFrameForVC
    toViewController.view.alpha = 0.5
    containerView?.addSubview(toViewController.view)
    containerView?.sendSubviewToBack(toViewController.view)
    
    UIView.animateWithDuration(transitionDuration(transitionContext), animations: {
        fromViewController.view.frame = CGRectInset(fromViewController.view.frame, fromViewController.view.frame.size.width / 2, fromViewController.view.frame.size.height / 2)
        toViewController.view.alpha = 1.0
    }, completion: {
        finished in
        transitionContext.completeTransition(true)
    })
}
```

这与实现presentation transition非常相似。在`animateTransition()`功能中，我们得到to view controller和from view controller。这里的 `to view controller`作为table view controller。我们改变其view的alpha值，以便我们开始制作动画效果时它会在淡出时迅速消失。之后我们把view添加到container并把它放置在from view controller的view后面以保证其不可视。

在动画块中，我们为`from view`的尺寸制作动画效果，使其宽和高为0并保持居中。这样我们就可以得到一个将`from view`缩减至无的效果。我们还会把`to view`的alpha进行动态化以便使其完全可视。

添加以下属性至ItemsTableViewController中。

```
let customDismissAnimationController = CustomDismissAnimationController()
```

添加以下功能至类中。

```
func animationControllerForDismissedController(dismissed: UIViewController) -> UIViewControllerAnimatedTransitioning? {
    return customDismissAnimationController
}
```

`UIViewControllerTransitioningDelegate`协议提供了以上功能，把一个解散的view controller中的animation controller收回。

运行app我们可以看到以下效果。

![pic-7](http://www.appcoda.com/wp-content/uploads/2015/03/vid05.gif)

这个动画效果并不是我们预期的。大家可以看到，from view的白色框架如我们所预期的缩小了，但是view中的image的尺寸并没有变化。这是因为view的边框改变并不影响其子框架。那么我们将使用UIView snapshotting来修复。

UIView snapshotting的工作原理是通过给现有的UIView生成快照并将其放入一个轻量级UIView。之后我们会在淡出动画，而不是实际view中使用这张快照。

用以下代码替换`animateTransition()`功能。

```
func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
    let fromViewController = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey)!
    let toViewController = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey)!
    let finalFrameForVC = transitionContext.finalFrameForViewController(toViewController)
    let containerView = transitionContext.containerView()
    toViewController.view.frame = finalFrameForVC
    toViewController.view.alpha = 0.5
    containerView?.addSubview(toViewController.view)
    containerView?.sendSubviewToBack(toViewController.view)
        
    let snapshotView = fromViewController.view.snapshotViewAfterScreenUpdates(false)
    snapshotView.frame = fromViewController.view.frame
    containerView?.addSubview(snapshotView)
        
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

这里我们会创建一个`from view controller`的view的快照，将其添加到container并把container中的`from view`移出。之后我们在动画中缩小这张快照，动画完成后，将snapshot view从container中移出。


再次运行时动画应该会很顺畅了。

![pic-8](http://www.appcoda.com/wp-content/uploads/2015/03/vid06.gif)

##Navigation controller transitions

我们已经在modal view controller presentation中添加了一个自定义切换，这个自定义切换中的presenting view controller添加了transitioning delegate。但是，当我们使用`UITabBarController`或`UINavigationController`来工作时，每个view controller都设置一个delegate就显得非常麻烦。

这些controller提供了一个更简单的方法，通过这个方法transition的animationcontroller会通过`UITabBarControllerDelegate` 或 `UINavigationControllerDelegate`来提供。

在navigation controller中添加一个自定义切换后我们可以看到其运转状态。


首先，创建一个animation controller。创建一个名为`CustomNavigationAnimationController`的类，将其作为`NSObject`的子类并按下列方式更改其定义。

```
class CustomNavigationAnimationController: NSObject, UIViewControllerAnimatedTransitioning {
```

将以下代码添加到类中。对于这个animation controller，我本人使用的是一个简版的[this cube animation](https://github.com/andresbrun/ABCustomUINavigationController#cube)。我们只需要像前面提到的两个animation controller一样建立这个animation controller。需要注意逆类变量。我们要用它来决定动画的方向，取决于我们是否从主视图过渡到详细视图或从详细视图过渡到主视图。

```
var reverse: Bool = false
    
func transitionDuration(transitionContext: UIViewControllerContextTransitioning?) -> NSTimeInterval {
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
    containerView?.addSubview(toView)
        
    UIView.animateWithDuration(transitionDuration(transitionContext), animations: {
        containerView.transform = CGAffineTransformMakeTranslation(-direction * containerView.frame.size.width / 2.0, 0)
        fromView.layer.transform = viewFromTransform
        toView.layer.transform = CATransform3DIdentity
    }, completion: {
        finished in
        containerView?.transform = CGAffineTransformIdentity
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

打开ItemsTableViewController并按照以下内容修改类定义。

```
class ItemsTableViewController: UITableViewController, UIViewControllerTransitioningDelegate, UINavigationControllerDelegate {
```


`UINavigationControllerDelegate`提供animation controller。


将以下属性添加到类中。

```
let customNavigationAnimationController = CustomNavigationAnimationController()
```

在`viewDidLoad()`末尾添加以下代码。

```
navigationController?.delegate = self
```

以上步骤设置了host navigation controller的delegate以便新的transition delegate方法能够被收到。


然后将以下代码添加到类中。

```
func navigationController(navigationController: UINavigationController, animationControllerForOperation operation: UINavigationControllerOperation, fromViewController fromVC: UIViewController, toViewController toVC: UIViewController) -> UIViewControllerAnimatedTransitioning? {
    customNavigationAnimationController.reverse = operation == .Pop
    return customNavigationAnimationController
}
```

以上功能称之为请求animation controller在from view controller和to view controller之间进行导航，它会返回animation controller的实例。切换的方向取决于这是一个push navigation还是一个pop navigation操作。

运行app。选择一个table view cell后大家看到的动画效果如下。

![pic-9](http://www.appcoda.com/wp-content/uploads/2015/03/vid07.gif)

##使其更具交互式特点

接下来我们将使以上切换更具交互式特点，也就是说，用户能够通过手势控制切换。

iOS内置app具有这个特点。我们可以从屏幕左侧滑动来发起切换，代替后退按钮的功能。大家可以进行短暂滑动以下来简单查看主视图，然后取消切换。滑动距离稍长就会触发pop navigation操作。

![pic-10](http://www.appcoda.com/wp-content/uploads/2015/03/vid08.gif)

首先，我们需要一个interaction controller。交互式controller使用`UIViewControllerInteractiveTransitioning`协议。navigation controller delegate 或 transitioning delegate在请求animation controller后会要求一个可选的interaction controller。

我们来创建一个interaction controller。创建一个新的类，取名为`CustomInteractionController`并使其作为`UIPercentDrivenInteractiveTransition`的子类。

`UIPercentDrivenInteractiveTransition` implements the `UIViewControllerInteractiveTransitioning` protocol so we wont have to add that to our class.

由于`UIPercentDrivenInteractiveTransition` 实现 `UIViewControllerInteractiveTransitioning`协议，所以我们不需要添加到类中。

要想使用`UIPercentDrivenInteractiveTransition`，animation controller必须使用单个UIView动画，以便动画能够被停止、翻转及使用。


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

`attachToViewController()`方法获得navigation controller在发生手势操作时用来发起pop transition的引用。下一步，我们设立一个手势识别器，滑动操作时识别器会调用`handlePanGesture()`方法。这检查了手势识别器的状态并在每一个阶段执行下列操作：

* 开始：把`transitionInProgress` 设为真，并发起pop navigation。
* 更改：此时手势正在进行，所以它决定了切换的百分比。一个200点滑动会是切换达到100%。之后它将通过手势停止的地方来判断切换是否完成。此时我们要确定用户是否在释放前滑到了屏幕的一半处。
* 取消/结束：如果`shouldCompleteTransition`被设为假或者手势被取消，把`transitionInProgress`设为假，并取消切换。否则，切换完成。

我们使用计算属性来确定完成速度。`completionSeed`具有`UIPercentDrivenInteractiveTransition`属性，它会在手势完成时通知框架还剩多少动画。如果互动取消，数字更大会使view controller更迅速返回。

打开ItemsTableViewController.swift 并把下列代码添加到类中之后，大家就可以使用我们的interaction controller了。

```
let customInteractionController = CustomInteractionController()
```

在`navigationController(_:animationControllerForOperation:
fromViewController:toViewController:)`功能起始处添加以下代码。

```
if operation == .Push {
    customInteractionController.attachToViewController(toVC)
}
```

它会调用CustomInteractionController的`attachToViewController()`方法并在发现push navigation操作时给这个方法传递to view controller的一个引用。

然后添加以下代码到类中。

```
func navigationController(navigationController: UINavigationController, interactionControllerForAnimationController animationController: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
    return customInteractionController.transitionInProgress ? customInteractionController : nil
}
```

框架请求并获得animation controller后，它会使用以上方法请求interaction controller。如果切换正在进行，那么将会返回interaction controller的一个实例。


运行app后我们将看到如图所示的切换。

![pic-11](http://www.appcoda.com/wp-content/uploads/2015/03/vid09.gif)


## 总结

大家已经知道如何创建自定义view controller切换以及如何使切换具有交互性。有了这些特性之后，开发者可以完全掌控app中从一个view切换到另一个view时使用的动画，由此带给用户更好的体验。大家可以在[此处下载项目](https://github.com/appcoda/CustomViewTransitionDemo)。


