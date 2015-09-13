Any iOS developer will tell you that iOS apps are built upon the [Model-View-Controller](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html) (MVC) design pattern. They might also tell you that some like to call it the “Massive View Controller” design pattern. Reason being, you’ll often find you have code that clearly doesn’t fit in the view or model, so it gets chucked into the controller, resulting in a bloated controller that is difficult to maintain because you have one giant class doing everything.

任何iOS开发者都会告诉你iOS应用程序是建立于[模型视图控制器](https://developer.apple.com/library/ios/documentation/general/conceptual/devpedia-cocoacore/MVC.html)（MVC）设计模式。他们也可能告诉你有些人叫它“Massive View Controller”设计模式。因为，你会经常发现你的代码有明显的不适合视图或者模型，所以它被抛到了控制器，导致臃肿的控制器很难维持,因为你有一个巨大的类做的一切。


In developing Brigade’s iOS app, we wanted to architect it in such a way that cleanly addressed a few key points:

在发展中旅的iOS应用程序,我们想建筑师以这样一种方式,干净利落地解决了几个关键点:

 * Easy to iterate on
 * Collaboration-friendly
 * Separated concerns
 * Easy to test

 * 易于迭代
 * 友好合作
 * 分离关注
 * 易于测试

We stumbled upon an architecture called VIPER, which seemed to fit the bill. VIPER was developed by the guys over at Mutual Mobile and they have a nice introductory blog post as well as a more involved blog post. We decided to stray from the Apple standard and give VIPER a shot.

我们偶然发现了一个建筑叫VIPER，这似乎刚好符合要求。VIPER是发展与朋友之间手机互动，他们有一个很好的介绍博客的职位以及更多参与的博客。我们决定离开苹果的标准给VIPER一枪。

#What is VIPER and how are we using it?

#什么是VIPER，我们要怎么使用它。

As stated by its creators, VIPER provides a guide to building an application architecture and can be adjusted to fit individual applications. This will be a discussion of what VIPER is and how we’ve adjusted it to fit our needs.


正如它的创造者所说, VIPER提供指导，构建应用程序的架构和可调整以适应个人应用。这将是一个VIPER是什么讨论我们如何调整以适应我们的需要。


To best understand VIPER, forget everything you know about MVC. VIPER is a new kind of beast (or reptile) and if your mindset is still in MVC land, you’ll have a difficult time accepting VIPER. Imagine that you are starting from square one and have no idea how iOS apps are structured. There is no spoon. There is no MVC.

为了最好的理解VIPER，忘记所有你知道的MVC。VIPER是一个新型的野兽（或者爬行动物），如果你的思维仍在MVC的地方，你会很难接受VIPER。想象你从起点开始，不知道iOS应用城西的结构。没有MVC。

VIPER aims to separate out application concerns into one of several roles:

VIPER打算分离应用程序几个角色之一的关注：

* View/User Interface
* Interactor
* Presenter/Event Handler
* Entity
* Router/Wireframe

* 视图/用户界面
* 交互器
* 提出者/事件处理程序
* 实体
* 路由器/线框

Note: Some of these have alternative names that you’ll see if you look at the other VIPER blog posts referenced above. I’ve added both names here as a heads up.

注:其中一些另类的名字,你会看到如果你看看上面提到的其他VIPER参考上文的博客文章。我已经添加了这两个名字在这里头。


In practice, we’ve found that we use a few additional roles, adding on a data-manager and service layer.

在实践中，我们发现，我们使用了一些额外的角色，增加了数据管理器和服务层。

Below is a diagram of a typical VIPER “stack” (more on this later) in our app and depicts how we think of VIPER. Each box represents a separate class and each line represents a reference between instances of the two respective classes.

下面是一个图一个典型的VIPER“栈”（稍后）在我们的应用程序和描述了我们如何看待VIPER。每一个方块代表一个单独的类，每一行代表一个参考实例之间的各自的类。


![](https://d262ilb51hltx0.cloudfront.net/max/720/1*XovYPHm53nQ5H8tSeBx_IQ.png)

Think of each of these classes as a worker in an assembly line. Each class only knows how to carry out a limited set of actions and relies on other classes to aid in completing the task at hand.

把每一个类当作一个工人在流水线中思考。每个类只知道如何进行一组有限的操作，并依赖于其他类来帮助完成手头的任务。

Let’s first go over the responsibilities of each class, then we’ll give an example of going from user interaction to displaying data to the user.

让我们先去把每个类的责任，然后我们会给出一个例子，从用户交互，向用户显示数据。

#View/User Interface

#视图/用户界面

![](https://d262ilb51hltx0.cloudfront.net/max/720/1*mmMgaqaPf2tisQJG8tlQng.png)


View responsibilities:

查看责任：


* Display information to the user
* Detect user interaction

* 向用户展示信息
* 检测用户交互


The view is told by the presenter what to display and it tells the presenter when an event needs to happen.

该视图是由提出者告诉由什么来显示，它告诉提出者，当事件需要发生。

#Displaying information to the user

#向用户展示信息

If the view should display an error to the user, the presenter might call a method on the view like:


如果视图应该向用户显示错误，提出者可能会调用视图的方法：


![](https://d262ilb51hltx0.cloudfront.net/max/800/1*wbZqa4CQmXpBjEvzv3L0kw.png)

It is then up to the view to display the error as it pleases, which could be in an alert view, a label, or any other way. The key thing here is that the presenter isn’t concerned with the details of how the error gets displayed. It just cares that it gets displayed.

然后，它可以显示错误，如它所喜悦的，它可以在一个警告视图，标签，或任何其他方式。这里的关键问题是，提出者不关心如何显示错误的细节。它只是关心它会显示。

###Detecting user interaction

###检测用户交互

If an event occurs such as the user tapping on a “Log in” button, the view might call a method on the presenter like:

如果一个事件发生如用户点击“登录”按钮，视图可能会调用一个类似的方法：

![](https://d262ilb51hltx0.cloudfront.net/max/1200/1*tEr3x6TgpbaK9BPwkgyJzg.png)

As we had before, the object making the method call just hands off the task to the next worker in line once it’s completed its job. As far as the view is concerned, it completed its job to detect the user interaction and it is now up to the presenter to deal with the event.

正如我们以前所做的那样，该对象的方法只是在有秩序下不干涉任务给下一个工人，一旦它完成了它的任务。就这一观点而言，它完成了它的工作来检测用户的交互，现在是向演示者处理事件。

#Presenter/Event handler

#演示/事件处理程序

![](https://d262ilb51hltx0.cloudfront.net/max/720/1*m0SBR_-COv6EskjU4wsgjg.png)

Presenter responsibilities:


提出者职责:

* Tell the view what to display
* Handle events


* 告诉视图该显示什么
* 处理事件

The presenter tells the view what to display and it handles events accordingly.


提出者会告诉视图该显示什么，并相应地处理相应的事件。

###Telling the view what to display

###告诉视图该显示什么


As we just saw, one of the presenter’s duties is to tell the view what to display. To accomplish this task, it also acts as a decorator, or a “presenter”, to format the data for the view so that it can be displayed in a meaningful way.


正如我们刚才看到的那样，一个演示者的职责就是告诉我们要展示什么。为了完成这一任务，它也作为一个设计师，还是一个“提出者”，格式化数据的视图，它可以以一种有意义的方式显示。


Looking back at our previous error example, the presenter likely received the error in the form of an error object. Instead of handing off the error directly to the view, it determined the appropriate message to describe the error and handed that off to the view.


回顾我们以前的错误例子，演示者可能会以一个错误对象的形式收到错误。而不是直接从错误的角度出发，它决定了恰当的信息来描述错误并将其传递给视图。

![](https://d262ilb51hltx0.cloudfront.net/max/1114/1*r046Z_kliF5RjrLPidtETA.png)

###Handling events

###处理事件

The presenter usually receives event type method calls from two sources: the view and the interactor.

演示者通常会收到事件类型的方法调用两个来源：视图和交互。

##View events

##查看事件

The presenter is notified of events by the view and part of its job is to handle those events accordingly. This usually means asking the interactor to retrieve some bit of information or carry out some task.

由视图和部分的工作是通知的事件的报告，相应地处理这些事件。这通常意味着要求关联检索一些信息或执行某些任务。

In our log in example, the presenter would have been notified by the view that a log in attempt event had occurred with a specific user name and password. The presenter would then ask the interactor to carry out the attempt by calling the appropriate method on it:

在我们的日志中，该演示者将被通知的视图，试图事件已发生与特定的用户名和密码。提出者会问的关联进行尝试通过它调用适当的方法：



![](https://d262ilb51hltx0.cloudfront.net/max/1200/1*Gl3ZFt2yUGJ_GCbEy_8pBA.png)

You can probably guess what the presenter does now; Nothing. It has carried out its duty of handling the event and it is now up to the interactor to carry on the task.

你可能猜到现在提出者在做什么；什么都没有，它的责任时事件的处理并且，它现在到了关联进行任务。

###Interactor events

###关联事件

The presenter can also receive events from the interactor. This would happen when the interactor finishes carrying out some task and the user should know about the result.

提出者也可以从关联接受事件。这将发生在交互执行完成任务和用户应该知道结果。

For example, if a log in attempt failed, the interactor might tell the presenter:

例如在尝试登陆失败，交际者会告诉提出者：

The presenter would then take this error, convert it into a string that is meaningful for the user, then tell the view to display an error using that string.

提出者会把这个错误，把它转换成一个有意义的字符串，然后告诉使用该字符串显示一个错误。

![](https://d262ilb51hltx0.cloudfront.net/max/1200/1*BS9djdcmnNCQAxXkMwGuGw.png)

#Interactor

#交互器

![](https://d262ilb51hltx0.cloudfront.net/max/720/1*wlArOkhJfXqhlfDFuoJveg.png)

Interactor responsibilities:

交互器的责任：

* Perform business logic
* 执行业务逻辑

The interactor is what performs the business logic of the app that revolves around data.

关联是什么执行的应用程序，都是围绕着数据的业务逻辑。

###Performing business logic

###执行业务逻辑


The interactor is the one that knows how to carry out the events that the view notifies the presenter of.

关联是一个知道如何开展，查看通知提出者的事件。

For example, suppose that in your app some event occurs that requires two synchronous network requests to your backend API. That is, request B cannot be executed until request A finishes because request B requires information that is in the response of request A. This would start out with a call to the interactor from the presenter:

例如，假设在应用程序中，需要向后端应用程序的同步网络请求。那是，请求B不能执行请求，请求B直到完成需要的信息，是在响应请求这会开始从提出者到关联的电话：

One thing we must bring up here is that the interactor itself does not deal with network requests directly. In fact, it doesn’t even know that network requests are occurring. All it knows is that it can get data in the form of entities from the data manager (we’ll elaborate on this concept soon). With that in mind, the interactor’s corresponding method to the call above would look something like this:

有一件事我们必须提出的是这里的关联本身并不直接处理网络请求。事实上，它甚至不知道网络请求正在发生。它所知道的是，它可以从数据管理器中的实体的形式（我们将详细说明这个概念很快）。记住，交际者的相应的方法调用上会是这个样子：

Here we see that the interactor calls the necessary methods on the data manager and then calls back to the presenter with the result. The key thing here is that the interactor knows that `performMyTask` requires both `fetchFooWithCallback:` and `fetchBarWithFoo:callback:` to be called AND that `fetchBarWithFoo:callback:` must be called within the callback block of `fetchFooWithCallback`. In this way, the interactor handles the “business logic” of the app.

在这里，我们看到，关联调用的数据管理的必要方法，然后调用返回结果的提出者。这里的关键是交际者知道` performmytask `需要` fetchfoowithcallback：`和` fetchbarwithfoo：回调：`被称为，` fetchbarwithfoo：回调：`必须在回调块` fetchfoowithcallback `。这样，关联处理的“业务逻辑”的应用。

![](https://d262ilb51hltx0.cloudfront.net/max/865/1*NrZgQI7NYhPoQZuy6icSQg.png)

#Data Manager

#数据管理器

![](https://d262ilb51hltx0.cloudfront.net/max/720/1*enr6llNjSGPY6taPP3IWSw.png)


Data manager responsibilities:

数据管理职责：

* Retrieve data
* 检索数据
* Store data (optional)
* 存储数据（可选）


The data manager is the one that knows where to retrieve data from and if it should be persisted or not.

数据管理器是一个知道在哪里检索数据的人，如果它应该坚持或不。

#Retrieving data

#检索数据

As we saw in the interactor example, we should be able to query the data manager for data and just get the correct entities back. We don’t care where the data comes from because the data manager handles that.

我们看到在关联的例子，我们应该能够查询数据的数据管理，就得到正确的实体回来。我们不关心数据来自何处，因为数据管理器处理。

The data manager knows exactly where to retrieve specific data or carry out certain requests. For example, the interactor might request a user object from the data manager:

数据管理器清楚地知道在哪里检索特定的数据或执行某些请求。例如，关联可以从数据管理器请求用户对象：

If this is an app that is backed by a backend server, the data manager would know that it has to eventually make a network request to retrieve user data. If this is an app that is not backed by a backend server, the data manager might retrieve the user data from a local persistent store instead. For network requests, we like to have the data manager query Service objects:

如果这是一个应用程序的后端服务器的支持，数据管理器会知道它必须最终使网络请求检索用户数据。如果这是一个应用程序没有后台服务器的支持，数据管理器可以从本地持久存储中检索用户数据。对于网络请求，我们喜欢有数据管理查询服务对象：

We’ll get into Service objects soon, but the key here is that the data manager knows what service to use to retrieve specific information. Once that information is received, it relays the information back to the interactor.

我们很快就要进入服务对象，但这里的关键是，数据管理器知道要使用什么服务来检索特定的信息。一旦接收到信息，它的信息反馈给团员的继电器。

![](https://d262ilb51hltx0.cloudfront.net/max/847/1*F1qTRN49rD_n0QhC2826ow.png)

###Storing data

###存储数据

Persisting data is also concern of the data manager. It knows when it should store data such as data fetched from the server that it might store for caching purposes. The main thing here is that no other class knows that any data is persisted because the data manager abstracts that away. A simple example of this is a modification of the code above:

持续的数据也是数据管理者关注的问题。它知道什么时候它应该存储数据，如数据从服务器端，它可以存储缓存的目的。这里的主要原因是，没有其他类知道任何数据是持久的，因为数据管理器摘要。一个简单的例子，这是一个修改上面的代码：

Let’s break this down:

让我们打破这个：

* The data manager first checks if it has a cached version of the user corresponding to the given `userID`.	
* 数据管理器首先检查是否已缓存版本的用户给出了相应的` userid `。								
* If the user is found, the interactor is notified and the method returns.	
* 如果用户发现，关联的通知和方法返回。					
* If the user is not found, the data manager must retrieve the user from the backend server and it uses a Service to do so.
* 如果未找到用户，则数据管理器必须从后端服务器检索用户，并使用一个服务来做到这一。
* Once the user is retrieved from the server, the user is first persistent, then the interactor is notified.
* 一旦用户从服务器检索，用户首先是持久的，那么关联的通知。

Persisting data is a huge topic all on its own and can be implemented many ways. This example just serves as a simple instructional schema, but the idea of abstracting all of this behind a data manager is one of the points of VIPER.
坚持数据是一个巨大的话题，所有的，可以实现许多方式。这个例子只是作为一个简单的教学模式，但这个想法将在这一切的背后，一个数据管理是一种VIPER的点。


#Service

#服务

![](https://d262ilb51hltx0.cloudfront.net/max/720/1*psxyw4Su-7uChjj4vqIINA.png)


Service responsibilities:

服务职责:

* Execute network requests to the server for specific entities
* 向特定实体执行网络请求


Service objects are not necessary in VIPER, but one that we have found very useful.

服务对象的VIPER是没有必要的，但是我们已经发现非常有用。

Executing network requests to the server for specific entities

向特定实体执行网络请求

We’ve found Services to be a nice way to keep code concerned with network requests DRY. The idea is that a single Service only deals with one entity type and knows what network requests need to be made to perform various modifications to that entity type.

我们已经找到服务是一个很好的方式来保持代码与网络请求干燥。其思想是一个单一的服务只处理一个实体类型，并且知道需要对该实体类型进行各种修改的网络请求。

For example, you may have a Service for a user entity. The header file for this class might look like the following:

例如，您可能有一个用户实体的服务。这个类的头文件可能看起来如下：

This service knows how to create a user, log a user in, and fetch a user object. Something like fetching a user object for a given ID is something that is likely to be used in multiple places in an app, which is where service objects come in handy in keeping things DRY.

此服务知道如何创建用户、登录用户、获取用户对象。对于一个给定的身份证的用户对象的一些类似的东西，是一种很可能被用于在多个地方的应用程序，这是服务对象是方便的，在保持事情干。

#Entity

#实体

![](https://d262ilb51hltx0.cloudfront.net/max/720/1*6UzkrgDdsaT17BOuciIfBQ.png)


Entity responsibilities:

实体职责:

* Represent data
* 代表数据


Entities are pretty straight forward and what you would expect. They embody some type of data and act as the “payload” that gets passed around between the other classes. For example, the data manager returns an entity to the interactor, which returns an entity to the presenter, which then uses that entity to tell the view what it should display.

实体是非常直接的向前和你所期望的。它们体现了某种类型的数据，并作为一种“负载”，通过周围的其他类。例如，数据管理器返回一个实体的交互，它返回一个实体的提出者，然后使用该实体告诉视图显示什么信息。

#Router/Wireframe

#路由器/线框

![](https://d262ilb51hltx0.cloudfront.net/max/720/1*oULn94oeeGrlZfAf_HJ27A.png)

Wireframe responsibilities:

线框职责：

* Initializes all the other classes
* 初始化所有其他类
* Handles routing to other views in the app
* 处理路由到其他视图的应用程序


The router/wireframe is what glues all of the other VIPER components to one another and handles navigating from one view to another in the app.

路由器/线框是什么胶水，其他所有的VIPER组件彼此并处理从一个视图导航到另一个应用程序。

###Initializing all the other classes

###初始化所有其他课程

You might be wondering how all of these VIPER classes get instantiated and wired up to talk to one another. That’s where the router/wireframe comes in.

你可能会想知道所有这些VIPER类被实例化和有线互相交谈。这是路由器/线框的地方来。

In VIPER, every “stack” consists of a view, presenter, interactor, data manager and services (for us anyways), and entities. This “stack” is what VIPER refers to as a module. A VIPER module should correspond to a single use case. A use case is some function your app should perform for the user.

在VIPER，每一“堆”由一个观点，提出者，交互，数据管理和服务（我们无论如何），和实体。这一“堆”是什么VIPER是指作为一个模块。VIPER模块对应一个用例。一个用例是你的应用程序应该为用户执行的功能。

For example, one use case that is common for many apps is allowing the user to log in with an account. For this, we would have a VIPER module specifically for the “Log in” screen of the app. This module would have:

例如，一个用于许多应用程序共同使用的案例是允许用户登录帐户。为此，我们将有一个VIPER模块专门为“登录”的应用程序屏幕。这个模块将有：

* A view that displays the “Log in” screen.
* 显示“登录”屏幕。
* A presenter that handles events like the user requesting to log in with a specific username and password.
* 一个演示者，处理事件，如用户要求登录在一个特定的用户名和密码。
* An interactor that knows what methods to call on the data manager for such events like attempting to log the user in.
* 一个团员知道叫上喜欢尝试登录用户在此类事件中的数据管理方法。
* A data manager that knows what services to use to retrieve or send information to the server.
* 一个知道用以检索或发送信息到服务器的数据管理器。
* Services that know what HTTP URLs to make requests to.
* 服务知道HTTP URL请求。
* Entities that your server responses are converted into so that the information is useful in your app.
* 实体，您的服务器响应被转换成这样的信息是有用的，在您的应用程序。


As you’ll notice, the view, presenter, interactor, and data manager are very specific to this module. That is, they only know how to deal with things related to logging in. The services and entities on the other hand are very general things that can be used throughout many different modules. In this case, the service might be one that knows how to hit all the endpoints related to Users on your server. This might include logging in, signing up, or just retrieving general User data. Then entity you might use here would be a User entity, which just represents a User object. It’s easy to see that this entity can be used in many places in your app.

你会注意到，看来，提出者，关联，和数据管理是这个模块非常具体的。也就是说，他们只知道如何处理与日志记录有关的事情。另一方面的服务和实体是非常一般的东西，可以用在许多不同的模块。在这种情况下，该服务可能是一个知道如何将所有端点与服务器上的用户关联的服务。这可能包括登录、注册，或只是检索一般用户数据。你可能在这里使用的是一个用户实体，它只代表用户对象。很容易看到，这个实体可以在许多地方使用的应用程序。


Now that we’ve explained what a VIPER “stack” or module is, we can explain part of the responsibility of the wireframe. The wireframe is what instantiates instances of each of these VIPER components and wires them up to talk to each other. That is, it gives the view and presenter references to one another, the presenter and interactor references to one another, and so on. Instantiating a wireframe is equivalent to instantiating an entire VIPER module.

现在我们已经解释了什么是VIPER“栈”或模块，我们可以解释的线框的部分责任。线框图是什么实例化实例这些VIPER组件和连接他们互相交谈。那是，它给出了视图和另一个提出者参考，提出者和彼此关联的引用，等等。实例化一个线框相当于实例化整个VIPER模块。

###Handling routing to other views in the app

###处理路由到其他视图的应用程序

he wireframe’s other alias, router, is what makes up the R in VIPER. The wireframe knows how to navigate to and present other modules when requested. This means the wireframe will also have references to other wireframes.

他线框的别名，路由器，是什么使得在VIPER的R。线框图知道如何浏览和其他模块时要求。这意味着线框也会有其他的框架参考。

For example, suppose you have an initial screen in your app that is your typical home screen for an app that requires user accounts. This screen would have a button for “Sign up” and a button for “Log in”. This screen is a module; let’s call it the registration prompt (naming is hard). The use case here is that the user should be able to see their options before being logged in to your app. When the user presses the “Log in” button, the typical flow is:

例如，假设你有一个初始屏幕在你的应用程序，这是你的典型的家庭屏幕的应用程序，需要用户帐户。这个屏幕上有一个按钮，“注册”和“登录”按钮。这个屏幕是一个模块，让我们称之为注册提示（命名是硬的）。这里使用的情况是，用户应该能够看到他们的选项之前登录到您的应用程序。当用户按“登录”按钮时，典型的流程是：

* The view will notify the presenter that the user has requested the log in prompt.
* 视图将通知用户已请求登录的提示。
* The presenter will realize this requires a separate module so it will notify the wireframe.
* 主持人会意识到这需要一个单独的模块，它将通知框。

Now, the registration wireframe will instantiate the log in wireframe, thereby instantiating an entire VIPER module, and present the log in view over the registration view (maybe as a modal).

现在，注册框线框将实例化的日志，从而实例化整个VIPER模块，和现在的日志视图在登记的观点（也许作为一个模态）。


#Benefits of VIPER

#VIPER的利益

After using VIPER, we’ve found it to be very beneficial in many ways. Let’s get back to the list of things we set out to accomplish when architecting our app to see if VIPER addresses them.

使用后的VIPER，我们发现它在许多方面是非常有益的。让我们回到我们出发去完成构建我们的应用程序时看看它们的地址列表中的VIPER。

* Easy to iterate on
* 易于迭代
* Collaboration friendly
* 友好合作
* Separated out concerns
* 分离关注
* Spec-ability
* 规格能力

###Easy to iterate on

###易于迭代

One thing that is very helpful in adding features to an app is knowing where new code should live in relation to the old code. VIPER does a good job of this with each of its components having a clear set of responsibilities that doesn’t bleed into one another, which makes it easy to decide where to put new code.

一件事是非常有用的，在添加功能的应用程序是知道新的代码应该生活在旧的代码。VIPER与每个组件有一个明确的责任，不排成一个很好的工作，这使得它很容易决定，把新的代码。

I often find that when I’m adding new features to a module, it almost feels like an everyday routine because I already know where each piece of code should be placed and it’s just a matter of getting it done.

我经常发现当我在一个模块中添加新功能时，它几乎感觉像是一个日常的例程，因为我已经知道每个代码应该放在哪里了，这只是一个完成的问题。

That being said, we do run into situations in VIPER where we’re unsure of where to place some piece of code and we have to make a judgement call. For example, if the user can select multiple items in a list before performing some action on them all, should we keep this state in the view, presenter, or another other class in the VIPER stack? Once you figure out what makes most sense to you, you can bypass these problems in the future easily because you’ve solved them already. Then everything becomes routine again.

也就是说，我们遇到的情况，我们确定VIPER在放置一块代码，我们必须作出判断。例如，如果用户可以执行所有行动前选择列表中的多个项目，我们应该在视图，保持这个状态的主持人，或其他类的VIPER堆栈？一旦你弄清楚什么对你最有意义，你可以绕过这些问题，在未来很容易，因为你已经解决了他们。然后一切又变得例行。

###Collaboration-friendly

###友好合作

VIPER makes working in a team surprisingly easy. Because use cases are separated into different modules, you don’t step on other people’s toes since all the code for one feature is usually compartmentalized into its own module.

VIPER在团队非常容易的工作。由于用例被分成不同的模块，你不踩别人的脚趾，因为所有的代码的一个特点是通常划分为自己的模块。

In practice each of the VIPER components interact with one another via an interface. In objective-C this is just a protocol. The nice thing about this is that you can define the interface between two classes and then separate people can work on those classes individually. We’ve had much success with this by defining the view-presenter interface ahead of time and having one person work on the view doing purely UI work and another person work on the rest of the VIPER stack doing purely “backend” work.

在实践中，每一个VIPER组件之间的交互通过接口。在Objective-C中这只是一个协议。很好的一点是，你可以定义一个类的接口，然后单独的人可以单独对这些类进行单独的工作。我们已经取得了很大成功，这被定义的视图演示界面提前对视图做纯粹的UI的工作一个人工作，一个人工作，其余的VIPER栈做纯粹的“后台”工作。


###Separated concerns

###分离关注

With each VIPER module abiding by the Single Responsibility Principle, VIPER naturally separates out concerns among classes. This is what makes the former two points (and the last point) work out so nicely.

每个VIPER模块遵循单一责任原则，VIPER自然分离出来的关注类。这是什么使前两点（和最后一个点）工作了这么好。

###Easy to test

###易于测试

By having separate components that follow the Single Responsibility Principle, this also makes things easy to spec. It gives you the ability to spec specific functionality while stubbing out other dependencies. For example, if you want to test your interactor logic, all you have to do is stub out the presenter and the data manager to eliminate the dependencies that can make writing specs complicated. Then your specs will only be testing the interactor and you can write separate specs for the presenter and data manager.

通过分离元件，遵循单一责任原则，这也使事情容易说明它给你而掐灭其他依赖规格特异功能的能力。例如，如果你想测试你的交互逻辑，你所要做的就是找出提出者和数据管理器来消除可以使写作规格复杂的依赖关系。那么你的规格只会测试的关联，你可以写为提出者和数据管理单独的规格。

#Conclusion

#结论

Overall we’ve found making the switch to VIPER to be very helpful and beneficial to us for the reasons mentioned above. There are of course many obstacles you will encounter in using VIPER that will require you to mold VIPER to your needs, but that is true of any architecture you choose to go with.

整体上我们发现使切换VIPER为上述原因是非常有用的，对我们有益。当然有许多的障碍，你会用VIPER需要模具VIPER需要你的遭遇，但那是真的任何建筑的你选择去。

I would definitely recommend others to try out VIPER and also to take a look at the other blog posts on it. Hopefully this post has been helpful and gotten some of you excited to try out VIPER.

我肯定会尝试VIPER推荐别人的同时也要在其他博客上看它。希望这个帖子一直帮助和得到一些你兴奋地尝试VIPER。

Additional Reading:

附加读数：

* [Introduction to VIPER](http://mutualmobile.github.io/blog/2013/12/04/viper-introduction/)
* [Architecting iOS Apps with VIPER](https://www.objc.io/issues/13-architecture/viper/)






