iOS项目的持续集成与管理
---

> * 原文链接 : [Continuous Integration & Deployment for iOS Projects](https://medium.com/ribot-labs/continuous-integration-deployment-for-ios-projects-7358b72ca2e9)
* 原文作者 : [Matt Oakes](https://medium.com/@matto1990)
* [译文出自 :  开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [Sam Lau](https://github.com/samlaudev) 
* 校对者: [Mr.Simple](https://github.com/bboyfeiyu)  
* 状态 :  校正完 

当实现新功能时，如果忽略可维护性而引入[技术债务](http://en.wikipedia.org/wiki/Technical_debt)，那将会需要延迟解决它或导致增加维护成本。

最近我们已经思考通过哪些方式来提高代码的质量：

* 当代码的质量下降时，通过设置一些工具来马上提醒开发者
* 文档化一些编码规范和思考在过去的几个项目中如何避免维护性差的问题

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

#Code Coverage
We also generate code coverage reports using the Gcovr tool, also installed with Homebrew. To set up a project you need to change two build settings for the debug configuration of the main target. Set both Generate Test Coverage Files and Instrument Program Flow to Yes.

We then need to add OBJROOT=./build to the end of the XCTool command when we run the unit tests and then generate our code coverage reports.

```
$ gcovr -r . — object-directory build/Project.build/Debug-iphonesimulator/Project.build/Objects-normal/x86_64 — exclude ‘.*Tests.*’ — xml > coverage.xml

```

The coverage report that Gcovr outputs can then be published by the Cobertura Jenkins plugin which provides a visual way of seeing how code coverage is changing over time.

We now have a way of seeing not only if the tests are passing, but also how thoroughly we are testing our code.

#代码覆盖率
我们也会用[Gcovr](http://gcovr.com/)工具来生成代码覆盖率报告，Gcovr的安装方式也是Homebrew。你需要针对main target的debug congfiguration改变两个构建设置来配置项目。将*Generate Test Coverage Files*和*Instrument Program Flow*都设置为*YES*。

<div align="center">

<img src="https://d262ilb51hltx0.cloudfront.net/max/1200/1*Aq8ClxmME0tcpf747uPguQ.png"/>
</div>

当我们运行单元测试来生成代码覆盖率报告时，我们需要将*OBJROOT=./build*添加到XCTool命令行的尾部。

```
$ gcovr -r . — object-directory build/Project.build/Debug-iphonesimulator/Project.build/Objects-normal/x86_64 — exclude ‘.*Tests.*’ — xml > coverage.xml

```
Gcovr输出的代码覆盖率报告也会被插件[Cobertura Jenkins plugin](https://wiki.jenkins-ci.org/display/JENKINS/Cobertura+Plugin)发布，这个插件会提供一种可视化的方式来根据时间的推移来显示代码覆盖率。

现在我们不仅可以看到测试是否通过，还可以看到代码的测试覆盖范围。

#Static Analysis
One of the most powerful set of tools to keep code quality high is static analysis. These tools will scan your code and generate a report of where your code breaks one of the code style rules. Some examples of these rules are:

* Unused variables or parameters
* Long variable names, method names or lines
* Overriding a method and not calling super on a method that requires it
* Overly long or complex methods
* And so on…

We use OCLint which is a static analysis tool which works with C, C++ and Objective-C. OCLint provides great integration with XCTool using a special json-compilation-database reporter. We first need to add a second reporter to our XCTool command and then pass that report to OCLint to perform the static analysis.

```
$ xctool -workspace Project.xcworkspace -scheme Project -reporter junit:junit-report.xml -reporter json-compilation-database:compile_commands.json test
$ oclint-json-compilation-database -e Pods -report-type pmd -o oclint-pmd.xml

```

The report generated is in the PMD format and can then be published in Jenkins by the PMD Plugin. With this plugin you can also set limits to how many of each priority of warning (low, medium and high) there can be before the tests fail. We initially set these limits low so we are alerted as soon as we introduce code that could be improved.

#静态分析
在工具集中，其中一个强大并能够保持高质量的代码的工具就是静态分析工具。这些工具会扫描你的代码，然后生成一个报告，这个报告会告诉你破坏代码风格规则的代码位置。举几个规则的例子：

* 未使用的变量或参数
* 长变量名，方法名或代码行
* 覆盖一个方法，但没有在这个方法调用*super*
* 方法太长或方法过于复杂
* 还更多的规格...

我们使用[OCLint](http://oclint.org/)静态分析工具，这个工具能够支持C，C++和Objective-C语言。OCLint通过结合XCTool使用来生成**json-compilation-database** reporter
，从而提供[great integration](http://docs.oclint.org/en/dev/guide/xctool.html)特性。我们首先添加另一个reporter到我们的XCTool命令行，然后将那个report传递到OCLint来执行静态分析。

```
$ xctool -workspace Project.xcworkspace -scheme Project -reporter junit:junit-report.xml -reporter json-compilation-database:compile_commands.json test
$ oclint-json-compilation-database -e Pods -report-type pmd -o oclint-pmd.xml

```
这个report以[PMD](http://pmd.sourceforge.net/)的方式来生成，然后使用[PMD Plugin](https://wiki.jenkins-ci.org/display/JENKINS/PMD+Plugin)被发布到Jenkins。有了这些插件之后，你也可以在测试失败之前，设置每个警告的优先级(底，中，高)中一些限制。最初，我们设置这些限制为低，那么只要我们引入代码，就会被提醒，从而提高代码质量。

<div align="center">

<img src="https://d262ilb51hltx0.cloudfront.net/max/1600/1*fkHdHqA_WGoWATdAs6or0A.png"/>
</div>

#Automated Deployment
The last piece of the puzzle is less about code quality and more about time saving. Developers will regularly need to send out builds through Crashlytics to designers for design reviews, or to clients at the end-of-sprint demos. Sending out a build of the app usually only takes around ten minutes of a developer’s time, but it requires them to switch tasks and break their flow.

We have recently set up a nightly build system which will automatically send a new version of the app each morning to everyone on the project at ribot.

To do this we are using fastlane, which is an amazing collection of tools to define lanes of actions to perform. We currently have three lanes defined, one for releasing just to ribot developers, one for releasing to everyone at ribot and another for releasing to the client.

```
before_all do |lane|
 cert
 sigh
end
desc “Deploy a new build to ribot iOS developers over crashlytics”
lane :dev do
 ipa
 crashlytics({ groups: ‘ribot-developers’ })
end
desc “Deploy a new build to people at ribot over crashlytics”
lane :internal do
 ensure_git_status_clean
 append_build_time
 ipa
 crashlytics({ groups: ‘ribot’ })
 reset_git_repo
end
desc “Deploy a new build to everyone over crashlytics”
lane :external do
 ensure_git_status_clean
 increment_build_number
 ipa
 crashlytics({ groups: [‘ribot’, ‘client’] })
 commit_version_bump
 add_git_tag
 push_to_git_remote
end
after_all do |lane|
 clean_build_artifacts
end

```
A lane is run using the fastlane tool (installed through Ruby Gems).

```
fastlane internal

```

At the start of all the lanes we automatically make sure we have a valid signing certificate and an up-to-date provisioning profile. All of our configuration lives in a .env file, which allows us to have default settings but override them when we run the fastlane command if needed.

In the future we will also look into automating the app store submission process using the deliver action.

#自动部署
最后一个问题不是如何提高代码质量，而是如何节省时间。开发者通常都会将编译好的代码通过[Crashlytics](https://www.crashlytics.com/)发送到设计师来设计审查，或在sprint结束演示时发给用户。发送一个已经编译好的app通常花一个开发者的10分钟左右时间，但它需要他们来切换任务和干扰他们的心流。

最近我们已经配置一个在夜晚构建系统，它会在早上自动发送一个新版本的app给每个人。

为了做到这样，我们使用[fastlane](http://fastlane.tools/)。fastlane是一个定义**lanes**的一些操作来执行的强大工具集。现在我们有三个已经定义好的lanes，一个是用来发布给ribot开发者，一个是用来发布给在ribot的每个人，最后一个是发布给用户。


```
before_all do |lane|
 cert
 sigh
end
desc “Deploy a new build to ribot iOS developers over crashlytics”
lane :dev do
 ipa
 crashlytics({ groups: ‘ribot-developers’ })
end
desc “Deploy a new build to people at ribot over crashlytics”
lane :internal do
 ensure_git_status_clean
 append_build_time
 ipa
 crashlytics({ groups: ‘ribot’ })
 reset_git_repo
end
desc “Deploy a new build to everyone over crashlytics”
lane :external do
 ensure_git_status_clean
 increment_build_number
 ipa
 crashlytics({ groups: [‘ribot’, ‘client’] })
 commit_version_bump
 add_git_tag
 push_to_git_remote
end
after_all do |lane|
 clean_build_artifacts
end

```

通过使用**fastlane**工具(通过Ruby Gems来安装)来运行一个lane。

```
fastlane internal

```
在开始使用所有的*lanes*之前，我们应该自动确保我们有一个有效的signing certificate和最新的provisioning profile。所有我们的配置都放在一个*.env*文件，它让我们有些默认配置，但当我们运行*fastlane*根据需要来覆盖它们。

在将来，我们会通过使用*deliver*操作来自动化app store提交过程。

#Going Forward
So far we have seen good results on the projects where we have trialled this process. We expect to see our code quality increase now we have these tools in place. The reports will enable us to quantify how our code quality has changed over time. We are looking forward to seeing what happens on our next project with these tools in place.

*Thanks to Renzo Crisóstomo for the guide I used to get my head around how these tools fit together.*

*Cover image: Cogs by SomeDriftwood. At ribot we call tools that help us automate something a “cog”.*

#最后总结
到目前为止，我们已经尝试这些过程，并在工程中呈现出好的结果。我们期望看到只要适当地使用这些工具，就能提高代码的质量，这些报告将会让我们随着时间推移来量化代码质量。我们期待在下一个工程中适当地使用这些工具会发生什么。