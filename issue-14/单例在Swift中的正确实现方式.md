# 单例在Swift中的正确实现方式
---
> * 原文链接 : [The Right Way to Write a Singleton](http://krakendev.io/blog/the-right-way-to-write-a-singleton?utm_campaign=This%2BWeek%2Bin%2BSwift&utm_medium=web&utm_source=This_Week_in_Swift_45)
* 原文作者 : [Hector Matos](http://krakendev.io/?author=5592eaffe4b08369d0205792)
* 译文出自 :  [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [Gottabe](https://github.com/Gottabe) 

尽管在我之前的博文里我就写过关于管理状态的那些坑，但是有时候我们就是无法避免它们。其中一类管理状态的方式我们耳熟能详 － 单例。但是在Swift中有好几种不同的方式来实现一个单例。到底哪一个才是正确的方式呢？在这边博客里，我将和你好好聊聊单例的历史和在Swift中单例正确的实现方式。

如果你想直接就看在Swift中如何正确地写出单例同时看到证明其“正确性”，你可以直接滚动到这篇博文的底部。

## 让我们先回忆一下

Swfit源于Objective-C，高于Objective-C。在Objective-C中，我们是这样写单例的：

```objc
@interface Kraken : NSObject
@end

@implementation Kraken

+ (instancetype)sharedInstance {
	static Kraken *sharedInstance = nil;
	static dispatch_once_t onceToken;
	
	dispatch_once(&onceToken, ^{
		sharedInstance = [[Kraken alloc] init];
	});
	return sharedInstance;
}

@end
```

当我们把它写出来之后，就能清楚的看到一个单例的架构，接下来我们把单例的规则理一理，以便更好的理解它： 

## 单例的道义

关于单例，有三件事是你必须要记住的：

* 单例必须是唯一的，所以它才被称为单例。在一个应用程序的生命周期里，有且只有一个实例存在。单例的存在给我们提供了一个唯一的全局状态。比如我们熟悉的NSNotification，UIApplication和NSUserDefaults都是单例。
* 为了保持一个单例的唯一性，单例的构造器必须是私有的。这防止其他对象也能创建出单例类的实例。感谢所有帮我指出这点的人。
* 为了确保单例在应用程序的整个生命周期是唯一的，它就必须是线程安全的。当你一想到并发肯定一阵恶心，简单来说，如果你写单例的方式是错误的，就有可能会有两个线程尝试在同一时间初始化同一个单例，这样你就有潜在的风险得到两个不同的单例。这就意味着我们需要用GCD的dispatch_once来确保初始化单例的代码在运行时只执行一次。

成为独一无二并只在一个地方做初始化并不难。这篇博客接下来要记住的内容就是要单例遵守了更难察觉的dispatch_once的规则。

## Swift单例

打从Swift 1.0开始，就有好几种创建单例的方法。在[这里](https://github.com/hpique/SwiftSingleton)，[这里](http://stackoverflow.com/questions/24024549/dispatch-once-singleton-model-in-swift)和[这里](https://developer.apple.com/swift/blog/?id=7)都有详细的说明。但谁有空点进去看呢？先来个剧透；一共有四种写法，请让我一一道来：

### 最丑陋的写法

```swift
class TheOneAndOnlyKraken {
	class var sharedInstance: TheOneAndOnlyKraken {
		struct Static {
			static var onceToken: dispatch_once_t = 0
			static var instance: TheOneAndOnlyKraken? = nil
		}
		dispatch_once(&Static.onceToken) {
			static.instance = TheOneAndOnlyKraken()
		}
		return Static.instance!
	}
}
```

这种写法其实就是把Objective-C中的写法照搬过来。我觉得奇丑无比因为Swift是一门更加简洁且表达力更强的语言。我们要做的比那些搬运工要好，要比他们好。

### 结构体写法

```swift
class TheOneAndOnlyKraken {
	class var sharedInstance: TheOneAndOnlyKraken {
		struct Static {
			static let instance = TheOneAndOnlyKraken()
		}
		return Static.instance
	}
}
```

这个在Swift 1.0的时候必须得这么写，因为那个时候，类还不支持全局类变量。而结构体却支持。正因为在全局变量上的局限性，我们不得不这么写。这比直接把Objective-C那一套搬过来要好，但是还不够。好玩的是，在Swift 1.2发布后的几个月后，我还是经常能看到这些的写法，但这个以后再表。

### 全局变量写法（又名“一句话写法”）

```swift
private let sharedKraken = TheOneAndOnlyKraken()
class TheOneAndOnlyKraken {
	class var sharedInstance: TheOneAndOnlyKraken {
		return sharedInstance
	}
}
```

在Swift 1.2之后，我们拥有了访问控制修饰词和可以使用全局类变量。这意味着我们不用整一个全局变量集群，也可以防止命名空间冲突。这才是我心目中Swift应该有的样子。

这会你可能会问我为什么没有在我们的结构体和全局变量实现中看不到dispatch_once。但其实Apple指出，这两个方法同时满足了我上面提到的dispatch_once条文。下面就截取他们写的[Swift Blog](https://developer.apple.com/swift/blog/?id=7)中的一段话来证明他们以将dispatch_once整合进去了：

> "全局变量（静态成员变量和结构体以及枚举）的延迟构造器在其被第一次访问时会加载，并以`dispatch_once`的方式启动来确保初始化的原子性。这让你写代码时可以用一种很酷的方式来使用`dispatch_once`：直接用一个全局变量的构造器去做初始化并用private来修饰。“ -- Apple's Swift Blog

就官方文献来看，Apple就给出这点说明。但这仅意味着对全局变量和结构体／枚举的静态成员我们是有证据的！目前来看，100%不会错的是用一个全局变量将一个单例的初始化包在一个隐含了dispatch_once的延迟构造器中。但是我们的全局类变量怎么办呢？！？！？！？

### 正确的写法 

```swift
class TheOneAndOnlyKraken {
	static let sharedInstance = TheOneAndOnlyKraken()
}
```

为了写这篇文章，我做了一番研究。事实上，之所以写这篇文章，是因为今日在Capital One的一次讨论，一位PR希望在我们所有的应用中用统一的方式来写单例。我们其实知道什么才是单例“正确”的书写方式，但是我们无法自证。如果不旁征博引来证明我们是正确的简直就是徒劳。这是我和缺乏信息的互联网／博文圈之间的较量。每个人都知道如果网上没写那就不是真的。这让我非常难过。

![](http://static1.squarespace.com/static/5592eb03e4b051859f0b377f/t/55a8b8bfe4b0671daf04772e/1437120704789/?format=500w)

我的搜索达到了互联网的尽头（Google到了第10页）却啥也没得到。难道真就发表一行单例的证明吗？也许有人做了，就是没找到而已。

所以我觉得我自己把所有的初始化构造器的方法都实现一遍，然后通过断点来检查他们。当分析完每个栈帧我所发现的相似点的时候，我终于发现期待已久的东西 － 证据！

直接上图，对了（还有emoji类哦！）：

![](http://static1.squarespace.com/static/5592eb03e4b051859f0b377f/t/55a8b833e4b044eedeff6ac4/1437120566797/Screen+Shot+2015-07-16+at+4.56.38+PM.png?format=1000w)

使用全局单例

![](http://static1.squarespace.com/static/5592eb03e4b051859f0b377f/t/55a8b860e4b00cca65ee95a2/1437120615798/Using+the+One+Line+Singleton?format=1000w)

使用一行单例

第一张图显示了一个全局let实例化。红框表示的地方就是证据。在实际去实例化Karken单例之前，是先由一个*swfit_once*调用了一个*swift_once_block_invoke*。加上Apple说他们通过一个dispatch_once的block去延迟初始化一个全局变量，我们现在可以说这就证明了他们所说的。

带着这个信息，我又跟踪了我们既亮眼又简洁的一行单例。正如第二张图所以，两者简直一样！这就足以证明我们的一行单例实现是正确的。现在全世界都清净了。还有，既然这篇文章已经上了互联网，那么它*肯定*就是真理！

##不要忘了INIT的私有化！

[@davedelong](https://twitter.com/davedelong)，Apple的架构师，非常含蓄的给我指出，你必须确保你的inits是私有的。只有这样才能确保你的单例是真正的独一无二，也能防止其他对象通过访问控制机制来创建他们自己的但是是你这个类的单例。因为在Swift中，所有对象的构造器默认都是public，你需要重写你的init让其成为私有的。这并不难实现而且也能确保我们的一行单例的优雅和简洁：

```swift
class TheOneAndOnlyKraken {
	static let sharedInstance = TheOneAndOnlyKraken()
	private init() {} // 这就阻止其他对象使用这个类的默认的'()'初始化方法
}
```

这么做能确保任何类如果尝试通过()初始化方法来初始化TheOneAndOnlyKraken时，编译器都会报错：

![](http://static1.squarespace.com/static/5592eb03e4b051859f0b377f/t/55ae6ad6e4b035a17a870044/1437493975886/?format=1000w)

你看！这就是完美的，一行实现单例。

## 结论

呼应一下[jtbandes](http://stackoverflow.com/users/23649/jtbandes)在[top rated answer to swift singletons on Stack Overflow](http://stackoverflow.com/a/24147830)上精彩的评论，我就无法找到关于使用'let'就能确保线程安全的任何文献。我其实依稀记得在去年的WWDC上有类似的这么一个说法，但你可不能指望读者或者googlers在尝试证明这就是在Swift中写单例的正确方法的时候就恰巧碰到那说法。不管怎么讲，我希望这篇博文能帮助一些人理解一行单例在Swift中是打开单例的正确方式。

基友们 Happy Coding!