
# Testing view controllers with Quick and Nimble 
# 使用Quick框架和Nimble来测试ViewControler
----------------------------------------------

> * 原文链接 : [Testing view controllers with Quick and Nimble ](https://medium.com/@MarcioK/how-you-can-test-view-controllers-with-quick-nimble-328f895b55ee)
* 译文出自 :  [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [Mr.Simple](https://github.com/bboyfeiyu)
* 校对 : [Lollypo](https://github.com/Lollypo)


View controllers are one of the pillars of your application
architecture, [they are the place where the user interface connects with
the app logic and
model](http://www.objc.io/issue-15/behavior-driven-development.html), on
them it can be sensible pieces of code that if modified could cause a
substantial damage, making it an important place to be tested, although
write tests for view controllers it is still an obscure topic, on this
tutorial I will try to clarify that with an example using [Quick and
Nimble](https://github.com/Quick/Quick).

ViewController是iOS应用开发的支柱之一,[它们是用户界面和业务逻辑、模型的纽带](http://www.objc.io/issue-15/behavior-driven-development.html),即便只是修改了它其中的一点代码也可能会引发严重的问题，因此对ViewController进行测试就变得极为重要。然而，为ViewController写测试代码一直以来都是一个晦涩难懂的话题。这篇文章中，我会尝试使用[Quick和Nimble](https://github.com/Quick/Quick)框架来解开这些疑惑。

#### Quick 和 Nimble 

**Quick** is a [behavior-driven
development](http://en.wikipedia.org/wiki/Behavior-driven_development)
framework for Swift and Objective-C. Inspired by
[RSpec](https://github.com/rspec/rspec),
[Specta](https://github.com/specta/specta), and
[Ginkgo](https://github.com/onsi/ginkgo).

**Quick** 是一个针对于Swift和Objective-C的[行为驱动开发](http://en.wikipedia.org/wiki/Behavior-driven_development)框架. 它的灵感来自于
[RSpec](https://github.com/rspec/rspec),
[Specta](https://github.com/specta/specta), 和
[Ginkgo](https://github.com/onsi/ginkgo).

**Nimble** is a Matcher Framework also for also both languages.
**Nimble** 是一个同时适用于Swift和Objective-C语言的匹配框架.

In another words Quick is a framework for creating
[**BDD**](http://en.wikipedia.org/wiki/Behavior-driven_development)
tests that comes with Nimble that can help to provide more clear
expectations for your tests.

换句话说，Quick是一个用于创建[BDD](http://en.wikipedia.org/wiki/Behavior-driven_development)测试的框架。配合Nimbl，可以为你创建更符合预期目标的测试。



### 示例

The example will be an iOS app written in Swift 1.2, named Pony. The app
has a tab-bar and a view controller as the app intro that will be
prompted when the app starts, the Main.storybard look like this:

这个示例是使用Swift 1.2创建的名为Pony的iOS应用。这个应用含有一个底部栏和一个用于在应用启动时弹出应用简介信息的ViewController。Main.storybard看起来如下图所示 : 

![](https://d262ilb51hltx0.cloudfront.net/max/1600/1*coDmMhq3NgRj0Unl_2EFCg.png)


and running:  

运行后 :     

<video src="https://d262ilb51hltx0.cloudfront.net/max/1600/1*9HuloDCQQ3Ul2t4KGQGkYg.ogv"></video>

( 如果浏览器不支持视频播放,可以手动拷贝地址到浏览器中观看，地址在 https://d262ilb51hltx0.cloudfront.net/max/1600/1*9HuloDCQQ3Ul2t4KGQGkYg.ogv ).

1. 加载应用.
2. 弹出简介信息.


### Setting up Quick and Nimble on Pony app
### 为Pony配置Quick和Nimble

If you have the latest [CocoaPods](http://cocoapods.org) version
installed, you just have to add them in the Podfile:

如果你安装了最新版的[CocoaPods](http://cocoapods.org),你可以将下面的配置代码添加到Podfile中 : 

```
platform :ios, '8.0'
 
source 'https://github.com/CocoaPods/Specs.git'
use_frameworks!
 
target 'PonyTests', :exclusive => true do
  pod 'Nimble', :git => 'https://github.com/Quick/Nimble.git' #, :branch => 'swift-1.1' # if you want to use swift 1.1
  pod 'Quick', :git => 'https://github.com/Quick/Quick.git', # :branch => 'swift-1.1'
end
```


#### Quick File Templates (Optional) 
#### Quick文件模板（可选）

*你可以使用Alcatraz 或者* [*通过
Rakefile手动创建*](https://github.com/Quick/Quick/blob/master/Documentation/InstallingFileTemplates.md)

#### 注意

**Before you start make sure the class is public, e.g:**

**在开始之前，确保你的类是public的, 例如:**

```
public class MyPublicClass {
 
  public var myPublicProperty: String?
  public func myPublicFunc () {
    //... 
  }
}
```

#### 创建一个测试类


If the Quick templates are installed you can just go: create a new file
and select quick template and import the app module, e.g:

如果Quick模板已经安装了，那么你可以选择quick模板、创建一个新的文件，然后import应用模块到文件中，例如 : 

```swift
import Quick
import Nimble
import MyAppModule // Importing the app module
 
class HelloTest: QuickSpec {
    override func spec() {
      //...
    }
}
```

If you are having trouble importing the app module check this:
<http://stackoverflow.com/a/24151067/1970675>

如果你导入应用模块有问题，可以参考这篇文章:
<http://stackoverflow.com/a/24151067/1970675>


### 测试 

#### PonyTabController: UITabBarController 

PonyTabController is a UITabController subclass that is responsible for
presenting the app intro if it hasn’t already and dismiss-it if the
delegate is called.

PonyTabController是UITabController的子类，它的职责是在第一次进入应用时展示应用简介(appIntroViewController)。


```swfit
public class PonyTabController: UITabBarController {
  
  override public func viewDidAppear(animated: Bool) {
    super.viewDidAppear(animated)
    
    let userDefaults = NSUserDefaults.standardUserDefaults()
    
    if !userDefaults.boolForKey("appIntroHasBeenPresented") {
      let storyboard = UIStoryboard(name: "Main", bundle: NSBundle.mainBundle())
      let appIntroViewController = storyboard.instantiateViewControllerWithIdentifier("appIntroViewControllerID") as! AppIntroViewController
        
      appIntroViewController.delegate = self
      
      self.presentViewController(appIntroViewController, animated: true) {
        userDefaults.setBool(true, forKey: "appIntroHasBeenPresented")
      }
    }
  }
}
 
extension PonyTabController: AppIntroDelegate {
    
// MARK: - AppIntroDelegate
    
  public func appIntroDidFinish(appIntro: UIViewController!) {
    // Dismissing app intro
    dismissViewControllerAnimated(true, completion:nil)
  }
}
```

Let’s start with our first expectation on viewDidAppear:

让我们从viewDidAppear中开始 : 

***“When app intro had never been dismissed it should be set as the
appIntroDelegate”***

`待审核`
***“当应用简介从来没有被dismissed时，它会被当做appIntroDelegate设置给PonyTabController”***


```swift
import Quick
import Nimble
import Pony
 
class PonyTabBarControllerSpec: QuickSpec {
  override func spec() {
    var tabBarController: PonyTabController!
    
    describe("viewDidAppear"){
      describe("When app intro had never been dismissed"){
        it("should be set as the appIntroDelegate"){
        }
      }
    }
  }
}
```

Now we have the expectation described but there are some things missing,
there is no assertion or setup for the object that we want to test (an
instance of PonyTabController) and it is not being invoked by any
method. In order to setup that more clearly we can divided the test code
into three groups: **Arrange**, **Act**, **Assert**, for example:


现在我们有了期望的描述信息，但是我们还缺少一些东西。我们还没有断言或者用于测试的对象，也没有任何的测试方法会被调用。为了更简介的进行初始化，我们可以将测试代码分为三个部分，例如 : 

**Arrange:**

**准备工作:**

- Create a instantiate a PonyTabController.
- 创建PonyTabController实例;

**Act:**     
**执行:**

-  Trigger the instance of the PonyTabController lifecycle .
-  触发PonyTabController的生命周期方法;

**Assert:**     
**断言:**

-  tabController should be the appIntroViewController delegate
-  tabController作为appIntroViewController的delegate;

resulting:

完成的测试代码 : 

```swift
import Quick
import Nimble
import Pony
 
class PonyTabBarControllerSpec: QuickSpec {
  override func spec() {
    var tabBarController: PonyTabController!
    
    describe(".viewDidAppear"){
      
      describe("When app intro had never been dismissed"){
      
       var appIntroViewController: AppIntroViewController?
        
        beforeEach{
            // Arrange:
            NSUserDefaults.standardUserDefaults().setBool(false, forKey: "appIntroHasBeenPresented")
            let storyboard = UIStoryboard(name:"Main", bundle: NSBundle.mainBundle())
            tabBarController = storyboard.instantiateInitialViewController() as! PonyTabController
            let window = UIWindow(frame: UIScreen.mainScreen().bounds)
            window.makeKeyAndVisible()
            window.rootViewController = tabBarController
            
            // Act:
            tabBarController.beginAppearanceTransition(true, animated: false) // Triggers viewWillAppear
            tabBarController.endAppearanceTransition() // Triggers viewDidAppear
            appIntroViewController = tabBarController.presentedViewController as! AppIntroViewController!
        }
        
        it("should be set as the appIntroDelegate"){
            // Assert:
            expect(appIntroViewController!.delegate as? PonyTabController).to(equal(tabBarController))
        }
      }
    }
  }
}
```


Organising the test code on this way its best known as the:
“[Arrange-Act-Assert](http://c2.com/cgi/wiki?ArrangeActAssert%5C)”
pattern and some benefits are:

通过[Arrange-Act-Assert](http://c2.com/cgi/wiki?ArrangeActAssert%5C)模式组织测试代码是一种非常好的实践，它的优点如下 : 

-  Clearly separates what is being tested from the setup and
    verification steps.
- 明确区分出初始化与验证步骤中有哪些需要被测试；    
-  Clarifies and focuses attention on a historically successful and
    generally necessary set of test steps.
-  关注点集中在测试历史记录和必须的测试步骤上。   

Now doing the same for:

现在，做同样的操作 : 

“***When app intro had never been dismissed it should be presented****”*

“***当应用简介从来没有被dismissed时，它应该被展示****”*

```swift
import Quick
import Nimble
import Pony
 
class PonyTabBarControllerSpec: QuickSpec {
  override func spec() {
    var tabBarController: PonyTabController!
    
    describe(".viewDidAppear"){
      
      context("When app intro had never been dismissed"){
        
        var appIntroViewController: AppIntroViewController?
        
        beforeEach{
            // Arrange:
            NSUserDefaults.standardUserDefaults().setBool(false, forKey: "appIntroHasBeenPresented")
            let storyboard = UIStoryboard(name:"Main", bundle: NSBundle.mainBundle())
            tabBarController = storyboard.instantiateInitialViewController() as! PonyTabController
            let window = UIWindow(frame: UIScreen.mainScreen().bounds)
            window.makeKeyAndVisible()
            window.rootViewController = tabBarController
            
            // Act:
            tabBarController.beginAppearanceTransition(true, animated: false) // Triggers viewWillAppear
            tabBarController.endAppearanceTransition() // Triggers viewDidAppear
            
            appIntroViewController = tabBarController.presentedViewController as! AppIntroViewController!
        }
        
        it("should be set as the appIntroDelegate"){
            // Assert:
            expect(appIntroViewController!.delegate as? PonyTabController).to(equal(tabBarController))
        }
        
        it("should be presented"){
          // Assert:
          expect(tabBarController.presentedViewController).toEventually(beAnInstanceOf(AppIntroViewController))
        }
      }
    }
  }
}
```

and

“***When app intro had never been dismissed and appIntroDidFinish***
***is*** ***called it should dismiss app intro****”*

“***当应用简介从来没有被dismissed且appIntroDidFinish被调用时应该dismiss应用简介"***



```swift
import Quick
import Nimble
import Pony
 
class PonyTabBarControllerSpec: QuickSpec {
  override func spec() {
    var tabBarController: PonyTabController!
    
    describe(".viewDidAppear"){
      describe("When app intro had not been presented"){
        // ... previous test 
       context("and appIntroDidFinish is called") { // Introducing a context
          
          let userDefaults = NSUserDefaults.standardUserDefaults()
          
          beforeEach {
            // Arrange:
            userDefaults.setBool(false, forKey: "appIntroHasBeenPresented")
            
            // Act:
            // Triggers viewWillAppear and viewDidAppear:animated
            tabBarController.beginAppearanceTransition(true, animated: false)
            tabBarController.endAppearanceTransition()
 
            // - Dismissing app intro.
            tabBarController.appIntroDidFinish(appIntroViewController)
          }
          
          it("should dismiss app intro"){
            // Assert:
            expect(appIntroViewController!.isBeingDismissed()).toEventually(beTrue())
          }
        }
      }
    }
  }
}
```

Little brief about “describe” and “context”:

**Describe**: wrap a set of **tests against one functionality**
**Context:** wrap a set of tests against **one functionality** **under
the same state**.


“describe” 和 “context”的简单介绍:
 
**Describe**: 包装一个功能测试集合;     
**Context:** 在同一状态下包装一个功能测试集合;

Concluding the tests for PonyTabController.

**You can** [**check the full project
here.**](https://github.com/getyourguide/Pony)


**你可以在[这里](https://github.com/getyourguide/Pony)查看该项目完整的代码** 。


### 其他 

Nimble comes with the function
[*waitUntil*](https://github.com/Quick/Nimble#asynchronous-expectations)
where you can execute something inside it closure and call *done()* when
is ready, if *done()* is not called within a second the test will fail,
if you need to extend that interval the function accepts a timeout
parameter where you can specify for how long the function will wait
until it fails. It could be handy when you want to wait for a view
controller to be presented, e.g:

Nimble含有一个[*waitUntil*](https://github.com/Quick/Nimble#asynchronous-expectations)方法，你能够在它里面执行闭包代码，当done函数被调用时表明它已经ready，如果done函数没有被调用，那么第二个测试将会失败。如果你需要扩展定时函数来指定一个超时参数来确定你在执行某个函数时多长时间视为失败。例如，当你想等待一个ViewController被展示时你可以这么处理 : 

```swift
waitUntil{ 
    done in
    tabBarController.presentViewController(viewController,
    			 animated: false){
           done()
   }
}
```
            

### 结论 

- You can use Quick and Nimble to help you achieve more meaningful
    tests with clearer expectation.
- There is a lifecycle that must be followed, i.e: you can’t present a
    view controller if there another already being presented or the view
    is not part of the hierarchy.
- UIKit provide public methods to help trigger view controller states.
- Testing on view controllers can be tricky sometimes, try to keep
    them thin as possible
- 明确测试目标，你可以使用Quick框架和Nimble来实现更有意义的测试;
- 你必须要遵守它的生命周期函数 ，例如如果另一个ViewController正在展示或者视图不是你的视图层级的一部分时你不能展示另一个ViewController ;
- UIKit提供了一些public的函数来帮助你触发view controller 的状态;
- 测试ViewController有时候是相当蛋疼的，尽量保持这些测试代码的简洁性。

### 参考资料 

-  [www.objc.io/issue-15/behavior-driven-development.html](http://www.objc.io/issue-15/behavior-driven-development.html)
-  <http://www.slideshare.net/bgesiak/everything-you-never-wanted>
-  <http://realm.io/news/testing-in-swift/>
-  <https://github.com/Quick/Quick>

### 特别鸣谢

[@eldudi](https://twitter.com/eldudi) 和 
[@banaslee](https://twitter.com/banaslee) 。
