# Back to the Future
---

> * 原文链接 : [Back to the Future](https://realm.io/news/swift-summit-javier-soto-futures/)
* 原文作者 : [Javier Soto](https://twitter.com/Javi)
* [译文出自 :  开发技术前线 www.devtf.cn](http://www.devtf.cn)
*  译者 : [sdq](http://shidanqing.net) 
* 校对者:  
* 状态 : 未校对

## 更优雅地使用Swift

谢谢各位，很高兴和大家一起来聊一聊Swift，感谢组织者把我们聚集在一起并让我在这里演讲。我的名字叫Javi，是来自Twitter的iOS工程师。

相比于Objective-C，我们在Swift里可以把很多事做得更好。我们已经在之前的演讲中看了不少例子，我想紧接着着他们，和大家谈论一下一种我想引入Swift的设计模式。这种设计模式会使我们的代码更为优雅。

##如今的异步API

今天我不会讲一些很空理论，而是通过实践和大家一起讨论。接下来会看到，在传统情况下我们是如何使用异步API的，并且在使用过程中存在的一些问题，尤其是对错误的处理。然后我会介绍一个简单的Future API，我们会看到它使如何提升我们的异步代码的。

让我们看看如今的问题在哪里？

```swift
struct User { let avatarURL: NSURL }
func requestUserInfo(userID: String, completion: (User?, NSError?) -> ())
func downloadImage(URL: NSURL, completion: (UIImage?, NSError?) -> ())

func loadAvatar(userID: String, completion: (UIImage?, NSError?) -> ()) {
    requestUserInfo(userID) { user, error in
        if let user = user {
            downloadImage(user.avatarURL) { avatar, error in
                if let avatar = avatar { completion(avatar, nil) }
                else { completion(nil, error!) }
            }
        } else { completion(nil, error!) }
    }
}
```

这是我们参照Objective-C异步API的方法写的Swift代码，其中包含大量的嵌套回调函数。我们可以看到loadAvatar方法，该方法的输入为用户ID，函数体完成对该用户头像的下载。其中，complete block需要传入一个image及一个error，但其实这个方法没有任何逻辑可言。方法本身不知道该如何进一步处理，其内部包含了一些异步方法来完成整个操作，包括采用requestUserInfo方法异步获取用户信息，以及一个下载图片的方法downloadImage。每一次我们都需要检查错误，执行完以后再调用completion block。

你们中的大部分可能已经习惯了这样的代码，但这样的模式非常不容易扩展。即使你没有使用过PHP，你也肯定看到过这样的场景（演讲者展示了一张异步代码层层嵌套的图）。所以让我们避免这样的情况发生吧，我们可以做的更好。此外，这段代码还有一些其它特殊的问题。其中的一个异步API将会查看参数类型。completion block需要传入的一个元祖或者两个数值；它们都是optional型的，因为下载失败的话image会返回nil，而下载成功没有错误的话error会返回nil。

这些问题会让你不爽，但你要知道问题其实一直存在，只是你从没有发现而已。在Objective-C中它们比较隐晦，因为任何类型都可以产生nil值。

如果我们看一下所有可能发生的情况，我们会发现总共有两种：一种是图片下载成功没有error，另一种是图片下载失败发生error。有趣的是其实还存在其余两种可能的情况，但完全没有任何意义。我的app该如何处理即有图片又有错误的状况呢？又或者当既没有图片又没有错误时，会发生什么？

##错误处理的重要性

我常常喜欢说我认为的计算机是不会犯错的完美机器，每当我们遇到了bug，那都是因为攻城狮所希望的与实际要求计算机去做的产生了冲突。如果我们没有一个很好地方式来表达如何能完成我们的设想，那很可能导致我们对计算机下达了错误的命令。

我认为错误处理非常重要，我们需要用合适的方式在应用程序中处理错误。上述这类API会令你在很多情况下用错，举个例子，你可能会首先检查error而不是先查看数值，虽然已经执行成功了，但你仍然会担心产生错误。

##我讨厌NSError

然后是NSError，我非常讨厌NSError,但我不能责怪Apple，因为老实说这的确是Objective-C中针对error最好的做法了。让我们再看一个例子看一下为什么NSError容易产生问题。

```swift
var error: NSError?
let string = NSString(contentsOfFile:path encoding:NSUTF8Encoding error:&error)

if string == nil {
    // Oops:
    if error.code == NSFileReadNoSuchFileError {
        // ...
    }
    else if ...
}
```

这段代码有错误，但是并不是非常明显。我们调用了Foundation API，如果查看文档，会发现我们完全不知道返回的是什么类型的错误，既没有提到领域，也没有提及错误代码。这段代码首先查看错误代码而不是先查看领域，这是没有任何意义的，因为在完全不同的领域可能产生相同的错误代码，非常容易误导开发者。另一种情况是，我们可以传nil给error，告诉Foundation“我不在乎error”，但是我认为鲁棒性强的软件应该认真处理错误。

我们每天都会遇到这样令人抓狂的情况，但我们无法责备开发者。如果我们的工具不能让这一切变得简单、让错误处理更方便，那我们只会变得更懒，我们将会向方法里传nil值，然后忽略这些error。我们不会去了解那些错误是什么，而是让它们显示在控制台然后继续。

##一个提议

这里我有一个提议：我们可以用自己定义的类型来封装我们API中可能产生的错误，而不是使用NSError。我们可以写一个protocol包含所有可能发生的错误类型。

```swift
protocol ErrorType { }

enum UserInfoErrorDomain: ErrorType {
    case UserDoesNotExist
    case UserRequestFailure(reason: String)
    case NetworkRequestFailure(reason: String)
}

extension NSError: ErrorType { }
```

举个例子，如果我们有一个API，我们可以声明一个遵循此protocol的枚举类型，包含了三种清楚的错误情况。这里甚至可以提供原因的信息，之后可以看到我们如何进行使用。如果确实想用NSError，我们也可以将其包含在内。

另一件看似矛盾但是却很酷的事情是，我们甚至可以创建一个NoError类型。

```swift
enum NoError: ErrorType { }

let error = NoError(?)
```


很奇怪是吧？由于它是空的枚举，我们实际上不能创建这种类型，因为它不存在任何构造函数。虽然没有意义，但是我们知道null类型是可以被构造的。如果API返回的error值是NOError类型，那API就会报错，我们不再需要通过单元测试进行检验，编译器就会替我们发现。

##采用Future建立Result类型

如Brian在之前的演讲里所叙述的那样，这里我们会在Result类型的基础上展开一些工作，Result无论成功或者失败都可以产生一个值。在这页slide里我做了点手脚，因为由于编译器的限制，目前这个代码在Swift里是无法实现的，你必须用class的形式来想办法实现，但我还是先用简单的形式在这里展示以免影响理解。

```swift
enum Result<T, E: ErrorType> {
    case Success(T)
    case Error(E)
}
```

现在让我们来看一下Future，我们如何来实现呢？希望通过研究其实现的过程，我们能够更加理解其价值，这会很有趣！那什么是Future呢？你可能在其它语言或者框架里听到过它的另外一个名字——Promise。它们的概念是完全相同的。

```swift
struct Future<T, E: ErrorType> {
    typealias ResultType = Result<T, E>
    typealias Completion = ResultType -> ()
    typealias AsyncOperation = Completion -> ()

    private let operation: AsyncOperation
}

```

Future包含了不一样的处理方式，可以对一段时间后才能获取数据先进行抽象的处理，比如网络延迟的情况。或许有人会说 “那是不是像闭包？”或者 “这不就是回调函数！” 不过Future的优势在于，我们可以像已经获取了数据那样进行操作，并且不用关心那些无关的异步操作，使我们的代码变得更简洁。

##实施Future

让我们开始实践吧。我们创建一个struct，类似Result它具有T和E的泛型，因为我们需要使用Result。我定义了一些别名用来帮助我们处理这些类型。我们之前看到过Result，同时需要一个可以触发的completion用于当你向Future要求，“现在给我值吧！”。你将会进到completion block里，它会获取到result并且不会返回任何东西。异步合并是一个闭包，当需要一个函数返回值的时候被调用。这时候completion块被调用，并且告知其函数工作的完成，并且不会返回任何值。Future封装了一些操作处理，我们可以在实例化中直接传入这些操作符。最重要的共有API是start方法，API的使用者可以通过此方法来告诉Future，“Ok，你现在可以去获取数据了，获取完之后交给我”。但是到现在为止用处不大，除了使用Result的方法不同外，它看起来完全和completion block一样。

```swift
struct Future<T, E: ErrorType> {
    init(operation: AsyncOperation) {
        self.operation = operation
    }

    func start(completion: Completion) {
        self.operation() { result in
            completion(result)
        }
    }
}
```

###map & andThen

Future真正酷的是在我们实现一些操作的时候。我想要实现map和andThen，让我们先从map开始吧。我们可以用map方法实现的一些功能。比如我们有了用户的ID，然后我们想取得用户头像的链接地址。我们创建一个方法来完成这个功能，另外我们还有一个已经创建出的方法用以下载用户信息并给我们一个基本的用户信息的结构体，也就是URL。如果我们想不用一堆回调来将其中一个转换为另一个，那可以先map，然后通过返回user内部的其它数据进行转换。

```swift
struct User { let avatarURL: NSURL }

func requestUserInfo(userID: String) -> Future<User, ErrorDomain>


func requestUserAvatarURL(userID: String) -> Future<NSURL, ErrorDomain> {
    return requestUserInfo(userID)
    .map { $0.avatarURL }
}
```

这就是函数签名的样子。比较一下它与map的函数签名你会发现很有意思，举个例子，两种类型都是出自swift标准库，Array和Optional。如果我们看一下类型，它们是完全一样的。他们会用函数来将获取的T类型值生成U类型的结果，其中T是容器内部的类型，而U将是新容器的类型。

```swift
struct Array<T> {
    func map<U>(f: T -> U) -> [U]
}

enum Optional<T> {
    func map<U>(f: T -> U) -> U?
}

struct Future<T, E: ErrorType> {
    func map<U>(f: T -> U) -> Future<U, E>
}
```

##实现map

那么让我们实现它吧。我们需要创建一个新的Future，因为它必须是U型的。我们用下列的方法来验证Future模式的机制。就像我们看到的，方法传给Future一个运算操作。为了转变数值，我们首先需要获得数值，所以我们调用开始方法从而得到结果。该结果可以是成功或失败，因此我们需要根据其情况处理。

```swift
func map<U>(f: T -> U) -> Future<U, E> {
    return Future<U, E>(operation: { completion in
        // Retrieve the value from self...
        self.start { result in

        }
    }
}
```

首先让我们看一下成功的情况。成功的情况下，如同类型签名那样，告诉我们需要通过F函数转化得到想要的数值 - 我们将再次在成功条件下运行它，然后会调用completion block。错误的情况下就更简单了。我们没有value，我们不能进行转换，只需要调用completion block。这就和幻灯片里显示的一样。

在错误情况下发生的短路情况和Brain之前展示的面向铁路对象编程概念基本一样。但是现在我们想要用最初Future给我们的value做一些事，但我们希望获取一个新的value。获取该value不像调用头像URL一样简单。获取该value会导致一些延迟，比如当我们需要另一个网络请求。

##声明andThen

这是行不通的，因为我们需要通过一个函数来返回另一个Future。因此，我们想要再Future API上声明一个Future和一个andThen函数。我们会这样使用，例如：如果我们有一个像刚才看到的请求头像URL，可以在之前的操作后紧接着下载图片，返回头像图片的Future。我们只需要调用andThen函数并传其它的函数。

```swift
func requestUserAvatarURL(userID: String) -> Future<NSURL, ErrorDomain>

func downloadImage(URL: NSURL) -> Future<UIImage, ErrorDomain>


func downloadUserAvatar(userID: String) -> Future<UIImage, ErrorDomain> {
    return requestUserAvatarURL(userID)
    .andThen(downloadImage)
}
```

这就是签名的样子，类似map，就像我说的重要的区别是，F函数不返回U，它返回的是一个新的Future。实现它非常非常相似。我们重新建立一个Future，初始化并取得value，然后选择Result。在成功的情况下，当我们在value上调用F，我们没有可以返回的value。现在我们有了另一个Future，我们需要启动它，当我们完成得到最后的Result。

在错误的情况下，我们需要对其短路。如果我们没有一个value，就不能转换它，以便继续做更多的操作。我们需要马上调用completion block。这就是整个的实现过程，你会意识到它看起来很像map。事实上，有趣的是你可以在其他情况下实现它，但我选择了单独实现来看看他们会做些什么。

让我们来看看已经完成的工作。我们从嵌套混乱复杂的回调函数以及每次单独调用都会涉及错误处理的情形，转变为通过一个声明方法来告诉我们需要获取头像：我们需要请求用户信息，将它map到头像的URL，然后下载图像。这不是关于代码的能力，它是关于我们在代码中要表达我们的意图的能力。不过其最大的优势在于，代码中不再有错误处理的内容，所有的error都会在运行中自动被识别。

```swift
func andThen<U>(f: T -> Future<U, E>) -> Future<U, E> {
    return Future<U, E>(operation: { completion in
        self.start { firstFutureResult in
            switch firstFutureResult {
                case .Success(let value): // ...
                case .Error(let error): // ...
            }
        }
    }
}

```

##Future的局限性

我希望你们会和我一样喜欢Future，Future也的确和我想的一样具有实用价值。但是，了解Future的缺陷也同样重要。文中的例子在我们的移动应用中很常见，会存在网络请求，会存在异步请求等等，但是问题是我们不仅仅只处理异步事件；另外，当返回值不只有一个值时Future就会失效，Future只能处理一个值，而当我们处理用户交互的时候，像手势识别，往往会存在多个值的情况。

另一个问题是我们在上文处理的情景并不是用户驱动的，就像交互时你不会告诉app “我现在准备触碰手机了”。 这些行为完全是用户决定的，程序无法预测。

##Signals

好消息是Future正在变得更好。Signals将会解决以上遇到的问题。Signals是一个非常有效的工具。

另一件令人兴奋得事情是我们不必自己去实现它。你可能已经听过了ReactiveCocoa可以用来实现signals并允许你的app使用这些概念。ReactiveCocoa 3是用swift写的，可以在Github上找到。
