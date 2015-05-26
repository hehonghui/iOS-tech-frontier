# Swift  EventKit的初学者指南--请求允许
---

> * 原文链接 : [Beginner’s Guide to EventKit in Swift – Requesting Permission](http://www.andrewcbancroft.com/2015/05/14/beginners-guide-to-eventkit-in-swift-requesting-permission/)
* 原文作者 : [Andrew](http://www.andrewcbancroft.com/)
* [译文出自 :  开发技术前线 www.devtf.cn](http://www.devtf.cn)
*  译者 : [MollyMmm](https://github.com/MollyMmm) 
* 校对者: []()  
* 状态 : 未校对

EventKit provides a set of classes for accessing and manipulating a user’s calendar events and reminders. In the tutorial that follows, my goal is to walk you through the first steps of setting up an app that utilizes EventKit. I will demonstrate how to request permission to the user’s calendar and show a couple of examples for how to handle the user’s response (for when they grant access, or deny it).

EventKit为获取和操作用户日历事件和提醒提供了一系列的类.在下面的教程中,我的目标是带领你走出利用EventKit建立一个应用程序的第.我将演示如何向用户的日历请求许可,我还将展示几个处理用户响应的例子(当他们授予访问权限,或者拒绝).

#Example scenario
#示例场景

Let’s start by proposing a basic scenario to serve as this tutorial’s example.

让我们先提出一个基本方案，作为本教程的例子。

Suppose that we’re building an app that, for now, has a single View Controller. We’d like this View Controller to display a list of calendars if the user grants us permission to do so. If they deny permission, we’d like to show a message to them that indicates that our app can’t function without this permission, and we’ll allow them to click a button to grant us permission in the Settings of their device.

假设我们正在构建一个应用程序，现在，有一个单一的视图控制器。我们希望这个视图控制器显示日历列表,如果用户授权允许我们那样做.如果用户拒绝访问,我们将向用户展示一个消息,用来说明我们的应用程序在没有访问权限时不能运行,我们也将允许他们通过单击一个按钮能够在他们的设备的设置中设置授权访问.

I’ve created such an app as an example – jump over to GitHub to grab the code and explore. Read on for an explanation of some of the finer points of the setup and code.

我已经创建一个那样的应用程序作为例子--跳到GitHub中查看并研究这个例子的代码.

>资源

> [Xcode工程示例](https://github.com/andrewcbancroft/EventTracker/tree/ask-for-permission)
 
#Storyboard setup
#故事面板设置

One of the first things you’ll deal with in EventKit is the need to set yourself up with a UI to handle the different responses that the user can give you on that first application launch when you ask, “Can we access your calendar?”. We’ll get to the particulars of how request that permission shortly. But first, let’s dissect how we might arrange a Storyboard with some views that do the right thing for a given response to that permission prompt.

你使用EventKit的第一步就是需要为自己创建一个用户界面来处理当你第一个程序启动时用户对"该程序可以访问你的日历吗?"对出不同的响应,不久,我们将得到如何请求这个许可的详情.但首先,让我们来剖析我们如何用对于一个许可操作导致的给定响应能够做正确的操作的一些视图来安排我们的故事板.

The user can either grant permission, or deny permission to interact with their calendar or reminders. We need to be prepared for either scenario.

用户可以授予访问权限,也可以拒绝授予访问权限来通知他们的日历或者提醒.我们需要为这两种情况做好准备.

#Tableview for the calendar list for when access is granted
#当被授予访问权限时,tableview显示日历列表

I’m feeling optimistic today, so let’s begin with the case where the user grants us permission to their calendar from the get-go.

我今天持乐观态度,所以让我们开始处理从一开始用户就授予我们访问他们日历的权限的情况.

When the user grants us permission, we’d like to list out their calendars inside a table view. We’ll worry with setting up the data source later in the tutorial. For now, we’ll drag over a table view from the Utilities pane.

当用户授予我们访问权限,我们想列出一个表视图的日历.在接下来的教程中,我们将担心数据源的设置.现在,我们将从实用工具栏中拽一个表格视图过来.

To get the table view to fill the whole screen, I do a couple of things. Usually, when you drag one out from the Utilities pane, the table view will fill the whole scene in the Storyboard. From that layout, I drag the top edge down until it “snaps” to the line where I’d expect the bottom of the status bar to be positioned. Then I set the following constraints:

为了得到填满整个屏幕的表视图,我做了几件事情.通常,当你从实用工具栏中拽一个表视图过来的时候,它会在故事板中填满整个场景.在布局中我向下拖顶部边缘知道它"捕捉"到我所期望的状态栏底部被定位的那行.然后,我设置了以下限制:

>*    Center X   
*  Center Y 
* Equal width to Superview
* Top space to Top Layout Guide for height.

I’ve created a short screencast on setting up a table view if you’d like a complete walkthrough:

我已经创建了一个设置表视图的简短截屏,如果你想要一个完整的练习,可以参看下面链接的内容:

>Resources
资源

> [Screencast: Setting Up a Table View](http://bit.ly/WatchSetUpTVFromACB)

>[ Full transcript](http://www.andrewcbancroft.com/2015/05/18/swift-how-to-setting-up-a-table-view/)

Here’s a detailed view of the constraints, along with a visual of what the Storyboard Scene looks like with the table view installed:

这里有这些约束的详细视图,以及故事板看起来像装表视图的视觉效果.

![Detailed view of constraints](http://ww1.sinaimg.cn/mw690/a10328aajw1eshuj6c424j20850ciwfn.jpg)

![Table view in Storyboard](http://ww2.sinaimg.cn/mw690/a10328aajw1eshuj777a3j212u0oxguc.jpg)

As a final note, I’ve set the hidden property of the table view to true in the Storyboard. I’ll toggle the table’s visibility based on the user’s granting or denying of the calendar access later, but I thought it was worth pointing out that the initial state of my table view in the example is hidden.

最后一点,在故事板中我已经将这个表视图的hidden属性设置为true.根据用户允许或者拒绝对日历的访问后,我将切换表的可见性,但我认为值得指出的是在我例子中表视图的初始状态是被隐藏.

#“Need permission” view for when access is denied

#访问被拒绝时的"需要许可"视图

There will be times when a user denies access to the calendar before realizing that doing so essentially stops all the functionality provided by your app. If your entire app, or even just a portion of it requires access to function, you need a way to inform the user of this, and provide them a way to navigate to settings and grant access manually if possible.

但有时,用户拒绝授权访问日历,在意识到这样做将导致基本上停止你应用程序所有的功能之前,如果你的整个应用程序或者只是该应用程序的一部分需要访问功能,你需要一种方法来告知用户,并为他们提供一种方法跳到用户设置,如果可能的话让用户手动授予访问权限.

The way I did this in the sample project was to organize a new View onto the Storyboard Scene which contains a label with some instructions, and a button that takes the user to the Settings page for our app when they tap on it.

我在示例项目中的方法是在故事板场景中组织一个新的视图,该视图包含一个展示操作说明的标签和一个点击后使用户进入我们应用程序的设置界面的按钮.

Once again, some constraints are involved in getting things to appear correctly at run-time. I won’t go into the details of this here, since it’s likely that every implementation of this will be slightly different.

再次,一些约束涉及到在运行时使一些事物正确的显示.在这里我不会讲述这个细节,因为它很可能因为每一个执行操作而有一点不同.

One thing I will point out though, is that the View’s alpha has been set to 0 so that I can perform a nice [fade in transition](http://www.andrewcbancroft.com/2014/07/27/fade-in-out-animations-as-class-extensions-with-swift/) if the user denies access. Here’s a look at the Scene with the invisible “NeedPermissionsView” installed:

我将指出的意见事情是,这个视图的透明度已经被设置为0,以便如果用户拒绝授权,我能够展示一个逐渐消失的效果.下面就来看看在设置了隐藏“NeedPermissionsView”的场景：

![Need permission view](http://ww3.sinaimg.cn/mw690/a10328aajw1eshuj6ra19j214p0ou12b.jpg)

#The role of the Event Store

# Event Store的角色

At the heart of EventKit is the EKEventStore. EKEventStore is the central “thing”. Creating an instance of EKEventStore provides developers with an API for performing various read/write operations on the user’s calendars and reminder lists.

EventKit的核心是EKEventStore.EKEventStore是事物的中心.创建EKEventStore的一个实例,为开发人员提供了对用户的日历和提醒列表中执行各种读/写操作的API.

A View Controller that interacts with the calendar should hold a reference to an EKEventStore instance. It’s easy to create one – here’s an example:

一个与日历交互的视图控制器应该有一个引用EKEventStore的实例.这很容易被创建--这里是一个例子:

>ViewController.swift

>```
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    let eventStore = EKEventStore()
    // ...    
}
```

#Checking for calendar authorization
#检查日历的授权

Once we have a reference to an EKEventStore instance, we can use it to do things like check whether or not the user has granted us permission to use their calendar. From there, we can make decisions about whether or not we need to request permission, and subsequently figure out which view to show (the table view or the need permission view).

一旦我们有了引用EKEventStore的实例,我们可以做像检查用户是否授权访问他们的日历这样的事情.根据这里,我们可以做是否需要请求许可的决定,随后确定要显示的视图(表视图或者需要许可视图).

Where we check for calendar authorization is important. My recommendation is to check each time the view appears (ie, in viewWillAppear(), because it’s completely possible that the user could grant access at first, switch to settings, and deny access. Our app would need to respond appropriately.
我们在哪里检查日历授权很重要.我的建议是每次在视图出现时检查(即在viewWillAppear()中),因为用户首次授予访问权限,切换设置,拒绝访问的情况是完全有可能的.我们的应用程序需要做出适当的响应.

In the example project provided with this article, I’ve created a function named checkCalendarAuthorizationStatus(). Here a peek at what it does:

在这个文章提供的示例工程中,我已经创建了一个名为checkCalendarAuthorizationStatus()的函数.
接下来看看它的实现:

>ViewController.swift
>```
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    // ...    
    override func viewWillAppear(animated: Bool) {
        checkCalendarAuthorizationStatus()
    }
        func checkCalendarAuthorizationStatus() {
        let status = EKEventStore.authorizationStatusForEntityType(EKEntityTypeEvent)      
        switch (status) {
            case EKAuthorizationStatus.NotDetermined:
                // This happens on first-run
                requestAccessToCalendar()
            case EKAuthorizationStatus.Authorized:
                // Things are in line with being able to show the calendars in the table view
                loadCalendars()
                refreshTableView()
            case EKAuthorizationStatus.Restricted, EKAuthorizationStatus.Denied:
                // We need to help them give us permission
                needPermissionView.fadeIn()
            default:
                let alert = UIAlertView(title: "Privacy Warning", message: "You have not granted permission for this app to access your Calendar", delegate: nil, cancelButtonTitle: "OK")
                alert.show()
        }
    }
    // ...
}
```

The key function here is EKEventStore's authorizationStatusForEntityType function. Passing in EKEntityTypeEvent is what corresponds to the user’s calendar. If we wanted to check for access to their reminders, we’d use EKEntityTypeReminder.

这里关键的功能是EKEventStore的 authorizationStatusForEntityType实现的.传入的EKEntityTypeEvent用于跟用户日历进行交互.如果我们想要检查他们的提醒的权限,我们将在这里使用EKEntityTypeReminder.

The possible EKAuthorizationStatus enumeration values are simply switched over – the logic to be performed is encapsulated in separate functions for ease of readability.

EKAuthorizationStatus的可能值根据switch里的相应的case来执行封装好的方便阅读的独立功能的逻辑代码.

Let’s step through each of those functions one by one…

让我们一步步来看一看这些功能.

#Requesting access to calendars
#请求访问日历

As the title of this tutorial suggests, all things start here. Whenever our application loads and we call authorizationStatusForEntityType, the status that will be returned is NotDetermined. It’s at this point that we’d like to request access to the calendar.

正如标题所说的,所有的事情从这里开始.每当我们的应用程序加载和调用authorizationStatusForEntityType的时候,将返回NotDetermined的状态.就是在这一点上我们想请求访问日历.

To do so, let’s dissect the requestAccessToCalendar function:

为了这样做,按照下面的方法定义requestAccessToCalendar函数:

>requestAccessToCalendar()
>``` 
class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
   // ...  
   func requestAccessToCalendar() {
        eventStore.requestAccessToEntityType(EKEntityTypeEvent, completion: {
            (accessGranted: Bool, error: NSError!) in            
            if accessGranted == true {
                // Ensure that UI refreshes happen back on the main thread!
                dispatch_async(dispatch_get_main_queue(), {
                    self.loadCalendars()
                    self.refreshTableView()
                })
            } else {
                // Ensure that UI refreshes happen back on the main thread!
                dispatch_async(dispatch_get_main_queue(), {
                    self.needPermissionView.fadeIn()
                })
            }
        })
    }
    // ...
}
```

Our EKEventStore instance provides a function called requestAccessToEntityType. Once again, passing in EKEntityTypeEvent is what signals that we’re requesting access to the calendar. The rest of the interesting parts are found in the completion closure that we provide.

我们的EKEventStore实例提供了一个名为requestAccessToEntityType的函数.再次将EKEntityTypeEvent作为我们请求访问日历的参数传递.剩余的有趣的部分在我们提供的封装完的闭包里能够找到.

There are three main things to note with this portion of the implementation:

在实现里有三个主要的事情需要注意:

1.The two parameters that are passed in to the closure is a Bool indicating access was granted (true) or denied (false). The second is an NSError.

2.We need to call dispatch_async() and indicate that we want to jump back over to the main queue to execute our UI refreshes.

3.self.needPermissionView.fadeIn() utilizes a UIView extension from my post, Fade In / Out Animations as Class Extensions in Swift.

1.传递到闭包里的两个参数一个是用来说明访问权限是否被授予的Bool类型的,另一个是NSError.

2.我们需要调用dispatch_async(),并表明我们要调回主队列中执行刷新UI的操作.

3.self.needPermissionView.fadeIn()作为我操作中的一个UIView的拓展,[Swift中渐入/淡出动画的拓展类(Fade In / Out Animations as Class Extensions in Swift)](https://github.com/andrewcbancroft/EventTracker/tree/ask-for-permission).

#Access granted! Load calendars and refresh table view

授予访问权限！加载日历和刷新表视图

When access is granted, we can call the eventStore instance’s calendarsForEntityType function and pass it EKEntityTypeEvent to grab an array of the user’s calendars to display in our table view. Here’s a look:

当被允许访问的时候,我们可以调用eventStore实例中的calendarsForEntityType函数,并传递EKEntityTypeEvent去抓取用户日历的数组在我们的表视图中显示.下面就来看看:

>loadCalendars()
>```
 class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    // ...
    var calendars: [EKCalendar]?
    // ...    
    func loadCalendars() {
        self.calendars = eventStore.calendarsForEntityType(EKEntityTypeEvent) as? [EKCalendar]
    }    
    func refreshTableView() {
        calendarsTableView.hidden = false
        calendarsTableView.reloadData()
    }
    // ...
}
```

#Access denied – Show needs permission view
#拒绝访问--显示需要许可视图

When access is denied, we need to unveil the “Needs Permission View” we created in our Storyboard Scene.

当访问被拒绝的时候,我们需要弹出在故事板场景中创建的“Needs Permission View”.

Recall that in that view, there’s a button to direct the user to the Settings page for our app so that they can easily grant access to the calendar from there. That button is wired up to an IBAction. Here’s an example implementation of that IBAction:

在这个视图中,上面的函数重新被调用,这样有一个能够让用户直接跳转到我们应用程序的设置页面中,以便他们能够从那里授权日历访问.这个按钮连线到了一个IBAction.下面有实现IBAction的例子:

>goToSettingsButtonTapped()
>```
 class ViewController: UIViewController, UITableViewDataSource, UITableViewDelegate {
    // ...
    @IBAction func goToSettingsButtonTapped(sender: UIButton) {
        let openSettingsUrl = NSURL(string: UIApplicationOpenSettingsURLString)
        UIApplication.sharedApplication().openURL(openSettingsUrl!)
    }
    // ...
}
```

#Wrapping up
#结束语

That pretty much completes the setup process for working with Event Kit! The remaining cases for the checkCalendarAuthorizationStatus() function simply re-use the functions I just dissected when exploring the requesting permission process.

这几乎完成了使用 Event Kit的开始工作!对于checkCalendarAuthorizationStatus()函数的其余案例简单的重用,我只是简单的剖析了请求允许的过程.

I encourage you to head over to GitHub and dive into the code for yourself as you get started with utilizing Event Kit in your app!

我鼓励你们[跳到Github](https://github.com/andrewcbancroft/EventTracker/tree/ask-for-permission),并且作为你应用程序中利用Event Kit的开始,自己深入研究这些代码.