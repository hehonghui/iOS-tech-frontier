# Swift的响应式编程
---

> * 原文链接 : [Reactive Swift](https://medium.com/swift-programming/reactive-swift-3b6050375534)
* 原文作者 : [Agnes Vasarhelyi](https://medium.com/@vasarhelyia)
* [译文出自 :  开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [Mr.Simple](https://github.com/bboyefiyu) 
* 校对者: []()  
* 状态 :  完成校对

Let’s go back in time to when Apple introduced Swift, the successor to good old Objective-C, it was a very different time. Siri hadn’t yet opened the gate to Hades, Prezi wasn’t available on a monthly subscription, and North Korea hadn’t hacked anyone’s emails. I myself was extremely excited about having a new language on the table, especially one with a background in scripting languages, but still type-safe. Although Swift has grown rapidly ever since, we don’t yet consider it production ready. When that time comes, I want to be familiar with how to deliver clean, testable code…with a reactive UI (if I could have any wish)? Well, read on, if you’re interested in something besides the ordinary Objective-C & MVC world.

让我们首先回到Apple刚推出Objective-C的继任者-Swift的时候，那真是一个非比寻常的时刻。
Siri还没有开启地狱之门，Prezi还没有支持订阅，那时的朝鲜也还没有hack任何人的email。一种新语言的出现让我个人非常兴奋，尤其是这是一种类型安全的脚本语言。虽然Swift还在快速的发展中，但是我们不必担心它是否已经稳定。当那一刻到来之时，我应该已经知道如何交付整洁的、可测试的代码。还要有非常灵活、流畅的UI？如果你对Objective-C 和 MVC之外的东西感兴趣，那么就请继续读下去吧。

## MVC vs. MVVM

Let’s start from zero. When designing an application you probably start by considering the architecture you want to build. Cocoa comes with Model-View-Controller (alias MVC), see my most favourite ever diagram below.

让我们从零开始，当我们设计一个应用时，你可能会先考虑应用的架构。Cocoa框架以[Model-View-Controller](https://developer.apple.com/library/ios/documentation/General/Conceptual/DevPedia-CocoaCore/MVC.html) (也称为 MVC)为基础架构，它的结构如下图所示。

![](https://d262ilb51hltx0.cloudfront.net/max/1600/0*pEvMKHBK9HeAgrYC.)

Although you might not find this architecture efficient for your design, we can see in the picture, the controller rules everything (the clue is in the name). If you have experience with MVC on iOS/Mac, I bet you’ve already seen view controllers responsible for a lot more than you wanted in the first place. The more you work with MVC, the sooner you’ll face the fact that you have to figure out a different way of solving your problems. When it comes to handling network communication methods — which is not exactly black magic nowadays — you’ll end up just like Thomas the Tank Engine, working a big fat controller, responsible for getting the data from all sources and rendering them to the UI at the same time. Even worse, have you ever tried to cover a view controller with unit tests? Painful stuff for sure, having a view involved in tests, testing the presentation logic. With the view’s complexity growing, it’s just not worth the effort. That’s why people usually don’t test view controllers inside MVC.

虽然，你可能发现这个架构并没有让你的设计更有效，从上图中我们可以看到Controller角色的职责太过复杂。如果你有在iOS或者Mac上使用MVC的经验，我打赌你已经领略到了View Controller承担了过多的职责。你使用MVC的经验越多，你就越会想找到另一个方法来解决掉MVC存在的这些问题。当使用MVC处理网络请求时，你会发现这会使得Controller变得极为臃肿，它不仅要处理获取数据的请求，同时也要负责将这些数据渲染到UI上。更蛋疼的是你有尝试过对一个ViewController进行单元测试吗？不堪回首啊！这涉及到View、业务逻辑的测试。随着View的复杂度的增长，它变得越来越难以维护。这就是为什么人们不对MVC进行测试的原因。

[Model-View-ViewModel](http://www.objc.io/issue-13/mvvm.html) (MVVM) though, can be better for your app’s design for a lot of reasons. It comes from Microsoft and is a powerful architectural pattern for event-driven implementations. As you can see, here the view owns the view model, unlike the MVC case.

Model-View-ViewModel (也叫MVVM)对于你的应用来说是一个更好的选择。这是微软发布的一个针对事件驱动实现的、非常强大的架构模式。正如你看到的，与MVC不同的是这里的View持有ViewModel角色。

![](https://d262ilb51hltx0.cloudfront.net/max/1600/0*SUaY1aSgIcvDys9j.)

Naturally, it depends on what are you working on and how you’d like to scale it, but at the end, it means less complexity and better testable code. At the same time? How is that possible? It’s all about moving logic to the view model and reducing the responsibility of the view controller.

当然，这些都依赖于你想如何实现这个模式，不管如何，最终MVVM模式都会给你带来更低的复杂性、更好的可测试性。同时能达到这些效果吗？怎么可能？反正我相信了！它把所有的逻辑移到ViewModel角色中，以此来减少View Controller的职责。( 译者注: 这个模式相当于其他平台的MVP模式，MVVM的ViewModel角色对应这个MVP中的P，也就是Presenter。)

![](http://img.blog.csdn.net/20150507120115027)       

Allow me to use one of my pet projects as an example to introduce you to the concept. It’s the iOS client of a semi-automated brewing machine that helps reach and hold temperature levels during the brewing process (extremely important work). If you want to read more about the brewer check out this article. The point is, we would like to see the temperature — amongst other information — of the current brew all the time within the app.

闪开，让哥用一个项目来演示MVVM模式的使用，这是一个用来控制酒窖气温的半自动iOS客户端项目。如何你想了解更多关于这个项目的细节，可以参考[这篇文章](http://blog.risingstack.com/brewfactory-full-stack-homebrew-with-iot/)。这个项目的关键在于我们能够通过这个App看到酒窖中各个时间段的温度。

Let’s get back to MVVM, and the temperature we were looking for. To be able to continuously read that value, we will use WebSocket with our server and an MVC app for showing it on an iPhone. At first, we need a Brew model with the desired temperature stored in it.

让我们回到MVVM与酒窖的温度上。为了能够读取到温度值，我们需要在我们的服务器上使用WebSocket，使得我们基于MVC架构的应用能够读取它，并且将它显示到iPhone上。首先我们需要建立一个Brew 模型类，这个类中有一个温度字段（ temp ）.

```swift
class Brew {
    var temp = 0.0
}
```

We also need a Brew view controller, showing the temperature of the beer we are currently brewing.

我们还需要建立一个将酒窖温度显示到UI上的ViewController。

```swift
class BrewViewController: UIViewController {
    @IBOutlet weak var tempLabel: UILabel!
}
```

In the old MVC way, we would implement the network logic inside the view controller and update the UI accordingly.

通过MVC的方式，我们需要在ViewController中实现网络请求逻辑、更新UI的操作。

```swift
socket.on("temperature_changed", callback: {(AnyObject data) -> Void in
    self.brew.temp = data[0] as Float
     dispatch_async(dispatch_get_main_queue(), {
       self.updateTempLabel()
     })
})
```

And that’s how we update our UI:

更新UI的函数 : 

```swift
func updateTempLabel() {
       self.brewLabel.text =  NSString(format:"%.2f ˚C", self.brew.temp)
    }
```
    
Well, we have everything in one place now, pretty awesome, isn’t it? No it’s not.

此时，我们成功的把这些代码都放到一个ViewController中了，非常好不是吗？必须不是呐！

Imagine the case when you have to authenticate a user first, or draw live charts, or handle input fields, then what? Check out the complete MVC example. But now check how it looks in the MVVM style:

想象一下这种场景，当你需要先验证用户的身份、绘制一个实时的图表或者处理出入的字段时你如何实现？MVC架构的完整例子在[这里](https://gist.github.com/vasarhelyia/70b2c3fae2bbe38c4a7a)。但是现在，先让我们来看看基于MVVM架构的实现:

```swift
var brew = Brew()
dynamic var temp: Float = 0.0 {
    didSet {
        self.brew.temp = temp
    }
}
```
These are the variables to store inside the view model, they look a bit strange, but I couldn’t see how to make it less weird (and I also wanted to shed a negative light on this solution, maybe I’m kind of biased already). Anyway, let me know how would you do it! Now back to the socket:

上面就是存储在ViewModel中的变量，虽然它们看起来有点陌生，但是我也不知道怎么才能使它们看起来不那么奇怪。不管它啦，我们先来看Socket: 

```swift
socket.on("temperature_changed", callback: {(AnyObject data) -> Void in
   self.temp = data[0] as Float
})
```

So the network logic has moved to the view model. But how to notify the view about the changes in the model? Key-Value Observing (KVO) is our friend. Here’s how our viewDidLoad will look:

这样，网络请求的逻辑就被移到了ViewModel中。但是我们如何修改UI呢？[KVO](http://nshipster.com/key-value-observing/)是我们的好朋友，我们就用它来实现，这里是viewDidLoad中的一个代码片段 : 

```swift
self.brewViewModel.addObserver(self, forKeyPath: "temp", options: .New, context: &brewContext)
```

So we need to observe the temperature somehow. For that, I used a separate field for the view model. You might be asking, why do we even care about having a Brew model inside the view model? I would say then, future Brew model could have different fields (hints on the screenshot above), and you might want to serialize it, send it over the network, or something. We need to have it’s state.

我们需要观察酒窖中的温度，因此，我为View Model添加了一个单独的字段。你可能会问，为什么我们要关心Brew模型是否在View Model中？我要说的是，Brew模型可能会有不同的字段，而你可能需要将它序列化，通过网络传输它，或者其他的一些什么操作。因此，我们需要它的状态。

```swift
override func observeValueForKeyPath(keyPath: String, ofObject object: AnyObject, change: [NSObject : AnyObject], context: UnsafeMutablePointer<Void>) {
        if context == &brewContext {
            self.updateTempLabel((change[NSKeyValueChangeNewKey]) as Float)
        } else {
            super.observeValueForKeyPath(keyPath, ofObject: object, change: change, context: context)
        }
    }
```
    
Wow, that’s.. a lot? There comes a huge amount of boilerplate code with KVO implementation, but it’s still more lightweight than an application written in the MVC style, especially when we think of putting more and more complexity into our application — into our view model. You can read the whole snippet of the MVVM example.

哇噢，这代码太多了？KVO实现产生了很多的样板代码，但是它还是比MVC实现更为轻量级，尤其是当我们考虑添加越来越多的功能到我们的应用中时,这些逻辑就会被移到View Model中。[这里](https://gist.github.com/vasarhelyia/e4fa64096517ae966d01)是MVVM实现的完整代码段。

Anyway, it’s much better, but I still have the feeling we can improve somewhere. Now that we understand that the key for representing mutable models in MVVM’s view is data binding, what can be better at carrying out our bindings than some reactive magic?

总之，MVVM实现好了很多，但是我们还有这还能再优化的感觉。现在，我们知道了展示可变数据模型到MVVM的View上的关键是数据绑定，既然这样，那还有什么比响应式编程适合这种情况？

## Reactive programming
## 响应式编程
“Reactive programming is programming with asynchronous data streams.”
“响应式编程就是以异步数据流的形式进行编程。”

If you are not familiar with this quote, I suggest to read the article about reactive programming by @andrestaltz, it’s a pretty cool introduction. Basically, in the reactive world, everything is a stream, even a stream of streams is a stream. Check out this picture, representing button clicks / touch events as a stream:

如果你对这个概念还不熟悉，我建议你阅读一下[这篇文章](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754) （中文版在这里 : [那些年我们错过的响应式编程](http://www.devtf.cn/?p=174) ），这是一篇非常好的介绍。在响应式编程的世界里，任何东西都是流（ stream ）。下面这幅图就展示了将一个按钮点击或者触摸事件转换为流。

![](https://d262ilb51hltx0.cloudfront.net/max/1600/0*JyZB_1WzJRNIY7o-.)    

Look at our example this way: Consider the socket data as a stream of temperature values and the UILabel listening to it. Transforming them meanwhile is where the functional magic comes into the picture, operators like map, merge, filter and a lot more, amazing! Let’s see how all of these work together in Cocoa.

如果运用响应式编程到我们的项目中，那么情况应该是这样的: 将socket数据看成是温度的流，这个流被UILabel监听着。转换它们的流程也就是上图的函数式编程所展示的那样，map、merge、filter等其他的操作，这个过程非常赞！让我们来看看这些概念如何跟Cocoa一起配合。

## ReactiveCocoa

When we plan to do some reactive programming with Cocoa, the most popular choice is to use ReactiveCocoa (aka RAC) by GitHub. ReactiveCocoa is an FRP inspired powerful data binding framework.

当我们计划在Cocoa上使用响应式编程时，最好的选择就是使用[ReactiveCocoa](https://github.com/ReactiveCocoa/ReactiveCocoa)。ReactiveCocoa是一个受到[函数式编程](http://en.wikipedia.org/wiki/Functional_reactive_programming)启发的数据绑定框架。

It operates with signals, which are defined as push-driven streams. It means, that a signal is a representation of an asynchronous flow that will deliver data or any kind of result to it’s subscribers in the future. In addition to modelling changes with signals, RAC also provides built-in UIKit/AppKit bindings, like rac_textSignal and a lot of other helpful stuff.

它通过信号来进行操作，这些信号被定义为push-driven的流。这意味着，一个信号代表了一个在未来会交付数据或者任意结果到它的观察者的异步工作流。除了数据模型变化会产生一个信号之外，ReactiveCocoa也提供了一些内置的UIKit/AppKit绑定，例如rac_textSignal和其他用用的东西。

You might be slightly unsure when you are planning to start your next project in Swift, and you may want to make it reactive the same time. Don’t be, people at GitHub will help you as they always do. While you are waiting (or contributing), they are planning the 3.0 release called The Great Swiftening, you can still work with 2.4.x as it is actually compatible.

你可能略微地不确定什么时候才会计划启动你的下一个Swift项目，你可能也会想在这个项目中实现响应式编程。无需这样，github上的开发人员会帮助你实现这些功能。他们正在计划发布名为Great Swiftening的ReactiveCocoa 3.0版本，当然你也可以使用2.4.x版本，因为它们也足够稳定。

Except for some tiny details, like having not occupied and alike, you are able to write code with RAC in Swift. Also, Swift has built-in functional operators for its collection types, which comes handy all the time. No need for loops anymore!

Swift在集合类型上也内置了一些函数式操作，它们能很方便的执行某些操作，我们无需对此进行重复地工作。

Let’s see how can we build a ReactiveViewModel for our app, using ReactiveCocoa as the binding system for the MVVM architecture. Our view controller looks a lot better now, see?

让我们看看使用ReactiveCocoa作为MVVM模式的数据绑定系统时我们如何创建一个ReactiveViewModel。看如下代码，我们的View Controller看起来是不是更优雅了？

```swift
self.brewViewModel.tempChangedSignal.map {
            (temp: AnyObject!) -> AnyObject! in
            return NSString(format:"%.2f ˚C", temp as Float)
        } ~> RAC(self.tempLabel, "text")
 }
```
 
The RAC macro used in Objective-C before has been replaced, since C style complex macros are not available in Swift. Thank Yusef Napora for the straightforward solution and @ColinEberhardt for wrapping it up in his sample app.

由于C式的复杂的宏在Swift中不可用，因此用于Objective-C的RAC宏被替换掉了。感谢Yusef Napora提供了一个直截了当的[解决方法](http://napora.org/a-swift-reaction/)，还有一种解决方法是在ColinEberhardt的这个[Sample App](https://github.com/ColinEberhardt/ReactiveSwiftFlickrSearch)中。

Now back to the brewing, it’s so reactive already! Let’s see the other end of the stream.

现在我们回到酒窖气温检测项目中，我们已经准备好了运用响应式编程！我们看看剩下的代码: 

```swift
socket.on("temperature_changed", callback: 
    {(AnyObject data) -> Void in
tempChangedSignal.sendNext(data[0]).deliverOn(RACScheduler.mainThreadScheduler())
    })
```

Here, we are just creating signals of temperature data straight from the socket, and making sure the updating of the UILabel of the view controller will occur safely on the main thread. Ah, dammit, I can’t resist showing another example of how you can leverage the power of RAC. Now it comes with an HTTP request, handling the unfortunate error when trying to initiate a new brew on the server.

在这里，我们仅仅创建了一个温度数据信号，并且确保UILabel的更新会被执行在主线程中。我对于ReactiveCocoa简直没有抵抗力了！现在我们来执行http请求、处理当试图在服务端初始化一个Brew对象时引发的错误，代码如下 :    

```swift
NSURLConnection.sendAsynchronousRequest(request, queue: NSOperationQueue(), completionHandler: {
                (response:NSURLResponse!, data: NSData!, error: NSError!) -> Void in
                if error == nil {
                    subscriber.sendNext(JSON(data))
                    subscriber.sendCompleted()
                } else {
                    subscriber.sendError(error)
                }
})
```

Here we send the received JSON to the subscribers on success and an error signal on failure. Handy, isn’t it?

如果成功我们将接收到的Json数据发送给订阅者，否则调用sendError函数将错误信息发送给订阅者。非常方便，不是吗？

```swift
syncSignal.subscribeError({ (error: NSError!) -> Void in
                UIAlertView(title: "Error creating brew", message: error.localizedDescription, delegate: nil, cancelButtonTitle: "OK").show()
})
```

We just had to subscribe for the error events in the view controller and wait for the magic to happen when we, for example, lost connection.

我们仅仅是需要在ViewController中实现错误处理，然后等待着响应式编程的魔法降临到我们身上，例如 丢失连接。

## What was it all about?
## 总结
It was actually a real life example I brought up. I started with the plain old MVC version of the app, first in Objective-C. It was pretty straightforward, no surprises. Then Swift occurred, and I of course I fall in love with any such innovation whenever I see it and so decided to rewrite the entire app. After struggling a lot with the heavy controllers and my UI refresh mechanism, which I was not exactly proud of, I decided to go reactive. I was already familiar with the fact that it goes well with MVVM, so I gave it a try. The experience was like erasing half of my code. Then, transforming the app with ReactiveCocoa felt like doubling the value of the remaining half. Since then, adding tweaks (like changing a button’s state depending on some data) is basically writing an enabled signal to the button’s command, which is like writing one line of code, almost no effort. Think about how long it would take with the good old way.

酒窖温度监测项目是一个真实的示例,起初我用Objective-C中的MVC实现.这种实现非常简单,却少了份惊喜.后来,Swift出现了,我理所当然地喜欢上这样的创新.因此我决定重写整个App.当我与繁重的控制器以及UI刷新机制斗争了许久之后,这些东西我不是很精通,所以我决定尝试响应式编程.由于我早已知道这与MVVM能够兼容,所以就决定尝试一下.这次经历清除了我一半代码.于是,经过ReactiveCocoa转换的App的价值感觉就像是剩下的代码的价值的两倍.从那时候起,做一些调整(比如根据一些数据改变按钮的状态)仅仅需要编写一行用于发送启用信号到按钮命令的代码,这几乎没有成本.想想使用原来的方式将花费多少时间呐！


There are so many other things you might wanna try out with ReactiveCocoa and with Swift as well. If you are interested in brewing some beer with this combination, or just the code itself, check out the full source of BrewMobile, the iOS client of @brewfactory. Are you aware of a technology to make the app even better, or just more delightful? Let me know! And of course, feel free to contribute.

有很多其他的因素可能使你想尝试ReactiveCocoa和Swift。如果你想对如何酿造啤酒感兴趣，或者只是对这些代码感兴趣，那么你可以这[这里(BrewMobile)](https://github.com/brewfactory/BrewMobile)查看到所有的代码。如果你有更好的技术使得这个App变得更好，请让我知道！当然，也欢迎给我们贡献代码。

And to end with, some insights about the future of BrewMobile, I’m looking forward to introducing React Native to brewing as soon as possible. I will of course share my experiences if React Native turns out to be all I think it might be.

在结束之前，分享一下我关于BrewMobile的未来的一些思考，我正在打算将React Native引入到BrewMobile。如果搞定了我会再次分享我的经验。

## 其他资料

### Swift
* [http://www.objc.io/issue-16/power-of-swift.html](http://www.objc.io/issue-16/power-of-swift.html)
* [https://designcode.io/swift-design](https://designcode.io/swift-design)
* [http://blog.scottlogic.com/2014/07/24/mvvm-reactivecocoa-swift.html](http://blog.scottlogic.com/2014/07/24/mvvm-reactivecocoa-swift.html)
* [http://www.objc.io/books/](http://www.objc.io/books/)

### MVVM模式
[http://www.objc.io/issue-13/mvvm.html](http://www.objc.io/issue-13/mvvm.html)
[http://www.teehanlax.com/blog/model-view-viewmodel-for-ios/](http://www.teehanlax.com/blog/model-view-viewmodel-for-ios/)

### 响应式编程
* [https://gist.github.com/staltz/868e7e9bc2a7b8c1f754](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754)
* [http://blog.risingstack.com/functional-reactive-programming-with-the-power-of-nodejs-streams/](http://blog.risingstack.com/functional-reactive-programming-with-the-power-of-nodejs-streams/)
* [http://rxmarbles.com](http://rxmarbles.com)

### ReactiveCocoa
* [https://github.com/ReactiveCocoa/ReactiveCocoa/blob/master/Documentation/DesignGuidelines.md](https://github.com/ReactiveCocoa/ReactiveCocoa/blob/master/Documentation/DesignGuidelines.md)
* [http://nshipster.com/reactivecocoa/](http://nshipster.com/reactivecocoa/)

### Ghostbusters
* [https://itunes.apple.com/us/movie/ghostbusters/id532285804](https://itunes.apple.com/us/movie/ghostbusters/id532285804)