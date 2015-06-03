Swift扩展的三个微妙细节
---

> * 原文链接 : [3 Nuances of Swift Extensions](http://www.andrewcbancroft.com/2015/04/22/3-nuances-of-swift-extensions/)
* 原文作者 : [Andrew Bancroft](http://www.andrewcbancroft.com/)
* 译文出自 : [开发技术前线 www.devtf.cn](www.devtf.cn)
* 译者 : [samw00](https://github.com/samw00/) 
* 校对者:  
* 状态 :  校对中 

每当我初次翻看某文档时，我都走马观花似的快速阅过，还一边点着头一边喃喃自语说：“好！懂了，就这么回事！”，可是过后当我真正要运用到这些我以为已经理解了的知识点时，却发现实际情况和我想的往往不一样，每当这时我就懵了，心想：“哇哦...怎么回事？这和我想的完全不一样啊！文档里有说这事吗？”。

最近的几次讨论促使我扪心自问是否真正的理解了Swift中的扩展。我阅读过关于扩展的文档，并且我“认为”我自己对这块内容已经是理解的相当透彻了。可是这几次讨论，加上自己私下通过敲代码的验证，让我发现了我原先不曾注意到几个微妙的细节。

**更新**：这篇文章刚一发表，Swift社区就出手襄助并帮助我弄明白了我最根本的纠结点在哪。为此，我写了另一篇文章“[阐明Swift访问控制](http://www.andrewcbancroft.com/2015/04/24/clarifying-swift-access-control-hint-swift-isnt-c-sharp/)”进一步说明我之前的误解。为了避免犯我曾今犯过的错误，我建议大家去读一读。

## 三个关于扩展的微妙细节
对下面列出的三个细节的思考严重挑战了我之前对Swift扩展的理解：

1. Swift扩展对它所扩展类型的**visibility**。比如，扩展能访问被`private`所修饰的内容吗?
2. 定义扩展的位置是否对扩展的**visibility**有影响。比如我这有一个类型我想写个扩展，把扩展写在同一个源文件里和把扩展写在另一个文件里有什么区别吗？
3. 扩展里“成员”的默认访问修饰符以及是否给他们添加修饰对这个扩展作为一个类型的公共接口的影响。

在我开始之前，假设我有一个公共结构体`Person`。这个结构体有一些私有属性，`name`，`gender`，和`age`。用一个枚举把`Gender`封装了一下。这个结构体看起来如下：

```
public struct Person {
    private var name: String
    private var gender: Gender
    private var age: Int

    public init(name: String, gender: Gender, age: Int) {
        self.name = name
        self.gender = gender
        self.age = age
    }

    public func howOldArdYou() -> String {
        return formattedAge()
    }

    // 私有方法，用于下面分析扩展的`visibility`...
    private func formattedAge() -> String {
        switch self.gender {
        case .Male:
            return "I'm \(self.age)."
        case .Female:
            return "Not telling."
        }
    }

    public enum Gender {
        case Male
        case Female
    }
}
```
现在，就让我们给`Person`写个扩展，通过实践来弄清楚刚刚提到的三个小细节...

##扩展对类型的访问能力
当我提出第一个细节时，关于扩展对被扩展类型的访问能力时，我问了一个问题：“扩展能访问到被`private`修饰的内容吗?”。答案一开始出乎我的预料：能...扩展能访问到。

然而，这里就要考虑到第二个细节所涉及的问题，那就是：在**哪里**定义这个扩展是绝对有影响的。

###定义在同一个文件里
如果扩展和类型是在写同一个源文件里，则扩展**能**访问到在类型中被`priavte`所修饰的内容。

举个栗子，在Person.swift里定义一个`Person`的扩展就会允许这个扩展访问被`private`修饰的变量和方法

```
extension Person {
    func getAge() -> Int {
        return age // 尽管age是 --private--， 但编译成功
    }

    func getFormattedAge() -> String {
        return formattedAge() // 尽管 formattedAge是 --private--，但编译成功
    }
}
```
“这谁知道？！什么？？为啥？”，我当时就没想明白...

至于为什么把扩展写在同一个源文件里头会这样，我自己的推理是其实可以在写类型的时候，就把扩展的implementation当作类型的一部分给写了，这样的最终效果是一样的。

我在我要“扩展”的类型的**源文件**里，所以无论是我把要新添加的功能当作这个类型的扩展写下来，或是就在这个类型里面定义我原本打算写在扩展里的功能是没有区别的，都是一样的效果。

所以，站在编译器的角度来看，编译器可能会说：“好吧，我看到这里写了一个扩展，但是真没这个必要，因为扩展和类型都在同一个源文件里...，所以开发者完全可以把扩展里的这些代码直接写在类型里面...，所以他／她能够访问到被`private`修饰的代码段。”

**更新:**我上面的写的推理恰恰说明了我压根就没搞明白Swift访问控制机制。所以我建议大家读一读我后来写的“[阐明Swfit控制机制](http://www.andrewcbancroft.com/2015/04/24/clarifying-swift-access-control-hint-swift-isnt-c-sharp/)”这篇文章，里面有更多的细节。

###定义在不同文件里
把扩展写在另一个文件里，则扩展无法访问类型中那些被`private`修饰的内容了。

按照上文我自己推理的逻辑来反过来想，定义在不同文件中就访问不了私有属性对我来说也是说的通的。

大多数情况下，你都会给那些你**没有**源代码的类型扩展，在这种情况下，扩展就只能访问那些被`public`修饰的内容了。
![pic1](http://www.andrewcbancroft.com/wp-content/uploads/2015/04/PersonExtensions_swift.png)

##默认情况下的扩展访问控制
对最后一个细节的验证也让我更深的体会。[苹果官方文档](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AccessControl.html#//apple_ref/doc/uid/TP40014097-CH41-ID25)说了，但是直到我动手验证了一番，我才算领会到了默认访问控制修饰符给扩展所造成的微妙的影响。

###没有明确声明访问修饰赋时的默认访问
简单的说，当你声明一个扩展但没有特别明确指明访问修饰符时（默认情况下），这个扩展的默认访问等级取决于被扩展的那个类型的访问等级。
* 如果类型是`public`或者是`internal`，那么扩展的implementation的“成员”就默认为`internal`。这里让我没想到是，除非你特别声明，那么给`public`类型的扩展的成员变量在默认情况下也是`internal`。
* 如果类型是`private`，那么默认情况下扩展的implementation中的“成员”也是`private`

下面就是在我们不明确的声明添加什么访问修饰的前提下，来看扩展会是一个什么反应（为了能访问私有属性变量和方法，我在Person.swift里定义了这个扩展）：

```
public struct Person {
    // ...

    // ...
}

extension Person {
    func getAge() -> Int {
        return age
    }

    func getFormattedAge() -> String {
        return formattedAge()
    }
}
```
像上面这段代码用默认的访问修饰符时就会允许在同一个模块中的实例访问扩展里的API。但是，如果被扩展的类型的实例是在另一个模块（比如在测试模块）,则无法访问扩展中任何新增的公共API。
####同一模块
![pic2](http://www.andrewcbancroft.com/wp-content/uploads/2015/04/SameModule.png)
####不同模块（测试）
![pic3](http://www.andrewcbancroft.com/wp-content/uploads/2015/04/DifferentModule.png)
因为某些原因，我一直都以为如果给一个是`public`的类型添加扩展，那么扩展里的成员也理应是`public`。我不知道为什么我会这么想，但是幸好我的验证把这点捋清楚了。

###正常声明扩展，但给扩展的implementation添加public修饰
给扩展的implementation的成员添加了`public`访问控制修饰，那么不管是在同一模块还是不同模块（test target）都能访问这些成员。

只要成员被`public`修饰，那么在同一个源文件里声明扩展还是在另一个文件里声明扩展已经无所谓了...但是，正如前文所讲，只有在同一个源文件中声明的扩展才能够访问那些被`private`修饰的类型成员变量。
####在不同（左）和同一个（右）源文件中声明的扩展
![pic4](http://www.andrewcbancroft.com/wp-content/uploads/2015/04/public_extension_members.png)
####在不同模块中也能访问公共的扩展成员变量
![pic5](http://www.andrewcbancroft.com/wp-content/uploads/2015/04/public_member_visibility.png)
这里请注意，在我写`extension Person {...}`时，我没有给这个扩展添加任何的修饰，我只是给这个扩展的成员添加了`public`。即便如此，新添加的方法仍然可以在不同的模块中被访问到。

也就是说，没有必要写`public extension Person {...}`。因为`Person`已经是`public`了，所以基于`Person`的扩展也就很自然的延用了类型本身的访问等级。

##总结
对我来说，这篇文章所提到的三个关于Swift扩展的细节已足以让我敲敲代码去验证一番了。我希望这里所作的分析能够为那些尝试理解Swfit扩展的朋友扫清一些障碍。
