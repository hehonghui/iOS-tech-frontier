在iOS8中给TableView加一个搜索栏(Swift)
---
> * 原文链接 : [Add Search to Table View Tutorial in iOS8 with Swift](http://www.ioscreator.com/tutorials/add-search-table-view-tutorial-ios8-swift)
* 原文作者 : [Arthur Knopper](https://twitter.com/ioscreator)
* 译文出自 :  [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [StormXX](https://github.com/StormXX) 
* 校对者: [Harries Chen](https://github.com/mrchenhao)
* 状态 : 已校对

现在大多数的iOS应用都会在TableView上放一个搜索框来提供一个搜索内容的入口。本篇教程将会讲述如何在TableView中添加一个搜索功能，能让用户来搜索整个TableView中的内容。在以前的iOS版本中，都是使用一个UISearchDisplayController对象来实现搜索。然而在iOS8中，这个类被弃用了，UISearchController被用来代替它。本篇教程也将会教大家使用UISearchController.
注：此教程的开发环境是iOS 8.3,Xcode 6.3,Swift 1.2.

打开Xcode然后创建一个新的**Single View Application**。在**product name**这一栏填写IOS8SwiftAddSearchTableViewTutorial 然后在Organization Name和Organization Identifier填写你自己的值。编程语言选择Swift然后在Devices选择iPhone。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54d7b6bde4b00ffa20f1df7a/1423423166651/?format=1500w)

上面的做好之后，打开Storyboard，移除自动生成的ViewController，然后拖拽一个NavigationController到Storyboard里。当刚刚的"初始"ViewController被删除了之后，这个APP就没有“起点”了。选择刚刚拖进来的NavigationController然后进到右边的Attribute Inspector。在 View Controller这一个节点下勾选”Is Initial View Controller“

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54d7b766e4b08579c6ac7813/1423423335054/?format=750w)

双击左边 TableViewController 的导航栏，把它的title设为“Numbers”。选择TableViewCell然后到右边的 Attributes Inspector 面板,把 Cell 的 Indentifier 设为"Cell"。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54d7b7dae4b002f56c3e6ca4/1423423451428/?format=750w)

Storyboard现在看起来应该像下面这样

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54d7b879e4b0ff7e52fca235/1423423610868/?format=2500w)

因为我们删掉了自动生成的ViewController，所以我们也可以删掉ViewController.swift。添加一个新的swift文件到项目中，选择**iOS->Source->Cocoa Touch Class**。把它命名为TableViewController 然后设置它为UITableViewController的子类。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54d7b8a7e4b0b30d9474144a/1423423656532/?format=1500w)

点开TableViewController.swift文件，把类的声明这一行改成下面这样：

`class TableViewController: UITableViewController, UISearchResultsUpdating {`

UISearchResultsUpdating协议用来更新搜索结果，稍后我们将会添加一个代理方法来让TableViewController继承这个协议。现在在这个类中添加下列属性：

```
let tableData = ["One","Two","Three","Twenty-One"]
var filteredTableData = [String]()
var resultSearchController = UISearchController()
```

这个`tableData`属性用来存储TableView的数据，`filteredTableData`属性用来存储搜索后的结果数据。Search Controller控制搜索的结果，在viewDidLoad中添加下列代码：

```
    override func viewDidLoad() {
        super.viewDidLoad()

    self.resultSearchController = ({
        let controller = UISearchController(searchResultsController: nil)
        controller.searchResultsUpdater = self
        controller.dimsBackgroundDuringPresentation = false
        controller.searchBar.sizeToFit()
            
        self.tableView.tableHeaderView = controller.searchBar
            
        return controller
    })()
        
    // Reload the table
    self.tableView.reloadData()
}
```

resultSearchController的初始化使用了一个闭包。搜索的结果将会在当前的TableView上展示，所以searchResultsController这个参数在UISearchController的初始化方法中被设置成nil。还有，searchResultsUpdater属性被设置成了当前的Table View Controller，background dimming也被设置成false。这个搜索被加到Table View上，数据也被重新加载。接下来，实现TableView的Data Source代理方法：

```
    override func numberOfSectionsInTableView(tableView: UITableView) -> Int {
        // 1
        // Return the number of sections.
        return 1
}
    
    override func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        // 2
        if (self.resultSearchController.active) {
            return self.filteredTableData.count
        }
        else {
            return self.tableData.count
        }
}
    
    
    override func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCellWithIdentifier("Cell", forIndexPath: indexPath) as! UITableViewCell
        
        // 3
        if (self.resultSearchController.active) {
            cell.textLabel?.text = filteredTableData[indexPath.row]
            
            return cell
        }
        else {
            cell.textLabel?.text = tableData[indexPath.row]
            
            return cell
        }
}
```

1. 这个TableView中只有一个Section
2. 当Search Controller被激活，搜索结果的数目被赋值到numberOfRows方法上，否则就使用tableData这个数组。
3. 当Search Controller被激活，所搜结果被展示出来，否则就使用tableData的数据来展示。

最后，实现`UISearchResultsUpdating`协议中的`updateSearchResultsForSearchController`代理方法。

```
 func updateSearchResultsForSearchController(searchController: UISearchController)
    {
        filteredTableData.removeAll(keepCapacity: false)
        
        let searchPredicate = NSPredicate(format: "SELF CONTAINS[c] %@", searchController.searchBar.text)
        let array = (tableData as NSArray).filteredArrayUsingPredicate(searchPredicate)
        filteredTableData = array as! [String]
        
        self.tableView.reloadData()
    }
```

当用户在搜索框输入的时候，`updateSearchResultsForSearchController`被调用。在这个方法里。我们处理我们的搜索条件。`NSPredicate`是一个用来放置筛选表达式的类，我们用它来存储搜索条件。“c”意思是区分大小写。筛选的结果被赋值给filteredTableData然后TableView调用了reloadData方法。编译运行这个项目，然后在搜索框输入搜索关键字，结果就会被展示出来。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54d7c286e4b00ffa20f21dbc/1423426183530/?format=1000w)

你可以下载`IOS8SwiftAddSearchTableViewTutorial`的源代码在ioscreator的[Github](https://github.com/ioscreator/ioscreator)上。

