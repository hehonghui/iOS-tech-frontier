
* 原文链接：[How to Create a CocoaPod in Swift](http://www.raywenderlich.com/99386/create-cocoapod-swift)
* 原文作者：[ Joshua Greene ](http://www.raywenderlich.com/u/JRG.Developer)
* [译文出自：开发者前线 www.devtf.cn](www.devtf.cn)
* 译者：[MrLoong](https://github.com/MrLoong)
* 校对者：
* 状态：

[![Learn how to make your own
CocoaPod!](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/ice_cream_shop_logo.png)](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/ice_cream_shop_logo.png)

Learn how to make your own CocoaPod in this ice-cream-delicious
tutorial!

在制作这个美味的冰淇凌中的教程中学会如何使用自己的CocoaPod

You’re probably familiar with some well-known, [open-source
CocoaPods](https://github.com/CocoaPods/Specs/tree/master/Specs) such as
[Alamofire](https://github.com/Alamofire/Alamofire) or
[MBProgressHUD](https://github.com/jdg/MBProgressHUD). But sometimes you
can’t find a a pod with the exact functionality you need, or you may
want to separate a large project into smaller, reusable components.

你可能熟悉一些众所周知的东西，开源的[open-source
CocoaPods](https://github.com/CocoaPods/Specs/tree/master/Specs)，例如[Alamofire](https://github.com/Alamofire/Alamofire)或[MBProgressHUD](https://github.com/jdg/MBProgressHUD)，但有的时候你并不能找到一个符合你要求的pod，或者你可能想要把一个大的项目分成小的项目，或是可复用组建。

Fortunately, it’s easy to create your own CocoaPods!

幸运的是创建自己的CocoaPods很容易。

If you’ve already created a Cocoa Touch framework for your component,
you’ve already done most of the hard work. If you haven’t, don’t panic
as it’s really straightforward.

如果你已经针对你的元件创建一个Cocoa Touch框架，那么你已经完成了大部分困难的工作。如果你没有创建过，不要惊慌，因为他确实很简单。

If you’ve only ever created classes as part of an iOS app, that’s okay
too. You can easily create a new pod by pulling out classes and
functionality that make sense for your particular use case.

如果你仅仅是创建了IOS应用的一部分，那也是ok的。你可以很轻松的创建一些新的pod用于特殊环境下使用的推送类和功能。

This tutorial picks up where [How to Use CocoaPods with
Swift](http://www.raywenderlich.com/97014) ends. If you’ve never used
CocoaPods before, then that tutorial is definitely a prerequisite to
this one.

本教程以[How to Use CocoaPods with
Swift](http://www.raywenderlich.com/97014)结束。如果你之前从来没有使用过CocoaPods，然而这是本教程的一先决条件。

Getting Started 开始
-------------------

Your top client is Ice Cream Shop, Inc. Their ice cream is so popular
they can’t keep up with customer orders at the counter. They’ve
recruited you to create a sleek iOS app that will allow customers to
order ice cream right from their phone. You’ve started developing the
app, and it’s coming along well.

你的顶级客户是一个冰淇凌店，他们的冰淇凌是如此的受欢迎，他们的柜台跟不上客户的订单。他们需要创建一个简洁的IOS应用。让用户使用他们的Phone订购冰淇凌。你将开始开发这个应用程序。

Download the starter project from
[here](http://cdn2.raywenderlich.com/wp-content/uploads/2015/06/IceCreamShop_Final1.zip)

从[这](http://cdn2.raywenderlich.com/wp-content/uploads/2015/06/IceCreamShop_Final1.zip)下载这个开始的项目

this is the final project from [How to Use CocoaPods with
Swift](http://www.raywenderlich.com/97014).

这是[How to Use CocoaPods with
Swift](http://www.raywenderlich.com/97014)最终版本

The app has a few pod dependencies that are already included in the
download, so you *don’t* need to run *pod install* to install them.

这个应用有一些pod开发包已经包含在下载中，所以你不需要运行pod install去安装他们。


Open *IceCreamShop.xcworkspace* and then *Main.storyboard*, found in the
*Views\\Storyboards & Nibs*group, to see how the app is laid out.

打开*IceCreamShop.xcworkspace*然后点击*Main.storyboard*，在*Views\\Storyboards & Nibs*看看这个应用是如何布局的。

Here’s a quick overview of the *Choose Your Flavor* scene, which is the
heart of the application:

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

The top managers at Ice Cream Shop really like how the app is coming
along, but they’ve added a new requirement: ice cream retailers need to
have the same choose-your-own-flavor functionality in their apps.

这个冰淇凌店的老板非常喜欢这个应用程序，但他们又添加了一个新的要求：在他们的应用程序中需要有相同的口味风格选择。

Wait, that wasn’t in the original design. But that should be no probelm
for a super developer like you!

等等，那不是最初的设计，但是，应该没有问题的对于一个NB的开发者来说。

[![SuperDev](http://cdn1.raywenderlich.com/wp-content/uploads/2014/11/SuperDev.jpg)](http://cdn1.raywenderlich.com/wp-content/uploads/2014/11/SuperDev.jpg)

Can you guess how you’re going to do this? Yep, you’re going to pull
this functionality into its own CocoaPod!

你能猜猜这个功能是怎么做的吗？是的，你讲把这个功能集成到你自己的CocoaPod！。

Setting up Your Pod 建立你自己的Pod
---------------------------------

Create a new Xcode project and select *iOS\\Framework & Library\\Cocoa
Touch Framework*, and then click *Next*:

创建一个新的Xcode项目并且选择*iOS\\Framework & Library\\Cocoa
Touch Framework*然后点击下一步。

[![Create Cocoa Touch
Framework](http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/cocoa_touch_framework-480x284.png "Create Cocoa Touch Framework")](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/cocoa_touch_framework.png)

Enter *RWPickFlavor* for the product name and *Swift* for the language.
Click *Next*.

将工程命名为*RWPickFlavor*并且选择*Swift*语言，点击*Next*

For the purposes of this tutorial you will create the project in
*\~/Documents/Libraries*. Choose the *Documents* folder in your home
directory. If you don’t already have a *Libraries* folder, click on the
*New Folder* button in the bottom and create it.

这个教程的工程将创建在*\~/Documents/Libraries*，在你的主目录中选择*Documents*文件夹。如果你没有*Libraries*文件夹，在底部点击新建文件夹按钮并且创建它。

Finally, choose the *Libraries* folder and click *Create*.

完成，选择*Libraries*文件夹并且单价创建

The directory where you save your development pods is important because
you will need to reference this directory from your Podfile during local
development.

保存你开发的pods这个目录非常重要，因为你将引用这个目录在你本地开发你的Podfile期间

Normally, when you *use* CocoaPods, you include dependencies in your
Podfile like this:

正常情况下，当你使用CocoaPods，你在你的Podfile包含的关系

 ``` 
 pod ' PodName ', '~> 1.0'                                                
 ```                                                                      

But when you’re developing *your own* CocoaPod, you will instead specify
a local path, like this:

 ``` 
pod ' MyPodName ', :path => ' ~/Path/To/Folder/Containing/My/Pod '  
 ```          
 
但是当你开发你自己的CocoaPod，你讲在本地建立一个特殊的路径，像这样：

There are two benefits to this approach:

这种方法有两种好处：

1.  It uses the local files on your machine for the pod, instead of
    fetching them from a remote repository.
    
    他使用你本地机器上的文件对于你的pod，而不是从远程仓库中获取。
    
2.  Normally, you don’t make changes to a pod included by your app
    because those changes would be overwritten the next time you run
    *pod install*, as the pod will be re-fetched from the remote
    repository and the source files you changed would be overwritten. By
    using the `:path =>` syntax, you can easily make changes to the pod
    during development that won’t be overwritten, since the specified
    path is now the *source* of the CocoaPod, and thus your changes
    won’t be lost when you run *pod install* again.
    
    正常情况下，你不需要选择一个pod包含到你的app中，因为这些选择将在你下一次运行pod install       时被覆盖，pod将会重新从远程仓库读取并且覆盖更改的文件。

While you can use a different location for your development pods, in
general I recommend putting them in *\~/Documents/Libraries*. This is
also a good location if you work with a team of developers since
CocoaPods knows to expand the tilde as the user’s home directory;
therefore you don’t need to hard code an absolute path in the Podfile to
this directory.

你可以使用不同的位置对你的pods进行开发，一般情况下我建议还是将他们放在*\~/Documents/Libraries*。如果你和一个团队合作开发，CocoaPods扩展作为扩展主目录。因此你不需要努力构建绝对路径在Podfile目录中。
，
You can also use other CocoaPods within your project as dependencies for
the CocoaPod you created — you simply need a Podfile to manage your
CocoaPod’s dependencies.

你也能使用其他的CocoaPods在你的工程中作为项目的依赖对于你创建CocoaPod。你只需要一个Podfile来管理你的CocoaPod依赖关系。

Close Xcode, then enter the following commands in Terminal:

关闭Xcode，然后在终端输入以下命令


 ```                              
 cd ~/Documents/Libraries/RWPickFlavor                                    
 pod init                                                                 
 open -a Xcode Podfile
                                                     
 ```                                                                     
 
This creates a new Podfile and opens it in Xcode.

这是创建一个新的Podfile并且在Xcode中打开它。

Replace the entire contents of the new Podfile with the following:

使用下面的命令对Podfile进行更新：

```                               
 platform :ios, '8.0'                                                     
 use_frameworks!                                                         
                                                                          
 target 'RWPickFlavor' do                                                 
   pod 'Alamofire', '~> 1.2'                                              
   pod 'MBProgressHUD', '~> 0.9.0'                                        
 end                                                                      
```                                                                      
This declares that *RWPickFlavor* will have external dependencies on
both [Alamofire](https://github.com/Alamofire/Alamofire) and
[MBProgressHUD](https://github.com/jdg/MBProgressHUD).

这声明*RWPickFlavor*将在[Alamofire](https://github.com/Alamofire/Alamofire) 和
[MBProgressHUD](https://github.com/jdg/MBProgressHUD)提供依赖关系

Save and close the Podfile, then enter the following command in
Terminal:

保存并且关闭Podfile，然后在终端输入以下命令

```                              
pod install
```                                                                      
 
Just as you’d expect, this creates a workspace and installs the various
requisite files.

正如你所期望的，这将创建一个工作区间并且安装你所需要的各种文件。

*Note*: If *pod install* gives you any warnings, then you’re likely
using an older version of CocoaPods. Swift based CocoaPods, such as
Alamofire, require CocoaPods version 0.36.0 or newer. You can check the
version of CocoaPods you have installed by running the following command
in Terminal:

注意：如果*pod install* 给你任何警告，这样的话你可能使用的是旧版本。Swift基于CocoaPods，尤其是Alamofire，需要 0.36 CocoaPods版本或更新。根据以下命令，你可以检测你已经安装的CocoaPods版本。

```
pod --version

```

If that’s the case, enter the following in Terminal to install the
latest version of CocoaPods:

如果是版本过低的原因，输入以下终端命令来安装最新的版本。

```
sudo gem install CocoaPods

```

Enter the following command in Terminal to open the newly created
*RWPickFlavor* workspace:

输入以下命令，打开并且创建一个新的*RWPickFlavor*工作区间。

```
open RWPickflavor.xcworkspace
```

Your project navigator should now look like the following:

你的项目导航器看起来应该是这个样子。

[![RWPickFlavor New Pod Files
Hierarchy](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/rwpickflavor_new_pod_files-255x320.png "RWPickFlavor New Pod Files Hierarchy")](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/rwpickflavor_new_pod_files.png)

You now need to copy a few of the existing files from the *IceCreamShop*
workspace into *RWPickFlavor*.

你现在需要从*IceCreamShop*复制一些已经存在的文件在*RWPickFlavor*工作区间。

First, create the following groups in *RWPickFlavor.xcworkspace* to hold
the files you’re going to copy:

首先在*RWPickFlavor.xcworkspace*创建一个组，把你需要的文件复制进去。

Categories

Controllers

Factories

Models

Views

-   Ice Cream
-   Storyboards & Nibs

Now drag and drop everything — *except* for *AppDelegate.swift* and
*LaunchScreen.xib* — from the above groups in *IceCreamShop.xcworkspace*
into the corresponding groups in *RWPickFlavor.xcworkspace* like so:

现在拖拽每一个文件，除了 *AppDelegate.swift*和*LaunchScreen.xib*，从*IceCreamShop.xcworkspace*拖拽进*RWPickFlavor.xcworkspace*，就像这样

[![Copy Workspace
Files](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/copy_workspace_files.gif)](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/copy_workspace_files.gif)

When prompted, ensure that *Copy items if needed* is checked so that
each file will actually be copied instead of simply linked.

当被提示时，确保每一个需要被复制的条目被复制安装了而不是简单的链接。

When you’re done, *RWPickFlavor* should have the following files:

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

Now open *Info.plist*, found under the *Supporting Files* group, and
delete the line for *Main storyboard file base name*.

现在打开*Info.plist*，找到*Supporting Files*组，并且删除*Main storyboard file base name*

Build and run. You shouldn’t see any errors, and you’ll eventually see
the “Ice Cream Shop” logo followed by a black screen.

构建并运行，你不应该看见任何错误，并且你会看见“Ice Cream Shop”的图标和黑色的背景。

What About the Images?图像呢？
----------------------

You may have noticed that you didn’t copy over the *Resources* group;
this is because you need to copy just *background.jpg* itself into the
*Resources* folder of `RWPickFlavor` — not the complete
*Images.xcassets* file.

你可能会注意，你并没有复制*Resources*组，这是因为你需要复制的仅仅是*background.jpg*他自己本身到*Resources*文件夹中。不完整的*Images.xcassets*资源文件。

First, create a new *Resources* group within *RWPickFlavor*.

首先在*RWPickFlavor*创建一个*Resources*组

Next, select *Images.xcassets* in *IceCreamShop*, right click on
*background* and select *Show In Finder*, like so:

下一步，选择*Images.xcassets*在*IceCreamShop*，点击右侧*background*，并且选择*Show In Finder*，像这样。

[![Background Show In
Finder](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/background_show_in_finder-480x267.png "Background Show In Finder")](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/background_show_in_finder.png)

Now drag and drop *background.jpg* from *Finder* into the *Resources*
group in *RWPickFlavor*. Again, make sure that you check *Copy items if
needed* when prompted. After you’ve copied the image, delete the
original *background* from *Images.xcassets* in *IceCreamShop*.

现在拖拽*background.jpg*到*RWPickFlavor*的 *Resources*组中。当提示的时候依然检测每一个条目是否被安装。当你复制后，从*IceCreamShop*中删除*Images.xcassets*原来的背景

Finally, in *RWPickFlavor* update the image view’s image on the *Choose
Your Flavor* scene in *Main.storyboard* so that it refers to
*background.jpg* instead of simply *background*:

最后，在*RWPickFlavor*更新视图的图像上选择你的场景在*Main.storyboard*，所以引用*background.jpg*替代*background*

[![Update PickFlavorViewController Image View
Image](http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/update_pickflavorviewcontroller_imageview-411x320.png "Update PickFlavorViewController Image View Image")](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/update_pickflavorviewcontroller_imageview.png)

Believe it or not, the hardest part of creating your pod is done! 

信不信，最难创造的是你的pod。

[![High
Five!](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/high_five_kitten-349x500.jpg)](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/high_five_kitten.jpg)

CocoaPods and Git 
-----------------

Since CocoaPods is bootstrapped on top of Git, each pod will need to
have its own Git repository. If you already have a preferred Git host,
great — you can use it to host your repository.

自从CocoaPods傻瓜式引导，每一个pod将需要有他自己的git仓库。如果你已经有一个首选的git托管，好的，你可以用它来作为你的仓库。

If you don’t, [GitHub](https://github.com) is an excellent choice as
it’s well-known by developers and has a free plan for open-source
projects.

如果没有，[GitHub](https://github.com)是一个很好的的选择因为它是众所周知的开发平台，与很多自由的开源项目。

This tutorial uses [GitHub](https://github.com), but feel free to use
your preferred Git host instead.

这个教程使用[GitHub](https://github.com)，但你也可以使用你的git仓库。

GitHub Repo Setup GitHub设置
-----------------

First, [Sign up](https://github.com/join) or
[Login](https://github.com/login) to your GitHub account.

首先[创建](https://github.com/join)或[登陆](https://github.com/login)你的GitHub账户。

Next, click on the *+* (create new) icon on the top right of the screen
and select *New repository* as shown below:

下一步，点击顶部右侧*+* (create new)图标并且选择 New repository展示如下：

 [![Github: New
Repository](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/github_new_repository-480x152.png "Github: New Repository")](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/github_new_repository.png)

Enter *RWPickFlavor* for the *Repository name*, and select *Create
repository*.

进入*RWPickFlavor*仓库，并且选择*Create repository*

Github will create a new repository under your account; you’ll then see
the following screen with a *Quick setup* section that displays your
repository URL:

Github将创建一个新的仓库在你的账户下。然后你会看到下面的屏幕与一个快速设置，现实您储存的仓库网址。

[![Github Quick
Setup](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/github_quick_setup-480x56.png "Github Quick Setup")](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/github_quick_setup.png)

You’ll need this URL in just a moment, so leave the page open for now.

所以距离打开到现在你需要这个URL在短短的一瞬间。

Now you need a second repository to host all of your private pod specs —
you’ll use this later on in the tutorial.

现在你需要第二个仓库涌来存放你所有私有的pod，你将在本教程中使用。

Open [github.com](http://github.com) in a new tab; again, press the
*Create new* icon and select *New repository*. Name this repository
*RWPodSpecs*, and select *Create repository*.

打开[github.com](http://github.com)在你个新的栏目。再一次点击*Create new*图标，并且选择*New repository*。仓库名为*RWPodSpecs*，并且选择*Create repository*

Leave this tab open as well so you can easily grab the URL later when
you need it.

离开这个标签，这样当你需要使用它的时候很简单去使用了。

Podspec Setup Podspec 设置
-------------

Now you need to create the *RWPickFlavor.podspec* file for
*RWPickFlavor*. The *Podspec* includes basic information such as the
pod’s name, version and Git download URL.

现在你需要创建*RWPickFlavor.podspec*文件对于*RWPickFlavor*，*Podspec*包含基础信息，尤其是pod的名字，版本和Git下载的URL

Enter the following commands in Terminal, hitting enter after each one:

在终端输入以下命令。

```
cd ~/Documents/Libraries/RWPickFlavor
pod spec create RWPickFlavor
open -a Xcode RWPickFlavor.podspec

```

This creates *RWPickFlavor.podspec* and opens it in Xcode.

这是创建的*RWPickFlavor.podspec*并且打开它在Xcode

There’s a lot of excellent documentation and examples in the default
Podspec – however, you don’t need most of it.

默认情况下有很多优秀的例子和文档，然而你并不需要大部分的他们。

Replace everything in *RWPickFlavor.podspec* with the following:

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

Just like a Podfile, the Podspec is written in Ruby. Be extra careful
not to make any typos, or else the pod will likely fail to validate
and/or install later.

就像一个Podfile，Podspec是用Ruby写的。要格外小心，不要做任何错误，否则将可能无法验证安装后是否成功.

Here’s what’s going on:

这是发生了什么？

1.  You first specify basic information about the pod. Swift based
    CocoaPods *must* have a minimum deployment target of 8.0 or higher.
    If you specify a lower version, the pod won’t install correctly.
    
    首先你详细说明了pod的基本信息。Swift必须最低基于CocoaPods8.0版本或者更高。
    如果你指定了较低的版本，pod无法正常安装。
    
2.  A Podspec is essentially a snapshot in time of your CocoaPod as
    denoted by a version number. When you update a pod, you will also
    need to update the Podspec’s version. All CocoaPods are highly
    encouraged to follow [Semantic Versioning](http://semver.org/). If
    you’re not familiar with Semantic Versioning, see [How to Use
    CocoaPods with Swift](http://www.raywenderlich.com/97014) for more
    information.
    
    对于你指定的CocoaPod版本好，本质上Podspec是一个快照。当你更新一个pod，你也将需要更
    新Podspec的版本。所有的高版本度遵循[Semantic Versioning](http://semver.org/)。
    如果你并没有相似的版本，看[如何使用CocoaPods在Swift中]
    (http://www.raywenderlich.com/97014)
    
3.  All pods must specify a license. If you don’t, CocoaPods will
    present a warning when you try to install the pod, and you won’t be
    able to upload it to CocoaPods
    [trunk](http://blog.cocoapods.org/CocoaPods-Trunk/) – the master
    specs repo.
    
    所有pods必须指定一个许可证。如果你没有，CocoaPods将在你安装的时候提出警告。并且你
    不能把它上传到分支。
    
4.  Here you specify information about yourself, the pod author. Enter
    your name and e-mail address instead of the placeholder text.
    
    在这里，您可以指定关于自己的信息，pod作者。输入你的名字和e-mail地址。替代那些占
    位符文本
    
5.  Here you need to specify the URL for your pod’s homepage. It’s okay
    to simply copy and paste the Github homepage from your browser’s
    address bar to use here.
    
    在这里你需要指定你的pod的网页和网址。就是简单的从浏览器地址栏复制和粘贴到Github
    主页
    
6.  Replace this URL with the Git download URL from the “Quick Setup”
    section of the first repo you created above. In general, it’s best
    to use either a *http:* or *https:* URL to make it easier for other
    users to consume. You can use an SSH URL if you want, but you’ll
    need to make sure that everyone on your team — and whoever else
    needs access to the CocoaPod — already has their public/private key
    pairs setup with your Git host.
    
    从上面创建第一个“Quick Setup”，那部分的git下载网址替代URL。最好使用一个HTTP或
    HTTPS：URL更容易被其他用户处理。如果你想你也可以使用SSH URL，但是你需要确保你团
    队的每一个人－无论谁需要访问CocoaPod－已经有自己的公钥／私钥针对你的Git host
    
7.  Here you specify the framework and any pod dependencies.

	 这里你需要说明你的框架和pod依赖关系。
	
8.  Here you specify the *public source files* based on file extensions;
    in this case, you specify `.swift` as the extension.
    
    在这里你可以说明指定基于文件扩展的公共源文件，在这种情况下，你可以说明‘.swift’作
    为扩展
    
9.  Finally, you specify the *resources* based on their file extensions.
     
    最后你可以根据指定的文件扩展名指定资源。
    

Like every other pod, you’ll need to create the *LICENSE* file.

Copy and paste the MIT License found
[here](http://choosealicense.com/licenses/mit/) into your favorite text
editor, and then save the file as *LICENSE* – with no extension – in
*\~/Documents/Libraries/RWPickFlavor*. Make sure to replace *[year]* and
*[fullname]* with the actual values- uh, the year and your actual name,
of course. ;]

[Choose a License](http://choosealicense.com) is a really great site
that helps you choose the most appropriate open-source license for your
project, and is built and maintained by the good folks at GitHub.

Push to Git  推送Git
-----------

You’re finally ready to push the *RWPickFlavor* pod to its new home on
GitHub!

你终于准备推送*RWPickFlavor* pod GitHub主页

Enter the following commands in Terminal

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

If prompted, enter your username and password for Github.

然后跳出提示，输入你的用户名和密码到Github

This commits all of the files within the *RWPickFlavor* directory,
creates a new *0.1.0* tag, and pushes eveything to your remote
repository.

这所有文件在*RWPickFlavor*目录中，创建一个新的*0.1.0*标签，并且推送你每一个东西到你的远程仓库

Congratulations, you’ve just created your first CocoaPod!

祝贺你，你已经创建你的第一个CocoaPod！

[![Victory
Dance!](http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/victory_dance-417x320.png "Victory Dance!")](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/victory_dance.png)

You’ve created your first CocoaPod, but can you actually use it? Well,
not quite yet. 

你已经创建了第一个CocoaPod，但是你能真正的使用吗？并不能很快使用。

You first need to add your Podspec to a private specs repo; this lets
CocoaPods find the pod when you try to install it. Fortunately, you’ve
already created a Git repo for this, so this final step is relatively
straightforward.

你首先需要添加你的Podspec去私有的specs repo，当你尝试去安装它这让CocoaPods发现pod。
对于这个你已经创建一个Git repo，所以这最后一步比较简单。

Enter the following in Terminal, making sure you’re still in the
*RWPickFlavor* directory:

在终端输入以下命令，确保你仍在*RWPickFlavor*目录中。

```
pod repo add RWPodSpecs [Your RWPodSpecs Git URL]
pod repo push RWPodSpecs RWPickFlavor.podspec

```

This creates a local reference to *RWPodSpecs* that’s stored in
*\~/.cocoapods* on your machine, and pushes the *RWPickFlavor.podspec*
to it.

这将创建一个本地引用 *RWPodSpecs*在你的机器上，并推送*RWPickFlavor.podspec*到它上。

You now have a private pod specs repo set up! Easier than you thought,
right?

你现在有一个私有的pod specs repo被建立！比你想象的容易，对吗？

Using Your New CocoaPod 使用你的新CocoaPod
-----------------------

It’s finally time to use your newly created pod.

最后来使用你创建的pod

Open the Podfile for *IceCreamShop* and replace its contents with the
following:

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

Make sure that you replace *[Your RWPodSpecs Git URL Goes Here]* with
the Git URL for your *RWPodSpecs* repo.

确保你替换的*[Your RWPodSpecs Git URL Goes Here]*Git URL是你的 *RWPodSpecs* repo

Then, run `pod install` in Terminal.

然后运行`pod install`在终端

Finally, replace the entire contents of `AppDelegate.swift` with the
following:

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

In `setupRootViewController()`, you get a reference to the
`RWPickFlavor` “bundle” – which is actually a dynamic framework – create
a reference to the `Main.storyboard`, and instantiate its root view
controller.

在In `setupRootViewController()`，你得到一个可参考的`RWPickFlavor`“bundle”，这实际上是一个动态绑定框架，创建一个`Main.storyboard`，并且实例化其根视图控制器。

Build and run. You’ll be greeted with the familiar “Choose Your Flavor”
screen. Awesome! 

构建并且运行。你将看到你熟悉的“Choose Your Flavor”界面。

![choose\_flavor](http://cdn4.raywenderlich.com/wp-content/uploads/2015/05/choose_flavor-180x320.png)

Abstracting All The Things! 抽象所有事物
---------------------------

If you’re like me, you’re probably thinking, “Wow, App Delegate sure
knows a lot about the structure of RWPickFlavor!”

如果你像我一样，你可能会想，“哇，App Delegate一定知道很多关于RWPickFlavor结构”

Fortunately, there’s something you can do to reduce this tight-coupling:
use[BetterBaseClasses](https://github.com/JRG-Developer/BetterBaseClasses)
– a CocoaPod that makes creating other CocoaPods easier.

幸运的是，有一些事情你可以做来减少这种耦合性，使用[BetterBaseClasses](https://github.com/JRG-Developer/BetterBaseClasses)– a CocoaPod使其创建CocoaPods更容易

Add the following code to the pod file for *RWPickFlavor*
添加一下代码带你的*RWPickFlavor*，

```
pod 'BetterBaseClasses', '~> 1.0'

```

Likewise, add the following to *RWPickFlavor.podspec*, right below the
line for *Alamofire*:

同样添加到*RWPickFlavor.podspec*

```
s.dependency 'BetterBaseClasses', '~> 1.0'

```

Now replace the *s.version* line with the following:

现在替换版本

```
s.version = "0.2.0"

```

Here you’re declaring *BetterBaseClasses* as a dependency, and then
bumping the version of your CocoaPod.

在这里，你声明*BetterBaseClasses*作为一个关系，然后加入你的CocoaPod

Now run *pod install* in Terminal to install this new dependency.

现在你在终端运行*pod install*安装这个新的依赖关系。

Next, add the following import to `PickFlavorViewController`, again
right after the line for *Alamofire*:

接下来添加提取`PickFlavorViewController`

```
import BetterBaseClasses

```

Then replace the class definition with the following:

然后替换类的定义

```
public class PickFlavorViewController: BaseViewController, UICollectionViewDelegate {

```
This changes `PickFlavorViewController` so it inherits from
`BaseViewController`, which is part of *BetterBaseClasses*.

这种`PickFlavorViewController`，所以它是`BaseViewController`是*BetterBaseClasses*一部分

Now you need to push these changes to both your *RWPickFlavor* and
*RWPodSpecs* repositories. Run the following commands in Terminal:

现在你需要推送这些改变到你的*RWPickFlavor* and*RWPodSpecs*仓库。运行以下命令

```
cd ~/Documents/Libraries/RWPickFlavor
git add .
git commit -m "Added BetterBaseClasses dependency"
git tag 0.2.0
git push origin master --tags
pod repo push RWPodSpecs RWPickFlavor.podspec

```
Next, you need to pull this change into *IceCreamShop*.

接下来你需要提交这些改变到你的*IceCreamShop*.

Update the Podfile for *IceCreamShop* by replacing the
`pod 'RWPickFlavor'` line with the following:

更新*IceCreamShop* Podfile，替换`pod 'RWPickFlavor'，

```
pod 'RWPickFlavor', '~> 0.2.0

```

Here you’re updating the Podfile to request the version of
*RWPickFlavor* you just pushed.

在这里更新Podfile请求的版本就是你刚刚推送的。

Then execute `pod install` in Terminal to update this dependency in
*IceCreamShop*.

在终端执行`pod install`去更新这种依赖关系在*IceCreamShop*.

Lastly, replace the entire contents of *AppDelegate.swift* with the
following:

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

That’s much simpler! 这很简单

[BetterBaseClasses](https://github.com/JRG-Developer/BetterBaseClasses)
adds categories to `UIViewController`, `UITableViewController`, and
other UIkit classes. This includes a category called
`UIViewController+BetterBaseClasses`, which adds convenience methods
such as `instanceFromStoryboard()` to make it super easy to instantiate
view controllers, whether they live in the main bundle or somewhere
else, such as a framework in this case.

[BetterBaseClasses](https://github.com/JRG-Developer/BetterBaseClasses)添加到
`UIViewController`，`UITableViewController`和其他的UIkit类。这里包括一个`UIViewController+BetterBaseClasses`，增加了更加方便的方法，尤其是`instanceFromStoryboard()`使它更容易实例化视图控制器。无论他们在main bundle活着其他一些地方，尤其是在框架中

Build and run. Once again, you should be greeted with the familiar
“Choose Your Flavor” screen.

构建并且运行，又一次，你应该看见了熟悉的界面“Choose Your Flavor”

Where to Go From Here?   何去何从
----------------------

You can download the completed IceCreamShop project
[here](http://cdn2.raywenderlich.com/wp-content/uploads/2015/06/IceCreamShop_Final2.zip),
and the RWPickFlavor pod
[here](http://cdn4.raywenderlich.com/wp-content/uploads/2015/06/RWPickFlavor_Final.zip).

你能在[这](http://cdn2.raywenderlich.com/wp-content/uploads/2015/06/IceCreamShop_Final2.zip)和[这](http://cdn4.raywenderlich.com/wp-content/uploads/2015/06/RWPickFlavor_Final.zip)完成IceCreamShop project和RWPickFlavor pod工程的下载,


You’re now ready to start making your own CocoaPods! However, what
you’ve covered in this tutorial is really just the tip of the iceberg
when it comes to CocoaPods. Check out the [CocoaPods
Guides](http://guides.cocoapods.org/) to learn everything you need to
know about creating CocoaPods.

你现在开始准备制作自己的CocoaPods！然而，本教程谈到的CocoaPods知识冰山一角。
学习[CocoaPods
Guides](http://guides.cocoapods.org/)去学习你需要了解的创建CocoaPods的知识。

After you create a CocoaPod, you might consider adding it to the
[CocoaPods Master Specs Repo]() so that it will be made available via
[CocoaPods.org](http://cocoapods.org) to thousands of developers around
the world. 

最后创建一个CocoaPod，你可能考虑添加[CocoaPods Master Specs Repo]()，因此他将可能通过[CocoaPods
Trunk](http://blog.cocoapods.org/CocoaPods-Trunk)博客到世界各地的开发商手里，

If you have any questions or comments about the tutorial, feel free to
join the discussion in the comments below!

关于本教程如果你有任何问题或评论，欢迎加入讨论


