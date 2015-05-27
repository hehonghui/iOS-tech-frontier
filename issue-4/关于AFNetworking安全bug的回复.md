关于AFNetworking 安全bug的回复
---

> * 原文链接 : [A response to recent concerns about security vulnerabilities in AFNetworking](https://gist.github.com/AlamofireSoftwareFoundation/f784f18f949b95ab733a?utm_campaign=iOS_Dev_Weekly_Issue_196&utm_medium=email&utm_source=iOS%2BDev%2BWeekly)
* 原文作者: [AlamofireSoftwareFoundation](https://github.com/AlamofireSoftwareFoundation)
* 译文出自: [开发技术前线http://www.devtf.cn/](http://www.devtf.cn/)
* 译者 : [Lollypo](https://github.com/Lollypo) 
* 校对者: [Lollypo](https://github.com/Lollypo) 
* 状态 :  完成

上周大量的出版物流出这么一个故事，大约有1000多个应用程序由于AFNetworking的一个SSL Bug而易受侵害。这些文章中对于该问题包含了一些不正确的误导性称述。

正是因为如此，我们发表了这篇文章来回应并澄清这些不正确的描述。



### 背景信息

对于那些不熟系AFNetworking的童鞋，我们准备了一些与该故事有关联的详细信息:

- [AFNetworking](https://github.com/AFNetworking/AFNetworking)是一个开源的第三方库，其基于苹果的基础平台上为开发者提供了便利的工具。
- AFNetworking 其中的一个组件名为`AFSecurityPolicy`，它负责依据应用程序配置的策略处理身份验证。这其中包括了[X.509证书 ](http://en.wikipedia.org/wiki/X.509)(当通过HTTPS连接时服务器传回的)的校验。
- [证书绑定](http://security.stackexchange.com/a/29990) 是通过强制执行证书服务器发送证书与客户端的凭证来改进标准TLS评分的安全技术。从[版本1.2.0](https://github.com/AFNetworking/AFNetworking/tree/1.2.0)开始，AFNetworking提供证书绑定功能。
- [中间人攻击](http://en.wikipedia.org/wiki/Man-in-the-middle_attack)是一个攻击服务，通过将其本身嵌入到服务器与客户端之间的这种方式，这样两者都认为它们仍然是在直接通信。
- 这样的攻击会通过不可信的WiFi热点代理客户端与服务器之间的请求。如果没有正确的验证响应，攻击者可以拦截通讯信息，这样可能会暴露用户凭证或其他敏感信息。
- AFNetworking文档 [强烈建议应用程序间通过HTTPS以及使用证书/公钥绑定来通信，以减轻MitM攻击](http://cocoadocs.org/docsets/AFNetworking/2.5.3/Classes/AFSecurityPolicy.html).。



### 时间线

考虑到有些朋友对此还不太了解，我们提供了与此相关的突发事件的时间表：

- 在2015年2月12日，[AFNetworking 2.5.1](https://github.com/AFNetworking/AFNetworking/tree/2.5.1)发布。此版本合并了一个补丁来修正当安全机制由`SSLPinningMode` 设为`AFSSLPinningModeNone`时的[校验凭证](https://github.com/AFNetworking/AFNetworking/blob/2.5.1/AFNetworking/AFSecurityPolicy.m#L257-L259)。默认情况下，证书服务器不会在
授权改变时做合法性验证，除非客户端配置不同的行为，比如启用SSL绑定。

- 在2015年3月12日，[从这个GitHub的Issue]( https://github.com/AFNetworking/AFNetworking/issues/2590) 中我们第一次意识到这种变化的行为的影响。

- 在2015年3月26日，Minded Security Research机构的Simone Bovi 和 Mauro Gentile 发表了一篇[博客](http://blog.mindedsecurity.com/2015/03/ssl-mitm-attack-in-afnetworking-251-do.html?m=1)详述在AFNetworking 2.5.1中潜在的MitM漏洞。

- 同样在2015年3月26日， [AFNetworking 2.5.2](https://github.com/AFNetworking/AFNetworking/tree/2.5.2)发布。这个版本[恢复到之前的证书校验机制](https://github.com/AFNetworking/AFNetworking/blob/2.5.2/AFNetworking/AFSecurityPolicy.m#L257-L265)以及如果将机制`validatesDomainName`设为`YES`，那么就设置安全机制`SSLPinningMode` 为`AFSSLPinningModeNone`。

- 在2015年4月20日，[AFNetworking 2.5.3](https://github.com/AFNetworking/AFNetworking/tree/2.5.3)发布。该版本做的附加改变是默认将所有机制的`validatesDomainName`设为`YES `。

- 在2015年4月21日，[一个Issue在Github上被打开](https://github.com/AFNetworking/AFNetworking/issues/2673)，该Issue请求增加关于AFNetworking安全特性的文档。我们遵循了这个建议并且正在积极的检查我们的参考文档。

- 同样在2015年4月21日，来自SourceDNA的Nate Lawson发布了一篇[博客](http://sourcedna.com/blog/20150420/afnetworking-vulnerability.html) 宣传说在应用商店中识别iOS应用程序的工具就是使用了AFNetworking 2.5.1的。一些记者，包括Ars Technica的的Dan Goodin，发表了一篇[文章](http://arstechnica.com/security/2015/04/1500-ios-apps-have-https-crippling-bug-is-one-of-them-on-your-device/)引用该博客及其作者。但是所有出版物都未向任何一个AFNetworking维护者请求回应。

- 在2015年4月24日，SourceDNA继续发表了一篇[博客](http://sourcedna.com/blog/20150424/afnetworking-strikes-back.html)指称更多的应用程序变得易受侵害。Ars Technica的的Dan Goodin同样也跟着发表了一篇[文章](http://arstechnica.com/security/2015/04/critical-https-bug-may-open-25000-ios-apps-to-eavesdropping-attacks/)做了同样的事.同样的，没有任何一个出版物向任何一个AFNetworking维护者请求回应。



### AFNetworking用户可操纵的信息

如果你是AFNetworking用户，那么这里是你需要知道的一些可操纵的信息:

#### 如果你的应用程序通过HTTPS通讯但是尚未启用SSL 绑定，这可能会被报道中的中间人攻击

来自AFSecurityPolicy的文档

> 添加SSL证书绑定有助于避免你的应用程序遭受中间人攻击或其他安全漏洞。处理敏感的客户数据或财务信息的应用程序我们强烈建议启用SSL绑定并使用 HTTPS 连接通信。

按照以下这些建议操作的任何应用程序在任何时刻应该都不会暴露上述漏洞。

####如果你的应用程序启用了SSL绑定且通过HTTPS通信,它就不会像报道中说的那样易受 MitM攻击

大量使用 AFNetworking的应用程序遵循了建议的步骤启用了SSL证书或公共密钥绑定。这些应用程序不会像报道中说的那样易受 MitM攻击。

####如果你正在使用早先的AFNetworking版本, 我们强烈建议你升级到版本2.5.3

AFNetworking 2.5.1 以及 2.5.2 不适合应用程序产品，尤其是它们不经过额外配置就无法提供TLS评分。

AFNetworking 2.5.3默认为安全的行为，即使未使用SSL绑定也支持域名校验。

####如果你正在使用NSURLConnection / NSURLSession而不是AFNetworking，你仍然需要审查你的身份校验的实现

苹果内置的NSURLConnection/NSURLSession以及安全框架API提供的凭证校验的安全实现。然而，就像任何API，应用程序只是在使用这些API的时候是安全的。

决定是否使用AFNetworking并不能保证你的应用程序免受攻击，例如MitM。这完全却取决于该应用程序如何使用这些可用的API。最终，这些开发人员还要在生产环境中测试应用程序的健壮性和网络安全。

#### 如果你想报告一个漏洞，请发邮件给[security@alamofire.org](security@alamofire.org)

我们会尽快的作出回应。

#### 如果你想要贡献自己的一份力量来让AFNetworking变得更改，请打开一个Issue或者Pull Request 

AFNetworking是开源的，这意味着任何人都有机会作出贡献，使它变得更好。[Issues](https://github.com/AFNetworking/AFNetworking/issues/new) 或者[Pull Requests](https://github.com/AFNetworking/AFNetworking/issues/new) 都行。



### 关于负责的安全研究和新闻声明

安全研究人员在实现面向用户的软件的安全性方面扮演者一个重要的角色。如果安全研究人员与开发者同心协力，遵守协定作出[负责任的漏洞报告](http://en.wikipedia.org/wiki/Responsible_disclosure)，那么就可以快速的解决应用程序的脆弱性问题同时将现有用户的风险降到最低。

我们是这样子做的，然而，对于某些安全研究人员以及出版商决定由他们自身报道AFNetworking的事情我们感到非常失望。信息安全是所有人都需要了解的一个重要话题，无论是安全研究人员或是新闻工作者都有机会让读者了解这些实际情况。很不幸的是，大多数时候，这些报道都是通过恐惧而不是客观现实来吸引流量的。

目前尚未由明确的方法来证明由多少应用程序遭受了这种行为；猜测安全问题的严重性来计算当它们发生的时候会造成多么大的破坏以及反应的比例数。同样，基于提供少量的工具给商家或他们的用户来推测出脆弱性的应用程序。

事实是，一直以来编写安全的软件都是一个困难的挑战。这样做需要跨越多个学科的工程师的合作。它是极为重要的任务，那个人最好是理性的、 负责任的。

作为软件维护者，由许多东西我们可以做得更好的，并且正在积极采取步骤来改善我们的组织和流程。我们期待着与信息安全社会的成员密切合作，从现在开始负责任地找出并解决任何漏洞。



### 一项关于负责任的开放源代码维护声明

我们对那些正在使用AFNetworking的所有开发者以及iOS社区表示最诚挚的歉意。

作为一个著名的开源项目维护者，提供满足高标准的选择让用户选择作为应用程序的依赖是我们的责任。我们未能尽快的发布更新版本的回应。我们未能有效地传达给你的重要的安全信息。以上，我们真的很抱歉，我们愿意承担全部责任。

在未来的几周，我们将推出了重构的的 AFNetworking 和及其相关的项目，以确保软件质量和通信的一致性一同向前发展。对于用户而言这意味着更加频繁的版本发布和更多的透明度和反馈处理issues和pull requests的过程。我们很兴奋地想知道对于AFNetworking 项目和它的用户，这将意味着什么。