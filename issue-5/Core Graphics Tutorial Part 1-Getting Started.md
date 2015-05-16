## Arcs with UIBezierPath
## 用UIBezierPath来画弧

The next customized view you’ll create is this one:

下一个你要自定义的View就是下面这个：

![1-CompletedCounterView](http://cdn2.raywenderlich.com/wp-content/uploads/2014/12/1-CompletedCounterView.png)


This looks like a filled shape, but the arc is actually just a fat stroked path. The outlines are another stroked path consisting of two arcs.

这个看起来像是一个被填满的弧形，但是这个弧确确实实仅仅是一个粗笔画路径。而轮廓又是另一个由两个弧组成的粗笔画路径。

Create a new file, File\New\File…, choose Cocoa Touch Class, and name the new class CounterView. Make it a subclass of UIView, and ensure the language is Swift. Click Next, and then click Create.
Replace the code with:

新建一个文件，File\New\File...，选择Cocoa Touch Class，然后命名这个类为CounterView。同时，让它为UIView的一个子类，并确认一下编程语言选择的是Swift。点击Next按钮，然后点击创建。然后用下面的代码把生成的代码覆盖掉：

	import UIKit
 
	let NoOfGlasses = 8
	let π:CGFloat = CGFloat(M_PI)
 
	@IBDesignable class CounterView: UIView {
 
 		@IBInspectable var counter: Int = 5 
  		@IBInspectable var outlineColor: UIColor = UIColor.blueColor()
  		@IBInspectable var counterColor: UIColor = UIColor.orangeColor()
 
  		override func drawRect(rect: CGRect) {
  		
 		}
	}
	
Note: Now that Apple allows Unicode characters in constant and variable names, you can use π as a constant for pi that will make your code more readable. You type in π by pressing Alt and P at the same time.

注意：既然Apple允许在常量(let)和变量(var)中使用Unicode编码的字符，你可以把π作为一个常量的名字，用它来储存pi，这样能提高代码的可读性。如何输入π：同时按下Alt和P即可。

Here you create two constants. NoOfGlasses is the target number of glasses to drink per day. When this figure is reached, the counter will be at its maximum.
You also create three @IBInspectable properties that you can update in the storyboard. The variable counter keeps track of the number of glasses consumed, and it’s useful to have the ability to change it in the storyboard, especially for testing the counter view.

在这个代码中，你创建了两个常量。NoOfGlasses 代表着每天需要喝水的目标杯数。当计数到达了这个目标杯数的时候，这个计数器就到了它的最大值。
同时你也创建了3个你可以在storyboard里修改的@IBInspectable属性。这个counter变量用来记录当前已经喝的杯数。对于这个变量，能在storyboard里面修改它是很有必要的，尤其是当测试这个CounterView的时候。

Go to Main.storyboard and add a UIView above the plus PushButtonView.

In the Size Inspector, set X=185, Y=70, Width=230, and Height=230:

打开Main.storyboard然后在“加号”按钮上添加一个UIView

在右边的Size Insepector面板里，设置X=185, Y=70, Width=230, 还有 Height=230：

![1-CounterViewCoords](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-CounterViewCoords.png)

Add the auto layout constraints for the new view similarly to how you did before:
给这个新的view添加几个自动布局的约束，就像你之前做的那样：

- With the view selected, control-drag from the center of the view(原文这里是button，应该是错误的) slightly left (still within the view), and choose Width from the popup menu.
- Similarly, with the view selected, control-drag from the center of the view(同上) slightly up (still within the view), and choose Height from the popup menu.
- Control-drag up from inside the view to outside the view and choose Center Horizontally in Container.
- Control-drag down from the view to the top button, and choose Vertical Spacing.

- 选中这个View,按住control从这个view的中心轻轻的向左边拖动鼠标，然后释放（仍然在这个view内），从弹出来的菜单中选择Width
- 同理，选中这个View，按住control从这个view的中心轻轻的向上面拖动鼠标，然后释放（仍然在这个view内），从弹出来的菜单中选择Height
- 按住control然后从这个view中拖动鼠标到外面的view然后释放，选择Center Horizontally in Container。
- 按住control然后从这个view往下拖动鼠标，一直到上面一个button然后释放，选择Vertical Spacing.

In the Identity Inspector, change the class of the UIView to CounterView. Any drawing that you code in drawRect(_:) will now show up in the view.

在右边的Identity Inspector面板中，把这个UIView所属的类改成CounterView。现在你在drawRect(_:)中的代码将会生效。


## Impromptu Math Lesson
## 温习数学知识

We interrupt this tutorial for a brief, and hopefully un-terrifying look back at high school level math. As Douglas Adams would say – Don’t Panic! :]

我们稍微打断一下这个教程，然后我们回顾一下高中级别的数学知识，千万不要害怕哦！就像Douglas Adams所说 - 不要慌张! :]

Drawing in the context is based on this unit circle. A unit circle is a circle with a radius of 1.0.

在这个context里画任何东西，都基于这个单元圆。一个单元圆就是半径为1的圆。

![1-FloUnitCircle](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-FloUnitCircle.png)

The red arrow shows where your arc will start and end, drawing in a clockwise direction. You’ll draw an arc from the position 3π / 4 radians — that’s the equivalent of 135º, clockwise to π / 4 radians – that’s 45º.

红色的箭头表示你的弧从哪里开始和结束，并且是按顺时针方向。接下来，你将从 3π/4弧度(135º)的位置开始画弧，然后顺时针到 π/4弧度（45º）

Radians are generally used in programming instead of degrees, and it’s useful to be able to think in radians so that you don’t have to convert to degrees every time you want to work with circles. Later on you’ll need to figure out the arc length, which is when radians will come into play.

在程序中，弧度经常被使用，而不是用度数。用弧度是非常有利的，因为你不必每次跟圆打交道的时候都要转换为度数。接下来，你需要计算一下这个弧的长度，当然是用弧度来算。

An arc’s length in a unit circle (where the radius is 1.0) is the same as the angle’s measurement in radians. For example, looking at the diagram above, the length of the arc from 0º to 90º is π/2. To calculate the length of the arc in a real situation, take the unit circle arc length and multiply it by the actual radius.

在单元圆（半径为1）中，弧的长度等于这个角度的用弧度表示的数值。比如说，看上面的哪个图，从0º到90º的弧的长度是 π/2。在真实场景中计算弧的长度，用单位圆的弧的长度乘以真实的圆的半径。

To calculate the length of the red arrow above, you would simply need to calculate the number of radians it spans:

计算上面的红色箭头的长度，只要计算它所跨过的弧度数值。

          2π – end of arrow (3π/4) + point of arrow (π/4) = 3π/2
          
In degrees that would be:

用角度表示就是：

          360º – 135º + 45º = 270º
          
![celebrate-all-the-maths](http://cdn1.raywenderlich.com/wp-content/uploads/2015/01/celebrate-all-the-maths-e1422223051445.png)

## Back to Drawing Arcs
## 回到画弧

In CounterView.swift, add this code to drawRect(_:) to draw the arc:

	// 1
	let center = CGPoint(x:bounds.width/2, y: bounds.height/2)
 
	// 2
	let radius: CGFloat = max(bounds.width, bounds.height)
 
	// 3
	let arcWidth: CGFloat = 76
 
	// 4
	let startAngle: CGFloat = 3 * π / 4
	let endAngle: CGFloat = π / 4
 
	// 5
	var path = UIBezierPath(arcCenter: center,
	  radius: radius/2 - arcWidth/2,
	  startAngle: startAngle,
	  endAngle: endAngle,
	  clockwise: true)
 
	// 6
	path.lineWidth = arcWidth
	counterColor.setStroke()
	path.stroke()


Imagine drawing this with a compass — you’d put the point of the compass in the center, open the arm to the radius you need, load it with a thick pen and spin it to draw your arc.
In this code, center is the point of the compass, the radius is the width that the compass is open (less half the width of the pen) and the arc width is the width of the pen.

想象一下用圆规来画这个，首先把圆规的一个点固定在中间，然后打开圆规臂选择你需要的半径，然后装上一个细港币，然后旋转它来画弧。
在这个代码中，center变量代表着圆规的不动点，半径就是圆规打开的宽度（小于钢笔宽度的一半）并且这个弧的宽度就是这个钢笔笔头的宽度。

The following explains what each section does:
下面的列表解释了上面代码中每个小节的意思：

1. Define the center point of the view where you’ll rotate the arc around.
2. Calculate the radius based on the max dimension of the view.
3. Define the thickness of the arc.
4. Define the start and end angles for the arc.
5. Create a path based on the center point, radius, and angles you just defined.
6. Set the line width and color before finally stroking the path.

---
1. 定义这个view的中点，然后你将会基于这个点来通过旋转生成弧。
2. 通过view的宽和高的最大值来计算弧的半径。
3. 定义这个弧的粗细
4. 为这个弧定义开始和结束的角度。
5. 基于刚刚创建的中点，半径和角度来定义个一个弧的轨道。
6. 在给这个轨道上色之前，设置这个弧的轨道的宽度和颜色。

Note: When you’re drawing arcs, this is generally all you need to know, but if you want to dive further into drawing arcs, then Ray’s (older) Core Graphics Tutorial on Arcs and Paths will help.

注意：当你在尝试画弧的时候，这些一般是你需要知道的，如果你想在画弧方面研究更深层次一点，你可以尝试阅读Raywenderlich老版本的 [Core Graphics Tutorial on Arcs and Paths](http://www.raywenderlich.com/33193/core-graphics-tutorial-arcs-and-paths)

In the storyboard and when you run your application, this is what you’ll see:

在storyboard里或者是你运行你的应用的时候，下面这个就是你可以看到的

![1-SimArcStroke](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-SimArcStroke.png)

## Outlining the Arc
## 如何给弧画轮廓

When the user indicates they’ve enjoyed a glass of water, an outline on the counter shows the progress towards the goal of eight glasses.

当用户表示他们已经享受了一杯圣水之后（=。=），这个计数器外面的线条用来表示进度--离8杯还有多少。

This outline will consist of two arcs, one outer and one inner, and two lines connecting them.
In CounterView.swift , add this code to the end of drawRect(_:):

这个外面的线由两个弧组成，一个外部一个内部，并且还有两条线来闭合他们。
在CounterView.swift里，把下面的代码加在drawRect(_:):的最后面：

	//Draw the outline
 
	//1 - first calculate the difference between the two angles
	//ensuring it is positive
	let angleDifference: CGFloat = 2 * π - startAngle + endAngle
 
	//then calculate the arc for each single glass
	let arcLengthPerGlass = angleDifference / CGFloat(NoOfGlasses)
 
	//then multiply out by the actual glasses drunk
	let outlineEndAngle = arcLengthPerGlass * CGFloat(counter) + startAngle
 
	//2 - draw the outer arc
	var outlinePath = UIBezierPath(arcCenter: center, 
   	                               radius: bounds.width/2 - 2.5,
   	                           startAngle: startAngle, 
   	                             endAngle: outlineEndAngle,
   	                            clockwise: true)
 
	//3 - draw the inner arc
	outlinePath.addArcWithCenter(center, 
   	                  radius: bounds.width/2 - arcWidth + 2.5,
   	              startAngle: outlineEndAngle, 
   	                endAngle: startAngle, 
   	               clockwise: false)
 
	//4 - close the path
	outlinePath.closePath()
 
	outlineColor.setStroke()
	outlinePath.lineWidth = 5.0
	outlinePath.stroke()
	
A few things to go through here:
下面解释一下各个小节代码的意思：

1. outlineEndAngle is the angle where the arc should end, calculated using the current counter value.
2. outlinePath is the outer arc. The radius is given to UIBezierPath() to calculate the actual length of the arc, as this arc is not a unit circle.
3. Adds an inner arc to the first arc. This has the same angles but draws in reverse (clockwise is set to false). Also, this draws a line between the inner and outer arc automatically.
4. Closing the path automatically draws a line at the other end of the arc.

---
1. oulineEndAngle表示这个弧应该结束的角度，用当前的counter的值来计算。
2. outlinePath 就是外面的弧。由于这个弧不是基于单元圆的，所以把半径传给UIBezierPath()这个方法来计算这个弧的真实长度。
3. 在第一个弧的基础上再加一个内部的弧。这个和前面一个弧的角度一样但是是反向的（clockwise的值置为false）。同时，这一段会自动生成一个直线连接内部弧还外部的弧。
4. 在另一个弧的末尾自动画一条直线来关闭整个弧的路径。

With the counter property in CounterView.swift set to 5, your CounterView should now look like this in the storyboard:

因为你在CounterView.swift中将counter属性设置为5，你的CounterView现在应该在storyboard中看起来像下面这样：

![1-ArcOutline](http://cdn5.raywenderlich.com/wp-content/uploads/2014/12/1-ArcOutline.png)

Open Main.storyboard, select the CounterView and in the Attributes Inspector, change the Counter property to check out your drawing code. You’ll find that it is completely interactive. Try adjusting the counter to be more than eight and less than zero. You’ll fix that up later on.

打开 Main.storyboard, 选择CounterView然后在右边的Attributes Insepector里，改变Counter的数值来检查你写在drawRect里的代码是否正确。然后你惊喜的发现这个storyboard里面也跟着改变了，它竟然是可以交互的。尝试改变counter的数值去超过8或者小于0。接下来你会修改这个。

Change the Counter Color to RGB(87, 218, 213), and change the Outline Color to RGB(34, 110, 100).
改变这个Counter的颜色为RGB(87, 218, 213),并且把外面一圈的颜色改为RGB(34, 110, 100)。

![1-CounterView](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-CounterView.png)

## Making it All Work
## 让一切都能够正常工作

Congrats! You have the controls, now all you have to do is wire them up so the plus button increments the counter, and the minus button decrements the counter.

祝贺你！你现在已经能够掌控这些了，现在你需要做的就是点击加号按钮来增加这个计数器的数值，或者是点击减号按钮来减少这个计数器的数值。

In Main.storyboard, drag a UILabel to the center of the Counter View, and make sure it is a subview of the Counter View.

In the Size Inspector, set X=93, Y=93, Width=44, and Height=44:

在Main.storyboard中，拖拽一个UILable控件，放置在CounterView的中间。让它成为CounterView的子View。

然后在右边的Size Inspector中，设置X=93, Y=93, Width=44, 还有Height=44：

![1-LabelCoords](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/1-LabelCoords.png)

In the Attributes Inspector, change Alignment to center, font size to 36 and the default Label title to 8.

在右边的Attributes Inspector，改变Alignment参数为center,font size为36并且默认的Label title为8.

![1-LabelAttributes](http://cdn4.raywenderlich.com/wp-content/uploads/2014/12/1-LabelAttributes.png)

Go to ViewController.swift and add these properties to the top of the class:

打开ViewController.swift然后添加下面的属性到这个类的顶部：

	//Counter outlets
	@IBOutlet weak var counterView: CounterView!
	@IBOutlet weak var counterLabel: UILabel!
	
Still in ViewController.swift, add this method to the end of the class:

还是在ViewController.swift里，添加下面的方法到这个类的最下面。

	@IBAction func btnPushButton(button: PushButtonView) {
  	if button.isAddButton {
   	 counterView.counter++
  	} else {
   	 if counterView.counter > 0 {
   	   counterView.counter--
   	 }
  	}
  	counterLabel.text = String(counterView.counter)
	}
	
Here you increment or decrement the counter depending on the button’s isAddButton property, make sure the counter doesn’t drop below zero — nobody can drink negative water. :] You also update the counter value in the label.

现在，你对计数器的增加或者减少都依赖于按钮的isAddButton属性，确保计数器不会降到0以下-没人会喝负数杯数的水。:] 同时你也要在更新label上面的计数器数值。

Also add this code to the end of viewDidLoad() to ensure that the initial value of the counterLabel will be updated:
把下面这段代码也加入到viewDidLoad()的底部，确保这个counterLabel的初始数值也被赋值到。

	counterLabel.text = String(counterView.counter)
	
In Main.storyboard, connect the CounterView outlet and UILabel outlet. Connect the method to the Touch Up Inside event of the two PushButtonViews.

在Main.storyboard中，连接CounterView outlet和UILabel outlet到ViewController中，同时连接两个按钮的Touch Up Inside事件到ViewController里面的相关方法上。

![1-ConnectingOutlets2](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-ConnectingOutlets2.gif)

Run the application and see if your buttons update the counter label. They should.

运行这个应用，然后看看是否你的按钮能够更新这个label上面的数字，他们应该能行的。

But wait, why isn’t the counter view updating?
等等！为什么这个CounterView上面的进度没有更新？

Think way back to the beginning of this tutorial, and how you only call drawRect(_:) when other views on top of it are moved, or its hidden property is changed, or the view is new to the screen, or your app calls the setNeedsDisplay() or setNeedsDisplayInRect() methods on the view.

回想一下在这个教程的开始，drawRect(_:)方法只有在某些情况下会被调用，比如：遮住它的view被移开的时候，或者是它的hidden属性被改变的时候，或者是这个view第一次出现在屏幕上，或者是你的app调用了setNeedsDisplay()或者setNeedsDisplayInRect()方法。

However, the Counter View needs to be updated whenever the counter property is updated, otherwise the user will think your app is busted.

然而，一旦counter属性改变的时候，CounterView也必须改变它的进度条。否则，用户就会认为你的app毫无用处。

Go to CounterView.swift and change the counter property declaration to:

打开CounterView.swift 然后改变这个counter属性声明为下面的代码：

	@IBInspectable var counter: Int = 5 {
  		didSet {
   	 		if counter <=  NoOfGlasses {
   	   		//the view needs to be refreshed
   	   		setNeedsDisplay()
   	 		}
  		}
	}
	
This code makes it so that the view refreshes only when the counter is less than or equal to the user’s targeted glasses, as the outline only goes up to 8.

这段代码使CounterView仅仅在counter的数值小于或者等于用户的每天目标杯数的时候才会刷新，因为外面的轮廓只会最高到8.

Run your app again. Everything should now be working properly.

再一次运行你的app，一切都正常工作起来。

![1-Part1Finished](http://cdn3.raywenderlich.com/wp-content/uploads/2014/12/1-Part1Finished.png)

## Where to Go From Here?
## 接下来何去何从？

You’ve covered basic drawing in this tutorial, and you should now be able to change the shape of views in your UIs. But wait – there’s more! In Part 2 of this tutorial , you’ll explore Core Graphics contexts in more depth and create a graph of your water consumption over time.

在这个教程你已经学习到了基本的绘图，现在你应该能够在你的UI里改变你的view的形状。但是等等，还有很多需要学习！在这篇教程的[第二部分](http://www.raywenderlich.com/?p=90693)，你将会尝试更深层次探索Core Graphics context并且创造一个记录你的喝水杯数的图表。

You can download the project with all the code up to this point.
If you have any questions or comments please join the forum discussion below.

你可以[下载](http://cdn2.raywenderlich.com/wp-content/uploads/2015/02/Flo-Part1-6.31.zip)这个项目的所有代码，如果你有任何问题和评论，在下面的讨论中提出来。