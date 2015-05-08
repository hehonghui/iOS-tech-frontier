`什么是委托代理？-Swift开发者指南`
---

>* 原文链接 : [What is Delegation? – A Swift Developer’s Guide](http://www.andrewcbancroft.com/2015/03/26/what-is-delegation-a-swift-developers-guide/)
* 原文作者 : [andrew](http://www.andrewcbancroft.com/)
* [译文出自 :  开发技术前线 www.devtf.cn](www.devtf.cn)
* 译者 : [sdq](https://github.com/sdq/) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  未完成 / 校对中 / 完成 

What is the delegation design pattern?

To read that “delegation is a design pattern that [insert explanation here]” never really clicked with me. Many who are new to programming have not dealt extensively with “design patterns”, so it doesn’t always suffice to define delegation in those terms.

## 什么叫代理模式我非常不喜欢看到“代理是一种XXX的设计模式”这种类型的说法，因为对大多数新手程序员来说，他们并没有大量接触过“设计模式”，用这样的定义去解释是不合适的。
I suspect that since Swift has lowered the barrier to entry for iOS development, many who are new to the platform are also new to coding in general, so here’s my attempt to explain what clicked for me regarding what delegation is:

由于Swift降低了iOS开发的门槛，很多初次尝试这个平台的开发者同时也可能是程序开发的新手。在这里我尝试用我觉得更便于理解的方式来解释什么叫代理：

###Design pattern

First, take the phrase “design pattern”.

###设计模式

首先，让我们来说一下“设计模式”。

Design implies architecture. It connotes a strategy for organizing something. Design conveys the method by which components will work together to accomplish an end.

**设计**指的是架构设计，它让人联想到一种管理事物的策略，它是一种通过各个不同单元的合作并最终有效完成任务的方法。

Pattern implies that there is some repeatable process that has honed in around a common thread… a common practice… a predictable method for doing something. “Pattern” gives the impression that such a practice has converged over time into something that is now well-known, well-understood, and commonly used. I imagine a sort of “survival of the fittest” approach to process and practice. Things that didn’t converge or that were weaker in the real world fell away and the strongest survived into this thing we call “pattern”.

**模式**指的是对于一些主要的，司空见惯的，可以预测的行为方式经过提炼之后得到的具有可重复性使用的行事模型。“模式”往往给人一种印象，即这种方法在长久时间的检验下已经具有了普适性，易懂性和常用性的特征。如果用“适者生存”的法则来比喻，那些在真实世界不能承受时间锤炼的方式终会消逝，而那些坚强地留存下来的方式就形成了我们所谓的“模式”。

A design pattern in software terms, then, is a method for architecting, strategizing about, and organizing an application’s code in such a way that has been found to be commonplace, repeatable, and practically sound over time.

在软件开发领域，**设计模式**是一套长期检验后公认的易见、重用性高、能产生实际效能的经验总结，常用于软件代码的架构设计，策略规划和结构组织。

###Delegation

Now take the word delegation. Three things come to my mind:

* The verb, “to delegate”, meaning “to give control”
* The noun, “a delegate”, meaning “a person acting for another”
* The made-up noun, “a delegator“, or more properly, a principal, meaning “a person who delegates to another”

###代理

现在让我们来看看代理(Delegate)，我想到了三个解释：

* 动词，“to delegate”,意思是“交出控制权”。
* 名词，“a delegate”,意思是“代理人”。
* 改造的名词，“a delegator“,意思是“委托人”。

In the real world, the word delegation encapsulates relationship and responsibility. A delegator/principal (noun) would delegate (verb) control or responsibility to another person called a delegate.

在现实世界中，代理这个词同事囊括了关系与责任。委托人(delegator名词)将控制权与责任委托(delegate动词)给另一个人，也就是代理人(delegate)。

How could we map this to software? Well, it actually falls in line quite nicely.

那么我们怎么将其映射到软件中去呢？事实上，它能很好地运用到代码中。

Rather than people, we’re dealing with instances of classes (or other data structures like structs, but I’m just going to keep it simple and say “class” to encapsulate the idea). For delegation to occur in software, you’d have a situation where one class (a delegator/principal class) would delegate control or responsibility, here meaning behavioral logic, to another class called a delegate.

相对于人，在软件中我们处理的对象是类的实例(或者可以是其它数据结构，比如structs，但我想让它保持简单，用“类”来概括这个想法)。代理在软件中是这样发生的，你会遇见这样的情况，一个类（委托者类）会将控制权与责任（这里值得是行为逻辑）委托给另一个类，这样的行为被称作代理。

## How is delegation used?

So yes… delegation is a design pattern. It’s a design pattern that exists on other platforms, but it is one that has been heavily adopted by Apple and is ubiquitous throughout the iOS APIs. It’s a design pattern that shifts responsibility from one class to another, thereby creating a separation of responsibilities and concerns. But what kinds of responsibilities and concerns? How is delegation used in practice?

## 代理是如何被使用的

没错，代理是一种设计模式。代理虽然是存在于其它各个平台的设计模式，但只有在Apple代理被如此深度地使用着，在iOS的API中代理几乎无所不在。这是一种将一个类的责任转移给另一个类的设计模式，因此它创造了各个类之间责任与关注点的分离。但是究竟是哪种责任与关注点？代理在实际中如何使用的呢？

### Communication

In practice, delegation is most often used as a way for one class to communicate to another class. Observing some of the actions that delegates perform from Apple’s own APIs give us clues about this. Take the following samples from UITableViewDelegate as an example:

tableView(_:willSelectRowAtIndexPath:)
tableView(_:didSelectRowAtIndexPath:)
tableView(_:didHighlightRowAtIndexPath:)

### 通信

在实际应用中，代理通常用来作为一个类与另一个类之间通信的方式。观察Apple官方API对代理的处理方式会观察到线索，比如以下UITableViewDelegate的例子：

* tableView(_:willSelectRowAtIndexPath:)
* tableView(_:didSelectRowAtIndexPath:)
* tableView(_:didHighlightRowAtIndexPath:)

Or how about a sampling from UIScrollViewDelegate:

scrollViewDidScroll(_:)
scrollViewWillBeginDragging(_:)
scrollViewWillEndDragging(_:withVelocity:targetContentOffset:)
scrollViewDidEndDragging(_:willDecelerate:)

或者是UIScrollViewDelegate的例子：

* scrollViewDidScroll(_:)
* scrollViewWillBeginDragging(_:)
* scrollViewWillEndDragging(_:withVelocity:targetContentOffset:)
* scrollViewDidEndDragging(_:willDecelerate:)

My observation of Apple’s APIs indicates to me that one of the intended uses for delegation is to allow one instance to communicate back to some other instance that something will/did happen. The table view or scroll view delegates the opportunity to perform some action in response to some lifecycle event to another class, namely, its delegate.

根据我对Apple官方API的观察，代理倾向于使用在某些事件即将发生/已发生时一个实例与另一个实例之间的通信。tableview与scrollview将实现生命周期中某些响应行为的机会委托给其它类，称之为代理。

It’s also worth noting the scope of the communication that delegation is intended to be used for. Whereas NSNotificationCenter fits the need for one instance to broadcast a message intended for more than one listening instance, delegation fits the scenario where an instance only needs to send a message to a single listener (the delegate).

同样值得注意的是代理倾向于使用的通信范围，代理适用于一个实例仅需要发消息给一个单一的接收者（代理）这样的场景，然而NSNotificationCenter则适合于从一个实例对象将信息进行广播。

### Customization

Another common usage for delegation is to provide a delegate instance the opportunity to customize certain aspects of the delegating instance’s internal state. Once again, clues from a few of Apple’s APIs shed some light on this usage scenario. Let’s look at UITableViewDelegate first:

tableView(_:heightForRowAtIndexPath:)
tableView(_:editActionsForRowAtIndexPath:)
tableView(_:indentationLevelForRowAtIndexPath:)
tableView(_:shouldHighlightRowAtIndexPath:)

### 自定义

代理模式的另一种常见用法，是授权于代理实例，使其可以自定义实例内部的某些特定状态。再提一下，从Apple官方的一些API中可以看到一些这类使用场景，让我们首先看一下UITableViewDelegate：

* tableView(_:heightForRowAtIndexPath:)
* tableView(_:editActionsForRowAtIndexPath:)
* tableView(_:indentationLevelForRowAtIndexPath:)
* tableView(_:shouldHighlightRowAtIndexPath:)

These are all customization-points that a UITableView allows its delegate to have a say in. Some of the methods are so important that the table view can’t display itself unless it gets this information from its delegate. The point here is that the table view is shifting responsibility for the implementation of that logic off to its delegate, allowing for greater controlled flexibility and customization.

这些自定义的点都是UITableView允许其代理自行处理的。有一些方法非常的重要，在没有从其代理收到信息前，tableview可能无法显示。tableview将这些点的实现方法的责任转移给了它的代理，这样做可以让控制更为灵活，便于自定义。

## Wrapping up

With delegation being such a heavily utilized strategy for organizing an iOS application’s logic, understanding what it is becomes key concern. In this article we unpacked the terms “design pattern” and “delegation” to get a grasp of why those words were chosen to describe the strategy. Finally, we looked at how the delegation pattern is used in practice, observing that two common use-cases for delegation: class-to-class communication and customization.

## 总结

代理在iOS中十分重要，理解它到底是什么成为关键点。这篇文章中，我们分解了名词“设计模式”和“代理”来解释为什么这些词被用来描述这样的策略。最后，我们看了代理模式是如何在实际中应用的，观察了两个代理的常用方式：类与类之间的通信以及自定义。

