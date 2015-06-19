## UIKit Dynamics 教程: 起步

> - 原文链接 : [UIKit Dynamics Tutorial: Getting Started](http://www.raywenderlich.com/76147/uikit-dynamics-tutorial-swift)
- 原文作者: [James Frost](http://www.raywenderlich.com/u/frosty)
- 译文出自: [开发技术前线http://www.devtf.cn/](http://www.devtf.cn/)
- 译者 : [Lollypo](https://github.com/Lollypo) 
- 校对者: [Harries Chen](https://github.com/mrchenhao)
- 状态 :  完成

**更新备注**:本教程是由[Colin Eberhardt](http://www.raywenderlich.com/u/ColinEberhardt)编写的[ iOS 7 by Tutorials](http://www.raywenderlich.com/?page_id=48020)其中某章节的精简版本.由James Frost更新到iOS8并用Swift重写,在Xcode 6 beta 7版本上测试通过。

iOS的设计目标鼓励你去创建数字界面模拟触摸，手势，改变方向等，就好像它们真实的物理对象而远非像素的集合。最终给予用户更深层级的交互而不是肤浅的拟真设计。

这听起来感觉挺难的，因为让数字界面看起来是真实的远比让它感觉是真实的简单。不过好在你有一些漂亮的新工具:

- **UIKit Dynamics** 是集成到UIKit中的物理引擎。这允许你创建一个感觉像是真实的界面，通过添加一些行为，例如重力，锚连接 (弹簧)以及力. 你可以定义你希望你的界面需要的物理特征, Dynamics会帮你关心其他的细枝末节。
- **Motion Effects** 允许你创建类似于iOS 7 主屏幕中酷酷的视差效果. 基本上你可以利用手机加速器提供的数据来创建界面来模拟手机方向发生改变的反应。

当一起使用的时候，由 Dynamics 与 Motion形成用户体验的工具，让你的界面给用户生活带来非同一般的体验。当观察到他们的操作是通过一种自然、动态的方式，你的用户将会与你的app进行更深层次的连接。

> **注意**: 由于本教程编写的时候iOS 8 还处于测试中，因此[没有提供iOS8的截图](http://www.raywenderlich.com/?p=74138). 所有的截图都是iOS7环境下的,不过看起来与iOS 8相差不大.



### Getting started  起步

UIKit Dynamics是非常有趣的；最好的学习方法就是脚踏实地先学一些小例子。

打开Xcode，依次选择**File / New / Project …** 然后选择 **iOS Application / Single View Application** 最后命名你的项目为**DynamicsDemo**。项目创建完成之后，打开 **ViewController.swift** ，然后在`viewDidLoad`方法中添加如下代码:

``` swift
let square = UIView(frame: CGRect(x: 100, y: 100, width: 100, height: 100))
square.backgroundColor = UIColor.grayColor()
view.addSubview(square)
```

以上代码仅仅是在界面上添加一个方形的`UIView`。

运行你的App,你就可以在模拟器中看到如下的正方形:

![](http://cdn1.raywenderlich.com/wp-content/uploads/2013/09/LonelySquare.png)

如果你在真机中运行此app,可以尝试倾斜、颠倒甚至是摇晃它.你会发现这样做没什么效果.这是因为一切是按照设计来工作的.当你添加一个View到界面上时,你希望它一直固定在设定好的坐标上,除非是你给界面添加一些动态的效果.



### Adding gravity   添加重力

还是在 **ViewController.swift**中，在`viewDidLoad`之上加上下面这些属性:

``` swift
var animator: UIDynamicAnimator!
var gravity: UIGravityBehavior!
```

这些属性是表示自动拆包可选类型(在类型后由!表示).这些属性必须是可选类型的,因为我们在`init`方法中不会初始化它们.同时也是因为在你初始化之后它们不可能为空.这样通过!就可以避免每一次都手动拆包.

在`viewDidLoad`方法的最后加上这段代码:

``` swift
animator = UIDynamicAnimator(referenceView: view)
gravity = UIGravityBehavior(items: [square])
animator.addBehavior(gravity)
```

我会在一会儿之后解释这个.现在,运行你的程序.你会看到你的方块慢慢的开始加速向下运行,直到掉落在屏幕的底部.

![](http://cdn1.raywenderlich.com/wp-content/uploads/2013/09/FallingSquare.png)

刚刚添加的代码中,有两个Dynamics的类在起作用:

- **UIDynamicAnimator** 是UIKit物理引擎.这个类跟踪你添加到Dynamics中的各种行为,例如重力,并且提供了整体的上下文.当你创建一个动画实例时, 你需要传递一个该动画用来定义坐标系统的View.
- **UIGravityBehavior** 模拟重力行为以及对一个或者多个物体施力,允许你模拟物理行为.当你创建一个行为的实例时, 你需要将此行为关联到一系列的物体上  — 通常来说是View. 这样你就可以选择哪些物体受到该行为的影响,在这个案例中物体受重力影响.

大多数的行为有一些配置属性;例如,重力行为允许你改变它的角度和大小.尝试修改这些属性让你的对象降落,侧移,或者是不同加速度的对角线.

注意:稍微说一下,在物理世界中,重力(g)表示米每秒平方,约等于9.8m/s2.使用牛顿第二定律,运用下列公式可以计算出在重力的影响下物体将会下滑多远.

**distance = 0.5 × g × time2**

在UIKit Dynamics中,该公式是一样的不过单位却是有所不同.你使用的是每千像素平方秒而不是米.使用牛顿第二定律,基于你所提供的重力组件,仍然可以计算出在任意时间点你的View的地点.

但是你真的需要全部了解么?并不是这样;你真正需要知道的是**g**越大就表示物体下降的越快,这在数学的角度来看并不难理解.



### Setting boundaries  设定边界

虽然你看不到它，这个方块甚至会继续下落后即便它已经消失在屏幕的底部.为了让它在屏幕的范围内，你需要定义一个边界.

在**ViewController.swift**中添加另外一个属性:

``` swift
var collision: UICollisionBehavior!
```

在`viewDidLoad`方法的最后加上这几行:

``` swift
collision = UICollisionBehavior(items: [square])
collision.translatesReferenceBoundsIntoBoundary = true
animator.addBehavior(collision)
```

以上代码创建了一个碰撞行为,其中定义了若干边界与该物体交互.

以上代码将`translatesReferenceBoundsIntoBoundary` 属性设为`true`,而不是显示的定义边界坐标. 这表示被关联的View的坐标被用于`UIDynamicAnimator`.

运行程序,你会看到方块碰撞屏幕的底部,然后反弹一下,停了下来,就像这样:

![](http://cdn1.raywenderlich.com/wp-content/uploads/2013/09/SquareAtRest.png)

这是一些令人印象深刻的行为,特别是考虑到你此时只添加了这一点代码.



### Handling collisions  处理碰撞

接下来,你将添加一个不可移动的障碍物来与下落的方块进行碰撞交互.

将下面的代码添加到`viewDidLoad`方法中添加方块View的后面:

``` swift
let barrier = UIView(frame: CGRect(x: 0, y: 300, width: 130, height: 20))
barrier.backgroundColor = UIColor.redColor()
view.addSubview(barrier)
```

运行你的程序;你会看到一个红色的"障碍物"在屏幕的中间穿过.然而事实是障碍物并未对下落的方块产生影响:

![](http://cdn4.raywenderlich.com/wp-content/uploads/2013/09/BadBarrier.png)

这并不是你想要的效果,但这却是一个重要的提醒:Dynamics为只对已关联的行为的View产生影响.

可以看一下这张图:

![](http://cdn1.raywenderlich.com/wp-content/uploads/2013/09/DynamicClasses.png)

**UIDynamicAnimator**与一个提供了坐标系统的View相关联.然后你在与此关联的物体上添加了若干个行为对它们施力.大多数行为可以与多个物体关联,同样的一个物体也可以与若干个行为关联.上面的图显示当前程序中有关联的行为.

无论是你现在代码中的行为还是障碍物都是不被知道的,所以从引擎系统角度看,障碍物是不存在的.



### Making objects respond to collisions  让物体对碰撞做出反应

为了使方块与障碍物相碰撞,找到初始化碰撞行为的那行,然后替换成下面的:

``` swift
collision = UICollisionBehavior(items: [square, barrier])
```

碰撞对象需要知道应该与之几乎的每一个View;因此添加障碍物到物体列表中,这样就允许碰撞对象与之发生作用.

运行你的程序;这两个对象产生碰撞行为,如下面的截图所示:

![](http://cdn3.raywenderlich.com/wp-content/uploads/2013/09/GoodBarrier.png)

这个碰撞的行为围绕与之关联的每个物体,形成了一个"边界".将他们从对象改变成更坚固的物体.

更新之前的图示,现在可以看到现在碰撞行为与View相关联了:

![](http://cdn2.raywenderlich.com/wp-content/uploads/2013/09/DynamicClasses2.png)

然后,两个物体的相互作用还是有些不太对劲.障碍物应该是不可移动的,但是当两个物体碰撞时,你当前的配置的障碍物向着屏幕的底部移动了.

更奇怪的是,障碍物居然在屏幕底部弹跳,而且不像方块那样停止下来 -这样是因为重力行为没有与障碍物交互.这同样也解释了为什么障碍物直到与方块碰撞才移动.

看起来你需要一个不一样的解决问题的办法.由于障碍物是不可移动的,没有任何必须要让运动引擎直到它的存在,但这样如何检测碰撞呢?



### Invisible boundaries and collisions  可见的边界与碰撞

恢复碰撞行为的初始化到原来的状态，让它只知道方块：

``` swift
collision = UICollisionBehavior(items: [square])
```

此行之后,添加以下内容:

``` swift
// add a boundary that has the same frame as the barrier
collision.addBoundaryWithIdentifier("barrier", forPath: UIBezierPath(rect: barrier.frame))
```

上面的代码添加了一个具有同样结构的障碍物View作为可见的边界.这个红色的障碍物保持着对用户可见,但对Dynamics引擎不可见,同时,边界对于Dynamics引擎可见,对于用户却不可见.当方块落下时,看起来似乎是与障碍物交互的,然而实际上却是撞到了不可移动的边界.

运行你的程序看看这个动作,如下图:

![](http://cdn1.raywenderlich.com/wp-content/uploads/2013/09/BestBarrier.png)

现在方块在边界处反弹,旋转了一下,然后继续继续朝屏幕底部落下.

现在的UIKit Dynamics的能力变得相当清楚：你可以用少量代码完成效果.在这之中有很多细节;接下来的部分我将向你展示一些Dynamics在你的应用程序中与对象的交互细节。



### Behind the scenes of collisions  碰撞背后的知识

任何Dynamic行为都有一个action属性,用来提供一个block,在动画的每一步都执行:添加以下代码到`viewDidLoad`:

``` swift
collision.action = {
  println("\(NSStringFromCGAffineTransform(square.transform)) \(NSStringFromCGPoint(square.center))")
}
```

以上代码的日志记录了下落方块的center与transform属性.运行程序,然后你就会在Xcode的输出窗口看到这些输出.

从开始到400毫秒这段时间,你应该是看到类似下面的日志信息:

``` swift
[1, 0, 0, 1, 0, 0], {150, 236}
[1, 0, 0, 1, 0, 0], {150, 243}
[1, 0, 0, 1, 0, 0], {150, 250}
```

这里你可以看到运动引擎一直在改变方块的center — 也就是动画执行中的每一步的frame值.

只要方块撞到障碍物,就开始旋转,这就会生成类似以下的日志信息:

``` swift
[0.99797821, 0.063557133, -0.063557133, 0.99797821, 0, 0] {152, 247}
[0.99192101, 0.12685727, -0.12685727, 0.99192101, 0, 0] {154, 244}
[0.97873402, 0.20513339, -0.20513339, 0.97873402, 0, 0] {157, 241}
```

这里你可以看到Dynamics使用的是transform与基于底层物理模型定位视图偏移的组合.

虽然对于Dynamics的这些属性的兴趣可能不大,不过重要的是要知道,他们正在发挥作用.因此,如果你以编程方式更改frame或transform的属性,你可以假设这些值将被覆盖.这意味着当对象被Dynamics控制时,你就不能使用transform缩放对象了.

该方法为Dynamics行为签名,选择物体而不是View.应用动力学行为的唯一需要就是让对象实现 `UIDynamicItem` 协议,这样:

``` swift
protocol UIDynamicItem : NSObjectProtocol {
  var center: CGPoint { get set }
  var bounds: CGRect { get }
  var transform: CGAffineTransform { get set }
}
```

`UIDynamicItem`协议允许Dynamics读写center和transform属性,允许其基于其内部计算来移动物体.它对bounds也有读取的权限,用来确定物体的大小。这允许它来创建碰撞边界的四周的物体以及计算当前物体的质量.

这个协议意味着引擎不与UIView耦合,此外还有一个UIKit类不是视图但仍然遵循这个协议:`UICollectionViewLayoutAttributes`.这允许引擎在集合视图内执行动画.



### Collision notifications 碰撞提醒

到目前为止,你已经添加了一些视图和行为然后让Dynamics控制.在这一节你会看看如何在物体碰撞时接收通知.

还是在**ViewController.swift**文件中,修改类的声明来遵循 `UICollisionBehaviorDelegate`协议:

```
class ViewController: UIViewController, UICollisionBehaviorDelegate {
```

在 `viewDidLoad`方法中,将视图控制器设置为刚刚初始化的碰撞代理对象,如下:

```
collision.collisionDelegate = self
```

接下来,在类中实现碰撞行为代理中的一个方法:

```swift
func collisionBehavior(behavior: UICollisionBehavior!, beganContactForItem item: UIDynamicItem!, withBoundaryIdentifier identifier: NSCopying!, atPoint p: CGPoint) {

  println("Boundary contact occurred - \(identifier)")

}
```

该代理方法会在发生碰撞时调用.这会在控制台输出日志信息.为了避免太多的日志信息搞乱了控制台的消息,请随意删除上一节添加的`collision.action`日志.

运行程序,物体发生交互,然后控制台会输出这些:

```
Boundary contact occurred - barrier
Boundary contact occurred - barrier
Boundary contact occurred - nil
Boundary contact occurred - nil
Boundary contact occurred - nil
Boundary contact occurred - nil
```

从上面的日志消息中可以看到,方块与标识的边界发生两次碰撞;这是您前面添加的不可见的边界。(null)标识符是指外部引用视图边界.

这些日志消息可以引人入胜(很严肃的说!),不过需要提供一个视觉指示项来表示反弹.

在第二次输出日志的行后,添加以下代码:

```swift
let collidingView = item as UIView
collidingView.backgroundColor = UIColor.yellowColor()
UIView.animateWithDuration(0.3) {
collidingView.backgroundColor = UIColor.grayColor()
}
```

上面的代码将碰撞物体的的背景颜色更改为黄色,然后再消褪到灰色.

运行程序然后查看这个效果:

![](http://cdn3.raywenderlich.com/wp-content/uploads/2013/09/YellowCollision.png)

方块每次碰撞到边界都会变化成黄色.

到目前为止UIKit Dynamics 通过计算它们的bounds自动为你的物体设置物理属性(如质量和弹性).接下来你将看到如何自己使用`UIDynamicItemBehavior`类来控制这些物理属性.



### Configuring item properties  设置属性

在`viewDidLoad`方法中,在方法的末尾加上以下代码:

```
let itemBehaviour = UIDynamicItemBehavior(items: [square])
itemBehaviour.elasticity = 0.6
animator.addBehavior(itemBehaviour)
```

以上代码创建了一个物体行为,并与方块关联上,接着将其添加到animator中. elasticity属性控制物体的弹力;其值为1.0时表示碰撞完全反弹;也就是说,碰撞不消耗能量或者减缓速度.你为方块设置的elasticity为0.6,这意味着你的方块每次碰撞时都会减缓速度.

运行你的程序,然后你会发现方块现在的行为很有弹性,如下所示:

![](http://cdn5.raywenderlich.com/wp-content/uploads/2013/09/PrettyBounce.png)

> 注意:如果你想知道我是如何产生上图的方块轨迹图的,这实际上很简单!我只是添加了一个block到每个行为中的action属性中,该block每三秒执行一次,然后使用当前方块的center和transform属性在视图中添加一个新的方块.下面就是我的解决方案.
> 解决方案:
```
var updateCount = 0
collision.action = {
  if (updateCount % 3 == 0) {
    let outline = UIView(frame: square.bounds)
    outline.transform = square.transform
    outline.center = square.center
    outline.alpha = 0.5
    outline.backgroundColor = UIColor.clearColor()
    outline.layer.borderColor = square.layer.presentationLayer().backgroundColor
    outline.layer.borderWidth = 1.0
    self.view.addSubview(outline)
  }
    ++updateCount
}
```

在上面的代码你只改变了物体的弹力;然而,物体的行为类有许多可以控制的属性,如下:

- `elasticity` – 弹力,决定碰撞时的弹力,例如,当物体发生碰撞行为时的弹力或者韧性.
- `friction`   –  摩擦力,决定物体滑动时的抵抗力.
- `density`   –  密度,如果加上大小,就能表示物体的质量.质量越大,物体越难加速或减速.
- `resistance` – 阻力,任何线性运动的抵抗力.与摩擦力不同的是,摩擦力只对滑动有效.
- `angularResistance` – 旋转的阻力.
- `allowsRotation` – 这个有点意思,因为这不表示真实物理世界中的属性. 将这个属性设置为NO可以让物体不管任何发生力量的旋转都无效.



### Adding behaviors dynamically  动态添加行为

在当前情况下,你的程序为系统设置全部的行为,然后让Dynamics处理系统中的物理事件直到是全部的物体都静止下来.接下来,你将看到如何动态的添加与删除行为.

打开**ViewController.swift**,然后在`viewDidLoad`方法中添加以下属性:

```
var firstContact = false
```

添加以下代码到碰撞的代理方法`collisionBehavior(behavior:beganContactForItem:withBoundaryIdentifier:atPoint:)`的最后

```
if (!firstContact) {
  firstContact = true

  let square = UIView(frame: CGRect(x: 30, y: 0, width: 100, height: 100))
  square.backgroundColor = UIColor.grayColor()
  view.addSubview(square)

  collision.addItem(square)
  gravity.addItem(square)

  let attach = UIAttachmentBehavior(item: collidingView, attachedToItem:square)
  animator.addBehavior(attach)
}
```

以上代码检测初次接触时的障碍物和方块,然后创建第二个方块,并为其添加碰撞与重力行为.此外,你创建一个锚链接行为来为一对对象创建虚拟的弹簧效果.

运行你的程序;当最初的方块碰撞到障碍物时,你将会看到一个新的方块出现,就像下面这样:

![](http://cdn5.raywenderlich.com/wp-content/uploads/2013/09/Attachment.png)

尽管这样看起来两个方块之间似乎是连接着的,不过你在屏幕上是无法看到线条或者弹簧之类的.



### User Interaction  用户交互

正如刚刚所看到的那样,你可以动态的添加或是删除你定义的物理系统中正在运动的行为.在最后一节,你将添加另一种类型的Dynamics行为-`UISnapBehavior`,当用户点击屏幕时,`UISnapBehavior`会生成一个弹簧动画跳转到指定位置.

删除你在上一节添加的代码:`firstContact`属性与`collisionBehavior()`中的if语句.这样当界面中只有一个方块时,就很容易看出来`UISnapBehavior`的作用.

在`viewDidLoad`中加上这两个属性:

```
var square: UIView!
var snap: UISnapBehavior!
```

这记录了方块视图,这样你就可以在控制器的任何地方访问到它.接下来你就会用到`snap`对象.

在`viewDidLoad`方法中,从声明方块的地方移除`let`关键字,这样就可以使用新的属性来替代局部变量:

```
square = UIView(frame: CGRect(x: 100, y: 100, width: 100, height: 100))
```

最后,实现`touchesEnded`方法,每当用户点击屏幕的时候就创建新的触摸行为:

```
override func touchesEnded(touches: NSSet, withEvent event: UIEvent) {
  if (snap != nil) {
    animator.removeBehavior(snap)
  }
 
  let touch = touches.anyObject() as UITouch 
  snap = UISnapBehavior(item: square, snapToPoint: touch.locationInView(view))
  animator.addBehavior(snap)
}
```

这段代码相当简单.首先,它会检查当前是否已经存在触摸行为并且移除.然后创建一个新的触摸行为来检测用户是否触摸方块,最后添加到animator中.

运行你的程序.尝试点击一下;无论你点击哪个地方,方块都应该会放大.



### Where To Go From Here? 何去何从

此时,你应该对于Dynamics的核心概念有了一定的理解.你可以下载本教程最后[DynamicsDemo项目](http://cdn5.raywenderlich.com/wp-content/uploads/2014/07/DynamicsDemo-Final-7.zip) 进行进一步的研究。

UIKit Dynamics 给你的应用带来物理引擎的能力.通过精妙的的反弹和弹簧和重力效果,给你的应用程序注入新的血液,让用户如同身临其境.

如果你想要更深入的学习UIKit Dynamics,请查阅我们编写的[iOS 7 By Tutorials](http://www.raywenderlich.com/?page_id=48020). 这本书让你的知识更进一步,向你展示如何在真实场景中使用UIKit Dynamics:

![](http://cdn5.raywenderlich.com/wp-content/uploads/2013/09/SandwichFlowDynamics.png)

用户可以把菜谱上一瞥,当他们将菜谱放回时,它会掉回菜谱堆中,或者靠近屏幕顶部.最终给予应用程序一个真实的物理效果。

我希望你能喜欢这个UIKit Dynamics的教程,我们觉得这是非常炫酷的,期待你在程序中创造性的使用他们.如果你有任何的问题或意见,请在一下进行讨论!