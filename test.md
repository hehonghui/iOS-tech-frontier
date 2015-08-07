iOS 9中UIDynamics的应用
---

> * 原文链接 : [Playing With UIDynamics in iOS 9](http://fancypixel.github.io/blog/2015/06/19/playing-with-uidynamics-in-ios-9/?utm_campaign=iOS%2BDev%2BWeekly&utm_medium=email&utm_source=iOS_Dev_Weekly_Issue_205)
* 原文作者 : [Andrea - @theandreamazz]
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [jointSong](https://github.com/joint-song/) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :   校对中

**注意 : 翻译完之后请认真的审核一遍有没有错字、语句通不通顺，谢谢~**


`这里是翻译原文，注意翻译时英文和译文都要留在该文档中，并且是一段英文原文下面直接跟着写译文，便于校对。如下示例 : `

UIDynamics was a welcome addition to the iOS 7 SDK. It’s basically a physics engine backing common UIViews, allowing us to define physics traits to the UI elements. The API is fairly straightforward, so you can easily create animations and transitions that look and feel great. I already covered the basics in this article a while ago, this time we’ll be looking at what’s new in UIDynamics in iOS 9.

UIDynamics在iOS7中第一次出现。他是一个支持UIView的物理引擎，允许我们为UI元素定义一系列的物理特性。API也非常的简洁，所以你可以非常轻松得创造看起来很不错的动画和转场效果。我已经在这片文章/*URL*/中介绍了UIDynamics的基本概念和常识，这次我们将来看一看iOS 9中的UIDynamics有什么新的变化。

Getting Started
We know that writing quality software is hard and complex: It is not only about satisfying requirements, also should be robust, maintainable, testable, and flexible enough to adapt to growth and change. This is where “the clean architecture” comes up and could be a good approach for using when developing any software application.
The idea is simple: clean architecture stands for a group of practices that produce systems that are:

## 入门指南
大家都知道要写一款精品软件是有难度且很复杂的：不仅要满足特定要求，而且软件还必须具有稳健性，可维护、可测试性强，并且能够灵活适应各种发展与变化。这时候，“清晰架构”就应运而生了，这一架构在开发任何软件应用的时候用起来非常顺手。

这个思路很简单：简洁架构 意味着产品系统中遵循一系列的习惯原则：