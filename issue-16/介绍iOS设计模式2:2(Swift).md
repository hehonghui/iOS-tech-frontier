* 原文链接：[Introducing iOS Design Patterns in Swift – Part 2/2](http://www.raywenderlich.com/90773/introducing-ios-design-patterns-in-swift-part-2)
* 原文作者：[Vincent Ngo](http://www.raywenderlich.com/u/jomoka)
* [译文出自：开发者前线 www.devtf.cn](www.devtf.cn)
* 译者：[lastdays](https://github.com/MrLoong)
* 校对者：[lastdays](https://github.com/MrLoong)
* 状态：完成


##Getting Started 让我们开始



你可以下载[the project source from the end of part 1](http://cdn1.raywenderlich.com/wp-content/uploads/2014/12/BlueLibrarySwift-Part11.zip)与我们共同来探索


这是你在第一部分结束时完成的音乐库App样品

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/11/Screen-Shot-2014-11-11-at-12.38.53-AM.png)


应用程序的最初设计包括在屏幕的顶端上上水平滚动条的专辑切换。但是为什么不重写它适配所有view，而不是单一的编辑一个简单的滚动条？




为了使这个view可重用。关于其内容的所有决定都应该留给下一个对象：一个委托。水平滚动条要声明一个delegate implements为了scroller的工作。类似于UITableView delegate。当我们讨论设计模式的时候我们会实现这个。

##适配器模式



Adapter允许classes与不兼容的接口一起工作。它围绕一个对象进行封装，并公开一个标准接口与该对象进行交互。



如果你很熟悉适配器你将注意到，App使用了略微不同的方式去实现它--App通过协议来实现它。
你可能会感觉它很像UITableViewDelegate，UIScrollViewDelegate, NSCoding 和 NSCopying。作为一个示例，随着NSCopying协议发展，任何class都能提供一个标准的copy方法。

##  怎样使用适配器

之前提到的horizontal scroller看起来像这个样子

![](http://cdn2.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern7.png)



开始实现它，在Project Navigator点击View group 选择New File…并且选择iOS > Cocoa Touch class然后点击Next。建立类名为HorizontalScroller并且继承于UIView。

打开HorizontalScroller.swift并且加入下面的代码类：

```
@objc protocol HorizontalScrollerDelegate {
}

```

这定义了一个协议名为HorizontalScrollerDelegate。在声明协议之前你包括了@objc所以你能使用@optional delegate 方法。像在 Objective-C。

你定义了所需要的并且选中的委托方法将在大括号之间实现。所以添加以下协议方法

```
// ask the delegate how many views he wants to present inside the horizontal scroller
func numberOfViewsForHorizontalScroller(scroller: HorizontalScroller) -> Int
// ask the delegate to return the view that should appear at <index>
func horizontalScrollerViewAtIndex(scroller: HorizontalScroller, index:Int) -> UIView
// inform the delegate what the view at <index> has been clicked
func horizontalScrollerClickedViewAtIndex(scroller: HorizontalScroller, index:Int)
// ask the delegate for the index of the initial view to display. this method is optional
// and defaults to 0 if it's not implemented by the delegate
optional func initialViewIndex(scroller: HorizontalScroller) -> Int

```



这里有需求和可选择的方法。所需的方法必须由委托来执行，通常包含一些数据，这是绝对必须的。
在这种情况下，所需的细节是view数量，特殊的索引视图，并且挖掘试图的行为。这里的可选方法是初始视图。如果没有实现，HorizontalScroller讲默认为第一个索引。


在HorizontalScroller.swift，

将下面的代码添加到HorizontalScroller的定义。

```
weak var delegate: HorizontalScrollerDelegate?

```


你上面创建的创建的属性被定义为弱引用。这是必要的，以防止保留一个周期。如果一类对它的委托有强引用的话，该委托保持强引用并且返回一个标准的类，你的app将发生内存泄露，因为这两个类将释放分配给其他的内存。所有的属性在swift中被默认为强引用



委托是可选的，所以有可能使用这个类的人不提供一个委托。但如果他们这样做，它将使HorizontalScrollerDelegate一致并且你可以确保协议方法在那里实现。

Add a few more properties to the class:添加一些属性到类中：

```
// 1
private let VIEW_PADDING = 10
private let VIEW_DIMENSIONS = 100
private let VIEWS_OFFSET = 100
 
// 2
private var scroller : UIScrollView!
// 3
var viewArray = [UIView]()

```


滚动每一个评论块:

* 定义常亮，使其易于在设计时修改布局。视图的尺寸内的滚动条是100 x 100的矩形。

* 创建一个包含试图的滚动视图
* 创建一个拥有专辑封面的数组

Next you need to implement the initializers. Add the following methods:
下一步你需要执行初始化。添加以下方法：

```
override init(frame: CGRect) {
  super.init(frame: frame)
  initializeScrollView()
}
 
required init(coder aDecoder: NSCoder) {
  super.init(coder: aDecoder)
  initializeScrollView()
}
 
func initializeScrollView() {
  //1
  scroller = UIScrollView()
  addSubview(scroller)
 
  //2
  scroller.setTranslatesAutoresizingMaskIntoConstraints(false)
  //3
  self.addConstraint(NSLayoutConstraint(item: scroller, attribute: .Leading, relatedBy: .Equal, toItem: self, attribute: .Leading, multiplier: 1.0, constant: 0.0))
  self.addConstraint(NSLayoutConstraint(item: scroller, attribute: .Trailing, relatedBy: .Equal, toItem: self, attribute: .Trailing, multiplier: 1.0, constant: 0.0))
  self.addConstraint(NSLayoutConstraint(item: scroller, attribute: .Top, relatedBy: .Equal, toItem: self, attribute: .Top, multiplier: 1.0, constant: 0.0))
  self.addConstraint(NSLayoutConstraint(item: scroller, attribute: .Bottom, relatedBy: .Equal, toItem: self, attribute: .Bottom, multiplier: 1.0, constant: 0.0))
 
  //4
  let tapRecognizer = UITapGestureRecognizer(target: self, action:Selector("scrollerTapped:"))
  scroller.addGestureRecognizer(tapRecognizer)
}

```


initializers delegate必须工作在initializeScrollView()。这是实现方法：

* 创建一个新的UIScrollView实例并将其添加到父视图。
* 关闭自动调整尺寸。就是这样，你可以应用你自己的限制。
* 应用约束到scrollview。完全填满HorizontalScroller视图
* 创建一个gesture收识别。这个收拾识别检测涉及的滚动试图。并且检查相册封面是否已经被窃听。如果是这样的话，他会通知 HorizontalScroller delegate

现在添加这个方法。

```
func scrollerTapped(gesture: UITapGestureRecognizer) {
    let location = gesture.locationInView(gesture.view)
    if let delegate = delegate {
      for index in 0..<delegate.numberOfViewsForHorizontalScroller(self) {
        let view = scroller.subviews[index] as! UIView
        if CGRectContainsPoint(view.frame, location) {
          delegate.horizontalScrollerClickedViewAtIndex(self, index: index)
          scroller.setContentOffset(CGPoint(x: view.frame.origin.x - self.frame.size.width/2 + view.frame.size.width/2, y: 0), animated:true)
          break
        }
      }
    }
  }

```

手势作为一个参数传入你locationInView()。

Next, you invoke numberOfViewsForHorizontalScroller() on the delegate. 

接下来，你调用委托numberOfViewsForHorizontalScroller()。

Next add the following to access an album cover from the scroller:

接下来添加下面的封面专辑

```
func viewAtIndex(index :Int) -> UIView {
  return viewArray[index]
}

```

viewatindex仅仅返回视图在一个特定的指数。使用此方法，以突出显示专辑封面。

Now add the following code to reload the scroller:
现在，添加下面的代码加载滚动：

```
func reload() {
    // 1 - Check if there is a delegate, if not there is nothing to load.
    if let delegate = delegate {
      //2 - Will keep adding new album views on reload, need to reset.
      viewArray = []
      let views: NSArray = scroller.subviews
 
			// 3 - remove all subviews
			for view in views {
				view.removeFromSuperview()
			}
 
      // 4 - xValue is the starting point of the views inside the scroller
      var xValue = VIEWS_OFFSET
      for index in 0..<delegate.numberOfViewsForHorizontalScroller(self) {
        // 5 - add a view at the right position
        xValue += VIEW_PADDING
        let view = delegate.horizontalScrollerViewAtIndex(self, index: index)
        view.frame = CGRectMake(CGFloat(xValue), CGFloat(VIEW_PADDING), CGFloat(VIEW_DIMENSIONS), CGFloat(VIEW_DIMENSIONS))
        scroller.addSubview(view)
        xValue += VIEW_DIMENSIONS + VIEW_PADDING
        // 6 - Store the view so we can reference it later
        viewArray.append(view)
      }
      // 7
      scroller.contentSize = CGSizeMake(CGFloat(xValue + VIEWS_OFFSET), frame.size.height)
 
      // 8 - If an initial view is defined, center the scroller on it
      if let initialView = delegate.initialViewIndex?(self) {
        scroller.setContentOffset(CGPoint(x: CGFloat(initialView)*CGFloat((VIEW_DIMENSIONS + (2 * VIEW_PADDING))), y: 0), animated: true)
      }
    }
  }

```



重载方法仿照UITableView中的reloadData；他重新载入所有用于构建水平滚动条的数据





逐句详解:

* 在我们重新加载之前检查是否有一个委托.
* 清理专辑封面, 你需要重置viewArray.
* 删除又有之前添加到滚动试图中的子视图.
* 所有的视图都是从给定的偏移量开始的。目前是100，但它可以很容易地调整，通过在文件的顶部变化不断view_offset
* 的horizontalscroller为代表之一，同时奠定了他们未来彼此水平与先前定义的填充.
* 在viewarra在存储视图中跟踪滚动视图子视图.
* 一旦所有的视图都到位，设置的滚动视图的内容偏移，让用户滚动通过所有的专辑封面.



当你的数据发生改变时，你可以重新加载。你也需要调用这个方法时，你horizontalscroller添加到另一个视图。将下面的代码添加到horizontalscroller.swift覆盖后者：

```
override func didMoveToSuperview() {
  reload()
}

```



didmovetosuperview查看时，它添加到另一个视图作为一个视图。重新规范内容正确的时间。



horizontalscroller的最后一块拼图是确保你看的专辑总是集中在滚动视图。这样做，你将需要执行一些计算，当用户拖动滚动查看他们的手指。

Add the following method:
添加以下方法：

```
 func centerCurrentView() {
    var xFinal = Int(scroller.contentOffset.x) + (VIEWS_OFFSET/2) + VIEW_PADDING
    let viewIndex = xFinal / (VIEW_DIMENSIONS + (2*VIEW_PADDING))
    xFinal = viewIndex * (VIEW_DIMENSIONS + (2*VIEW_PADDING))
		scroller.setContentOffset(CGPoint(x: xFinal, y: 0), animated: true)
    if let delegate = delegate {
      delegate.horizontalScrollerClickedViewAtIndex(self, index: Int(viewIndex))
    }  
 }

```



上面的代码考虑到滚动视图和视图的当前偏移量，以及视图的填充量，以便计算当前视图从中心的距离。最后一行很重要：一次视图是居中的，然后通知委托，选定以更改的视图。



发现用户在完成拖动滚动视图，你需要实现一些uiscrollviewdelegate方法。将下面的类扩展添加到文件底部；请记住，这必须在主类声明的大括号之后添加！

```
extension HorizontalScroller: UIScrollViewDelegate {
  func scrollViewDidEndDragging(scrollView: UIScrollView, willDecelerate decelerate: Bool) {
    if !decelerate {
      centerCurrentView()
    }
  }
 
  func scrollViewDidEndDecelerating(scrollView: UIScrollView) {
    centerCurrentView()
  }
}

```



scrollviewdidenddragging（_：willdecelerate：）通知委托当用户完成拖动。如果滚动视图尚未完全停止来参数是真实的。当滚动行动结束，该系统调scrollviewdidenddecelerating。在这两种情况下，调用新方法以当前视图为中心，因为当前视图可能在用户拖动滚动视图后发生改变。


最后别忘了设定委托。在initializescrollview()添加以下代码后

```
scroller.delegate = self;

```

scroller = UIScrollView():



你的horizontalscroller准备就绪！浏览你刚才写的代码；你会看到有没有一个提到的专辑或albumview类。那是很好的，因为这意味着新的滚动条是真正独立的和可重复使用

Build your project to make sure everything compiles properly.
建立项目，确保所有编译正确



现在，horizontalscroller是完整的，它的时间来使用它在您的应用程序。首先，打开main.storyboard。点击顶部灰色的矩形视图，点击identity。改变类的名称来horizontalscroller如下所示：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattwern9-700x414.png)



接下来，打开助理编辑和控制从灰色的矩形视图拖到viewcontroller.swift创建一个出口。名称出口滚动，如下图所示：

![](http://cdn3.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern10.png)


接下来，打开viewcontroller.swift。现在是时候开始实施的一些horizontalscrollerdelegate方法！

Add the following extension to the bottom of the file:
将下列扩展名添加到文件底部：

```
extension ViewController: HorizontalScrollerDelegate {
  func horizontalScrollerClickedViewAtIndex(scroller: HorizontalScroller, index: Int) {
    //1
    let previousAlbumView = scroller.viewAtIndex(currentAlbumIndex) as! AlbumView
    previousAlbumView.highlightAlbum(didHighlightView: false)
    //2
    currentAlbumIndex = index
    //3
    let albumView = scroller.viewAtIndex(index) as! AlbumView
    albumView.highlightAlbum(didHighlightView: true)
    //4
    showDataForAlbum(index)
  }
}

```


让我们以下列的方式去执行委托方法吧：:   1

* 首先选定以前的专辑，然后取消选择专辑封面
* Display the data for the new album within the table view.
* 存储当前点击的相册封面索引
* 抓住当前选定的相册封面，并突出显示选择。.
* 在表视图中显示新相册的数据.

```
func numberOfViewsForHorizontalScroller(scroller: HorizontalScroller) -> (Int) {
  return allAlbums.count
}

```



正如你所认识的，这是一种在滚动视图中返回视图的方法。由于滚动视图将显示所有的专辑数据的封面，count是专辑记录的数量。

Now, add this code:、
现在，添加此代码：

```
func horizontalScrollerViewAtIndex(scroller: HorizontalScroller, index: Int) -> (UIView) {
    let album = allAlbums[index]
    let albumView = AlbumView(frame: CGRect(x: 0, y: 0, width: 100, height: 100), albumCover: album.coverUrl)
    if currentAlbumIndex == index {
      albumView.highlightAlbum(didHighlightView: true)
    } else {
      albumView.highlightAlbum(didHighlightView: false)
    }
    return albumView
  }

```



在这里，你创建一个新的albumview，接下来检查用户是否选择这张专辑。然后，您可以将其设置为突出显示或不取决于是否选择相册。最后，你通过它的horizontalscroller。


这是它！仅仅三个短的方法显示一个漂亮的水平滚动条的方法。


是的，你还需要创建滚动条，并把它添加到你的主要观点，但在这之前，将下面的方法添加到主类的定义：

```
func reloadScroller() {
    allAlbums = LibraryAPI.sharedInstance.getAlbums()
    if currentAlbumIndex < 0 {
      currentAlbumIndex = 0
    } else if currentAlbumIndex >= allAlbums.count {
      currentAlbumIndex = allAlbums.count - 1
    }
    scroller.reload()
    showDataForAlbum(currentAlbumIndex)
  }

```



此方法加载相册数据通过libraryapi然后设置当前显示基于当前视图索引的当前值。如果当前视图索引小于0，则表示当前没有选择视图，然后在列表中显示的第一张专辑。否则，最后一张专辑被显示。

```
scroller.delegate = self
reloadScroller()

```

由于horizontalscroller是创建在storyboard中，所有你需要做的是设置代理，叫reloadscroller()，将负荷的滚动条来显示专辑数据视图。


由于horizontalscroller是创建在storyboard中，所有你需要做的是设置代理，叫reloadscroller()，将负荷的滚动条来显示专辑数据视图。

编译和运行你的项目，新的水平滚动条，看看：

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern12.png)

##The Observer Pattern

在观察者模式，一个对象的状态变化通知其他任何对象。所涉及的对象不需要知道彼此的，从而鼓励一个解耦设计。当一个属性发生改变时，这个模式最常用于通知感兴趣的对象。



通常的实现要求一个观察者在另一个对象的状态寄存器。当状态发生改变时，所有的观察对象都会被通知。



如果你想坚持MVC的概念（提示：你这样做），你需要让模型对象和视图对象沟通，但他们之间没有直接的参考。这就是观察者模式的所在。



Cocoa在两个熟悉的方式实现观察者模式：通知和键值观察（KVO）。

##Notifications


不要被混淆与推送本地通知，通知是基于订阅和发布模式，允许对象（发行商）发送消息到其他对象（用户/听众）。出版商从不需要了解有关用户的任何事情。



通知被苹果严重使用。例如，当键盘显示/隐藏系统发送uikeyboardwillshownotification / uikeyboardwillhidenotification，分别。当你的应用程序进入后台，系统将一个uiapplicationdidenterbackgroundnotification通知。

##How to Use Notifications


去albumview.swift在初始化结束中插入下面的代码（框架：CGRect，albumcover：初始化字符串）：

```
NSNotificationCenter.defaultCenter().postNotificationName("BLDownloadImageNotification", object: self, userInfo: ["imageView":coverImage, "coverUrl" : albumCover])

```



这条线穿过NSNotificationCenter发送通知单。通知信息包含在UIImageView和封面图像被下载的URL。这是所有的信息，您需要执行的封面下载任务。


在libraryapi.swift init中，直接在super.init()后面添加下面一行

```
NSNotificationCenter.defaultCenter().addObserver(self, selector:"downloadImage:", name: "BLDownloadImageNotification", object: nil

```



这是等式的另一边：观察者。每一次albumview类岗位bldownloadimagenotification通知，自libraryapi注册同一通知观察者，系统会通知libraryapi。然后libraryapi通知downloadimage()响应。



然而，在你实现downloadimage()你要记得退订此通知时候释放。如果你不正确的退订通知你们班登记，通知会发送到回收实例。这可能会导致应用程序崩溃。

Add the following method to LibraryAPI.swift:
添加下面的方法到libraryapi.swift：

```
deinit {
  NSNotificationCenter.defaultCenter().removeObserver(self)
}

```


当这个对象被释放，它使自己从所有通知已注册的观察者。



还有一件事要做。这可能是一个好主意，以节省下载资源并且覆盖本地，所以应用程序将不需要下载相同的盖过一遍又一遍。



打开persistencymanager.swift并添加下面的方法：

```
func saveImage(image: UIImage, filename: String) {
  let path = NSHomeDirectory().stringByAppendingString("/Documents/\(filename)")
  let data = UIImagePNGRepresentation(image)
  data.writeToFile(path, atomically: true)
}
 
func getImage(filename: String) -> UIImage? {
  var error: NSError?
  let path = NSHomeDirectory().stringByAppendingString("/Documents/\(filename)")
  let data = NSData(contentsOfFile: path, options: .UncachedRead, error: &error)
  if let unwrappedError = error {
    return nil
  } else {
    return UIImage(data: data!)
  }
}

```


这个代码非常简单。下载的图片将被保存在文件目录，并将getimage()如果匹配的文件不在文件目录中找到返回nil。

Now add the following method to LibraryAPI.swift:
现在添加下面的方法来libraryapi.swift：

```
  func downloadImage(notification: NSNotification) {
    //1
    let userInfo = notification.userInfo as! [String: AnyObject]
    var imageView = userInfo["imageView"] as! UIImageView?
    let coverUrl = userInfo["coverUrl"] as! String
 
    //2
    if let imageViewUnWrapped = imageView {
      imageViewUnWrapped.image = persistencyManager.getImage(coverUrl.lastPathComponent)
      if imageViewUnWrapped.image == nil {
        //3
        dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), { () -> Void in
          let downloadedImage = self.httpClient.downloadImage(coverUrl as String)
          //4
          dispatch_sync(dispatch_get_main_queue(), { () -> Void in
            imageViewUnWrapped.image = downloadedImage
            self.persistencyManager.saveImage(downloadedImage, filename: coverUrl.lastPathComponent)
          })
        })
      }
    }
  }

```


再次，你使用的是隐藏的复杂性，从其他类下载图像的外观模式。通知发件人不关心图像来自网络或文件系统。



建立并运行你的应用程序看看美丽的覆盖在你的horizontalscroller：

![](http://cdn4.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern13.png)



停止应用程序和运行它。注意，有没有延迟加载的封面，因为他们已经保存在本地。你甚至可以断开互联网和您的应用程序将正常工作。然而，有一个奇怪的点这里：微调从未停止旋转！发生了什么事？


你开始旋转时下载的图像，但是你还没有实现的逻辑映像下载完成后停止旋转。你可以发送一个通知，每一次的图像已被下载，但相反的，你会使用其他观察者模式，KVO。

##Key-Value Observing (KVO) 键值观察（KVO）


在KVO，对象可以被通知到一个特定的财产的任何变化；要么自己或另一个对象。如果你有兴趣，你可以更多地了解这个[Apple’s KVO Programming Guide](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/KeyValueObserving/KeyValueObserving.html)。

##How to Use the KVO Pattern 如何使用KVO模式


如上所述，该KVO机制允许一个对象观察变化的属性。在你的情况，你可以使用KVO观察到保存图像的UIImageView图像属性。

打开albumview.swift并添加以下代码以init（框架：albumcover：），只在你添加载体图像作为子视图：

```
coverImage.addObserver(self, forKeyPath: "image", options: nil, context: nil)

```


这增加了self，这是当前类，对载体图像图像特性观察。


你还需要注销作为观察者,仍在albumview.swift，添加以下代码：

```
deinit {
  coverImage.removeObserver(self, forKeyPath: "image")
}

```


最后添加此方法

```
override func observeValueForKeyPath(keyPath: String, ofObject object: AnyObject, change: [NSObject : AnyObject], context: UnsafeMutablePointer<Void>) {
  if keyPath == "image" {
    indicator.stopAnimating()
  }
}

```



你必须在每一个类中实现这种方法作为一个观察者。系统每一次都会有一次观测到的性能变化来执行这个方法。在上面的代码中，你停止旋转时的“形象”性质的变化。这样，当一个图像被加载，微调将停止转动。


建设和运行您的项目。微调应该消失：

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern14.png)



如果你适当使用和终止它，你会注意到，你的应用程序的状态没有保存。你看的最后一张专辑当应用程序启动时不会是默认的相册。

##The Memento Pattern 备忘录模式




备忘录模式捕捉和表现对象的内部状态。换句话说，它可以节省你的东西。后来，这种外在的状态可以在不破坏封装恢复；即，私有数据保密。

##如何使用备忘录模式


在viewcontroller.swift中添加下面的两种方法：

```
//MARK: Memento Pattern
func saveCurrentState() {
  // When the user leaves the app and then comes back again, he wants it to be in the exact same state
  // he left it. In order to do this we need to save the currently displayed album.
  // Since it's only one piece of information we can use NSUserDefaults.
  NSUserDefaults.standardUserDefaults().setInteger(currentAlbumIndex, forKey: "currentAlbumIndex")
}
 
func loadPreviousState() {
  currentAlbumIndex = NSUserDefaults.standardUserDefaults().integerForKey("currentAlbumIndex")
  showDataForAlbum(currentAlbumIndex)
}

```


saveCurrentState保存当前专辑指数NSUserDefaults – NSUserDefaults是一种标准的数据存储提供的iOS应用程序特定的设置和数据保存。



loadpreviousstate加载以前保存的指标。这不是该备忘录模式实现比较充分的，但是你要有。



现在，添加下面一行在viewcontroller.swift viewDidLoad前scroller.delegate =self：

```
loadPreviousState()

```



当应用程序启动时加载先前保存的状态。但是，你从哪里来拯救这个应用程序的当前状态？你会使用通知来做这个。iOS发送uiapplicationdidenterbackgroundnotification通知当应用程序进入后台。你可以使用该通知称savecurrentstate。那不方便吗？

Add the following line to the end of viewDidLoad:
添加下面一行到viewDidLoad：

```
NSNotificationCenter.defaultCenter().addObserver(self, selector:"saveCurrentState", name: UIApplicationDidEnterBackgroundNotification, object: nil)

```



现在，当应用程序即将进入后台，视图会自动调用的savecurrentstate保存当前状态。


向类添加下面的代码：

```
deinit {
  NSNotificationCenter.defaultCenter().removeObserver(self)
}

```



这将确保你将类作为一个观察者的时候释放视图。



构建和运行您的应用程序。导航到一个相册，把应用程序的主页按钮的背景（命令+ Shift +如果你在模拟器），然后关闭你的应用程序从Xcode。重新启动，并检查先前选定的专辑的中心：

![](http://cdn1.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern15.png)



它看起来像这张专辑的数据是正确的，但不是以正确版本的专辑。给什么？



这是可选的方法initialviewindexforhorizontalscroller的意思是！因为这方法不在委托执行，在这种情况下，视图，初始视图总是设置为第一视角。



为了解决这个问题，将下面的代码添加到viewcontroller.swift：

```
func initialViewIndex(scroller: HorizontalScroller) -> Int {
  return currentAlbumIndex
}

```



现在horizontalscroller第一视角设置为任何专辑由currentalbumindex。这是为了确保应用程序的经验仍然是个人和恢复一个的方式。



再次运行您的应用程序。滚动到一张专辑之前，把应用程序的背景下，停止应用程序，然后重新启动以保证问题是固定的：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern16-230x320.png)



如果你看persistencymanager的初始化，你会注意到这张专辑是硬编码的数据并重新创建每一次persistencymanager创建。但最好在一个文件中创建一个相册列表。如何将相册数据保存到文件中？



然后他们需要重现重新创建专辑的情况时，一种选择是遍历专辑的性质，将它们保存到一个plist文件。这不是最好的选择，因为它需要你写特定的代码，根据什么数据/属性是在每个类。例如，如果你创建了一个具有不同性质的电影类，保存和加载该数据将需要新的代码。


此外，您将无法为每个类实例保存私有变量，因为它们不能访问外部类。这正是苹果创造的归档机制的原因。

##Archiving



苹果的一个专门的implementations是归档模式实现。这将一个对象转换为一个流，可以保存和稍后恢复，而不暴露私有属性到外部类。你可以阅读更多关于这个功能在iOS 16的6章的教程书。[Apple’s Archives and Serializations Programming Guide](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Archiving/Archiving.html).

## 如何使用Archiving

 
 打开album.swift改变班线如下：

```
class Album: NSObject, NSCoding {

```



在album.swift添加下面的两种方法:

```
required init(coder decoder: NSCoder) {
  super.init()
  self.title = decoder.decodeObjectForKey("title") as! String
  self.artist = decoder.decodeObjectForKey("artist") as! String
  self.genre = decoder.decodeObjectForKey("genre") as! String
  self.coverUrl = decoder.decodeObjectForKey("cover_url") as! String
  self.year = decoder.decodeObjectForKey("year") as! String
}
 
 
func encodeWithCoder(aCoder: NSCoder) {
  aCoder.encodeObject(title, forKey: "title")
  aCoder.encodeObject(artist, forKey: "artist")
  aCoder.encodeObject(genre, forKey: "genre")
  aCoder.encodeObject(coverUrl, forKey: "cover_url")
  aCoder.encodeObject(year, forKey: "year")
}

```


在NSCoding协议的一部分，encodewithcoder会给你打电话的时候，问一个专辑实例进行归档。相反，init（编码器）初始化将用来重建或解压缩从保存的实例。它虽然简单，但强大。

现在，该相册类可以被归档，添加的代码，实际上保存和加载的专辑列表。



添加下面的方法到persistencymanager.swift：

```
func saveAlbums() {
  var filename = NSHomeDirectory().stringByAppendingString("/Documents/albums.bin")
  let data = NSKeyedArchiver.archivedDataWithRootObject(albums)
  data.writeToFile(filename, atomically: true)
}

```


这将是一种被称为保存专辑的方法。nskeyedarchiver档案专辑阵列为一个名为albums.bin。




当你archive的对象包含其他对象，该文档会自动尝试递归archive的子对象和孩子任何子对象等。在这种情况下，archive开始与专辑，这是一个数组的相册实例。由于数组和专辑都支持NSCopying接口，数组中的每件事都是archive。


现在替换init在persistencymanager.swift用下面的代码：

```
override init() {
  super.init()
  if let data = NSData(contentsOfFile: NSHomeDirectory().stringByAppendingString("/Documents/albums.bin")) {
    let unarchiveAlbums = NSKeyedUnarchiver.unarchiveObjectWithData(data) as! [Album]?
      if let unwrappedAlbum = unarchiveAlbums {
        albums = unwrappedAlbum
      }
  } else {
    createPlaceholderAlbum()
  }
}
 
func createPlaceholderAlbum() {
  //Dummy list of albums
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
  saveAlbums()
}

```



你有移动占位符创作专辑代码可读性的一个单独的方法createplaceholderalbum()。在新的代码，如果它存在的话,nskeyedunarchiver加载相册数据从文件。如果它不存在，它创建的相册数据，并立即保存它为下一次推出的应用程序。


你还想保存相册数据，每次应用程序进入背景。这似乎不是必要的，但如果你后来添加的选项来改变专辑的数据？然后，你会希望这个，以确保所有的变化被保存。



由于主要的应用程序访问所有服务通过libraryapi，这就是应用程序将让persistencymanager知道它需要保存相册数据。



现在添加以下实现方法到 LibraryAPI.swift中

```
func saveAlbums() {
  persistencyManager.saveAlbums()
}

```


此代码只会在调用libraryapi保存相册上persistencymangaer。



将下面的代码添加到saveCurrentState在ViewController.swift结束：

```
LibraryAPI.sharedInstance.saveAlbums()

```


和上面的代码使用libraryapi触发数据视图专辑时保存其状态的保存。

##Final Touches


您将通过允许用户执行删除操作来删除一个相册，或撤消操作以使其改变自己的想法，从而为您的音乐应用程序添加最后的触摸！


添加以下属性视图：

```
// We will use this array as a stack to push and pop operation for the undo option
var undoStack: [(Album, Int)] = []

```



这将创建一个空的撤销堆栈。的undoStack将举行一个元组的两参数。第一张是一张专辑，二是这张专辑的索引。



在viewDidLoad中的reloadscroller()后添加以下代码：

```
let undoButton = UIBarButtonItem(barButtonSystemItem: .Undo, target: self, action:"undoAction")
undoButton.enabled = false;
let space = UIBarButtonItem(barButtonSystemItem: .FlexibleSpace, target:nil, action:nil)
let trashButton = UIBarButtonItem(barButtonSystemItem: .Trash, target:self, action:"deleteAlbum")
let toolbarButtonItems = [undoButton, space, trashButton]
toolbar.setItems(toolbarButtonItems, animated: true)

```


上面的代码创建了一个工具栏，其中有2个按钮和一个灵活的空间。撤消按钮在这里被禁用，因为撤消堆栈开始空。注意工具栏已经在故事情节中，所以你需要做的是设置工具栏的项目。


你会加入三个方法在viewcontroller.swift，专辑管理动作处理：添加，删除，和撤销。


第一个是增加新专辑的方法：

```
func addAlbumAtIndex(album: Album,index: Int) {
  LibraryAPI.sharedInstance.addAlbum(album, index: index)
  currentAlbumIndex = index
  reloadScroller()
}

```



在这里你添加相册，将其设置为当前专辑索引，并重新加载滚动。



下一步是删除方法：

```
func deleteAlbum() {
  //1
  var deletedAlbum : Album = allAlbums[currentAlbumIndex]
  //2
  var undoAction = (deletedAlbum, currentAlbumIndex)
  undoStack.insert(undoAction, atIndex: 0)
  //3
  LibraryAPI.sharedInstance.deleteAlbum(currentAlbumIndex)
  reloadScroller()
  //4
  let barButtonItems = toolbar.items as! [UIBarButtonItem]
  var undoButton : UIBarButtonItem = barButtonItems[0]
  undoButton.enabled = true
  //5
  if (allAlbums.count == 0) {
    var trashButton : UIBarButtonItem = barButtonItems[2]
    trashButton.enabled = false
  }
}

```

考虑下面的每一个部分:

* 获得这张专辑删除.
* 创建一个变量称为undoaction存储一个元组的专辑，这张专辑的指标。然后将元组添加到堆栈中
* 使用libraryapi从数据结构中删除专辑和重载滚动。.
* 因为在撤消堆栈中有一个动作，您需要启用撤消按钮.




最后，添加撤消操作的方法：

```
func undoAction() {
  let barButtonItems = toolbar.items as! [UIBarButtonItem]
  //1		
  if undoStack.count > 0 {
    let (deletedAlbum, index) = undoStack.removeAtIndex(0)
    addAlbumAtIndex(deletedAlbum, index: index)
  }
  //2		
  if undoStack.count == 0 {
    var undoButton : UIBarButtonItem = barButtonItems[0]
    undoButton.enabled = false
  }
  //3		
  let trashButton : UIBarButtonItem = barButtonItems[2]
  trashButton.enabled = true
}

```



最后考虑上述方法的意见：

* 该方法将对象从堆栈中弹出，给你一个包含已删除的相册及其索引的元组。然后你继续增加专辑的背面。
* 自从你在堆栈中的最后一个对象被删除时，你就需要检查堆栈是否为空。如果是，那就意味着没有更多的动作来撤消。所以你禁用了撤消按钮
* 你也知道，既然你毁掉了一个行动，至少应该有一张专辑封面。因此你启用了垃圾桶。


建立和运行你的应用程序来测试你的撤销机制，删除一张专辑（或两者），并点击撤消按钮看到它的动作：

![](http://cdn2.raywenderlich.com/wp-content/uploads/2014/11/swiftDesignPattern1.png)



这也是一个很好的地方，以测试是否更改您的相册数据保留在会话之间。现在，如果你删除了一张专辑，把应用程序发送到后台，然后终止应用程序，下一次你启动应用程序的显示相册列表应该反映删除。



如果你想得到所有的专辑回来，只是删除应用程序并运行它再从Xcode安装一个新的副本的入门资料。


