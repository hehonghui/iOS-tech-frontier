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

```class Ellipse: UIView {    
   override var collisionBoundsType: UIDynamicItemCollisionBoundsType {
    return .Ellipse    
  }    
}```

## 边缘碰撞

UIDynamics的第一个正式发布的版本中所使用的碰撞系统(由[UICollisionBehavior](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UICollisionBehavior_Class/)类提供支持)仅仅支持矩形物体。对于由一个矩形框(rectangular frames)所包围的`UIView`来说没什么问题，但是对于一个圆形的UIView甚至自定义的贝塞尔路径来说并不适用。在iOS 9中添加了一个新的属性到`UIDynamicItem`协议中：`UIDynamicItemCollisionBoundsType`，它接受的以下枚举值之一:

- `Rectangle`
- `Ellipse`
- `Path`

该属性为只读，所以如果我们想要改变他的话需要提供我们自己子类：

```**class Ellipse: UIView {**    
   **override var** collisionBoundsType: UIDynamicItemCollisionBoundsType {
    **return** .Ellipse    
  }    
}```

This is a UIView with the default collision bound:    
![](http://fancypixel.github.io/images/posts/2015-06-19/slide.gif)

This is the same UIView with the` .Ellipse`:    
![](http://fancypixel.github.io/images/posts/2015-06-19/roll.gif)

这个是使用UIView默认的边缘碰撞:    
![](http://fancypixel.github.io/images/posts/2015-06-19/slide.gif)

这个是同一个UIView使用` .Ellipse`的效果:    
![](http://fancypixel.github.io/images/posts/2015-06-19/roll.gif)

This covers round views, if we want to get fancy and draw a more complex view with a coherent rigid body we can use the enum property `.Path` and also override this property:    
``` **var** collisionBoundingPath: UIBezierPath **{ get }**```
    
The path can be whatever you can think of, as long as it’s convex (this means that for any given couple of points inside the polygon, the segment between the two is always entirely contained in the polygon itself) and counter-clockwise wound.
The convex requirement could be a significant limit, so `UIDynamicItemGroup` was introduced to specify shapes as a group of different shapes. This way as long as each shape in the group is convex we are fine even if the resulting polygon composition is concave.

这是个圆形的视图，如果我们想要更好的，画更复杂的带有一致刚性的视图，我们可以使用枚举的`.Path`属性而后重写这个属性:    
``` **var** collisionBoundingPath: UIBezierPath **{ get }**```
路径(path)只要是你想要的都可以，只要他是个凸面的(意思是说对于所有给定的点必须在多边形上，线段的每一段总是完全包含在多边形上)并且逆时针方向相切。

凸面是一个非常重要的限制，所以`UIDynamicItemGroup`被引入，其用来指定这些形状(shapes)作为一个组。这种方式只要求组中的每一个形状是凸面，所以组合起来的形状是一个凹面也没关系。

## Field Behavior

Field behaviors are a new type of behavior that is applied to the whole scene. The most common example that we’ve been using implicitly all along is `UIGravityBehavior`, which applies a down force to each item in the scene. Now we can use a new set of field forces, like Radial (the forces are stronger at the center and weaker around the edges), Noise (forces with different magnitudes are scattered around the field), and so on.

## 领域行为

领域行为也是行为(behavior)当中一个新的类型，他作用于整个场景当中。最常用的一个情况就是，我们一直隐式得使用`UIGravityBehavior`，他可以给场景中的每一个项(item)施加一个向下的力。现在我们可以使用一个新的一套场力(field forces)，像放射状的力场(所有的力由中心向外发射逐渐变弱)，噪声(力分为不同的震级分散在场中不同区域)等等。

## Dynamic Item Behavior
`UIDynamicItemBehavior` received a couple of interesting new properties:

```var charge: CGFloat    
var anchored: Bool```   
 
`charge` represent the electric charge that can influence how an item moves in an electric or magnetic field (yeah, it’s bonkers), while `anchored` basically turns a shape into a static object that participates in the collisions, but without response (if something crashes into the item, it won’t budge), so it’s perfect to represent something like a floor or a wall.

## Dynamic Item Behavior

`UIDynamicItemBehavior`现在有了一对新的属性:    

```var charge:CGFloat var anchored:Bool```    

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

```<font color=#C1C1B8>/* 
Build the hoop, setup the world appearance
*/</font>    
func buildViews() {    
	board = <font color=#3494BB>UIView</font>(frame: <font color=#3494BB>CGRect</font>(x: hoopPosition<font color=#006E6D>.x</font>, y: hoopPosition<font color=#006E6D>.y</font>, width: <font color=#006E6D>100</font>, height: <font color=#006E6D>100</font>))    
	board<font color=#006E6D>.backgroundColor</font> = <font color=#006E6D>.whiteColor</font>()    
	board<font color=#006E6D>.layer.borderColor</font> = <font color=#3494BB>UIColor</font>(red: <font color=#006E6D>0.98</font>, green: <font color=#006E6D>0.98</font>, blue: <font color=#006E6D>0.98</font>, alpha: <font color=#006E6D>1</font>)<font color=#006E6D>.CGColor</font>    
	board<font color=#006E6D>.layer.borderWidth</font> = <font color=#006E6D>2</font>    
  board<font color=#006E6D>.addSubview</font>({    
  	let v = <font color=#3494BB>UIView</font>(frame: <font color=#3494BB>CGRect</font>(x: <font color=#006E6D>30</font>, y: <font color=#006E6D>43</font>, width: <font color=#006E6D>40</font>, height: <font color=#006E6D>40</font>))    
    v<font color=#006E6D>.backgroundColor</font> = <font color=#006E6D>.clearColor</font>()    
    v<font color=#006E6D>.layer.borderColor</font> = <font color=#3494BB>UIColor</font>(red: <font color=#006E6D>0.4</font>, green: <font color=#006E6D>0.4</font>, blue: <font color=#006E6D>0.4</font>, alpha: <font color=#006E6D>1</font>)<font color=#006E6D>.CGColor</font>    
    v<font color=#006E6D>.layer.borderWidth</font> = <font color=#006E6D>5</font>    
    **return** v    
    }())    
  leftHoop = Ellipse(frame: <font color=#3494BB>CGRect</font>(x: hoopPosition.x + 20, y: hoopPosition.y + 80, width: 10, height: 6))    
  leftHoop.backgroundColor = .clearColor()    
  leftHoop.layer.cornerRadius = 3    
  rightHoop = Ellipse(frame: <font color=#3494BB>CGRect</font>(x: hoopPosition.x + 70, y: hoopPosition.y + 80, width: 10, height: 6))    
  rightHoop.backgroundColor = .clearColor()    
  rightHoop.layer.cornerRadius = 3```
 
  
  hoop = <font color=#3494BB>UIView</font>(frame: <font color=#3494BB>CGRect</font>(x: hoopPosition.x + 20, y: hoopPosition.y + 80, width: 60, height: 6))    
  hoop.backgroundColor = <font color=#3494BB>UIColor</font>(red: 177.0/255.0, green: 25.0/255.0, blue: 25.0/255.0, alpha: 1)    
  hoop.layer.cornerRadius = 3

  [board, leftHoop, rightHoop, floor, ball, hoop].map({**self**.view.addSubview($0)})    
}```

## 入门指南
大家都知道要写一款精品软件是有难度且很复杂的：不仅要满足特定要求，而且软件还必须具有稳健性，可维护、可测试性强，并且能够灵活适应各种发展与变化。这时候，“清晰架构”就应运而生了，这一架构在开发任何软件应用的时候用起来非常顺手。

这个思路很简单：简洁架构 意味着产品系统中遵循一系列的习惯原则：