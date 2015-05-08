`什么是委托代理？-Swift开发者指南`
---

>* 原文链接 : [What is Delegation? – A Swift Developer’s Guide](http://www.andrewcbancroft.com/2015/03/26/what-is-delegation-a-swift-developers-guide/)
* 原文作者 : [andrew](http://www.andrewcbancroft.com/)
* [译文出自 :  开发技术前线 www.devtf.cn](www.devtf.cn)
* 译者 : [sdq](https://github.com/sdq/) 
* 校对者:  
* 状态 :  完成 


## 什么叫代理模式我非常不喜欢看到“代理是一种XXX的设计模式”这种类型的说法，因为对大多数新手程序员来说，他们并没有大量接触过“设计模式”，用这样的定义去解释是不合适的。

由于Swift降低了iOS开发的门槛，很多初次尝试这个平台的开发者同时也可能是程序开发的新手。在这里我尝试用我觉得更便于理解的方式来解释什么叫代理：

###设计模式

首先，让我们来说一下“设计模式”。

**设计**指的是架构设计，它让人联想到一种管理事物的策略，它是一种通过各个不同单元的合作并最终有效完成任务的方法。

**模式**指的是对于一些主要的，司空见惯的，可以预测的行为方式经过提炼之后得到的具有可重复性使用的行事模型。“模式”往往给人一种印象，即这种方法在长久时间的检验下已经具有了普适性，易懂性和常用性的特征。如果用“适者生存”的法则来比喻，那些在真实世界不能承受时间锤炼的方式终会消逝，而那些坚强地留存下来的方式就形成了我们所谓的“模式”。

在软件开发领域，**设计模式**是一套长期检验后公认的易见、重用性高、能产生实际效能的经验总结，常用于软件代码的架构设计，策略规划和结构组织。

###代理

现在让我们来看看代理(Delegate)，我想到了三个解释：

* 动词，“to delegate”,意思是“交出控制权”。
* 名词，“a delegate”,意思是“代理人”。
* 改造的名词，“a delegator“,意思是“委托人”。

在现实世界中，代理这个词同事囊括了关系与责任。委托人(delegator名词)将控制权与责任委托(delegate动词)给另一个人，也就是代理人(delegate)。

那么我们怎么将其映射到软件中去呢？事实上，它能很好地运用到代码中。

相对于人，在软件中我们处理的对象是类的实例(或者可以是其它数据结构，比如structs，但我想让它保持简单，用“类”来概括这个想法)。代理在软件中是这样发生的，你会遇见这样的情况，一个类（委托者类）会将控制权与责任（这里值得是行为逻辑）委托给另一个类，这样的行为被称作代理。

## 代理是如何被使用的

没错，代理是一种设计模式。代理虽然是存在于其它各个平台的设计模式，但只有在Apple代理被如此深度地使用着，在iOS的API中代理几乎无所不在。这是一种将一个类的责任转移给另一个类的设计模式，因此它创造了各个类之间责任与关注点的分离。但是究竟是哪种责任与关注点？代理在实际中如何使用的呢？


### 通信

在实际应用中，代理通常用来作为一个类与另一个类之间通信的方式。观察Apple官方API对代理的处理方式会观察到线索，比如以下UITableViewDelegate的例子：

* tableView(_:willSelectRowAtIndexPath:)
* tableView(_:didSelectRowAtIndexPath:)
* tableView(_:didHighlightRowAtIndexPath:)

或者是UIScrollViewDelegate的例子：

* scrollViewDidScroll(_:)
* scrollViewWillBeginDragging(_:)
* scrollViewWillEndDragging(_:withVelocity:targetContentOffset:)
* scrollViewDidEndDragging(_:willDecelerate:)

根据我对Apple官方API的观察，代理倾向于使用在某些事件即将发生/已发生时一个实例与另一个实例之间的通信。tableview与scrollview将实现生命周期中某些响应行为的机会委托给其它类，称之为代理。

同样值得注意的是代理倾向于使用的通信范围，代理适用于一个实例仅需要发消息给一个单一的接收者（代理）这样的场景，然而NSNotificationCenter则适合于从一个实例对象将信息进行广播。

### 自定义

代理模式的另一种常见用法，是授权于代理实例，使其可以自定义实例内部的某些特定状态。再提一下，从Apple官方的一些API中可以看到一些这类使用场景，让我们首先看一下UITableViewDelegate：

* tableView(_:heightForRowAtIndexPath:)
* tableView(_:editActionsForRowAtIndexPath:)
* tableView(_:indentationLevelForRowAtIndexPath:)
* tableView(_:shouldHighlightRowAtIndexPath:)

这些自定义的点都是UITableView允许其代理自行处理的。有一些方法非常的重要，在没有从其代理收到信息前，tableview可能无法显示。tableview将这些点的实现方法的责任转移给了它的代理，这样做可以让控制更为灵活，便于自定义。


## 总结

代理在iOS中十分重要，理解它到底是什么成为关键点。这篇文章中，我们分解了名词“设计模式”和“代理”来解释为什么这些词被用来描述这样的策略。最后，我们看了代理模式是如何在实际中应用的，观察了两个代理的常用方式：类与类之间的通信以及自定义。

