`iOS8使用icloud`
---

>* 原文链接 : [Working with CloudKit in iOS 8](http://www.appcoda.com/cloudkit-introduction-tutorial/)
* 译文出自 [开发技术前线](http://www.dectf.cn)
* 译者 : [Harries Chen](https://github.com/mrchenhao) 

对于开发者来说，如何用适当的方式来存储应用程序数据是一个比较重要的问题。 毫无疑问，任何应用都或多或少需要存储一些数据。对于一般的应用程序来说，被保存的数据各不相同，大部分时间都被存储在本地以便再次在相同的设备运行该应用程序。尽管这不是是唯一的选择。随着云服务的发展，一个应用程序也可以把数据存储在云端，既可以完全把数据存储在云端，也可以本地和远程都存储。


有许多网络服务商提供云服务，通常会赠送一定的免费空间。其中之一就是苹果，也就是著名的云服务提供商iCloud。开发人员可以访问iCloud，把它作为一个存储方式。有多种方法可以从iCloud中存储和读取数据，但在本教程中，我们将专注于其中之一：CloudKitframework。
![这里写图片描述](http://img.blog.csdn.net/20150515220618400)

当你处理结构化数据（不是单纯的文件和文档）并希望将其保存在网络上的，使用CloudKit会非常方便。通过CloudKit，可以很容易的做到其它数据库能做的事情，代替数据库、记录等等。事实上，一条CloudKit记录就是iOS中大家都知道的字典，当然CloudKit还可以处理其它不同的数据。通过本教程，我们会发现使用这个框架是有趣的，而不是特别困难。


我不想在这里说很多理论上的细节，因为有很多的地方可以看。你可以从苹果在[这里](https://developer.apple.com/library/ios/documentation/General/Conceptual/iCloudDesignGuide/DesigningforCloudKit/DesigningforCloudKit.html#//apple_ref/doc/uid/TP40012094-CH9-SW1)和[这里](https://developer.apple.com/library/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/Introduction/Introduction.html#//apple_ref/doc/uid/TP40014987)找到有用的文档。不过，也有一些事情我必须说一下，因为你需要在我们继续之前了解它们。先来看看他们是什么。

第一点也是最重要的一点，就是CloudKit使用容器来组织和处理应用程序的数据。容器就是分给应用程序的一块“空间”，换句话说就是一部分iCloud的空间分给了应用程序。在Xcode中使用CloudKit功能时，会创建一个默认的容器，但是可以忽略它然后自己创建一个（或者多个）。每个容器都有两个数据库：私人和公共。写入到私有数据库中的任何数据，只能被拥有它的用户进行访问。与此相反，存在于公共数据库中的数据，可以是对所有使用这个应用程序的用户可见的。

上面我所描述的几乎就是我们所谓的数据库管理系统。同样的，在CloudKit数据库表被称为记录类型，而表中的行被称为只是记录。有多种，可以使用CloudKit框架存储到iCloud的数据类型，但它们不是无限制的。下面是支持的几种类型：
NSData (single bytes)
NSDate (date and time)
NSNumber (both Int and Double)
NSString (or String in Swift)
NSArray (list)
CKReference (used to create relationships between objects)
CLLocation (location)
CKAsset (file)

以CK开头的数据类型是 CloudKit framework的一部分。

在接下来的部分你会看到如何处理这些类型的数据。说实话，我们将只处理字符串，日期和资源（这是一种特殊情况）。我们不会乱用CKReference和CLLocation（特殊类型），其余的使用方式与字符串和日期的方式相似。


最后，我告诉你一些你应该知道的。

没有登录iCloud帐户的用户可以从公共数据库中获取记录，但只有登录后才可以写入数据。至于私有数据库，必须先登录。

如果你还没有使用`iCloud Drive`，请务必使用的iOS 8的设备或者Yosemite升级到`iCloud Drive`。但是请注意，你将会和任何以前的iOS或Mac OS版本之间的iCloud不兼容。


一旦在iCloud中创建容器讲不能被删除。记录可以，但容器不行

有一个叫做 `CloudKit Dashboard`的网络平台，可以找到所有使用CloudKit框架的应用程序的数据，并在此教程的中，我会做一个简单的介绍。在`Dashboard `有两种环境：开发和生产。千万不要在这个演示中切换到第二个，不要部署容器架构到生产环境（会有特殊的按钮）。


我建议你读官方文档或者一些理论性的文章，用它作为你将来的应用程序的参考。我在前面的段落中描述的只是一小部分在你看那些之前。让我们一起加油因为还有很多东西要学。

##演示程序

现在我们来暂时告别理论，开始实战。和往常一样，在本教程中，你可以得到一个[开始项目](https://www.dropbox.com/s/jp1ouk52lw58c8p/CloudDemoStarter.zip?dl=0)。先把它下载下来，继续阅读，看看我们需要做什么。


在本次演示中，我们将创建一个非常简单的笔记应用程序。它将由两个视图控制器组成：一个主控制器和详细视图控制器（应用程序是基于导航）。我们会列出所有的笔记在第一个叫做ListNotesViewController的tableview，并使用CloudKit把他们存储到iCloud中。换句话说，我们将从iCloud中获取所有的记录并将他们显示在表格中。我们将创建或修改笔记在第二个叫做EditNoteViewController视图控制器，。

对于一个笔记，我们将有以下数据：

 + Title
 + Text
 + Last edited date
 + An image

如下图所示，EditNoteViewController视图控制器分成两块：在上面有一个文本框的标题和一个TextView的文本。在底部有一个按钮，点击它会出现图片选择器，所以我们可以从库中选择照片。选择完图片以后，按钮会被隐藏，被选择图片和一张按钮（其用于从图像中删除视图的照片）会显示在上面。

![这里写图片描述](http://img.blog.csdn.net/20150515221001859)


除此之外，我们不会在使用导航栏默认的返回按钮。而是使用自定义按钮来做到这一点，理由我现在不说，你将在后面看到它。最后，当按钮保存按钮时，笔记和图片一起将被存储到iCloud中。如果没有选择图片，一个默认的“无图像”图像将被上传来代替。

在ListNotesViewController视图控制器，没什么特别需要注意的地方。这意味着，当一个记录（笔记）被点击时，我们将跳转到下一个视图控制器进行编辑，同时我们也可以删除笔记。下面是一个示例：
![这里写图片描述](http://img.blog.csdn.net/20150515220939032)


在我们开始之前的工作，让我告诉你，在这个演示应用程序中，我们将在每次启动的时候下载所有的笔记。这不是你应该做的。真实的情况下，你应该找到一种方法来本地存储数据，并且只获取存在于iCloud的新数据。但是，这样做，在本教程将是毫无意义的;我的目标是向您展示如何处理CloudKit，而不是如何创建本地架构或者其他的解决方案来保存本地数据。所以，我就把这个问题留给你们自己去解决喽。

最后，不要忘记开启iCloud，虽然在你可以在本教程的最后下载到完整的项目（阅读有关启用iCloud功能）。

##为CloudKit做准备

在你第一次使用iCloudKit之前,有一些事情是必须做的，事情不多，事实上Xcode都可以帮你完成，你需要做的只是得到`anentitlements`文件以便让你的APP能够访问iCloud

首先，选择`Project Navigator`，打开`General`选项卡。在`Identity`部分，在`Team`的下拉菜单中将`none`选项改成一个真实的`Apple ID`（你的开发人员的Apple ID）。

![这里写图片描述](http://img.blog.csdn.net/20150515221134788)

如果只有`Non`e选项的话，打开 `Xcode > Preferences…`目录，进入`Accounts`选项卡，点击加号按钮添加你的`Apple ID`，添加完成后，返回`General`并选择它。

第二部就是去`Capabilities`选项卡打开`iCloud`能力，确定选择下面两项：

```
Key-value storage
CloudKit
```

在容器选项，只需要按照默认设置即可，如果没有问题的话，你会看到下面有四个勾在`Steps`选项，这意味着一切都准备完毕，此外，将会有一个叫做`CloudKit.entitlements`的新文件出现在左侧的导航栏。

![这里写图片描述](http://img.blog.csdn.net/20150515221208063)

如果其中一部有问题，Xcode会显示一个修复问题的按钮。使用Xcode可以解决任何问题。如果任然有问题，那么你可能需要修改捆绑标识符值，并更改`com.appcoda.CloudDemo tocom.YOURID.CloudDemo`（用户的ID来代替）。

![这里写图片描述](http://img.blog.csdn.net/20150515221235519)

通常情况下，不会有任何的问题，完成上诉步骤以后就可以进入下一步。Xcode会自动添加`CloudKit.framework`到项目中。在两个视图控制器中我们需要这个框架，我已经导入了，所以你不必再那样做。

```
import CloudKit
```

别忘了在你的项目中添加这一行代码，否则你将不能访问这个框架中得类。


##插入图片

现在，关于CloudKit的一切都已经完成，是时候来编写一些代码。首先就是让我们的演示应用程序能够从设备的照片库导入照片。如果你曾经使用过这类东西，那么你肯定知道，我们会使用`UIImagePickerController`类来访问照片库并选择照片。如果没有使用过，那么你也可以轻易的做到它。

`UIImagePickerControlle`r实际上是iOS SDK预制的视图控制器。它的功能非常简单：它可以访问设备的照片库中的图片，并提供委托方法使我们能够处理被选择的资源（这里称为资源，是因为你不止可以访问图片），另一方面，用户可以简单的关闭控制器。我们需要在这里的第一件事就是实现两个必需的协议来使`UIImagePickerController`正常得工作：`UIImagePickerControllerDelegate`和t`UINavigationControllerDelegate`。你需要做的就是打开`EditNoteViewController.swift`文件，如下图所示修改：


```
class EditNoteViewController: UIViewController, UIImagePickerControllerDelegate, UINavigationControllerDelegate
```

现在，我们来编写一些必要的代码来显示图像选择器。浏览整个项目，在哪里可以看到我们需要的所有`IBAction`方法，但是他们都还没有被实现。我们将会实现它，因为我们向前迈进这里。现在，让我们来参观`pickPhoto（_:)`方法，这是我们会写在这个项目中的第一段真正的代码。我们初始化并调用方式的图像选择器视图控制器基本上都是一样的，主要这取决于资源的类型（照片，视频），现在我们并不关心其它的因素。让我们来看看执行的第一部分：

```
@IBAction func pickPhoto(sender: AnyObject) {
    if UIImagePickerController.isSourceTypeAvailable(UIImagePickerControllerSourceType.SavedPhotosAlbum) {
        let imagePicker = UIImagePickerController()
        imagePicker.delegate = self
        imagePicker.sourceType = UIImagePickerControllerSourceType.PhotoLibrary
        imagePicker.allowsEditing = false
    }
}
```

首先，我们要保证设备可访问照片库，否则我们将在运行的时候会面临严重的问题。所以，在if语句中添加判断语句，初始化图像选择器并设置委托。然后我们指定资源的来源例如把相机作为图片的来源。最后，我们禁止编辑选择的图像，当然这取决于你是否需要禁止编辑。

上述步骤还少了一步，那就是将图片选择器呈现到到屏幕上。请注意，因为这个演示程序应该只在iPhone设备上运行，而不是通用的，我们只是以模态的形式来呈现图像选择器。你如想要在iPad上实现，那么你必须以`popover`的形式来呈现它。看[这里](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIImagePickerController_Class/)（在引言部分），了解更多信息。


```
@IBAction func pickPhoto(sender: AnyObject) {
    if UIImagePickerController.isSourceTypeAvailable(UIImagePickerControllerSourceType.SavedPhotosAlbum) {
        ...

        presentViewController(imagePicker, animated: true, completion: nil)
    }
}

```

完成了上述步骤，这意味着每次你点击“选择图片”按钮，图片选择器控制器将被显示在屏幕上。您可以马上试一试，但是不要期望现在能够做什么。

下一步是实现两个委托方法，我们需要知道用户何时点击图片选择器控制器的取消按钮，或者选择图片，并采取适当的动作。我们先从简单的来，当用户点击了取消按钮：



```
func imagePickerControllerDidCancel(picker: UIImagePickerController) {
    dismissViewControllerAnimated(true, completion: nil)
}
```

正如你看到的，我们做的无非就是简单地让图像选择器视图消失。

在接下来的方法中，我们要执行四个不同的任务：

+ 要设置拾取的照片作为图像视图的图像。
+ 要临时存储被拍摄的图像到沙盒目录下（随后我们会看到为什么要这样做）。
+ 要显示删除图片按钮，并同时隐藏选择按钮。
+ 要关闭图片选择器。

来看上述代码

```
func imagePickerController(picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [NSObject : AnyObject]) {
    imageView.image = info[UIImagePickerControllerOriginalImage] as? UIImage
    imageView.contentMode = UIViewContentMode.ScaleAspectFit

    saveImageLocally()

    imageView.hidden = false
    btnRemoveImage.hidden = false
    btnSelectPhoto.hidden = true

    dismissViewControllerAnimated(true, completion: nil)
}
```

让我们来看一下上述代码做了什么事情。我们使用名叫`info`的字典中的`UIImagePickerControllerOriginalImage`键值啦访问用户的选择的照片。因为我们不允许编辑，所以我们需要得到的是原始图像。如果允许编辑，我们需要获取被编辑的图片。我们在给图片设置了图像之后，我们指定`contentMode`的的值，以便让图像能够正确的显示。

接着就是`saveImageLocally`方法，很明显这是我们马上需要实现的自定义方法。如果我们不处理`CloudKit`，而只是存储图像到沙盒目录将是完全没有意义的。我们需要创建一个文件URL（一种特殊的NSURL对象）来指向该文件，并把它提供给`CloudKit`，让它可以找到图像并上传到iCloud中。

最后，我们正确地隐藏和显示必要的子视图，隐藏了视图控制器。移看`viewDidLoad`方法，你会发现上面的子视图的初始状态已经被设置好。

现在我们来实现saveImageLocally方法。然而，在这之前，让我们去到类的顶部（IBOutlet属性声明的下面）声明一个属性：

```
var imageURL: NSURL!
```

这就是我前面提到的NSURL对象，在实现它之前让我们来先声明两个属性。你看到他们就会明白他们是什么：

```
let documentsDirectoryPath = NSSearchPathForDirectoriesInDomains(.DocumentDirectory, .UserDomainMask, true)[0] as! NSString

let tempImageName = "temp_image.jpg"
```

来看新的自定义方法

```
func saveImageLocally() {
    let imageData: NSData = UIImageJPEGRepresentation(imageView.image, 0.8)
    let path = documentsDirectoryPath.stringByAppendingPathComponent(tempImageName)
    imageURL = NSURL(fileURLWithPath: path)
    imageData.writeToURL(imageURL, atomically: true)
}
```

一开始我们通过`UIImageJPEGRepresentation（...）`方法将图像转换为一个`NSData`对象。然后，拼接图片完整的文件路径，这样我们把它转换成一个文件URL对象。当然，我们存储`ImageUrl`到前面定义的变量中。最后，我们数据对象到写到指定的路径。

我说过我们需要临时存储摄取的图像，这意味着，在某些时候我们必须从文件目录中删除它（它不会自行删除）。最好的办法是在`dismiss（_:) IBAction`方法我们将弹出当前视图控制器。我们需要做的就是在弹出之前检查`imageURLproperty`是否存在，如果它具有一个有效的值，就删除所指向的文件。很简单吧，让我们来看看：

```
@IBAction func dismiss(sender: AnyObject) {
    if let url = imageURL {
        let fileManager = NSFileManager()
        if fileManager.fileExistsAtPath(url.absoluteString!) {
            fileManager.removeItemAtURL(url, error: nil)
        }
    }

    navigationController?.popViewControllerAnimated(true)
}
```

我们的工作还剩下一步。我们必须现实删除图像按钮，隐藏原来的图片。代码如下：


```
@IBAction func unsetImage(sender: AnyObject) {
    imageView.image = nil

    imageView.hidden = true
    btnRemoveImage.hidden = true
    btnSelectPhoto.hidden = false

    imageURL = nil
}
```

当我们选择了图片以后，我们不仅隐藏图像和现实删除图像按钮，还需要将imageview的值设为nil，我们有必要将ImageUrl属性设置为nil。

我们的演示应用程序的第一部分已经准备好了。你可以看到它是如何工作的，当然，你可以自己测试一下。

![这里写图片描述](http://img.blog.csdn.net/20150515221409193)


##创建一条新的cloudKit记录

现在让我们来完成有关CloudKit的第一个重要任务。在这一部分，我们将让我们的应用程序能够存储数据到iCloud，具体的说应该存储到Xcode为我们创建的默认容器中。我在前面列举过可以存储的数据类型，但我们不打算全部使用他们。通过保存笔记这个演示应用程序，我们将使用以下类型：

+ String: 笔记的标题和正文都会是字符串类型。
+ NSDate: 我们将存储笔记的最后修改时间，虽然我们没有必要这么做，但是为了演示`NSDate`的用法，我还是决定要存储这个。
+ Asset: 图片将会以Asset的形式存储到iCloud中，.

其余的数据类型也很容易使用，而且他们都与字符串数据类型相似。只有一个例外是，那就是CLLocation类型。在这种情况下，必须要特殊的处理，在这里我们不会处理这个数据。如果您的应用程序需要保存CLLocation类型，我建议你查看官方文档，你可以找到相当不错的指导。


我还要强调一件事。如果用户在日记中没有选择图片，我们会使用默认的图片（no_image.png）。通过这种方式，我们确保会在任何情况下都会有图片上传。

CloudKit的记录是由CKRecord类表示。我们需要创建这样一个对象并赋值以提供给CloudKit存储到数据库中，方法就像我们使用字典一样。详细的说，需要做四件事在记录被保存之前：

+ 创建一个唯一的标识符用作记录的键。更确切地说，我们不仅需要创建这样一个键，而且我们必须找到一种方法让这些键是唯一的。
+ 要创建一个CKRecord对象，设置它所有应该存储的值。
+ 指定容器和该数据应存储到数据库中。
+ 执行保存方法。

进入`saveNote（：_）`IBAction方法，并添加以下内容。我们必须确保我们保存的数据不为空：

```
@IBAction func saveNote(sender: AnyObject) {
    if txtNoteTitle.text == "" || textView.text == "" {
        return
    }

}
```

当然，在实际应用中，我们需要一条信息来提示用户输入最少的字符，或者其他的方法。当时对于目前来说，上述方法更加完美。


使用当前的时间戳是一个很好的方式来确保我们每产生新的记录标识将是独一无二的。使用的`NSDate`类的`thetimeIntervalSinceReferenceDate（）`方法来获取时间戳，它是以`abc.def`（其中ABC和DEF都是数组）的形式返回。点（.）在标识符中是无关紧要的，因为只有整数部分才对我们有用。所以，我们要做的很简单：

+ 我们会得到当前时间戳作为字符串值。
+ 我们将它分割为整数和小数部分。
+ 我们将使用的整数部分作为新记录的标识。

代码在这

```
@IBAction func saveNote(sender: AnyObject) {
    ...


    let timestampAsString = String(format: "%f", NSDate.timeIntervalSinceReferenceDate())
    let timestampParts = timestampAsString.componentsSeparatedByString(".")

    let noteID = CKRecordID(recordName: timestampParts[0])

}
```

我们创建了一个名为`noteID`的`CKRecordID`对象。这很关键，我们将在下面的部分用到，我们创建了一个新的`CKRecord`对象，我们设置除图片外所有需要保存的数据。


```
@IBAction func saveNote(sender: AnyObject) {
    ...

    let noteRecord = CKRecord(recordType: "Notes", recordID: noteID)

    noteRecord.setObject(txtNoteTitle.text, forKey: "noteTitle")
    noteRecord.setObject(textView.text, forKey: "noteText")
    noteRecord.setObject(NSDate(), forKey: "noteEditedDate")    
}
```

上述代码做了两件事：首先，我们创建了`CKRecord`对象，`noteID`使用的是我们前面所说的方法。然后我们可以非常方便的设置所有需要保存的数据。

现在我们处理图像的保存。正如我已经提到，如果用户已经选择图片，我们会使用用户选择的图片。如果没有选择的话，我们使用默认的图片。请注意，首先我们创建一个对象`CKAsset`（用于图片等），然后保存到`noteRecord`对象。


```
@IBAction func saveNote(sender: AnyObject) {
    ...

    if let url = imageURL {
        let imageAsset = CKAsset(fileURL: url)
        noteRecord.setObject(imageAsset, forKey: "noteImage")
    }
    else {
        let fileURL = NSBundle.mainBundle().URLForResource("no_image", withExtension: "png")
        let imageAsset = CKAsset(fileURL: fileURL)
        noteRecord.setObject(imageAsset, forKey: "noteImage")
    }    

}
```

在接下来的步骤中，我们必须指定所使用的应用程序（这里是默认的容器)和数据库。据我的介绍说，有两种类型的数据库：公共和私人。在本演示中，我们将使用私人数据库。：


```
@IBAction func saveNote(sender: AnyObject) {
    ...

    let container = CKContainer.defaultContainer()
    let privateDatabase = container.privateCloudDatabase
}
```

现在我们可以将记录保存到`iCloudKit`

```
@IBAction func saveNote(sender: AnyObject) {
    ...

    privateDatabase.saveRecord(noteRecord, completionHandler: { (record, error) -> Void in
        if (error != nil) {
            println(error)
        }
    })

}

```

通过上面的步骤你现在可以使用`CloudKit`存储数据到`iCloud`中。第一次保存记录，记录类型也会自动创建（相当于数据库中建表）。重要的是要记住，如果你需要更改记录数据，并添加或删除“字段”，你必须先删除`CloudKit Dashboard`平台的记录类型。在下面部分我们会看到关于`Dashboard`的几件事情。


这一点我应该提前告诉你，我们需要更新现有的记录还会提到这个。但现在，我们几乎完成。

我故意说“几乎”，是因为我们还需要考虑一种情况，那就是防止用户在数据正在保存的时候对数据进行操作。在真正的应用程序中，你应该允许用户进行操作，但是在这个deme中，我们不会那样做。我们会显示一个活动指示器，并阻止访问子视图，直到一切数据保存完成。


在EditNoteViewController的界面编辑器中，你可以找到一个UIView和一个活动指示器在它的子视图。它已经被连接到一个叫做`viewWait`（原因很明显）的IBOutlet属性。现在，这个视图（连同指示器）是隐藏的，所以我们将使其可见，我们会带它在前面，这样就可以禁止用户对note进行操作。但是这只完成了一半，因为它不能“遮住”导航栏。因为导航栏始终处于任何子视图的顶部，因此，在现实`viewWait`的同时，我们将隐藏导航栏。直到数据保存完成以后，我们会把一切都恢复原样。


所以，在`saveNote（_ :)`方法的开始部分。在标识符被创建的下面添加以下代码：


```
@IBAction func saveNote(sender: AnyObject) {
    if txtNoteTitle.text == "" || textView.text == "" {
        return
    }

    viewWait.hidden = false
    view.bringSubviewToFront(viewWait)
    navigationController?.setNavigationBarHidden(true, animated: true)

    ...    
}
```

直到完成以后，我们需要恢复我们刚才的操作，在完成方法中添加以下代码，注意我们是在修改界面，所以必须在主线程中执行
	
```
@IBAction func saveNote(sender: AnyObject) {
    ...

    privateDatabase.saveRecord(noteRecord, completionHandler: { (record, error) -> Void in
        if (error != nil) {
            println(error)
        }

        NSOperationQueue.mainQueue().addOperationWithBlock({ () -> Void in
            self.viewWait.hidden = true
            self.navigationController?.setNavigationBarHidden(false, animated: true)
        })
    })

}
```

请注意在日记保存完成之后并控制器不会自动消失，需要点击关闭按钮。


现在你可以测试一下我们所做的。无论只是运行在虚拟机还是运行在真机，只需要保证已经连接到`iCloud`，尤其是对真机老说，确认已经升级`iCloud Drive`（如果你还没有那样做），你可以在[这里](https://developer.apple.com/library/ios/documentation/DataManagement/Conceptual/CloudKitQuickStart/CreatingaSchemabySavingRecords/CreatingaSchemabySavingRecords.html#//apple_ref/doc/uid/TP40014987-CH3-SW12)看到如何创建一个iCloud账户。

![这里写图片描述](http://img.blog.csdn.net/20150515221505665)

##在CloudKit Dashboard浏览数据

我们使用cloud框架创建的所有数据都可以在一个叫做 `CloudKit Dashboard`的平台上浏览和修改，你可以在这里访问并登陆。

![这里写图片描述](http://img.blog.csdn.net/20150515221532419)


在右上角你可以看到我们开发使用的容器，你可以选择合适的容器如果有其它的容器的话，而不仅如此，我们还可以看到这里做了最常见的任务。也许，你只是来浏览一下数据而已。你可以在这里创建记录类型而不是让它自动创建里。如果你这样做（在大多数情况下是没有意义的），请确保你的属性和你程序里的保持一致。

对于我们的demo来说，你可以点击左侧菜单的 `Record Types` 选项，你会获得所有已经存在的的记录类型，默认会有Users，当然还有我们自己的notes，见下面截图，在右侧会有一个窗口显示所有的字段。你可以添加删除表通过点击垃圾桶按钮或者加号按钮。

![这里写图片描述](http://img.blog.csdn.net/20150515221646243)

为Notes记录类型可以在私人`Datacategory`的`Default Zone`可以看到。还记得我们把数据写在私人数据库嘛，这就是为什么这是你可以找到他们的原因。他们显示在面板中间，如果你点击任何记录就可以在右侧看到数据。您可以使用在顶部的添加或者删除按钮，除非非常有必要，否则这不是一个好主意。

![这里写图片描述](http://img.blog.csdn.net/20150515221721779)

注意，在左下区域中的显示的是开发环境。确保选择的是`Development `在开发过程中。在某些时候，但是你需要切换到生产模式，并部署该环境的容器架构。在这种情况下，打开菜单，在那里你会发现标题为`Deploy To Production`的按钮。

此外，在同一个地方，你可以找到`Reset Development Environment`，它会删除在开发模式下的所有数据。

![这里写图片描述](http://img.blog.csdn.net/20150515221802527)

## 拉取数据

使用`CloudKit `的逻辑和使用数据库很相似的，保存和修改数据总是比查询要困难一些，到现在为止，我们已经完成了保存功能，还没有做任何修改功能。现在让我们来做简单的部分，我们来查询所有储存在iCloud默认容器中得用户私有数据来显示给用户。


正如你想的那样，我们从`EditNoteViewController.swift`文件切换到`ListNotesViewController.swift`。我们会看到还没有被实现的方法，现在来完成取回数据的逻辑。


使用表格的第一件事就是需要一个数组作为数据源，设置数组内容的类型是非常重要的，对我们接下来的工作会有很大的影响，最好的选择就是让数组可以接受`CKRecords`类型，让我来告诉你为什么。

我们从iCloud拉取的数据是CKRecord类型的对象，我们可以直接把他们添加到数据。

我们可以访问所有的属性，尤其是我们需要在表格中现实日记的详细内容。

选择（通过点击）传递`CKRecord`对象到 `EditNoteViewController`类中将会非常的简单。


从`EditNoteViewController` 类中获得的`CKrecord`对象可以直接显示详细内容。


我们在`ListNotesViewController.swift` 的顶部添加以下定义。

```
var arrNotes: Array<CKRecord> = []
```

下一步要创建一个自定义的方法来从iCloud获取我们需要的数据，那么我们需要什么数据呢？


问题的答案要根据我们应用程序需要实现的功能，你可以拉取一条数据，或者多条特定条件的数据，或者只是简单的获取所有的数据。在这个demo中，我们不会用前两种情况，

如果你访问第二个链接，你会看到可以谓词(NSPredicate)来筛选和限制查询的记录。实际上，查询所有的数据也会用到谓词，但是非常的简单。

说了那么多，我们回到代码上，我们的目标就是访问默认的容器和私有数据库，创建一个查询对象然后执行它来获取我们需要的数据。创建一个新的方法：

```
func fetchNotes() {
    let container = CKContainer.defaultContainer()
    let privateDatabase = container.privateCloudDatabase
    let predicate = NSPredicate(value: true)

    let query = CKQuery(recordType: "Notes", predicate: predicate)
}
```

可以看到， `CKQuery`接受两个参数，第一个是记录名，第二个是谓词对象，最后当然是执行查询:

```
func fetchNotes() {
    ...

    privateDatabase.performQuery(query, inZoneWithID: nil) { (results, error) -> Void in
        if error != nil {
            println(error)
        }
        else {
            println(results)

            for result in results {
                self.arrNotes.append(result as! CKRecord)
            }

            NSOperationQueue.mainQueue().addOperationWithBlock({ () -> Void in
                self.tblNotes.reloadData()
                self.tblNotes.hidden = false
            })
        }
    }
}
```


可以看到，在没出错的情况下，完成之后会返回一个数据作为结果，首先我们把所有数据放在arrNotes（我们前面定义的）数组中，然后通过简单的刷新表格来显示数据。还是要注意在主线程中完成。


注意上面我们添加了一行输出代码，这是为了我们可以在控制台中看到我们拉取的数据。

我们在viewDidLoad调用上述方法。

```
override func viewDidLoad() {
    ...

    fetchNotes()
}
```


现在所有在`CloudKit`中存在的数据都会被拉取下来，但是还不会显示在表格中，我们还需要实现一个委托方法，先看简单的：

```
func tableView(tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return arrNotes.count
}


func tableView(tableView: UITableView, heightForRowAtIndexPath indexPath: NSIndexPath) -> CGFloat {
    return 80.0
}

```
对于所有在arrNotes中得记录，我们获取并显示笔记的标题，最后编辑日期和所选择的图片。我们不会显示笔记的正文因为它没有必要显示在列表中。日期和图片有一些麻烦，先看简单的标题。

```
func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    var cell = tableView.dequeueReusableCellWithIdentifier("idCellNote", forIndexPath: indexPath) as! UITableViewCell

    let noteRecord: CKRecord = arrNotes[indexPath.row]

    cell.textLabel?.text = noteRecord.valueForKey("noteTitle") as? String

    return cell
}
```


上述代码就是配置每一个cell，从arrNotes中获取记录，然后显示笔记的标题到单元格的标签中。

对于最后修改日期来说，我们在显示之前需要把它转换为字符串类型，我们使用NSDateFormatter来指定自定义的格式来显示。

```
func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    ...

    let dateFormatter = NSDateFormatter()
    dateFormatter.dateFormat = "MMMM dd, yyyy, hh:mm"
    cell.detailTextLabel?.text = dateFormatter.stringFromDate(noteRecord.valueForKey("noteEditedDate") as! NSDate)

    return cell
}
```

如果你曾经使用过的NSDate对象，那么你知道，如果没有NSDateFormatter的帮助，我们不能把一个日期作为字符串。你可以改变上面的日期格式，根据您希望的方式进行定制。


当我们使用返回的图片时，在拉取数据的时候，所有的数据都被存储在应用程序的缓存文件夹中，那意味着所有的图片每次我们拉取数据，都会存在缓存中直到程序退出。现在所有的图片以`CKAssetobject`的形式存在，对象包括临时文件的路径，我们需要读取图片被显示到单元格中，


```
func tableView(tableView: UITableView, cellForRowAtIndexPath indexPath: NSIndexPath) -> UITableViewCell {
    ...

    let imageAsset: CKAsset = noteRecord.valueForKey("noteImage") as! CKAsset
    cell.imageView?.image = UIImage(contentsOfFile: imageAsset.fileURL.path!)
    cell.imageView?.contentMode = UIViewContentMode.ScaleAspectFit

    return cell
}
```

这一部分也到了尾声，再次运行程序，如果你还没有创建笔记，那就是最这个的最后时机，看输出控制台，试着输出临时文件夹的路径当下载图片的时候。

![这里写图片描述](http://img.blog.csdn.net/20150515221809683)

##修改和更新记录


保存和拉取是非常重要的功能，但是修改和更新也同样重要，在iCloudKit中，修改记录并不难，我们要做的就是修改已经存在的CKrecord记录，然后用我们已经实现的方法来保存他们。在这个demo程序中，当从表格中选择一条笔记的时候将一个`CKrecord`从 `ListNotesViewController` 传递到`EditNoteViewController`类是非常简单的。

打开`EditNoteViewController.swift`类，在顶部定义一下变量


```
var editedNoteRecord: CKRecord!
```

`editedNoteRecord `是一个`CKRecord `对象，在笔记修改过后的`CKRecord `将会赋值给它


让我们回到`ListNotesViewController.swift`文件，定义另一个变量:


```
var selectedNoteIndex: Int!
```


我们将把所选的下标存在上面定义的变量中，以便我们将`properCKRecord `从`arrNotes `数组中传递给视图控制器。

现在要实现一个新的委托方法:

```
func tableView(tableView: UITableView, didSelectRowAtIndexPath indexPath: NSIndexPath) {
    selectedNoteIndex = indexPath.row
    performSegueWithIdentifier("idSegueEditNote", sender: self)
}
```

上述代码，我们让响应了表格的点击事件，我们要做的非常简单，我们先存储被点击的下标，
然后跳转到`EditNoteViewController `，当然这对对于编辑笔记还是不够的，我们需要传递对象到`theEditNoteViewController `

```
override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
    if segue.identifier == "idSegueEditNote" {
        let editNoteViewController = segue.destinationViewController as! EditNoteViewController

        if let index = selectedNoteIndex {
            editNoteViewController.editedNoteRecord = arrNotes[index]
        }
    }
}
```


这里有一个重要的细节，我们要使用if来避免程序的崩溃，`theselectedNoteIndex `只在编辑的时候有有效的值，当创建新的笔记的时候为nil。

让我们打开`EditNoteViewController.swift`文件。在这里我们的首先是从我们传递过来`CKRecord`（theeditedNoteRecord对象）得到的所有数据，并将其正确设置为子视图。所以，在`viewDidLoad`方法添加以下代码段。：


```
override func viewDidLoad() {
    ...

    if let editedNote = editedNoteRecord {
        txtNoteTitle.text = editedNote.valueForKey("noteTitle") as! String
        textView.text = editedNote.valueForKey("noteText") as! String
        let imageAsset: CKAsset = editedNote.valueForKey("noteImage") as! CKAsset
        imageView.image = UIImage(contentsOfFile: imageAsset.fileURL.path!)
        imageView.contentMode = UIViewContentMode.ScaleAspectFit

        imageURL = imageAsset.fileURL

        imageView.hidden = false
        btnRemoveImage.hidden = false
        btnSelectPhoto.hidden = true
    }
}
```

在一开始检查editedNoteRecord是否是一个有效的值是非常重要的，因为在如果editedNoteRecord为nil的话在载入EditNoteViewController的时候会发生奔溃。

最后我们需要做的就是保存笔记的变化到iCloud中，我们已经实现了保存方法，我们不需要创建一个新的。我们只需要做一些小修改在原来的`saveNote(_:) IBAction`方法上

```
@IBAction func saveNote(sender: AnyObject) {
    if txtNoteTitle.text == "" || textView.text == "" {
        return
    }

    viewWait.hidden = false
    view.bringSubviewToFront(viewWait)
    navigationController?.setNavigationBarHidden(true, animated: true)

    let timestampAsString = String(format: "%f", NSDate.timeIntervalSinceReferenceDate())
    let timestampParts = timestampAsString.componentsSeparatedByString(".")
    let noteID = CKRecordID(recordName: timestampParts[0])

    let noteRecord = CKRecord(recordType: "Notes", recordID: noteID)

    ...    
}
```

最后一行代码我们创建了CKRecord对象，然后，让我们编辑了笔记以后，但是我们已经有了一个对象，显然不应该再创建一个对象，对于这个疑惑，结论很简单：我们将判断是否修改了笔记,我们会将editedNoteRecord赋值给noteRecord，而不会重新创建一个对象。所以现在无论是新建还是更新都是一样的。

```
@IBAction func saveNote(sender: AnyObject) {
    if txtNoteTitle.text == "" || textView.text == "" {
        return
    }

    viewWait.hidden = false
    view.bringSubviewToFront(viewWait)
    navigationController?.setNavigationBarHidden(true, animated: true)

    var noteRecord: CKRecord!

    if let editedNote = editedNoteRecord {
        noteRecord = editedNote
    }
    else {
        let timestampAsString = String(format: "%f", NSDate.timeIntervalSinceReferenceDate())
        let timestampParts = timestampAsString.componentsSeparatedByString(".")        
        let noteID = CKRecordID(recordName: timestampParts[0])

        noteRecord = CKRecord(recordType: "Notes", recordID: noteID)
    }

    ...

}
```

现在可以再次测试程序，编辑已经存在的笔记，你可以看到你所做的改变一旦保存就可以在iCloud中体现

##更新笔记列表

如果你已经测试过，那么你肯定已经注意到，创建一个新的笔记，或更新现有的笔记列表中`ListNotesViewController`视图控制器并没有更新，除非关闭并重新运行该应用程序。在这一部分，我们将解决这个问题，但我要强调一件事。这是完全没有意义让程序重新获取来自CloudKit所有记录在每次保存的时候，因为这会导致两个（不必要的）事情：

笔记列表不会实时更新。相反，只有在所有的记录都被下载才会更新。

重新获取所有记录是一个浪费资源的方法，只是因为一个新的已添加，或编辑现有的笔记。

因此，一个更合适的解决方案来保持列表是最新的。既不是在添加笔记的时候，也不是在修改的时候。而是在返回`ListNotesViewController`时候。这意味着，我们需要使用代理模式，这是最简单的方式来达到我们的目的。

我们来一步一步解决，首先来到`EditNoteViewController.swift`文件创建一个新的协议，包含一个委托方法。

```
protocol EditNoteViewControllerDelegate {
    func didSaveNote(noteRecord: CKRecord, wasEditingNote: Bool)
}
```

正如你看到的，这个方法接受两个参数。`noteRecord`是我们传递给`ListNotesViewController`类的`CKRecord`对象。第二个表示该记录是否是新的，或者经过编辑。这非常重要，因为如果该记录是新的，它只是将其添加到arrNotes数组。如果它是编辑后的，name需要替换`arrNotes `的其中一个元素。

接下来就是声明委托对象，在`EditNoteViewController `的顶部

```
var delegate: EditNoteViewControllerDelegate!
```

现在，`saveNote（_:) IBAction`需要两个标识符。首先，我们需要使用一个标志位表明是否保存新的或现有的笔记记录。我们在方法中声明并设置标志的值，在这里我们区分是否是一个新的笔记。接下来，在这里你可以看到如何`isEditingNote`标志使用（注意注释）：

```
@IBAction func saveNote(sender: AnyObject) {
    ...

    var noteRecord: CKRecord!
    var isEditingNote: Bool!   // Flag declaration.

    if let editedNote = editedNoteRecord {
        noteRecord = editedNote

        isEditingNote = true // True because a note record has been edited.
    }
    else {
        let timestampAsString = String(format: "%f", NSDate.timeIntervalSinceReferenceDate())
        let timestampParts = timestampAsString.componentsSeparatedByString(".")        
        let noteID = CKRecordID(recordName: timestampParts[0])

        noteRecord = CKRecord(recordType: "Notes", recordID: noteID)

        isEditingNote = false   // False because it's a new note record.
    }

    ...
}
```

现在，我们调用我们先前声明的方法。要做到这一点，在这里我们将创建一个else分支来指示保存过程是否成功：

```
@IBAction func saveNote(sender: AnyObject) {
    ...

    privateDatabase.saveRecord(noteRecord, completionHandler: { (record, error) -> Void in
        if (error != nil) {
            println(error)
        }
        else {
            self.delegate.didSaveNote(noteRecord, wasEditingNote: isEditingNote)
        }

        NSOperationQueue.mainQueue().addOperationWithBlock({ () -> Void in
            self.viewWait.hidden = true
            self.navigationController?.setNavigationBarHidden(false, animated: true)            
        })
    })

}
```

我们在这个类中的工作已经结束，现在来到 ListNotesViewController.swift 文件，添加以下的协议。

```
class ListNotesViewController: UIViewController, UITableViewDelegate, UITableViewDataSource, EditNoteViewControllerDelegate
```

现在来实现其他的委托


```
override func prepareForSegue(segue: UIStoryboardSegue, sender: AnyObject?) {
    if segue.identifier == "idSegueEditNote" {
        let editNoteViewController = segue.destinationViewController as EditNoteViewController

        editNoteViewController.delegate = self

        ...
    }
}
```

最后，让我们来实现的委托方法。我们首先检查是否有新的或现有的记录被保存。在第一种情况下，我们只是将其添加到arrNotes数组。在第二种情况下，我们替换下标为selectedNoteIndex的对象，然后我们令该变量为零。最后，刷新tableview来现实数据：


```

func didSaveNote(noteRecord: CKRecord, wasEditingNote: Bool) {
    if !wasEditingNote {
        arrNotes.append(noteRecord)
    }
    else {
        arrNotes.insert(noteRecord, atIndex: selectedNoteIndex)
        arrNotes.removeAtIndex(selectedNoteIndex + 1)
        selectedNoteIndex = nil
    }


    if tblNotes.hidden {
        tblNotes.hidden = false
    }

    tblNotes.reloadData()
}
```

现在日志一旦创建或者更新，他就会显示在表格中，并立即被存储。

##删除记录

如果我们没有删除现有的笔记功能的话。我们的演示应用程序将是不完整的。我们只需要提供需要被删除的记录的标识符然后调用iCloudKit的删除方法。

我们将实现下面的委托方法，首先，我们需要获得记录的ID，和前面一样，操作容器和数据库 调用了`iCloudKit`的`deleteRecordWithID(…)`方法，如果没出错的话，我们也需要从`arrNotes`中删除`note`，更新表格，非常简单，来看下面的代码

```
func tableView(tableView: UITableView, commitEditingStyle editingStyle: UITableViewCellEditingStyle, forRowAtIndexPath indexPath: NSIndexPath) {
    if editingStyle == UITableViewCellEditingStyle.Delete {
        let selectedRecordID = arrNotes[indexPath.row].recordID

        let container = CKContainer.defaultContainer()
        let privateDatabase = container.privateCloudDatabase

        privateDatabase.deleteRecordWithID(selectedRecordID, completionHandler: { (recordID, error) -> Void in
            if error != nil {
                println(error)
            }
            else {
                NSOperationQueue.mainQueue().addOperationWithBlock({ () -> Void in
                    self.arrNotes.removeAtIndex(indexPath.row)
                    self.tblNotes.reloadData()
                })
            }
        })
    }
}

```

我再次提醒你，任何UI的更新必须在主线程，因此我们使用`NSOperationQueue`类。

如果你现在测试的应用程序，你可以简单地通过滑动单元格让红色删除按显示删除一行。

##总结

教程到这已经结束了，你会发使用CloudKit并不是一件很难的事情。有人可能会说这就是处理不同类型的数据库的方法。本教程中提供的信息帮助您在正确的方式，如果你是要在你的应用程序支持这样的功能。数据存储到iCloud会有良好的用户体验，因为用户能在多个设备使用到他们的数据。当然，并不是只有iCloud才能做到这一点，但是，在大多数情况下是可以使用的。这取决于你从现在开始寻找更多的资源，我希望你学到新的东西在这里。最后，我想最后说一句话，Apple使用iCloud在各个地方，为什么开发者不用呢？想到这一点，开始利用它。你可以在[这里](https://www.dropbox.com/s/gige1jsnz3eownm/CloudDemo.zip?dl=0)下载这个项目

版权: no_image.png 由Mathieu Jeannot提供

