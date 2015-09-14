介绍iOS设计模式 （1/2）
---

>* 原文链接 : [Introducing iOS Design Patterns in Swift – Part 1/2](http://www.raywenderlich.com/86477/introducing-ios-design-patterns-in-swift-part-1)
* 译者 : [Alier Hu](https://github.com/alier1226) 
* 校对者：[lastdays](https://github.com/MrLoong)
* 状态：完成



**2015/04/22更新：** 最新更新到Xcode6.3和Swfit 1.2。




**更新日志：** 这个教程由Vicent Ngo最新更新到iOS 8和Swift。原教程由教程组组员Eli Ganem发表。




**iOS设计模式** － 你很可能听说过这个词，但是你真的知道它什么意思么？虽然大多数开发者都同意设计模式非常重要，但于此同时，并没有很多文章写这个内容，而且我们作为开发者在写代码时也有时不在意设计模式。



设计模式是在软件设计中常见问题的重复使用的解决方法。它们是可以让你的代码清晰易懂可重复使用度高的模版。它们也可以帮你完成一些松散的代码，你不用很麻烦就可以更改代码或者替换里面的组成部分。



如果你并不熟悉设计模式，那我有一个好消息给你！首先， 由于Cocoa的建立方式，你现在就可以使用很多iOS的设计模式了！其次，这个教程会让你快速学所有的主流（以及不那么主流）的被广泛应用在Cocoa里的iOS设计模式。



教程分为两部分，你将会做一个音乐库的app，它可以显示你的专辑以及它们相关的信息。



在开发这个app的过程中，你将会了解大部分常见的Cocoa设计模式：


* 创建型：单例模式（Singleton）

* 结构型：MVC，修饰模式（Decorator），适配器模式（Adapter），外观模式（Facade）

* 行为型：观察者模式（Observer），备忘录模式（Memento）



别以为这篇文章是关于理论的；你将会在你的音乐app中知道怎么运用大多数这些设计模式。在本教程最后你的app最终会是这样：

![swiftDesignPattern1](http://cdn2.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern1-179x320.png)

![swift设计模式1](http://cdn2.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern1-179x320.png)



让我们开始吧！



## 开始



下载并解压 [起始项目](http://cdn1.raywenderlich.com/wp-content/uploads/2014/11/BlueLibrarySwift-Starter.zip), 在xCode里打开 `BlueLibrarySwift.xcodeproj` .



在这个项目里，有三件事需要注意：



1. `ViewController` 有两个`IBOutlet`连接了表格（table view）和storyboard的工具栏

2. 为了让你更方便，storyboard里已经有三个设置好限制的部分了。最上面的部分是显示专辑封面的，在它下面会有一个表格显示对应专辑封面的信息。最后那个工具栏有两个按钮，一个可以撤销操作，另一个可以删除你所选择的专辑。Storyboard如下所示：
![swift设计模式storyboard](http://cdn5.raywenderlich.com/wp-content/uploads/2014/11/Screen-Shot-2014-11-11-at-12.20.32-AM-480x228.png)

3.一个还没写的HTTP Client类 （`HTTPClient	`）需要你之后完成。



**注意：** 你知道当你新建一个Xcode项目时，你的代码已经有设计模式了么？MVC，代理，原型，单例 － 你可以免费得到它们所有！


在你深入第一种设计模式之前，你需要先新建两个类来存以及展示专辑数据。


去到`“File\New\File…”`（或者直接按`Command+N`）。选择`iOS > Cocoa Touch Class`然后点击`Next`。将类的名字设置成`Album`，子类设置成`NSObject`。最后选择`Swift`语言。点击`Next` 和`Create`。


打开`Album.swift`并且在类定义里加上以下这些属性。

```
var title : String!
var artist : String!
var genre : String!
var coverUrl : String!
var year : String!
```


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


这段代码给`Album`类创建了初始化方法。当你创建一个新的专辑时，你需要传给它专辑名称，艺术家，种类，封面URL和年份。



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


`description()`方法返回一个代表专辑属性的字符串。



再次去`File\New\File…`，选择`Cocoa Touch Class`并且点击`Next`。将这个类的名称改为`AlbumView`，不过这次将它的子类设置成`UIView	`。确认语言为`Swift`然后点击`Next`和`Create`。


打开`AlbumView.swift`，然后在这个类的定义里加上以下属性：

```
private var coverImage: UIImageView! 
private var indicator: UIActivityIndicatorView!
```


`coverImage`代表了专辑封面图片。第二个属性是指示物，表明专辑封面正在下载这么一个活动。



这些属性被设置成private因为没有除了`AlbumView`的类需要知道这些属性的存在；它们只需要在这个类的功能里面被用到。当你要建立一个库给别的开发者用，你要把隐藏的状态信息隐藏，这个惯例是非常重要的。



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



`commonInit`是一个这两个初始化里都被用到的帮助方法：你将在这个项目的剩余部分也用到它，你将会给专辑界面设置一些好的默认值。把背景设置为黑，建立一个5像素边框的图片界面，然后建立并添加到活动指示器。



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



如果专辑被高亮，它将设置专辑的背景色为白，如果没有被高亮，则被设置成黑。


建造你的项目（`Command+B`）来确认所有东西都正常。都好了？那么就准备好来做你第一个设计模式吧！：］


MVC - 设计模式之王

![mvcking](http://cdn5.raywenderlich.com/wp-content/uploads/2013/07/mvcking.png)



MVC是Cocoa中的一个组成部件，它无可厚非是所有设计模式中最常用的。它将对象按照你程序的角色分类，并且根据角色清晰地分离代码。


这三个角色为：


Model: 这个对象存着你程序的数据并且定义怎么控制它。举个例子，在你的程序中，专辑这个类就是Model。


View: 这个对象负责Model的视觉描述以及用户可以交互的控制；总而言之，所有UIVIew有关的对象。在你的程序里，你的AlbumVIew类就代表着View。


Controller： Controller 是一个“调解人”，协调所有的工作。它从model里面得到数据，然后在view里面展示它们，监听事件并且如果需要会控制数据。你能猜到哪个类是你的controller么？没错，是ViewController.


在你的程序中执行一个好的设计模式意味着每个对象都可以被分到其中的一个组里。



通过Controller的View 和Model之间的交流可由下图表示：
![mvc0](http://cdn2.raywenderlich.com/wp-content/uploads/2013/07/mvc0.png)



如果数据有变化Model会通知Controller，然后Controller会在View中更新显示数据。View可以通知Controller用户的行为，然后Controller会根据需要更新Model或者得到需要的数据。


你可能会疑惑为什么你不能直接抛弃Controll二，然后在一个类里面运行View和Model，这样看起来会更简单。这都因为要分离代码以及重复利用性。理想中，View应该完全脱离Model。如果View完全不依赖一个特定的Model，那么它可以在另一个model中再次被利用来展示其他的数据。


举例说，如果以后你想在你的库中添加电影或者书籍，你依然可以用`AlbumView`来展示你的电影和书籍。更多的是，如果你想新创建一个有关专辑的项目，你还可以重复利用`Album`类，因为它并不依赖于任何View。这就是MVC的厉害之处！



## 怎么用MVC模式



首先，你需要确保你的项目中的每一个类必须是Controller，Model或者View；不要在一个类里混杂了两个角色的功能。到现在你建立了`Album`类和`AlbumView`类，做得好！



其次，为了确保你遵守了这种工作的方法，你要创建三个组（group）来放你的代码，每一个种类有一个组。


选择`File\New\Group` (或者直接按`Command+Option+N`)并且给你的组命名为`Model`。重复这个过程来创建`View` 和 `Controller` 组。


现在把`Album.swift` 拖到Model组。把`AlbumView.swift`拖到View组，最后把`ViewController.swift` 拖到Controller组。


现在项目应该长这样：

![swiftDesignPattern2](http://cdn4.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern2-259x320.png)



你的项目已经看起来很好了，所有文件都没有到处乱放。很明显你也可以有别的组合类，但是这个程序的核心都在这三个种类里。
现在你项目的组成部分已经很有序了，你需要从某个地方得到专辑的数据。你需要创建一个API类来管理数据，它将在你整个代码中都被使用。这也意味着我们有机会来聊下下一个需要你学习的设计模式－单例。

##The Singleton Pattern


单例设计模式确保了制定类里只有一个实例并且有一个全局的访问指向这个实例。当它第一次被用的时候，一般是以惰性加载的方式来创建单例。


```
注：苹果用了很多这个方法。比如：NSUserDefaults.standardUserDefaults(), UIApplication.sharedApplication(), UIScreen.mainScreen(), NSFileManager.defaultManager() 他们都是返回一个单例的对象。
```



你很可能能想为什么需要关心一个类中是否不止一个实例。代码和内存都很容易获得啊，不是吗？



在一些情况下，类里只有一个实例是比较符合逻辑的。比如说， 你的程序里只有一个实例，设备有一个主要的屏幕，所以你只需一个实例。或者处理全局配置类：实现一个线程安全的访问指向到单个分享的资源，比如配置文件，比起很多类可能在同一时间改这个配置文件，会更简单些。



## 单例模式怎么用


看下下图：

![singleton](http://cdn3.raywenderlich.com/wp-content/uploads/2013/08/singleton.png)



上面的图显示了一个Logger类有一个属性（一个单例）和两个方法：`sharedInstance` 和 `init`。



当用户第一次请求sharedInstance，单例属性还没有被初始化，所以你需要先创建一个类的实例，然后返回它的参考（reference）。



下一次你调用`sharedInstance`,`instance`（实例）就将立即返回不再需要其他的初始化。这个逻辑确保了每次只有一个实例存在。


你将通过创建一个实例类来管理全部数据来实现这个模式。



你讲发现，在这个项目中有一个组叫`API`；这就是你将把所有给你程序提供服务的类放置的地方。右键这个组然后选择`New File`来在这个组里创建一个新的文件。选`iOS > Cocoa Touch Class` 然后点击`Next`。将这个类的名字设置为`LibraryAPI`, 子类为 `NSObject`。最后选择 `Swift`作为编程语言，点击`Next` 然后点 `Create`。



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



这个很短的方法里干了很多事



它首先创建了一个类变量为计算类型属性。这个类变量，就是OC里的类方法，是一个不需要实例化`LibarayAPI`类就可以调用的东西。想要更多关于类型属性的信息，请去参考Apple Swift关于属性的文档。



在这个类变量里嵌入一个结构称为`Singleton`



`Singleton`包含了一个叫instance的静态常量。把一个属性声明为`static`(静态)意味着这个属性只出现一次。同时也要注意静态属性在Swift中为绝对惰性，当它被创造以后，它不会再一次被创造。这也是单例设计模式必须的，当它被初始化过后，这个初始化方法永远不会再被调用。


返回计算类型属性

```
**Note**: To learn more about different ways to create a singleton in Swift refer to this: [Github page](https://github.com/hpique/SwiftSingleton)
```

```
**注意`**: 想要了解在swift中更多创建一个单例的方法，请去[GitHub网页]（https://github.com/hpique/SwiftSingleton）
```



你现在又了一个单例对象作为管理专辑的开始。让我更进一步然后创建一个类来处理你的库里数据的永久性。


现在在`API`组里创建一个新文件。选`iOS > Cocoa Touch class`然后点击`Next`。把这个类的名字改成PersistencyManager，然后把它弄成`NSObject`的子类。


打开`PersistencyManager.swift`然后将下面的代码放到大括号里。

```
private var albums = [Album]()
```



现在你声明了一个私有属性来放置专辑的数据。这个数组是可变的，你可以很方便的添加或者删除专辑。



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


在这个初始化方法里，你往数组里添加了五个样本专辑。如果你不喜欢上面的专辑，你也可以自己换成你喜欢的音乐：］



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

.

这些方法让你可以得到，添加以及删除专辑。



运行你的项目来确保所有东西都能正常编译。



到现在这个时候，你可以会想什么时候为什么`PersistencyManager`会介入因为它并不是一个单例。你将在下一个章节中看到`LibraryAPI` 与 `PersistencyManager`之间的关系，下一个章节是关于外观模式的。


#外观设计模式

![facade](http://cdn1.raywenderlich.com/wp-content/uploads/2013/07/facade.jpg)


外观设计模式给复杂的子系统提供一个借口。你只会有一个整合的API暴露给你，而不是一堆类和它们各自的API。


下面这个图解释了这个概念：

![facade2](http://cdn1.raywenderlich.com/wp-content/uploads/2013/07/facade2-480x241.png)


API的用户并不完全知道API有多复杂。这个模式是当要用到大量类时，特别是那些非常复杂难懂难用的，非常理想好用的。



外观模式将那些从接口处用系统的代码和你要隐藏的执行类的代码分离开；它并且减少你子系统内部对外面代码的依赖性。如果外观模式下的代码很有可能变，那么这个就非常有用，因为外观类可以保持同样的CPI当其它东西在幕后变化。



举个例子，如果你有一天想要换你的后端服务，你并不需要改变你的代码因为它的API并不会改变。


##怎么用外观模式


现在你有`PersistencyManager `来本地储存专辑数据，和`HTTPClient` 来处理远端通讯。在你的项目中，其它的类不应该知道这个逻辑，因为它们会被`LibraryAPI`隐藏在外观后。



为了实现这个模式，只有`LibraryAPI`拥有`PersistencyManager` 和 `HTTPClient`。并且，`LibraryAPI`会暴露给一个简单的API使它可以获得这些服务。



这个模式长这样：

![design-patterns-facade-uml](http://cdn2.raywenderlich.com/wp-content/uploads/2013/08/design-patterns-facade-uml-480x71.png)



`LibraryAPI`会暴露给其他的代码，但是会对app中其它部分隐藏`HTTPClient` 和`PersistencyManager`的复杂性。

打开 `LibraryAPI.swift`然后添加下面这些常量属性到类里：

```
private let persistencyManager: PersistencyManager
private let httpClient: HTTPClient
private let isOnline: Bool
```


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


HTTP Client 并不真正与真实的服务器工作而且只在这里需要声明外观模式的运用，所以`isOnline`永远是否（false）。


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


看下`addAlbum(_:index:)`。这个类首先本地更新数据，如果有网络连接，它再更新远程服务器。这才是外观模式真正的厉害之处；当你的系统之外的什么类添加了一个专辑，它并会不知道－也不需要知道－其底下的复杂性。



```
**注：** 当给你的子系统的类设计一个外观模式时，记住什么都不能阻止客户直接进入那些被“隐藏”的类。不要吝啬地加上防守的代码，也不要假设所有客户都会像外观模式那样用你的类。
```


编译运行你的app。你应该会看到两个空的界面和一个工具栏。上面的界面会用来展示你的专辑封面，下面那个界面将用来展示专辑相关信息的表格。

![designPatternInitialScreen](http://cdn3.raywenderlich.com/wp-content/uploads/2014/11/Screen-Shot-2014-11-11-at-12.33.50-AM-179x320.png)



你现在需要一些可以展示专辑数据到屏幕上的东西－是一个好机会用你下一个设计模式了：修饰模式。


#修饰设计模式


修饰模式动态添加行为和责任到一个对象而不改变它的代码。这是一个用来创建子类的另一种方法：当你通过把一个类包装到另一个对象来更改类的时候。在swift中有两个非常常见的方法来实施这个模式：`Extensions` 和 `Delegation`


##扩建



添加一个扩建是非常有用的技巧，它使你可以给现有类、结构、枚举类型添加新功能而不用让它们成为子类。这个的绝妙之处在于你可以扩展你的那些你没有权限的代码，并且增加它们的功能性。这个意味着你可以Cocoa类里，比如UIView和UIImage里加入你自己的方法。



比如说，在编译时添加的新方法可以想扩展类里的普通方法一样被执行。这个与典型的修饰模式还是有一点点的区别，因为一个扩建不不拥有它扩建的那个类的实例。


## 怎么用扩建



想象一下当你有一个专辑对象，然后你想在表单里展示这个。

![swiftDesignPattern3](http://cdn2.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern3-480x262.png)



专辑标题从哪来呢？`Album`是一个在model里的对象，所以它不关心你怎么展示数据。你只需要有一些外部的代码来添加功能到Album类而不用直接更改这个类。


你首先创建一个扩展Album类的扩建，它会定义一个新方法，这个方法会返回一个方便与`UITableView`用的数据结构。



这个数据结构看上去像下面这样：

![delegate2](http://cdn5.raywenderlich.com/wp-content/uploads/2013/08/delegate2-480x67.png)



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



注意在这个方法名字的开头有一个`ae_`，这是`AlbumExtension`的缩写。这样的规则会避免在原始执行中的方法有冲突（包括那些你可以不知道的私有的方法）。



```
注意：类当然可以是父类的方法无效，不过扩展的话是不可以的。扩展里方法和属性是不能与原先的类里的方法和属性有同样的名字。
```



想一下这个模式有多么的厉害：


* 你直接用`Album`里的属性。
* 你已经添加到了`Album`类里了，但是你还没有让它变为子类。如果你想要让它变为`Album`	子类，你依旧可以这么做。
* 这步简单的添加可以让你返回一个类似UITableView的专辑描述，而不用更改Album里的代码。



##代理


.
另外一个修饰模式，代理，是一个对象代理或者与另一个对象合作的机制。比如说，当你用`UITableView`时，其中一个你必须用到的方法是`tableView(_:numberOfRowsInSection:)`。



你不能指望`UITableView`来指导每个部分里有几行，因为这具体到了应用。所以，`UITableView`的代理就有了计算行数的任务。这使得`UITableView`类独立于它要展示的数据。


这里是一个大概的解释当你创建一个`UITableView`时发生了什么。

![delegate](http://cdn5.raywenderlich.com/wp-content/uploads/2013/08/delegate-480x252.png)



这个`UITableView`对象要展示一个表单。不过，最终它会需要一些它没有的信息。然后，它转向他的代理们然后发出需要其他信息的请求。在Objective-C实施代理模式里，一个类可以通过协议声明非强制的和必须的方法。在这个教程的后面一点你会学到协议的。



只是加个子类然后覆盖必须的方法看上去很简单一下，不过要注意，对一个子类只能有一个父类，如果你想要这个对象成为2个及以上对象的代理，你将不能用子类的方法。



```
注意：这是一个非常重要的模式。Apple在大多UIKit类里用这个方法：UITableView, UITextView, UITextField, UIWebView, UIAlert, UIActionSheet, UICollectionView, UIPickerView, UIGestureRecognizer, UIScrollView. 这个单子持续更长。
```


##怎么使用代理模式



打开`ViewController.swift`然后给它加上一些私有的属性。

```
private var allAlbums = [Album]()
private var currentAlbumData : (titles:[String], values:[String])?
private var currentAlbumIndex = 0
```


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



现在让我们分析一下上面的代码：


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


`showDataForAlbum()`从专辑数组里得到需要的专辑数据。当你想要展现新的数据时，你只需要调用`reloadData`。这个会使`UITableView` 问他的代理，比如这个表单里需要几个部分，每个部分里有多少行，每个单格应该长什么样之类的问题。


把下面这行添加到`viewDidLoad`的最后。

```
self.showDataForAlbum(currentAlbumIndex)
```


这个会在打开app的时候载入当前的专辑。而且因为`currentAlbumIndex`之前被设置成0，这会展示列表里的第一个专辑


现在是执行数据源协议的时候了！你先添加由类执行的在类声明那行的协议列表。或者，为了使东西看起来干净点你可以把他们添加成扩展，你应该已经对扩展很熟悉了。



将下面的扩展添加到文件的尾部。你要确保把这些添加在类定义的大括号之前！

```
extension ViewController: UITableViewDataSource {
}
 
extension ViewController: UITableViewDelegate {
}
```



这就是怎么使你的代理凑手协议 － 把它想象成这是代理做出的满足方法合同的承诺。这样，你表明了`ViewController`会遵守 `UITableViewDataSource` 和 `UITableViewDelegate`的下移。这样` UITableView`可以百分百确保这些需要的方法由代理执行了。


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



`tableView(_:numberOfRowsInSection:)` 返回表单里的行数，而且这与数据结构里的标题数量一致。



`tableView(_:cellForRowAtIndexPath:) `创建并且返回了单元的标题和值。



```
注：你实际需要添加方法到主类的声明里或者到扩展：编译器不会在乎数据源方法到底是不是在UITableViewDataSource扩展里。当然对于别人读代码来说，这样的结构相当帮助可读性。
```



编译运行你的程序。你的app应该启动而且展现下面的屏幕：

![designPatternDataTable](http://cdn3.raywenderlich.com/wp-content/uploads/2014/11/Screen-Shot-2014-11-11-at-12.38.53-AM-179x320.png)


表单数据源成功！



##现在能去哪里？



现在东西看上去不错！你的MVC模式在它该在的地方，而且你也看见过了单例，外观，修饰模式运作时的样子。你也可以看他们在由Apple写的Cocoa里样子，也能看这些模式如何能被运用到妮子的代码。


这里是到现在为止的[项目文件](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/BlueLibrarySwift-Part11.zip)



还有很多没学过的：比如适配器，观察者和备忘录模式将在[本教程的第二部分](http://www.raywenderlich.com/90773/introducing-ios-design-patterns-in-swift-part-2)提到。而且这还不是全部，我们将会有一个复查的教程，里面有更多的设计模式来让你写一个简单的iOS游戏。



如果你还有其他的问题或者只是想提到你最喜欢的设计模式，加入到下面的讨论！