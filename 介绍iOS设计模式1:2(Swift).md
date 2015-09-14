介绍iOS设计模式 （1/2）
---

>* 原文链接 : [Introducing iOS Design Patterns in Swift – Part 1/2](http://www.raywenderlich.com/86477/introducing-ios-design-patterns-in-swift-part-1)
* 译者 : [Alier Hu](https://github.com/alier1226) 
* 校对者:  

**Update 04/22/2015:** Updated for Xcode 6.3 and Swift 1.2.

**2015/04/22更新：** 最新更新到Xcode6.3和Swfit 1.2。


**Update note:** This tutorial was updated for iOS 8 and Swift by Vincent Ngo. Original post by Tutorial team member Eli Ganem.

**更新日志：** 这个教程由Vicent Ngo最新更新到iOS 8和Swift。原教程由教程组组员Eli Ganem发表。


**iOS Design Patterns** – you’ve probably heard the term, but do you know what it means? While most developers probably agree that design patterns are very important, there aren’t many articles on the subject and we developers sometimes don’t pay too much attention to design patterns while writing code.

**iOS设计模式** － 你很可能听说过这个词，但是你真的知道它什么意思么？虽然大多数开发者都同意设计模式非常重要，但于此同时，并没有很多文章写这个内容，而且我们作为开发者在写代码时也有时不在意设计模式。

Design patterns are reusable solutions to common problems in software design. They’re templates designed to help you write code that’s easy to understand and reuse. They also help you create loosely coupled code so that you can change or replace components in your code without too much of a hassle.

设计模式是在软件设计中常见问题的重复使用的解决方法。它们是可以让你的代码清晰易懂可重复使用度高的模版。它们也可以帮你完成一些松散的代码，你不用很麻烦就可以更改代码或者替换里面的组成部分。


If you’re new to design patterns, then I have good news for you! First, you’re already using tons of iOS design patterns thanks to the way Cocoa is built and the best practices you’re encouraged to use. Second, this tutorial will bring you up to speed on all the major (and not so major) iOS design patterns that are commonly used in Cocoa.

如果你并不熟悉设计模式，那我有一个好消息给你！首先， 由于Cocoa的建立方式，你现在就可以使用很多iOS的设计模式了！其次，这个教程会让你快速学所有的主流（以及不那么主流）的被广泛应用在Cocoa里的iOS设计模式。

In this two-part tutorial, you will create a Music Library app that will display your albums and their relevant information.

教程分为两部分，你将会做一个音乐库的app，它可以显示你的专辑以及它们相关的信息。

In the process of developing this app, you’ll become acquainted with the most common Cocoa design patterns:

在开发这个app的过程中，你将会了解大部分常见的Cocoa设计模式：

* Creational: Singleton.
* 创建型：单例模式（Singleton）
* Structural: MVC, Decorator, Adapter, Facade.
* 结构型：MVC，修饰模式（Decorator），适配器模式（Adapter），外观模式（Facade）
* Behavioral: Observer, and, Memento
* 行为型：观察者模式（Observer），备忘录模式（Memento）

Don’t be misled into thinking that this is an article about theory; you’ll get to use most of these design patterns in your music app. Your app will look like this by the end of the tutorial:

别以为这篇文章是关于理论的；你将会在你的音乐app中知道怎么运用大多数这些设计模式。在本教程最后你的app最终会是这样：

![swiftDesignPattern1](http://cdn2.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern1-179x320.png)

![swift设计模式1](http://cdn2.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern1-179x320.png)

Let’s get started!

让我们开始吧！

## Getting Started

## 开始

Download the [starter project](http://cdn1.raywenderlich.com/wp-content/uploads/2014/11/BlueLibrarySwift-Starter.zip), extract the contents of the ZIP file, and open `BlueLibrarySwift.xcodeproj` in Xcode.

下载并解压 [起始项目](http://cdn1.raywenderlich.com/wp-content/uploads/2014/11/BlueLibrarySwift-Starter.zip), 在xCode里打开 `BlueLibrarySwift.xcodeproj` .

There are three things to note in the project:

在这个项目里，有三件事需要注意：



1. The `ViewController` has two `IBOutlet` connecting the table view and toolbar in storyboard.
`ViewController` 有两个`IBOutlet`连接了表格（table view）和storyboard的工具栏

2. The storyboard has 3 components which are setup with constraints for your convenience. The top component is where the album covers will be displayed. Below the album covers will be a table view which list information related to an album cover. Lastly the tool bar has two buttons, one to undo an action and another to delete an album that you select. The storyboard is shown below: 
![swiftDesignPatternStoryboard](http://cdn5.raywenderlich.com/wp-content/uploads/2014/11/Screen-Shot-2014-11-11-at-12.20.32-AM-480x228.png)

为了让你更方便，storyboard里已经有三个设置好限制的部分了。最上面的部分是显示专辑封面的，在它下面会有一个表格显示对应专辑封面的信息。最后那个工具栏有两个按钮，一个可以撤销操作，另一个可以删除你所选择的专辑。Storyboard如下所示：
![swift设计模式storyboard](http://cdn5.raywenderlich.com/wp-content/uploads/2014/11/Screen-Shot-2014-11-11-at-12.20.32-AM-480x228.png)

3. A starter HTTP Client class (`HTTPClient`) with an empty implementation for you to fill in later.
一个还没写的HTTP Client类 （`HTTPClient	`）需要你之后完成。

**Note**: Did you know that as soon as you create a new Xcode project your code is already filled with design patterns? Model-View-Controller, Delegate, Protocol, Singleton – You get them all for free! :]

**注意：** 你知道当你新建一个Xcode项目时，你的代码已经有设计模式了么？MVC，代理，原型，单例 － 你可以免费得到它们所有！

Before you dive into the first design pattern, you must create two classes to hold and display the album data.

在你深入第一种设计模式之前，你需要先新建两个类来存以及展示专辑数据。

Navigate to `“File\New\File…”` (or simply press `Command+N`). Select `iOS > Cocoa Touch Class` and then click `Next`. Set the class name to `Album` and the subclass to `NSObject`. Lastly choose `Swift` as the language. Click `Next` and then `Create`.

去到`“File\New\File…”`（或者直接按`Command+N`）。选择`iOS > Cocoa Touch Class`然后点击`Next`。将类的名字设置成`Album`，子类设置成`NSObject`。最后选择`Swift`语言。点击`Next` 和`Create`。

Open `Album.swift` and add the following properties to the class definition:

打开`Album.swift`并且在类定义里加上以下这些属性。

```
var title : String!
var artist : String!
var genre : String!
var coverUrl : String!
var year : String!
```

Here you create five properties. The `Album` class will keep track of the title, artist, genre, album cover, and the year of the album.
Next add the following object initializer after the properties:

这样你就建立了五个属性。`Album`类会记录标题，艺术家，类别，专辑封面，以及专辑年份。
接下来在属性下面加上对象初始化方法。

```
init(title: String, artist: String, genre: String, coverUrl: String, year: String) {
  super.init()
  self.title = title
  self.artist = artist
  self.genre = genre
  self.coverUrl = coverUrl
  self.year = year
}
```
This code creates an initializer for the `Album` class. When you create a new album, you’ll pass in the album name, the artist, the genre, the album cover URL, and the year.

这段代码给`Album`类创建了初始化方法。当你创建一个新的专辑时，你需要传给它专辑名称，艺术家，种类，封面URL和年份。

Next add the following method:

接下来加上以下这段代码：

```
override var description: String {
	return "title: \(title)" +
	       "artist: \(artist)" +
	       "genre: \(genre)" +
	       "coverUrl: \(coverUrl)" +
	       "year: \(year)"
}
```

The method `description()` returns a string representation of the album’s attributes.

`description()`方法返回一个代表专辑属性的字符串。

Again, navigate to `File\New\File…`. Select `Cocoa Touch Class` and then click `Next`. Set the class name to `AlbumView`, but this time set the subclass to `UIView`. Make sure the language is set to `Swift` and then click `Next` and then `Create`.

再次去`File\New\File…`，选择`Cocoa Touch Class`并且点击`Next`。将这个类的名称改为`AlbumView`，不过这次将它的子类设置成`UIView	`。确认语言为`Swift`然后点击`Next`和`Create`。

Open `AlbumView.swift` and add the following properties inside the class definition:

打开`AlbumView.swift`，然后在这个类的定义里加上以下属性：

```
private var coverImage: UIImageView! 
private var indicator: UIActivityIndicatorView!
```

The `coverImage` represents the album cover image. The second property is an indicator that spins to indicate activity while the cover is being downloaded.

`coverImage`代表了专辑封面图片。第二个属性是指示物，表明专辑封面正在下载这么一个活动。

The properties are marked as private because no class outside `AlbumView` needs to know of the existence of these properties; they are used only in the implementation of the class’s internal functionality. This convention is extremely important if you’re creating a library or framework for other developers to use to keep private state information private.

这些属性被设置成private因为没有除了`AlbumView`的类需要知道这些属性的存在；它们只需要在这个类的功能里面被用到。当你要建立一个库给别的开发者用，你要把隐藏的状态信息隐藏，这个惯例是非常重要的。

Next, add the initializers to the class:

接下来，给这个类加上初始化方法：

```
required init(coder aDecoder: NSCoder) {
  super.init(coder: aDecoder)
  commonInit()
}
 
init(frame: CGRect, albumCover: String) {
  super.init(frame: frame)
  commonInit()
}
 
func commonInit() {
  backgroundColor = UIColor.blackColor()
  coverImage = UIImageView(frame: CGRect(x: 5, y: 5, width: frame.size.width - 10, height: frame.size.height - 10))
  addSubview(coverImage)
  indicator = UIActivityIndicatorView()
  indicator.center = center
  indicator.activityIndicatorViewStyle = .WhiteLarge
  indicator.startAnimating()
  addSubview(indicator)
}

The `NSCoder` initializer is required because UIView conforms to NSCoding.

`NSCoder`初始化方法是必须的，因为UIView遵守NSCoding.

`commonInit` is a helper method used in both init: that you’ll use in the rest of the app, you set some nice defaults for the album view. You set the background to black, create the image view with a small margin of 5 pixels and create and add the activity indicator.

`commonInit`是一个这两个初始化里都被用到的帮助方法：你将在这个项目的剩余部分也用到它，你将会给专辑界面设置一些好的默认值。把背景设置为黑，建立一个5像素边框的图片界面，然后建立并添加到活动指示器。

Finally, add the following method:

最后，添加一下方法：

```
func highlightAlbum(#didHighlightView: Bool) {
  if didHighlightView == true {
    backgroundColor = UIColor.whiteColor()
  } else {
    backgroundColor = UIColor.blackColor()
  }
}
```

This will toggle the album’s background color to white if it’s highlighted, and black when it’s not.

如果专辑被高亮，它将设置专辑的背景色为白，如果没有被高亮，则被设置成黑。

Build your project (`Command+B`) just to make sure everything is in order. All good? Then get ready for your first design pattern! :]

建造你的项目（`Command+B`）来确认所有东西都正常。都好了？那么就准备好来做你第一个设计模式吧！：］

MVC – The King of Design Patterns
MVC - 设计模式之王

![mvcking](http://cdn5.raywenderlich.com/wp-content/uploads/2013/07/mvcking.png)

Model-View-Controller (MVC) is one of the building blocks of Cocoa and is undoubtedly the most-used design pattern of all. It classifies objects according to their general role in your application and encourages clean separation of code based on role.

MVC是Cocoa中的一个组成部件，它无可厚非是所有设计模式中最常用的。它将对象按照你程序的角色分类，并且根据角色清晰地分离代码。

The three roles are:
这三个角色为：

Model: The object that holds your application data and defines how to manipulate it. For example, in your application the Model is your Album class.

Model: 这个对象存着你程序的数据并且定义怎么控制它。举个例子，在你的程序中，专辑这个类就是Model。

View: The objects that are in charge of the visual representation of the Model and the controls the user can interact with; basically, all the UIView-derived objects. In your application the View is represented by your AlbumView class.

View: 这个对象负责Model的视觉描述以及用户可以交互的控制；总而言之，所有UIVIew有关的对象。在你的程序里，你的AlbumVIew类就代表着View。

Controller: The controller is the mediator that coordinates all the work. It accesses the data from the model and displays it with the views, listens to events and manipulates the data as necessary. Can you guess which class is your controller? That’s right: ViewController.

Controller： Controller 是一个“调解人”，协调所有的工作。它从model里面得到数据，然后在view里面展示它们，监听事件并且如果需要会控制数据。你能猜到哪个类是你的controller么？没错，是ViewController.

A good implementation of this design pattern in your application means that each object falls into one of these groups.

在你的程序中执行一个好的设计模式意味着每个对象都可以被分到其中的一个组里。

The communication between View to Model through Controller can be best described with the following diagram:

通过Controller的View 和Model之间的交流可由下图表示：
![mvc0](http://cdn2.raywenderlich.com/wp-content/uploads/2013/07/mvc0.png)

The Model notifies the Controller of any data changes, and in turn, the Controller updates the data in the Views. The View can then notify the Controller of actions the user performed and the Controller will either update the Model if necessary or retrieve any requested data.

如果数据有变化Model会通知Controller，然后Controller会在View中更新显示数据。View可以通知Controller用户的行为，然后Controller会根据需要更新Model或者得到需要的数据。

You might be wondering why you can’t just ditch the Controller, and implement the View and Model in the same class, as that seems a lot easier.
It all comes down to code separation and reusability. Ideally, the View should be completely separated from the Model. If the View doesn’t rely on a specific implementation of the Model, then it can be reused with a different model to present some other data.

你可能会疑惑为什么你不能直接抛弃Controll二，然后在一个类里面运行View和Model，这样看起来会更简单。这都因为要分离代码以及重复利用性。理想中，View应该完全脱离Model。如果View完全不依赖一个特定的Model，那么它可以在另一个model中再次被利用来展示其他的数据。

For example, if in the future you’d also like to add movies or books to your library, you could still use the same `AlbumView` to display your movie and book objects. Furthermore, if you want to create a new project that has something to do with albums, you could simply reuse your `Album` class, because it’s not dependent on any view. That’s the strength of MVC!

举例说，如果以后你想在你的库中添加电影或者书籍，你依然可以用`AlbumView`来展示你的电影和书籍。更多的是，如果你想新创建一个有关专辑的项目，你还可以重复利用`Album`类，因为它并不依赖于任何View。这就是MVC的厉害之处！

## How to Use the MVC Pattern

## 怎么用MVC模式

First, you need to ensure that each class in your project is either a Controller, a Model or a View; don’t combine the functionality of two roles in one class. You’ve already done a good job so far by creating an `Album` class and an `AlbumView` class.

首先，你需要确保你的项目中的每一个类必须是Controller，Model或者View；不要在一个类里混杂了两个角色的功能。到现在你建立了`Album`类和`AlbumView`类，做得好！

Second, in order to ensure that you conform to this method of work you should create three project groups to hold your code, one for each category.

其次，为了确保你遵守了这种工作的方法，你要创建三个组（group）来放你的代码，每一个种类有一个组。

Navigate to 	`File\New\Group`	 (or press on 	`Command+Option+N`) and name the group `Model`. Repeat the same process to create `View` and `Controller` groups.

选择`File\New\Group` (或者直接按`Command+Option+N`)并且给你的组命名为`Model`。重复这个过程来创建`View` 和 `Controller` 组。

Now drag `Album.swift` to the Model group. Drag `AlbumView.swift` to the View group, and finally drag `ViewController.swift` to the Controller group.

现在把`Album.swift` 拖到Model组。把`AlbumView.swift`拖到View组，最后把`ViewController.swift` 拖到Controller组。

At this point the project structure should look like this:
现在项目应该长这样：

![swiftDesignPattern2](http://cdn4.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern2-259x320.png)

Your project already looks a lot better without all those files floating around. Obviously you can have other groups and classes, but the core of the application is contained in these three categories.
Now that your components are organized, you need to get the album data from somewhere. You’ll create an API class to use throughout your code to manage the data — which presents an opportunity to discuss your next design pattern — the Singleton.

你的项目已经看起来很好了，所有文件都没有到处乱放。很明显你也可以有别的组合类，但是这个程序的核心都在这三个种类里。
现在你项目的组成部分已经很有序了，你需要从某个地方得到专辑的数据。你需要创建一个API类来管理数据，它将在你整个代码中都被使用。这也意味着我们有机会来聊下下一个需要你学习的设计模式－单例。

##The Singleton Pattern

The Singleton design pattern ensures that only one instance exists for a given class and that there’s a global access point to that instance. It usually uses lazy loading to create the single instance when it’s needed the first time.

单例设计模式确保了制定类里只有一个实例并且有一个全局的访问指向这个实例。当它第一次被用的时候，一般是以惰性加载的方式来创建单例。

```
Note: Apple uses this approach a lot. For example: NSUserDefaults.standardUserDefaults(), UIApplication.sharedApplication(), UIScreen.mainScreen(), NSFileManager.defaultManager() all return a Singleton object.
```
```
注：苹果用了很多这个方法。比如：NSUserDefaults.standardUserDefaults(), UIApplication.sharedApplication(), UIScreen.mainScreen(), NSFileManager.defaultManager() 他们都是返回一个单例的对象。
```

You’re likely wondering why you care if there’s more than one instance of a class floating around. Code and memory is cheap, right?

你很可能能想为什么需要关心一个类中是否不止一个实例。代码和内存都很容易获得啊，不是吗？

There are some cases in which it makes sense to have exactly one instance of a class. For example, there’s only one instance of your application and one main screen for the device, so you only want one instance of each. Or, take a global configuration handler class: it’s easier to implement a thread-safe access to a single shared resource, such as a configuration file, than to have many class modifying the configuration file possibly at the same time.

在一些情况下，类里只有一个实例是比较符合逻辑的。比如说， 你的程序里只有一个实例，设备有一个主要的屏幕，所以你只需一个实例。或者处理全局配置类：实现一个线程安全的访问指向到单个分享的资源，比如配置文件，比起很多类可能在同一时间改这个配置文件，会更简单些。

## How to Use the Singleton Pattern

## 单例模式怎么用
Take a look at the diagram below:

看下下图：

![singleton](http://cdn3.raywenderlich.com/wp-content/uploads/2013/08/singleton.png)

The above image shows a Logger class with a single property (which is the single instance), and two methods: `sharedInstance` and `init`.

上面的图显示了一个Logger类有一个属性（一个单例）和两个方法：`sharedInstance` 和 `init`。

The first time a client asks for the sharedInstance, the instance property isn’t yet initialized, so you create a new instance of the class and return a reference to it.

当用户第一次请求sharedInstance，单例属性还没有被初始化，所以你需要先创建一个类的实例，然后返回它的参考（reference）。

The next time you call `sharedInstance`, `instance` is immediately returned without any initialization. This logic ensures that only one instance exists at one time.

下一次你调用`sharedInstance`,`instance`（实例）就将立即返回不再需要其他的初始化。这个逻辑确保了每次只有一个实例存在。

You’ll implement this pattern by creating a singleton class to manage all the album data.

你将通过创建一个实例类来管理全部数据来实现这个模式。

You’ll notice there’s a group called `API` in the project; this is where you’ll put all the classes that will provide services to your app. Create a new file inside this group by right-clicking the group and selecting `New File`. Select `iOS > Cocoa Touch Class` and then click `Next`. Set the class name to `LibraryAPI`, and the subclass to `NSObject`. Lastly choose `Swift` as the language. Click `Next` and then `Create`.

你讲发现，在这个项目中有一个组叫`API`；这就是你将把所有给你程序提供服务的类放置的地方。右键这个组然后选择`New File`来在这个组里创建一个新的文件。选`iOS > Cocoa Touch Class` 然后点击`Next`。将这个类的名字设置为`LibraryAPI`, 子类为 `NSObject`。最后选择 `Swift`作为编程语言，点击`Next` 然后点 `Create`。

Now go to `LibraryAPI.swift` and insert this code inside the class definition.

现在去`LibraryAPI.swift` 然后把这段代码添加到类定义里。

```
//1
class var sharedInstance: LibraryAPI {
  //2
  struct Singleton {
    //3
    static let instance = LibraryAPI()
  }
  //4
  return Singleton.instance
}
```

There’s a lot going on in this short method:

这个很短的方法里干了很多事

1. Create a class variable as a computed type property. The class variable, like class methods in Objective-C, is something you can call without having to instantiate the class `LibraryAPI` For more information about type properties please refer to Apple’s Swift documentation on
properties

它首先创建了一个类变量为计算类型属性。这个类变量，就是OC里的类方法，是一个不需要实例化`LibarayAPI`类就可以调用的东西。想要更多关于类型属性的信息，请去参考Apple Swift关于属性的文档。

2. Nested within the class variable is a struct called `Singleton`.

在这个类变量里嵌入一个结构称为`Singleton`

3. `Singleton` wraps a static constant variable named instance. Declaring a property as `static` means this property only exists once. Also note that static properties in Swift are implicitly lazy, which means that `Instance` is not created until it’s needed. Also note that since this is a constant property, once this instance is created, it’s not going to create it a second time. This is the essence of the Singleton design pattern. The initializer is never called again once it has been instantiated.

`Singleton`包含了一个叫instance的静态常量。把一个属性声明为`static`(静态)意味着这个属性只出现一次。同时也要注意静态属性在Swift中为绝对惰性，当它被创造以后，它不会再一次被创造。这也是单例设计模式必须的，当它被初始化过后，这个初始化方法永远不会再被调用。

4. Returns the computed type property.
返回计算类型属性

```
**Note**: To learn more about different ways to create a singleton in Swift refer to this: [Github page](https://github.com/hpique/SwiftSingleton)
```

```
**注意`**: 想要了解在swift中更多创建一个单例的方法，请去[GitHub网页]（https://github.com/hpique/SwiftSingleton）
```

You now have a Singleton object as the entry point to manage the albums. Take it a step further and create a class to handle the persistence of your library data.

你现在又了一个单例对象作为管理专辑的开始。让我更进一步然后创建一个类来处理你的库里数据的永久性。

Now within the group `API` create a new file. Select `iOS > Cocoa Touch class` and then click `Next`. Set the class name to PersistencyManager, and make it a subclass of `NSObject`.

现在在`API`组里创建一个新文件。选`iOS > Cocoa Touch class`然后点击`Next`。把这个类的名字改成PersistencyManager，然后把它弄成`NSObject`的子类。

Open `PersistencyManager.swift` Add the following code inside the curly braces

打开`PersistencyManager.swift`然后将下面的代码放到大括号里。

```
private var albums = [Album]()
```

Here you declare a private property to hold album data. The array is mutable, so you can easily add and delete albums.

现在你声明了一个私有属性来放置专辑的数据。这个数组是可变的，你可以很方便的添加或者删除专辑。

Now add the following initializer to the class:

现在讲下面的初始化方法添加到类：

```
override init() {
  //Dummy list of albums
  //事先写死的专辑列表
  let album1 = Album(title: "Best of Bowie",
         artist: "David Bowie",
         genre: "Pop",
         coverUrl: "http://www.coversproject.com/static/thumbs/album/album_david%20bowie_best%20of%20bowie.png",
         year: "1992")
 
  let album2 = Album(title: "It's My Life",
         artist: "No Doubt",
         genre: "Pop",
         coverUrl: "http://www.coversproject.com/static/thumbs/album/album_no%20doubt_its%20my%20life%20%20bathwater.png",
         year: "2003")
 
  let album3 = Album(title: "Nothing Like The Sun",
         artist: "Sting",
         genre: "Pop",
         coverUrl: "http://www.coversproject.com/static/thumbs/album/album_sting_nothing%20like%20the%20sun.png",
         year: "1999")
 
  let album4 = Album(title: "Staring at the Sun",
         artist: "U2",
         genre: "Pop",
         coverUrl: "http://www.coversproject.com/static/thumbs/album/album_u2_staring%20at%20the%20sun.png",
         year: "2000")
 
  let album5 = Album(title: "American Pie",
         artist: "Madonna",
         genre: "Pop",
         coverUrl: "http://www.coversproject.com/static/thumbs/album/album_madonna_american%20pie.png",
         year: "2000")
 
  albums = [album1, album2, album3, album4, album5]
}

```

In the initializer, you’re populating the array with five sample albums. If the above albums aren’t to your liking, feel free to replace them with the music you enjoy. :]

在这个初始化方法里，你往数组里添加了五个样本专辑。如果你不喜欢上面的专辑，你也可以自己换成你喜欢的音乐：］

Now add the following functions to the class:

现在把下面的方法添加到类：

```
func getAlbums() -> [Album] {
  return albums
}
 
func addAlbum(album: Album, index: Int) {
  if (albums.count >= index) { 
    albums.insert(album, atIndex: index)
  } else {
    albums.append(album)
  }
}
 
func deleteAlbumAtIndex(index: Int) {
  albums.removeAtIndex(index)
}
```

These methods allow you to get, add, and delete albums.

这些方法让你可以得到，添加以及删除专辑。

Build your project just to make sure everything still compiles correctly.

运行你的项目来确保所有东西都能正常编译。

At this point, you might wonder where the `PersistencyManager` class comes in since it’s not a Singleton. You’ll see the relationship between `LibraryAPI` and `PersistencyManager` in the next section where you’ll look at the `Facade` design pattern.

到现在这个时候，你可以会想什么时候为什么`PersistencyManager`会介入因为它并不是一个单例。你将在下一个章节中看到`LibraryAPI` 与 `PersistencyManager`之间的关系，下一个章节是关于外观模式的。

#The Facade Design Pattern
#外观设计模式

![facade](http://cdn1.raywenderlich.com/wp-content/uploads/2013/07/facade.jpg)

The Facade design pattern provides a single interface to a complex subsystem. Instead of exposing the user to a set of classes and their APIs, you only expose one simple unified API.
外观设计模式给复杂的子系统提供一个借口。你只会有一个整合的API暴露给你，而不是一堆类和它们各自的API。

The following image explains this concept:
下面这个图解释了这个概念：

![facade2](http://cdn1.raywenderlich.com/wp-content/uploads/2013/07/facade2-480x241.png)

The user of the API is completely unaware of the complexity that lies beneath. This pattern is ideal when working with a large number of classes, particularly when they are complicated to use or difficult to understand.

API的用户并不完全知道API有多复杂。这个模式是当要用到大量类时，特别是那些非常复杂难懂难用的，非常理想好用的。

The Facade pattern decouples the code that uses the system from the interface and implementation of the classes you’re hiding; it also reduces dependencies of outside code on the inner workings of your subsystem. This is also useful if the classes under the facade are likely to change, as the facade class can retain the same API while things change behind the scenes.

外观模式将那些从接口处用系统的代码和你要隐藏的执行类的代码分离开；它并且减少你子系统内部对外面代码的依赖性。如果外观模式下的代码很有可能变，那么这个就非常有用，因为外观类可以保持同样的CPI当其它东西在幕后变化。

For example, if the day comes when you want to replace your backend service, you won’t have to change the code that uses your API as it wont’ change.

举个例子，如果你有一天想要换你的后端服务，你并不需要改变你的代码因为它的API并不会改变。

##How to Use the Facade Pattern
##怎么用外观模式

Currently you have `PersistencyManager `to save the album data locally and `HTTPClient` to handle the remote communication. The other classes in your project should not be aware of this logic, as they will be hiding behind the facade of `LibraryAPI`.

现在你有`PersistencyManager `来本地储存专辑数据，和`HTTPClient` 来处理远端通讯。在你的项目中，其它的类不应该知道这个逻辑，因为它们会被`LibraryAPI`隐藏在外观后。

To implement this pattern, only `LibraryAPI` should hold instances of `PersistencyManager` and `HTTPClient`. Then, `LibraryAPI` will expose a simple API to access those services.

为了实现这个模式，只有`LibraryAPI`拥有`PersistencyManager` 和 `HTTPClient`。并且，`LibraryAPI`会暴露给一个简单的API使它可以获得这些服务。

The design looks like the following:

这个模式长这样：

![design-patterns-facade-uml](http://cdn2.raywenderlich.com/wp-content/uploads/2013/08/design-patterns-facade-uml-480x71.png)

`LibraryAPI` will be exposed to other code, but will hide the `HTTPClient` and `PersistencyManager` complexity from the rest of the app.

`LibraryAPI`会暴露给其他的代码，但是会对app中其它部分隐藏`HTTPClient` 和`PersistencyManager`的复杂性。

Open `LibraryAPI.swift` and add the following constant properties to the class:
打开 `LibraryAPI.swift`然后添加下面这些常量属性到类里：

```
private let persistencyManager: PersistencyManager
private let httpClient: HTTPClient
private let isOnline: Bool
```

`isOnline` determines if the server should be updated with any changes made to the albums list, such as added or deleted albums.
You now need to initialize these variables via `init`. Add the initializer to the class next:
`isOnline` 决定了当专辑列表有任何改变时，比如添加或删除专辑，服务器是否要更新。
你现在需要通过`init`初始化这些变量。将初始化方法添加到类：

```
override init() {
  persistencyManager = PersistencyManager()
  httpClient = HTTPClient()
  isOnline = false
 
  super.init()
}
```

The HTTP client doesn’t actually work with a real server and is only here to demonstrate the usage of the facade pattern, so `isOnline` will always be `false`.
HTTP Client 并不真正与真实的服务器工作而且只在这里需要声明外观模式的运用，所以`isOnline`永远是否（false）。

Next, add the following three methods to `LibraryAPI.swift`:
然后，将下面三个方法添加到 `LibraryAPI.swift`：

```
func getAlbums() -> [Album] {
  return persistencyManager.getAlbums()
}
 
func addAlbum(album: Album, index: Int) {
  persistencyManager.addAlbum(album, index: index)
  if isOnline {
    httpClient.postRequest("/api/addAlbum", body: album.description)
  }
}
 
func deleteAlbum(index: Int) {
  persistencyManager.deleteAlbumAtIndex(index)
  if isOnline {
    httpClient.postRequest("/api/deleteAlbum", body: "\(index)")
  }
}
```

Take a look at `addAlbum(_:index:)`. The class first updates the data locally, and then if there’s an internet connection, it updates the remote server. This is the real strength of the Facade; when some class outside of your system adds a new album, it doesn’t know — and doesn’t need to know — of the complexity that lies underneath.
看下`addAlbum(_:index:)`。这个类首先本地更新数据，如果有网络连接，它再更新远程服务器。这才是外观模式真正的厉害之处；当你的系统之外的什么类添加了一个专辑，它并会不知道－也不需要知道－其底下的复杂性。

```
**Note**: When designing a Facade for classes in your subsystem, remember that nothing prevents the client from accessing these “hidden” classes directly. Don’t be stingy with defensive code and don’t assume that all the clients will necessarily use your classes the same way the Facade uses them.
```

```
**注：** 当给你的子系统的类设计一个外观模式时，记住什么都不能阻止客户直接进入那些被“隐藏”的类。不要吝啬地加上防守的代码，也不要假设所有客户都会像外观模式那样用你的类。
```

Build and run your app. You’ll see two empty views, and a toolbar. The top view will be used to display your album covers, and the bottom view will be used to display a table of information related to that album.

编译运行你的app。你应该会看到两个空的界面和一个工具栏。上面的界面会用来展示你的专辑封面，下面那个界面将用来展示专辑相关信息的表格。

![designPatternInitialScreen](http://cdn3.raywenderlich.com/wp-content/uploads/2014/11/Screen-Shot-2014-11-11-at-12.33.50-AM-179x320.png)

You’ll need something to display the album data on screen — which is a perfect use for your next design pattern: the Decorator.

你现在需要一些可以展示专辑数据到屏幕上的东西－是一个好机会用你下一个设计模式了：修饰模式。

#The Decorator Design Pattern
#修饰设计模式

The Decorator pattern dynamically adds behaviors and responsibilities to an object without modifying its code. It’s an alternative to subclassing where you modify a class’s behavior by wrapping it with another object.
In Swift there are two very common implementations of this pattern: `Extensions` and `Delegation`.

修饰模式动态添加行为和责任到一个对象而不改变它的代码。这是一个用来创建子类的另一种方法：当你通过把一个类包装到另一个对象来更改类的时候。在swift中有两个非常常见的方法来实施这个模式：`Extensions` 和 `Delegation`

##Extensions
##扩建

Adding extensions is an extremely powerful mechanism that allows you to add new functionality to existing classes, structures or enumeration types without having to subclass. What’s also really awesome is you can extend code you don’t have access to, and enhance their functionality. That means you can add your own methods to Cocoa classes such as UIView and UIImage!

添加一个扩建是非常有用的技巧，它使你可以给现有类、结构、枚举类型添加新功能而不用让它们成为子类。这个的绝妙之处在于你可以扩展你的那些你没有权限的代码，并且增加它们的功能性。这个意味着你可以Cocoa类里，比如UIView和UIImage里加入你自己的方法。

For example, new methods that are added at compile time can be executed like normal methods of the extended class. It’s slightly different from the classic definition of a decorator, because a extension doesn’t hold an instance of the class it extends.

比如说，在编译时添加的新方法可以想扩展类里的普通方法一样被执行。这个与典型的修饰模式还是有一点点的区别，因为一个扩建不不拥有它扩建的那个类的实例。

##How to Use Extensions
## 怎么用扩建

Imagine a situation in which you have an Album object that you want to present inside a table view:

想象一下当你有一个专辑对象，然后你想在表单里展示这个。

![swiftDesignPattern3](http://cdn2.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern3-480x262.png)

Where will the album titles come from? `Album` is a Model object, so it doesn’t care how you present the data. You’ll need some external code to add this functionality to the Album class, but without modifying the class directly.

专辑标题从哪来呢？`Album`是一个在model里的对象，所以它不关心你怎么展示数据。你只需要有一些外部的代码来添加功能到Album类而不用直接更改这个类。

You’ll create a extension that will extend the Album class; it will define a new method that returns a data structure which can be used easily with `UITableView`.

你首先创建一个扩展Album类的扩建，它会定义一个新方法，这个方法会返回一个方便与`UITableView`用的数据结构。

The data structure will look like the following:

这个数据结构看上去像下面这样：

![delegate2](http://cdn5.raywenderlich.com/wp-content/uploads/2013/08/delegate2-480x67.png)

To add a Extensions to Album, navigate to `File\New\File… `and select the `iOS > Source > Swift File` template, and then click `Next`. Enter `AlbumExtensions` and click `Create`.

想要给Album添加一个扩展，去到`File\New\File… `然后选择 `iOS > Source > Swift File` 模版, 然后点击 `Next`. 键入 `AlbumExtensions` 然后点击 `Create`

Go to `AlbumExtensions.swift` and add the following extension:
去`AlbumExtensions.swift`然后添加下面的扩展：

```
extension Album {
  func ae_tableRepresentation() -> (titles:[String], values:[String]) {
    return (["Artist", "Album", "Genre", "Year"], [artist, title, genre, year])
  }
}
```

Notice there’s a` ae_ `at the beginning of the method name, as an abbreviation of the name of the extension: `AlbumExtension`. Conventions like this will help prevent collisions with methods (including possible private methods you might not know about) in the original implementation.

注意在这个方法名字的开头有一个`ae_`，这是`AlbumExtension`的缩写。这样的规则会避免在原始执行中的方法有冲突（包括那些你可以不知道的私有的方法）。

```
Note: Classes can of course override a superclass’s method, but with extensions you can’t. Methods or properties in an extension cannot have the same name as methods or properties in the original class.
```

```
注意：类当然可以是父类的方法无效，不过扩展的话是不可以的。扩展里方法和属性是不能与原先的类里的方法和属性有同样的名字。
```

Consider for a moment how powerful this pattern can be:

想一下这个模式有多么的厉害：

* You’re using properties directly from `Album`.
* You have added to the `Album` class but you haven’t subclassed it. If you need to sub-class `Album`, you can still do that too.
* This simple addition lets you return a UITableView–ish representation of an Album, without modifying Album‘s code.
* 你直接用`Album`里的属性。
* 你已经添加到了`Album`类里了，但是你还没有让它变为子类。如果你想要让它变为`Album`	子类，你依旧可以这么做。
* 这步简单的添加可以让你返回一个类似UITableView的专辑描述，而不用更改Album里的代码。


##Delegation
##代理

The other Decorator design pattern, Delegation, is a mechanism in which one object acts on behalf of, or in coordination with, another object. For example, when you use a `UITableView`, one of the methods you must implement is `tableView(_:numberOfRowsInSection:)`
.
另外一个修饰模式，代理，是一个对象代理或者与另一个对象合作的机制。比如说，当你用`UITableView`时，其中一个你必须用到的方法是`tableView(_:numberOfRowsInSection:)`。

You can’t expect the `UITableView` to know how many rows you want to have in each section, as this is application-specific. Therefore, the task of calculating the amount of rows in each section is passed on to the `UITableView` delegate. This allows the `UITableView` class to be independent of the data it displays.

你不能指望`UITableView`来指导每个部分里有几行，因为这具体到了应用。所以，`UITableView`的代理就有了计算行数的任务。这使得`UITableView`类独立于它要展示的数据。

Here’s a pseudo-explanation of what’s going on when you create a new `UITableView`:

这里是一个大概的解释当你创建一个`UITableView`时发生了什么。

![delegate](http://cdn5.raywenderlich.com/wp-content/uploads/2013/08/delegate-480x252.png)

The `UITableView `object does its job of displaying a table view. However, eventually it will need some information that it doesn’t have. Then, it turns to its delegates and sends a message asking for additional information. In Objective-C’s implementation of the delegate pattern, a class can declare optional and required methods through a protocol. You’ll cover protocols a bit later in this tutorial.

这个`UITableView`对象要展示一个表单。不过，最终它会需要一些它没有的信息。然后，它转向他的代理们然后发出需要其他信息的请求。在Objective-C实施代理模式里，一个类可以通过协议声明非强制的和必须的方法。在这个教程的后面一点你会学到协议的。

It might seem easier to just subclass an object and override the necessary methods, but consider that you can only subclass based on a single class. If you want an object to be the delegate of two or more other objects, you won’t be able to achieve this by subclassing.

只是加个子类然后覆盖必须的方法看上去很简单一下，不过要注意，对一个子类只能有一个父类，如果你想要这个对象成为2个及以上对象的代理，你将不能用子类的方法。

```
Note: This is an important pattern. Apple uses this approach in most of the UIKit classes: UITableView, UITextView, UITextField, UIWebView, UIAlert, UIActionSheet, UICollectionView, UIPickerView, UIGestureRecognizer, UIScrollView. The list goes on and on.
```

```
注意：这是一个非常重要的模式。Apple在大多UIKit类里用这个方法：UITableView, UITextView, UITextField, UIWebView, UIAlert, UIActionSheet, UICollectionView, UIPickerView, UIGestureRecognizer, UIScrollView. 这个单子持续更长。
```

##How to Use the Delegate Pattern
##怎么使用代理模式

Open up `ViewController.swift` and add these private properties to the class:

打开`ViewController.swift`然后给它加上一些私有的属性。

```
private var allAlbums = [Album]()
private var currentAlbumData : (titles:[String], values:[String])?
private var currentAlbumIndex = 0
```

Next, replace `viewDidLoad` with this code:

接下来，用下面的代码替换`viewDidLoad`。

```
override func viewDidLoad() {
  super.viewDidLoad()
  //1
  self.navigationController?.navigationBar.translucent = false
  currentAlbumIndex = 0
 
  //2
  allAlbums = LibraryAPI.sharedInstance.getAlbums()
 
  // 3
  // the uitableview that presents the album data
  dataTable.delegate = self
  dataTable.dataSource = self
  dataTable.backgroundView = nil
  view.addSubview(dataTable!)		
}
```

Here’s a breakdown of the above code:

现在让我们分析一下上面的代码：

1. Turn off translucency on the navigation bar.
2. Get a list of all the albums via the API. Remember, the plan is to use the facade of LibraryAPI rather than `PersistencyManager` directly!
3. This is where you setup the `UITableView.` You declare that the view controller is the UITableView delegate/data source; therefore, all the information required by `UITableView` will be provided by the view controller. Note that you can actually set the delegate and datasource in a storyboard, if your table view is created there.

1. 关掉导航栏的透明度。
2. 从API那里得到所有专辑的列表。记住，我们的计划使用LibraryAPI的外观模式而不是直接用 `PersistencyManager` 
3. 现在这步就是要建立`UITableView`了。你要声明view controller是`UITableView`代理／数据的源头；所以，所有`UITableView`需要的信息都会由view controller提供。注意你其实可以在storyboard设置代理和数据源，如果你的表单是在那里创建的。

Now, add the following method to `ViewController.swift`:

现在，将下面的方法添加到`ViewController.swift`：

```
func showDataForAlbum(albumIndex: Int) {
  // defensive code: make sure the requested index is lower than the amount of albums
  if (albumIndex < allAlbums.count && albumIndex > -1) {
    //fetch the album
    let album = allAlbums[albumIndex]
    // save the albums data to present it later in the tableview
    currentAlbumData = album.ae_tableRepresentation()
  } else {
    currentAlbumData = nil
  }
  // we have the data we need, let's refresh our tableview
  dataTable!.reloadData()
}
```

`showDataForAlbum()` fetches the required album data from the array of albums. When you want to present the new data, you just need to call `reloadData`. This causes `UITableView` to ask its delegate such things as how many sections should appear in the table view, how many rows in each section, and how each cell should look.

`showDataForAlbum()`从专辑数组里得到需要的专辑数据。当你想要展现新的数据时，你只需要调用`reloadData`。这个会使`UITableView` 问他的代理，比如这个表单里需要几个部分，每个部分里有多少行，每个单格应该长什么样之类的问题。

Add the following line to the end of `viewDidLoad`
把下面这行添加到`viewDidLoad`的最后。

```
self.showDataForAlbum(currentAlbumIndex)
```

This loads the current album at app launch. And since `currentAlbumIndex` was previously set to 0, this shows the first album in the collection.

这个会在打开app的时候载入当前的专辑。而且因为`currentAlbumIndex`之前被设置成0，这会展示列表里的第一个专辑

Now it’s time to implement the data source protocol! You can add the list of protocols implemented by the class right on the class declaration line. Or, to keep things tidy you can add them as extensions, which you’re already familiar with.

现在是执行数据源协议的时候了！你先添加由类执行的在类声明那行的协议列表。或者，为了使东西看起来干净点你可以把他们添加成扩展，你应该已经对扩展很熟悉了。

Add the following extensions to the bottom of the file. Make sure you add these lines after the closing brace of the class definition!

将下面的扩展添加到文件的尾部。你要确保把这些添加在类定义的大括号之前！

```
extension ViewController: UITableViewDataSource {
}
 
extension ViewController: UITableViewDelegate {
}
```

This is how you make your delegate conform to a protocol — think of it as a promise made by the delegate to fulfill the method’s contract. Here, you indicate that `ViewController` will conform to the `UITableViewDataSource` and `UITableViewDelegate` protocols. This way` UITableView` can be absolutely certain that the required methods are implemented by its delegate.

这就是怎么使你的代理凑手协议 － 把它想象成这是代理做出的满足方法合同的承诺。这样，你表明了`ViewController`会遵守 `UITableViewDataSource` 和 `UITableViewDelegate`的下移。这样` UITableView`可以百分百确保这些需要的方法由代理执行了。

Add the following code to the `UITableViewDataSource` extension:

将下面的代码添加到`UITableViewDataSource` 扩展

```
func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
  if let albumData = currentAlbumData {
    return albumData.titles.count
  } else {
    return 0
  }
}
 
func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
  var cell:UITableViewCell = tableView.dequeueReusableCellWithIdentifier("Cell", forIndexPath: indexPath) as! UITableViewCell
  if let albumData = currentAlbumData {
    cell.textLabel!.text = albumData.titles[indexPath.row]
    cell.detailTextLabel!.text = albumData.values[indexPath.row]
  }
  return cell
}
```

`tableView(_:numberOfRowsInSection:)` returns the number of rows to display in the table view, which matches the number of titles in the data structure.

`tableView(_:numberOfRowsInSection:)` 返回表单里的行数，而且这与数据结构里的标题数量一致。

`tableView(_:cellForRowAtIndexPath:) `creates and returns a cell with the title and its value.

`tableView(_:cellForRowAtIndexPath:) `创建并且返回了单元的标题和值。

```
Note: You can actually add the methods to the main class declaration or to the extension; the compiler doesn’t care that the data source methods are actually inside the UITableViewDataSource extension. For humans reading the code though, this kind of organization really helps with readability.
```

```
注：你实际需要添加方法到主类的声明里或者到扩展：编译器不会在乎数据源方法到底是不是在UITableViewDataSource扩展里。当然对于别人读代码来说，这样的结构相当帮助可读性。
```

Build and run your project. Your app should start and present you with the following screen:

编译运行你的程序。你的app应该启动而且展现下面的屏幕：

![designPatternDataTable](http://cdn3.raywenderlich.com/wp-content/uploads/2014/11/Screen-Shot-2014-11-11-at-12.38.53-AM-179x320.png)
Table view data source success!

表单数据源成功！

##Where to go from here?

##现在能去哪里？

Things are looking pretty good so far! You have the MVC pattern in place, and you’ve also seen the singleton, facade, and decorator patterns in action. You can see how these are used within Cocoa by Apple, and also how to apply the patterns to your own code.

现在东西看上去不错！你的MVC模式在它该在的地方，而且你也看见过了单例，外观，修饰模式运作时的样子。你也可以看他们在由Apple写的Cocoa里样子，也能看这些模式如何能被运用到妮子的代码。

Here are the [project files up to this point](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/BlueLibrarySwift-Part11.zip) if you want to have a look or compare.

这里是到现在为止的[项目文件](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/BlueLibrarySwift-Part11.zip)

There’s a lot more in store: there are still the adapter, observer, and memento patterns to cover in [part two of this tutorial](http://www.raywenderlich.com/90773/introducing-ios-design-patterns-in-swift-part-2). And if that’s not enough, we have a follow-up tutorial coming up covering even more design patterns as you work on refactoring a simple iOS game.

还有很多没学过的：比如适配器，观察者和备忘录模式将在[本教程的第二部分](http://www.raywenderlich.com/90773/introducing-ios-design-patterns-in-swift-part-2)提到。而且这还不是全部，我们将会有一个复查的教程，里面有更多的设计模式来让你写一个简单的iOS游戏。

If you have questions or just want to talk about your favorite design patterns, join in on the forum discussion below!

如果你还有其他的问题或者只是想提到你最喜欢的设计模式，加入到下面的讨论！