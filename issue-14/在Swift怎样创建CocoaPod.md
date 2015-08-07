
* 原文链接：[How to Create a CocoaPod in Swift](http://www.raywenderlich.com/99386/create-cocoapod-swift)
* 原文作者：[ Joshua Greene ](http://www.raywenderlich.com/u/JRG.Developer)
* [译文出自：开发者前线 www.devtf.cn](www.devtf.cn)
* 译者：[MrLoong](https://github.com/MrLoong)
* 校对者：[MrLoong](https://github.com/MrLoong)
* 状态：完成

[![Learn how to make your own
CocoaPod!](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/ice_cream_shop_logo.png)](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/ice_cream_shop_logo.png)


在制作这个美味的冰淇凌中的教程中学会如何使用自己的CocoaPod


你可能熟悉一些众所周知的东西，开源的[open-source
CocoaPods](https://github.com/CocoaPods/Specs/tree/master/Specs)，例如[Alamofire](https://github.com/Alamofire/Alamofire)或[MBProgressHUD](https://github.com/jdg/MBProgressHUD)，但有的时候你并不能找到一个符合你要求的pod，或者你可能想要把一个大的项目分成小的项目，或是可复用组建。


幸运的是创建自己的CocoaPods很容易。


如果你已经针对你的元件创建一个Cocoa Touch框架，那么你已经完成了大部分困难的工作。如果你没有创建过，不要惊慌，因为他确实很简单。


如果你仅仅是创建了IOS应用的一部分，那也是ok的。你可以很轻松的创建一些新的pod用于特殊环境下使用的推送类和功能。


本教程以[How to Use CocoaPods with
Swift](http://www.raywenderlich.com/97014)结束。如果你之前从来没有使用过CocoaPods，然而这是本教程的一先决条件。

开始
-------------------


你的顶级客户是一个冰淇凌店，他们的冰淇凌是如此的受欢迎，他们的柜台跟不上客户的订单。他们需要创建一个简洁的IOS应用。让用户使用他们的Phone订购冰淇凌。你将开始开发这个应用程序。


从[这](http://cdn2.raywenderlich.com/wp-content/uploads/2015/06/IceCreamShop_Final1.zip)下载这个开始的项目



这是[How to Use CocoaPods with
Swift](http://www.raywenderlich.com/97014)最终版本



这个应用有一些pod开发包已经包含在下载中，所以你不需要运行pod install去安装他们。




打开*IceCreamShop.xcworkspace*然后点击*Main.storyboard*，在*Views\\Storyboards & Nibs*看看这个应用是如何布局的。



下面是快速*Choose Your Flavor*的应用场景，核心功能如下

-   *PickFlavorViewController*: handles user interaction, such as when
    the user selects an ice cream flavor.    
-   *PickFlavorViewController*：处理用户交互，例如当用户选择一个口味的冰淇凌的时候
-   *PickFlavorDataSource*: is the data source for the collection view
    that displays ice cream flavors.
-   *PickFlavorDataSource*:是选择冰淇凌口味试图的数据源。
-   *IceCreamView*: is a custom view that displays an ice cream cone and
    is backed by a `Flavor` model.
-   *IceCreamView*: 是一个冰淇凌自定义模型。
-   *ScoopCell*: is a custom collection view cell that contains a
    *ScoopView*, which is also backed by an instance of the `Flavor`
    model class.
-   *ScoopCell*:自定义集合试图集合单元包含一个*ScoopView*，也是风味模型的一个支持类。
    
[![Storyboard
Annotated](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/storyboard_annotated-427x500.png "Storyboard Annotated")](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/storyboard_annotated.png)

这个冰淇凌店的老板非常喜欢这个应用程序，但他们又添加了一个新的要求：在他们的应用程序中需要有相同的口味风格选择。


等等，那不是最初的设计，但是，应该没有问题的对于一个NB的开发者来说。

[![SuperDev](http://cdn1.raywenderlich.com/wp-content/uploads/2014/11/SuperDev.jpg)](http://cdn1.raywenderlich.com/wp-content/uploads/2014/11/SuperDev.jpg)



你能猜猜这个功能是怎么做的吗？是的，你讲把这个功能集成到你自己的CocoaPod！。

 建立你自己的Pod
---------------------------------



创建一个新的Xcode项目并且选择*iOS\\Framework & Library\\Cocoa
Touch Framework*然后点击下一步。

[![Create Cocoa Touch
Framework](http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/cocoa_touch_framework-480x284.png "Create Cocoa Touch Framework")](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/cocoa_touch_framework.png)


将工程命名为*RWPickFlavor*并且选择*Swift*语言，点击*Next*


这个教程的工程将创建在*\~/Documents/Libraries*，在你的主目录中选择*Documents*文件夹。如果你没有*Libraries*文件夹，在底部点击新建文件夹按钮并且创建它。



完成，选择*Libraries*文件夹并且单价创建



保存你开发的pods这个目录非常重要，因为你将引用这个目录在你本地开发你的Podfile期间



正常情况下，当你使用CocoaPods，你在你的Podfile包含的关系

 ``` 
 pod ' PodName ', '~> 1.0'                                                
 ```                                                                      



 ``` 
pod ' MyPodName ', :path => ' ~/Path/To/Folder/Containing/My/Pod '  
 ```          
 
但是当你开发你自己的CocoaPod，你讲在本地建立一个特殊的路径，像这样：



这种方法有两种好处：


    
    1.他使用你本地机器上的文件对于你的pod，而不是从远程仓库中获取。
    
    2.正常情况下，你不需要选择一个pod包含到你的app中，因为这些选择将在你下一次运行pod install       时被覆盖，pod将会重新从远程仓库读取并且覆盖更改的文件。


你可以使用不同的位置对你的pods进行开发，一般情况下我建议还是将他们放在*\~/Documents/Libraries*。如果你和一个团队合作开发，CocoaPods扩展作为扩展主目录。因此你不需要努力构建绝对路径在Podfile目录中。
，


你也能使用其他的CocoaPods在你的工程中作为项目的依赖对于你创建CocoaPod。你只需要一个Podfile来管理你的CocoaPod依赖关系。



关闭Xcode，然后在终端输入以下命令


 ```                              
 cd ~/Documents/Libraries/RWPickFlavor                                    
 pod init                                                                 
 open -a Xcode Podfile
                                                     
 ```                                                                     
 


这是创建一个新的Podfile并且在Xcode中打开它。


使用下面的命令对Podfile进行更新：

```                               
 platform :ios, '8.0'                                                     
 use_frameworks!                                                         
                                                                          
 target 'RWPickFlavor' do                                                 
   pod 'Alamofire', '~> 1.2'                                              
   pod 'MBProgressHUD', '~> 0.9.0'                                        
 end                                                                      
```                                                                      


这声明*RWPickFlavor*将在[Alamofire](https://github.com/Alamofire/Alamofire) 和
[MBProgressHUD](https://github.com/jdg/MBProgressHUD)提供依赖关系


保存并且关闭Podfile，然后在终端输入以下命令

```                              
pod install
```                                                                      
 


正如你所期望的，这将创建一个工作区间并且安装你所需要的各种文件。



注意：如果*pod install* 给你任何警告，这样的话你可能使用的是旧版本。Swift基于CocoaPods，尤其是Alamofire，需要 0.36 CocoaPods版本或更新。根据以下命令，你可以检测你已经安装的CocoaPods版本。

```
pod --version

```


如果是版本过低的原因，输入以下终端命令来安装最新的版本。

```
sudo gem install CocoaPods

```



输入以下命令，打开并且创建一个新的*RWPickFlavor*工作区间。

```
open RWPickflavor.xcworkspace
```


你的项目导航器看起来应该是这个样子。

[![RWPickFlavor New Pod Files
Hierarchy](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/rwpickflavor_new_pod_files-255x320.png "RWPickFlavor New Pod Files Hierarchy")](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/rwpickflavor_new_pod_files.png)


你现在需要从*IceCreamShop*复制一些已经存在的文件在*RWPickFlavor*工作区间。



首先在*RWPickFlavor.xcworkspace*创建一个组，把你需要的文件复制进去。

Categories

Controllers

Factories

Models

Views

-   Ice Cream
-   Storyboards & Nibs



现在拖拽每一个文件，除了 *AppDelegate.swift*和*LaunchScreen.xib*，从*IceCreamShop.xcworkspace*拖拽进*RWPickFlavor.xcworkspace*，就像这样

[![Copy Workspace
Files](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/copy_workspace_files.gif)](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/copy_workspace_files.gif)


当被提示时，确保每一个需要被复制的条目被复制安装了而不是简单的链接。



当你完成了，*RWPickFlavor*应该有以下文件：

*RWPickFlavor*

RWPickFlavor.h

*Categories*

-   RGBAColorFromString.swift

*Controllers*

-   PickFlavorDataSource.swift
-   PickFlavorViewController.swift

*Factories*

*Models*

*Supporting Files*

*Views*

*Ice Cream*

-   IceCreamView.swift
-   ScoopCell.swift
-   ScoopView.swift

*Storyboard & Nibs*

-   Main.storyboard

Once you’re sure all the files have been copied over, *delete* the
originals and any empty groups from *IceCreamShop*, leaving just the
files in *RWPickFlavor*. Take care not to delete any of the following:

-   AppDelegate.swift
-   LaunchScreen.xib
-   Images.xcassets
-   Anything under the *Supporting Files* group


现在打开*Info.plist*，找到*Supporting Files*组，并且删除*Main storyboard file base name*



构建并运行，你不应该看见任何错误，并且你会看见“Ice Cream Shop”的图标和黑色的背景。

图像呢？
----------------------



你可能会注意，你并没有复制*Resources*组，这是因为你需要复制的仅仅是*background.jpg*他自己本身到*Resources*文件夹中。不完整的*Images.xcassets*资源文件。


首先在*RWPickFlavor*创建一个*Resources*组



下一步，选择*Images.xcassets*在*IceCreamShop*，点击右侧*background*，并且选择*Show In Finder*，像这样。

[![Background Show In
Finder](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/background_show_in_finder-480x267.png "Background Show In Finder")](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/background_show_in_finder.png)



现在拖拽*background.jpg*到*RWPickFlavor*的 *Resources*组中。当提示的时候依然检测每一个条目是否被安装。当你复制后，从*IceCreamShop*中删除*Images.xcassets*原来的背景



最后，在*RWPickFlavor*更新视图的图像上选择你的场景在*Main.storyboard*，所以引用*background.jpg*替代*background*

[![Update PickFlavorViewController Image View
Image](http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/update_pickflavorviewcontroller_imageview-411x320.png "Update PickFlavorViewController Image View Image")](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/update_pickflavorviewcontroller_imageview.png)


信不信，最难创造的是你的pod。

[![High
Five!](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/high_five_kitten-349x500.jpg)](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/high_five_kitten.jpg)

CocoaPods and Git 
-----------------


自从CocoaPods傻瓜式引导，每一个pod将需要有他自己的git仓库。如果你已经有一个首选的git托管，好的，你可以用它来作为你的仓库。



如果没有，[GitHub](https://github.com)是一个很好的的选择因为它是众所周知的开发平台，与很多自由的开源项目。


这个教程使用[GitHub](https://github.com)，但你也可以使用你的git仓库。

 GitHub设置
-----------------

First, [Sign up](https://github.com/join) or
[Login](https://github.com/login) to your GitHub account.

首先[创建](https://github.com/join)或[登陆](https://github.com/login)你的GitHub账户。



下一步，点击顶部右侧*+* (create new)图标并且选择 New repository展示如下：

 [![Github: New
Repository](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/github_new_repository-480x152.png "Github: New Repository")](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/github_new_repository.png)



进入*RWPickFlavor*仓库，并且选择*Create repository*



Github将创建一个新的仓库在你的账户下。然后你会看到下面的屏幕与一个快速设置，现实您储存的仓库网址。

[![Github Quick
Setup](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/github_quick_setup-480x56.png "Github Quick Setup")](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/github_quick_setup.png)



所以距离打开到现在你需要这个URL在短短的一瞬间。



现在你需要第二个仓库涌来存放你所有私有的pod，你将在本教程中使用。


打开[github.com](http://github.com)在你个新的栏目。再一次点击*Create new*图标，并且选择*New repository*。仓库名为*RWPodSpecs*，并且选择*Create repository*



离开这个标签，这样当你需要使用它的时候很简单去使用了。

Podspec 设置
-------------



现在你需要创建*RWPickFlavor.podspec*文件对于*RWPickFlavor*，*Podspec*包含基础信息，尤其是pod的名字，版本和Git下载的URL


在终端输入以下命令。

```
cd ~/Documents/Libraries/RWPickFlavor
pod spec create RWPickFlavor
open -a Xcode RWPickFlavor.podspec

```



这是创建的*RWPickFlavor.podspec*并且打开它在Xcode



默认情况下有很多优秀的例子和文档，然而你并不需要大部分的他们。



以下*RWPickFlavor.podspec*代替了一切。

```
Pod::Spec.new do |s|
 
  # 1
  s.platform = :ios
  s.ios.deployment_target = '8.0'
  s.name = "RWPickFlavor"
  s.summary = "RWPickFlavor lets a user select an ice cream flavor."
  s.requires_arc = true
 
  # 2
  s.version = "0.1.0"
 
  # 3
  s.license = { :type => "MIT", :file => "LICENSE" }
 
  # 4 - Replace with your name and e-mail address
  s.author = { "[Your Name Goes Here]" => "[Your_Email@Your_Email_Domain.com]" }
 
  # For example,
  # s.author = { "Joshua Greene" => "jrg.developer@gmail.com" }
 
 
  # 5 - Replace this URL with your own Github page's URL (from the address bar)
  s.homepage = "[Your RWPickFlavor Homepage URL Goes Here]"
 
  # For example,
  # s.homepage = "https://github.com/JRG-Developer/RWPickFlavor"
 
 
  # 6 - Replace this URL with your own Git URL from "Quick Setup"
  s.source = { :git => "[Your RWPickFlavor Git URL Goes Here]", :tag => "#{s.version}"}
 
  # For example,
  # s.source = { :git => "https://github.com/JRG-Developer/RWPickFlavor.git", :tag => "#{s.version}"}
 
 
  # 7
  s.framework = "UIKit"
  s.dependency 'Alamofire', '~> 1.1'
  s.dependency 'MBProgressHUD', '~> 0.9.0'
 
  # 8
  s.source_files = "RWPickFlavor/**/*.{swift}"
 
  # 9
  s.resources = "RWPickFlavor/**/*.{png,jpeg,jpg,storyboard,xib}"
end


```


就像一个Podfile，Podspec是用Ruby写的。要格外小心，不要做任何错误，否则将可能无法验证安装后是否成功.



这是发生了什么？

    
1.    首先你详细说明了pod的基本信息。Swift必须最低基于CocoaPods8.0版本或者更高。
    如果你指定了较低的版本，pod无法正常安装。
    
    
2.    对于你指定的CocoaPod版本好，本质上Podspec是一个快照。当你更新一个pod，你也将需要更
    新Podspec的版本。所有的高版本度遵循[Semantic Versioning](http://semver.org/)。
    如果你并没有相似的版本，看[如何使用CocoaPods在Swift中]
    (http://www.raywenderlich.com/97014)
    
    
3.  所有pods必须指定一个许可证。如果你没有，CocoaPods将在你安装的时候提出警告。并且你
    不能把它上传到分支。
    

4.    在这里，您可以指定关于自己的信息，pod作者。输入你的名字和e-mail地址。替代那些占
    位符文本
    
    
5.    在这里你需要指定你的pod的网页和网址。就是简单的从浏览器地址栏复制和粘贴到Github
    主页
    
    
6.    从上面创建第一个“Quick Setup”，那部分的git下载网址替代URL。最好使用一个HTTP或
    HTTPS：URL更容易被其他用户处理。如果你想你也可以使用SSH URL，但是你需要确保你团
    队的每一个人－无论谁需要访问CocoaPod－已经有自己的公钥／私钥针对你的Git host
    

7.	 这里你需要说明你的框架和pod依赖关系。
	

    
8.    在这里你可以说明指定基于文件扩展的公共源文件，在这种情况下，你可以说明‘.swift’作
    为扩展
    

     
 9.   最后你可以根据指定的文件扩展名指定资源。
    


  推送Git
-----------



你终于准备推送*RWPickFlavor* pod GitHub主页


在终端输入以下命令，

```
cd ~/Documents/Libraries/RWPickFlavor
git init
git add .
git commit -m "Initial commit"
git tag 0.1.0
git remote add origin [Your RWPickFlavor Git URL]
git push -u origin master --tags

```



然后跳出提示，输入你的用户名和密码到Github



这所有文件在*RWPickFlavor*目录中，创建一个新的*0.1.0*标签，并且推送你每一个东西到你的远程仓库



祝贺你，你已经创建你的第一个CocoaPod！

[![Victory
Dance!](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/victory_dance-417x320.png "Victory Dance!")](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/victory_dance.png)



你已经创建了第一个CocoaPod，但是你能真正的使用吗？并不能很快使用。



你首先需要添加你的Podspec去私有的specs repo，当你尝试去安装它这让CocoaPods发现pod。
对于这个你已经创建一个Git repo，所以这最后一步比较简单。

在终端输入以下命令，确保你仍在*RWPickFlavor*目录中。

```
pod repo add RWPodSpecs [Your RWPodSpecs Git URL]
pod repo push RWPodSpecs RWPickFlavor.podspec

```


这将创建一个本地引用 *RWPodSpecs*在你的机器上，并推送*RWPickFlavor.podspec*到它上。


你现在有一个私有的pod specs repo被建立！比你想象的容易，对吗？

使用你的新CocoaPod
-----------------------


最后来使用你创建的pod


对于*IceCreamShop*打开Podfile，输入以下命令

```
platform :ios, '8.0'
 
source 'https://github.com/CocoaPods/Specs.git'
source '[Your RWPodSpecs Git URL Goes Here]'
 
use_frameworks!
 
target 'IceCreamShop' do
  pod 'RWPickFlavor', :path => '~/Documents/Libraries/RWPickFlavor'
end

```



确保你替换的*[Your RWPodSpecs Git URL Goes Here]*Git URL是你的 *RWPodSpecs* repo



然后运行`pod install`在终端


最后，用下面的替换`AppDelegate.swift`。

```
import UIKit
import RWPickFlavor
 
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
 
  var window: UIWindow?
  var rootViewController: UIViewController!
 
  func application(application: UIApplication, didFinishLaunchingWithOptions 
    launchOptions: [NSObject : AnyObject]?) -> Bool {
 
    setupRootViewController()
 
    window = UIWindow(frame: UIScreen.mainScreen().bounds)
    window?.rootViewController = rootViewController
    window?.makeKeyAndVisible()
 
    return true
  }
 
  func setupRootViewController() {
    let bundle = NSBundle(forClass: PickFlavorViewController.self)
    let storyboard = UIStoryboard(name: "Main", bundle: bundle)
    rootViewController = storyboard.instantiateInitialViewController() as! UIViewController
  }
}

```



在In `setupRootViewController()`，你得到一个可参考的`RWPickFlavor`“bundle”，这实际上是一个动态绑定框架，创建一个`Main.storyboard`，并且实例化其根视图控制器。


构建并且运行。你将看到你熟悉的“Choose Your Flavor”界面。

![choose\_flavor](http://cdn4.raywenderlich.com/wp-content/uploads/2015/05/choose_flavor-180x320.png)

 抽象所有事物
---------------------------



如果你像我一样，你可能会想，“哇，App Delegate一定知道很多关于RWPickFlavor结构”



幸运的是，有一些事情你可以做来减少这种耦合性，使用[BetterBaseClasses](https://github.com/JRG-Developer/BetterBaseClasses)– a CocoaPod使其创建CocoaPods更容易


添加一下代码带你的*RWPickFlavor*，:

```
pod 'BetterBaseClasses', '~> 1.0'

```



同样添加到*RWPickFlavor.podspec*:

```
s.dependency 'BetterBaseClasses', '~> 1.0'

```



现在替换版本:

```
s.version = "0.2.0"

```


在这里，你声明*BetterBaseClasses*作为一个关系，然后加入你的CocoaPod



现在你在终端运行*pod install*安装这个新的依赖关系。



接下来添加提取`PickFlavorViewController`

```
import BetterBaseClasses

```


然后替换类的定义

```
public class PickFlavorViewController: BaseViewController, UICollectionViewDelegate {

```


这种`PickFlavorViewController`，所以它是`BaseViewController`是*BetterBaseClasses*一部分



现在你需要推送这些改变到你的*RWPickFlavor* and*RWPodSpecs*仓库。运行以下命令

```
cd ~/Documents/Libraries/RWPickFlavor
git add .
git commit -m "Added BetterBaseClasses dependency"
git tag 0.2.0
git push origin master --tags
pod repo push RWPodSpecs RWPickFlavor.podspec

```


接下来你需要提交这些改变到你的*IceCreamShop*.



更新*IceCreamShop* Podfile，替换`pod 'RWPickFlavor'，

```
pod 'RWPickFlavor', '~> 0.2.0

```



在这里更新Podfile请求的版本就是你刚刚推送的。



在终端执行`pod install`去更新这种依赖关系在*IceCreamShop*.



最后用以下内容替换*AppDelegate.swift*

```
import UIKit
import RWPickFlavor
 
@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
 
  var window: UIWindow?
 
  func application(application: UIApplication, didFinishLaunchingWithOptions 
    launchOptions: [NSObject : AnyObject]?) -> Bool {
 
    window = UIWindow(frame: UIScreen.mainScreen().bounds)
    window?.rootViewController = UINavigationController(rootViewController: 
      PickFlavorViewController.instanceFromStoryboard())
    window?.makeKeyAndVisible()
 
    return true
  }
}

```

 这很简单



[BetterBaseClasses](https://github.com/JRG-Developer/BetterBaseClasses)添加到
`UIViewController`，`UITableViewController`和其他的UIkit类。这里包括一个`UIViewController+BetterBaseClasses`，增加了更加方便的方法，尤其是`instanceFromStoryboard()`使它更容易实例化视图控制器。无论他们在main bundle活着其他一些地方，尤其是在框架中



构建并且运行，又一次，你应该看见了熟悉的界面“Choose Your Flavor”

   何去何从?
----------------------


你能在[这](http://cdn2.raywenderlich.com/wp-content/uploads/2015/06/IceCreamShop_Final2.zip)和[这](http://cdn4.raywenderlich.com/wp-content/uploads/2015/06/RWPickFlavor_Final.zip)完成IceCreamShop project和RWPickFlavor pod工程的下载,




你现在开始准备制作自己的CocoaPods！然而，本教程谈到的CocoaPods知识冰山一角。
学习[CocoaPods
Guides](http://guides.cocoapods.org/)去学习你需要了解的创建CocoaPods的知识。



最后创建一个CocoaPod，你可能考虑添加[CocoaPods Master Specs Repo]()，因此他将可能通过[CocoaPods
Trunk](http://blog.cocoapods.org/CocoaPods-Trunk)博客到世界各地的开发商手里，



关于本教程如果你有任何问题或评论，欢迎加入讨论


