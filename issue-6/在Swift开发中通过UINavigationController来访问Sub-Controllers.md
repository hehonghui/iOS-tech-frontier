在Swift开发中通过UINavigationController来访问Sub-Controllers
===

> * 原文链接 : [Access Sub-Controllers from a UINavigationController in Swift](http://www.andrewcbancroft.com/2015/06/02/access-sub-controllers-from-a-uinavigationcontroller-in-swift/)
* 原文作者 : [Andrew Bancroft](http://www.andrewcbancroft.com)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [samw00](http://www.andrewcbancroft.com/2015/06/02/access-sub-controllers-from-a-uinavigationcontroller-in-swift/) 
* 校对者: [mrchenhao](https://github.com/mrchenhao) 
* 状态 : 完成

通过`AppDelegate`或者通过`prepareForSegue(_:sender:)`来访问`UINavigationController`的第一个子元素的访问顺序总是让我有点纳闷。这篇博文中有几段代码可以帮助你和我迅速的弄清楚如何在我们的Swift应用程序开发中和navigation controllers打交道。

##AppDelegate

每一个iOS应用在其启动加载完成后都会显示一个根视图控制器。假设我们要搭建一个以导航控制器(navigation controller)为主的一个app...,也就是说我们这个app的第一（根）视图控制器是一个UINavigationController。在我们的Storyboard中，我们创建了一个简单的场景，给一个视图控制器添加了些UI控件和一些属性，然后我们将这个视图控制器嵌入在一个导航控制器中。

但是如果我想在app启动之后再去设置视图控制器中的某些个属性呢？我们要如何才能做到这点呢？

我一直把我在Storyboard中添加UI要素的第一个场景(first scene)视为“第一视图控制器”。但是，对iOS来说，导航控制器才是实际意义上的第一（根）视图控制器。

当一个app把导航控制器视为其第一（根）视图器时，我们就需要在视图层级结构中“挖”的更深一点才能访问到我们之前所以为的“第一视图控制器“。

###找寻第一视图控制器

下面的代码段展示了如果在`UINavigationController`的视图控制器层级中找到第一个控制器并给其设置一些虚构属性，这一次都发生在AppDelegate中：

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {
	var window: UIWindow?
	
	func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
		// Override point for customization after application launch.
	
		let navigationController = window?.rootViewController as! UINavigationController
		let firstVC = navigationController.viewControllers[0] as! NameofFirstViewController
		// set whatever properties you might want to set
		// such as an NSManagedObjectContext reference
	
		return true
	}
	
	// ...
}
```

整个流程是这样的：

* 获取window的根视图控制器（在我们的例子中就是导航控制器）
* 通过导航控制器的视图控制器数组获取其第一个子视图控制器（也就是原先我一直以为的“第一”视图控制器）
* 设置任何你需要设置的属性

在上面这段代码中，你可能担心我用了隐式拆包这方法来看可选值变量是否有值。通常情况下我也是避免用这种方法的，但在这里我用这种方法的理由是我的这个app的根视图控制器就是一个`UINavigationController`，这点确保了我可以使用隐式拆包这种用法。因为不管如何，只要改变了app的导航模式都会出错。

如果上面的理由没有说服你也别担心 － 你可以把`?`换成`!`然后添加一些`if-let`语句来确保你不会碰到`nil`。举个例子：

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {
	
	// ...
	if let navigationController = window?.rootViewController as? UINavigationController {
		if let firstVC = navigationController.viewControllers[0] as? NameOfFirstViewController {
			firstVC.someproperty = someValue
		}
	}
	// ...
}
```

##Prepare for segue

那么`prepareForSegue(_:sender:)`又是怎么一回事？什么时候不得不走这条路呢？

假设，我们有一个应用跳转进一个导航控制器。我们可能需要给下一个视图控制器传值，但是“下一个视图控制器”严格来说其实是一个导航控制器，并不是我们声明属性的那个控制器。

和通过AppDelegate时的情形一样，我们也需要在视图层级中深挖一点来访问第一视图控制器来，这样才能传递我们的数据。下面是一个实现的例子：

```swift
public class ViewController: UIViewController {

	// ...
	override public func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
		let destinationVC = segue.destinationViewController as! UINavigationController
		let nextViewController = destinationVC.viewControllers[0] as! SecondViewController
		
		nextViewController.someProperty = someValue
	}
	// ...
}
```

在`AppDelegate`例子和`prepareForSegue`的例子中唯一改变的就是我们是从哪里如何获取`UINavigationController`的。在`AppDelegate`例子中，导航控制器是来自window的根视图控制器。在`prepareForSegue`中则来自segue的目标视图控制器。

除此之外，获取导航控制器的第一个子元素的步骤都是一样的。

##总结

如何去访问一个导航控制器的视图控制器层级结构让我觉得有点含糊不清，所以我给我自己写了这篇小博文作为日后的一个参考，但是我希望你也能从这篇博文中受益。
