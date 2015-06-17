Swift 2 有哪些新特性
---

> * 原文链接 : [What’s New in Swift 2](http://www.raywenderlich.com/108522/whats-new-in-swift-2)
* 原文作者 : [Greg Heo](http://www.raywenderlich.com/u/gregheo)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [Sam Lau](https://github.com/samlaudev) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  未完成

At WWDC we found out the Swift team hasn’t been messing around sending crude watch drawings to each other like the rest of us — they’ve been hard at work on Swift 2.

在WWDC我们发现Swift团队已经没有浪费时间在无谓的地方，而是致力于改善Swift 2。

We’ll have plenty of written and video Swift 2 tutorials for you soon, but in the meantime I wanted to highlight the most exciting changes so you can be prepared for the great migration to Swift 2 in the fall.

我们将会为你编写和录制很多关于Swift 2的教程，但在此期间我想强调Swift最令人兴奋的改变，那么你可以在秋天为迁移到Swift 2做准备。

![](http://cdn2.raywenderlich.com/wp-content/uploads/2015/06/swift-new.jpg)

#Error Handling

#错误处理

As Ray mentioned in his [WWDC 2015 Initial Impressions](http://www.raywenderlich.com/108379/wwdc-2015-initial-impressions) post, error handling has been revamped in Swift 2. Instead of **NSError** objects and double pointers, we’re moving to a new system that looks similar to exception handling.

正如Ray在他的[WWDC 2015 Initial Impressions](http://www.raywenderlich.com/108379/wwdc-2015-initial-impressions)文章，错误处理已经在Swift 2改进了。我们已经迁移到新的系统就像异常处理，而不是**NSError**对象和双指针。

You may be familiar with code like this:

你可能对以下代码比较熟悉：

```
if drinkWithError(nil) {
  print("Could not drink beer! :[")
  return
}
```

Generally in Cocoa, you pass in a reference to an **NSError** object (an **inout** parameter in Swift) and then the method will assign the variable if there was a problem. However, the problem is that you can pass nil here to completely ignore the error; or, you can pass in the **NSError** but then never check it.

一般在Cocoa，你传入一个**NSError**对象的引用(一个**inout**参数在Swift)，然后方法会赋值给错误变量。但问题是你可以传入一个nil到这里来完全忽略这个错误；或者，你可以传入**NSError**但从不检查它。

Swift 2 adds additional safety to your error checking. You use the **throws** keyword to specify which functions and methods could throw an error. Then you have the **do**, **try**, and **catch** keywords for when you call something that could throw:

Swift 2 为错误检查添加额外保护层。你可以使用**throws**关键字来指定那个函数和方法能够抛出一个错误。然后当你调用某样东西时，可以用**do**, **try**和**catch**多个关键字来捕捉和处理错误。

```
// 1
enum DrinkError: ErrorType {
  case NoBeerRemainingError
}
 
// 2
func drinkWithError() throws {
  if beer.isAvailable() {
    // party!
  } else {
    // 3
    throw DrinkError.NoBeerRemainingError
  }
}
 
func tryToDrink() {
  // 4
  do {
    try drinkWithError()
  } catch {
    print("Could not drink beer! :[")
    return
  }
}

```

There are a few things to highlight here:

这里有几样东西需要强调的：

1. To create an error to throw, simply create an **enum** that derives from **ErrorType**.
2. You need to use the **throws** keyword to mark any function that can throw an error.
3. This throws an error, which will be caught in section 4.
4. Instead of **try** blocks, which might be familiar from other languages, you wrap any code that can throw an error in a **do** block. Then, you add the **try** keyword to each function call that could throw an error.

1. 为了创建一个错误可以抛出，只是创建一个继承**ErrorType**的**enum**。
2. 你需要使用**throws**关键字来标志任何函数可以抛出一个错误。
3. 这里抛出一个错误，它将会在section 4中被捕捉。
4. 你在一个**do**块中包含任何可以抛出一个错误的代码，而不是其他语言类似的**try**块中。然后，你添加一个**try**关键字到函数被调用的前面，而且这个函数能够抛出一个错误。

The new syntax is pretty lightweight and readable. Any API that currently uses **NSError** now uses this system, so we’ll be seeing a lot of it!

新语法是非常简洁和易读。任何API当前使用**NSError**以后都会使用这种错误处理方式。

![](http://cdn4.raywenderlich.com/wp-content/uploads/2015/06/throw-all-the-things-415x320.jpg)

#Binding

#绑定

With Swift 1.2, we lost the “pyramid of doom” and gained the ability to test binding multiple optionals in one line:

在Swift 1.2，我们失去了金字塔的厄运和能够在一行代码测试多个绑定的optionals：

```
if let pants = pants, frog = frog {
  // good stuff here!
}
```

That works just fine, but the issue for some people is that the “preferred” code path where all the optionals have some value is indented. That means you need to keep looking inside indented code blocks for the mainline part of your code, while the error conditions are outside.

这样勉强能够工作，但对于有些人需要缩进那个嵌套着很多optionals才能访问的值的“首选”的代码路径是一个问题。这意味着你需要深入查看缩进主线部分的代码块，而错误条件却在外面。

If only there were some way to check that some of the optionals don’t have a value, and then exit early! That’s exactly what Swift 2 offers with its guard statement:

如果有些方式来检查一些没有值的optionals，然后早点退出。这正是Swift 2提供的guard语句：

```
guard let pants = pants, frog = frog else {
  // sorry, no frog pants here :[
  return
}
 
// at this point, frog and pants are both unwrapped and bound!
```

Using **guard** means you can perform the optional binding (or any other operation, really) and provide a code block in the **else** to run if the condition fails. Then, you can continue on – in this case, the optionals **frog** and **pants** are now bound and are no longer optionals in scope.

使用**guard**意味着你可以执行optional binding (或其他操作)和如果条件失败就提供一个代码块在**else**运行。然后，你可以继续执行。在这种情况下，optionals **frog**和**pants**在作用域内被unwrap。

This should make for much clearer code since **guard** lets you specify the state you’re actually expecting rather than checking for the error case.

使用**guard**指定某种你希望得到状态而不是检查错误情况之后，使代码更加简洁。

> **Note:** If your’e still confused about why the **guard** statement is more useful than __if-else__ statements alone, check out Swift team member [Eric Cerney‘s post](http://www.raywenderlich.com/u/ecerney) on the [Swift guard statement](http://ericcerney.com/swift-guard-statement/).

> **注意：** 如果你仍然不明白为什么使用**guard**语句比**if-else**语句更加有用，请查看Swift团队[Eric Cerney‘s post](http://www.raywenderlich.com/u/ecerney)在[Swift guard statement](http://ericcerney.com/swift-guard-statement/)。

#Protocol Extensions

#协议扩展

Object-oriented? Functional? There’s one more to add to the front of the line of what Swift is: a protocol-oriented programming language!

面向对象编程？函数式编程？Swift其实还是一种面向协议的编程语言！

In Swift 1, protocols were like interfaces to specify a set of properties and methods that a class, struct, or enum would then conform to.

在Swift 1，协议就像接口一样可以指定一些属性和方法，然后类，结构体或枚举会遵循它。

Now in Swift 2, you can extend protocols and add default implementations for properties and methods. You can already do this with classes and structs — adding new methods to __String__ or __Array__, for example — but adding these to protocols now gives you a much wider reach.

现在在Swift 2，你可以扩展协议和给属性和方法添加默认实现。你之前已经可以在类或结构体添加新的方法到**String**或**Array**，但现在你可以添加这些到协议，这让你更加广泛地应用。

```
extension CustomStringConvertible {
  var shoutyDescription: String {
    return "\(self.description.uppercaseString)!!!"
  }
}
 
let greetings = ["Hello", "Hi", "Yo yo yo"]
 
// prints ["Hello", "Hi", "Yo yo yo"]
print("\(greetings.description)")
 
// prints [HELLO, HI, YO YO YO]!!!
print("\(greetings.shoutyDescription)")

```

Note that the __Printable__ protocol is now called __CustomStringConvertible__, which most Foundation objects conform to. With protocol extensions, you can extend wide swaths of the system with your own custom functionality. And rather than adding bits of custom code to many classes and structs and enums, you can write one general implementation and have it apply across a set of types.

注意**Printable**协议现在被命名为**CustomStringConvertible**，而大多数的Foundation对象都遵循__Printable__协议。有了协议扩展之，你可以用自定义功能来扩展系统。相比于向很多类、结构体和枚举添加少量的自定义代码，你可以编写一个通用实现，然后应用到不同的数据类型。

The Swift team has been busy doing this already — if you’ve ever used __map__ or __filter__ in Swift, you may have thought they would do better as methods rather than as global functions. Thanks to the power of protocol extensions, there are a new set of methods on collection types such as __map__, __filter__, __indexOf__, and more!

Swift团队已经忙着做这个了 - 如果你在Swift已经使用__map__或__filter__，你可能也认为以方法的方式比全局函数来使用它们更好。多亏了强大的协议扩展，已经有一些新的方法添加到集合类型，例如：__map__，__filter__，__indexOf__和更多！

```
et numbers = [1, 5, 6, 10, 16, 42, 45]
 
// Swift 1
find(filter(map(numbers, { $0 * 2}), { $0 % 3 == 0 }), 90)
 
// Swift 2
numbers.map { $0 * 2 }.filter { $0 % 3 == 0 }.indexOf(90) // returns 2
```

Thanks to protocol conformance, your Swift 2 code can be more concise and readable. With the Swift 1 version, you need to look at the calls inside out to understand what’s going on; in the Swift 2 version, the chain of functions is clear.

多亏了协议一致性，你的Swift 2代码会变得更加简洁和易读。在Swift 1版本，你需要查看调用函数内部来理解它的工作原理；在Swfit 2版本，函数链会变得清晰。

You’re also leveraging the power of protocol-oriented programming — check out the [WWDC session on this topic](https://developer.apple.com/videos/wwdc/2015/?id=408) and keep an eye out for future tutorials and articles here on the site!

如果你打算使用面向协议编程 - 请查看[WWDC session on this topic](https://developer.apple.com/videos/wwdc/2015/?id=408)和留意这个网站的教程和文章。

#Grab Bag

#汇总

There were a ton of things announced throughout all the sessions, so I want to highlight a few more quick things:

在WWDC大会中发布很多东西，所以我想强调几样重要的东西：

* __Objective-C generics__ – Apple has already started annotating all their Objective-C code so that types in Swift get the correct kind of optionality. That work continues with Objective-C generics, which will give Swift developers better type hinting. If you’re expecting a set of __UITouch__ objects or an array of strings, that’s exactly what you’ll get rather than a collection of __AnyObjects__.

* __Objective-C 泛型__ - Apple已经开始标注所有的Objective-C代码以便Swift类型能够获取正确类型的optional。使用Objective-C泛型也能正常工作，这样给Swift开发者更好的类型提示。如果你希望出现一些__UITouch__对象或字符串数组，那就会出现你想要的而不是一些__AnyObjects__。

* __Renamed syntax__ – __println__ has left us after only a year; now it’s plain old __print__ which now has a default second boolean argument set to __true__ on whether to print a newline or not. With the __do__ keyword focused on scope for error handling, do-while loops are now __repeat-while__. Similarly, there are many changes to protocol names such as __Printable__ becoming __CustomStringConvertible__.

* __Renamed 语法__ - __println__已经离开我们一年了；现在它是普通旧的__print__，现在它有第二个参数的默认值设置为__true__来决定是否换行。__do__关键字主要用来错误处理，do-while循环现在是使用__repeat-while__。类似地，有很多协议名都改变了，例如：__Printable__改为__CustomStringConvertible__。

* __Migrator__ – With all these small syntax changes, how are you going to get your codebase up to date? The Swift 1-to-2 migrator will come to the rescue and help bring things up to the latest standards and syntax changes. The migrator is even smart enough to update your code to use the new error handling and update your docblock comments to the new style of formatting!

* __Migrator__ - 有很多小的语法改变，你怎样使得你代码变得最新？Swift 1-to-2 migrator会将代码变成最新的标准和改变语法。这个migrator智能到能够更新你的代码使用新的错误处理，和更新块注释到新的格式风格！

* __Open source!__ – The big news for the nerds is that Swift is going open source when Swift 2 is released in the fall. Likely this will lead to Swift being used for more than just iOS development, which makes it even more important to learn. Plus, it will be a great opportunity to get a look under the hood and even to contribute back and get your name in the Swift compiler commit history. 

* __Open source!__ - 对码农有一个重大消息就是在秋天发布Swift 2的时候，Swift将会开源！这意味着不仅可以使用它来iOS开发，更重要的是学习它的源代码。不仅如此，这将是很好的机会来深入源代码，甚至为项目贡献代码，然后在swift编译器提交历史上留下你的名字。

#Where To Go From Here?
This is just a sampling of the many things released; for the full details, check out the [WWDC session videos](https://developer.apple.com/videos/wwdc/2015/) and the updated [Swift Programming Language book](https://itunes.apple.com/us/book/swift-programming-language/id1002622538?mt=11).

There will surely be more to come if anyone remembers the volume of changes between the first beta and the 1.0 release of Swift. We’ll be staying on top of all the updates here on the team, so keep an eye out for tutorials, book announcements, and videos as we dig into all the exciting changes to come.

What parts of Swift 2 are you most excited about? What would you like us to cover first? Let us know in the comments below!
