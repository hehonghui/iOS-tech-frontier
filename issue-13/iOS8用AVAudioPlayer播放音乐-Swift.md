iOS8 用AVAudioPlayer播放音乐（Swift）
---

> * 原文链接 : [Play Music with AVAudioPlayer in iOS8 with Swift](http://www.ioscreator.com/tutorials/play-music-avaudioplayer-ios8-swift)
* 原文作者 : [http://www.ioscreator.com](http://www.ioscreator.com/)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [HarriesChen](https://github.com/mrchenhao) 


`AVAudioPlayer `类提供了播放音频文件的功能，在本次教程中，我们将对一个音乐文件进行播放暂停和停止操作，此外还会显示标题和播放时间。本次教程使用iOS8和Xcod6.3.1

打开Xcode创建一个新的`Single View Application`,使用`IOS8SwiftPlayMusicAVAudioPlayerTutorial `作为`product name`,并填写组织名称和标识符。选择`Swift`语言并确保设备选择iPhone。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/557809f1e4b0c6cf0d971d80/1433930226089/?format=1500w)

在这个工程中我们需要开始暂停和停止按钮的图片和一个mp3文件。你可以在这里[下载](http://www.ioscreator.com/tutorials/play-music-avaudioplayer-ios8-swift#)。解压文件并把它们添加到工程，确保是拷贝他们到目标文件夹。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/55782903e4b09779d065bf7f/1433938179851/?format=2500w)

转到故事板拖两个标签和两个按钮到主视图。依次选择他们并在`Size Inspector`填入下列值。

* 上面的标签 -> X: 16, Y: 60, Width: 568, Height:17
* 中间的标签 -> X: 250, Y: 120, Width: 100, Height: 36
* 左边的按钮 -> X: 16, Y: 220, Width: 102, Height: 102
* 右边的按钮 -> X:484,  Y: 220, Width: 102, Height: 102

再依次选择他们在`Attributes Inspector`添加以下值。

* 上面的标签: Center Alignment, Font - System 14.0
* 中间的标签: Center Alignment. Font - System Bold 30.0
* 左边的按钮: Type -Custom, Image - playpause.png
* 右边的按钮: Type - Custom, Image - stop.p

按下`Resolve Auto Layout`按钮在故事板的右下角，选择`Reset to Suggested Constraints`

现在故事板看起来是这样的

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/55872ba7e4b087a1e08d485d/1434921897044/?format=2500w)

选择助理窗口并确保`ViewController.swift`可见。

按住`Ctrl`拖动顶部的标签创建下列的插口。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/55872c0ee4b0eb99d4affc23/1434922000928/?format=750w)

按住`Ctrl`拖动中间的标签创建下列的插口。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/55872c35e4b08fd2d8e19699/1434922041212/?format=750w)

按住`Ctrl`拖动播放暂停图片创建下列动作。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/55872c6be4b042906e107112/1434922092023/?format=750w)

按住`Ctrl`拖动播放停止图片创建下列动作。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/55872c89e4b042906e107182/1434922121603/?format=750w)

在`ViewController.swift`文件中导入`AVFoundation `框架。

```
import AVFoundation
```

添加下列属性在`ViewController `类中

```
var audioPlayer = AVAudioPlayer()
var isPlaying = false
var timer:NSTimer!
```

`AVAudioPlayer `类可以让你播放任何iOS支持的音频格式。使用`isPlaying`布尔值来表示当前是否正在播放音乐。`NSTimer `属性用来显示当前播放音乐的时间。


修改`viewDidLoad `方法

```
override func viewDidLoad() {
    super.viewDidLoad()
        
    trackTitle.text = "Future Islands - Tin Man"
    var path = NSBundle.mainBundle().URLForResource("Future Islands - Tin Man", withExtension: "mp3")
    var error:NSError?
        
    audioPlayer = AVAudioPlayer(contentsOfURL: path!, error: &error)
}

```

将标题赋值给标题的标签，然后用音乐文件初始化`AVAudioplayer `，接着实现`playOrPauseMusic `方法。


```
@IBAction func playOrPauseMusic(sender: AnyObject) {
    if isPlaying {
        audioPlayer.pause()
        isPlaying = false
    } else {
        audioPlayer.play()
        isPlaying = true
            
        timer = NSTimer.scheduledTimerWithTimeInterval(1.0, target: self, selector: "updateTime", userInfo: nil, repeats: true)
    }
}
```

首先我们判断是否正在播放，如果是，那么暂停音乐并改变`isPlaying `的值，如果没有播放，我们播放它并改变`isPlaying `的值。然后创建一个`NSTimer`对象用来每秒调用`updateTime`方法，我们来实现它。

```
func updateTime() {
    var currentTime = Int(audioPlayer.currentTime)
    var minutes = currentTime/60
    var seconds = currentTime - minutes * 60
        
    playedTime.text = NSString(format: "%02d:%02d", minutes,seconds) as String
}
```

播放器的`currentTime `属性将被分割成分和秒两部分用来显示播放的时间。最后实现`stopSound `方法。

```
@IBAction func stopMusic(sender: AnyObject) {
    audioPlayer.stop()
    audioPlayer.currentTime = 0
    isPlaying = false
}
```
 
播放器调用停止方法并重置当前播放时间。当下次按下播放时，将会从头开始播放。`isPlaying `属性将被赋值为false，构建并运行程序，按下播放/暂停按钮和停止按钮来控制音乐。


![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/5587308fe4b087a1e08d5bd3/1434923153194/?format=1500w)

你可以下载`IOS8SwiftPlayMusicAVAudioPlayerTutorial`的源代码在[GitHub](https://github.com/ioscreator/ioscreator)的仓库上。