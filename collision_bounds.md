iOS 9中UIDynamics的应用
---

> * 原文链接 : [Playing With UIDynamics in iOS 9](http://fancypixel.github.io/blog/2015/06/19/playing-with-uidynamics-in-ios-9/?utm_campaign=iOS%2BDev%2BWeekly&utm_medium=email&utm_source=iOS_Dev_Weekly_Issue_205)
* 原文作者 : [Andrea - @theandreamazz]
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [jointSong](https://github.com/joint-song/) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :   校对中

**注意 : 翻译完之后请认真的审核一遍有没有错字、语句通不通顺，谢谢~**


`这里是翻译原文，注意翻译时英文和译文都要留在该文档中，并且是一段英文原文下面直接跟着写译文，便于校对。如下示例 : `

UIDynamics was a welcome addition to the iOS 7 SDK. It’s basically a physics engine backing common UIViews, allowing us to define physics traits to the UI elements. The API is fairly straightforward, so you can easily create animations and transitions that look and feel great. I already covered the basics in [this article](http://andreamazz.github.io/blog/2014/05/22/uikit-dynamics/) a while ago, this time we’ll be looking at what’s new in UIDynamics in iOS 9.

UIDynamics在iOS7中第一次出现。他是一个支持UIView的物理引擎，允许我们为UI元素定义一系列的物理特性。API也非常的简洁，所以你可以非常轻松得创造看起来很不错的动画和转场效果。我已经在[这篇文章](http://andreamazz.github.io/blog/2014/05/22/uikit-dynamics/)中介绍了UIDynamics的基本概念和常识，这次我们将来看一看iOS 9中的UIDynamics有什么新的变化。
## Collision Bounds
The first release of UIDynamics shipped with a collision system (provided by [UICollisionBehavior](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollisionBehavior_Class/)) that only supported rectangular bodies. It made sense, since `UIViews` are backed by rectangular frames, but it’s not uncommon to have a circular view, or even better our own custom Bezier path. With iOS 9 a new property has been added to the `UIDynamicItem` protocol: `UIDynamicItemCollisionBoundsType`, which accepts one of these enum values:

- `Rectangle`
- `Ellipse`
- `Path`

The property is readonly, so if we want to change it we need to provide our own subclass:

	class Ellipse: UIView {    
		override var collisionBoundsType:UIDynamicItemCollisionBoundsType {
    		return .Ellipse    
    	}
    }

## 边缘碰撞

UIDynamics的第一个正式发布的版本中所使用的碰撞系统(由[UICollisionBehavior](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollisionBehavior_Class/)类提供支持)仅仅支持矩形物体。对于由一个矩形框(rectangular frames)所包围的`UIView`来说没什么问题，但是对于一个圆形的UIView甚至自定义的贝塞尔路径来说并不适用。在iOS 9中添加了一个新的属性到`UIDynamicItem`协议中：`UIDynamicItemCollisionBoundsType`，它接受的以下枚举值之一:

- `Rectangle`
- `Ellipse`
- `Path`

该属性为只读，所以如果我们想要改变他的话需要提供我们自己子类：

	class Ellipse: UIView {    
		override var collisionBoundsType: UIDynamicItemCollisionBoundsType {
    		return .Ellipse    
    	}    
    }

This is a UIView with the default collision bound:    
![](http://fancypixel.github.io/images/posts/2015-06-19/slide.gif)

This is the same UIView with the` .Ellipse`:    
![](http://fancypixel.github.io/images/posts/2015-06-19/roll.gif)

这个是使用UIView默认的边缘碰撞:    
![](http://fancypixel.github.io/images/posts/2015-06-19/slide.gif)

这个是同一个UIView使用` .Ellipse`的效果:    
![](http://fancypixel.github.io/images/posts/2015-06-19/roll.gif)

This covers round views, if we want to get fancy and draw a more complex view with a coherent rigid body we can use the enum property `.Path` and also override this property:

	var collisionBoundingPath: UIBezierPath { get }
    
The path can be whatever you can think of, as long as it’s convex (this means that for any given couple of points inside the polygon, the segment between the two is always entirely contained in the polygon itself) and counter-clockwise wound.
The convex requirement could be a significant limit, so `UIDynamicItemGroup` was introduced to specify shapes as a group of different shapes. This way as long as each shape in the group is convex we are fine even if the resulting polygon composition is concave.

这是个圆形的视图，如果我们想要更好的，画更复杂的带有一致刚性的视图，我们可以使用枚举的`.Path`属性而后重写这个属性:
    
	var collisionBoundingPath: UIBezierPath { get }

路径(path)只要是你想要的都可以，只要他是个凸面的(意思是说对于所有给定的点必须在多边形上，线段的每一段总是完全包含在多边形上)并且逆时针方向相切。

凸面是一个非常重要的限制，所以`UIDynamicItemGroup`被引入，其用来指定这些形状(shapes)作为一个组。这种方式只要求组中的每一个形状是凸面，所以组合起来的形状是一个凹面也没关系。

## Field Behavior

Field behaviors are a new type of behavior that is applied to the whole scene. The most common example that we’ve been using implicitly all along is `UIGravityBehavior`, which applies a down force to each item in the scene. Now we can use a new set of field forces, like Radial (the forces are stronger at the center and weaker around the edges), Noise (forces with different magnitudes are scattered around the field), and so on.

## 领域行为

领域行为也是行为(behavior)当中一个新的类型，他作用于整个场景当中。最常用的一个情况就是，我们一直隐式得使用`UIGravityBehavior`，他可以给场景中的每一个项(item)施加一个向下的力。现在我们可以使用一个新的一套场力(field forces)，像放射状的力场(所有的力由中心向外发射逐渐变弱)，噪声(力分为不同的震级分散在场中不同区域)等等。

## Dynamic Item Behavior
`UIDynamicItemBehavior` received a couple of interesting new properties:

	var charge: CGFloat
	var anchored: Bool 
 
`charge` represent the electric charge that can influence how an item moves in an electric or magnetic field (yeah, it’s bonkers), while `anchored` basically turns a shape into a static object that participates in the collisions, but without response (if something crashes into the item, it won’t budge), so it’s perfect to represent something like a floor or a wall.

## Dynamic Item Behavior

`UIDynamicItemBehavior`现在有了一对新的属性:    

	var charge:CGFloat
	var anchored:Bool

`charge`表示电量(electric charge)，它可以影响item在一个电场或者磁场当中的移动(没错，这听起来非常疯狂！)，而`anchored`主要是把一个形状(shape)变为一个静态的物体，这个物体来参与一系列的碰撞，但是碰撞对于这个静态物体来说并不会有什么反应(如果有另一个item撞上这个静态物体，静态物体不会动)，所以他可以非常完美得充当一些物体，比如像地板或者墙壁。

## Attachment Behavior

`UIAttachmentBehavior` was revamped and now has a sleuth of new methods and properties, like `frictionTorque` and `attachmentRange`. The attachments can now be more flexible, we can specify relative sliding movements, fixed attachments, rope attachments and what I like the most: pin attachment. Think of two objects nailed together and you get the idea.

This more or less covers what’s new in UIDynamics, now it’s time to drop the changelog and start building something silly.

##Attachment Behavior
`UIAttachmentBehavior`被重新修改，并且拥有一些新的方法和属性，比如`frictionTorque`和`attachmentRange`。attachments(附件)现在变得更加灵活，我们可以指定相对的滑动(距离)，固定附件(attachments)，翻转附件(attachment)和我最喜欢的:按住附件(pin attachment)。想象一下你可以固定两个物体，然后随心所欲。

到现在为止或多或少得介绍了一些UIDynamics的新特性，现在让我们来动手做一些东西吧！

## Let’s play ball

I’ve been spending a lot of idle time with [Ball King](https://itunes.apple.com/us/app/ball-king/id946496840?mt=8) in the last week. It’s a brilliant little time waster, the concept is simple but well executed. Also it adopts the same monetization policies of the Apple design winner Crossy Road: it doesn’t bother the player in any way. Kudos.

![](http://fancypixel.github.io/images/posts/2015-06-19/ballking.jpeg)

One thing I really like about it is the physic model of the ball and how the hoop’s backboard reacts when it gets hit by it. Looks like a fun exercise to test out the new UIDynamics stuff listed above. Let’s take a step by step look at how to build our own scruffy version: [BallSwift](https://github.com/FancyPixel/BallSwift)
# 让我们来玩球吧！

上周我花了很多空闲时间在[Ball King](https://itunes.apple.com/us/app/ball-king/id946496840?mt=8)上。它是一个不错的打发时间的小东西，游戏概念非常简单而且玩起来还不错！游戏采用与苹果设计的赢家通过相同的货币化策略，不以任何方式来打扰玩家的游戏。

![](http://fancypixel.github.io/images/posts/2015-06-19/ballking.jpeg)

首先需要考虑的是篮球的物理模型，另外还要考虑的是当篮板被击中时应该做出怎样的回应。让我们看看如何使用UIDynamics来解决这两个问题。下面我们将一步一步来看看如何构建我们自己的游戏:[BallSwift](https://github.com/FancyPixel/BallSwift)。

## The hoop

The basket can be created with a single UIView acting as the backboard, a couple of views with rigid bodies as the left and right arms of the hoop, and a frontmost view as the hoop itself (without a physic body). Using the previously defined class `Ellipse` we can create the visual representation of our game scene:

     /* 
     Build the hoop, setup the world appearance
     */
     func buildViews() {
     	board = UIView(frame: CGRect(x: hoopPosition.x, y: hoopPosition.y, width: 100, height: 100))
     	board.backgroundColor = .whiteColor()
     	board.layer.borderColor = UIColor(red: 0.98, green: 0.98, blue: 0.98, alpha: 1).CGColor
     	board.layer.borderWidth = 2
     	
     	board.addSubview({
     		let v = UIView(frame: CGRect(x: 30, y: 43, width: 40, height: 40))
     		v.backgroundColor = .clearColor()
     		v.layer.borderColor = UIColor(red: 0.4, green: 0.4, blue: 0.4, alpha: 1).CGColor
     		v.layer.borderWidth = 5
     		return v
     	}())
     	
     	leftHoop = Ellipse(frame: CGRect(x: hoopPosition.x + 20, y: hoopPosition.y + 80, width: 10, height: 6))
     	leftHoop.backgroundColor = .clearColor()
     	leftHoop.layer.cornerRadius = 3
     	
     	rightHoop = Ellipse(frame: CGRect(x: hoopPosition.x + 70, y: hoopPosition.y + 80, width: 10, height: 6))
     	rightHoop.backgroundColor = .clearColor()
     	rightHoop.layer.cornerRadius = 3
     	
     	hoop = UIView(frame: CGRect(x: hoopPosition.x + 20, y: hoopPosition.y + 80, width: 60, height: 6))
     	hoop.backgroundColor = UIColor(red: 177.0/255.0, green: 25.0/255.0, blue: 25.0/255.0, alpha: 1)
     	hoop.layer.cornerRadius = 3
     	
     	[board, leftHoop, rightHoop, floor, ball, hoop].map({self.view.addSubview($0)})
     }

Nothing new here, the hoop is created programmatically and is placed in the constant ```CGPoint hoopPosition```. The order of the views is important though, since we want the hoop to be above the basket ball.
## 篮板

篮筐可以由一个UIView充当背板，一对刚体视图来做篮筐的左右两臂，最前面的视图作为篮圈本身(没有物理特性的物体)。使用之前定义好的```Ellipse```类，我们就可以创建一个可见的游戏场景了：

	/* 
     Build the hoop, setup the world appearance
     */
     func buildViews() {
     	board = UIView(frame: CGRect(x: hoopPosition.x, y: hoopPosition.y, width: 100, height: 100))
     	board.backgroundColor = .whiteColor()
     	board.layer.borderColor = UIColor(red: 0.98, green: 0.98, blue: 0.98, alpha: 1).CGColor
     	board.layer.borderWidth = 2
     	
     	board.addSubview({
     		let v = UIView(frame: CGRect(x: 30, y: 43, width: 40, height: 40))
     		v.backgroundColor = .clearColor()
     		v.layer.borderColor = UIColor(red: 0.4, green: 0.4, blue: 0.4, alpha: 1).CGColor
     		v.layer.borderWidth = 5
     		return v
     	}())
     	
     	leftHoop = Ellipse(frame: CGRect(x: hoopPosition.x + 20, y: hoopPosition.y + 80, width: 10, height: 6))
     	leftHoop.backgroundColor = .clearColor()
     	leftHoop.layer.cornerRadius = 3
     	
     	rightHoop = Ellipse(frame: CGRect(x: hoopPosition.x + 70, y: hoopPosition.y + 80, width: 10, height: 6))
     	rightHoop.backgroundColor = .clearColor()
     	rightHoop.layer.cornerRadius = 3
     	
     	hoop = UIView(frame: CGRect(x: hoopPosition.x + 20, y: hoopPosition.y + 80, width: 60, height: 6))
     	hoop.backgroundColor = UIColor(red: 177.0/255.0, green: 25.0/255.0, blue: 25.0/255.0, alpha: 1)
     	hoop.layer.cornerRadius = 3
     	
     	[board, leftHoop, rightHoop, floor, ball, hoop].map({self.view.addSubview($0)})
     }

到目前为止没有使用任何新的东西我们就通过编程的方式创建了一个篮板，并且把他放在了常量```CGPoint hoopPosition```的位置上。这些视图的顺序也非常重要，因为我们想让篮板在篮筐的上面。

## Nuts and bolts

The most important part of the hoop are the left and right arms. They need a physical round body (so that the collision with the ball is smooth) and need to be bolted to the board and the front hoop. These two will be basic ```UIDynamicItems``` and won’t partecipate directly in the collisions. The newly introduced pin attachment is perfect for this job, it can hold everything together quite nicely as we can see in this rather ugly drawing:
![](http://fancypixel.github.io/images/posts/2015-06-19/hoop.png)

## 螺母和螺栓(Nuts and bolts)
篮筐上的左右两臂是最重要的部分。他们需要物理刚体包围(这样才能使与球的碰撞看起来更自然)，而且还需要与篮板和篮筐前面部分拴在一起。左右两臂是基本的```UIDynamicItems```并且不会直接参与碰撞。之前介绍的pin attachment(按住附件)可以很好得处理这个工作，他可以把所有的物体结合在一起，就像下面这幅图示意的一样，画得不太好:
![](http://fancypixel.github.io/images/posts/2015-06-19/hoop.png)

The pin can be attached only to a couple of views at a time, within a given absolute spatial point:

	let bolts = [
	CGPoint(x: hoopPosition.x + 25, y: hoopPosition.y + 85), // leftHoop -> Board
	CGPoint(x: hoopPosition.x + 75, y: hoopPosition.y + 85), // rightHoop -> Board
	CGPoint(x: hoopPosition.x + 25, y: hoopPosition.y + 85), // hoop -> Board (L)
	CGPoint(x: hoopPosition.x + 75, y: hoopPosition.y + 85)] // hoop -> Board (R)
	
	// Build the board
	zip([leftHoop, rightHoop, hoop, hoop], offsets).map({
       (item, offset) in
       animator?.addBehavior(UIAttachmentBehavior.pinAttachmentWithItem(item, attachedToItem: board, attachmentAnchor: bolts))
	})
	
pin(按住)一次只能将两个视图固定在一起，且最终会被固定在一个空间内绝对的点上：

	let bolts = [
	CGPoint(x: hoopPosition.x + 25, y: hoopPosition.y + 85), // leftHoop -> Board
	CGPoint(x: hoopPosition.x + 75, y: hoopPosition.y + 85), // rightHoop -> Board
	CGPoint(x: hoopPosition.x + 25, y: hoopPosition.y + 85), // hoop -> Board (L)
	CGPoint(x: hoopPosition.x + 75, y: hoopPosition.y + 85)] // hoop -> Board (R)
	
	// Build the board
	zip([leftHoop, rightHoop, hoop, hoop], offsets).map({
       (item, offset) in
       animator?.addBehavior(UIAttachmentBehavior.pinAttachmentWithItem(item, attachedToItem: board, attachmentAnchor: bolts))
	})
	
If you’re not participating in the race to Swift’s functional awesomeness you’re probably not familiar with zip and map. It might seem contrived at first, but it’s rather simple: each view is coupled with the offset point in which we’re going to pin the attachment, resulting in an array of tuples that is then used in the map function that, as the name suggests, creates a mapping with each element of the array with the provided closure. This results in both the left and right arms of the hoop to be bolted to the board and the front hoop as follows:

* Left arm bolted to the left of the board
* Right arm bolted to the right of the board
* Hoop bolted to the left of the board
* Hoop bolted to the left of the board

如果你没有接触过swift的函数精髓的话你可能会对zip和map比较疑惑。刚开始看上去有点不自然，但其实非常简单：每个视图与pin attachment的一定偏移点组合在一起，产生了一个元组的数组，然后使用map函数，map就像名字所代表的意思一样，创建了一个单个元素组成的数组与提供的闭包的之间的映射。这样就完成了将篮板左右臂与篮板和前筐组合起来，具体关系如下：

* 左臂与做篮板组合；
* 右臂与右篮板组合；
* 篮筐与左篮板组合；
* xxx;

The next step requires us to hang the board, letting it rest loosely, so that a collision can cause it to swivel a bit like it does in Ball King:

	// Set the density of the hoop, and fix its angle
	// Hang the hoop
	animator?.addBehavior({
		let attachment = UIAttachmentBehavior(item: board, attachedToAnchor: CGPoint(x: hoopPosition.x, y: hoopPosition.y))
		attachment.length = 2
		attachment.damping = 5
		return attachment
		}())
		
	animator?.addBehavior({
		let behavior = UIDynamicItemBehavior(items: [leftHoop, rightHoop])
		behavior.density = 10
		behavior.allowsRotation = false
		return behavior
		}())
		
	// Block the board rotation
	animator?.addBehavior({
		let behavior = UIDynamicItemBehavior(items: [board])
		behavior.allowsRotation = false
		return behavior
		}())
		
下一步需要我们去悬挂篮板，让他松散一些，这样当被撞击时才能转动一点，就像Ball King中的那样：

	// Set the density of the hoop, and fix its angle
	// Hang the hoop
	animator?.addBehavior({
		let attachment = UIAttachmentBehavior(item: board, attachedToAnchor: CGPoint(x: hoopPosition.x, y: hoopPosition.y))
		attachment.length = 2
		attachment.damping = 5
		return attachment
		}())
		
	animator?.addBehavior({
		let behavior = UIDynamicItemBehavior(items: [leftHoop, rightHoop])
		behavior.density = 10
		behavior.allowsRotation = false
		return behavior
		}())
		
	// Block the board rotation
	animator?.addBehavior({
		let behavior = UIDynamicItemBehavior(items: [board])
		behavior.allowsRotation = false
		return behavior
		}())


The hoop is ready to go. Let’s take care of the ball, starting with a custom subclass of ```UIImageView``` with a rounded physics body, just like the ```Ellipse``` class:

	class Ball: UIImageView {
		override var collisionBoundsType: UIDynamicItemCollisionBoundsType {
    	return .Ellipse
    	}
    }
    
We can then istantiate the ball as a common UIImageView:

	let ball: Ball = {
		let ball = Ball(frame: CGRect(x: 0, y: 0, width: 28, height: 28))
		ball.image = UIImage(named: "ball")
		return ball
	}()

Finally we set its physical properties:

	// Set the elasticity and density of the ball
	animator?.addBehavior({
		let behavior = UIDynamicItemBehavior(items: [ball])
		behavior.elasticity = 1
		behavior.density = 3
		behavior.action = {
			if !CGRectIntersectsRect(self.ball.frame, self.view.frame) {
				self.setupBehaviors()
				self.ball.center = CGPoint(x: 40, y: self.view.frame.size.height - 100)
			}
		}
		return behavior
	}())
	
In this bit of code I set the elasticity (how much it should bounce after a collision), density (think of it as the *weight*) and a handy action closure that resets the world state when the ball exits the play area (the main view).

篮框已经就绪了！让我们来把注意力放在篮球上，首先来创建一个自定义的```UIImageView```的子类，并且用一个圆形的物理特性的物体包围，就像```Ellipse```类一样：

	class Ball: UIImageView {
		override var collisionBoundsType: UIDynamicItemCollisionBoundsType {
    	return .Ellipse
    	}
    }

我们可以用UIImageView的实例来充当篮球。

	let ball: Ball = {
		let ball = Ball(frame: CGRect(x: 0, y: 0, width: 28, height: 28))
		ball.image = UIImage(named: "ball")
		return ball
	}()


接下来设置球的物理特性：

	// Set the elasticity and density of the ball
	animator?.addBehavior({
		let behavior = UIDynamicItemBehavior(items: [ball])
		behavior.elasticity = 1
		behavior.density = 3
		behavior.action = {
			if !CGRectIntersectsRect(self.ball.frame, self.view.frame) {
				self.setupBehaviors()
				self.ball.center = CGPoint(x: 40, y: self.view.frame.size.height - 100)
			}
		}
		return behavior
	}())


在这段代码中我设置了球体的弹性(当碰撞后产生多大的反弹)，密度（把它当做一个*重物*），以及一个便利动作闭包(handy action closure)以用来充值世界的状态当球从当前的活动区域(主视图)出去时。

## Collisions and gravity

I mentioned the new ```anchored``` property of ```UIDynamicItemBehavior```, which disables the dynamic behavior of an object while keeping it in the collision’s loop. Sounds like a great way to build a steady floor:

	// Anchor the floor
	animator?.addBehavior({
		let behavior = UIDynamicItemBehavior(items: [floor])
		behavior.anchored = true
		return behavior
	}())
	
## 碰撞和重力
我提到过UIDynamicBehavior的一个新的属性:anchored。它可以让对象(object)在一个碰撞循环当中失去动态行为(dynamic behavior)。可以利用这个属性来构建固定的地面。

	// Anchor the floor
	animator?.addBehavior({
		let behavior = UIDynamicItemBehavior(items: [floor])
		behavior.anchored = true
		return behavior
	}())
	
Forget to set this property and you’ll be scratching your head a lot. I know I did.
Ok, everything is set, it just needs some gravity and a set of collisions:

	animator?.addBehavior(UICollisionBehavior(items: [leftHoop, rightHoop, floor, ball]))
	animator?.addBehavior(UIGravityBehavior(items: [ball]))

The gravity is a field behavior that applies a down force of 1 point per second as default. The collision behavior takes as parameter only the views that should collide with each other. The world is set up, now we can apply an instantaneous force to the ball and keep our fingers crossed:

	let push = UIPushBehavior(items: [ball], mode: .Instantaneous)
	push.angle = -1.35
	push.magnitude = 1.56
	animator?.addBehavior(push)

如果你一不小心忘记了设置这个属性那么你一定会非常抓狂，因为我就做过这样的事情。好了，所有东西都就绪了，就差一点重力和碰撞了。

	animator?.addBehavior(UICollisionBehavior(items: [leftHoop, rightHoop, floor, ball]))
	animator?.addBehavior(UIGravityBehavior(items: [ball]))
	
重力是一个1point每秒的向下的场行为的作用力。碰撞行为仅仅是当视图相互碰撞是来作为一个参数对待。世界已经构建完成了，现在我们来为球施加一个实例化的力，然后滑动我们的手指:

	let push = UIPushBehavior(items: [ball], mode: .Instantaneous)
	push.angle = -1.35
	push.magnitude = 1.56
	animator?.addBehavior(push)
	
![](http://fancypixel.github.io/images/posts/2015-06-19/ball.gif)

And there you go, it’s really rough around the edges, but that was a lot of fun to build (yes, the clouds and the bushes are the same drawing, like in [Super Mario](https://www.youtube.com/watch?v=ai7d1K4Yf6A)).
As always you can find the source on our [GitHub page](https://github.com/FancyPixel/BallSwift).

Until next time,

Andrea - [*@theandreamazz*](https://twitter.com/theandreamazz)

![](http://fancypixel.github.io/images/posts/2015-06-19/ball.gif)

好了，虽然屏幕边缘没有处理看起来有些粗略，但是这个东西看起来还是非常有趣的！(灌木丛和云也同样被添加上去了，像超级玛丽那样。)
所有的资源都被放在了[这里](https://www.youtube.com/watch?v=ai7d1K4Yf6A)。     
下次再见！     
Andrea - [*@theandreamazz*](https://twitter.com/theandreamazz)