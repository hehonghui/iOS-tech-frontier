iOS8 Swift文件管理教程
---

> * 原文链接 : [File Management Tutorial in iOS8 with Swift](http://www.ioscreator.com/tutorials/file-management-tutorial-ios8-swift)
* 原文作者 : [http://www.ioscreator.com](http://www.ioscreator.com/)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [HarriesChen](https://github.com/mrchenhao) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  未完成 / 校对中 / 完成 

When an iOS App is installed on a device, a folder structure will be created, including a tmp directory for storing temporary data. In this tutorial we are managing files in this tmp directory such as creating, reading and deleting files. This tutorial is built in iOS 8.1 with Xcode 6.1.

当一款iOS的App安装到设备上以后，将会创建一个文件结构，包含了用于存储临时文件的临时文件夹。这篇文章我们来讲解在临时文件夹中管理文件，包括创建读取和删除。教程基于iOS8.1和Xcode6.1

Open Xcode and create a new Single View Application. For product name, use IOS8SwiftFileManagementTutorial and then fill out the Organization Name and Organization Identifier with your customary values. Enter Swift as Language and make sure only iPhone is selected in Devices.

打开Xcode创建一个新的`Single View Application`项目，以`IOS8SwiftFileManagementTutorial `作为项目的名称，填写组织名称和组织标识符。选择Swift语言，确保Devices为iPhone。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54f8bc03e4b0d007a6fee8a4/1425587211870/?format=1500w)

Go to the Storyboard and drag four Buttons to the main View. Give the buttons the following titles. 

在故事版中拖四个按钮到主视图中，名称分别为以下：

* Create File
* List Directory
* View File Content
* Delete File


The Storyboard will look like this.
故事版看起来像这样
![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54f8d8bfe4b00d102a265006/1425594560844/?format=1500w)

Select all items by holding the Ctrl-key  and select the "Resolve Auto Layout Issues" button at the bottom-right(3rd button) and select Add Missing Constraints.

通过按住Ctrl键选择全部，然后点击在底部的第三个按钮选择`Resolve Auto Layout Issues`来添加缺少的约束。
![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54f8d98ce4b070bffa96534e/1425594775771/?format=750w)

The buttons will be placed below and next to each other, even when the device rotates. Select the Assistant Editor and make sure the ViewController.swift is visible. Ctrl and drag from the "Create File" Button to the ViewController class and create the following Action.

按钮依次放置，即使在设备旋转的时候，选择`Assistant Editor`并让ViewController.swift可见。按住Ctrl将`Create File`拖到`ViewController `类，创建以下动作。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54fb76ade4b03452415dab06/1425766065262/?format=750w)

Ctrl and drag from the "List Directory" Button to the ViewController class and create the following Action.

按住Ctrl将`List Directory`拖到`ViewController `类，创建以下动作。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54fb7951e4b0f473c20ec908/1425766739551/listFile-Action.png?format=750w)


Ctrl and drag from the "View File Content" Button to the ViewController class and create the following Action.

按住Ctrl将`View File Content`拖到`ViewController `类，创建以下动作。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54fb7989e4b0edea6489289e/1425766794964/?format=750w)

Ctrl and drag from the "Delete File" Button to the ViewController class and create the following Action.

按住Ctrl将`Delete File`拖到`ViewController `类，创建以下动作。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54fb7a06e4b0ecb31a5b46ea/1425766920438/?format=750w)


Go to the ViewController.swift file and add the following properties to the ViewController class

在`ViewController.swift`文件中添加下列属性。

```
var fileManager = NSFileManager()
var tmpDir = NSTemporaryDirectory()
let fileName = "sample.txt"
```

* The NSFileManager class lets you examine the contents of the filesystem and make changes to it.
* The NSTemporaryDirectory is a temporary directory for the current user
* A file with a filename of "sample.txt" will be used for this tutorial

* `NSFileManager `类让你查看文件系统的内容并改变它。
* `NSTemporaryDirectory `是一个当前用户的临时文件夹
* "sample.txt"文件将在本教程中用到

Before the Action methods will be implemented a helper method  enumerateDirectory will be created, which will be used to check for files inside a directory.

在动作方法实现之前先创建一个`enumerateDirectory`方法用来检查文件是否存在。

```
func enumerateDirectory() -> String? {
    var error: NSError?
    let filesInDirectory =  fileManager.contentsOfDirectoryAtPath(tmpDir, error: &error) as? [String]
        
    if let files = filesInDirectory {
        if files.count > 0 {
            if files[0] == fileName {
                println("sample.txt found")
                return files[0]
            } else {
                println("File not found")
                return nil
            }
        }
    }
    return nil
}

```

All files inside the temporary directory will be put into a array of type String and this array will be checked for the existence of our sample.txt file. When succesful, the filename will be returned, otherwise nil will be returned. Next, implement the createFile method.

临时文件夹中的所有文件将被放在一个字符串的数组中，这个数组将被用来检查sample.txt是否存在。如果存在，文件名会被返回。反则返回nil。接着实现文件创建方法。

```
@IBAction func createFile(sender: AnyObject) {
    let path = tmpDir.stringByAppendingPathComponent(fileName)
    let contentsOfFile = "Sample Text"
    var error: NSError?
        
    // Write File
    if contentsOfFile.writeToFile(path, atomically: true, encoding: NSUTF8StringEncoding, error: &error) == false {
        if let errorMessage = error {
            println("Failed to create file")
            println("\(errorMessage)")
        }
    } else {
        println("File sample.txt created at tmp directory")
    }
}
```

The sample.txt file is created with some content and written to the tmp directory with the writeToFile:atomically:encoding:error method. When this actions fails. an error message will be printed to the console. Next, implement the listDirectory method.

使用`writeToFile:atomically:encoding:error`将sample.txt被创建并写到临时文件夹中。如果失败，一个错误信息会被打印到控制台。接下来实现`listDirectory `方法。


```
@IBAction func listDirectory(sender: AnyObject) {
    // List Content of Path
    let isFileInDir = enumerateDirectory() ?? "Empty"
    println("Contents of Directory =  \(isFileInDir)")
}
```

The enumerateDirectory method is called, when there is no file present in the directory the "Empty" string is assigned to the isFileInDir variable. the ?? operator is the nil coalescing operator which evaluates an expression (enumerateDirectory() ) When true the optional value will be unwrapped, when false a default value will be returned ( "Empty" string). The Contents of the directory will be printed to the console. Next, implement the viewFileContent method.

`enumerateDirectory `被调用，当没有文件存在的时候将`isFileInDir `赋值为`Empty`。??操作符用来判断表达式`(enumerateDirectory() )`是否为nil，当为真的时候可选变量会被拆包。当为假的时候会有默认值"Empty"，文件夹的内容会被打印到控制台。接下来实现`viewFileContent `方法。

```
@IBAction func viewFileContent(sender: AnyObject) {
    let isFileInDir = enumerateDirectory() ?? ""
        
    let path = tmpDir.stringByAppendingPathComponent(isFileInDir)
    let contentsOfFile = NSString(contentsOfFile: path, encoding: NSUTF8StringEncoding, error: nil)
        
    if let content = contentsOfFile {
        println("Content of file = \(content)")
    } else {
        println("No file found")
    }
}
```

The contentsOfFile:encoding:error method puts the content of a file into a NSString. If the file is present, the content will be printed to the console, otherwise the "No file found" message will be printed to the console. Finally, implement the deleteFile method

`contentsOfFile:encoding:error`方法将文件内容放在一个字符串中。如果文件存在，将会被打印到控制台，如果不存在，将会打印'No file found'消息。最后来实现`deleteFile `方法。

```
@IBAction func deleteFile(sender: AnyObject) {
    var error: NSError?
        
    if let isFileInDir = enumerateDirectory() {
        let path = tmpDir.stringByAppendingPathComponent(isFileInDir)
        fileManager.removeItemAtPath(path, error: &error)
    } else {
        println("No file found")
    }
}
```

The removeItemAtPath:error method deletes the file. Build and Run the project, select the  buttons and look at the console to see the results of each action.

`removeItemAtPath:error`方法用来删除文件，编译并运行工程，选择按钮来观察控制台输出。

![](http://static1.squarespace.com/static/52428a0ae4b0c4a5c2a2cede/t/54fb82f2e4b03edc59f68802/1425769204078/?format=1000w)

You can download the source code of the IOS8SwiftFileManagementTutorial at the ioscreator repository on Github.

你可以在GitHub下载源码[IOS8SwiftFileManagementTutorial](https://github.com/ioscreator/ioscreator)