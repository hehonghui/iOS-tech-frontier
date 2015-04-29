`后台模式教程:开始`
---

>* 原文链接 : [Background Modes Tutorial: Getting Started](http://www.raywenderlich.com/92428/background-modes-ios-swift-tutorial)
* 原文作者 : [ Ray Fix ](http://www.raywenderlich.com/u/rayfix)
* 译者 : [MollyMmm](https://github.com/MollyMmm) 
* 校对者:  
* 状态 :  已完成


Update note: This tutorial was updated to iOS 8 and Swift by Ray Fix. Original post by Gustavo Ambrozio.
更新说明:这个教程被Ray Fix更新为关于iOS和Swift的.原著作者为[Gustavo Ambrozio](http://www.raywenderlich.com/29948/backgrounding-for-ios).

Since the ancient days of iOS 4, you can design your apps to stay suspended in memory when users push the home button. Even though the app is in memory, its operations are paused until the user starts it again. Or are they?
自从古老的iOS4以来,当用户点击home建的时候,你可以使你的APP们在内存中处于suspended(挂起)状态.即使APP仍停留在内存中,它(或它们)的所有操作是被暂停的直到用户再次运行它(或它们).
Of course there are exceptions to this rule! In specific situations, the app can still run some operations in the background. This tutorial will teach you how and when to use some of the most common background operations.
当然这个规则中有例外情况.在特定的情况下,这个APP仍然可以在后台中执行某些操作.
There are important restrictions on the use of background operations and the specifics have evolved with each release of iOS in an effort to improve user experience and extend battery life. Background modes are not a magic solution for getting “real” multitasking on iOS. Most apps will still get completely suspended when the user switches to another app. Your app is only allowed to keep running in the background in very specific cases. For example, these include playing audio, getting location updates, or fetching the latest content from a server.
后台操作和细节上的重要限制已经与每一次iOS的发布都努力提高用户体验,延长电池寿命的同时一起发展.对于在iOS中实现"真正"的多任务来说,后台模式不是一个神奇的解决办法.当用户切换到其他的APP应用时,大多数的APP应用仍然会完全的暂停运行.你的应用只被允许在很特殊的情况下才能在后台中继续运行.例如,这些包括播放音频,获取位置更新,或者从服务器获取最新内容的情况.
Previous to iOS 7, apps were given up to 10 continuous minutes to finish whatever they were doing before being truly suspended. With the advent of NSURLSession there is a much more elegant way to handle large network transfers. Because of this, the available background time for arbitrary computation has been reduced to just a few minutes and is no longer necessarily continuous.
iOS7之前,APP应用在真正暂停之前被给予了连续10分钟去完成它们当前的操作.随着NSURLSession的出现,有了一种更为优雅的方式去应对巨大的网络切换.因此,对于任意计算的可用后台运行时间已经减少到只有几分钟,而且不再必须为连续的.
So backgrounding may not be for you. But if it is, keep reading!
这样的后台模式可能不适合你.但如果合适,请继续阅读!
As you’ll soon learn, there are several background modes available to you in iOS. The project you’ll build in this tutorial is a simple tabbed application that explores four of the most common modes – from continuously playing audio to periodically fetching updated content.
接下来的学习中,将会有几个几个后台模式提供给你.在本教程中你将建立一个关于简单标签应用的工程,来探索从连续播放视频到周期性的获取更新内容的四种常见模式.
##Getting Started
##开始
Before digging into the project, here’s a quick overview of the basic background modes available in iOS. In Xcode 6, you can see the list by pulling up the Capabilities tab of your app target. It looks like this:
在挖掘这个工程之前,这里有一个iOS可用的基础后台模式的快速预览.在Xcode 6中,你通过点击目标程序的Capabilities(功能)选项卡能够看到如下列表:
-----------------图片-------------
To get to the background modes capability list you (1) select the project from the Project Navigator, (2) select the app target, (3) select the Capabilities tab, and (4) turn the Background Modes switch on.
打开后台模式功能列表(1)在项目导航栏中选择项目(2)选择目标应用(3)选择功能选项卡(4)把后台模式开关打开.
In this tutorial you will investigate four ways of doing background processing.
在这个教程中,你会研究四种后台进程处理方式.
Play audio: the app can continue playing and/or recording audio in the background.
视频播放:APP可以在后台播放或录制视频
Receive location updates: the app can continue to get callbacks as the device’s location changes.
获取位置更新:该应用会随着设备位置的改变继续回调结果.
Perform finite-length tasks: the generic “whatever” case, where the app can run arbitrary code for a limited amount of time.
执行一定的任务:通常在没有限制的情况下,这时APP会在有限的时间内运行任意的代码.
Background Fetch: Get updates to the latest content scheduled by iOS.
后台获取:通过iOS的更新计划获取最细的内容.
This tutorial will cover how to use these four modes in the above order, one for each section of the tutorial. If you are only interested in one or several of the modes, feel free to skip ahead!
这个教程将按照上面的顺序,在本教程的每个部分中介绍如何使用这四个模板.
To get started with the project that will be your tour bus through backgrounding, first download the starter project. Here’s some sweet news: the user interface has been pre-configured for you.
从这个像观光车一样的工程开始，通过它熟悉一下iOS后台机制，首先下载这个上手工程.有个好消息：用户界面已经为你预配置好了.
-----------------图片-------------
Run the sample project and check out your four tabs:
运行这个示例项目,检查一下你的四个选项卡.
-----------------图片-------------
These tabs are your road map for the rest of the tutorial. First stop… background audio.
这些选项卡是本教程剩余部分的路线图.第一站:后台视频
Note: For the full effect, you should follow along on a real device. In my experience, if you forget a configuration setting, the app might appear to run fine in the background on the Simulator. However, when you switch to a real device, it doesn’t work at all.
提示:为了使后台模式充分发挥作用,你应该使用一个真正的设备.根据我的经验,如果你忘记配置设置,该APP在模拟器的后台能很好的运行运行.然而,当你切换到真正的设备时,它将不会运行.
##Playing Audio
##音频播放
There are several ways to play audio on iOS and most of them require implementing callbacks to provide more audio data to play. A callback is when iOS asks your app to do something (like a delegate method), in this case filling up a memory buffer with an audio waveform.
这里有iOS播放音频的几种方法,他们中的大部分需要实现回调函数去提供更多用来播放的音频数据.当用户使你的APP做某些事情,会调用回调函数(比如委托模型),在这种情况下,会把音波存储在内存缓存区中.
If you want to play audio from streaming data, you can start a network connection, and the connection callbacks provide continuous audio data.
如果你想播放流数据中的音频,你可以开启一个网络连接,连接的这些回调函数提供连续的音频数据.
When you activate the audio background mode, iOS will continue these callbacks even if your app is not the current active app. That’s right – the audio background mode is (almost) automatic. You just have to activate it and provide the infrastructure to handle it appropriately.
当你激活音频后台模式后,即使你的APP现在没在活动,iOS将继续执行这些回调函数.音频后台模式是自动的,这么说很正确.你只是激活它,恰好为管理它提供了基础设备.
For those of us who are a bit sneaky-minded, you should only use the background audio mode if your app really does play audio to the user. If you try to use this mode just to get CPU time while playing silence in the background, Apple will reject your app!
对于我们这些有点小心思的人来说,如果你的APP确实为用户播放音频,你应该只使用后台音频模式.如果你尝试使用这个模式只是为了获取当程序安静运行的时候使用CPU的时长,苹果将拒绝你APP的运行.
-----------------图片-------------
In this section, you’ll add an audio player to your app, turn on the background mode and demonstrate to yourself that it’s working.
在这部分,你将在你的APP中添加一个音频播放器,打开后台模式,为你演示它的运行过程.
To get audio playback, you’ll need to lean on AV Foundation. Open AudioViewController.swift and add the import to the top of the file right after import UIKit:
为了获取到音频播放装置,你需要学习 AV Foundation.打开AudioViewController.swift,在文件顶部import UIKit后面添加引用.
import AVFoundation

Override viewDidLoad() with the following implementation:
用下面的实现代码重写viewDidLoad()
override func viewDidLoad() {
super.viewDidLoad()

var error: NSError?
var success = AVAudioSession.sharedInstance().setCategory(
AVAudioSessionCategoryPlayAndRecord,
withOptions: .DefaultToSpeaker, error: &error)
if !success {
NSLog("Failed to set audio session category.  Error: \(error)")
}
}

This uses the audio session singleton sharedInstance() to set the category of playback and ensure sound goes through the speaker rather than the phone earpiece. It checks whether the call fails and logs the error if it ever does. An actual app might want to present an error dialog in response to a failure, but no need to get bogged down in details here.
这使用了音频回话的单例模式sharedInstance()去设置播放的类别,也确保了声音是通过手机扬声器而不是通过手机听筒传播的.如果它执行了,他会检查调用是否失败并记录错误.一个真正的APP在发生错误后会显示一个队伍的对话框,作为对错误的回应,但是我们不需要因为这些小细节而纠结.
Next you want to add a player property method to AudioViewController:
接下来,你要把播放器这个成员属性添加到AudioViewController中:
var player: AVQueuePlayer!

This is an implicitly unwrapped optional so it starts off as nil and you will initialize it in viewDidLoad().
这是个隐式的可拓展的属性,最初为nil,你将在viewDidLoad()对它进行初始化.
The starter project includes audio files from incompetech.com, a favorite royalty-free music website. With credit given, you can use the music for free. So, here you go: all songs are by Kevin MacLeod at incompetech.com. Thanks, Kevin!
这个上手项目包含来自主要收纳免版权税的音乐网站incompetech.com的音频文件.认证之后你可以免费的使用它上面的音乐.你这里使用的全部歌曲来自incompetech.com 上Kevin MacLeod的作品.谢谢Kevin!
Going back to viewDidLoad() add the following to the end of the method:
返回viewDidLoad(),在此函数的末尾处添加如下方法:
let songNames = ["FeelinGood", "IronBacon", "WhatYouWant"]
let songs = songNames.map {
AVPlayerItem(URL: NSBundle.mainBundle().URLForResource($0, withExtension: "mp3"))
}

player = AVQueuePlayer(items: songs)
player.actionAtItemEnd = .Advance

This takes the list of songs and maps them to URLs in the main bundle and turns them into AVPlayerItems that can then be put into a AVQueuePlayer. In addition, the queue is set up to play the next item when one item ends.
这样可以获取到歌曲的列表,把它们映射到主程序包的路径中并把它们转化为可以在AVQueuePlayer上播放的AVPlayerItems.此外,这个队列被设置为循环播放.
In order to update the song name as the queue progresses, you need to observe currentItem of the player. To do this, add the following to the end of viewDidLoad()
为了在队列进程中更新歌曲名字,你需要观察播放器中的currentItem.为了达到上述目的,需要在viewDidLoad()的末尾处添加如下代码:
player.addObserver(self, forKeyPath: "currentItem", options: .New | .Initial , context: nil)

This makes it so that the class’s observer callback is called on initialization and whenever the player’s currentItem changes.
这使得每当播放器中currentItem改变,类观察者的回调被初始化.
Now you can add the observer method. Put it below viewDidLoad().
现在你可以添加观察者模式方法.把下面代码放到viewDidLoad()下面.
override func observeValueForKeyPath(keyPath: String, ofObject object: AnyObject, change: [NSObject : AnyObject], context: UnsafeMutablePointer<Void>) {
if keyPath == "currentItem", let player = object as? AVPlayer,
currentItem = player.currentItem?.asset as? AVURLAsset {
songLabel.text = currentItem.URL?.lastPathComponent ?? "Unknown"
}
}

When the method gets called, you first make sure the property being updated is the one in which you’re interested. In this case, it’s not actually necessary because there’s only one property being observed, but it’s good practice to check in case you add more observers later. If it’s the currentItem key, then you use it to update songLabel with the file name. If for some reason the current item’s URL is not available, it falls back to the string “Unknown”.
当这个函数被调用的时候,你首先要确保这个被更新的属性是你所关注的.在这种情形下,它不是那么重要了因为只有一个属性被观察,但是在你之后添加更多的观察者的情况下去检查,是个不错的方法.如果它是currentItem键,你将使用它通过文件名更新songLabel.如果由于某些原因,当前项的URL不能获取到,它将使songLabel显示字符串"Unknown".
You also need a way to update timeLabel to display the elapsed time of the currently playing item. The best way to do this is by using addPeriodicTimeObserverForInterval(_:queue:usingBlock:), which will call the provided block on a given queue. Add this to the end of viewDidLoad:
你也需要一个去更新timeLabel的方法来显示当前播放项消耗的时间.使用addPeriodicTimeObserverForInterval(_:queue:usingBlock:)是达到当前目的最好的方法,该函数讲调用给定的队列当中提供的块.在viewDidLoad()的末尾处添加如下代码:
player.addPeriodicTimeObserverForInterval(CMTimeMake(1, 100), queue: dispatch_get_main_queue()) {
[unowned self] time in
let timeString = String(format: "%02.2f", CMTimeGetSeconds(time))
if UIApplication.sharedApplication().applicationState == .Active {
self.timeLabel.text = timeString
} else {
println("Background: \(timeString)")
}
}

This adds a periodic observer to the player that gets called back every 1/100 of a second and updates the UI to the time if the app is in the foreground.
这添加给播放器一个周期性的观察者,如果这个APP在前台,这个观察者每一秒的1/100就会被调用一次并且更新UI.
Important: Since you are going to update the UI in the closure, you must make sure the code is called on the main queue. This is why you specify dispatch_get_main_queue() for the queue parameter.
重要提示:由于你想在结束时更新UI,你必须确保这些代码在主队列中被调用.这就是你指定dispatch_get_main_queue()参数的原因.
Pause here and consider the application’s state.
在这里暂停一下,思考应用的状态.
Your application can have one of five states. Briefly, they are:
你的应用处于下面五个状态之一中.简单地说,他们是:
Not running: Your app is in this state before it starts up.
未运行:你的APP在开启之前处于这个状态.
Active: Once your app is started, it becomes active.
激活:一旦你的APP被开启,它变成活跃状态.
Inactive: When your app is running but something happens to interrupt it, like a phone call, it becomes inactive. Inactive means that the app is still running in the foreground but it’s not receiving events.
未激活:当你的APP正在运行,但是一些事情打断它的动作,比如有电话打进来,它变成inactive状态.休眠意味着这个APP仍然在前台运行,只是它没有接收事件.
Backgrounded: In this state, your app is not in the foreground anymore but it is still able to run code.
后台:在这个状态下,你的APP不在前台显示了但是它仍然在执行代码.
Suspended: Your app enters this state when it’s no longer able to run code.
挂起:你的APP进入不再运行代码的状态.
If you’d like a more thorough explanation of the differences between these states, the subject is well-documented on Apple’s website at [Execution States for Apps](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/TheAppLifeCycle/TheAppLifeCycle.html#//apple_ref/doc/uid/TP40007072-CH2-SW3).
如果你想更深入的了解这些状态之间的区别,苹果网站的Execution States for Apps对此有很详细介绍.
You can check your app’s state by reading UIApplication.sharedApplication().applicationState. Keep in mind that you’ll only get three states back: .Active, .Inactive, and .Background. Suspended and not running obviously would never happen while your app is running code.
你可以通过读取UIApplication.sharedApplication().applicationState来检查APP的转台.记住:你只能获取三种状态的返回值: .Active, .Inactive, and .Background.当你的APP在执行代码的时候,挂起状态和未运行状态很明显不可能出现.
Returning to the code, if the app is in the active state, you need to update the music title label. You could still update the label text while in the background, but the point here is just to demonstrate that your app continues to receive this callback when your app is in the background.
让我们将目光继续放在之前代码上,如果该应用处于激活状态,你需要更新音乐标题栏.在后台中,你仍然能够更新这个label的文字,但是这点知识证明了当你APP在后台的时候继续接受回调.
Now all that’s remaining is for you to add the implementation of playPauseAction(_:) to get the play/pause button working. Update the empty implementation of playPauseAction(_:) in AudioViewController with:
现在,把剩余的代码添加到playPauseAction(_:)的实现中,让播放/暂停按钮工作.在AudioViewController中,把下面代码添加到playPauseAction(_:)的实现中:
@IBAction func playPauseAction(sender: UIButton) {
sender.selected = !sender.selected
if sender.selected {
player.play()
} else {
player.pause()
}
}

Great, that’s all your code. Build and run, and you should see this:
很好,这是你全部的代码.创建并运行,你将看到下面的样子:

Now hit Play and the music will start. Nice!
现在,点播放,音乐将开始.很好!
Test if backgrounding works. Hit the home button (if you’re using the Simulator, hit Cmd-Shift-H). If you are running on an actual device (and not the Simulator in Xcode 6.1) the music will stop. Why? Well, there’s still a very crucial piece missing!
测试后台模式是否起作用.按home按钮(如果你正在使用模拟器,按Cmd-Shift-H).如果你在真正的设备上运行(不是Xcode 6.1的模拟器)音乐将停止.这是为什么呢?还有很重要的一块落下了!)
For most background modes (the “Whatever” mode is the exception) you need to add a key in Info.plist to indicate that the app wants to run code while in the background. Fortunately, Xcode 6 makes this a matter of clicking on a checkbox.
对于大多数的后台模式("Whatever"模式除外)你需要在Info.plist中添加一个key用来指明APP在后台中运行的代码.幸运的是,在Xcode6可以通过复选框进行选择.
Back in Xcode, do the following:
回到Xcode，按照以下步骤进行操作:
Click the project on the Project Navigator;
在项目管理器中点击工程
Click TheBackgrounder target;
点击目标TheBackgrounder
Click on the Capabilities tab;
点击功能标签
Scroll down to Background Modes and turn the switch on;
滑动背景模式并设置为ON
Check Audio and AirPlay.
选中 Audio和AirPlay
-----------------图片-------------

Build and run again. Start the music and hit the home button, and this time you should still hear the music, even though the app is in the background.
重新编译并且运行.开始运行音乐并且点击home键，尽管这个APP在后台运行，这次你就会依旧能够听到音乐.
You should also see the time updates in your Console output in Xcode, proof that your code is still working even though the app is in the background. You can download the partially finished sample project up to this point.
在Xcode的输出里你也能够在控制台看到实时的更新，着就证明了虽然你的APP在后台运行，但是你的代码依旧在工作.现在你可以[下载部分完成的示例代码了.](http://cdn4.raywenderlich.com/wpcontent/uploads/2015/04/TheBackgrounder2.zip)
That’s one mode down, if you’re following through the entire tutorial – three to go!
以上第一个模式结束了,如果你想学完整个教程--那就继续往下读吧!

##Receiving Location Updates
##接收位置更新
When in location background mode, your app will still receive location delegate messages with the updated location of the user, even when the app is in the background. You can control the accuracy of these location updates and even change that accuracy while backgrounded.
当在后台模式进行定位时，你的APP依旧会随着用户更新位置而接收到位置信息，甚至APP在后台的时候.你可以控制这些位置更新的准确性,甚至改变精度.
Once again, for the sneaks: you can only use this background mode if your app truly needs this information to provide value for the user. If you use this mode and Apple sees nothing the user will gain from it, your app will be rejected. Sometimes Apple will also require you to add a warning to your app’s description stating that your app will result in increased battery usage.
如果你的app真正需要这些信息来为用户提供价值，你只能使用后台模式.如果你使用这个模式并且Apple看到用户将要获得这些信息，你的应用程序将会被拒绝.有时苹果也将要求你向app添加一个警告的描述说明app将导致增加电量的使用.
The second tab is for location updates, so open LocationViewController.swift and add some properties with inline initialization to LocationViewController.
第二步是为了位置更新，打开LocationViewController.swift并且向里面增加一些属性用来初始化LocationViewController.
var locations = [MKPointAnnotation]()

lazy var locationManager: CLLocationManager! = {
let manager = CLLocationManager()
manager.desiredAccuracy = kCLLocationAccuracyBest
manager.delegate = self
manager.requestAlwaysAuthorization()
return manager
}()

You’ll use locations to store the locations so you can plot them on the map. CLLocationManager is what you’ll use to get the device’s location updates. You instantiate it in a lazy way so the first time the property is accessed the function is called and it is initialized.
你将使用locations来存储能够绘制在地图上的位置信息.CLLocationManager可以使你能够从设备上获取位置更新.你使用延迟的方法实例化它,所以当你第一次访问该属性被调用的函数时,它才被初始化.
The code sets the accuracy of the location manager to the most accurate, which you can adjust to whatever your app might need. You’ll learn more about those other accuracy settings and their importance in a moment. Notice that you also are calling requestAlwaysAuthorization(). This is a requirement in iOS 8, and brings up a user interface asking for permission to use location in the background.
代码可以设置位置管理器的精确度来实现最高的精确，你可以调节到你的app所需要的精确度.你会了解更多关于其他精度设置和它们的重要性.注意你也可以调用requestAlwaysAuthorization().这是在IOS8中的要求，并且为用户提供了接口来允许用户在后台使用位置.
Now you can fill in the empty implementation of accuracyChanged(_:) in LocationViewController:
现在你可以填写空的accuracyChanged(_:)的实现在LocationViewController里:
@IBAction func accuracyChanged(sender: UISegmentedControl) {
let accuracyValues = [
kCLLocationAccuracyBestForNavigation,
kCLLocationAccuracyBest,
kCLLocationAccuracyNearestTenMeters,
kCLLocationAccuracyHundredMeters,
kCLLocationAccuracyKilometer,
kCLLocationAccuracyThreeKilometers]

locationManager.desiredAccuracy = accuracyValues[sender.selectedSegmentIndex];
}

accuracyValues is an array with all the possible values of desiredAccuracy of CLLocationManager. This property controls how accurate you want your location to be.
accuracyValues是由CLLocationManager的desiredAccuracy可能值构成的数组.这些变量控制了你的位置的精确度.
You might think that this is stupid. Why shouldn’t the location manager always give you the best accuracy possible? Well, there’s a reason for the other values: conservation of battery power. Lower accuracy means less battery used.
你可能认为这种方式是愚蠢的.为什么位置管理器不能够给你最精确的位置信息呢？最重要的原因是为了节省电量.低精确意味着耗电量较低.
That means you should select the value for the least amount of accuracy that you can live with for your app. You can change this value whenever you want.
这就意味着你应该选择最少的值实现你的app可以承受的最低限度的精确度.你随时可以修改这些值在你的需求.

There’s another property that will control how often your app receives location updates, regardless of the value of desiredAccuracy: distanceFilter. This property tells the location manager that you only want to receive a new location update when the device has moved a certain value, in meters. Again, this value should be as high as it can be to conserve battery power.
另一个性能就是你可以控制你的app接收位置更新的频率，忽视desiredAccuracy: distanceFilter的值.当你的设备移动到了一定的值（以米计算）时，这个性能告诉位置管理器你只想接收位置更新.这个值应该最大限度的节省你的电池消耗.
Now you can add the code to start getting location updates in enabledChanged(_:):
现在你可以在enabledChanged(_:)中添加代码来实现获取位置更新：
@IBAction func enabledChanged(sender: UISwitch) {
if sender.on {
locationManager.startUpdatingLocation()
} else {
locationManager.stopUpdatingLocation()
}
}

The storyboard has a UISwitch attached to this action that turns location tracking on and off.
这个代码示例有一个与动作相关的UISwitch，这个UISwitch实现了位置跟踪的开启与关闭.
Next you need to add a CLLocationManagerDelegate method to receive location updates. Add the following method to LocationViewController.
下一步你可以通过添加一个CLLocationManagerDelegate方法来接收位置更新.添加以下方法到LocationViewController中.
// MARK: - CLLocationManagerDelegate

func locationManager(manager: CLLocationManager!, didUpdateToLocation newLocation: CLLocation!, fromLocation oldLocation: CLLocation!) {
// Add another annotation to the map.
let annotation = MKPointAnnotation()
annotation.coordinate = newLocation.coordinate

// Also add to our map so we can remove old values later
locations.append(annotation)

// Remove values if the array is too big
while locations.count > 100 {
let annotationToRemove = locations.first!
locations.removeAtIndex(0)

// Also remove from the map
mapView.removeAnnotation(annotationToRemove)
}

if UIApplication.sharedApplication().applicationState == .Active {
mapView.showAnnotations(locations, animated: true)
} else {
NSLog("App is backgrounded. New location is %@", newLocation)
}
}

If the app’s state is active, this code will update the map. If the app is in the background, you should see the log of location updates in your console output in Xcode.
如果app的状态是激活状态，这些代码将更新地图.如果这个app在后台运行，你应该在xcode的控制台来看位置更新的log.
Now that you know about Background Modes, you don’t need to make the same mistake as before! Check the box for Location updates to let iOS know that your app wants to continue receiving location updates while in the background.
现在你已经知道了后台模式，现在你不应该犯以前的相同的错误了.现在你可以在Location updates中设置使得ios知道你的app想在后台运行时继续接受位置更新
-----capabilities_location

In addition to checking this box, iOS 8 also requires that you set a key in your Info.plist allows you to explain to the user why background updates are needed. If you do not include this, location will silently fail.
除了更改这个之外，你应该在你的Info.plist中设置一个关键词来允许你向使用者解释为什么后台更行数据是需要的.如果不被允许后台更新，位置更新就会慢慢地失败.
Select the Info.plist file.
Click on the + button to add a key.
Add the key named NSLocationAlwaysUsageDescription.
Write a convincing description of why you need location in the background.
步骤如下：
选择Info.plist文件
点击+号来添加一个关键词
点击这个关键词的名字：NSLocationAlwaysUsageDescription
描述为什么你需要在后台位置更新，能够另使用者信服.
------Info_plist

Now build and run! Switch to the second tab and flip the switch to ON.
现在你可以编译并且运行你的程序了.切换到第二个选项卡并打开开关.
When you do this the first time, you will see the message that you put into your Info.plist. Tap Allow and go for a walk outside or around your building. You should start seeing location updates, even on the Simulator.
当你第一次运行的时候，你会看到你写入到Info.plist中的信息.点击allow出去走走，或者围绕你周围的建筑转一转.这时候你就开始看到位置信息的更新，在模拟器里也可以实现.
-----location_permission

After a while, you should see something like this:
过一会，你将会看到如下的一些东西：
---location_done

If you background the app, you should see the app updating the location in your console log. Open the app again and you’ll see that the map has all the pins for the locations that were updated while the app was in the background.
如果你在后台运行你的app，你将会在你的控制台log看到你的app位置更新信息.重新打开你的app，你就会发现地图上有所有的位置点，这些就是你的app在后台 运行时候更新的数据.
If you’re using the Simulator, you can use it to simulate movement, too! Check out the Debug \ Location menu:
如果你使用的是模拟器，你也可以使用这个app来模拟这个动作.打开菜单Debug \ Location：

---location_sim

Try setting the location to Freeway Drive and then hit the home button. You should see the console logs printing out your progress as you simulate that drive down a California highway:
设置location选项为Freeway Drive然后点击home按钮.这时候你就会看到在控制台打印出你的程序运行的状态，就像你在模拟你开车在加利福尼亚的高速公路上.

--------
2014-12-21 20:05:13.334 TheBackgrounder[21591:674586] App is backgrounded. New location is <+37.33482105,-122.03350886> +/- 5.00m (speed 15.90 mps / course 255.94) @ 12/21/14, 8:05:13 PM Pacific Standard Time
2014-12-21 20:05:14.813 TheBackgrounder[21591:674586] App is backgrounded. New location is <+37.33477977,-122.03369603> +/- 5.00m (speed 17.21 mps / course 255.59) @ 12/21/14, 8:05:14 PM Pacific Standard Time
2014-12-21 20:05:15.320 TheBackgrounder[21591:674586] App is backgrounded. New location is <+37.33474691,-122.03389325> +/- 5.00m (speed 18.27 mps / course 257.34) @ 12/21/14, 8:05:15 PM Pacific Standard Time
2014-12-21 20:05:16.330 TheBackgrounder[21591:674586] App is backgrounded. New location is <+37.33470894,-122.03411085> +/- 5.00m (speed 19.27 mps / course 257.70) @ 12/21/14, 8:05:16 PM Pacific Standard Time

You can download the partially finished sample project up to this point. On to the third tab and the third background mode!
现在你可以[下载这个示例程序了]（http://cdn2.raywenderlich.com/wp-content/uploads/2015/04/TheBackgrounder3.zip）.
Performing Finite-Length Tasks… or, Whatever
执行有限长任务…或者,等等
The next background mode is officially called Executing a Finite-Length Task in the Background. What a mouthful. I think Whatever is catchier!
下一个后台模式在可以正式的称为后台执行有限长的任务（Executing a Finite-Length Task in the Background）.
Technically, this is not a background mode at all, as you don’t have to declare that your app uses this mode in Info.plist (or use the Background Mode checkboxes). Instead, it’s an API that lets you run arbitrary code for a finite amount of time when your app is in the background. Well… whatever!
严格的说这并不是真正意义上的后台模式，因为你并没有在Info.plist中声明在你的app中使用这个模式（或者在复选框中使用Background Mode）.相反，它只是一个api你可以让你的任意代码运行有限的时间，当你的app在后台运行的时候.
In the past, this mode was often used to complete an upload or download and a generous (but not guaranteed) 10 minutes was provided to accomplish these tasks. But what if the connection was slow and the process still didn’t finish? It would leave your app in an odd state and you would have to add lots of error handling code to make things work sanely. Because of this, Apple introduced NSURLSession.
在过去，这个模式只是在上传或者下载或者运行某一段时间来完成某一项任务.但是如果这个链接很缓慢或者这个进行一直不结束怎么办？它会让你的应用程序在一个奇怪的状态,你必须添加大量的代码来处理错误使得程序稳健地工作.work sanely.  因为这样的原因，Apple介绍了NSURLSession.
Although not a topic of this tutorial, NSURLSession is robust in the face of backgrounding and even device reboots and accomplishes this in a power efficient way. If you are dealing with large downloads, check out our NSURLSession tutorial.
NSURLSession在面对后台运行甚至设备重启时具有鲁棒性，并且以减少设备能耗的方式完成任务.如果你想处理大规模的下载，请查看我们的NSURLSession tutorial.
A still very valid use case of the Whatever background mode is to complete some long running task, such as rendering and writing a video to the camera roll.
这种后台运行模式对完成一些长时间的任务还是一种非常有效的方法，比如在相机相册中进行渲染和写入一个视频.
-------Whatever_cheers

But this is just one example. As the code you can run is arbitrary, you can use this API to do pretty much anything: perform lengthy calculations (as in this tutorial), apply filters to images, render a complicated 3D mesh… whatever! Your imagination is the limit, as long as you keep in mind that you only get some time, not unlimited time.
但是这只是一个例子.你可以运行的代码是任意的,你可以用这个api来实现任意的事情：运行长时间的计算，将过滤器应用到图像处理，渲染一个复杂3 d网格...whatever！只要是你想在长时间运行你的程序你都可以用这个api.
How much time you get after your app gets backgrounded is determined by iOS. There are no guarantees on the time you’re granted, but you can always check backgroundTimeRemaining on UIApplication. This will tell you how much time you have left.
你的app在后台运行的时间取决于ios系统.对于后台运行时间你可以在UIApplication中查询backgroundTimeRemaining，它将会告诉你剩余多长时间.
The general, observation-based consensus is that you get 3 minutes. Again, there are no guarantees and the API documentation doesn’t even give a ballpark number – so don’t rely on this number. You might get 5 minutes or 5 seconds, so your app needs to be prepared for anything!
一般来说你会有3分钟时间来实现.但是在api文档中并没有给一个大约的时间，所以你不能依赖这个时间，可能是5分钟也可能是5秒.所以你的app需要准备发生的任何事情.
Here’s a common task that every CS student should be familiar with: calculating numbers in the Fibonacci Sequence.
这里给一个计算机学生都熟悉的任务：斐波纳契数列.
The twist here is that you’ll calculate these numbers in the background!
这里的意义是,你会在后台计算这些数字!
Open WhateverViewController.swift and add the following properties to WhateverViewController:
打开WhateverViewController.swift并且在WhateverViewController里面添加属性.
var previous = NSDecimalNumber.one()
var current = NSDecimalNumber.one()
var position: UInt = 1
var updateTimer: NSTimer?
var backgroundTask: UIBackgroundTaskIdentifier = UIBackgroundTaskInvalid
The NSDecimalNumbers will hold the two previous values of the number in the sequence. NSDecimalNumber can hold very large numbers, so it’s well suited for your purpose. position is just a counter that tells you the current number position in the series.
NSDecimalNumbers将保存序列中的前两个数的值.NSDecimalNumbers可以保存大的数据，因此非常适合你的目标.Position只是一个计数器来告诉你这个这个数在当前序列中的位置.
You’ll use updateTimer to demonstrate that even timers continue to work when using this API, and also to slow down the calculations a little so you can observe them.
你将使用updateTimer证明甚至计时器继续使用这个API时,也稍微放慢速度的计算,这样你就可以观察他们.
Add some utility methods to WhateverViewController that reset the Fibonacci calculation and that start and stop a background-able task:
在WhateverViewController中添加一些实用方法来重置斐波那契计算,启动和停止能够后台运行的任务:
func resetCalculation() {
previous = NSDecimalNumber.one()
current = NSDecimalNumber.one()
position = 1
}

func registerBackgroundTask() {
backgroundTask = UIApplication.sharedApplication().beginBackgroundTaskWithExpirationHandler {
[unowned self] in
self.endBackgroundTask()
}
assert(backgroundTask != UIBackgroundTaskInvalid)
}

func endBackgroundTask() {
NSLog("Background task ended.")
UIApplication.sharedApplication().endBackgroundTask(backgroundTask)
backgroundTask = UIBackgroundTaskInvalid
}
Now for the important part, fill in the empty implementation for didTapPlayPause(_:)
现在到了重要部分，在didTapPlayPause(_:)添加空的实现：
@IBAction func didTapPlayPause(sender: UIButton) {
sender.selected = !sender.selected
if sender.selected {
resetCalculation()
updateTimer = NSTimer.scheduledTimerWithTimeInterval(0.5, target: self,
selector: "calculateNextNumber", userInfo: nil, repeats: true)
registerBackgroundTask()
} else {
updateTimer?.invalidate()
updateTimer = nil
if backgroundTask != UIBackgroundTaskInvalid {
endBackgroundTask()
}
}
}
The button will toggle its selected state depending on whether the calculation is stopped and should start, or is already started and should stop.
按钮改变选择状态取决于计算已经停止，应该开始或者是计算已经开始，应该停止.
First you have to set up the Fibonacci sequence variables. Then you can create an NSTimer that will fire twice every second and call calculateNextNumber() (still to come).
首先你必须设置斐波那契序列变量.然后你可以创建一个NSTimer，没秒启动两次，并且调用 calculateNextNumber()函数.
Now comes the essential bit: the call to registerBackgroundTask() that in turn calls beginBackgroundTaskWithExpirationHandler(_:). This is the method that tells iOS that you need more time to complete whatever it is you’re doing in case the app is backgrounded. After this call, if your app is backgrounded it will still get CPU time until you call endBackgroundTask().
现在到了一个重要的时刻：调用registerBackgroundTask()函数，反过来调用beginBackgroundTaskWithExpirationHandler(_:).这个方法告诉了ISO你需要时间在后台运行你的app.这些调用完成之后，在你调用endBackgroundTask()之前你的app会一直获取cpu时间.
Well, almost. If you don’t call endBackgroundTask() after a period of time in the background, iOS will call the closure defined when you called beginBackgroundTaskWithExpirationHandler(_:) to give you a chance to stop executing code. So it’s a very good idea to then call endBackgroundTask() to tell the OS that you’re done. If you don’t do this and continue to execute code after this block is called, your app will be terminated!
嗯,差不多.如果你的app在后台运行一段时间后没有调用endBackgroundTask()，IOS将调用关闭程序定义，这是在你调用beginBackgroundTaskWithExpirationHandler(_:)时给你机会来停止执行代码.所以调用endBackgroundTask()告诉IOS你已经完成工作了是非常好的一个主意.如果你不执行上面所说的而是继续执行你的代码，你的app将会终止.
The second part of the if is simple: it only invalidates the timer and calls endBackgroundTask() to indicate to iOS that you don’t need any extra CPU time.
第二部分关于if的语句是很简单的：它只是使定时器失效，并且调用endBackgroundTask()来告诉ios不再需要额外的CPU 时间.
It is important that you call endBackgroundTask() for every time you call beginBackgroundTaskWithExpirationHandler(_:). If you call beginBackgroundTaskWithExpirationHandler(_:) twice and only call endBackgroundTask() for one of the tasks, you’re still going to get CPU time until you call endBackgroundTask() a second time with the value of the second background task. This is also why you needed backgroundTask.
在你每次调用beginBackgroundTaskWithExpirationHandler(_:)时调用endBackgroundTask()是非常重要的.如果你在一个任务里调用beginBackgroundTaskWithExpirationHandler(_:)两次而只调用endBackgroundTask()一次，你将仍然获取cpu时间，直到你在运行第二次的后台任务是调用endBackgroundTask()才能结束.这就是为什么你需要backgroundTask.
Now you can implement the little CS project method. Add the following method to WhateverViewController:
现在你可以实现简单的计算机程序方法.在WhateverViewController添加以下的方法：
func calculateNextNumber() {
let result = current.decimalNumberByAdding(previous)

let bigNumber = NSDecimalNumber(mantissa: 1, exponent: 40, isNegative: false)
if result.compare(bigNumber) == .OrderedAscending {
previous = current
current = result
++position
}
else {
// This is just too much.... Start over.
resetCalculation()
}

let resultsMessage = "Position \(position) = \(current)"

switch UIApplication.sharedApplication().applicationState {
case .Active:
resultsLabel.text = resultsMessage
case .Background:
NSLog("App is backgrounded. Next number = %@", resultsMessage)
NSLog("Background time remaining = %.1f seconds", UIApplication.sharedApplication().backgroundTimeRemaining)
case .Inactive:
break
}
}
Once again, here’s the application state trick to show the result even when your app’s in the background. In this case, there’s one more interesting piece of information: the value of backgroundTimeRemaining. The calculation will only stop when iOS calls the block added through the call to beginBackgroundTaskWithExpirationHandler(_:).
再一次，我们将展示另一个方法即使你的app在后台运行依旧能够显示结果.在这种情形下，还有一个有趣的信息: backgroundTimeRemaining的数值.只有当ios调用添加调用beginBackgroundTaskWithExpirationHandler(_:)的时才会停止.
Build and run, then switch to the third tab.
编译并且运行，然后切换到第三个选项卡.

Tap Play and you should see the app calculating the values. Now hit the home button and watch the output in Xcode’s console. You should see the app still updating the numbers while the time remaining goes down.
点击play并且你将会看到app计算出的值.现在点击home键然后查看xcode控制台.你应该会看到app依旧会更新数字，与此同时时间依旧在向前走.
In most cases, this time will start with 180 (180 seconds = 3 minutes) and go down to 5 seconds. If you wait for the time to expire when you reach 5 seconds (could be another value depending on your specific conditions), the expiration block will be invoked and your app should stop outputting anything. Then if you go back to the app, the timer should start firing again and the whole madness will continue.
在大多数情况下，这个时间将从第180秒开始并且延续5秒钟.如果你等待

如果你重新回到你的app，定时器将重新开始启动并且所有的错误行为将继续.
There’s only one bug in this code, and it gives me the opportunity to explain about the background notifications. Suppose you background the app and wait until the allotted time expires. In this case, your app will call the expiration handler and invoke endBackgroundTask(), thus ending the need for background time.
在代码里只有一个bug，它给我机会来解释关于后台通知.假设你或太运行app并且等待分配的时间到期.在这种情况下，你app将调用？？并且调用endBackgroundTask()，也就是终结后台运行时间的需求.
If you then return to the app, the timer will continue to fire. But if you leave the app again, you’ll get no background time at all. Why? Because nowhere between the expiration and returning to the background did the app call beginBackgroundTaskWithExpirationHandler(_:).
如果你继续返回你的app，定时器将继续激活.但是如果你离开app，你将不会得到或太运行时间.Why？因为在超时和回到后台期间app没有间隙来调用beginBackgroundTaskWithExpirationHandler(_:).
How can you solve this? There are a number of ways to go about it and one of them is to use a state change notification.
你怎么解决这个问题呢？有许多方法能够解决这个问题，并且其中一个是使用一种状态来改变通知.
There are two ways you can get a notification that your app has changed state. The first is through your main app delegate’s methods. The second is by listening for some notifications that iOS sends to your app:
有两种你可以得到通知并且你的app可以改变它的状态的方法：第一种是通过你的主app委托方法；第二种是通过监听ios发送给你的app的通知.
•	UIApplicationWillResignActiveNotification and applicationWillResignActive(_:) These are sent and called when your app is about to enter the inactive state. At this point, your app is not yet in the background – it’s still the foreground app but it won’t receive any UI events.
当你的app将要进入不活跃的状态，UIApplicationWillResignActiveNotification和applicationWillResignActive(_:)将会被发送和调用.在这种情况下，你的app不是在后台运行，它依旧在前台运行，但是它将不会接收到任何UI事件.
•	UIApplicationDidEnterBackgroundNotification and applicationDidEnterBackground(_:) These are sent and called when the app enters the background state. At this point, your app is not active anymore and you’re just getting this last chance to run some code. This is the perfect moment to call beginBackgroundTaskWithExpirationHandler(_:) if you want to get more CPU time.
•	当app进入到后台状态，UIApplicationDidEnterBackgroundNotification 和applicationDidEnterBackground(_:)将会被发送和调用.在这种情况下，你的app将不会是在激活状态，并且它是你最后的机会运行你的代码.如果你想得到更多的CPU时刻，这是一个调用beginBackgroundTaskWithExpirationHandler(_:)非常完美的时机.
•	UIApplicationWillEnterForegroundNotification and applicationWillEnterForeground(_:) These are sent and called when the app is returning to the active state. The app is still in the background but you can already restart anything you want to do. This is a good time to call endBackgroundTask() if you’re only calling beginBackgroundTaskWithExpirationHandler(_:) when actually entering the background.
当app返回激活状态，UIApplicationWillEnterForegroundNotification 和applicationWillEnterForeground(_:)将会被发送和调用.这是app依旧在后台运行，你已经可以启动任何你想做的事.当你真正进入后台运行是如果你只调用了beginBackgroundTaskWithExpirationHandler(_:)，此时将是一个好的时机调用endBackgroundTask().
•	UIApplicationDidBecomeActiveNotification and applicationDidBecomeActive(_:) These are sent and called right after the previous notification, in case your app is returning from the background. These are also sent and called if your app was only temporarily interrupted – by a call, for example – and never really entered the background, but you received UIApplicationWillResignActiveNotification.
以防你的app从后台运行状态返回，在前一个通知完成后UIApplicationDidBecomeActiveNotification和applicationDidBecomeActive(_:)将会被发送和调用.如果你的app只是临时的中断也会被调用-举例—如果你的app没有真正的进入到后台，但是你依旧会收到UIApplicationWillResignActiveNotification.
You can see all this in graphic detail (literally – there are some nice flow charts) in Apple’s documentation for App States for Apps.
你可以在Apple’s documentation for App States for Apps中看到所有的图像化描述（文章—有着许多非常棒的图表）
Time to fix the bug. First, override viewDidLoad() and subscribe to UIApplicationDidBecomeActiveNotification.
现在是解决这个bug的时间了.首先要重写viewDidLoad()并且订阅UIApplicationDidBecomeActiveNotification.
override func viewDidLoad() {
super.viewDidLoad()
NSNotificationCenter.defaultCenter().addObserver(self, selector: Selector("reinstateBackgroundTask"), name: UIApplicationDidBecomeActiveNotification, object: nil)
}
This designates the selector reinstateBackgroundTask to be called whenever the application becomes active.
不管何时这app变成激活状态，指定的选择器reinstateBackgroundTask将被调用.
Whenever you subscribe to a notification you should also think about where to unsubscribe. Use deinit to do this. Add the following to WhateverViewController:
不管何时你订阅了一个通知你也应该想到这个订阅的通知哪里不应该被订阅.使用deinit来完成这个功能.按照下面的代码加入到WhateverViewController.
deinit {
NSNotificationCenter.defaultCenter().removeObserver(self)
}
Finally, implement reinstateBackgroundTask().
最后实现reinstateBackgroundTask().
func reinstateBackgroundTask() {
if updateTimer != nil && (backgroundTask == UIBackgroundTaskInvalid) {
registerBackgroundTask()
}
}
You only need to reinstate if there is a timer going but the background task is invalid.
如果定时器依然运行但是后台任务没有运行，你只需要恢复就可以了.
Breaking your code into smaller utility functions that do one thing is paying off. You simply need to call registerBackgroundTask() when a background task not running for the current timer.
把你的代码分解成小的实用的代码只需要做一件事就可以.当一个后台任务不是在当前的定时器下你只需要调用registerBackgroundTask()即可.
And there you have it. You can download the partially finished sample project up to this point.
然后你可以使用了.你可以再下载http://cdn3.raywenderlich.com/wp-content/uploads/2015/04/TheBackgrounder4.zip这个程序.
On to the final topic for this tutorial: Background Fetching.
这个课程的最后的一节是：Background Fetching.
Background Fetch
后台获取
Background fetch is a mode introduced in iOS 7 that lets your app appear always up-to-date with the latest information while minimizing the impact on battery. Suppose, for example, you were implementing a news feed in your app. Prior to background fetch, you might do this by getting new data in viewWillAppear(_:). The problem with this solution is that your user is looking at the old data for at least several seconds until the new data comes in. Wouldn’t it be better if right when they opened your app the new data was magically there? This is what background fetch gives you.
后台获取是iOS7中推出的让你的APP在最大限度减少对电池损耗的时候总是展现最新的信息.举个例子,假设你正在给你的APP填充信息.你可以通过viewWillAppear(_:).获取最新数据来预先通知后台模式.这个方案可以解决在新数据刷新过来之前你的用户正在浏览前几秒的数据.当用户打开你APP的同时,最新的数据同时被神奇的展现了,这种情况再好不过了.这是后台模式能够为你实现的操作.
When enabled, the system uses usage patterns to determine when to best fire off a background fetch. For example, if your user opens the app at 9 AM each morning, it is likely that a background fetch will be scheduled sometime before that time. The system decides the best time to issue a background fetch and for this reason you should not use it to do critical updates.
当APP被激活的时候,系统会使用惯用模式去决定什么时候执行后台获取.比如,如果用户每天都在早上9点打开改APP,后台获取在这个时间点之前预先执行是很可能的.系统决定什么时候是安排后台获取的最好时间,因此你不应该用它去做紧急的更新.
In order to implement background fetch there are three things you must do:
这里有你为了实现后台获取必须做的三件事情:
Check the box Background fetch in the Background Modes of your app’s Capabilities.
检查你APPCapabilities选项中后台模式的后台获取选项框是否被选中.
Use setMinimumBackgroundFetchInterval(_:) to set a time interval appropriate for your app.
使用setMinimumBackgroundFetchInterval(_:) 为你的APP创建一个合适的时间间隔.
Implement application(_:performFetchWithCompletionHandler:) in your app delegate to handle the background fetch.
在你APP委托中实现application(_:performFetchWithCompletionHandler:)去管理后台获取.
As the name implies, background fetch normally involves fetching information from an external source like a network service. For the purposes of this tutorial, you won’t use the network but just fetch the current time. This simplification will let you understand everything required to perform a background fetch and test it without worrying about an external service.
后台获取就像他名字表示的一样,他通常涉及到从外源,比如网络服务,中获取信息.就这个教程的意图,你将不会使用网络而仅仅获取现在的时间.这样简化讲让你理解在不同担心外在的服务的时候操作并测试后台模式所需要的每一样东西.
In contrast to finite-length tasks, you only have seconds to operate when doing a background fetch – the consensus figure is a maximum of 30 seconds, but shorter is better. If you need to download large resources as part of the fetch, this is where you need to use NSURLSession‘s background transfer service.
对于有限长度的任务,你只有以按秒为单位的时间去执行操作,公认的时间是不超过30秒，但越短越好.如果您需要下载大量资源最为获取的部分，这就是你需要使用NSURLSession的背景传输服务的地方.
Time to get started. First, open FetchViewController.swift and add the following property and method to FetchViewController.
开始的时间到了.首先,打开FetchViewController.swift,并将下面的属性和方法添加到FetchViewController中.
var time: NSDate?

func fetch(completion: () -> Void) {
time = NSDate()
completion()
}

This code is a simplified replacement of what you might actually do to fetch some data from an external source (such as a JSON or XML RESTful service). Since it might take several seconds to fetch and parse the data, you pass a completion handler that gets called when the process is done. You will see why this is important a little later.
这些代码是代替你真正的从外源(json或XML RESTful 服务)中获取数据的一种简化.因为它可能需要几秒钟来获取和分析数据，你传递一个完成的handler,这个handler在进程完成后被调用.你待会儿会看到为什么很很重要.
Next, finish off the view controller. Add the following methods to FetchViewController.
接下来,完成view controller的代码.将下面的方法添加到FetchViewController中.
func updateUI() {
if let time = time {
let formatter = NSDateFormatter()
formatter.dateStyle = .ShortStyle
formatter.timeStyle = .LongStyle
updateLabel?.text = formatter.stringFromDate(time)
}
else {
updateLabel?.text = "Not yet updated"
}
}

override func viewDidLoad() {
super.viewDidLoad()
updateUI()
}

@IBAction func didTapUpdate(sender: UIButton) {
fetch { self.updateUI() }
}

updateUI() formats the time and shows it. It is an optional type so if it is not set, it shows the message not updated yet. When the view is first loaded (in viewDidLoad()) you don’t fetch, but go straight to updateUI() which means the message “Not yet updated” will appear first. Finally, when the Update button is tapped it performs a fetch and as a completion updates the UI.
updateUI()格式化这个时间并显示它.它是一个可选的类型,所以如果它没有被创建,他将展示至今没有更新的信息.当这个view初次被加载时(在 viewDidLoad()中)你不能获取到,但是直接调用updateUI()函数,将会有“Not yet updated”的字样在开始时显示.最后,当更新按钮被监听的时候,它运行获取的代码并且会完成对UI的更新.
updateUI()
At this point, the view controller is working.
就这一点而言,该view controller正在工作.
fetch_working

However, background fetching is not enabled.
然而,后台获取没有起作用.
The first step to enabling background fetching is to check Background fetch in the Capabilities tab of your app. By now this should be old hat. Go ahead and do this.
启用后台获取的第一步是在Capabilities选项栏里选中Background fetch.到现在这个操作已经是老一套的了,直接找到它并选中.
capability_fetch

Next, open AppDelegate.swift and request background fetches in application(_:didFinishLaunchingWithOptions:) by setting the minimum background fetch interval.
接下来,打开AppDelegate.swift,通过在 application(_:didFinishLaunchingWithOptions:)中设置最小的后台获取时间间隔来请求后台获取操作.
func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: [NSObject: AnyObject]?) -> Bool {
UIApplication.sharedApplication().setMinimumBackgroundFetchInterval(
UIApplicationBackgroundFetchIntervalMinimum)

return true
}

The default interval is UIApplicationBackgroundFetchIntervalNever which you might want to switch back to, if, for example your user logs out and no longer needs updates. You can also set a specific interval in seconds. The system will wait at least that many seconds before issuing a background fetch.
默认的时间间隔是你想切换回去的UIApplicationBackgroundFetchIntervalNever,比如,你的用户日志和不需要更新的内容.你也可以设置一个精确到秒的时间间隔.系统在开始执行后台获取之前将等待一段时间.
Be careful not to set the value too small because it will chew through battery unnecessarily as well as hammer your server. In the end the exact timing of the fetch is up to the system but will wait at least this interval before performing it. Generally, UIApplicationBackgroundFetchIntervalMinimum is a good default value to use.
要小心,,不要将时间间隔设置过短,因为它会多余的消耗电池和损害服务器.结束获取信息的确切时间是由系统决定的,但是在执行它之前将会等待一段时间.通常,UIApplicationBackgroundFetchIntervalMinimum是很好用的默认值.
Finally, to enable background fetch you must implement application(_:performFetchWithCompletionHandler:). Add the following method to AppDelegate.swift:
最后,为了启用后台程序,你必须实现application(_:performFetchWithCompletionHandler:).将下列方法添加到AppDelegate.swift中.
// Support for background fetch
func application(application: UIApplication, performFetchWithCompletionHandler completionHandler: (UIBackgroundFetchResult) -> Void) {

if let tabBarController = window?.rootViewController as? UITabBarController,
viewControllers = tabBarController.viewControllers as? [UIViewController] {
for viewController in viewControllers {
if let fetchViewController = viewController as? FetchViewController {
fetchViewController.fetch {
fetchViewController.updateUI()
completionHandler(.NewData)
}
}
}
}
}

First you need to get the FetchViewContoller. Next, optionally cast since the rootViewController is not necessarily a UITabBarController in every app, although it is in this app so it will never fail.
首先你需要获取FetchViewContoller.然后,因为rootViewController在每个APP中不是必须的UITabBarController,所以它是可以选择创建的,不过它在这个APP中,所以它绝不会出现问题.
Next, you loop though all of the view controllers in the tab bar controller and find the one that successfully casts to FetchViewController. In this app you know it is the last view controller so you could hard-code it, but looping makes it a little more robust in case you decide to add or remove a tab later.
接下来,你在选项卡控制器中循环添加所有的视图控制器,并且将它们成功的放到FetchViewController中.在这个APP中,你知道它是最后的控制器,所以你不能对它进行硬编码,但是在你决定以后添加或删除选项卡的时候循环创建会提高程序的健壮性.
Finally you call fetch(_:). When it completes, you update the UI and then call the completionHandler that was passed in as a parameter of the function. It is important that you call this completion handler at the end of the operation. You specify what happened during the fetch as the first parameter. Possible values are .NewData, .NoData, or .Failed.
最后,你可以调用fetch(_:).当它执行完后,你会更新UI,然后调用将completionHandler作为参数传递的函数.你在这个操作的最后调用这个完成处理的程序是很重要的.你指定在获取过程中获取的结果作为以一个参数.它的可能值为.NewData, .NoData或者.Failed.
For simplicity, the tutorial always specifies .NewData since getting the time will never fail and is always different than the last call. iOS can then use this value to better time the background fetches. The system knows at this point to take a snapshot of the app so it can present it in the card view of the app switcher. And that is all there is to it.
为了简单起见,该教程总是指定.NewData作为永远成功获取时间的返回值,并且这个值和上一次的结果总是不同的.在这之后,iOS可以使用更好的时间间隔来执行后台获取.该系统知道在这个时间点上的系统快照,所以它可以在应用程序切换卡中显示.以上是为了实现后台获取所需要的所有的操作.
Note: Rather than passing the completion closure along, it can be tempting to save it away in a property variable and call that when your fetch completes. Don’t do this. If you get multiple calls to application(_:performFetchWithCompletionHandler:), the previous handler will get overwritten and never called. It is better to pass the handler through and call it as it will make this kind of programming error impossible.
提示:不是沿着信息传递完成对属性的调用,而是保存一个属性变量,并且在你获取完成后调用它是很有诱惑力的.不这样做的话,如果你多次调用 application(_:performFetchWithCompletionHandler:),先前的处理程序将会被覆盖,永远不会被调用.最好通过传递处理程序,并且在它不会造成这种编程错误的时候调用它.
##Testing Background Fetch
##测试后台获取
One way to test background fetch is to sit around and wait for the system to decide to do it. That would require a lot of sitting. Fortunately, Xcode gives a way to simulate a background fetch. There are two scenarios that you need to test. One is when your app is in the background, and the other when your app is coming back from being suspended. The first way is the easiest and is just a menu selection.
测试后台获取的一个方法是停下来等着系统决定去执行它.这需要大量的等待.幸运的是,Xcode体统了模拟后台获取的方法.有两种你需要测试的情况,一种是当你的APP在后台中时,另一种是你的APP处于从被挂起到继续运行的情况.第一种方法最简单,仅仅是一个选择菜单.
Start the app on an actual device (not a simulator);
在真正的设备上运行(不是模拟器);
From the Debug menu in Xcode, select Simulate Background Fetch;
在Xcode调试菜单中选择模拟后台获取;
Screen Shot 2015-01-16 at 9.57.24 PM

Reopen the app, noticing that it got sent to the background;
重新打开这个APP,注意被送到后台的数据.
Switch to the Fetch tab, (Notice that the time is updated to when you simulated the background fetch and not “Not yet updated”.)
切换到Fetch选项卡,(注意当你模拟后台获取而且不是显示“Not yet updated”的时候时间)
The other way you should test background fetch is to do it as a resume from suspension. There is a launch option that lets you launch your app directly into suspension. Since you might want to test this semi-often it is best to make a new Scheme with that option always set. Xcode makes this easy.
另一种方法是在从挂起状态回复的时候测试后台获取.这里有一个启动项让你APP一运行就直接进入挂起状态.因为你可能要测试这种临界状态,用这个选项始终建立新的Scheme是最好的.Xcode使这种情况很容易实现.
First select the Manage Schemes option.
首先选择Manage Schemes选项.
Screen Shot 2015-01-16 at 10.10.42 PM

Screen Shot 2015-01-16 at 10.42.12 PM

Next, select the only scheme in the list, and then click on the gear icon and select Duplicate Scheme.
接下来,选择列表里仅有的方案,然后点击齿轮图标,选择Duplicate Scheme.
Screen Shot 2015-01-16 at 10.08.58 PM

Lastly, rename your scheme to something reasonable, like, “Background Fetch” and check the checkbox to Launch due to background fetch event.
最后,用合理的名字重命名你的方案,比如 “Background Fetch”,并选中 Launch due to background fetch event的复选框.
launch_suspended

Note that in Xcode 6.1, this doesn’t work reliably with the simulator. In my own testing, I needed to use an actual device to launch into the suspended mode correctly.
需要注意的是在Xcode6.1中,在模拟器上这并不能可靠的运行.我自己测试的时候,我需要使用真正的设备正确的从启动进去到挂起状态.
Run your app with this scheme. You will notice that the app never actually opens but is launched into a suspended state. Now manually launch it and go to the Fetch tab. You should see that there is an updated time when you launched the app instead of “Not yet updated”.
用这个方案运行你的APP.你会发现,该APP没有真正的打开,而是直接运行到了挂起状态.现在,手动开启它,并进入Fetch选项.你会看到,当你运行该APP时,时间会更新,而不会显示“Not yet updated”.
Using background fetch effectively lets your users to seamlessly get the latest content.
使用后台获取能够有效地让你的用户们流畅的一直获取最新的内容.
##Where to Go From Here?
##到哪里去?
You can download the fully finished sample project here.
你可以[在这里下载完整的示例工程](http://cdn2.raywenderlich.com/wp-content/uploads/2015/04/TheBackgrounder.zip).
If you want to read Apple’s documentation on what we covered here, the best place to start is in Background Execution. This documentation explains every background mode and has links to the appropriate section of the documentation for every mode.
如果你想读我们这里涉及到苹果文档里的内容,最佳开始地点是 [Background Execution](https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/BackgroundExecution/BackgroundExecution.html#//apple_ref/doc/uid/TP40007072-CH4-SW1).该文档介绍了每一个后台模式,并为每个模式链接到相应的位置.
A particularly interesting section of this document is the one that talks about being a responsible background app. There are some details that might or might not relate to your app here that you should know before releasing an app that runs in the background.
该文档有趣的部分谈论了如何构建一个可靠的APP.你应该知道释放正在后台运行的APP中的一些细节或多或少会涉及到你到APP.
Finally, be sure to check out NSURLSession if you plan on doing large network transfers in the background.
最后,如果你打算做大型网络信息传输,确保检查[NSURLSession](http://www.raywenderlich.com/51127/nsurlsession-tutorial).
We hope you enjoyed this tutorial, and if you have any questions or comments, please join the forum discussion below!
我们希望你能享受这个课程,如果你有任何疑问或意见, 请加入下面的论坛讨论.