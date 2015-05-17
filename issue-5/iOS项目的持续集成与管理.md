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


