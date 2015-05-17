iOS项目的持续集成与管理
---

> * 原文链接 : [Continuous Integration & Deployment for iOS Projects](https://medium.com/ribot-labs/continuous-integration-deployment-for-ios-projects-7358b72ca2e9)
* 原文作者 : [Matt Oakes](https://medium.com/@matto1990)
* [译文出自 :  开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [Sam Lau](https://github.com/samlaudev) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  未完成 

Ignoring maintainability introduces technical debt, which will either need to be addressed later or will lead to increasing costs when implementing new features.

当实现新功能时，如果忽略可维护性而引入[技术债务](http://en.wikipedia.org/wiki/Technical_debt)，那将会需要延迟解决它或导致增加维护成本。

We have recently been looking into how to increase our code quality by:

* Setting up tools to immediately alert the developers when code quality drops

* Documenting code guidelines and thinking about how we could have avoided maintainability problem in past projects


最近我们已经思考通过哪些方式来提高代码的质量：

* 当代码的质量下降时，通过设置一些工具来马上提醒开发者
* 文档化一些编码规范和思考在过去的几个项目中如何避免维护性差的问题

**I am going to briefly outline what we have set up to automatically monitor code quality.**

**我将会简单地概括我们需要设置什么才能自动监控代码质量.**

#Groundwork
We have opted to base our continuous integration setup on Jenkins, running on a Mac Mini that lives in our studio. As much as I dislike how Jenkins looks and works, it is by far the most stable and suitable tool for this job.

We have Jenkins installed through Homebrew and Ruby installed with rbenv, which provides us an up-to-date and stable Ruby Gems environment. With these two package managers we are able to install almost any tool we need, in a way that is much less likely to break with system updates compared with using the OS X provided Ruby.

#基础
我们选择一个持续集成工具[Jenkins](https://jenkins-ci.org/)，让它运行在一台放在我们工作室的Mac Mini。其实我不怎么喜欢Jenkins，但到目前为止，它是最稳定和最适合的工具来完成这些工作。

我们已经通过[Homebrew](https://brew.sh/)和[rbenv](https://github.com/sstephenson/rbenv)来分别安装Jenkins和Ruby，而rbenv能够为我们提供一个最新和稳定的[Ruby Gems](https://rubygems.org/)环境。有个Homebrew和Ruby Gems两个包管理工具之后，我们就几乎能够安装所有我们需要的工具，但很少会破坏与原有OS X系统更新提供的Ruby。

