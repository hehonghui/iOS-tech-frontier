> * 原文链接 : [iOS 9 App Search Tutorial: Introduction to App Search](http://www.raywenderlich.com/116608/ios-9-app-search-tutorial-introduction-to-app-search)
* 原文作者 : [Chris Wagner](http://www.raywenderlich.com/u/cwagdev)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [kmyhy](https://github.com/kmyhy) 
> 
> Note from Ray: This is an abridged version of a chapter from iOS 9 by Tutorials, to give you a sneak peek of what’s inside the book, released as part of the iOS 9 Feast. We hope you enjoy!

> Ray 注：本文作为《iOS 9 Feast》中的一部分，节略自 《iOS 9 Tutorials》其中一章——通过本文，您可对全书内容窥见一斑。祝您阅读愉快！

There’s been a big hole in Spotlight on iOS for a long time. Although users can use it to find you app, they can’t see inside it — to all the content that they really care about. Currently, when users want to get to content in an app, they have to flip through pages on their home screen to find it, open it and then search for what they’re looking for — assuming you actually implemented a search feature in the first place!

在相当长的一段时间内，iOS 的 Spotlight 都是一个大坑。尽管用户可以用它来搜索你的 App，但他们却无法看到其中的内容——他们真正关心的部分。现在，当用户想读取一个 App 中的内容时，他们只能回到 Home 屏一屏一屏翻，找到 App，打开 App，搜索他们想要的内容——假设你的App实现了搜索功能的话。

An especially savvy user could launch your app by using Siri or searching for it in Spotlight, but neither of these tools help the user find what they want inside a non-Apple app. Meanwhile, Apple makes things like contacts, notes, messages, email and apps directly searchable within Spotlight. The user simply taps on the search result and goes straight to the content. No fair!

对于比较老练的用户，则可能会通过Siri 或者 Spotlight 来打开你的 App，但无论哪个工具都不能让用户查找“非苹果官方App”内的内容。也就是说，苹果在 Spotlight 中可以查找通讯录、备忘录、信息、邮件以及其它支持查找功能的App中的内容。用户只需要点击搜索结果就可以直接访问相应的内容。这真是太不公平了！

Sometimes it seems like Apple keeps all the fun features to itself, like using Spotlight. The good news is that after Apple developers finish playing around with a feature and feel it’s ready for showtime, it often lets the masses play too, like it did with app extensions in iOS 8.

有时苹果会将一些有趣的功能保留给自己专用，比如 Spotlight。好消息是，每当苹果的开发者调教好一个功能，觉得已经可以把它放出去的时候，他们就会让大伙也尝尝鲜，比如 iOS 8 中的 App 扩展。

With iOS 9, Apple is passing a very exciting feature off to the rest of us; third party developers now have the ability to make their content searchable through Spotlight!

在iOS 9 中，苹果又放出来一个很酷的功能给我们，第三方开发者现在可以在Spotlight 中搜索他们的App 内容了！

In this iOS 9 app search tutorial you’ll discover the power of app searching, and learn how easy it is to integrate into your own apps.

在本教程中，你将领略 App Search 的威力，并学会如何将它集成到你自己的App 中。

##App search APIs
## App Search API
App search in iOS 9 comprises three main aspects. Each is broken into separate APIs that achieve distinct results, but they also work in concert with one another:

在iOS 9 中，App Search由三个部分组成。每一部分根据不同的目的分成独立的 API，但它们也能和其它部分一起使用：

 - NSUserActivity 
 - Core Spotlight 
 - Web markup

###NSUserActivity
###NSUserActivity

Being a clever little feature, this aspect of app search makes use of the same NSUserActivity API that enables Handoff in iOS 8.

在App Search 中，使用了NSUserActivity，这是一个灵活小巧功能，在iOS 8 Handoff 中就使用到了NSUserActivity 。

In iOS 9, NSUserActivity has been augmented with some new properties to enable search. Theoretically speaking, if a task can be represented as an NSUserActivity to be handed off to a different device, it can be stored in a search index and later continued on the same device. This enables you to index activities, states and navigation points within your app, allowing the user to find them later via Spotlight.

在iOS 9 中，NSUserActiviy增加一些新的属性以支持 App Search。从理论上讲，如果一个任务能够转变成一个 NSUserActivity 并转交给其它设备，它也能转换为一个搜索项并在同一个设备上继续处理。这就有可能对App 上的活动、状态和导航点进行索引，这样用户才能在 Spotlight 中对其进行搜索。

For example, a travel app might index hotels the user has viewed, or a news app might index the topics the user browsed.

例如，一个旅游类App 可能会将用户查看过的酒店进行索引，而一个新闻类App 会将用户浏览过的文章进行索引。

> Note: This tutorial will not specifically cover Handoff, but you’ll
> learn how to make content searchable once it’s viewed. To get up to
> speed at Handoff you can check out the Getting Started with Handoff
> tutorial.
> 
> 注意：本教程不涉及 Handoff，我们只会讨论当一个内容被浏览后如何创建可搜索的内容。如果你想熟悉了解 Handoff 的内容，请阅读[ Getting Started with Handoff 教程](http://www.raywenderlich.com/84174/ios-8-handoff-tutorial)。

###Core Spotlight
### Core Spotlight
The second, and perhaps most “conventional” aspect of app search is Core Spotlight, which is what the stock iOS apps like Mail and Notes use to index content. While it’s nice to allow users to search for previously accessed content, you might take it a step further by making a large set of content searchable in one go.

第二个同时也是App Search 中最“常用到的”概念就是 Core Spotlight，它是存储类 App 诸如邮件、备忘录用于索引内容的东西。它既可以允许用户搜索之前访问过的内容，也可以用它来一次性构建一个巨大的可搜索内容的集合。

You can think of Core Spotlight as a database for search information. It provides you with fine-grained control of what, when and how content is added to the search index. You can index all kinds of content, from files to videos to messages and beyond, as well as updating and removing search index entries.

你可以将Core Spotlight 看成是一个专门用于搜索的数据库。它提供了对添加搜索索引中的内容的细粒度的控制，例如这些内容是什么、什么时候添加的以及如何添加到搜索索引中的。你可以检索任何类型的内容，包括文件、视频、消息等等，还可以更新和移除搜索索引中的条目。

Core Spotlight is the best way to provide full search capabilities of your app’s private content.

Core Spotlight 为全面搜索 App 内部内容提供了一种最好的方式。

This tutorial will focus on what is required to get Spotlight results by using NSUserActivity described above. The full version of this tutorial in iOS 9 by Tutorials shows how to fully index your content by using Core Spotlight.

本教程关注于使用前面提及的 NSUserActivity 对象获取 Spotlight 搜索结果。本教程的完整版位于《iOS 9 Tutorials》中，其中介绍了如何通过Core Spotlight 全面检索你的内容。

###Web markup
### Web markup

The third aspect of app search is web markup, which is tailored towards apps that mirror their public content from a web site. A good example is Amazon, where you can search the millions of products it sells, or even raywenderlich.com. Using open standards for marking up web content, you can show it in Spotlight and Safari search results and even create deep links within your app.

App Search的第三个方面是 Web Markup，这个功能允许App 将它们的内容镜像到一个Web站点上。比较好的例子如 Amazon，你可以搜索它上面成千上万的在售产品，甚至是raywenderlich.com上的产品。在web 内容上使用了标准的标签进行标记，你可以将App 内容显示在 Spotlight 和 Safari的搜索结果中，甚至可以直接链接到你的App。

This tutorial will not cover web markup, but you can learn all about it in Chapter 3, “Your App On The Web” of iOS 9 by Tutorials.

本教程不涉及 Web Markup，你可以在《iOS 9 by Tutorials》第三章“Your App On The Web"中学习这部分内容。

##Getting started
##开始
You’ll work with a sample app named Colleagues that simulates a company address book. It provides an alternative to adding every coworker to your contacts, instead providing you with a directory of your colleagues. To keep things simple, it uses a local dataset, comprising of a folder of avatar images and a JSON file that contains employee information. In the real world, you would have a networking component that fetches contact data from a web-service. This is a tutorial, so JSON it is. Download and open the starter project, and before you do anything, build and run the app.

你将学习的示例程序叫做 Colleagues，它模拟一个公司通讯录。它可以将你的同时添加到你的联系人中，而不是直接给你一个同事的目录。为了简单起见，它使用的是本地数据库，由一个文件夹（存放头像图片）和一个 JSON文件（包含了所有公司职员信息）组成。在生产环境中，你应该使用一个网络组件从 Web 上抓取这些数据。作为教程，JSON 文件就足够了。[下载](http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/intro-app-search-starter.zip)并打开初始项目，不需要做任何事情，直接编译运行。

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/intro-app-search-app-preview.png"/>

You’ll immediately see a list of employees. It’s a small startup, so there’s only 25 staff. Select Brent Reid from the list to see all of his details. You’ll also see a list of employees who are in the same department. And that is the extent of the app’s features — it’s very simple!

你会看到一张职员列表。这是一个小型创业公司，只有25个职员的规模。选择 Brent Reid，可以查看这个职员的信息。你同时还可以看到 Brent Reid 所在的部门的其他人的列表。那是 App 的一个扩展功能——其实非常简单！

Search would make this app infinitely better. As it stands, you can’t even search while you’re in the app. You won’t add in-app search, but instead add the ability to search from outside the app with Spotlight!

搜索功能将让 App 增色不少。但是现在，你甚至无法在 App 搜索。你不用在 App 中增加搜索功能，相反，你可以用 Spotlight 从 App 外部增加一个搜索功能。

###Sample project
### 示例项目
Take a moment to familiarize yourself with the project’s codebase. There are two targets: Colleagues which is the app itself, and EmployeeKit which is a framework to facilitate interactions with the employee database.

花点时间来熟悉一下示例项目的代码。项目中存在两个 Target，一个是 Colleagues，即 App 自身；一个是 EmployeeKit，负责和职员数据库进行交互。

From the EmployeeKit group in Xcode, open Employee.swift. This is the model for an employee that has all the properties you might expect. Employee instances are initialized using a JSON model, which are stored under the Database group in a file named employees.json.

在 Xcode 中，展开 EmployeeKit 文件夹，打开 Employee.swift。这是职员信息的模型类，定义了一系列相关属性。Employee 对象使用一个 JSON 对象进行实例化，后者来自于 Database 文件夹下的 employees.json 文件。

Moving on, open EmployeeService.swift. At the top of the file is an extension declaration, and there is a method named destroyEmployeeIndexing() marked with TODO. You’ll fill out this method’s implementation later. This method will destroy any indexing that has occurred.

然后打开 EmployeeService.swift。在文件头部声明了一个扩展，扩展中有一个 destroyEmployeeIndexing()方法，这个方法用TODO标记进行注明。你将在稍后实现这个方法。这个方法负责销毁所有显示过的索引。

There’s more to the EmployeeKit target, but it’s not related to app searches, so there’s no need to go into it now. By all means though, feel free to poke around!

在 EmployeeKit 这个 Target 中有许多内容，但都和 App Search 毫无关联，因此我们就不多说了。当然，你可以花时间看一看。

Open AppDelegate.swift in the Colleagues group. Notice there is only one method in here: application(_:didFinishLaunchingWithOptions:).This implementation checks if Setting.searchIndexingPreference is set to .Disabled, if so it will destroy any search indexing that has occurred.

打开Colleagues 文件夹下的 AppDelegate.swift。注意只有一个方法在里边：
application(_:didFinishLaunchingWithOptions:)。这个方法判断Setting.searchIndexingPreference 是否设置为 .Disabled，如果是，则将所有存在的搜索索引删除。
 
You don’t need to do anything other than just be aware of this setting. You can change the setting in the iOS system Settings app under “Colleagues”.

除了知道有这么一个设置项存在外，你并不需要做任何事情。你可以通过 iOS 的设置程序中的 Colleagues 来修改这个设置。

That concludes your tour. The rest of the code is view controller logic that you’ll modify, but you don’t need to know all of it to work with app search.

参观到此结束。接下来你需要修改 View Controller 中的代码。

###Searching previously viewed records
###搜索曾经浏览过的记录
When implementing app search, NSUserActivity is the first thing to work with because:

实现App Search时，NSUserActivity 总是第一个要实现的，因为：

1. It’s dead simple. Creating an NSUserActivity instance is as easy as setting a few properties.

 它最简单。创建一个 NSUserActivity 实例就如同设置几个属性那么简单。

2. 
When you use NSUserActivity to flag user activities, iOS will rank that content so that search results prioritize frequently accessed content.

 当你用 NSUserActivity 表示用户活动时，iOS 会对内容进行排序，以便搜索结果对经常被访问的内容进行优先处理。

3. You’re one step closer to providing Handoff support.

 它和实现 Handoff 很像。

Time to prove how simple NSUserActivity can be to implement!

现在，让我们来看看实现 NSUserActivity 到底有多简单！

###Implement NSUserActivity
###实现 NSUserActivity
With the EmployeeKit group selected, go to File \ New \ File…. Choose the iOS \ Source \ Swift File template and click Next. Name your new file EmployeeSearch.swift and verify that the target is set to EmployeeKit.

选中 EmployeeKit 文件夹，依次选择 File \ New \ File...，然后选择 iOS\ Source \ Swift File 模板，再点击 Next。将文件命名为 EmployeeSearch.swift，并确保其 Target 为 EmployeeKit。

Within the new file, first import CoreSpotlight:

在这个文件中，首先导入 CoreSpotlight：

```
import CoreSpotlight
```

Next, still in EmployeeSearch.swift, add the following extension to the Employee struct:

然后定义一个 Employee 的扩展：

```
extension Employee {
  public static let domainIdentifier = "com.raywenderlich.colleagues.employee"
}
```

This reverse-DNS formatted string identifies the type of NSUserActivity created for employees. Next, add the following computed property below the domainIdentifier declaration:

反域名字符串将用于唯一标识 NSUserActivity 所属的一类活动类型。接着，在domainIdentifier 之后增加一个计算属性：

```
public var userActivityUserInfo: [NSObject: AnyObject] {
  return ["id": objectId]
}
```

This dictionary will serve as an attribute for your NSUserActivity to identify the activity. Now add another computed property named userActivity:

这个字典用于 NSUserAcitivity 唯一标识某个活动（Activity）。然后再添加一个计算属性，名为 userActivity：

```
public var userActivity: NSUserActivity {
  let activity = NSUserActivity(activityType: Employee.domainIdentifier)
  activity.title = name
  activity.userInfo = userActivityUserInfo
  activity.keywords = [email, department]
  return activity
}
```

This property will come into play later to conveniently obtain an NSUserActivity instance for an employee. It creates new NSUserActivity and sets a few properties:

这个属性用于很方便地根据一个 Employee 创建一个 NSUserActivity 实例。它创建了一个 NSUserActivity 对象，并用对以下属性进行了赋值：

- activityType: The type of activity that this represents. You’ll use this later to identify NSUserActivity instances that iOS provides to you. Apple suggests using reverse DNS formatted strings.

 activityType：活动所属的类型。你会在后面用它来识别 iOS 传递给你的NSUserActivity实例。苹果建议该值采用反域名命名规则。

- title: The name of the activity — this will also appear as the primary name in a search result.

 title：活动的名字——这将用于在搜索结果中作为主要名显示。

- userInfo: A dictionary of values for you to use however you wish. When the activity is passed to your app, such as when the user taps a search result in Spotlight, you’ll receive this dictionary. You’ll use it to store the unique employee ID, allowing you to display the correct record when the app starts.

 userInfo：一个字典，用于存放你想传递的任意数据。当你的App 收到一个活动时——比如用户从 Spotlight 点击了一个搜索结果，你就可以获取这个字典。你将在这个字典中存放同事的唯一 ID，这样 App 打开后就能显示正确的同事资料。

- keywords: A set of localized keywords that help the user find the record when searching.

 keywords：一个本地化的关键字列表，用于作为搜索关键字。

Next up, you are going to use this new userActivity property to make employee records searchable when the user views them. Since you added these definitions in the EmployeeKit framework, you’ll need to build the framework so that Xcode is aware they can be used from the Colleagues app.

然后，我们将使用刚才定义的 userActivity 属性去搜索同事记录。因为这些代码位于 EmployeeKit 框架中，我们需要编译框架才能在 Colleagues App 中使用它们。

Press Command-B to build the project.

按 Command+B，编译项目。

Open EmployeeViewController.swift and add the following to the bottom of viewDidLoad():

打开 EmployeeViewController.swift，在viewDidLoad()方法最后加入代码：

```
let activity = employee.userActivity
 
switch Setting.searchIndexingPreference {
case .Disabled:
  activity.eligibleForSearch = false
case .ViewedRecords:
  activity.eligibleForSearch = true
}
 
userActivity = activity
```

This retrieves userActivity — the property you just created in the Employee extension. Then it checks the app’s search setting.

上述代码读取 userActivity 属性——这个属性是我们刚才通过定义 Employee 扩展时添加的。然后检查 App 的搜索设置。

If search is disabled, you mark the activity as ineligible for search.
If the search setting is set to ViewedRecords, then you mark the activity as eligible for search.

如果搜索被禁用，将 activty 标记为不可用于搜索。如果该设置为 ViewedRecords，则将 activity 标记为能够用于搜索。

Finally, you set the view controller’s userActivity property to your employee’s activity.

最后，将 View Controller 的 userActivity 属性设置为 employee 的 userActivity。

> NOTE: The userActivity property on the view controller is inherited
> from UIResponder. It’s one of those things Apple added with iOS 8 to
> enable Handoff.
> 注意：View Controller 的 userActivity 属性继承自 UIResponder 。这个属性是苹果为了支持 Handoff 而增加到 iOS 8 中的。

The last step is to override updateUserActivityState(). This ensures that when a search result is selected you’ll have the information necessary.

最后还应该覆盖 updateUserActivityState() 方法。这样，当某个搜索结果被选择时，你才可以获得所需要的数据。

Add the following method after viewDidLoad():

在 viewDidLoad() 方法后增加这个方法：

```
override func updateUserActivityState(activity: NSUserActivity) {
  activity.addUserInfoEntriesFromDictionary(
    employee.userActivityUserInfo)
}
```

During the lifecycle of UIResponder, the system calls this method at various times and you’re responsible for keeping the activity up to date. In this case, you simply provide the employee.userActivityUserInfo dictionary that contains the employee’s objectId.

在 UIResponder 的生命周期中，系统会多次调用这个方法，你应该在这个方法中保持更新 activity。在我们的例子里，你只需要将包含有 employee的 objectId 的 userActivityUserInfo 字典传递给 activity。

Great! Now when you pull up an employee, that bit of history will be tracked and become searchable, provided the setting is turned on.

好了！现在，在搜索设置被开启的情况下，每当你浏览了一个同事，浏览历史将被记下并可用于搜索。

In the simulator or on your device, open the Settings app and scroll down to Colleagues. Change the Indexing setting to Viewed Records.

在模拟器或设备上，打开设置程序，找到 Colleagues。将 Indexing 设置改成 Viewed Records。

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/intro-app-search-app-screen-3.png"/>

Now, build and run the app and select Brent Reid.

现在，编译运行程序，然后选择 Brent Reid。

Okay, so it doesn’t look like anything spectacular happened, but behind the scenes, Brent’s activity is being added to the search index. Exit to the home screen (⇧⌘H) and bring up Spotlight by either swiping down from the middle of the screen or swiping all the way to the left of your home screen pages. Type brent reid into the search.

OK，看起来没有什么新奇的事情发生，但在你不知不觉中，Brent 的活动已经被加到搜索索引中了。回到 Home 屏幕（shift+command+H），通过下拉屏幕或者向右划动屏幕，打开 Spotlight。在搜索栏输入 brent reid 。

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/intro-app-search-app-screen-4.png"/>

And there’s Brent Reid! If you don’t see him, you may need to scroll past other results. And if you tap on it, it should move up the list next time you perform the same search.

"Brent Reid"显示出来了！如果你没看见，可能需要向下滚动列表。如果你点击这个Brend Reid，它将移动到列表上部，以便下次你可以搜索同一个关键字。

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/intro-app-search-whoa-meme.jpg"/>

Now, of course this is awesome, but the result is a little…bland.

虽然到现在为止结果还蛮不错，但这个搜索结果却是有点索然无味了。

Surely you can do more than give a name? Time to crack open the Core Spotlight framework and discover how.

除了显示一个名字外，我们还能干什么？现在就让我们彻底进入 Core Spotlight 的殿堂探索一番。

###Adding more information to search results
###在搜索结果中显示更多信息
NSUserActivity has a property named contentAttributeSet. It is of the type CSSearchableItemAttributeSet, which allows you to describe your content with as many attributes as necessary. Review the CSSearchableItemAttributeSet class reference to see the many ways to describe your content with these attributes.

NSUserActivity 有一个 contentAttributeSet 属性。这个属性的类型是 CSSearchableItemAttributeSet，它允许你用一系列属性来描述你的内容。查看 CSSearchableItemAttributeSet 类参考，你可以发现很多利用这些属性来描述内容的方法。

Below is the desired result, complete with each component’s property name called out:

下图是我们需要的搜索结果，每个部分都分别标出了所用的属性名：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/intro-app-search-properties-diagram-1.png"/>

You’ve already set title on NSUserActivity, and at the moment it’s all you see. The other three, thumbnailData, supportsPhoneCall and contentDescription are all properties of CSSearchableItemAttributeSet.

前面已经设置过 NSUserActivity 的 title 属性，这个属性正如你所看到的。其它3个属性，thumbnailData、supportsPhoneCall 和 contentDescription 全部都是通过 CSSearchableItemAttributeSet 来设置的。

Open EmployeeSearch.swift. At the top, import MobileCoreServices:

打开 EmployeeSearch.swift，在文件头部，导入 MobileCoreServices：

```
import MobileCoreServices
```

MobileCoreServices is required for a special identifier that you’ll use to create the CSSearchableItemAttributeSet instance. You’ve already imported CoreSpotlight, which is required for all of the APIs prefixed with CS.

MobileCoreServices 是必须的，因为在我们创建 CSSearchableItemAttributeSet 对象时需要用到其中定义的一个常量。你已经导入过 CoreSpotlight了，这个框架也是必须的，它的所有 API 都使用了 CS 作为前缀。

Still in EmployeeSearch.swift, add a new computed property named attributeSet to the Employee extension:

仍然在 EmployeeSearch.swift中，在 Employee 扩展中添加新的计算属性：

```
public var attributeSet: CSSearchableItemAttributeSet {
  let attributeSet = CSSearchableItemAttributeSet(
    itemContentType: kUTTypeContact as String)
  attributeSet.title = name
  attributeSet.contentDescription = "\(department), \(title)\n\(phone)"
  attributeSet.thumbnailData = UIImageJPEGRepresentation(
    loadPicture(), 0.9)
  attributeSet.supportsPhoneCall = true
 
  attributeSet.phoneNumbers = [phone]
  attributeSet.emailAddresses = [email]
  attributeSet.keywords = skills
 
  return attributeSet
}
```

When initializing CSSearchableItemAttributeSet, an itemContentType parameter is required. You then pass in kUTTypeContact from the MobileCoreServices framework. (Read about these types on Apple’s UTType Reference page.)

初始化 CSSearchableItemAttributeSet 时，需要提供一个 itemContentType 参数，我们传递了一个 kUTTypeContact 进去（该常量在 MobileCoreServices 框架中定义，关于该常量，请阅读苹果的 [UTType 参考](http://apple.co/1NilqiZ)）。

The attribute set contains the relevant search metadata for the current employee: title is the same as the title from NSUserActivity, contentDescription contains the employee’s department, title and phone number, and thumbnailData is the result of loadPicture() converted to NSData.

attributeSet 中包含了一些与当前 employee 搜索时用到的相关数据：title 来自于 NSUserActivity 的 title，contentDescription 包括了这个同事的部门、称谓和电话号码等信息，而 thumbnailData 则调用 loadPicture() 方法结果并转换为 NSData。

To get the call button to appear, you must set supportsPhoneCall to true and provide a set of phoneNumbers. Finally, you add the employee’s email addresses and set their various skills as keywords.

要显示”打电话“按钮，我们必须将 supportsPhoneCall 设置为true，并给 phoneNumbers 属性赋一个数组。最后，我们设置了 email 地址，并将同事的 skills （技能）作为 keyword 关键字。

Now that these details are included, Core Spotlight will index each and pull the results during a search. This means that your users can now search for coworkers by name, department, title, phone number email and even skills!

现在所有的数据都准备好了，Core Spotlight 在搜索时会检索这些数据并添加到搜索结果中。这样，用户就可以搜索同事的姓名、部门、称谓、电话号码、email甚至是技能。

Still in EmployeeSearch.swift, add the following line above the return in userActivity:

仍然是 EmployeeSearch.swift，在返回 userActivity 前面添加以下语句：
```
activity.contentAttributeSet = attributeSet
```
Here you tell the contentAttributeSet from NSUserActivity to use this information.

这句代码告诉 NSUserActivity 使用这些信息作为 contentAttributeSet属性的值。

Build and run. Open Brent Reid’s record so the index can do its thing. Now go to the home screen pull up Spotlight and search for “brent reid”. If your previous search is still there, you’ll need to clear it and search again.

编译运行。查看 Brent Reid 的个人信息以便索引生效。回到 Home 屏幕，拉出 Spotlight，搜索 brent reid。如果你先前的搜索结果仍然存在，你只需要清除并重新搜索。

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/intro-app-search-app-screen-5.png"/>

Voila! Aren’t you amazed with how little code it took to pull this off?

噢，你是不是很奇怪实现的代码太少了？

Great work! Now Spotlight can search for colleagues the user previously viewed. Unfortunately, there is one glaring omission…try opening the app from the search result. Nothing.

好了！现在 Spotlight 能够如我们所想的一样搜索同事了。不过，似乎我们还是遗漏了点什么...当你尝试通过搜索结果打开 App 时，什么也不会发生。

###Opening search results
###打开搜索结果
The ideal user experience is to launch the app directly to the relevant content without any fanfare. In fact — it’s a requirement — Apple uses the speed at which your app launches and displays useful information as one of the metrics to rank search results.

理想的用户体验是直接打开 App 并显示相关的内容。事实上——这个是一个要求——苹果会将能够启动并显示有用的信息的App的排在搜索结果的前列。

In the previous section, you laid the groundwork for this by providing both an activityType and a userInfo object for your NSUserActivity instances.

通过将一个 activityType 和一个 userInfo 对象赋给 NSUserActivity 对象，你已经在上一节中为后续的工作做了铺垫。

Open AppDelegate.swift and add this empty implementation of application(_:continueUserActivity:restorationHandler:) below application(_:didFinishLaunchingWithOptions:):

打开 AppDelegate.swift，在application(_:didFinishLaunchingWithOptions:) 方法下面，添加
一个application(_:continueUserActivity:restorationHandler:) 方法：

```
func application(application: UIApplication,
  continueUserActivity userActivity: NSUserActivity,
  restorationHandler: ([AnyObject]?) -> Void) -> Bool {
 
  return true
}
```

When a user selects a search result, this method is called — it’s the same method that’s called by Handoff to continue an activity from another device.

当用户选择了一个搜索结果时，这个方法会被调用——这个方法也会被Handoff 用来接收其他设备传来的活动。

Add the following logic above return true in application(_:continueUserActivity:restorationHandler:):

在这个方法返回 true 之前，加入以下语句：

```
guard userActivity.activityType == Employee.domainIdentifier,
  let objectId = userActivity.userInfo?["id"] as? String else {
    return false
}
```

This guard statement verifies the activityType is what you defined as an activity for Employees, and then it attempts to extract the id from userInfo. If either of these fail, then the method returns false, letting the system know that the activity was not handled.

guard 语句检查 activityType 是否是我们希望的类型（用于处理 Employee 的活动），然后从 userInfo 中获取 objectId。如果这两个条件中有一个不满足则返回 false，通知系统该活动不会被处理。

Next, below the guard statement, replace return true with the following:

接着，在 guard 语句后，将 return true 语句替换为：

```
if let nav = window?.rootViewController as? UINavigationController,
  listVC = nav.viewControllers.first as? EmployeeListViewController,
  employee = EmployeeService().employeeWithObjectId(objectId) {
    nav.popToRootViewControllerAnimated(false)
 
    let employeeViewController = listVC
      .storyboard?
      .instantiateViewControllerWithIdentifier("EmployeeView") as!
        EmployeeViewController
 
    employeeViewController.employee = employee
    nav.pushViewController(employeeViewController, animated: false)
    return true
}
 
return false
```

If the id is obtained, your objective is to display the EmployeeViewController for the matching Employee.

获得 id 之后，你的目标就是用EmployeeViewController 显示匹配的同事信息。

The code above may appear a bit confusing, but think about the app’s design. There are two view controllers, one is the list of employees and the other shows employee details. The above code pops the application’s navigation stack back to the list and then pushes to the specific employee’s details view.

上述代码稍微有点乱，但你可以想象一下 App 的设计。App 中有两个 View Controller，一个是同事的列表，另一个是则显示同事的详细信息。上述代码先将导航控制器的视图控制器堆栈弹回到列表界面，然后push 一个该同事细节窗口。

If for some reason the view cannot be presented, the method returns false.

如果因为某种原因视图无法呈现，方法会返回一个false。

Okay, time to build and run! Select Cary Iowa from the employees list, and then go to the home screen. Activate Spotlight and search for Brent Reid. When the search result appears, tap it. The app will open and you’ll see it fade delightfully from Cary to Brent. Excellent work!

OK，编译和运行！从同时列表中选择 Cary Iowa，然后回到 Home 屏。调出 Spotlight 搜索 Brent Reid。找到结果后，点击它。App 会打开，并且可以看到 Cary 的详情界面迅速地过渡到了 Bent 的详情界面。干得不错！

###Deleting items from the search index
###从搜索索引中删除条目
Back to the premise of your app. Imagine that an employee was fired for duct taping the boss to the wall after a particularly rough day. Obviously, you won’t contact that person anymore, so you need to remove him and anybody else that leaves the company from the Colleagues search index.

回到 App 的话题上来。想象一下，在某个狂风暴雨的一天，一个同事因为将老板用胶带绑在墙上而被解雇。显然，你是无论如何都不想和这个人有任何关系了，因此你必须将他和其他离开公司的人一起从 Colleagues 的搜索索引中删除。

For this sample app, you’ll simply delete the entire index when the app’s indexing setting is disabled.

由于只是一个示例App，你可以在 App 的索引设置关闭的前提下将整个索引删除。


Open EmployeeService.swift and add the following import statement at the top of the file.

打开EmployeeService.swift 在文件头部添加导入语句：

```
import CoreSpotlight
```

Next, find destroyEmployeeIndexing(). Replace the TODO with the following logic:

找到 destoryEmployeeIndexing()，将 TODO 注释替换为：

```
CSSearchableIndex
  .defaultSearchableIndex()
  .deleteAllSearchableItemsWithCompletionHandler { error in
  if let error = error {
    print("Error deleting searching employee items: \(error)")
  } else {
    print("Employees indexing deleted.")
  }
}
```

This single parameterless call destroys the entire indexed database for your app. Well played!

这个无参的方法将删除整个App的索引数据库。Good！

Now to test out the logic; perform the following test to see if index deletion works as intended:

现在可以来测试一下。通过下列步骤来测试是否索引一如我们希望的那样已被删除：

1. Build and run to install the app.

 编译运行程序。

2. Stop the process in Xcode.

 用 Xcode 终止程序。

3. In the simulator or on your device, go to Settings \ Colleagues and set Indexing to Viewed Records.

 在模拟器或者设备中，打开 设置 \ Colleagues，将 Indexing 设置为 Viewed Records。

4. Open the app again and select a few employees so that they are indexed.

 再次打开 App，选择一个新的同事，让索引生效。

5. Go back to the home screen and activate Spotlight.

 回到 Home 屏，调出 Spotlight。

6. Search for one of the employees you viewed and verify the entry appears.

 搜索浏览过的同事，等待索引项出现。

7. Go to Settings \ Colleagues and set Indexing to Disabled.

 回到 设置 \ Colleagues，将 Indexing 设置为关。

8. Quit the app.

 退出 App。

9. Reopen the app. This will purge the search index.

 重新打开 App。这将清除搜索索引。

10. Go to the home screen and activate Spotlight.

回到 Home 屏，调出 Spotlight。

11. Search for one of the employees you viewed and verify that no results appear for the Colleagues app.

 搜索浏览过的同事，你会发现没有和 Colleagues App 有关的搜索结果。

So deleting the entire search index was pretty easy, huh? But what if you want to single out a specific item? Good news — these two APIs give you more fine-tuned control over what is deleted:

呵呵，删除整个搜索索引实在太容易了。但如果你想只删除某个单独的记录呢？幸运的是——有两个 API 能够让你更精确地删除想删的记录：

- deleteSearchableItemsWithDomainIdentifiers(_:completionHandler:) lets you delete entire “groups” of indexes based on their domain identifiers.

 deleteSearchableItemsWithDomainIdentifiers(_:completionHandler:) 方法允许你删除整个 domain ID 相同的一组索引。

- deleteSearchableItemsWithIdentifiers(_:completionHandler:) allows you work with individual records using their unique identifiers.

 deleteSearchableItemsWithIdentifiers(_:completionHandler:) 方法允许你通过唯一ID 指定要删除哪条记录。

This means that globally unique identifiers (within an app group) are required if you’re indexing multiple types of records.

也就是说，如果你所索引的记录具有多种类型的话，全局 ID (在同一个 App 组中）必须唯一。

> Note: If you can’t guarantee uniqueness across types, like when
> replicating a database that uses auto-incrementing IDs, then a simple
> solution could be to prefix the record’s ID with its type. For
> example, if you had a contact object with an ID of 123 and an order
> object with an ID of 123, you could set their unique identifiers to
> contact.123 and order.123 respectively.
> 
> 注意：如果你不能保证跨类型ID 是唯一的，比如你的 ID 是通过数据库中的自增长类型获得的，则你可以采取一种简单办法，即在记录 ID 前面加上一个类型前缀。例如，如果你有一个联系人记录的 ID 为 123，一个订单记录的 ID 也是 123，则可以将它们的唯一 ID 设置为 contact.123 和 order.123。


If you’ve run into any issues you can download the completed project here.

如果你在运行过程中遇到任何问题，你可以从[这里](http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/intro-app-search-final.zip)下载到最终完成的项目。

##Where to go from here?
##接下来做什么？
This iOS 9 app search tutorial has covered iOS 9’s simple yet powerful approach to indexing the content inside your app using user activities. Searching isn’t limited to content though — you can also use it to index navigation points within an app.

这篇 iOS 9 App Search 教程介绍了在 iOS 9 中使用 User Activity 搜索 App 内部内容的简单但强大的方法。搜索的内容从来不会受到限制——你可以用这种方法搜索 App 中的导航点。

Consider a CRM app that has multiple sections such as Contacts, Orders and Tasks. By creating user activities whenever a user lands on one of these screens, you’d make it possible for them to search for Orders and be directly to that section of your app. How powerful would this be if your app has many levels of navigation?

想象一下，一个 CRM App，它拥有许多窗口，比如联系人、订单和任务。通过 User Activity，用户随时可以到达这些窗口，用户可以搜索订单，然后直接跳到 App 的某个订单界面。这个功能太有用了，尤其是你的 App 有很多层级的导航时。

There are many unique ways to bubble up content to your users. Think outside the box and remember to educate your users about this powerful function.

有许多独特的方法将内容推给你的用户。想突破沙盒的限制，就要教会用户使用这个强大的功能。

This tutorial was an abbreviated version of Chapter 2, “Introducing App Search” from iOS 9 by Tutorials. If you’d like to learn about indexing large data sets with Core Spotlight or web content with iOS 9 please check out the book!

这个教程是《iOS 9 by Tutorials》第2章的精简版。如果你想学习用 Core Spotlight 检索大数据集，或者学习 iOS 9 的 Web Content，请阅读这本书！
