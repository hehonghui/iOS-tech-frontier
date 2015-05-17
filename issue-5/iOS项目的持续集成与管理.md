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

#Unit Testing
We test our iOS projects using Specta and Expecta.

Specta gives us a Behaviour-Driven Development (BDD) style syntax for writing tests which (we think) is more readable than XCTest syntax. It also has a very powerful system of grouping tests together and running blocks of code before or after those tests, which can greatly reduce the amount of duplicated code.

Expecta is a matcher framework which we use to create assertions in our tests. The syntax is very powerful, yet at the same time more readable than the built in XCAssert suite. For example:

```
expect(@"foo").to.equal(@"foo");
expect(foo).notTo.equal(1);
expect([bar isBar]).to.equal(YES);
expect(baz).to.equal(3.14159);
```

We run our tests from XCode while developing and using XCTool on Jenkins, installed via Homebrew. XCTool is an alternative to xcodebuild which allows you to very easily run a test suite from the command line and generate JUnit-style reports.

```
$ xctool -workspace Project.xcworkspace -scheme Project -reporter junit:junit-report.xml test

```

These reports are then published in Jenkins using the JUnit Plugin which provides graphs of the unit test results over time, giving us an insight into how stable our tests are.

#单元测试
我们使用[Specta](https://github.com/specta/specta)和[Expecta](https://github.com/specta/expecta)来测试我们的iOS项目。

Specta让我们采用行为驱动开发(BDD)风格的语法来编写测试，相比于*XCTest*的语法，它更加易读。它还有一个强大的分组测试功能，在测试之前或之后运行一些代码块，这样的话，能够极大地减少重复代码。

Expecta是一个匹配器框架，我们可以在测试中使用它来创建断言。它的语法非常强大，与此同时，它比内建的*XCAssert*套件更加易读。例如：

```
expect(@"foo").to.equal(@"foo");
expect(foo).notTo.equal(1);
expect([bar isBar]).to.equal(YES);
expect(baz).to.equal(3.14159);
```
我们在开发时，通过XCode来运行测试；而使用通过Homebrew来安装的Jenkins时，会借助[XCTool](https://github.com/facebook/xctool)。XCTool是一个可代替的选择来*xcodebuild*，它能让你通过命令行的方式来非常轻松地运行测试套件和生成JUnit风格的测试报告。

```
$ xctool -workspace Project.xcworkspace -scheme Project -reporter junit:junit-report.xml test

```

这些测试报告会发布在Jenkins上，而Jenkins会使用[JUnit Plugin](https://wiki.jenkins-ci.org/display/JENKINS/JUnit+Plugin)来根据时间的推移提供单元测试结果的图表，同时会向我们显示我们的测试是否稳定。

<div align="center">

<img src="https://d262ilb51hltx0.cloudfront.net/max/1600/1*YXbjsC43-eVvMPPw2CXn6A.png"/>
</div>

#Pull Request Testing
We want our tests to run as soon as possible so we know right away if we have broken something. At ribot we make changes on feature branches and then submit a pull request on Github so the code can be reviewed by another developer. As soon as one is opened, we run all the tests to ensure that nothing has been broken.

To manage this, we set up the Github Pull Request plugin which sends a message from Github to Jenkins when a new pull request is opened. If any tests fail it will be shown in Github and we will not merge it in until it has been fixed.

#Pull Request测试
我们想我们的测试尽可能运行以至于如果我们破坏什么东西，我们就会马上知道。我们在[feature branches](https://www.atlassian.com/git/tutorials/comparing-workflows/feature-branch-workflow/)做些修改，然后提交一个pull request到Github，那么代码就会被另一个开发者审查。只要被打开，我们就能运行所有的测试来确保没有任何东西被破坏。

当新的pull requst是开放状态时，为了管理这些，我们安装[Github Pull Request plugin](https://wiki.jenkins-ci.org/display/JENKINS/GitHub+pull+request+builder+plugin)来将信息从Github发送到Jenkins。如果有任何测试失败，它将会显示在Github，然后我们就不将代码合并，直到代码被修复为止。

