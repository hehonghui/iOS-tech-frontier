> * 原文链接 : [Trigonometry for Games – Sprite Kit and Swift Tutorial: Part 1/2](http://www.raywenderlich.com/90520/trigonometry-games-sprite-kit-swift-tutorial-part-1)
* 原文作者 : [Nick Lockwood](http://www.raywenderlich.com/u/nicklockwood)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [kmyhy](https://github.com/kmyhy) 

Update 04/20/2015: Updated for Xcode 6.3 and Swift 1.2.

更新 2015/04/20：升级至 Xcode 6.3 和 Swift 1.2

Update Note: This is the third incarnation of one of our very popular tutorials – the first version was written by Tutorial Team member Matthijs Hollemans for Cocos2D, and the second version was update to Sprite Kit by Tony Dahbura. This latest version still uses Sprite Kit, but is updated for iOS 8 and Swift.

更新说明：这是我们广受欢迎的教程之一的第三个版本——第一个版本是 Cocos2D 的，由 Matthijs Hollemans 缩写，第二个版本由 Tony Dahbura 升级为 Sprite Kit。最终的版本仍然是 Sprite Kit 的，但升级至 iOS 8 和 Swift。

Does the thought of doing mathematics give you cold sweats? Are you ready to give up on your career as a budding game developer because the math just doesn’t make any sense to you?

是否一提到数学就让你恐惧？你是否曾经因为数学成绩不好而想放弃游戏开发这个职业？

Don’t fret – math can be fun, and this cool 2-part game tutorial will prove it!

不要烦恼——数学其实很有趣，而且也很酷——这两篇教程会证明这一点！

Here’s a little secret: as an app developer, you don’t really need to know a lot of math. Most of the computations that we do in our professional lives don’t go much beyond basic arithmetic.

有一个诀窍：作为一个开发者，你其实不需要学习多少数学技能。在我们的职业生涯中的绝大部分计算，其实都用最基本的数学技能就足以应付。

That said, for making games it is useful to have a few more math skills in your toolbox. You don’t need to become as smart as Archimedes or Isaac Newton, but a basic understanding of trigonometry, combined with some common sense, will take you a long way.

对于编写游戏来说，在你的技能中拥有一些数学技能是有用的。不需要你是阿基米德或者艾萨克.牛顿，但需要知道一些三角学以及一些数学常识，你需要做好心理准备。

In this tutorial, you will learn about some important trigonometric functions and how you can use them in your games. Then you’ll get some practice applying the theories by developing a simple space shooter iPhone game using the Sprite Kit game framework.

在本教程中，你需要学习一些重要的三角函数，以及如何在游戏中使用它们。然后，你需要用学到的知识做一些练习，通过 Sprite Kit 开发一个简单的太空射击游戏。

Don’t worry if you’ve never used Sprite Kit before or are going to use a different framework for your game – the mathematics covered in this tutorial are applicable to any engine you might choose to use. And you don’t need any prior experience, as I’ll walk through the process step-by-step.

如果你之前从未使用过 Sprite Kit 或其它游戏开发框架也不要担心——本教程中涉及的数学技能对任何游戏引擎都是有效的。你不需要做任何预习，我会一步一步地开始整个教程。

If you supply the common sense, this tutorial will get you up to speed on the trigonometry, so let’s get started!

如果你已经具备一些基本的背景知识，本教程将让加深对理解三角数学的理解，让我们开始吧！

> Note: The game you’ll build in this tutorial uses the accelerometer so you’ll need a real iOS device and a paid developer account.

> 注意：本教程中的游戏使用了加速计，因此你应该使用真实的 iOS 设备以及一个开发者账号。

## Getting Started: It’s All About Triangles
## 开始：关于三角学

It sounds like a mouthful, but trigonometry (or trig, for short) simply means calculations with triangles (that’s where the tri comes from).

听起来有点拗口，但三角数学（或简称三角学）的简单定义就是与三角形有关的计算（三角学因此而来）。

You may not have realized this, but games are full of triangles. For example, imagine you have a spaceship game, and you want to calculate the distance between these ships:

你也许不知道，游戏基本上是由三角形构成。例如，一个太空飞船游戏，我们需要计算出飞船之间的距离：

![](http://cdn4.raywenderlich.com/wp-content/uploads/2013/04/ships.png)

You have X and Y position of each ship, but how can you find the length of that line?

假设你知道每张飞船的 x ，y 坐标，如何计算出二者之间的距离？

Well, you can simply draw a line from the center point of each ship to form a triangle like this:

从两张飞船的中心画一条连线，构造出一个三角形：

![](http://cdn3.raywenderlich.com/wp-content/uploads/2013/03/Triangles-in-games-455x320.png)

Then, since you know the X and Y coordinates of each ship, you can compute the length of each of the new lines. Now that you know the lengths of two sides of the triangle, you can use trig to compute the length of the diagonal line – the distance between the ships.

因为我们知道每张飞船的 x,y 坐标，因此，我们可以算出新加的两条线的长度。现在，你已经获得三角形两条边的长，通过三角函数，你可以算出对角线的长度——也就是飞船之间的距离。

Note that one of the corners of this triangle has an angle of 90 degrees. This is known as a right triangle (or right-angle triangle, for you Brits out there!), and that’s the sort of triangle you’ll be dealing with in this tutorial.

注意，这个三角形有一个 90 度的角。因此它是直角三角形（或者正三角形，随便你怎么称呼它），这个教程中会针对这种三角形进行特别的处理。

Any time you can express something in your game as a triangle with a 90-degree right angle – such as the spatial relationship between the two sprites in the picture – you can use trigonometric functions to do calculations on them.

只要在游戏中能够以直角三角形描述的问题——比如两个对象之间的空间关系——我们都可以用三角学函数进行计算。

So in summary, trigonometry is the mathematics that you use to calculate the lengths and angles of right triangles. And that comes in handy more often that you might think.

总之，三角学是用来计算直角三角形边长和角度的数学。它们比你想象的还要有用。

For example, in this spaceship game you might want to:

例如，在这个太空飞行游戏中，可能会发生这些事情：

* Have one ship shoot a laser in the direction of the other ship
	
	一只飞船向另一只飞船发射激光束
	
* Have one ship start moving in the direction of another ship to chase

	一只飞船想另一只飞船追去

* Play a warning sound effect if an enemy ship is getting too close

	如果敌人的飞船靠得太紧，播放报警声

All of this and more you can do with the power of trigonometry!

诸如此类的，你都会用到三角学！

### Your Arsenal of Functions
### 三角函数

First, let’s get the theory out of the way. Don’t worry, I’ll keep it short so you can get to the fun coding bits as quickly as possible.
These are the parts that make up a right triangle:

首先介绍一些理论。别担心，我会尽量简短，已让你尽快接触到代码。一个直角三角形有以下几部分组成：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2013/03/Triangle-anatomy.png)


In the picture above, the slanted side is called the hypotenuse. It always sits across from the corner with the 90-degree angle (also called a right angle), and it is always the longest of the three sides.

在上图中，三角形中倾斜的那条边被叫做斜边。它总是对着 90 度角（即直角）的那条边，它是三条边中最长的一条边。

The two remaining sides are called the adjacent and the opposite, as seen from one particular corner of the triangle, the bottom-left corner in this case.

另外两条边叫做邻边和对边，对边是对着三角形某个角的那条边，在这个例子里，也就是位于左下角的角。

If you look at the triangle from the point of view of the other corner (top-right), then the adjacent and opposite sides switch places:

如果你从另一个角的角度（例如右上角）来看，则邻边和对边恰恰相反。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2013/03/Triangle-other-corner.png)

Alpha (α) and beta (β) are the names of the two other angles. You can call these angles anything you want (as long as it sounds Greek!), but usually alpha is the angle in the corner of interest and beta is the angle in the opposing corner. In other words, you label your opposite and adjacent sides with respect to alpha.

α 和 β 是直角之外的两个角。你可以随便命名这些角（任何希腊字母），一般我们将第一个角叫做 α 角，另一个角叫做 β 角。同时，邻边和对边是相对于 α 角而言的。

The cool thing is that if you only know two of these things, trigonometry allows you to find out all the others using the trigonometric functions sine, cosine and tangent. For example, if you know any angle and the length of one of the sides, then you can easily derive the lengths and angles of the other sides and corners:

最酷的一件事情是，你只需要知道其中两个变量，你就可以用三角函数 sin、cos 和 tan 算出其它所有的变量。例如，你知道任何一个角的大小和一条边的长度，你就可以算出其它所有角的大小好边长：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2013/03/Trig-functions.png)

You can see the sine, cosine, and tangent functions (often shortened to sin, cos and tan) are just ratios – again, if you know alpha and the length of one of the sides, then sin, cos and tan are ratios that relate two sides and the angle together.

你可以把 sin、cos、tan 看成是系数——如果你知道 α 角和一条边的长度，sin、cos 和 tan 则代表了两条边和角度之间的关系的系数。

Think of the sin, cos and tan functions as “black boxes” – you plug in numbers and get back results. They are standard library functions, available in almost every programming language, including Swift.

以 sin 为例，cos 和 tan 函数就像一个”黑盒子“——将几个数字放到盒子中，它就会返回结果。它们是标准库函数，无论哪种编程语言都会有， Swift 也不例外。

> Note: The behavior of the trigonometric functions can be explained in terms of projecting circles onto straight lines, but you don’t need to know how to derive those functions in order to use them. If you’re curious, there are plenty of sites and videos to explain the details; check out the Math is Fun site for one example.
> 
> 注意：三角函数的作用就像是把一个圆投影到直线上，要使用它们并不需要我们去理解函数是怎么实现的。如果你想知道其中细节，可以在许多站点或视频中找到解释，例如这个站点：[Math is Fun](http://www.mathsisfun.com/sine-cosine-tangent.html)

###Know Angle and Length, Need Sides
###已知一个夹角和一边之长，求三角形另两边之长

Let’s consider an example. Suppose that you know the alpha angle between the ships is 45 degrees, and the length between the ships (the hypotenuse) is 10 points long.

我们来举一个例子。假设已知两只飞船之间的 α 角为 45 度，以及斜边长度为 10。

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/01/Triangles-in-games-measured.png)

You can then plug these values into the formula:

将上述值代入公式：

sin(45) = opposite / 10

To solve this for the hypotenuse, you simply shift this formula around a bit:

进行等式变形，结果为：

opposite = sin(45) * 10

The sine of 45 degrees is 0.707 (rounded to three decimal places), and filling that in the forumla gives you the result:

45 度角的 sin 值为 0.707(截取至 3 位小数)，于是上式可变为：

opposite = 0.707 * 10 = 7.07

There is a handy mnemonic for remembering what these functions do that you may remember from high school: SOH-CAH-TOA (where SOH stands for Sine is Opposite over Hypotenuse, and so on), or if you need something more catchy: Some Old Hippy / Caught Another Hippy / Tripping On Acid. (That hippy was probably a mathematician who did a little too much trig! :])

还记得你在高中的时候学过的一个记住这些函数的小窍门吗：SOH-CAH-TOA(SOH表示：sin 是对边比斜边，依次类推)，还有一首顺口溜：Some Old Hippy / Caught Another Hippy / Tripping On Acid,有个老嬉皮士，抓住另一个嬉皮士，陷入了迷幻之中（有可能那个嬉皮士就是一个三角学搞多了的数学家:]）。

### Know 2 Sides, Need Angle
### 已知两条边之长，求夹角

The formulae above are useful when you already know an angle, but that is not always the case – sometimes you know the length of two sides and are looking for the angle between them. To derive the angle, you can use the inverse trig functions, aka arc functions (which has nothing to do with Automatic Reference Counting, before you ask!):

当你知道角度的时候，上面的公式很有用，但这种情况就不行了——你只知道两条边求它们之间的夹角。这就需要用到反三角函数了，即 arc 函数（这跟自动引用计数毫无关系！）。

![](http://cdn1.raywenderlich.com/wp-content/uploads/2013/04/Inverse-trig-functions.png)

* angle = arcsin(opposite/hypotenuse)

	角度 = arcsin(对边/斜边)

* angle = arccos(adjacent/hypotenuse)

	角度 = arccos(邻边/斜边)

* angle = arctan(opposite/adjacent)

	角度 = arctan(对边/邻边)

If sin(a) = b, then it is also true that arcsin(b) = a. Of these inverse functions, you will probably use the arc tangent (arctan) the most in practice because it will help you find the hypotenuse (remember TOA – Opposite over Adjacant!). Sometimes these functions are written as sin-1, cos-1, and tan-1, so don’t let that confuse you.

如果 sin(a) = b，则 arcsin(b) = a。在这些反三角函数中，反切函数 arctan 是最实用的，因为它能够帮你找出斜边（即TOA——对边比邻边）。有时候这些函数也被写成 sin-1,cos-1,tan-1，千万别搞错了。

Is any of this sinking in or sounding familiar? Good, because you’re not done yet with the theory lesson – there is still more that you can calculate with triangles.

是不是有点感觉活着听起来很顺耳了？很好，因为理论课还没有上完——在你能够进行三角计算之前还有一些东西需要学习。

### Know 2 Sides, Need Remaining Side
### 已知两边之长，求第三边之长

Sometimes you may know the length of two of the sides and you need to know the length of the third (as with the example at the beginning of this tutorial, where you wanted to find the distance between the two spaceships).

有时候，你知道了两条边的长，想求取第三边的长（例如本教程一开始的例子，想计算两个飞船之间的距离）。

This is where Trigonometry’s Pythagorean Theorem comes to the rescue. Even if you forgot everything else about math, this is probably the one formula you do remember:

这就需要用到三角学的勾股定理了。如果你已经彻底忘光了以前学过的数学课，那么这个公式也许会勾起你的记忆：

a<sup>2</sup> + b<sup>2</sup> = c<sup>2</sup>

Or, put in terms of the triangle that you saw earlier:

或者用三角形的专用名词来说：

opposite2 + adjacent2 = hypotenuse2

对边<sup>2</sup> + 邻边<sup>2</sup> = 斜边<sup>2</sup>

If you know any two sides, calculating the third is simply a matter of filling in the formula and taking the square root. This is a very common thing to do in games and you’ll be seeing it several times in this tutorial.

如果你知道两边之长，用上面的公式通过开方能够很容易计算出第三边。在游戏中经常需要这样做，在本教程中你会反复看到。

> Note: Want to drill this formula into your head while having a great laugh at the same time? Search YouTube for “Pythagoras song” – it’s an inspiration for many!
> 
> 注意：要想牢牢记住这个公式，有一个很有趣的方式。在 YouTube 中搜索一首“Pythagoras song”的视频吧，很有意思。

### Have Angle, Need Other Angle
### 知道一个角，求取另一个角

Lastly, the angles. If you know one of the non-right angles from the triangle, then figuring out the other ones is a piece of cake. In a triangle, all angles always add up to a total of 180 degrees. Because this is a right triangle, you already know that one of the angles is 90 degrees. That leaves:

最后，来求夹角。如果我们知道一个非直角的角的大小，则很容易得到另一个夹角的大小。在一个三角形中，所有角的大小之和总是 180 度。对于直角三角形，我们知道其中一个角肯定是 90 °，因此剩下两个角就简单了：

alpha + beta + 90 = 180

Or simply:

简化之后变成：

alpha + beta = 90

The remaining two angles must add up to 90 degrees. So if you know alpha, you can calculate beta, and vice-versa.

剩余两个角之和总是 90 °。如果你知道 α 是多少，肯定能算出 β，反之亦然。

And those are all the formulae you need to know! Which one to use in practice depends on the pieces that you already have. Usually you either have the angle and the length of at least one of the sides, or you don’t have the angle but you do have two of the sides.

所有这些公式你都需要记住！要用哪一个公式，取决于已知条件。通常，要么已知夹角和一条边的边长，要么已知两条边之长。

Enough theory. Let’s put this stuff into practice.

好了，理论就学到这里。让我们来做些练习。

![](http://cdn1.raywenderlich.com/wp-content/uploads/2013/03/Aristotle.png)

### To Skip, or Not to Skip?
### 跳过，还是不跳过？

In the next few sections, you will be setting up a basic Sprite Kit project with a spaceship that can move around the screen using the accelerometer. This won’t involve any trigonometry (yet), so if you already know Sprite Kit and feel like this guy:

接下来几节，你会创建一个基本的 Sprite Kit 项目，这个 App 中有一艘太空飞船会在屏幕上根据加速计来移动。这不会涉及任何三角计算，你如果对 Sprite Kit 非常熟悉了，就像下面这个家伙一样：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2011/03/fthat.jpg)

Then feel free to skip ahead to the Begin the Trigonometry! section below – I have a starter project waiting for you there.

那么你可以跳过开头的内容，直接进入“开始三角计算”一节！——在那里，我会为你提供一个开始项目。

But if you’re the type who likes to code everything from scratch, keep reading! :]

但如果你喜欢从头开始编写代码，请继续阅读 :]

###Creating the Project
### 创建项目

First make sure you have Xcode 6.1.1 or later, as Swift is a brand new language and the syntax is prone to change subtly between versions.

首先，确保你安装了 Xcode 6.1.1 或以上版本。因为 Swift 是一个崭新的语言，它的每个版本的语法都会何之前的版本有细微的区别。

Next, start up Xcode, select File\New\Project…, choose iOS\Application\Game template. Name the project TrigBlaster. Make sure Language is set to “Swift”, Game Technology is set to “SpriteKit”, and Devices is set to “iPhone”. Then click Next:

打开 Xcode,选择 File\New\Project...，选择 iOS\Application\Game 模板。项目命名为 TrigBlaster，语言选择 Swift，游戏技术设置为 SpriteKit，设备类型设置为 iPhone。然后点击 Next:

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/SpriteKit-Project-480x283.png)

Build and run the template project. If all works OK, you should see the following:

编译运行程序。如果一切顺利，你将看到：

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/SpriteKitHelloWorld-179x320.png)

Download the resources for this tutorial. This file contains the images for the sprites and the sound effects. Unzip it, and drag the images individually into your Images.xcassets to set up the sprites. You can delete/replace the Spaceship sprite that comes with the default project template, as you won’t be using that.

从[这里](http://cdn5.raywenderlich.com/wp-content/uploads/2013/12/TrigResourcesArt.zip)下载本教程所需资源。这个压缩文件包含了图片和声音。解压缩，将每张图片拖到 Images.xcassets 文件夹，以备创建精灵时用到。你可以删除/替换默认项目中的 Spaceship 精灵，如果你不想用它的话。

Now add the sounds. You can simply drag the whole Sounds folder into Xcode, but make sure you select the “Create groups” option.

现在来添加声音。将 Sounds 文件夹拖进 Xcode 中，确保选中 Create groups 选项。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/AddingSounds-281x320.png)
![](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/FileImport-480x282.png)

Great; the usual preliminaries are over with – now let’s get coding!

好，准备工作已经完成——现在让我们来编写代码！

### Steering with Accelerometers
### 用加速计做方向盘

Because this is a simple game, you will be doing most of your work inside a single file: GameScene.swift. Right now, this file contains a bunch of stuff that you don’t need. It also does not run with the correct orientation for the game, so let’s fix that first.

这是一个简单游戏，你只需要在一个文件中完成绝大部分工作：GameScene.swift。现在，这个文件中包含了一大堆你用不到的代码。游戏运行的方向也不正确，我们先来搞定这个。

#### Switching to Landscape Orientation
#### 切换到横屏模式
Open the target settings by clicking your TrigBlaster project in the Project Navigator and selecting the TrigBlaster target. Then, in the Deployment Info section make sure General is checked at the top and under Device Orientation uncheck all but Landscape Left (as shown below):

在项目导航窗口中点击 TrigBlaster ,打开 Target 设置，选中 Target 列表中的 TrigBlaster。打开 General 标签窗口，在 Deployment Info 一栏的 Device Orientation 下，反选所有方向，只勾选 Landscape Right(译者注：原文是 Left,但图中又是 Right，根据后面的内容看应该是 Right)：

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/LandscapeRight-480x303.png)

If you build and run, the app will now launch in landscape orientation. The app is currently loading an empty scene from the GameScene.sks file in GameViewController.swift, and then in GameScene.swift, a “Hello World” label is being added programmatically.

运行程序， App 将以横屏方向启动。当前 App 打开了一个空的画面，在 GameViewController.swift 的代码中，这个画面是来自于 GameScene.sks 文件。在 GameScene.swift 代码中，添加了一个 Hello World 标签。

Replace the contents of GameScene.swift with:

将 GameScene.swift 中的代码替换为：

```swift
import SpriteKit
 
class GameScene: SKScene {
 
  override func didMoveToView(view: SKView) {
 
    // set scene size to match view
    size = view.bounds.size
 
    backgroundColor = SKColor(red: 94.0/255, green: 63.0/255, blue: 107.0/255, alpha: 1)
  }
 
  override func update(currentTime: CFTimeInterval) {
 
  }
}
```

Build and run, and you should see… nothing but purple:

运行程序，你将看到一个空的、紫颜色的画面：

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/Purple-480x269.png)

Let’s make things a bit more exciting by adding a spaceship to the scene. Modify the GameScene class as follows:

让我们来干点稍微有趣的事情，将一艘太空飞船添加到画面中。将 GameScene 类修改为：

```swift
class GameScene: SKScene {
 
  let playerSprite = SKSpriteNode(imageNamed: "Player")
 
  override func didMoveToView(view: SKView) {
 
    // set scene size to match view
    size = view.bounds.size
 
    backgroundColor = SKColor(red: 94.0/255, green: 63.0/255, blue: 107.0/255, alpha: 1)
 
    playerSprite.position = CGPoint(x: size.width - 50, y: 60)
    addChild(playerSprite)
  }
 
  ...
}
```

This is all pretty basic if you have worked with Sprite Kit before. The playerSprite property holds the spaceship sprite, which is positioned in the bottom-right corner of the screen. Remember that with Sprite Kit it is the bottom of the screen that has a Y-coordinate of 0, unlike in UIKit where y = 0 points to the top of the screen. You’ve set the Y-coordinate to 60 to position it just above the FPS (Frames Per Second) counter in the bottom-left corner.

这些代码太常见了，如果你以前用过 Sprite Kit 的话。playerSprite 属性用于保存飞船精灵，并将它放到屏幕的右下角。注意，Sprite Kit 的 y 坐标零点位于屏幕最下边，而不是 UIKit 中的屏幕最上边。我们将 y 坐标设置为 60，这样会将它放到屏幕左下角的FPS(帧率)的上方。

> Note: The FPS counter is useful for debugging purposes, but you can disable it in GameViewController.swift if it annoys you. You’ll probably want to do that before you submit your game to the App Store!
> 
> 注意：FPS 信息是用于调试的，但我们可以隐藏它，如果你不想看到它的话。在你将游戏提交给 App 商店之前，你可以这样做。

Build and run to try it out, and you should see the following:

运行程序，你将看到：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/PurpleShip-480x269.png)

To move the spaceship, you’ll be using the iPhone’s built-in accelerometer. Unfortunately, the iOS Simulator can’t simulate the accelerometer, so from now on you will need to run the app on a real device to test it.

要让飞船移动，你需要使用 iPhone 的内置加速计。不幸的是，iOS 模拟器无法模拟加速计，因此从现在起，你就需要在真实物理设备上进行开发了。

>Note: If you are unsure how to install the app on a device, check out this extensive tutorial that explains how to obtain and install the certificates and provisioning profiles that allow Xcode to install on a physical iPhone or iPad. It’s not as intimidating as it looks, but you will need to sign up for the paid Apple developer program.
>
>注意：如果你不知道如何在设备上安装 App，请看另外一个教程，该教程描述了如何获取和安装证书和设备授权文档，已允许 Xcode 将 App 安装到真实的 iPhone 或 iPad 上。虽然不是强制的，但你必须购买一个苹果开发者证书。

To move the spaceship with the accelerometer, you’ll need to tilt your device from side to side. This was the reason you de-selected all Device Orientation options except for Landscape Right in the Project Settings screen earlier, because it would be really annoying for the screen to flip when you’re in the middle of a heated battle!

要让加速计能够驱动飞船，我们需要将设备向一边倾斜。这就是为什么我们要在项目设置中将设备方向固定为一个横屏方向的原因，因为当你处于激烈战斗中的时候，屏幕突然发生旋转是一件非常悲剧的事情！

Using the accelerometer is pretty straightforward thanks to the Core Motion framework. There are two ways to get accelerometer data: You can either register to have it delivered to your application at a specific frequency via a callback, or you can poll the values when you need them. Apple recommends not having data pushed to your application unless timing is very critical (like a measurement or navigation service) because it can drain the batteries more quickly.

加速计的使用非常简单，因为我们可以使用 Core Motion 框架。要获取加速计数据有两种方式：注册一个通知让加速计以某个周期不断地向 App 发送消息并调用回调方法，或者在我们需要数据时主动拉取数据。苹果建议我们不要使用“推”数据的方式除非有必要（比如进行精确测量或导航服务）。因为这种方式会比较耗电。

Your game already has a logical place from which to poll the accelerometer data: the update() method that gets called by Sprite Kit once per frame. You will read the accelerometer values whenever this method is fired, and use them to move the spaceship.

你的游戏有一个地方非常适合“拉取”加速计数据：update()方法每一帧都会被 Sprite Kit 调用。你可以在这个方法中获取加速计数据，并以此来移动飞船。

First, add the following import to the top of GameScene.swift:

首先，在 GameScene.swift 顶部加入一个导入语句：

```swift
import CoreMotion
```

Now you’ll have Core Motion available to you and it’ll be linked into your app.

现在，Core Motion 框架会链接到 App，你可以使用它了。

Next, add the following properties inside the class implementation:

接着，在类的实现中增加如下属性：

```swift
var accelerometerX: UIAccelerationValue = 0
var accelerometerY: UIAccelerationValue = 0
 
let motionManager = CMMotionManager()
```
You’ll need these properties to keep track of the Core Motion manager and the accelerometer values. You only need to store the values for the x- and y-axes; the z-axis isn’t used by this game.

我们用这些属性来存储 Core Motion 管理器和加速计的值。你只需要保存 x 的值和 y 值，z 坐标的值在这个游戏中暂时不需要。

Next, add the following utility methods to the class:

然后，新增两个工具方法：

```swift
func startMonitoringAcceleration() {
 
  if motionManager.accelerometerAvailable {
    motionManager.startAccelerometerUpdates()
    NSLog("accelerometer updates on...")
  }
}
 
func stopMonitoringAcceleration() {
 
  if motionManager.accelerometerAvailable && motionManager.accelerometerActive {
    motionManager.stopAccelerometerUpdates()
    NSLog("accelerometer updates off...")
  }
}
```

The start and stop methods check to make sure the accelerometer hardware is available on the device and, if so, tell it to start gathering data. The stop method will be called when you wish to turn off acceleration monitoring.

start 方法会检测设备上是否具有加速计硬件，如果是，则开始收集数据。stop 方法则用于关闭加速计监听。

A good place to activate the accelerometers is inside didMoveToView(). Add the following line to it underneath the addChild(playerSprite) line:

激活加速计的较合适的地方是在 didMoveToView() 方法里面。在这个方法的 addChild(playerSprite) 一行后加入：

```swift
startMonitoringAcceleration()
```

For stopping the accelerometers, a good place is in the class de-initializer. Add the following to the class:

而停止加速计的时机是在类的解析函数里面。在类中增加一个方法：

```swift
deinit {
  stopMonitoringAcceleration()
}
```

Next, add the method that will be called to read the values and let your player change positions:

然后，新增这个方法，每当玩家角色位置发生改变时就调用这个方法读取加速计的值：

```swift
func updatePlayerAccelerationFromMotionManager() {
 
  if let acceleration = motionManager.accelerometerData?.acceleration {
 
    let FilterFactor = 0.75
 
    accelerometerX = acceleration.x * FilterFactor + accelerometerX * (1 - FilterFactor)
    accelerometerY = acceleration.y * FilterFactor + accelerometerY * (1 - FilterFactor)
  }
}
```

This bit of logic is necessary to filter, or smooth the data that you get back from the accelerometers so that it appears less jittery. The motionManager.accelerometerData property may be nil if no data is yet available, so you use the ?. operator to access the acceleration property, and wrap the logic in if let ... to ensure it will be skipped if there is no acceleration data to work with yet.

这里进行了过滤处理，目的是为了使加速计返回的数据更平滑，卡顿感更少。如果没有数据，motionManager.accelerometerData 属性有可能为 nil，因此要用 ?. 操作符和 if let ... 语法访问 acceleration 属性，以确保当加速计数据为空时 if 语句不会被执行。

> Note: An accelerometer records the acceleration currently being applied to it. The iPhone is always under acceleration due to the pull of gravity (which is how iOS knows which way to orient the screen), but because the user is holding the iPhone in their hands (and hands are never completely steady) there are a lot of tiny fluctuations in this gravity value. You’re not so interested in these fluctuations as in the larger, deliberate changes that the user makes to the device orientation. By applying this simple low-pass filter, you retain the orientation information but filter out the less important fluctuations.
>
>注意：加速计负责记录当前施加到它身上的加速度。由于重力的作用iPhone 总是处于加速度的作用下（也因此 iPhone 总是知道哪个方向是屏幕的方向），但由于用户是用手拿着 iPhone(手并永远不会完全稳定在一个地方)，因此重力会有细微波动。对于这些细微的波动我们不在乎，但比较大的改变就有可能是用户改变了设备的方向。通过一个简单的低通量过滤，我们可以只获取方向改变信息而过滤掉无关的波动。


Now that you have a stable measurement of the device’s orientation, how can you use this to make the player’s spaceship move?

现在我们已经让设备方向固定为一个，又如何让玩家的飞船移动呢？

Motion in physics-based games is typically implemented like this:

基于物理引擎的移动通常是这样实现的：

* First, you set the acceleration, based on some form of user input (in this case the accelerometer values).

	首先，基于用户输入（在这里就是加速计数据）改变加速度。

* Second, you add the new acceleration to the spaceship’s current velocity. This makes the object speed up or slow down, depending on the direction of the acceleration.

	然后，将当前加速度加到飞船的当前速度中去。这会让飞船基于加速度的方向进行加速或减速。

* Finally, you add the new velocity to the spaceship’s position to make it move.

	最终，用新的速度改变飞船的位置，使其移动。

You have a great mathematician to thank for the equations that control this motion: Sir Isaac Newton!

在此，我们需要感谢一个伟大数学家艾萨克.牛顿，是他发明了这个位移公式！

![](http://cdn1.raywenderlich.com/wp-content/uploads/2013/03/Newton1.png)

You’ll need to add some more properties to track the velocity and acceleration. There is no need to keep track of the player’s position because the SKSpriteNode already does that for you.

我们需要将速度和加速度保存到属性中。玩家位置是不需要跟踪的，因为 SKSpriteNode 已经保存了这个值。

> Note: Technically, Sprite Kit can keep track of velocity and acceleration as well, thanks to the SKPhysicsBody property. Sprite Kit’s physics can track forces on the sprite and update the acceleration, velocity and position automatically. But if you use Sprite Kit’s physics to do all the math, you won’t learn much about trigonometry! So, for this tutorial, you’re going to do all the math yourself.
> 
> 注意：实际上，Sprite Kit 也会记录当前速度和加速度，这要用到 SKPhysicsBody 属性。Sprite Kit 的物理引擎会记录精灵所受的力，并自动更新加速度、速度和位置。但如果你要让 Sprite Kit 的物理引擎来进行这些计算，那你就无法学习三角学了。因此在本教程中，你将自己完成这些数学计算。

Add these properties to the class next:

在这个类中增加如下属性：

```swift
var playerAcceleration = CGVector(dx: 0, dy: 0)
var playerVelocity = CGVector(dx: 0, dy: 0)
```

It’s good to set some bounds on how fast the spaceship can travel or it would be pretty hard to maneuver. Unlimited acceleration would make the ship tricky to control (not to mention turning the poor pilot into jello!), so let’s set an upper limit.

最好将飞船移动的速度做一个限制，否则飞船很难操控。不对加速度进行限制的话，将使飞船失控（让可怜的飞行员变成果冻！），因此，让我们来加一点限制。

Add the following lines directly below the import statements:

直接在 import 语句后加入：

```swift
let MaxPlayerAcceleration: CGFloat = 400
let MaxPlayerSpeed: CGFloat = 200
```

This defines two constants: The maximum acceleration (400 points per second squared), and the maximum speed (200 points per second). You’ve used the common Swift convention of capitalising the first letter of your configuration constants to distinguish them from regular “let” variables.

这里我们新加了两个常量：最大加速度（400 像素/秒<sup>2</sup>），以及最大速度（200 像素/秒）。依照 Swift 一般约定，将两个常量的首字母大写，以区别于普通的 let 变量。

Now add the following code to the bottom of the if let ... statement in updatePlayerAccelerationFromMotionManager:

在 updatePlayerAccelerationFromMotionManager 方法的 if let ... 一句的最后加入：

```swift
playerAcceleration.dx = CGFloat(accelerometerY) * -MaxPlayerAcceleration
playerAcceleration.dy = CGFloat(accelerometerX) * MaxPlayerAcceleration
```

Accelerometer values are provided in the range -1 to +1, so to get the final acceleration, you simply multiply the accelerometer value by MaxPlayerAcceleration.

加速计的取值范围一般是 -1 到 +1 之间，因此要获得最终的加速度，需要乘以最大加速度 MaxPlayerAcceleration。

> Note: You’re using the accelerometerY value for the x-direction and accelerometerX for the Y-direction. That’s as it should be. Remember that this game is in landscape, so the X-accelerometer runs from top to bottom in this orientation, and the Y-accelerometer from right to left.
> 
> 注意：我们在 x 方向上用 accelerometerY 而在 y 方向上用 accelerometerX。这是正确的。注意这个游戏是横屏的，因此 x 方向的加速度是从上到下，y 方向上的加速度是从右到左。

You’re almost there. The last step is applying the playerAcceleration.dx and playerAcceleration.dy values to the velocity and position of the spaceship. You will do this from within the game’s update() method. This method is called once per frame (60 times per second), so it’s the natural place to perform all of the game logic.

继续。接下来是将 playerAcceleration.x 和 playerAcceleration.dy 用到飞船的速度和位置上，这将放在 update() 方法中进行。这个方法每帧调用一次（即 60 次/秒）。因此这个地方是进行所有游戏逻辑的好地方。

Add the updatePlayer() method:

新增一个 updatePlayer() 方法：

```swift
func updatePlayer(dt: CFTimeInterval) {
 
  // 1
  playerVelocity.dx = playerVelocity.dx + playerAcceleration.dx * CGFloat(dt)
  playerVelocity.dy = playerVelocity.dy + playerAcceleration.dy * CGFloat(dt)
 
  // 2
  playerVelocity.dx = max(-MaxPlayerSpeed, min(MaxPlayerSpeed, playerVelocity.dx))
  playerVelocity.dy = max(-MaxPlayerSpeed, min(MaxPlayerSpeed, playerVelocity.dy))
 
  // 3
  var newX = playerSprite.position.x + playerVelocity.dx * CGFloat(dt)
  var newY = playerSprite.position.y + playerVelocity.dy * CGFloat(dt)
 
  // 4
  newX = min(size.width, max(0, newX));
  newY = min(size.height, max(0, newY));
 
  playerSprite.position = CGPoint(x: newX, y: newY)
}
```

If you’ve programmed games before (or studied physics), then this should look familiar. Here’s how it works:

如果你以前编写过游戏（或者学过物理），这些代码看起来会很熟悉。这是它们的大致作用：

1. This code adds the current acceleration to the velocity.

	将当前加速度加到当前速度上。

	The acceleration is expressed in points per second (actually, per second squared, but don’t worry about that). However, the update() method is executed a lot more often than once per second. To compensate for this difference, you multiply the acceleration by the elapsed or “delta” time, dt. Without this, the spaceship would move about sixty times faster than it should!
	
	加速度以“像素/秒”为单位（实际上是秒<sup>2</sup>，但这无关紧要）。而 update() 方法执行的频率要远远大于“1次/秒”。因此，我们需要用加速度乘以 δ 时间（每帧所用的时间），即 dt。否则，飞船会比它理论上的速度要快 60 倍！
	
2. This clamps the velocity so that it doesn’t go faster than MaxPlayerSpeed if it is positive or -MaxPlayerSpeed if it is negative.

	将飞船的速度限制在 ± MaxPlayerSpeed 之内，如果飞船速度为负值，不得小于 ﹣ MaxPlayerSpeed，如果飞船速度为正，不得大于 + MaxPlayerSpeed。

3. This adds the current velocity to the sprite’s position. Again, velocity is measured in points per second, so you need to multiply it by the delta time to make it work correctly.

	将当前速度加到位置计算中去。速度的单位是“像素点/秒”，因此需要将它乘以 δ 时间（dt），然后才能加到当前位置中去。

4. Clamp the new position to the sides of the screen. You don’t want the player’s spaceship to fly off-screen, never to be found again!

	限制飞船的位置不要超出屏幕边沿。我们不想让飞船飞出屏幕以外，然后再也回不来了！

One more thing: you need to measure time as differences (deltas) in time. The Sprite Kit update() method gets called repeatedly with the current time, but you’ll need to track the delta time between calls to the update() method ourselves, so that the velocity calculations are smooth.

还有一件事情：你需要计算时间差（δ 时间 dt）。Sprite Kit 会重复调用 update() 方法并传入一个当前时间，因此速度计算是 OK 的。

To track the delta time, add another property:

要记录 δ 时间，需要增加一个属性：

```swift
var lastUpdateTime: CFTimeInterval = 0
```

Then replace the update() method stub with the actual implementation:

然后将 update 方法修改为：

```swift
override func update(currentTime: CFTimeInterval) {
 
  // to compute velocities we need delta time to multiply by points per second
  // SpriteKit returns the currentTime, delta is computed as last called time - currentTime
  let deltaTime = max(1.0/30, currentTime - lastUpdateTime)
  lastUpdateTime = currentTime
 
  updatePlayerAccelerationFromMotionManager()
  updatePlayer(deltaTime)
}
```

That should do it.

让我们看一下是怎么实现的。

You calculate deltaTime by subtracting the last recorded update time from the the current time. Just to be safe, clamp deltaTime to a maximum of 1/30th of a second. That way, if the app’s frame rate should fluctuate or stall for some reason, the ship won’t get catapulted across the screen when the next update occurs.

用这一次 update() 方法调用的时间，减去上一次 update() 方法调用的时间，得到 δ 时间 dt。为了保险起见，将 dt 限制为最小不得小于 30 分之 1 秒。如果 App 的帧率因为某种原因变得波动较大的时候，飞船不至于在一帧之内突然就飞出屏幕。

The updatePlayerAccelerationFromMotionManager() method is called to calculate the players acceleration from the accelerometer values.

调用 updatePlayerAccelerationFromMotionManager() 方法根据加速计的值计算玩家的加速度。

Finally, updatePlayer() is called to move the ship, making use of the delta time to compute the velocity.

最后，调用 updaePlayer() 方法去移动飞船，将 dt 引入到移动速度的计算中去。

Build and run the game on an actual device (not the simulator). You can now control the spaceship by tilting the device:

在真实设备上（不要在模拟器上）运行程序。现在你可以通过倾斜设备来控制飞船了：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/MovingShip-480x269.png)

One last thing before you proceed: In GameViewController.swift, find the line:

还剩最后一件事情：在 GameViewController.swift 中，找到这行：

```swift
skView.ignoresSiblingOrder = true
```

And change it to:

修改为：

```swift
skView.ignoresSiblingOrder = false
```

This disables a small optimization in the way that sprites are rendered, but it means that sprites will be drawn in the order they are added. This will be useful later.

这一句将 Sprite Kit 绘制精灵时的一个优化特性关闭。也就是说绘制精灵时，将按照精灵被加入的先后顺序进行绘制。这一点将在后面用到。

### Begin the Trigonometry!
### 开始三角计算
If you skipped ahead to this section, here is the starter project at this point. Build and run it on your device – you’ll see there’s a spaceship that you can move around with the accelerometer.
You haven’t used any actual trigonometry yet, so let’s put some into action!

如果你跳过了前面的内容，直接从这一节开始，请在[这里](http://cdn1.raywenderlich.com/wp-content/uploads/2015/04/TrigBlasterStarter.zip)下载开始项目。在你的设备上运行程序——你会看到一艘飞船，并可以用加速计来控制它移动。当然，这其中没有使用任何三角学的内容，因此接下来让我们开始这部分的内容！

It would be cool – and much less confusing to the player – to rotate the spaceship in the direction it is currently moving rather than having it always pointing upward.

我们有一个不错的想法——为了减少玩家的困惑——让飞船根据它当前运动的方向旋转，而不是一直将头朝向一个方向：正前方。

To rotate the spaceship, you need to know the angle to rotate it to. But you don’t know what that is; you only have the velocity vector. So how can you get an angle from a vector?

要旋转飞船，要先计算出它应该旋转多少度。但你并不知道它是多少，你只有一个速度向量。通过这个向量能够得到一个角度吗？

Let’s think about what you do know. The player’s velocity consists of two components: a length in the X-direction and a length in the Y-direction:

让我们想一下，我们已知的条件。玩家的速度由两部分组成：一个 x 轴方向上的长度，和一个 y 方向上的长度：

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/VelocityComponents-349x320.png)

If you rearrange these a little, you can see that they form a triangle:

如果你将它们重新排列一下，你就会发现这构成了一个三角形：

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/VelocityTriangle-434x320.png)

Here you know the lengths of the adjacent (playerVelocity.dx) and the opposite (playerVelocity.dy) sides.

这里，邻边（playerVelocity.dx)的长和对边（playerVelocity.dy)的长是已知的。

So basically, you know 2 sides of a right triangle, and you want to find an angle (the Know 2 Sides, Need Angle case), so you need to use one of the inverse functions: arcsin, arccos or arctan.

你已知直角三角形的两边，想知道一个夹角（这符合“已知两条边之长，求角的大小”），因此我们需要用到下列反三角函数之一：arcsin、arccos、arctan。

The sides you know are the opposite and adjacent sides to the angle you need, so you’ll want to use the arctan function to find the angle to rotate the ship. Remember, that looks like the following:

因为我们求的是已知的两边边长之间的夹角，因此用 arctan 函数即可找出飞船旋转的角度。也就是：

```swift
angle = arctan(opposite / adjacent)
```

The Swift standard library includes an atan() function that computes the arc tangent, but it has a couple of limitations: x / y yields exactly the same value as -x / -y, which means that you’ll get the same angle output for opposite velocities. Worse than that, the angle inside the triangle isn’t exactly the one you want anyway – you want the angle relative to one particular axis, which may be 90, 180 or 270 degrees offset from the angle returned by atan().

Swift 标注库中有一个计算反切的 atan() 函数，但它有几个限制：x 或 y 得到的结果和 -x 或 -y 是一样的，因此对于两个完全相反的速度向量来说，atan() 计算出来的角度是相同的。此外，这个角度也不是你最终想像的那样——你想计算的是实际上是相对于某个轴的相对角度，在 atan() 返回的结果上加上 90 、180 或者 270 度偏移角度后的角度。

You could write a four-way if-statement to work out the correct angle by taking into account the signs of the velocity components to determine which quadrant the angle is in, and then apply the correct offset. But it turns out there’s a much simpler way:

你可以写一个四个分支的 if 语句，去计算正确的角度，将速度向量中的变量的符号也就是说向量所处的象限也考虑进去，然后再进行正确的偏移。但我们有一个更简单的解决方法：

For this specific problem, instead of using atan(), it is simplier to use the function atan2(), which takes the x and y components as separate parameters, and correctly determines the overall rotation angle.

对于这个问题，用 atan2() 函数要比用 atan() 函数要简单得多。atan2() 函数使用单独的 x 参数和 y 参数，并能够正确地判断整个旋转角度。

angle = atan2(opposite, adjacent)

Add the following two lines to the bottom of updatePlayer:

在 updatePlayer 方法最后加入这两句：

```swift
let angle = atan2(playerVelocity.dy, playerVelocity.dx)
playerSprite.zRotation = angle
```

Notice that the Y-coordinate goes first. A common mistake is to write atan(x, y), but that’s the wrong way around. Remember the first parameter is the opposite side, and in this case the Y coordinate lies opposite the angle you’re trying to measure.

注意首先传入 y 坐标。通常我们会写成 atan(x,y)，但这错的。记住，第一个参数是对边，也就是这里的 y 坐标，位于我们想计算的角的正对面。

Build and run the app to try it out:

运行程序，进行测试：

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/ShipPointingWrongWay-480x269.png)

Hmm, this doesn’t seem to be working quite right. The spaceship certainly rotates but it’s pointing in a different direction from where it’s flying!

呃，有点不对劲。飞船是会转，但它指向的方向不是它正在飞行的方向！

Here’s what’s happening: the sprite image for the spaceship points straight up, which corresponds to the default rotation value of 0 degrees. But by mathematical convention, an angle of 0 degrees doesn’t point upward, but to the right, along the X-axis:

这是因为：飞船精灵的图片是指向上方的，默认的旋转角度是 0 度。但在数学中，0 度并不是指向上的，而是指向右的，即 X 轴的方向：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/RotationDifferences-480x220.png)

To fix this, subtract 90 degrees from the rotation angle so that it matches up with the sprite image:

为了解决这个问题，可以将旋转角度减去 90 度，以便和精灵图片的朝向相一致：

playerSprite.zRotation = angle - 90

Try it out…

你可以测试一下。

Nope! If anything, it’s even worse now! What’s missing?

不！比刚才还要糟糕了！到底怎么回事？

###Radians, Degrees and Points of Reference
###弧度、度和参考点

Normal humans tend to think of angles as values between 0 and 360 (degrees). Mathematicians, however, usually measure angles in radians, which are expressed in terms of π (the Greek letter Pi, which sounds like “pie” but doesn’t taste as good).

正常情况下，人们总是习惯于将角度看成是 0-360 度的值。但在数学中，通常用弧度来作为角度的地位，也就是说用 π （希腊字母 pi，读“pie”，但却不能吃）来表达角度。

One radian is defines the angle you get when you travel the distance of the radius along the arc of the circle. You can do that 2π times (roughly 6.28 times) before you end up at the beginning of the circle again.

一个弧度被定义为在圆上的一段长度和圆半径相等的弧所对应的角度。因此，如果要用这个线段（一个弧度长）测量整个圆的长度，就需要用反复测量 2π 次。

![](http://cdn2.raywenderlich.com/wp-content/uploads/2013/03/Radian.png)

Notice the yellow line (the radius) is the same length as the red curved line (the arc). That magic angle where the two are equal is one radian!

注意黄色的线段（半径）和红色的线段（圆弧）是等长。这个圆弧所夹的角度就是一个弧度！

So while you may think of angles as values from 0 to 360, a mathematician sees values from 0 to 2π. Most computer math functions work in radians, because it’s a more useful unit for doing calculations. Sprite Kit uses radians for all its angular measurements as well. The atan2() function returns a value in radians, but you’ve tried to offset that angle by 90 degrees.

当你用 3-360 °来衡量一个角度时，数学家却将它看成是 0-2π 。绝大部分数学函数都使用弧度，因为计算的时候弧度更方便一些。Sprite Kit 在测量角度时一律使用弧度。atan2() 函数的返回值也是弧度，但你却用它和 90 进行加减。

Since you will be working with both radians and degrees, it will be useful if you have a way to easily convert between them. The conversion is pretty simple: Since there are 2π radians or 360 degrees in a circle, π equates to 180 degrees, so to convert from radians to degrees you divide by π and multiply by 180. To convert from degrees to radians you divide by 180 and multiply by π.

由于我们将同时使用弧度和度，因此将二者进行相互转换是很有必要的。转换非常简单：因为不管 2π 还是 360° 都是一个圆，π 就等于 180°，从弧度转换为度只需要除以 π 再乘以 180 即可。至于从度转换到弧度，则除以 180 再乘以 π 即可。

The C math library (which is automatically made available to Swift) has a constant, M_PI, that represents the value of π as a double. Swift’s strict casting rules make it inconvenient to use this constant when most of the values you’re dealing with are CGFloat, so you can just define your own constant. In GameScene.swift add the following to the top-level of the file, above the class definition:

在 C 的数学库中（它在 Swift 中是自动包含的）有一个常量 M_PI，就代表了一个 π，类型为 Double。Swift 严格的类型转换规则使得这个常量并不是很好用，很多时候这个值需要被转换成 CGFloat，因此最好重新定义一个常量。在 GameScene.swift 的类的定义之外，在文件顶部添加下列声明：

```swift
let Pi = CGFloat(M_PI)
```

Now define another two constants that will make it easy to convert between degrees and radians:

然后定义两个常量，用于在度和弧度之间进行转换：

```swift
let DegreesToRadians = Pi / 180
let RadiansToDegrees = 180 / Pi
```

Finally, edit the rotation code in updatePlayer again, to include the DegreesToRadians multiplier:

接下来在 updatePlayer 方法中修改旋转的代码，引入 DegreesToRadians 常量：

```swift
playerSprite.zRotation = angle - 90 * DegreesToRadians
```

Build and run again and you’ll see that the spaceship is finally rotating correctly.

运行程序，你将看到飞船终于正确地转向了。

### Bouncing Off the Walls
### 从墙壁上弹回

You have a spaceship that you can move using the accelerometers and you’re using trig to make sure it points in the direction it’s flying. That’s a good start.

我们的飞船现在可以用加速计来控制移动了，同时我们通过三角计算让它在飞行的同时保持正确的方向。这开了一个很好头。

Having the spaceship get stuck on the edges of the screen isn’t very satisfying though. You’re going to fix that by making it bounce off the screen borders instead!

让飞船在屏幕边沿卡住不动并不是一个很好的做法。我们它替换成：当它飞到屏幕边缘时，让它反弹回来！

First, delete these lines from updatePlayer():

首先将 upatePlayer() 方法中的这几行删除：

```swift
// 4
newX = min(size.width, max(0, newX))
newY = min(size.height, max(0, newY))
```

替换为：

```swift
And replace them with the following:
var collidedWithVerticalBorder = false
var collidedWithHorizontalBorder = false
 
if newX < 0 {
  newX = 0
  collidedWithVerticalBorder = true
} else if newX > size.width {
  newX = size.width
  collidedWithVerticalBorder = true
}
 
if newY < 0 {
  newY = 0
  collidedWithHorizontalBorder = true
} else if newY > size.height {
  newY = size.height
  collidedWithHorizontalBorder = true
}
```

This checks whether the spaceship hit any of the screen borders, and if so, sets a Bool variable to true. But what to do after such a collision takes place? To make the spaceship bounce off the border, you can simply reverse its velocity and acceleration.
Add the following lines to updatePlayer(), directly below the code you just added:

这段代码片段飞船是否飞到了屏幕的边沿，如果是，将一个布尔变量设置为 true。当这样的碰撞发生后会怎样？让飞船从边缘弹回，你可以直接将速度向量和加速度向量取反。在 updatePlayer() 方法中继续添加：

```swift
if collidedWithVerticalBorder {
  playerAcceleration.dx = -playerAcceleration.dx
  playerVelocity.dx = -playerVelocity.dx
  playerAcceleration.dy = playerAcceleration.dy
  playerVelocity.dy = playerVelocity.dy
}
 
if collidedWithHorizontalBorder {
  playerAcceleration.dx = playerAcceleration.dx
  playerVelocity.dx = playerVelocity.dx
  playerAcceleration.dy = -playerAcceleration.dy
  playerVelocity.dy = -playerVelocity.dy
}
```

If a collision is registered, you invert the acceleration and velocity values, causing the ship to bounce away again.

如果碰撞发生，将加速度和速度反向，让飞船从墙上弹开。

Build and run to try it out.

运行程序，进行测试。

Hmm, the bouncing works, but it seems a bit energetic. The problem is that you wouldn’t expect a spaceship to bounce like a rubber ball – it should lose most of its energy in the collision, and bounce off with less velocity than it had beforehand.

呃，弹是会弹了，只不过看起来有点过于灵敏了。问题是你并不想让飞船像一只橡皮球一样弹来弹去——每次碰撞后它都会消耗掉一些能量，因此经过碰撞之后速度会比之前的要小。

Define another constant at the top of the file, right below the let MaxPlayerSpeed: 

另外定义一个常量，就放在 let MaxPlayerSpeed: CGFloat = 200 之后：

```swift
let BorderCollisionDamping: CGFloat = 0.4
```

Now, replace the code you just added in updatePlayer with this:

现在，将 updatePlayer 方法中刚才新加的代码修改为：

```swift
if collidedWithVerticalBorder {
  playerAcceleration.dx = -playerAcceleration.dx * BorderCollisionDamping
  playerVelocity.dx = -playerVelocity.dx * BorderCollisionDamping
  playerAcceleration.dy = playerAcceleration.dy * BorderCollisionDamping
  playerVelocity.dy = playerVelocity.dy * BorderCollisionDamping
}
 
if collidedWithHorizontalBorder {
  playerAcceleration.dx = playerAcceleration.dx * BorderCollisionDamping
  playerVelocity.dx = playerVelocity.dx * BorderCollisionDamping
  playerAcceleration.dy = -playerAcceleration.dy * BorderCollisionDamping
  playerVelocity.dy = -playerVelocity.dy * BorderCollisionDamping
}
```

You’re now mutliplying the acceleration and velocity by a damping value, BorderCollisionDamping. This allows you to control how much energy is lost in the collision. In this case, you make the spaceship retain only 40% of its speed after bumping into the screen edges.

现在，我们将加速度和速度乘以了一个衰减系数 BorderCollisionDamping。这样就可以让能量在碰撞后有所损失。当飞船撞上屏幕边沿之后只保留原来速度的 40%。

For fun, play with the value of BorderCollisionDamping to see the effect of different values for this constant. If you make it larger than 1.0, the spaceship actually gains energy from the collision!

如果你有兴趣，可以修改 BorderCollisionDamping 的值，看看效果会有什么不同。如果你将值改成大于 1 的数，则飞船甚至可以从碰撞中获得能量！

You may have noticed a slight problem: Keep the spaceship aimed at the bottom of the screen so that it continues smashing into the border over and over, and you’ll see that it starts to stutter between pointing up and pointing down.

你会注意到还有一个小问题：如果你将飞船瞄准屏幕底部，让它反复不停地撞向屏幕边沿，则它会在向上和向下的方向之间打转。

Using the arc tangent to find the angle between a pair of X and Y components works quite well, but only if those X and Y values are fairly large. In this case, the damping factor has reduced the speed to almost zero. When you apply atan2() to very small values, even a tiny change in these values can result in a big change in the resulting angle.

用 arctan 函数计算 x 和 y 组件之间的夹角是 OK 的，但这个 X 和 Y 值必须足够大。在这里，由于衰减系数的存在，速度被降低到接近于 0。当我们用 atan2() 计算飞船小的 x 和 y 值时，一个很小的波动就会导致算出的角度出现非常大的改变。

One way to fix this is to not change the angle when the speed is very slow. That sounds like an excellent reason to give a call to your old friend, Pythagoras.

一个办法是当速度变得很低时，就不要改变角度了。嗯，是该打个电话问候下我们的老朋友毕达哥拉斯（勾股定理的发明者）了。

![](http://cdn3.raywenderlich.com/wp-content/uploads/2013/04/pythagoras-480x177.png)

Right now you don’t actually store the ship’s speed. Instead, you store the velocity, which is the vector equivalent (see here for an explanation of the difference between speed and velocity), with one component in the X-direction and one in the Y-direction. But in order to draw any conclusions about the ship’s speed (such as whether it’s too slow to be worth rotating the ship) you need to combine these X and Y speed components into a single scalar value.

事实上我们保存的并不是飞船的 speed（快慢）。我们保存的是飞船的 velocity (速度)，它是一个向量（关于 speed 和 velocity 的区别，请看[这里](https://en.wikipedia.org/wiki/Velocity#Distinction_between_speed_and_velocity)），速度有两个组件构成，一个 x 方向上的速度，一个 y 方向上的速度。但为了表达最终这个飞船的速度有多快（比如它是否慢到不需要飞船转向），我们需要将速度的 x 组件和 y 组件合并成一个单个的标量值。

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/Pythagoras-405x320.png)

Here you are in the Know 2 Sides, Need Remaining Side case, discussed earlier.

这就是前面我们讲过的“已知三角形两边之长，求第三边之长。”

As you can see, the true speed of the spaceship – how many points it moves across the screen per second – is the hypotenuse of the triangle that is formed by the speed in the X-direction and the speed in the Y-direction.

如图所示，飞船真正的速度是——它每秒钟在屏幕上移动的像素——即屏幕上三角形的斜边，它又是由 x 方向上的速度和 y 方向上的速度构成。

Put in terms of the Pythagorean formula:

使用毕达哥拉斯公式（勾股定理）就是：

true speed = √(playerVelocity.dx<sup>2</sup> + playerVelocity.dy<sup>2</sup>)

真实速度 = √(playerVelocity.dx<sup>2</sup> + playerVelocity.dy<sup>2</sup>)

Remove this block of code from updatePlayer():

从 updatePlayer() 中删除以下代码：

```swift
let angle = atan2(playerVelocity.dy, playerVelocity.dx)
playerSprite.zRotation = angle - 90 * DegreesToRadians
```

And replace it with this:

替换成以下代码：

```swift
let RotationThreshold: CGFloat = 40
 
let speed = sqrt(playerVelocity.dx * playerVelocity.dx + playerVelocity.dy * playerVelocity.dy)
if speed > RotationThreshold {
  let angle = atan2(playerVelocity.dy, playerVelocity.dx)
  playerSprite.zRotation = angle - 90 * DegreesToRadians
}
```

Build and run. You’ll see the spaceship rotation seems a lot more stable at the edges of the screen. If you’re wondering where the value 40 came from, the answer is: experimentation. Putting NSLog() statements into the code to look at the speeds at which the craft typically hit the borders helped in tweaking this value until it felt right :]

运行程序。现在飞船在碰到边缘后的转向变得稳定了。如果你奇怪 40 这个值是怎么来的，我的回答是“经验值”。在代码中通过 "NSLog()" 语句打印飞船撞到墙上的速度值，然后不停地调整这个值，一直到你觉得可以就行了。

### Blending Angles for Smooth Rotation
### 平滑转向

Of course, fixing one thing always breaks something else. Try slowing down the spaceship until it has stopped, then flip the device so the spaceship has to turn around and fly the other way.

但是，解决一个问题的同时又会带来别的问题。让飞船慢慢减速，直至停止。然后翻转设备，让飞船转向并向另一个方向飞行。

Previously, that would happen with a nice animation where you actually saw the ship turning. But because you just added some code that prevents the ship from changing its angle at low speeds, the turn is now very abrupt. It’s only a small detail, but it’s the details that make great apps and games.

如果是在之前，你会看到一个漂亮的转向动画。但因为我们添加了防止飞船在低速下改变方向的代码，现在的转向会变得非常突然。这只是一个小问题，但这个问题关系到我们能否制作出一个好的 App 和游戏。

The fix is to not switch to the new angle immediately, but to gradually blend it with the previous angle over a series of successive frames. This re-introduces the turning animation and still prevents the ship from rotating when it is not moving fast enough.
This “blending” sounds fancy, but it’s actually quite easy to implement. It will require you to keep track of the spaceship’s angle between updates, however, so add a new property for it in the implementation of the GameScene class:

解决办法是不要立马将方向切换到新的角度，而是在每一帧逐步“混合渗入”新角度和旧角度。这种方式不但重新生成了转向动画而且仍然能够防止飞船在低速下转向。“混合渗入”听起来很神奇，但实际上却不难实现。但是它需要你记录下飞船每帧的角度，因此我们要在 GameScene 类中新增一个属性：

```swift
var playerAngle: CGFloat = 0
```

Update the rotation code in updatePlayer() to this:

将 updatePlayer() 中的转向代码修改为：

```swift
let RotationThreshold: CGFloat = 40
let RotationBlendFactor: CGFloat = 0.2
 
let speed = sqrt(playerVelocity.dx * playerVelocity.dx + playerVelocity.dy * playerVelocity.dy)
if speed > RotationThreshold {
  let angle = atan2(playerVelocity.dy, playerVelocity.dx)
  playerAngle = angle * RotationBlendFactor + playerAngle * (1 - RotationBlendFactor)
  playerSprite.zRotation = playerAngle - 90 * DegreesToRadians
}
```

The playerAngle variable combines the new angle and its own previous value by multiplying them with a blend factor. In human-speak, this means the new angle only contributes 20% towards the actual rotation that you set on the spaceship. Over time, more and more of the new angle gets added so that eventually the spaceship points in the correct direction.

playerAngle 变量包含了用混合系数乘以新角度和上一帧的角度。也就是说新的角度只占飞船实际转向的 20% 的份额。随着时间的增长，越来越多的新角度被累加进去，直到飞船最终指向了正确的方向。

Build and run to verify that there is no longer an abrupt change from one rotation angle to another.

运行程序，测试飞船从一个方向转到另一个方向时不会再显得突兀。

![](http://cdn2.raywenderlich.com/wp-content/uploads/2013/03/Rage-end-of-it.png)

Now try flying in a circle a couple of times, both clockwise and counterclockwise. You’ll notice that at some point in the turn, the spaceship suddenly spins 360 degrees in the opposite direction. It always happens at the same point in the circle. What’s going on?

现在，飞出几个圆环，反时针和顺时针都试一试。你会看到在圆环的某些点上，飞船会突然反方向旋转 360°。这种现在总是出现在圆环上的某几个位置。这是怎么回事？

The atan2() returns and angle between +π and –π (between +180 and -180 degrees). That means that if the current angle is very close +π, and then it turns a little further, it’s going to wrap around to -π (or vice-versa).

atan2() 返回一个 +π 到 -π (+180°到-180°)之间的角度。也就是说如果当前角度接近 +π 时，并在转动过程中转过了一小点，那么他会反过来转到 -π（反之亦然）。

That’s actually equivalent to the same position on the circle (just like -180 and +180 degrees are the same point), but your blending algorithm isn’t smart enough to realise that – it thinks the angle has jumped a whole 360 degrees (aka 2π radians) in one step, and it needs to spin the ship 360 degrees in the opposite direction to catch back up.

这两个位置实际上是同一个位置（ -180 和 +180 在圆上是同一个位置），但混合算法还不够智能，没有意识到这点——它认为角度整个改变了 360 度（2π 弧度），因此飞船做了反方向旋转 360°。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2013/03/atan2-range.png)

To fix it, you need to recognize when the angle crosses that threshold, and adjust playerAngle accordingly. Add a new property to the GameScene class:

要解决这个问题，需要知道什么时候角度超过了阀值，并适当地调整 playerAngle。在 GameScene 类中添加一个新属性：

```swift
var previousAngle: CGFloat = 0
```

And change the rotation code one more time to this:

然后再一次修改旋转代码为：

```swift
let speed = sqrt(playerVelocity.dx * playerVelocity.dx + playerVelocity.dy * playerVelocity.dy)
if speed > RotationThreshold {
  let angle = atan2(playerVelocity.dy, playerVelocity.dx)
 
  // did angle flip from +π to -π, or -π to +π?
  if angle - previousAngle > Pi {
    playerAngle += 2 * Pi
  } else if previousAngle - angle > Pi {
    playerAngle -= 2 * Pi
  }
 
  previousAngle = angle
  playerAngle = angle * RotationBlendFactor + playerAngle * (1 - RotationBlendFactor)
  playerSprite.zRotation = playerAngle - 90 * DegreesToRadians
}
```

Now you’re checking the difference between the current angle and the previous angle to watch for changes over the thresholds of 0 and π (180 degrees).

这里，我们判断当前和之前的角度之差，看是否超过了这个阀值：0 到 π（180°）。

Build and run. That’ll fix things right up and you should have no more problems turning your spacecraft!

运行程序。这样飞船的转向就不再有任何问题了。

### Using Trig to Find Your Target
### 用三角学发现目标

This is a great start – you have a spaceship moving along pretty smoothly! But so far the little spaceship’s life is too easy and carefree. Let’s spice things up by adding an enemy: a big cannon!

我们有了一个很好的开始——我们拥有了一艘能够灵活飞行的飞船。但这艘飞船的日子未免也太舒服、太一帆风顺了。给它添点刺激怎么样？我们将为它增加一个敌人：一挺大炮！

Add two new properties to the GameScene class:

在 GameScene 类中加入两个属性：

```swift
let cannonSprite = SKSpriteNode(imageNamed: "Cannon")
let turretSprite = SKSpriteNode(imageNamed: "Turret")
```

You’ll set these sprites up in didMoveToView(). Place this code before the setup for playerSprite, so that the spaceship always gets drawn after (and therefore in front of) the cannon:

我们将在 didMoveToView() 方法中加入这两个角色。将代码放到创建 playSprite 之前，以便在飞船出现之前大炮就已经存在了：

```swift
cannonSprite.position = CGPoint(x: size.width/2, y: size.height/2)
addChild(cannonSprite)
 
turretSprite.position = CGPoint(x: size.width/2, y: size.height/2)
addChild(turretSprite)
```

> Note: Remember that change you made to set skView.ignoresSiblingOrder = false earlier? That ensures that sprites are drawn in the order they are added to their parent. There are other ways to control sprite drawing order – such as using the zPosition – but this is the simplest.
> 
> 注意：还记得我们之前写的 skView.ignoresSiblingOrder=false 一句吗？这句代码让精灵按照它们添加到场景的先后顺序绘制。虽然还可以用别的方式来决定精灵绘制的顺序——比如使用 zPosition 属性——但我们采用的是最简单的方法。

The cannon consists of two sprites: the fixed base, and the turret that can rotate to take aim at the player. Build and run, and you should see a brand-new cannon sitting smack in the middle of the screen.

大炮由两部分构成：一个固定不动的底座，以及一个会旋转瞄向玩家的炮塔。运行程序，你会看到一座全新的大炮坐落在屏幕的中央。

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/Cannon-480x269.png)

Now to give the cannon a target to snipe at!

给大炮一个攻击的目标吧！

You want the cannon’s turret to point at the player at all times. To get this to work, you’ll need to figure out the angle between the turret and the player.

我们想让大炮的炮塔随时都能指向玩家。要达到这个目的，我们需要计算出炮塔和玩家之间的角度。

Figuring this out will be very similar to how you calculated how to rotate the spaceship to face the direction it’s moving in. The difference is that this time, the triangle won’t be derived from the velocity of the spaceship; instead, it will be drawn between the centers of the two sprites:

这个计算和让飞船转向前进方向的计算差不多。不同的是这个三角形不是用飞船的速度来构成，而是用飞船和大炮之间的连线来构成：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2013/03/Triangle-between-cannon-and-player.png)

Again, you can use atan2() to calculate this angle. Add the following method:

我们仍然可以用 atan2() 来计算这个角度。添加一个新方法：

```swift
func updateTurret(dt: CFTimeInterval) {
 
  let deltaX = playerSprite.position.x - turretSprite.position.x
  let deltaY = playerSprite.position.y - turretSprite.position.y
  let angle = atan2(deltaY, deltaX)
 
  turretSprite.zRotation = angle - 90 * DegreesToRadians
}
```

The deltaX and deltaY variables measure the distance between the player sprite and the turret sprite. You plug these values into atan2() to get the relative angle between them.

deltaX 和 deltaY 变量表示了玩家和炮塔之间的距离。将这两个值代入到 atan2() 中，就可以得到它们之间的夹角。

As before, you need to convert this angle to include the offset from the X-axis (90 degrees) so the sprite is oriented correctly. Remember that atan2() always gives you the angle between the hypotenuse and the 0-degree line; it’s not the angle inside the triangle.

同前次一样，我们需要将这个角度偏转到 X 轴方向（90°），以使炮塔的方向正确。注意，atan2() 只会返回一个由斜线和 0 度线构成的夹角，而不是三角形的内角。

Finally, add a call this new method. Find update() and add the following code to the end of that method:

然后来调用这个方法。在 update() 方法中的最后一句加上：

```swift
updateTurret(deltaTime)
```

Build and run. The turret will now always point toward the spaceship. See how easy that was? That’s the power of trig for you!

运行程序，炮塔会自动对着飞船。很简单是吧？这就是三角学的威力！

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/TurretTrackingPlayer-480x269.png)

> Challenge: It is unlikely that a real cannon would be able to move instantaneously – it would have to be able to predict exactly where the target was going. Instead, it would always be playing catch up, trailing the position of the ship slightly.
> 
> 挑战：实际上真正的大炮是不会瞬移的——它实际是预判目标下一个位置在哪里。它总是追赶着目标，略略地尾随着飞船的位置。
> 
> You can accomplish this by “blending” the old angle with the new one, just like you did earlier with the spaceship’s rotation angle. The smaller the blend factor, the more time the turret needs to catch up with the spaceship. See if you can implement this on your own.
> 
> 要实现这个，我们可以用新角度和老角度进行“混合”，正如我们先前在飞船转向的过程中所做的一样。混合系数越小，炮塔瞄准飞船所需要的时间就越长。你可以试一下，看能否独立实现这个功能。

 
### Adding Health Bars
### 加入血槽

In part 2, you’ll add code to let player fire missiles at the cannon, and the cannon will be able to inflict damage on the player. To show the amount of hit points each object has remaining, you will need to add some health bar sprites to the scene. Let’s do that now.

在第二部分，你将实现玩家向大炮开火的功能，而大炮也可以给飞船造成损坏。要显示二者剩余的生命值，我们需要为角色添加血槽。让我们开始吧。

Add the following new constants to the top of the GameScene.swift file:

在 GameScene.swift 中添加如下常量：

```swift
let MaxHealth = 100
let HealthBarWidth: CGFloat = 40
let HealthBarHeight: CGFloat = 4
```

Also, add these new properties to the GameScene class:

在 GameScene 类中加入如下新属性：

```swift
let playerHealthBar = SKSpriteNode()
let cannonHealthBar = SKSpriteNode() 
var playerHP = MaxHealth
var cannonHP = MaxHealth
```

Now, insert the following code into didMoveToView(), just before startMonitoringAcceleration():

在 didMoveToView() 方法中，在 startMonitoringAcceleration() 一句前插入：

```swift
addChild(playerHealthBar)
addChild(cannonHealthBar)

cannonHealthBar.position = CGPoint(
  x: cannonSprite.position.x,
  y: cannonSprite.position.y - cannonSprite.size.height/2 - 10
)
```

The playerHealthBar and cannonHealthBar objects are SKSpriteNodes, but you haven’t specified an image to display for them. Instead, you will be drawing the health bar images dynamically using Core Graphics.

playerHealthBar 和 cannonHealthBar 都是 SKSpriteNode 对象，但我们没有为它们指定任何图片。相反，我们将用 Core Graphics 动态地为它们绘制血槽。

Note that you placed the cannonHealthBar sprite slightly below the cannon, but didn’t assign a position to the playerHealthBar yet. That’s because the cannon never moves, so you can simply set the position of its health bar once and forget about it.

注意，我们将 cannonHealthBar 放到大炮稍下一点的位置，但却没有指定 playerHealthBar 所在的位置。因为大炮不会动，只需要设置一次它的位置就可以了。

Whenever the spaceship moves though, you’ll have to adjust the position of the playerHealthBar as well. That happens in updatePlayer. Add these lines to the bottom of that method:

而飞船是在不停运动着的，我们必须随时修改 playerHealthBar 的位置。这个动作应当在 updatePlayer 中完成。在这个方法的最后加入：

```swift
playerHealthBar.position = CGPoint(
  x: playerSprite.position.x,
  y: playerSprite.position.y - playerSprite.size.height/2 - 15
)
```

Now all that’s left is to draw the bars themselves. Add this new method to the class:

剩下是就是绘制血槽自身了。在这个类中新加一个方法：

```swift
func updateHealthBar(node: SKSpriteNode, withHealthPoints hp: Int) {
 
  let barSize = CGSize(width: HealthBarWidth, height: HealthBarHeight);
 
  let fillColor = UIColor(red: 113.0/255, green: 202.0/255, blue: 53.0/255, alpha:1)
  let borderColor = UIColor(red: 35.0/255, green: 28.0/255, blue: 40.0/255, alpha:1)
 
  // create drawing context
  UIGraphicsBeginImageContextWithOptions(barSize, false, 0)
  let context = UIGraphicsGetCurrentContext()
 
  // draw the outline for the health bar
  borderColor.setStroke()
  let borderRect = CGRect(origin: CGPointZero, size: barSize)
  CGContextStrokeRectWithWidth(context, borderRect, 1)
 
  // draw the health bar with a colored rectangle
  fillColor.setFill()
  let barWidth = (barSize.width - 1) * CGFloat(hp) / CGFloat(MaxHealth)
  let barRect = CGRect(x: 0.5, y: 0.5, width: barWidth, height: barSize.height - 1)
  CGContextFillRect(context, barRect)
 
  // extract image
  let spriteImage = UIGraphicsGetImageFromCurrentImageContext()
  UIGraphicsEndImageContext()
 
  // set sprite texture and size
  node.texture = SKTexture(image: spriteImage)
  node.size = barSize
}
```

This code draws a single health bar. First it sets up the fill and border colors, then it creates a drawing context, and draws two rectangles: the border, which always has the same size, and the bar itself, which varies in width depending on the number of hit points. The method then generates a UIImage from the drawing context, and assigns it as the texture for the sprite.

这段代码绘制了一个血槽。首先设定填充色和边框色，然后创建图形上下文，绘制两个方框：一个用作血槽的边框，它总是固定大小，另一个是血条，它是会变的，要看生命的点数。这个方法从上下文中返回一个 UIImage 并赋给 Sprite 的 texture 属性。

You need to call this method twice, once for the player and once for the cannon. Because redrawing the health bar is relatively expensive (Core Graphics drawing isn’t hardware accelerated), you don’t want to do it every frame. Instead, you’ll call this code only when the player’s or cannon’s health changes. For now, you’ll call it just once to set the initial appearance for the bars.

我们需要调用这个方法两次，一次是针对玩家对象，一次是针对大炮。因为绘制血槽的代价相对昂贵（Core Graphics 绘图不使用硬件加速），因此我们不想在帧刷新时绘制。相反，我们只应该在玩家或者大炮的生命值被改变的时候绘制。暂时，我们只调用它一次，用于显示血槽满血的状态。

Add the following code to the end of didMoveToView:

在 didMoveToView 方法最后加入：

```swift
updateHealthBar(playerHealthBar, withHealthPoints: playerHP)
updateHealthBar(cannonHealthBar, withHealthPoints: cannonHP)
```

Build and run. Now, both the player and the cannon have health bars:

运行程序，现在玩家和大炮都有了血槽：

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/HealthBars-480x269.png)

### Using Trig for Collision Detection
### 用三角学进行碰撞检测

Right now, the spaceship can fly directly through the cannon without consequence. It would be more challenging (and realistic) if it suffered damage when colliding with the cannon. This is where you enter the sphere of collision detection (sorry about the pun! :])

暂时，飞船直接从大炮身上飞过不会导致任何后果。假设让飞船在和大炮发生碰撞后造成一定的伤害，则效果要更刺激（和更真实）一些。现在可以把你扔到碰撞检测范围内试一试了（不好意思，开个玩笑了 :]）。

At this point, a lot of game devs would think, “I need a physics engine!” and while it’s certainly true that you can use Sprite Kit’s physics for this, it’s not that hard to do collision detection yourself, especially if you model the sprites using simple circles.

这里，有许多游戏开发者会说:“我需要使用物理引擎！”。当然，你可以用 Sprite Kit 的物理引擎来做这个，但要自己实现碰撞检测其实一点都不难，尤其是如果你的精灵使用了简单的圆形建模时。

Detecting whether two circles intersect is a piece of cake: all you have to do is calculate the distance between them (*cough* Pythagoras) and see if it is smaller than the sum of the radii (or “radiuses” if you prefer) of both circles.

检测两个圆形是否相交其实很简单：你只需要计算二者之间的距离（*咳咳* 勾股定理），然后判断是否小于二者半径之和（或者两个半径）。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2013/03/Collision-detection.png)

Add two new constants to the top of GameScene.swift:

在 GameScene.swift 顶部加入两个新常量：

```swift
let CannonCollisionRadius: CGFloat = 20
let PlayerCollisionRadius: CGFloat = 10
```

These are the sizes of the collision circles around the cannon and the player. Looking at the sprite, you’ll see that the actual radius of the cannon image in pixels is slightly larger than the constant you’ve specified (around 25 points), but it’s nice to have a bit of wiggle room; you don’t want your games to be too unforgiving, or players won’t have fun.

这是大炮和玩家的碰撞环的大小。查看一下精灵位图，大炮图片的大小实际上要比这里指定的值要略大（25 像素），不过保留一点缓冲空间是好的，我们不准备让这个游戏过于苛求，否则玩家就毫无乐趣可言了。

The fact that the spaceship isn’t circular at all shouldn’t deter you. A circle is often a good enough approximation for the shape of an arbitrary sprite, and it has the big advantage that it makes it much simpler to do the trig calculations. In this case, the body of the ship is roughly 20 points in diameter (remember, the diameter is twice the radius).

事实上，飞船也根本不是圆形也没有关系。对于各种形状的精灵来说，使用圆形模拟都是不错的，而且这样做还有一个好处，即使三角计算更加简单。这里，飞船的直径约 20 像素（直径是半径的两倍）。

Add a new method to the class to do the collision detection:

新增一个方法用于碰撞检测：

```swift
func checkShipCannonCollision() {
 
  let deltaX = playerSprite.position.x - turretSprite.position.x
  let deltaY = playerSprite.position.y - turretSprite.position.y
 
  let distance = sqrt(deltaX * deltaX + deltaY * deltaY)
  if distance <= CannonCollisionRadius + PlayerCollisionRadius {
    runAction(collisionSound)
  }
}
```

You’ve seen how this works before: first you calculate the distance between the X-positions of the two sprites, then the Y-positions. Treating these two values as the sides of a right triangle, you can then calculate the hypotenuse, which is the true distance between these sprites.

首先算出两个精灵间的 x 和 y 距离，将 x 和 y 当成是直角三角形的两条边就可以算出斜边，这就是二者间的直线距离。

If that distance is smaller than the sum of the collision radii, play a sound effect. You’ll see an error on that line will error for now, because you haven’t added the sound effect code yet – it’s coming soon, so just be patient!

如果这个距离小于两个碰撞半径之和，播放生效。这个地方会报一个错误，因为我们还没有实现声效代码——耐心一点，待会实现。

Add a call to this new method at the end of update():

在 update() 最后添加：

```swift
checkShipCannonCollision()
```

Then, add this property to the top of the GameScene class:

在 GameScene 类顶部新加一个属性：

```swift
let collisionSound = SKAction.playSoundFileNamed("Collision.wav", waitForCompletion: false)
```

Time to build and run again. Give the collision logic a whirl by flying the spaceship into the cannon.

运行程序，将飞船飞到炮塔上方测试碰撞逻辑是否正确。

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/Overlap-480x269.png)

Notice that the sound effect plays endlessly as soon as a collision begins. That’s because, while the spaceship flies over the cannon, the game registers repeated collisions, one after another. There isn’t just one collision, there are 60 per second, and it plays the sound effect for every one of them!

注意当碰撞发生时，声效播放起来就没完没了。因为当飞船飞过大炮时，会检测到多次碰撞，一个接一个。不仅仅是一个碰撞，而是每秒 60 次碰撞发生了，而每次碰撞都会播放一次声效。

Collision detection is only the first half of the problem. The second half is collision response. Not only do you want audio feedback from the collision, but you also want a physical response – the spaceship should bounce off the cannon.

碰撞检测只是一方面的问题，另外一方面的问题是碰撞反应。我们不但要在碰撞时播放声效，也想有一个物理反应——飞船会从大炮上弹开。

Add this constant to the top of the GameScene.swift file:

在 GameScene.swift 文件顶部添加一个常量：

```swift
let CollisionDamping: CGFloat = 0.8
```

Then add these lines inside the if statement in checkShipCannonCollision():

然后在 checkShipCannonCollision() 的 if 语句内加入以下语句：

```swift
playerAcceleration.dx = -playerAcceleration.dx * CollisionDamping
playerAcceleration.dy = -playerAcceleration.dy * CollisionDamping
playerVelocity.dx = -playerVelocity.dx * CollisionDamping
playerVelocity.dy = -playerVelocity.dy * CollisionDamping
```

This is very similar to what you did to make the spaceship bounce off the screen borders. Build and run to see how it works.

就像我们让飞船从屏幕边沿弹开一样。运行程序进行测试。

It looks pretty good if the spaceship is going fast when it hits the cannon. But if it’s moving too slowly, then even after reversing the speed, the ship sometimes stays within the collision radius and never makes its way out of it. Clearly, this solution has some problems.

如果飞船在撞上大炮时飞得很快，这个方法没有什么问题。如果速度很慢，哪怕它从反方向弹开，飞船仍然会有一段时间处于碰撞半径之内，甚至再也无法离开。显然，这个办法也有问题。

Instead of just bouncing the ship off the cannon by reversing its velocity, you need to physically push the ship away from the cannon by adjusting its position so that the radii no longer overlap.

如果不将速度取反来弹开飞船，则我们可以通过改变飞船的位置让它离开碰撞半径，真正地将飞船从大炮身上推开，

To do this, you’ll need to calculate the vector between the cannon and the spaceship, which, fortunately, you already calculated earlier in order to measure the distance between them. So how do you use that distance vector to move the ship?

这需要计算大炮和飞船之间的向量，幸运的是，为了计算二者之间的距离，我们已经在前面计算过这个了。那么，如何利用距离向量去移动飞船？

The vector formed by deltaX and deltaY is already pointing in the right direction, but it’s the wrong length. The length you need it to be is the difference between the radii of the ships and its current length – that way, when you add it to the ship’s current position, the ship will no longer be overlapping the cannon.

这个向量由一个 deltaX 和一个 deltaY 构成，并且指向了正确的方向，但它的长度是不对的。我们需要的长度是碰撞半径和当前长度之差——这样，我们将可以将这个长度加到飞船当前位置，飞船就不再和大炮发生交叠了。

The current length of the vector is distance, but the length that you need it to be is:

当前向量的长度是 distance，而我们需要将它的长度变成：

CannonCollisionRadius + PlayerCollisionRadius – distance

So how can you change the length of a vector?

如何改变一个向量的长度？

The solution is to use a technique called “normalization”. You normalize a vector by dividing the X and Y components by the current scalar length (calculated using Pythagoras). The resultant “normal” vector, has an overall length of one.

办法是使用“向量规范化”。通过将向量的 x 和 y 分别除以向量长度（用勾股定理），就可以对这个向量进行规范化。规范化向量之后，向量的长度就变成了 1。

Then, you just multiply the X and Y by the desired length to get the offset for the spaceship. Add the following code immediately underneath the previous lines you added:

然后，将 x 和 y 乘以上面计算出来的长度，就得到飞船需要移动的距离。在上几行代码后面加入：

```swift
let offsetDistance = CannonCollisionRadius + PlayerCollisionRadius - distance

let offsetX = deltaX / distance * offsetDistance
let offsetY = deltaY / distance * offsetDistance
playerSprite.position = CGPoint(
  x: playerSprite.position.x + offsetX,
  y: playerSprite.position.y + offsetY
)
```

Build and run, and you’ll see the spaceship now bounces properly off the cannon.

运行程序，你将发现飞船能够从大炮上正确地弹开了。

To round off the collision logic, you’ll subtract some hit points from the spaceship and the cannon, and update the health bars. Add the following code inside the if statement:

除了碰撞逻辑，我们还需要让飞船和大炮“掉一些血”，并刷新血槽。在 if 语句中加入：

```swift
playerHP = max(0, playerHP - 20)
cannonHP = max(0, cannonHP - 5)
updateHealthBar(playerHealthBar, withHealthPoints: playerHP)
updateHealthBar(cannonHealthBar, withHealthPoints: cannonHP)
```

Build and run again. The ship and cannon now lose a few hit points each time they collide.

运行程序，飞船和大炮发生碰撞后都会损失一些生命点。

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/Damage-480x269.png)

### Adding Some Spin
### 碰撞偏移

For a nice effect, you can add some spin to the spaceship after a collision. This is additional rotation that doesn’t influence the flight direction; it just makes the effect of the collision more profound (and the pilot more dizzy). Add a new constant to the top of GameScene.swift:

为使效果更好看，我们可以让飞船在碰撞后发生一些旋转。这些旋转是额外的，不影响飞行的飞行；仅仅是使碰撞效果更显眼一点（飞行员头会更晕）。在 GameScene.swift 顶部加入一个新常量：

```swift
let PlayerCollisionSpin: CGFloat = 180
```

This sets the amount of spin to half a circle per second, which I think looks pretty good. Now add a new property to the GameScene class:

设置旋转的速度为每秒半圈就足够了。在 GameScene 类中加入一个新属性：

```swift
var playerSpin: CGFloat = 0
```

In checkShipCannonCollision(), add the following line inside the if statement:

在 checkShipCannonCollision() 中，在 if 语句中加入：

```swift
playerSpin = PlayerCollisionSpin
```

Finally, add the following code to updatePlayer(), immediately before the line playerSprite.zRotation = playerAngle - 90 * DegreesToRadians:

然后，在 updatePlayer() 中，就在 playerSprite.zRotation = playerAngle - 90 * DegreesToRadians 一句之前加入：

```swift
if playerSpin > 0 {
 
  playerAngle += playerSpin * DegreesToRadians
  previousAngle = playerAngle
  playerSpin -= PlayerCollisionSpin * CGFloat(dt)
  if playerSpin < 0 {
    playerSpin = 0
  }
}
```

The playerSpin effectively just overrides the display angle of the ship for the duration of the spin, without affecting the velocity. The amount of spin quickly decreases over time, so that the ship comes out of the spin after one second. While spinning, you update previousAngle to match the spin angle, so that the ship doesn’t suddenly snap to a new angle after coming out of the spin.

playerSpin 用于表示碰撞偏移过程中飞船偏移的角度，不计算速度的影响。偏移角度会随时间递减，因此飞船在一秒后停止偏移。在碰撞偏移过程中，我们修改 previousAngle 的值，使其和偏移角度匹配，这样飞船才不会在偏移结束时突然转到一个新的角度。

Build and run and set that ship spinning!

运行程序，查看飞船碰撞偏移的效果。

### Where to Go from Here?
### 接下来做什么

Here is the full example project from the tutorial up to this point.

[这里](http://cdn5.raywenderlich.com/wp-content/uploads/2015/04/TrigBlasterPart1.zip)是教程中使用到的完整示例项目。

Triangles are everywhere! You’ve seen how you can use this fact to breathe life into your sprites with the various trigonometric functions to handle movement, rotation and even collision detection.

一切都是三角形！通过三角函数我们处理移动、旋转和碰撞侦测的问题，从而使我们的精灵具备了生命！

You have to admit, it wasn’t that hard to follow along, was it? Math doesn’t have to be boring if you can apply it to fun projects, such as making games!

我们不得不承认，其实这些并不难学习。数学，如果将它用到有趣的事情上比如制作游戏是，就不会那么索然无味了！

But there’s more to come: in Part 2 of this Trigonometry for Game Programming series, you’ll add missiles to the game, learn more about sine and cosine, and see some other useful ways to put the power of trig to work in your games.

但我们还有更多的内容需要学习：在本教程的第二部分，你将在游戏中加入导弹，学习更多关于 sin 和 cos 的知识，学些更多在游戏中三角学的不同用途。

Credits: The graphics for this game are based on a free sprite set by Kenney Vleugels. The sound effects are based on samples from freesound.org.

声明：游戏中使用的图片来自于 Kenney Vleugels，声音来自于 freesound.org。
