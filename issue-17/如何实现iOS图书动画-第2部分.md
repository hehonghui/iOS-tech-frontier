> * 原文链接 : [How to Create an iOS Book Open Animation: Part 2](http://www.raywenderlich.com/97690/how-to-create-an-ios-book-open-animation-part-2)
* 原文作者 : [Vincent Ngo](http://www.raywenderlich.com/u/jomoka)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [kmyhy](https://github.com/kmyhy) 
* 校对者: 这里校对者的github用户名
* 状态 : 校对中

Welcome back to our iOS book open animation tutorial series!
In the first part of this tutorial series, you learned how to create two custom collection view layouts and applied shadow layers to the book’s pages to create depth and realism in your app.
In this final part, you’ll learn to create custom navigation transitions and apply interactive gestures to open a book with a pinch gesture.

欢迎回到iOS图书动画系列教程！在第一部分，我们学习了如何创建两个自定义的collection view layout并在图书书页中使用了阴影图层以使我们的App显得更加立体和真实。
在这一部分，我们将学习如何创建自定义的转场动画并通过捏放手势来打开一本书。

Note: Full credit goes to Attila Hegedüs for creating this awesome sample project.



> 注意：感谢[Attila Hegedüs](https://twitter.com/hegedus90)创建了本教程的示例程序。

##Getting Started
##开始
The tutorial picks up from Part 1. If you didn’t work through the last part, or want to start afresh, simply download the completed sample project from the previous tutorial.

本教程以前一部分的内容为基础。如果你还没有看过第一部分，或者想从一个新项目开始，你可以在[这里](http://cdn1.raywenderlich.com/wp-content/uploads/2015/05/Part-1-Paper-Completed.zip)下载上一部分教程中的完整示例程序。

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/VN2_Start.gif"/>

Open up the project in Xcode. Right now, when you select a book to read the open pages simply slide in from the right. This is the default transition behavior for a UINavigationController. But by the end of this tutorial, your custom transition will look like the following:

在Xcode中打开项目。现在，你可以选择一本书进行阅读，并从右边滑动进行翻页。这时的转场动画使用的是UINavigationController自带的动画效果。通过本教程的学习，我们将自定义这个动画效果，如下图所示：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/VN_BookOpening.gif"/>

The custom transition will animate the book smoothly between the closed and opened states in a natural manner that users will love.
Time to get started!

这个动画会在“打开书”和“合起书”两个状态之间一一种更加自然的方式平滑过渡，这将更能获得用户的欢心。让我们马上开始吧！

##Creating your Custom Navigation Controller
##创建自定义的导航控制器
To create a custom transition on a push or pop you must create a custom navigation controller and implement the UINavigationControllerDelegate protocol.
Right-click (or Ctrl-click) on the App group and click New File. Select the iOS\Source\Cocoa Touch Class template and name the new file CustomNavigationController. Make sure it’s a subclass of UINavigationController and set the language to Swift. Click Next and then Create.
Open CustomNavigationController.swift and replace its contents with the following:

要创建自定义的push动画和pop动画，我们必须创建自定义导航控制器并实现UINavigationControllerDelegate协议。
在App文件夹上右击（或ctrl+左键）并点击New File。选择iOS\Source\Cocoa Touch Class模板并将类名设置为CustomNavigationController。让它继承自UINavigationController并将语言设置为Swift。点击Next，Create。
打开CustomNavigationController.swift，编辑其内容为：

```
import UIKit
 
class CustomNavigationController: UINavigationController, UINavigationControllerDelegate {
 
  override func viewDidLoad() {
    super.viewDidLoad()
    //1
    delegate = self
  }
 
  //2
  func navigationController(navigationController: UINavigationController, animationControllerForOperation operation: UINavigationControllerOperation, fromViewController fromVC: UIViewController, toViewController toVC: UIViewController) -> UIViewControllerAnimatedTransitioning? {
    if operation == .Push {
      return nil
    }
 
    if operation == .Pop {
      return nil
    }
 
    return nil
  }
}
```

Here’s what you’re doing in the code above:

上述代码分别解释如下：

1. In viewDidLoad you set the navigation controller as its own delegate.
2. navigationController(_:animationControllerForOperation:fromViewController:toViewController:) is one of the methods you can implement for UINavigationControllerDelegate. This method executes each time you push or pop between view controllers, and you control which animated transition you return from this method. The code currently returns nil which defaults to the standard transition. You’ll replace it with your own custom transition object shortly.

---

1. 在 viewDidLoad 方法中，设置CustomNavigationController的delegate属性为它自己。
2. navigationController(_:animationControllerForOperation:fromViewController:toViewController:) 方法属于UINavigationControllerDelegate协议。这个方法在两个View Controller之间发生push或pop导航时调用。你可以在这个方法中分别针对push导航和pop导航返回各自的Transition对象。目前我们都返回了nil，这表明我们将使用UINavigationController内置的标准Transition。稍后我们将替换为自己的Transition对象。

Now that you have your custom navigation controller set up, it’s time to replace the default navigation controller in storyboard.
Open Main.storyboard and click Navigation Controller in the storyboard’s view hierarchy on the left. Next, click the Identity Inspector and under Custom Class, change UINavigationController to CustomNavigationController, as shown below:

现在我们拥有了自己的Navigation Controller类，接下来在故事板中将默认的UINavigationController替换为我们的CustomNavigationController。
打开Main.storyboard，在故事板编辑器左侧的对象窗口中选择Navigation Controller对象，打开Identity窗口，在Custom Class下，将Class由UINavigationController修改为CustomNavigationController，如下图所示：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/VN_storyboard2.png"/>

Build and run to ensure everything still works; nothing will have changed since you’re returning nil in your delegate method, which defaults to the navigation controller’s standard transition.

编译运行，什么变化都没有发生。这是因为在委托方法中我们仍然返回了nil，因此使用的仍然是UINavigationController内置的标准Transition。

##Creating the Custom Transition
##创建自定义导航动画

Time for the fun part — building your custom transition object! :]
With a custom transition object, the class you create must conform to the UIViewControllerAnimatedTransitioning protocol, and in particular, the methods below:

 - transitionDuration: Required. Returns the duration of the animation
   and synchronizes interactive transitions.
 - animateTransition: Required. Provides the to and from controllers you’re transitioning between. Most of the heavy lifting will be done in this method.
 - animationEnded: Optional. Informs you when the transition has finished. You can perform any required cleanup in this method.

最有趣的部分来了——创建我们的自定义Transition对象!:]
要自定义Transition类，我们必须实现UIViewControllerAnimatedTransitioning协议，最主要的是这几个方法：

 - transitionDuration: 必须实现。这个方法返回一个动画时长，使两个动画的播放时间相同（或不同）。
 - animateTransition: 必须实现。这个方法负责提供参与动画的两个控制器：一个to控制器，一个from控制器。Transiton的大部分工作在这个方法中完成。
 - animationEnded: 可选的方法。这个方法主要是用来通知你什么时候动画完成了。我们可以在这个方法中进行必要的清理动作。

##Setting up Your Transition
##创建自定义Transition类
Right-click (or Ctrl-click) on the App group and click New File. Select the iOS\Source\Cocoa Touch Class template and name the new file BookOpeningTransition. Make sure it’s a subclass of NSObject and set the language to Swift. Click Next and then Create.
Open BookOpeningTransition.swift and replace its contents with the following:

在App文件夹上右击（或ctrl+左键），然后点击New File。选择iOS\Source\Coca Touch Class 模板，将类名设置为BookOpeningTransition，继承NSObject，语言Swift。然后点击Next，Create。
打开BookOpeningTransition.swift，编辑代码如下：

```
import UIKit
 
//1
class BookOpeningTransition: NSObject, UIViewControllerAnimatedTransitioning {
 
  // MARK: Stored properties
  var transforms = [UICollectionViewCell: CATransform3D]() //2
  var toViewBackgroundColor: UIColor? //3
  var isPush = true //4
 
  //5
  // MARK: UIViewControllerAnimatedTransitioning
  func transitionDuration(transitionContext: UIViewControllerContextTransitioning) -> NSTimeInterval {
    return 1
  }
 
  func animateTransition(transitionContext: UIViewControllerContextTransitioning) {
 
  }
}
```

Taking each commented section in turn:

1. BookOpeningTransition implements the required methods for the UIViewControllerAnimatedTransitioning protocol.
2. The dictionary transforms stores key value pairs, where the key is a UICollectionViewCell and the value is of type CATransform3D. This dictionary tracks each cell’s page transform when the book is open.
3. This defines the color you transition to, which helps the fade look much cleaner.
4. The boolean isPush determines whether the transition is a push, or a pop,
5. Here you add the required methods for UIViewControllerAnimatedTransitioning to avoid build errors; you’ll implement these methods shortly.

以上代码对应注释中的编号，分别解释如下：

1. 声明 BookOpeningTransition 类实现UIViewControllerAnimatedTransitioning 协议。
2. 声明一个 transforms 字典，键存储UICollectionViewCell，值则存储对应的CATransiform3D。这个字典保存在书打开后所有cell的翻页动画。
3. 指定to视图控制器的背景色，它将让淡出淡入动画看起来更加清楚。
4. 布尔值isPush 用于标识当前动画是Push动画还是Pop动画。
5. 增加必须实现的 UIViewControllerAnimatedTransitioning 协议方法，以使编译错误不再出现，稍后我们会实现这些方法。

Now that you have your variables set up, it’s time to implement the protocol methods.
Replace the contents of transitionDuration(_:) with the following:

定义好需要的变量，接下来就是实现协议方法。
首先是transitionDuration(_:)方法：

```
if isPush {
  return 1
} else {
  return 1
}
```

transitionDuration(_:) returns the duration of the transition animation. In this case, you want it to take 1 second on either a push or a pop. Writing the method this way lets you easily change the timing of the push or pop.

transitionDuration(_:)方法返回了动画播放时长。这里，无论是Push动画还是Pop动画我们都设置为1秒。通过这个方法我们可以很方便地改变Push动画或Pop动画的时长。

Next, you need to implement the second required protocol method — animateTransition — where the magic will happen! :] You’ll implement this in two parts:

1. Implement the helper methods to set up animateTransition for a push.
2. Implement the helper methods to set up animateTransition for a pop.

然后，是第二个协议方法——animateTransition——这是最核心的部分！:]我们将这个方法分成两部分来介绍：

1. 实现一个助手方法，用于创建Push动画所需的Transition对象。
2. 实现一个助手方法，用于创建Pop动画所需的Transition对象。

##Creating the Push Transition
##创建Push动画

Imagine yourself opening a book in real life:

回想你在生活中打开一本书的样子：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/VN_PushStage.png"/>

Although it looks complicated, you only need to consider the two states of your animation and let UIView‘s method animateWithDuration handle the animation between the following two states:

1. Stage 1 is when the book is closed.
2. Stage 2 is when the book is open; this is essentially the transform you created in Part 1 of this tutorial.

虽然看起来复杂，但我们只需要考虑两个状态，同时让UIView的animateWithDuration方法根据这两个状态进行不同的处理：

1. 状态1，书处于合起状态。
2. 状态2，书处于打开状态；这就是我们在第一部分教程中实现的部分。

First, you’ll implement some helper methods to handle the two states before you implement the animateTransition(_:) protocol method.
Still in BookOpeningTransition.swift, add the following code to the end of the class:

首先，在实现animateTransition(:_)协议方法之前，我们来实现几个助手方法。
仍然在BookOpeningTransition.swift中，编写如下方法：

```
// MARK: Helper Methods
func makePerspectiveTransform() -> CATransform3D {
  var transform = CATransform3DIdentity
  transform.m34 = 1.0 / -2000
  return transform
}
```

This code returns a transform and adds perspective in the z-axis. You’ll use this later to help transform your views during the animation.

这个方法返回了一个Transform对象，并在z轴上增加了一点立体感。在播放动画时，我们将用到这个方法。

###State 1 – Closed Book
###状态 1 - 合起书
Next, add the following code just after makePerspectiveTransform:

在makePerspectiveTransform方法后实现如下方法：

func closePageCell(cell : BookPageCell) {
  // 1
  var transform = self.makePerspectiveTransform()
  // 2
  if cell.layer.anchorPoint.x == 0 {
    // 3
    transform = CATransform3DRotate(transform, CGFloat(0), 0, 1, 0)
    // 4
    transform = CATransform3DTranslate(transform, -0.7 * cell.layer.bounds.width / 2, 0, 0)
    // 5
    transform = CATransform3DScale(transform, 0.7, 0.7, 1)
   }
   // 6
   else {
     // 7
     transform = CATransform3DRotate(transform, CGFloat(-M_PI), 0, 1, 0)
     // 8
     transform = CATransform3DTranslate(transform, 0.7 * cell.layer.bounds.width / 2, 0, 0)
     // 9
     transform = CATransform3DScale(transform, 0.7, 0.7, 1)
    }
 
    //10
    cell.layer.transform = transform
}
Recall that the BookViewController is a collection view of pages. You transformed every page to align to the book’s spine, and rotated it on an axis to achieve the page flipping effect. Initially, you want the book to be closed. This method transitions every cell (or page) to be flat and fit behind the book’s cover.
Here’s a quick illustration of the transform:

回想一下BookViewController，它是一个CollectionView，代表了书中的一页。我们将每一页和书脊对齐，以y轴为心进行旋转实现翻页效果。首先，书是合起（关闭）的。这个方法将每个cell（即书页）放平并置于封面的下面。
这是动画运行效果：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/VN2_ClosedState.png"/>

Here’s an explanation of the code that makes that happen:

1. Initialize a new transform using the helper method you created earlier.
2. Check that the cell is a right-hand page.
3. If it’s a right-hand page, set its angle to 0 to make it flat.
4. Shift the page be centered behind the cover.
5. Scale the page on the x and y axes by 0.7. Recall that you scaled the book covers to 0.7 in the previous tutorial, in case you wondered where this magic number came from.
6. If the cell isn’t a right-hand page, then it must be a left-hand page.
7. Set the left-hand page’s angle to 180. Since you want the page to be flat, you need to flip it over to the right side of the spine.
8. Shift the page to be centered behind the cover.
9. Scale the pages back to 0.7.
10. Finally, set the cell’s transform.

以上代码解释如下：

1. 用前面创建的助手方法，生成一个Transform对象。
2. 判断cell是否是右侧页。
3. 如果是，设置其角度为0，即放置为水平。
4. 将它移动到封面下方并居中对齐。
5. 将它缩放为70%。还记得我们前面将封面也缩放为70%吗？这里是同样的意思。
6. 如果cell不是右侧页，则就是左侧页。
7. 设置左侧页的角度为180度。要将它水平放置，我们需要将它翻到书脊的右边。
8. 将它放到封面下方并居中对齐。
9. 缩放70%。
10. 最后，赋给cell的transform属性。

Now add the following method below the one you added above:

在上面的方法后添加如下方法：

```
func setStartPositionForPush(fromVC: BooksViewController, toVC: BookViewController) {
  // 1
  toViewBackgroundColor = fromVC.collectionView?.backgroundColor
  toVC.collectionView?.backgroundColor = nil
 
  //2
  fromVC.selectedCell()?.alpha = 0
 
  //3
  for cell in toVC.collectionView!.visibleCells() as! [BookPageCell] {
    //4
    transforms[cell] = cell.layer.transform
    //5
    closePageCell(cell)
    cell.updateShadowLayer()
    //6
    if let indexPath = toVC.collectionView?.indexPathForCell(cell) {
      if indexPath.row == 0 {
        cell.shadowLayer.opacity = 0
      }
    }
  }
}
```

setStartPositionForPush(_:toVC:) sets up stage 1 of the transition. It takes in two view controllers to animate:

- fromVC, of type BooksViewController, lets you scroll through your list of books.
- toVC, of type BookViewController, lets you flip through the pages of the book you selected.

setStartPositionForPush(_:toVC:)方法创建状态1的Transition。这个动画涉及到两个ViewController：

- fromVC，类型为BooksViewController，用于滚动浏览图书列表。

- toVC，BookViewController类型，让你可以翻阅选定的书。

Here’s what’s going on in the code above:

1. Store the background color of BooksViewController‘s collection view and set BookViewController‘s collection view background to nil.
2. Hide the selected book cover. toVC will now handle the display of the cover image.
3. Loop through the pages of the book.
4. Save the current transform of each page in its opened state.
5. Since the book starts from a closed state, you transform the pages to closed and update the shadow layer.
6. Finally, ignore the shadow of the cover image.

以上代码解释如下：

1. 保存BooksViewController的Cellection View的背景色，然后设置BookViewController的Collection View的背景色为nil。
2. 隐藏封面。现在toVC将负责处理封面图片的显示。
3. 遍历书中所有书页。
4. 保存每一页的当前Transform到transforms字典。
5. 由于书一开始是合起的，我们将该页转换为合起状态，然后更新阴影图层。
6. 最后，忽略封面图片的阴影。

###State 2 – Opened Book
###状态 2 - 打开书
Now that you’ve finished state 1 of the transitions, you can move on to state 2, where you go from a closed book to an opened book.
Add the following method below setStartPositionForPush(_:toVC:)):

现在，状态1的动画完成了，我们可以转移到状态2的处理中来。在这里我们将一本合起的书转换成一本打开的书。在setStartPositionForPush(_:toVC:)方法下添加如下方法：

```
func setEndPositionForPush(fromVC: BooksViewController, toVC: BookViewController) {
  //1
  for cell in fromVC.collectionView!.visibleCells() as! [BookCoverCell] {
    cell.alpha = 0
  }
 
  //2
  for cell in toVC.collectionView!.visibleCells() as! [BookPageCell] {
    cell.layer.transform = transforms[cell]!
    cell.updateShadowLayer(animated: true)
  }
}
```

Digging into the code above:

1. Hide all the book covers, since you’re presenting the selected book’s pages.
2. Go through the pages of the selected book in BookViewController and load the previously saved open transforms.

上述代码解释如下：

1. 隐藏所有书的封面，因为接下来我们要显示所选图书的内容。
2. 在BookViewController中遍历书中每一页并读取先前保存在transform数组中的的Transform。


After you push from BooksViewController to BookViewController, there’s a bit of cleanup to do.
Add the following method just after the one you added above:

在从BooksViewController导航到BookViewController后，我们还需要进行一些清理工作。
在上面的方法之后加入如下方法：
```
func cleanupPush(fromVC: BooksViewController, toVC: BookViewController) {
  // Add background back to pushed view controller
  toVC.collectionView?.backgroundColor = toViewBackgroundColor
}
```
Once the push is complete, you simply set the background color of BookViewController‘s collection view to the background color you saved earlier, hiding everything behind it.

在Push完成时，我们将BookViewController的Collection View的背景色设回原来保存的颜色，隐藏位于它下面的内容。

###Implementing the Book Opening Transition
###实现打开书的动画
Now that you have your helper methods in place, you’re ready to implement the push animation! Add the following code to the empty implementation of animateTransition(_:):

现在我们已经实现了助手方法，接下来要实现Push动画了！
在空的animateTransition(_:)方法中加入以下代码：

```
//1
let container = transitionContext.containerView()
//2
if isPush {
  //3
  let fromVC = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey) as! BooksViewController
  let toVC = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey) as! BookViewController
  //4
  container.addSubview(toVC.view)
 
  // Perform transition
  //5
  self.setStartPositionForPush(fromVC, toVC: toVC)
 
  UIView.animateWithDuration(self.transitionDuration(transitionContext), delay: 0.0, usingSpringWithDamping: 0.7, initialSpringVelocity: 0.7, options: nil, animations: {
    //6
    self.setEndPositionForPush(fromVC, toVC: toVC)
    }, completion: { finished in
      //7
      self.cleanupPush(fromVC, toVC: toVC)
      //8
      transitionContext.completeTransition(finished)
  })
} else {
  //POP
}
```

Here’s what’s happening in animateTransition(_:):

1. Get the container view, which acts as the superview between the transitioning view controllers.
2. Check that you’re performing a push.
3. If so, get both fromVC (BooksViewController) and toVC (BookViewController).
4. Add toVC (the BookViewController) to the containing view.
5. Set up the starting positions for the to and from view controllers for the closed state.
6. Next, you animate from the starting position (Closed State) to the ending position (Opened State)
7. Perform any cleanup.
8. Notify the system that the transition is complete.

以上代码解释如下：

1. 获取Container View，Container View在两个View Controller发生转场时充当父视图的角色。
2. 判断当前的转场动作是否是一个Push动作。
3. 如果是，分别获取fromVC（BooksViewController）和toVC（BookViewController)。
4. 将toVC（BookViewController）加到Container View。
5. 设定Push动作的起止点，即toVC和fromVC。
6. 开始动画。从起始点（书合起的状态）转变到终点（书打开状态）。
7. 执行清理动作。
8. 告诉系统，转换完成。

##Applying the Push Transition to the Navigation Controller
##在Navigation Controller中应用Push动画
Now that you have your push transition set up, it’s time to apply it to your custom navigation controller.
Open BooksViewController.swift and add the following property just after the class declaration:

现在我们已经创建好Push动画，接下来就是将它应用到自定义的Navigation Controller中了。
打开BooksViewController.swift在类声明中增加属性：
```
var transition: BookOpeningTransition?
```

This property keeps track of your transition, letting you know whether the transition is a push or pop.
Next add the following extension after the ending curly brace:

transition属性用于保存Transition对象，通过它我们可以知道当前动画是Push动画还是Pop动画。
然后在文件末尾的大括号之后加入一个扩展：

```
extension BooksViewController {
func animationControllerForPresentController(vc: UIViewController) -> UIViewControllerAnimatedTransitioning? {
  // 1
  var transition = BookOpeningTransition()
  // 2
  transition.isPush = true
  // 3
  self.transition = transition
  // 4
  return transition
  }
}
```

This creates an extension to separate parts of the code’s logic. In this case, you want to group methods related to transitions in one place. This method sets up the transition object and returns it as well.
Taking a closer look at the code:

1. Create a new transition.
2. Since you are presenting the controller, or pushing, set isPush to true.
3. Save the current transition.
4. Return the transition.

通过扩展，我们将一部分代码分离出来。这里，我们将和转换动画有关的方法放到了一起。上面的这个方法创建并返回了一个Transition对象。
以上代码解释如下：

1. 创建一个新的Transition。
2. 因为我们是要弹出或者Push一个Controller，所以将isPush设置为true。
3. 保存当前Transition对象。
4. 返回Transition对象。

Now open CustomNavigationController.swift and replace the push if statement with the following:

现在打开CustomNavigationController.swift并将Push的if语句替换为：

```
if operation == .Push {
  if let vc = fromVC as? BooksViewController {
    return vc.animationControllerForPresentController(toVC)
  }
}
```

This checks that the view controller you’re pushing from is a BooksViewController, and presents BookViewController with the transition you created: BookOpeningTransition.
Build and run your app; click on a book of your choice and you’ll see the book animate smoothly from closed to opened:

上述语句判断当前Push的View Controller是不是一个BooksViewController，如果是，用我们创建的BookOpeningTransition呈现BookViewController。
编译运行，选择某本书，你将看到书缓缓由合起状态打开：
<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/VN_PushGlitch.gif"/>

Uh..how come it’s not animating?
呃...我们的动画效果呢？
<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/angry-desk-flip.png"/>

It’s jumping straight from a closed book to an opened book because you haven’t loaded the pages’ cells!

书直接从合起状态跳到了打开状态，原因在于我们没有加载cell（书页）！

The navigation controller transitions from BooksViewController to BookViewController, which are both UICollectionViewControllers. UICollectionView cells don’t load on the main thread, so your code sees zero cells at the start — and thinks there’s nothing to animate!

导航控制器从BooksViewController切换到BookViewController，这二者都是UICollecitonViewController。UICollectionViewCell没有在主线程中加载，因此代码一开始的时候以为cell的个数为0——这样当然不会有动画产生。

You need to give the collection view enough time to load all the cells.
Open BooksViewController.swift and replace openBook(_:) with the following:

我们需要让Collection View有足够的时间去加载所有的Cell。
打开BooksViewController.swift将openBook(_:)方法替换为：

```
func openBook(book: Book?) {
  let vc = storyboard?.instantiateViewControllerWithIdentifier("BookViewController") as! BookViewController
  vc.book = selectedCell()?.book
  //1
  vc.view.snapshotViewAfterScreenUpdates(true)
  //2
  dispatch_async(dispatch_get_main_queue(), { () -> Void in
    self.navigationController?.pushViewController(vc, animated: true)
    return
  })
}
```

Here’s how you solved the problem:

1. You tell BookViewController to create a snapshot after the changes have been incorporated.
2. Make sure you push BookViewController on the main thread to give the cells time to load.

以上代码解释如下：

1. 告诉BookViewController在动画一开始之前截屏。
2. 将Push BookViewController的动作放到主线程中进行，这样就有时间去加载cell了。

Build and run your app again; you should see the book animate properly on a push:

编译、运行，这次你将看到正确的Push动画了：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/VN_PushGlitchAnimate.gif"/>

That looks much better! :]
Now that you’re done with the push transition, you can move on to the pop transition.

这样看起来是不是好多啦？
现在，关于Push动画的内容就到此结束，接下来，我们开始实现Pop动画。
###Implementing the Pop Helper Methods
###实现Pop动画的助手方法
Popping the view controller is pretty much the opposite of a push. Stage 1 is now the open book state, and Stage 2 is now the closed book state:

一个View Controller的Pop动作刚好和Push相反。状态1是图书打开的状态，而状态2则变成了书合起的状态：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/VN_PopState.png"/>
Open up BookOpeningTransition.swift and add the following code:

打开BookOpeningTransition.swift，加入以下方法：

```
// MARK: Pop methods
func setStartPositionForPop(fromVC: BookViewController, toVC: BooksViewController) {
  // Remove background from the pushed view controller
  toViewBackgroundColor = fromVC.collectionView?.backgroundColor
  fromVC.collectionView?.backgroundColor = nil
}
```

setStartPositionForPop(_:toVC) only stores the background color of BookViewController and removes the background color of BooksViewController‘s collection view. Note that you don’t need to set up any cell transforms, since the book is currently in its opened state.

setStartPositionForPop(_:toVC)方法仅仅是保存BookViewController的背景色并将BooksViewController的Collection View的背景色删除。注意，你不需要创建任何cell动画，因为书在这个时候是打开状态。

Next, add the following code for setEndPositionForPop(_:toVC) immediately after the code you just added above:

接着，在上面的方法后面加入这个方法：

```
func setEndPositionForPop(fromVC: BookViewController, toVC: BooksViewController) {
  //1
  let coverCell = toVC.selectedCell()
  //2
  for cell in toVC.collectionView!.visibleCells() as! [BookCoverCell] {
    if cell != coverCell {
      cell.alpha = 1
    }
  }      
  //3
  for cell in fromVC.collectionView!.visibleCells() as! [BookPageCell] {
    closePageCell(cell)
  }
}
```

This method sets up the end state of the pop transition where the book goes from opened to closed:

1. Get the selected book cover.
2. In the closed book state, loop through all the book covers in BooksViewController and fade them all back in.
3. Loop through all the pages of the current book in BookViewController and transform the cells to a closed state.

这个方法创建Pop动画的起止点，即从打开变成合起：

1. 获取选择的书的封面。
2. 在合起状态，在BooksViewController中遍历私有书的封面，然后对所有对象进行一个渐入效果。
3. 在BookViewController中遍历当前图书的所有页，将所有cell转变成合起状态。

Now add the following method:

现在新建如下方法：

```
func cleanupPop(fromVC: BookViewController, toVC: BooksViewController) {
  // Add background back to pushed view controller
  fromVC.collectionView?.backgroundColor = self.toViewBackgroundColor
  // Unhide the original book cover
  toVC.selectedCell()?.alpha = 1
}
```

This method performs some cleanup once the pop transition has finished. The cleanup process sets BooksViewController‘s collection view background to its original state and displays the original book cover.

这个方法在Pop动画完成时执行清理动作：将BooksViewController的Collection View的背景色设回它开始的值并显示封面。

Now add the following code within the protocol method animateTransition(_:) inside the else block of the code with the //POP comment:

在animateTransition(_:)方法里面，找到注释有“//POP”的else语句块，添加如下代码：

```
//1
let fromVC = transitionContext.viewControllerForKey(UITransitionContextFromViewControllerKey) as! BookViewController
let toVC = transitionContext.viewControllerForKey(UITransitionContextToViewControllerKey) as! BooksViewController
 
//2
container.insertSubview(toVC.view, belowSubview: fromVC.view)
 
//3
setStartPositionForPop(fromVC, toVC: toVC)
UIView.animateWithDuration(self.transitionDuration(transitionContext), animations: {
  //4
  self.setEndPositionForPop(fromVC, toVC: toVC)
}, completion: { finished in
  //5
  self.cleanupPop(fromVC, toVC: toVC)
  //6
  transitionContext.completeTransition(finished)
})
```

Here’s how the pop transition animation works:

1. Grab the view controllers involved in the transition. fromVC is now BookViewController (the opened book state) and toVC is now the BooksViewController (closed book state).
2. Add BooksViewController below BookViewController within the container view.
3. setStartPositionForPop(_:toVC) stores the background color before setting it to nil.
4. Animate from the opened book state to the closed book state.
5. Clean up the view controller once the animation is done by setting the background color back to it’s original color and showing the book cover.
6. Notify the transition is complete.

以上代码解释如下：

1. 获取动画中涉及的两个ViewController。现在，fromVC 是BookViewController （打开状态），toVC是BooksViewController（合起状态）。
2. 向Container View中加入BooksViewController(在BookViewContorller的下方)。
3. setStartPositionForPop(_:toVC) 方法先保存背景色，再将背景色设为nil。
4. 执行动画，即从打开状态切换到合起状态。
5. 动画完成，执行清理动作。将背景色设回原来值，显示封面。
6. 通知动画完成。

##Applying the Pop Transition to the Navigation Controller
##在Navigation Controller中应用Pop动画
Now you need to set up the pop transition just as you did with the push transition.
Open BooksViewController.swift and add the following method right after animationControllerForPresentController(_:):

现在需要创建Pop动画，就如同我们在Push动画所做一样。
打开BooksViewController.swift，在animationControllerForPresentController(_:)方法后增加如下方法：

```
func animationControllerForDismissController(vc: UIViewController) -> UIViewControllerAnimatedTransitioning? {
  var transition = BookOpeningTransition()
  transition.isPush = false
  self.transition = transition
  return transition
}
```

This again creates a new BookOpeningTransition, but the only difference is that the transition is now set to be a pop.
Now open CustomNavigationController.swift and replace the pop if statement with the following:

这里，我们创建了一个新的BookOpeningTransition对象，但不同的是isPush设置为false。
打开CustomNavigationController.swift，然后替换Pop部分的if语句为：

```
if operation == .Pop {
  if let vc = toVC as? BooksViewController {
    return vc.animationControllerForDismissController(vc)
  }
}
```

This returns the transition and performs the pop animation to close the book.
Build and run your app; select a book to see it open and close as shown below:

上述代码返回一个Transition对象，并执行Pop动画，合起书本。
编译，运行程序，选择一本书，查看它的打开和合起。如下图所示：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/VN_PushGlitch.gif"/>
##Creating an Interactive Navigation Controller
##创建互动式的Navigation Controller
The opening and closing transition animations look great — but you can still do better! You can apply intuitive gestures to pinch the book open or closed.
First, open BookOpeningTransition.swift and add the following property:

打开和合起动画搞定了——但我们还能更进一步！我们为什么不用一个更直观的捏放手势来打开和合起书本呢？
打开BookOpeningTransition.swift，增加如下属性定义：

```
// MARK: Interaction Controller
var interactionController: UIPercentDrivenInteractiveTransition?
```

Next open CustomNavigationController.swift and add the following code:
然后打开CustomNavigationController.swift，加入下列代码：


```
func navigationController(navigationController: UINavigationController, interactionControllerForAnimationController animationController: UIViewControllerAnimatedTransitioning) -> UIViewControllerInteractiveTransitioning? {
  if let animationController = animationController as? BookOpeningTransition {
    return animationController.interactionController
  }
  return nil
}
```

In the above method, you return the interactive animator object from BookOpeningTransition. This lets the navigation controller keep track of the progress of the animation so the user can interactively pinch a book opened or closed.

在这个方法中，我们从BookOpeningTransition对象获得了一个interactionController。这样导航控制器能够跟踪动画进程以便用户可以用捏放手势打开和合起书。

Now open BooksViewController.swift and add the following property under the transition variable:

打开BooksViewController.swift，在trnasitoin变量下增加如下属性：

```
//1
var interactionController: UIPercentDrivenInteractiveTransition?
//2
var recognizer: UIGestureRecognizer? {
  didSet {
    if let recognizer = recognizer {
      collectionView?.addGestureRecognizer(recognizer)
    }
  }
}
```

Here’s why you added these variables:

1. interactionController is of class type UIPercentDrivenInteractiveTransition, which manages the custom animation between the view controllers transitioning in and out. The interaction controller also depends on a transition animator, which is a custom object that implements the UIViewControllerAnimatorTransitioning protocol. You’ve created BookOpeningTransition — which does exactly that!
The iteractionController can control the progress between pushing and popping view controllers. To learn more about this class, read up on Apple’s documentation on how this works.
2. recognizer is a UIGestureRecognizer. You’ll use the gesture recognizer to pinch the book in and out.

这两个属性的作用分别是：

1. interactionController 是一个UIPercentDrivenInteractiveTransition类，它负责管理View Contorller之间转场的自定义动画。interactionController由一个Transition Animator生成，后者是一个实现了UIViewControllerAnimatorTransitioning协议的对象。而我们已经拥有了BookOpeningTransition——这就是一个实现了UIViewControllerAnimatorTransitioning的对象。interactionController能够控制Push动画和Pop动画之间的进度。关于这个类的更多内容，请参考Apple官方文档。
2. recognizer 是一个UIGestureRecognizer。我们用这个手势识别器实现以捏放手势开合书本。

Now add the following snippet under the transition.isPush = true line of your BooksViewController extension in animationControllerForPresentController(_:):

在BooksViewController扩展的animationControllerForPresentController(_:)方法中，transition.isPush=true一行下面，加入代码：

```
transition.interactionController = interactionController
```

This lets your custom navigation controller know which interaction controller to use.
Add the same code to animationControllerForDismissController(_:), under transition.isPush = false:

这句代码让CustomNavigationController知道要用哪个interaction controller。
在animationControllerForDismissController(_:)方法中transition.isPush=false一行下面加入同样的代码：

```
transition.interactionController = interactionController
```

Next, add the following code to viewDidLoad():

在viewDidLoad()方法中增加代码：

```
recognizer = UIPinchGestureRecognizer(target: self, action: "handlePinch:")
```

This initializes a UIPinchGestureRecognizer, which lets the user perform a pinch gesture with the action method handlePinch(_:).
Implement the action under viewDidLoad() like so:

这里我们初始化了一个UIPinchGestureRecognizer，允许用户在做出捏放手势时调用handlePinch(_:)方法。
在viewDidLoad()方法下面实现这个方法：

```
// MARK: Gesture recognizer action
func handlePinch(recognizer: UIPinchGestureRecognizer) {
  switch recognizer.state {
    case .Began:
      //1
      interactionController = UIPercentDrivenInteractiveTransition()
      //2
      if recognizer.scale >= 1 {
        //3
        if recognizer.view == collectionView {
          //4
          var book = self.selectedCell()?.book
          //5
          self.openBook(book)
        }
      //6
      } else {
        //7
        navigationController?.popViewControllerAnimated(true)
      }        
    case .Changed:
      //8
      if transition!.isPush {
        //9
        var progress = min(max(abs((recognizer.scale - 1)) / 5, 0), 1)
        //10
	interactionController?.updateInteractiveTransition(progress)
	//11
      } else {
        //12
	var progress = min(max(abs((1 - recognizer.scale)), 0), 1)
        //13
	interactionController?.updateInteractiveTransition(progress)
      } 
    case .Ended:
      //14
      interactionController?.finishInteractiveTransition()
      //15
      interactionController = nil
    default:
      break
  }
}
```

For the UIPinchGestureRecognizer, you’ll keep track of three different states. The state began lets you know when the pinch has started. The state changed detects changes to the pinch, and ended lets you know when the pinch has ended.
The rundown of your implementation of handlePinch(_:) code is below:

对于UIPinchGestureRecognizer，我们要关注这3个状态：开始状态，这让你知道捏放手势何时开始；改变状态，检测捏放手势的变化；结束状态，让你知道捏放手势何时结束。
handlePinch(_:)方法代码解释如下：

**Began State**
1. Instantiate a UIPercentDrivenInteractiveTransition object.
2. Check that the scale, which is dependent on the distance between the pinch points, is greater than or equal to 1.
3. If so, ensure that the view you involved in the gesture is indeed a collection view.
4. Grab the book being pinched.
5. Perform a push of BookViewController to show the pages of the book.
6. If the scale is less than 1…
7. …perform a pop of BookViewController to show the book cover again.
**Changed State – While Pinching**
8. Check to see if the current transition is performing a push.
9. If you’re pushing to BookViewController, obtain the progress of the user’s pinch gesture. progress must be between 0 and 1. You scale the pinch down to one-fifth of its original value; this gives the user more control over the transition. Otherwise pinching a book open would appear to jump to the opened state immediately.
10. Update the completed percentage of the transition based on the progress you calculated earlier.
11. If the current transition is not performing a push, then it must be performing a pop.
12. While pinching the book closed, the scale must progress from 1 to 0.
13. Finally, update the progress of the transition.
**End State – Stop Pinching**
14. Notify the system that the user interaction of the transition is complete.
15. Set the interaction controller to nil.

**开始状态**
1. 创建一个UIPercentDrivenInteractiveTransition 对象。
2. scale取决于捏合点之间的距离，判断scale值是否大于或者等于1。
3. 如果是，判断相关的View是否是一个Collection View。
4. 获取正在被捏合的书。
5. 执行Push BookViewController的动画，显示书本中的书页。
6. 如果 scale 小于 1…
7. …执行Pop BookViewController的动画，显示封面
**改变状态 – 捏合过程中**
8. 判断当前是否是Push动画。
9. 如果正在Push一个BookViewConroller，计算捏放手势的进度。该进度必然是0-1之间的数字。我们将原始值除以5以让用户拥有更好的控制感。否则用双指打开的手势打开一本书时，会突然跳到打开状态。
10. 基于我们计算的进度，更新动画进度。
11. 如果当前不是Push动画，则它应该是Pop动画。
12. 当双指捏合合起一本书时，scale值必然是从1慢慢变到0。
13. 最后, 更新动画进度。
**结束状态 – 手势终止**
14. 告诉系统，用户交互式动画完成。
15.将interaction controller 设置为 nil。

Finally, you need to implement the pinch-to-closed state. Therefore you have to pass the gesture recognizer to BookViewController so it can pop itself.
Open up BookViewController.swift, and add the following property under the book variable:

最后，我们需要实现“捏合以合起书本”的状态。当然，我们必须将手势识别器传递给BookViewController以便它会Pop。
打开BookViewController.swift，在book变量声明下增加一个属性：

```
var recognizer: UIGestureRecognizer? {
  didSet {
    if let recognizer = recognizer {
      collectionView?.addGestureRecognizer(recognizer)
    }
  }
}
```

Whenever you set the gesture recognizer in BookViewController, the gesture will be added immediately to the collection view so you can track the pinch gesture as the user closes the book.
Next you need to pass the gesture recognizer between the BooksViewController and BookViewController.
Open up BookOpeningTransition.swift. Add the following lines to cleanUpPush(_:toVC) after the point where you set the background color:

当我们将手势识别器传递给BookViewController时，它会被添加到Collection View，因此我们可以跟踪到用户的“关书”手势。
然后需要在BooksViewController和BookViewController之间传递手势识别器。
打开BookOpeningTransition.swift。在cleanUpPush(_:toVC)方法中，在设置背景色之后添加如下代码：

```
// Pass the gesture recognizer
toVC.recognizer = fromVC.recognizer
```

Once you’ve pushed from BooksViewController to the BookViewController, you pass the pinch gesture to BookViewController. This automatically adds the pinch gesture to the collection view.
When you pop from BookViewController to BooksViewController, you have to pass the pinch gesture back.
Add the following line to cleanUpPop(_:toVC), just after the line where you set the background color:

当我们从BooksViewController Push到BookViewController时，将捏放手势传递给BookViewController。这会导致捏放手势自动添加到Collection View中。
当我们从BookViewController Pop回BooksViewController时，我们必须将捏放手势又传递回去。
在cleanUpPop(_:toVC)方法中，在我设置背景色之后添加如下代码：

```
// Pass the gesture recognizer
toVC.recognizer = fromVC.recognizer
```

Build and run your app; select any book and use a pinch gesture to open and close the book:

编译、运行程序，选择一本书，用捏放手势打开和合起书：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/VN_Pinching.gif"/>
The pinch gesture is a natural mechanism to open and close a book; it is also an opportunity to clean up your interface. You don’t need that Back button in the navigation bar anymore — time to get rid of it.
Open Main.storyboard, select Custom Navigation View Controller, open the Attributes Inspector and uncheck Bar Visibility under the Navigation Controller section, like so:

捏放手势是一种天然就适合用于对书本进行“开关”的手势；它让我们的界面显得更加简单。我们不再需要导航栏的Back按钮——因此我们决定去掉它。
打开Main.storyboard，选择Custom Navigation View Controller，打开属性面板，在Navigation Controller一栏下面，取消Bar Visibility选项，如下所示：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/Screen-Shot-2015-03-14-at-1.16.36-PM.png"/>
Build and run your app again:

再次编译运行程序：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/VN_BookOpening.gif"/>
Much cleaner! :]
##Where To Go From Here?
##接下来做什么
You can download the final project from this part with all the code from the tutorial above.
In this tutorial, you learned how to apply custom layouts to collection views to give the user a more natural, and decidedly more interesting, experience with your app. You also created custom transitions and applied smart interactions to pinch a book opened and closed. Your app, while still solving the same basic problem, gives the application much more personality and helps it stand out from the rest.

你可以在[这里](http://cdn3.raywenderlich.com/wp-content/uploads/2015/05/Part-2-Paper-Completed_Final.zip)下载到上面所有步骤完成后的最终项目。
在本教程中，我们学习如何对Collection View进行自定义布局，让App的用户体验更加自然、也更加有趣。我们还创建了自定义动画，使用智能交互让用户以捏放手势开合一本书。这个App在实现了所有基本功能的同时，让程序显得更加的人性化和与众不同。

Is it easier to go with the default “ease-in/ease-out” animations? Well, you could save a little development time. But the best apps have that extra bit of customized polish that makes them shine. Everyone remembers the apps they downloaded that were a lot of fun to use; you know, the ones that gave you a little UI thrill, but without sacrificing utility.

相比较之下，是默认的“淡入/淡出”动画更简单一些。它能节省你一部分开发时间。但是杰出的应用程序都应当有一些自己特有的地方，从而使它们能够脱颖而出。
要知道，每个人都喜欢记住那些用起来非常有趣的App，在UI上能让人感到兴奋而同时又没有牺牲功能的App。

I hope you enjoyed this tutorial, and again I would like to thank Attila Hegedüs for creating this awesome project.
If you have any question about this tutorial, please join the forum discussion below!

希望你能喜欢本教程，再次感谢[Attila Hegedüs](https://twitter.com/hegedus90)提供了这个教程的示例项目。
如果对本教程有任何问题，请加入到下面的讨论中来！