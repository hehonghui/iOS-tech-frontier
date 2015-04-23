`CocoaPods指南`
---

>
* 原文链接 : [Introduction to CocoaPods Tutorial](http://www.raywenderlich.com/64546/introduction-to-cocoapods-2)
* 原文作者: [Joshua Greene](http://www.raywenderlich.com/u/JRG.Developer)
* 译文出自: [开发技术前线http://www.devtf.cn/](http://www.devtf.cn/)
* 译者 : [Lollypo](https://github.com/Lollypo) 
* 校对者: [Mr.Simple](https://github.com/bboyfeiyu)  
* 状态 :  未完成


<div align="center">

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2014/03/CocoaPods.png"/><br/>

<i>让多依赖管理更简单</i><br/>
</div>


**4/14/2014**: 完全更新到CocoaPods 0.31和iOS 7 ([Marcelo Fabri](http://www.raywenderlich.com/64546/about#mfabri)首发, [Joshua Greene](http://www.raywenderlich.com/64546/about#jgreene)更新).

在这篇教程中, 你将学习到如何使用非常流行的依赖管理工具 -- [CocoaPods](http://www.cocoapods.org/).

不过，先等一下!到底什么是依赖管理工具？你为什么需要它?

作为一名iOS开发者,你必定会大量使用到他人的代码以及各式各样的库. 想象一下假如你必须从头开始集成每一个库,这将是一件多么蛋疼的事呐!

假如没有依赖管理工具, 你也许仅仅将每个库的代码添加到你的项目中. 但是,这会有几个弊端:

- 库代码储存在你的项目内,浪费空间.
- 没有一个可以让你看到所有可用的库的中央仓库.
- 查找与更新到库的最新版本会变得特别困难尤其是当你需要同时更新多个库时.
- 下载、将库包含到你的项目内可能会让你更改下载的代码或者仅仅让它留在那里(使得后续的更新更加困难).

依赖管理工具可以帮助你克服这些问题.它将获取源代码库,解决库之间的依赖性,甚至用很小的代价创建和维护适当的环境来构建您的项目.

你将在本教程中获得使用CocoaPods以来管理的实战经验. 具体点说, 你将创建一个应用程序, 它使用一些开源库来从一个受欢迎的电视和电影信息网站获取信息, [trakt.tv](http://trakt.tv/).

CocoaPods将使这一项目更加简单. 读下去,亲眼看看!



### 起步

> 在你开始前: 本教程假设你已经熟悉Xcode,命令行的使用,AFNetworking的使用,与JSON格式等. 如果你对这些一点也不了解(有一个基本的了解也是可以的), 你应该先参考本站上的[其他教程](http://www.raywenderlich.com/?page_id=2519).

按照其[官网](http://cocoapods.org/)上所说, CocoaPods是“管理Objective-C项目依赖的最佳方式.“在这种情况下, 这广告是真的!

<div align="center">

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2014/02/true-dat.jpg"/>
</div>

你可以让CocoaPods为你做这些而不是从GitHub上下载代码然后拷贝到项目里去(从而使未来更新困难).

在本教程中, 你将使用[trakt.tv](http://trakt.tv/)构建一个应用程序显示即将播放的电视节目. 如果你还没有听说过它, trakt帮助你跟踪显示你的电视节目. Trakt有[这些功能](http://trakt.tv/about#features), 但是本篇教程中只会使用到这一功能.

你需要从trakt提供的JSON检索信息中获取数据. 在此过程中,为了简化下载, 解析,和显示结果集,你需要使用[AFNetworking](http://afnetworking.com/)与其他一些开源库.

开始, 首先你需要安装CocoaPods. CocoaPods运行在Ruby上, 这是其目前为止唯一的依赖项. 幸运的是, Mac OS X (从 OS X 10.7 Lion) 所有最新的的版本都预装了Ruby. 所以你只需要更新RubyGems就可以了(只需确保你使用的是最新版本).

因此,打开终端并输入如下命令:

```
sudo gem update --system
```

当需要时输入你的密码. 终端的输出应该像下面这样:

<div align="center">
<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2014/03/TerminalOutput.png"/><br/>
</div>

该更新需要花费一点时间. 你需要有耐心并给它几分钟时间来完成.你也可以期待终端窗口会打印关于最新版本的文档信息; 目前你可以忽略它.

下一步, 你需要安装CocoaPods. 在终端输入这个命令:

```
sudo gem install cocoapods
```

你在安装过程中可能得到这个提示:

```
rake's executable "rake" conflicts with /usr/bin/rake
Overwrite the executable? [yN]
```

如果是这样,只需要**输入y**继续。(这个警告因为更新rake gem是安装过程的一部分。你可以安全地忽略它.)

最后, 在终端输入这个命令完成CocoaPods的设置:

```
pod setup
```

这个过程可能需要花费一点时间,因为这个命令会克隆[CocoaPods Specs库](https://github.com/CocoaPods/Specs) into **~/.cocoapods/** 到你的计算机上.

太好了, 你现在已经完成了使用CocoaPods的准备工作!

<div align="center">
<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2014/03/oh-yeah.png"/>
</div>



### 介绍“Show Tracker”应用程序

在[这里](http://cdn4.raywenderlich.com/wp-content/uploads/2014/04/ShowTracker-Starter.zip)下载本教程的起步项目. 这提供了一个基本的UI让你开始——目前为止没有添加依赖关系.

打开**Main.storyboard**, 接着你会看到只有一个view controller:

<div align="center">
<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2014/03/RWViewController.png"/>
</div>

这是初始化的view controller, 简单的称为`ViewController`.

它有一个通过IBOutlet链接到`showsScrollView`属性上的UIScrollView和一个链接到`showsPageControl`属性上的`UIPageControl` .

这被设置为`showsScrollView`的委托且当前只有一个方法－`pageChanged:`, 这是一个连接到`showsPageControl`的值发生变化的事件.

现在, **关掉Xcode**.

是的,你没有看错!是时候创建pod文件了.



### 安装你的第一个依赖

打开你的终端并且使用**cd**命令导航到你的**ShowTracker**项目目录下:

```
cd ~/Path/To/Folder/Containing/ShowTracker
```

下一步输入这个命令:

```
pod init
```

这将会为你的项目创建一个默认的**Podfile**.这个**Podfile**就是定义你项目的依赖的地方.

输入该命令打开Xcode对**Podfile**进行编辑:

```
open -a Xcode Podfile
```

> **注意**: 你不应该使用文本编辑器来编辑pod文件,因为文本编辑器喜欢替换标准的引号为更生动吸引人的引号.这会使CocoaPods感到困惑并且报错, 所以最好只使用Xcode或另一个编程文本编辑器.

默认的**Podfile**应该是这样的:

```
# Uncomment this line to define a global platform for your project
# platform :ios, "6.0"
 
target "ShowTracker" do
 
end
```

替换`# platform :ios, "6.0"`为如下:

```
platform :ios, "7.0"
```

这告诉CocoaPods你的项目是基于iOS 7的.

许多库 – 包括AFNetworking都有一个最小的iOS版本要求.如果你忽略了这一行, CocoaPods假定为默认的目标版本(目前是iOS 4.3).

如果你只用过Objective-C编程, 这可能会使你有点奇怪 – 这是因为pod文件实际上是用Ruby编写的. 使用CocoaPods你不需要知道Ruby, 但你应该意识到,即使是很小的文本错误通常也会导致CocoaPods抛出一个错误.

这将是你用CocoaPods添加第一依赖的最后一步了!复制粘贴下面的文本到你的pod文件中,放在`target "ShowTracker" do`的后面:

```
pod 'AFNetworking', '2.2.1'
```

这会告诉CocoaPods你想要包含AFNetworking 2.2.1版本(本教程编写时的最新版)作为项目的依赖.

这个[链接](http://guides.cocoapods.org/syntax/podfile.html)有关于Podfile格式和语法的更多信息.如果你想做更复杂的东西(比如指定“任何版本高于”一个特定版本的库),您应该可以查到.

保存然后关闭pod文件.

现在你需要告诉CocoaPods为你的项目”安装"依赖。在终端输入以下命令(确保你仍然在ShowTracker项目的目录中而且已经创建了Podfile):

```
pod install
```

应该会看到类似于下面的输出:

```
Analyzing dependencies
Downloading dependencies
Installing AFNetworking (2.2.1)
Generating Pods project
Integrating client project
```

它还可能告诉你这样的事情:

```
[!] From now on use `ShowTracker.xcworkspace`.
```

如果你现在输入**ls**(或者使用Finder浏览你的项目文件夹),您将看到CocoaPods创建了一个Pods文件夹 -- 它储存了所有的依赖项, 还有**ShowTracker.xcworkspace**.

> #### 非常重要!
从现在开始, 如命令行所提示的那样, 你**必须**打开这个工作区(**ShowTracker.xcworkspace**) 而不是原本的项目!

关闭Xcode项目(如果你打开它了)然后**打开ShowTracker.xcworkspace**.



### 注册Trakt

在你使用Trakt APIs之前, 你首先需要[注册一个免费的账号](http://trakt.tv/join). 不要担心 – 这会很快而且很简单!

注册之后, 去到[Settings -> API](http://trakt.tv/settings/api)页面获取你的API key. 一直前进然后保持这个页面打开,因为你不久之后还会用到它.



### 测试AFNetworking

验证AFNetworking是否被成功添加, 创建一个新文件,通过**iOS -> Cocoa Touch -> Objective-C** class template.

给这个类命名为**TraktAPIClient**, 然后让它成为`NSObject`的之类.

确保你将新文件添加到ShowTracker项目而不是Pods项目中 --如果你看看你的项目导航栏, 你将会看到在你的工作区中现在有两个分开的项目.

<div align="center">
<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2014/03/Adding-TraktAPIClient.png"/>
</div>


打开**TraktAPIClient.h**然后替换原来的代码为如下:

```
// 1
#import <AFNetworking/AFNetworking.h>
 
// 2
extern NSString * const kTraktAPIKey;
extern NSString * const kTraktBaseURLString;
 
// 3
@interface TraktAPIClient : AFHTTPSessionManager
 
// 4
+ (TraktAPIClient *)sharedClient;
 
// 5
- (void)getShowsForDate:(NSDate *)date
               username:(NSString *)username
           numberOfDays:(int)numberOfDays
                success:(void(^)(NSURLSessionDataTask *task, id responseObject))success
                failure:(void(^)(NSURLSessionDataTask *task, NSError *error))failure;
 
@end
```

这是你刚刚做的一些事情:

1. 首先导入**AFNetworking**头文件, 也就是**AFNetworking.h**. 这其中包括了 `AFHTTPSessionManager`, 也就是将会作为`TraktAPIClient`的父类, 和一些其他有关联的网络处理.
2. 接下来, 声明两个`extern`修饰符修饰的字符串. 这意味着这些字符串是全局可用的.
3. 让`TraktAPIClient`去继承`AFHTTPSessionManager` 而不是`NSObject`, 这在第一步提到了.
4. 声明`sharedClient`方法, 这个方法将会返回一个[单实例](http://en.wikipedia.org/wiki/Singleton_pattern) 的 `TraktAPIClient`对象.
5. 最后, 声明一个辅助方法来使得能很容易地使用trakt API来显示为一个特定的日期,用户名和天数.


你可能想知道,为什么既然`AFHTTPSessionManager`早已能处理HTTP请求,我还要创建`TraktAPIClient`呢?

`TraktAPIClient`将使访问trakt API与大量的同一链接、同一个key的请求更加方便.这也封装了所有的网络请求和响应为一个类.

打开**TraktAPIClient.m**,将其内容替换为如下:

```
#import "TraktAPIClient.h"
 
// Set this to your Trakt API Key
NSString * const kTraktAPIKey = @"PASTE YOUR API KEY HERE";
NSString * const kTraktBaseURLString = @"http://api.trakt.tv";
 
@implementation TraktAPIClient
 
+ (TraktAPIClient *)sharedClient {
    static TraktAPIClient *_sharedClient = nil;
    static dispatch_once_t oncePredicate;
    dispatch_once(&oncePredicate, ^{
        _sharedClient = [[self alloc] initWithBaseURL:[NSURL URLWithString:kTraktBaseURLString]];
    });
    return _sharedClient;
}
 
- (instancetype)initWithBaseURL:(NSURL *)url {
 
    self = [super initWithBaseURL:url];
    if (!self) {
        return nil;
    }
 
    self.responseSerializer = [AFJSONResponseSerializer serializer];
    self.requestSerializer = [AFJSONRequestSerializer serializer];
    return self;
}
 
- (void)getShowsForDate:(NSDate *)date
               username:(NSString *)username
           numberOfDays:(int)numberOfDays
                success:(void(^)(NSURLSessionDataTask *task, id responseObject))success
                failure:(void(^)(NSURLSessionDataTask *task, NSError *error))failure
{
    NSDateFormatter* formatter = [[NSDateFormatter alloc] init];
    formatter.dateFormat = @"yyyyMMdd";
    NSString* dateString = [formatter stringFromDate:date];
 
    NSString* path = [NSString stringWithFormat:@"user/calendar/shows.json/%@/%@/%@/%d",
                      kTraktAPIKey, username, dateString, numberOfDays];
 
    [self GET:path parameters:nil success:^(NSURLSessionDataTask *task, id responseObject) {
        if (success) {
            success(task, responseObject);
        }
    } failure:^(NSURLSessionDataTask *task, NSError *error) {
        if (failure) {
            failure(task, error);
        }
    }];
}
 
@end
```

这段代码实现你在头文件中做的所有声明:

首先,你声明了字符串常量.确保你替换了**PASTE YOUR KEY HERE**为你实际的trakt API key.

接下来, 你实现 `sharedClient`,它使用中央调度(GCD)来创建一个单例实例. 

然后重写 `initWithBaseURL:`然后依次设置 `responseSerializer`和`requestSerializer`为默认的JSON serializer 

最后, 创建帮助方法使用trakt要求的格式来获取电视节目.

现在打开**AppDelegate.m**然后正确添加如下代码到目前已存在的import语句后:

```
#import <AFNetworking/AFNetworkActivityIndicatorManager.h>
```

接着正确添加如下代码的到`application:didFinishLaunchingWithOptions:`的return语句前:

```
[AFNetworkActivityIndicatorManager sharedManager].enabled = YES;
```

这将会自动的显示网络活动的指示器无论AFNetworking是否在处理网络请求.

Next, open **ViewController.m** and add the following import right after the last one:

接下来, 打开**ViewController.m** 然后正确添加如下到import语句的最后:

```
#import "TraktAPIClient.h"
```

接下来,替换`viewDidLoad`为如下代码:

```
- (void)viewDidLoad
{
    [super viewDidLoad];
 
    TraktAPIClient *client = [TraktAPIClient sharedClient];
 
    [client getShowsForDate:[NSDate date]
                   username:@"rwtestuser"
               numberOfDays:3
                    success:^(NSURLSessionDataTask *task, id responseObject) {
                        NSLog(@"Success -- %@", responseObject);
                    }
                    failure:^(NSURLSessionDataTask *task, NSError *error) {
                        NSLog(@"Failure -- %@", error);
                    }];
}
```

这段代码调用刚才创建的帮助方法来显示今天和未来3天的电视节目.

> **注意**: 这个API查询用户观看列表上的电视节目. 如果你想使用自己的用户名, 你首先要从[TV电视节目分类](http://trakt.tv/shows)**将电视节目添加到你的观看列表上add shows to your watch list** . 如果你不想这样做, 使用**rwtestuser** 作为用户名,该账户预先添加了电视节目.

构建然后运行, 您应该在控制台看到类似如下的输出:

<div align="center">
<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2014/03/Get-Shows-Output.png"/></br>
</div>



### 添加更多的Pods

现在你已经在实战中了解了CocoaPods, 现在只剩下一件事了…

<div align="center">
<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2014/03/add-more-pods.jpg"/>
</div>

> **实际上…** 你仍然需要在你的项目中. 尽管CocoaPods让你更新依赖库变的更加方便,每当库更新的时候你仍然要不时的更新自己的代码.

> 要保证持久运行最好的办法就是控制住你的依赖.

你需要添加两个以上的依赖到项目中.现在你可以在工作区找到**Pods文件**而不用通过命令行打开.

<div align="center">
<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2014/03/PodsInWorkspace.png"/>
</div>

打开Podfile并将如下两行代码添加到**AFNetworking**后面:

```
pod 'SAMCategories', '0.5.2'
pod 'Nimbus/AttributedLabel', '1.2.0'
```

、保存文件,然后安装依赖(像之前一样在终端输入**pod install**).

你注意到**Nimbus/AttributedLabel** pod有什么不同吗?在中间那个斜线叫什么名字?

大的pods通常被分割成几个子模块,依赖一个“核心”模块.在这个案例中,该斜线意味着AttributedLabel是Nimbus的子模块之一.

如果你不熟悉这些库,它们是:

[SSToolkit](http://sstoolk.it/)是一个方便iOS开发者收集的所面临的常见问题的解决方案.在ShowTracker应用程序中,您将使用它来检查设备是否支持视网膜显示.

[Nimbus](http://nimbuskit.info/), NimbusKit的简称, 这是另外一个有用的iOS类别与类的集合. 在这个应用程序中你主要使用其AttributedLabel组件.

为了验证一切是否仍然正常, 继续构建应用程序.现在你应该看到了几个警告信息!

“等一下, 我没对代码做什么啊”,你也许会这么想.

当你通过CocoaPods添加依赖时,pod的源文件就被包含在你的项目工作区中.每当你编译你的项目时,它们也被编译. 

因此,如果你使用的pod有许多的警告信息(或者更糟糕,一直无法编译),这意味着该库不再被维护了.

在这种情况下,你可以在[CocoaPods网站](http://cocoapods.org/)寻找另外一个替代的库.你能在那里找到关于pod的详细信息,包括pod的网站,文档,以及更多的信息.

然而,在这种情况下,警告主要是由于最近在iOS 7过时的方法(主要是调用“sizeWithFont:”),你可以安全地忽略.

添加如下代码到**Podfile**的最上面,告诉CocoaPods沉默pod所有依赖项的警告:

```
inhibit_all_warnings!
```

再次运行**pod install**, 构建你的项目, 你不会看到任何警告了.



### 完成Show Tracker

太好了, 你现在已经拥有了完成Show Tracker的依赖.所以让我们开始吧!

打开**ViewController.m**,并在最后添加以下导入语句:

```
#import <AFNetworking/UIKit+AFNetworking.h>
#import <Nimbus/NIAttributedLabel.h>
#import <SAMCategories/UIScreen+SAMAdditions.h>
```

接下来,添加以下属性后到`@interface ViewController ()`之后:

```
@property (nonatomic, strong) NSArray *jsonResponse;
@property (nonatomic) BOOL pageControlUsed;
@property (nonatomic) NSInteger previousPage;
```

这些将用于存储下载的电视节目信息.

在文件的底部创建一个名为`loadShow:`的方法:

```
- (void)loadShow:(NSInteger)index 
{
    // empty for now...
}
```

该方法将负责向用户呈现一个页面显示其选择的电视节目的信息.

现在你需要修改`viewDidLoad`方法,让它可以通过响应对象做一些有用的事情而不仅仅是记录.首先添加下面一行到`[super viewDidLoad];的后面`:

```
self.previousPage = -1;
```

然后替换内容为如下语句块:

```
// Save response object
self.jsonResponse = responseObject;
 
// Get the number of shows
NSInteger shows = 0;
for (NSDictionary *day in self.jsonResponse)
    shows += [day[@"episodes"] count];
 
// Set up page control
self.showsPageControl.numberOfPages = shows;
self.showsPageControl.currentPage = 0;
 
// Set up scroll view
self.showsScrollView.contentSize = CGSizeMake(CGRectGetWidth(self.view.bounds) * shows, CGRectGetHeight(self.showsScrollView.frame));
 
// Load first show
[self loadShow:0];
```

现在名为`responseObject`的JSON数据被存储了,该页面控制和滚动视图被创建,`loadShow:`被调用且成功加载第一个电视节目.

现在你可以通过以下代码替换`loadShow:`改变显示信息:

```
- (void)loadShow:(NSInteger)index
{
    // 1 - Find the show for the given index
    NSDictionary *show = nil;
    NSInteger shows = 0;
 
    for (NSDictionary *day in self.jsonResponse) {
        NSInteger count = [day[@"episodes"] count];
 
        // 2 - Did you find the right show?
        if (index < shows + count) {
            show = day[@"episodes"][index - shows];
            break;
        }
 
        // 3 - Increment the shows counter
        shows += count;
    }
 
    // 4 - Load the show information
    NSDictionary *showDict = show[@"show"];
 
    // 5 - Display the show title
    UILabel *titleLabel = [[UILabel alloc] initWithFrame:CGRectMake(index * CGRectGetWidth(self.showsScrollView.bounds) + 20, 40, CGRectGetWidth(self.showsScrollView.bounds) - 40, 40)];
    titleLabel.text = showDict[@"title"];
    titleLabel.backgroundColor = [UIColor clearColor];
    titleLabel.textColor = [UIColor whiteColor];
    titleLabel.font = [UIFont boldSystemFontOfSize:18];
    titleLabel.textAlignment = NSTextAlignmentCenter;
 
    // 6 - Add to scroll view
    [self.showsScrollView addSubview:titleLabel];
}
```

构建运行,然后你会看到是这样的:

<div align="center">
<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2014/03/Title-Only-266x500.png"/>
</div>

酷,今晚行尸走肉将会更新,不过是哪一季呢?

添加以下代码到**loadShow:**中放在section #6的后面:

```
// 5.1 - Create formatted airing date
static NSDateFormatter *formatter = nil;
if (!formatter) {
    formatter = [[NSDateFormatter alloc] init];
    formatter.dateStyle = NSDateFormatterLongStyle;
    formatter.timeStyle = NSDateFormatterShortStyle;
    formatter.timeZone = [NSTimeZone timeZoneWithAbbreviation:@"PST"];
}
 
NSDictionary *episodeDict = show[@"episode"];
 
NSTimeInterval showAired = [episodeDict[@"first_aired_localized"] doubleValue];
NSString *showDate = [formatter stringFromDate:[NSDate dateWithTimeIntervalSince1970: showAired]];
 
// 5.2 - Create label to display episode info
UILabel *episodeLabel = [[UILabel alloc] initWithFrame:CGRectMake(index * CGRectGetWidth(self.showsScrollView.bounds),
                                                         360, CGRectGetWidth(self.showsScrollView.bounds), 40)];
NSString* episode  = [NSString stringWithFormat:@"%02dx%02d - \"%@\"",
                      [[episodeDict valueForKey:@"season"] intValue],
                      [[episodeDict valueForKey:@"number"] intValue],
                      [episodeDict objectForKey:@"title"]];
episodeLabel.text = [NSString stringWithFormat:@"%@\n%@", episode, showDate];
episodeLabel.numberOfLines = 0;
episodeLabel.textAlignment = NSTextAlignmentCenter;
episodeLabel.textColor = [UIColor whiteColor];
episodeLabel.backgroundColor = [UIColor clearColor];
 
CGSize size = [episodeLabel sizeThatFits:CGSizeMake(CGRectGetWidth(self.view.frame),
                                          CGRectGetHeight(self.view.frame) - CGRectGetMinY(episodeLabel.frame))];
CGRect frame = episodeLabel.frame;
frame.size.width = self.view.frame.size.width;
frame.size.height = size.height;
episodeLabel.frame = frame;
 
[self.showsScrollView addSubview:episodeLabel];
```

这将创建一个包含关于这一剧集的信息标签,包括季数,集数,标题和一个人类可读的将会播出的日期和时间.

构建运行,你现在可以看到,《行尸走肉》已经在播第4季了!

<div align="center">
<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2014/03/Title-Details.png"/>
</div>

是不是觉得好像有什么在呼唤你将屏幕的中心巨大的空白的填满?如果放一张图片会怎样?

还是在**loadShow:**方法中, 添加如下代码到 section #6后面:

```
// 5.3 - Get image
NSString *posterUrl = showDict[@"images"][@"poster"];
if ([[UIScreen mainScreen] sam_isRetina]) {
    posterUrl = [posterUrl stringByReplacingOccurrencesOfString:@".jpg" withString:@"-300.jpg"];
} else {
    posterUrl = [posterUrl stringByReplacingOccurrencesOfString:@".jpg" withString:@"-138.jpg"];
}
 
// 5.4 - Display image using image view
UIImageView *posterImage = [[UIImageView alloc] init];
posterImage.frame = CGRectMake(index * CGRectGetWidth(self.showsScrollView.bounds) + 90, 105, 150, 225);
[self.showsScrollView addSubview:posterImage];
 
// 5.5 - Asynchronously load the image
[posterImage setImageWithURL:[NSURL URLWithString:posterUrl] placeholderImage:[UIImage imageNamed:@"placeholder.png"]];
```

这段代码首先基于设备是否支持视网膜显示决定要下载的图片(因为不需要在非视网膜设备上下载大图).注意`isRetinaDisplay`｀是SSToolkit中的一个帮助方法.

之后,一个`UIImageView`被创建然后添加到滚动视图中.

最后,使用AFNetworking提供的便利方法下载图像并设置到图像视图上.该方法还设置一个占位符显示图像,直到下载完成.

构建运行,你将看到该电视节目的美丽的海报.

<div align="center">
<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2014/03/Title-Details-Poster.png"/>
</div>

太棒了!你现在有显示第一个节目的信息但是其他的呢?

您首先需要通过`showsPageControl`在分页视图上显示其他电视节目信息.

替换`pageChanged:`方法为如下代码:

```
- (IBAction)pageChanged:(id)sender
{
    // Set flag
    self.pageControlUsed = YES;
 
    // Get previous page number
    NSInteger page = self.showsPageControl.currentPage;
    self.previousPage = page;
 
    // Call loadShow for the new page
    [self loadShow:page];
 
    // Scroll scroll view to new page
    CGRect frame = self.showsScrollView.frame;
    frame.origin.x = frame.size.width * page;
    frame.origin.y = 0;
    [UIView animateWithDuration:.5 animations:^{
        [self.showsScrollView scrollRectToVisible:frame animated:NO];
    } completion:^(BOOL finished) {
        self.pageControlUsed = NO;
    }];
}
```

这里`pageControlUsed`被设为 YES,表明新页面将显示因为用户通过页面控制选择它而不是滚动滚动视图。(稍后你需要使用滚动视图处理滚动事件).

然后,新页面加载以及其`frame`的计算.最后,新页面显示在屏幕上.

构建运行,您现在可以使用页面控制浏览页面.简单地点击页面中的圈控制和屏幕将导航到下一个或前一页。很酷,不是吗?

但是,有些东西不见了…当页面滚动的时候发生了什么?如果你这样尝试过,你会发现你得到了空白页, 除非你已经使用视图控制滚动到页面.让我们修复它吧.

粘贴以下到文件的`UIScrollViewDelegate`方法之后:

```
- (void)scrollViewDidScroll:(UIScrollView *)sender
{
    // Was the scrolling initiated via page control?
    if (self.pageControlUsed)
        return;
 
    // Figure out page to scroll to
    CGFloat pageWidth = sender.frame.size.width;
    NSInteger page = floor((sender.contentOffset.x - pageWidth / 2) / pageWidth) + 1;
 
    // Do not do anything if we're trying to go beyond the available page range
    if (page == self.previousPage || page < 0 || page >= self.showsPageControl.numberOfPages)
        return;
    self.previousPage = page;
 
    // Set the page control page display
    self.showsPageControl.currentPage = page;
 
    // Load the page
    [self loadShow:page];
}
 
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView
{
    self.pageControlUsed = NO;
}
```

注意`self.pageControlUsed`的作用.是因为当滚动视图抵消`pageChanged`的改变(当你使用页面控制更改页面的时候),这称为`scrollViewDidScroll:`,但如果你在这里处理滚动,你会在同一个页面加载两次.所以你需要检测通过页面滚动时启动控制,什么也不做.

构建运行,你现在也可以通过滚动浏览电视节目!

但是,如果如果用户想要知道更多关于这个节目的信息呢?Trakt有一个已经注册的电视节目的页面,和它的URL是在应用程序的信息已经收到了。

你可以使用`NIAttributedLabel` 表示它.

在**ViewController.m**的顶端,替换`@interface ViewController ()`为如下:

```
@interface ViewController () <NIAttributedLabelDelegate>
```

这个声明表示`ViewController`遵从`NIAttributedLabelDelegate`协议.

现在添加以下的代码到`NIAttributedLabelDelegate`的方法到**ViewController.m**的底部:

```
- (void)attributedLabel:(NIAttributedLabel *)attributedLabel didSelectTextCheckingResult:(NSTextCheckingResult *)result atPoint:(CGPoint)point
{
    [[UIApplication sharedApplication] openURL:result.URL];
}
```

当某个链接被点击时这个委托方法就会被调用.它只是请求`UIApplication`打开URL,这将启动Safari打开它.

但如何在应用程序中添加链接标签呢?很简单,就是改变#5部分的`loadShow:`的实现,因此显示标题的是一个`NIAttributedLabel`而不是`UILabel`。

替换`// 5 - Display the show title`部分为如下代码:

```
// 5 - Display the show title
NIAttributedLabel *titleLabel = [[NIAttributedLabel alloc] initWithFrame:CGRectMake(index * CGRectGetWidth(self.showsScrollView.bounds), 40, CGRectGetWidth(self.showsScrollView.bounds), 40)];
titleLabel.text = showDict[@"title"];
titleLabel.backgroundColor = [UIColor clearColor];
titleLabel.linkColor = [UIColor redColor];
titleLabel.font = [UIFont systemFontOfSize:18];
titleLabel.textAlignment = NSTextAlignmentCenter;
[titleLabel addLink: [NSURL URLWithString:showDict[@"url"]] range:NSMakeRange(0, titleLabel.text.length)];
titleLabel.delegate = self;
```

主要的区别是 `linkColor`的存在与`addLink:`. 这与设置委托标签为`self`同样重要,表示当前实例的`ViewController`.

构建运行,你将看到红色的标题.如果你点击它,Safari浏览器将打开,并显示trakt页面!



### 何去何从  

你可以从[这里](http://cdn1.raywenderlich.com/wp-content/uploads/2014/04/ShowTracker-Final.zip)下载完成的项目.

现在你对于[CocoaPods](http://guides.cocoapods.org/)的使用有了基本的了解, 你可以通过阅读CocoaPods Guides学习CocoaPods更细节的使用方法, 其中包括如何创建自己的pod说明书与如何提交到[官方仓库](https://github.com/CocoaPods/Specs).

要检查CocoaPods是否已经支持某个库,你可以搜索[CocoaPods网站](http://cocoapods.org/).

这里有一些我认为最有用的库:

- [AFNetworking](https://github.com/AFNetworking/AFNetworking): 网络连接和响应处理.
- [CorePlot](https://github.com/core-plot/core-plot): 绘图框架.
- [MagicalRecord](https://github.com/magicalpanda/MagicalRecord): 超级棒, 很方便的获取Core Data 1!!!11!!!!1!
- [MBProgressHUD](https://github.com/jdg/MBProgressHUD): 一个活动的指示器,可添加到你的View上
- [Nimbus](https://github.com/jverkoey/nimbus): 保持iOS框架与文档一致.

你也应该看看[trakt API文档](http://trakt.tv/api-docs).文档非常齐全,而且你也可以用它做很多事情.

对于这个案例程序, 有许多地方你可以改进, 例如:

- 如果没有给定日期范围内的电视节目会发生什么?
- 如果有很多节目怎么办? 所有的页面都加载然后丢弃掉, 这很快就会消耗大量的内存!
- 有更多的信息可以被展现出来 – 例如简介,显示频道播出的节目等等.

我希望你喜欢这个教程,而且我期望在论坛上阅读你的评论.

知道另一个有用的库吗?告诉我!你创建过一个pod说明书吗?也告诉我让我知道!

