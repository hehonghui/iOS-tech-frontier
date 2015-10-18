* 原文链接：[Getting Started with Core Data Tutorial](http://www.raywenderlich.com/115695/getting-started-with-core-data-tutorial)
* 原文作者：[Pietro Rea](http://www.raywenderlich.com/u/pietrorea)
* [译文出自：开发者前线 www.devtf.cn](www.devtf.cn)
* 译者：[LastDay](http://lastday.github.io)
* 校对者：[LastDay](http://lastday.github.io)
* 状态：完成


#Core Data Tutorial教学


在这篇文章中我们将学习Core Data的系列教程，你将使用Swift2.0写你的Core Data。你将发现在Xcode中它是很容易上手的，从启动代码导师数据模型编辑器。在教程结束后，你会了解到：

* 使用Xcode的model editor将你想存储在Core Data。
* 添加新的记录到 Core Data
* 从Core Data中读取一组数据
* 在表视图中显示所获取的结果

你也将会了解Core Data背后的数据是什么，以及如何进行交互。OK，现在让我们来构建我的app吧。

##开始

打开你的Xcode新建一个iPhone工程，选择Single View Application template起名为HitList并且选择Use Core Data。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-2.png)

选中Use Core Data复选框后将会在AppDelegate.swift生成Core Data stack样本代码

Core Data stack由一组对象组成，方便于检索和保存Core Data的数据。有一个对象最为一个整体来管理Care Data的状态和数据模型等等。

这个示例程序的想法很简单。有一个被叫"hit list"的表视图。你可以在这个列表中添加名字，并且最终你将使用Core Data确保数据在各个环节之间。

点击Main.storyboard在Interface Builder.接下来点击Editor，选择Navigation Controller。具体操作如图所示:
![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-4.png)

返回Interface Builder，拖拽一个Table view。

接下来拖拽一个Bar Button Item将它放置到navigation bar。最终，起名为Add。就像这样

![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-7.png)

当你每次点击Add的时候，一个包含文本信息字段的弹框将会出现在屏幕上显示。在那里你能够输入默认的名字到进入文本域。


如果你想知道原因，你可以不设置表示图的委托，这样就不会触发任何行为。

打开Assistant Editor拖拽table view到 ViewController.swift，在类中插入一个outlet：

![](http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-10.png)

起名为 tableview

```
@IBOutlet weak var tableView: UITableView!

```

同样将Add拖拽到ViewController.swift，创建一个action 命名为addName：

```
@IBAction func addName(sender: AnyObject) {
 
}

```

那么现在你可引用表示图和按钮了。接下来就是建立表示图模型。在ViewContrroller.swift中添加一下代码：

```
//Insert below the tableView IBOutlet
var names = [String]()

```

names是一个可变的shtring类型的数组，在tableview中显示。

在viewDidLoad()中实现一下代码：

```
override func viewDidLoad() {
  super.viewDidLoad()
  title = "\"The List\""
  tableView.registerClass(UITableViewCell.self,
    forCellReuseIdentifier: "Cell")
}

```

在这里将建立一个标题，注册UITableViewCell在table view类中。table view将返回正确类型的cell

仍然在ViewController.swift，添加UITableViewDataSource,UITableViewDelegate

```
//Add UITableViewDataSource to class declaration
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate{
  //这里添加
	tableView.dataSource = self
	tableView.delegate = self


}

```



这个时候Xcode会提示ViewCotroller不符合协议.实现data source方法修改这个错误。

```
// MARK: UITableViewDataSource
func tableView(tableView: UITableView,
  numberOfRowsInSection section: Int) -> Int {
  return names.count
}
 
func tableView(tableView: UITableView,
  cellForRowAtIndexPath
  indexPath: NSIndexPath) -> UITableViewCell {
 
  let cell =
  tableView.dequeueReusableCellWithIdentifier("Cell")
 
  cell!.textLabel!.text = names[indexPath.row]
 
  return cell!
}

```

如果你用过UITableView，这段代码看起来会很相似。第一个方法就是names数量。

第二个方法tableView(_:cellForRowAtIndexPath:)返回对cell对象。

不要现在就运行。首先你需要一个输入names的方法然后在table view中进行显示他们。

实现addName IBAction

```
//Implement the addName IBAction
@IBAction func addName(sender: AnyObject) {
 
  let alert = UIAlertController(title: "New Name",
    message: "Add a new name",
    preferredStyle: .Alert)
 
  let saveAction = UIAlertAction(title: "Save",
    style: .Default,
    handler: { (action:UIAlertAction) -> Void in
 
      let textField = alert.textFields!.first
      self.names.append(textField!.text!)
      self.tableView.reloadData()
  })
 
  let cancelAction = UIAlertAction(title: "Cancel",
    style: .Default) { (action: UIAlertAction) -> Void in
  }
 
  alert.addTextFieldWithConfigurationHandler {
    (textField: UITextField) -> Void in
  }
 
  alert.addAction(saveAction)
  alert.addAction(cancelAction)
 
  presentViewController(alert,
    animated: true,
    completion: nil)
}

```
每次点击Add按钮的时候，这个方法该方法弹出文本域和两个按钮，保存和取消。

点击保存，将其名称插入到数组中，table view将重新加载数据并显示。

在这里，构建并且第一次运行我们的应用程序。点击顶部的Add按钮，将会插入一个弹框。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-12.png)

添加4个或者5个左右的数据，就是下面的样子

![](http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-14.png)

你的table view将显示数据，但是并不能实现持久化，什么意思呢？就是说我们现在的数组数据是存放在内存中的，但是如果我们一旦强制退出应用程序或者重新启动你的设备，你的数组数据就会被销毁。

Core Data提供持久化，意思就是他可让数据保持为持久状态，尽管应用重新启动或者是重新运行。

你现在还没有添加任何Core Data。让我们来测试下，切换到Simulator，点击Shift+⌘+H,将会返回home界面。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-15.png)

看到HitList图标，点击它切换到应用，这些名字一样存在着。这跟我们刚才描述的不一样，为什么呢？

当你点击Home按钮的时候。这个时候操作系统会瞬间冻结所有当前在内存中的一切信息。包括我们的名称数组字符串。同样，当我们的应用切换回去的时候，操作系统会恢复过去的记忆，就像你从来没有离开过一样，意思就是我们的names被恢复了。

多任务模式早在iOS 4中推出。他们创造了iOS用户无缝体验，同时也添加了持续性的概念，这心真的存在吗？

不，这不是真的。如果你完全杀死应用程序或者关闭你的iPhone，你的names数组就会消失。你可以体验下，快速的双击Home。

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-17.png)

在这里，向上拉动你的应程序，在这里就会将程序杀死了。这个时候反回Home，再一次点击你的应用程序，names就会消失。

以上展现的两种方式书有差异的，所以这里看来了解熟悉多任务模式是显而易见的。但是这个在用户的头脑中是没有什么区别的。用户不会在乎通过哪种方式切回到Home，或者切回到应用。

现在的问题就是怎么样让应用无论通过哪种方式返回都能够存在。

现在就到了我们要讲诉的东西了，实现真正的持久性，数据在一个应用中无论怎样都会存在着。

##数据建模

现在你知道如何检测持久性了，让我们开始Core Data的学习。你的目标很简单，就是持久化你输入的名字，重新启动后仍然存在这个应用程序中。

这这里，你已经了解了如何在内存中存储names名称。在这里你将使用Core Data代替那种方法。

第一步就是创建一个managed object model，就是意味着通过Core Data数据将在磁盘上。默认情况下，Core Data使用SQlite数据库作为持久化的存储，所以你可以想象数据模型看成数据库架构。

当你创建了工程的时候我们选择了Use Core Data，Xcode会自动创建一个数据模型文件叫做HitList.xcdatamodeld

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-19.png)

点击HitList.xcdatamodeld打开它，正如你所看到的，Xcode有自己的数据模型编辑器，就像如图所示那样

![](http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-20.png)

这个数据模型有很多功能，让我们创建一个简单的数据实体。

在左下方点击 Add Entity创建一个新的实体，双击我们新创建的实体，并且改名为Person，就像这样：

![](http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-21.png)

你可能很想知道为什么数据模型编辑器使用"实体"，而不是简单的定义一个新类。你不久就会看到，Core Data的数据来源于自身的词汇表。以下有一些常见的书语你可能会遇到：

* 在Core Data中一个entity是一个类的定义。举一个典型的例子就是员工和公司的例子。在关系型数据库中，一个实体对应一个表。
* 一个attribute是连接到特定实体信息的一部分。例如，一个员工可以拥有姓名，职位，工资等attribute。在数据库中attribute（属性）对应表中特定的字段。
* relationship是多个实体之间的一个连接。

现在你知道了什么是attribute，返回模型编辑并且添加一个attribute到Person中，选择Person，点击+。

建立一个name并且选择类型为String。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-22.png)

在Core Data包含很多种数据类型，String是其中的一种。

##保存数据到Core Data

在ViewCortroller.swift中 Import Core Data

```
//Add below "import UIKit"
import CoreData

```

在Objective-C你可能不得不手动链接框架，但是在Swift中，一个简单的Import语句就就可以让你在你的代码中使用API。

接下来，更换模型。

```
//将names变味people，并且将people类型改为NSManagedObject类型
var people = [NSManagedObject]()


```

接下来你存储的是Person实体而不是names，所以将数据模型更名为people，并且它现在是NSManagedObject类型而不是简单的String类型。

NSManagedObject被称为在Core Data中的单一对象。你必须使用它创建，修改，保存和删除操作你的持久数据。

就在刚刚你已经改变了视图的模型，你必须也要使用下面的代码替换原来的数据源。

```
//Replace both UITableViewDataSource methods
func tableView(tableView: UITableView,
  numberOfRowsInSection section: Int) -> Int {
    return people.count
}
 
func tableView(tableView: UITableView,
  cellForRowAtIndexPath
  indexPath: NSIndexPath) -> UITableViewCell {
 
    let cell =
    tableView.dequeueReusableCellWithIdentifier("Cell")
 
    let person = people[indexPath.row]
 
    cell!.textLabel!.text =
      person.valueForKey("name") as? String
 
    return cell!
}

```

其实通过比较来看其实最显著的变化在cellForRowAtIndexPath，仔细看看就能够发现其中的变化。

当然，你需要注意的地方还有就是你是如何从NSManagedObject抓去name属性。

```
cell!.textLabel!.text = person.valueForKey("name") as? String

```

为什么用以上的方式呢？因为NSManagedObject并不知道在你的数据模型中name是如何被定义在你的数据模型中的，因此没有办法直接访问你的name属性。唯一的方法就是用过Core Data提供的key-value来读取，这种方式通常被叫做KVC。

我在这里同样也简单的介绍一下KVC吧。就是说如果你是一个新的iOS开发者可能不了解什么是KVC或者是key-vale编码。KVC就是Cocoa和Cocoa Touch的机制来访问一个对象的而属性间接的使用字符串来识别。在以上的情况下，KVC就像是一本字典。

接下来就是改变我们@IBAction addName方法：

```
let saveAction = UIAlertAction(title: "Save",
  style: .Default,
  handler: { (action:UIAlertAction) -> Void in
 
    let textField = alert.textFields!.first
    self.saveName(textField!.text!)
    self.tableView.reloadData()
})

```

看到上面我们有新添加了一个saveName方法

```
func saveName(name: String) {
  //1
  let appDelegate =
  UIApplication.sharedApplication().delegate as! AppDelegate
 
  let managedContext = appDelegate.managedObjectContext
 
  //2
  let entity =  NSEntityDescription.entityForName("Person",
    inManagedObjectContext:managedContext)
 
  let person = NSManagedObject(entity: entity!,
    insertIntoManagedObjectContext: managedContext)
 
  //3
  person.setValue(name, forKey: "name")
 
  //4
  do {
    try managedContext.save()
  //5
    people.append(person)
  } catch let error as NSError  {
      print("Could not save \(error), \(error.userInfo)")
  }
}

```

这里面都是什么呢？我们来分析下

* 在你做svae操作之前，你需要先获取NSManagedObjectContext。你可以认为这是用来managed object context的。想进行保存对象到Core Data中需要两步，首先，你需要插入一个对象到managed object context中。然后提交，将该对象存储到磁盘中。Xcode其实已经产生一个通用的模板，当你选择Use Core Data的时候。这个默认的managed object context存在于application delegate中。要想引用它，你需要获取一个app delegate引用。
* 创建managed object context并且完成NSManagedObject的初始化，init(entity:insertIntoManagedObjectContext:).你可能会想到NSEntityDescription的所有相关东西
* 使用NSManagedObject，必须使用你建立的name，必须使用KVC，否则会出现崩溃现象。
* 你的提交的person被保存在磁盘中，注意save会抛出异常，这就是为什么我们需要使用try do。
* 接下来就要恭喜你已经成功并且安全的的实现了数据的持久化。仍然是当我们插入后将会重新加载视图

这比使用一个字符串可能复杂的很多，但是并不是很复杂。这里的代码就是获取managed object context和entity，

构建并且运行，添加一些类似下面的名字。

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-24.png)

如果你的数据已经在Core Data中存储，并且实现了数据的持久化，这个时候我们杀死我们的app，然后重现加载我们程序，等一下，发生了什么？table view是空的

![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-26.png)

你存续到Core Data中的数据但是重新加载后并没有，仍然是空的，其实数据事实上在那里的等待着，你并没有显示它、

从Core Data中获取

要得到持久化的数据，你必须取出它。在ViewController.swift中天添加一个方法

```
override func viewWillAppear(animated: Bool) {
  super.viewWillAppear(animated)
 
  //1
  let appDelegate =
  UIApplication.sharedApplication().delegate as! AppDelegate
 
  let managedContext = appDelegate.managedObjectContext
 
  //2
  let fetchRequest = NSFetchRequest(entityName: "Person")
 
  //3
  do {
    let results =
      try managedContext.executeFetchRequest(fetchRequest)
    people = results as! [NSManagedObject]
  } catch let error as NSError {
    print("Could not fetch \(error), \(error.userInfo)")
  }
}

```

以上代码中做了什么？

* 就像刚才说的，在做一些操作前你需要获取一个managed object context。从delegate中引用managed object context.
* 根据名字就能看出来，NSFetchRequest就是返回的数据。
* executefetchrequest()返回满足managed objects读取请求中指定的标准数组。

构建并且重新运行，就能发现你想要的效果了

![](http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/CoreData2-28.png)

现在你就可以随意的测试了。

















