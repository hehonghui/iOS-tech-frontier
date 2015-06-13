在Swift开发中通过UINavigationController来访问Sub-Controllers
===

> * 原文链接 : [Access Sub-Controllers from a UINavigationController in Swift](http://www.andrewcbancroft.com/2015/06/02/access-sub-controllers-from-a-uinavigationcontroller-in-swift/)
* 原文作者 : [Andrew Bancroft](http://www.andrewcbancroft.com)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [samw00](http://www.andrewcbancroft.com/2015/06/02/access-sub-controllers-from-a-uinavigationcontroller-in-swift/) 
* 校对者: 
* 状态 : 校对中

The sequence of accessing a UINavigationController's first child from within the AppDelegate or within prepareForSegue(_:sender:) always gets me. Here are a few quick snippets to help you and I quickly get up and running when working with navigation controllers in our Swift applications:

通过`AppDelegate`或者通过`prepareForSegue(_:sender:)`来访问`UINavigationController`的第一个子元素的访问顺序总是让我有点纳闷。这篇博文中有几段代码可以帮助你和我迅速的弄清楚如何在我们的Swift应用程序开发中和navigation controllers打交道。

##AppDelegate

Every iOS application has one root view controller that’s presented when the app finishes its launch process. Suppose we’re building a navigation controller-based app… that is, we’re building an app where the first (root) view controller is a UINavigationController. In our Storyboard, we’ve set up a Scene with some UI controls with a view controller and some properties, and we’ve embedded that view controller in a navigation controller.

每一个iOS应用在其启动加载完成后都会显示一个根视图控制器。假设我们要搭建一个以导航控制器(navigation controller)为主的一个app...,也就是说我们这个app的第一（根）视图控制器是一个UINavigationController。在我们的Storyboard中，我们创建了一个简单的场景，给一个视图控制器添加了些UI控件和一些属性，然后我们将这个视图控制器嵌入在一个导航控制器中。

What if we want to set some of the view controller’s properties after the app launches? How could we go about doing that?

但是如果我想在app启动之后再去设置视图控制器中的某些个属性呢？我们要如何才能做到这点呢？

I tend to always think of the “first view controller” as the first Scene in the Storyboard where I’ve set up UI components. To iOS, however, the navigation controller is actually the first (or root) view controller.

我一直把我在Storyboard中添加UI要素的第一个场景(first scene)视为“第一视图控制器”。但是，对iOS来说，导航控制器才是实际意义上的第一（根）视图控制器。

When an app incorporates a navigation controller as its first (root) view controller, we end up needing to do a little digging into the view controller hierarchy to get access what we might perceive as the true “first view controller”.

当一个app把导航控制器视为其第一（根）视图器时，我们就需要在视图层级结构中“挖”的更深一点才能访问到我们之前所以为的“第一视图控制器“。

###找寻第一视图控制器

Here is a snippet of how to dig into the UINavigationController's view controller hierarchy to grab the first one and set some fictitious properties on it, all from within the AppDelegate:

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
So the workflow goes like this:

整个流程是这样的：

* Get the window’s root view controller (which is the navigation controller in our case)
* Get the navigation controller’s first view controller from its array of view controllers (which is what I always think of as the “first” view controller)
* Set whatever properties you need to set

* 获取window的根视图控制器（在我们的例子中就是导航控制器）
* 通过导航控制器的视图控制器数组获取其第一个子视图控制器（也就是原先我一直以为的“第一”视图控制器）
* 设置任何你需要设置的属性

You may be worried about the usage of implicitly unwrapped optionals in this snippet. I tend to avoid them wherever possible too, but I used them here because I reasoned that my navigation controller-based app hinges on the fact that the root view controller of the application is a UINavigationController. Something so fundamental to the app warranted my usage of the implicitly unwrapped optionals, since changing the navigation paradigm of the app would probably break things anyway.

在上面这段代码中，你可能担心我用了隐式拆包这方法来看可选值变量是否有值。通常情况下我也是避免用这种方法的，但在这里我用这种方法的理由是我的这个app的根视图控制器就是一个`UINavigationController`，这点确保了我可以使用隐式拆包这种用法。因为不管如何，只要改变了app的导航模式都会出错。

If you’re not convinced by that line of reasoning, no worries – you can switch out some of the ! operators for ? operators and add in some if-let syntax to protect against encountering nil. For example:

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

What about in prepareForSegue(_:sender:)? When would this even be necessary?

那么`prepareForSegue(_:sender:)`又是怎么一回事？什么时候不得不走这条路呢？

Well, suppose that we have an app which segues into a navigation controller. We may need to pass some data off the next view controller, but that “next view controller” is technically the navigation controller, not the controller where our properties are declared.

假设，我们有一个应用跳转进一个导航控制器。我们可能需要给下一个视图控制器传值，但是“下一个视图控制器”严格来说其实是一个导航控制器，并不是我们声明属性的那个控制器。

In similar fashion to the AppDelegate situation, we want to dig into the navigation controller’s view controller hierarchy to access the first child so that we can pass the data along. Here’s an example implementation:

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

The only thing that really changes between the AppDelegate example and the prepareForSegue example is where we obtain the UINavigationController from. In AppDelegate, the navigation controller comes from the window’s root view controller. In prepareForSegue it comes from the segue’s destination view controller.

在`AppDelegate`例子和`prepareForSegue`的例子中唯一改变的就是我们是从哪里如何获取`UINavigationController`的。在`AppDelegate`例子中，导航控制器是来自window的根视图控制器。在`prepareForSegue`中则来自segue的目标视图控制器。

After that, though, the process for grabbing the first child of the navigation controller is the same.

除此之外，获取导航控制器的第一个子元素的步骤都是一样的。

##总结

Accessing a navigation controller’s view controller hierarchy was just vague enough for me to write this little reference for myself, but I hope you benefited from it as well!

如何去访问一个导航控制器的视图控制器层级结构让我觉得有点含糊不清，所以我给我自己写了这篇小博文作为日后的一个参考，但是我希望你也能从这篇博文中受益。
