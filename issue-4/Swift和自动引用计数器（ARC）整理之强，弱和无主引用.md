Swift和自动引用计数(ARC)整理之强，弱和无主引用
---

>
* 原文链接 : [Strong, Weak, and Unowned - Sorting out ARC and Swift](http://www.andrewcbancroft.com/2015/05/08/strong-weak-and-unowned-sorting-out-arc-and-swift/)
* 原文作者 : [Andrew Bancroft](http://www.andrewcbancroft.com/)
* 译文出自 : [开发技术前线 www.devtf.cn](www.devtf.cn)
* 译者 : [samw00](https://github.com/samw00/) 
* 校对者:  
* 状态 :  校对中 

我敢打赌有相当一部分的Swfit开发者纠结于`strong`（强引用），`weak`（弱引用)和`unowned`（无主引用）这几种不同类型的引用方式对他们的代码在运行时所造成的影响。如果我的日子依赖于自动引用计数（ARC）,我想我是不会费神来尝试解释其中的奥妙的。

在我每次声明变量或是常量时，我都不是特别确定到底应该用哪种类型的引用，为了让自己不再纠结，我最终决定把[苹果官方关于ARC的文档](https://developer.apple.com/library/ios/documentation/Swift/Conceptual/Swift_Programming_Language/AutomaticReferenceCounting.html)拿出来拜读，试着去理解和消化里面的每一句话。这篇文章尝试着把我脑里关于Swift和自动引用计数（ARC）的整理分享给大家。

这篇文章稍微有点长所以我想：“为何不先把总结直接抛出来让小伙伴们先睹为快呢？然后如果实好奇再继续看我是如何得出这总结的”。所以下面先上总结！

##总结

* 在你开始担心应该是用`strong`，`weak`还是`unowned`之前，先问自己一个问题：“我是在和引用类型打交道吗？”。如果是在和结构体或是枚举类型打交道，你就压根不用管是用`weak`还是`unowned`来修饰这些常量或变量，因为ARC就没做这些类型的内存管理。
* `strong`强引用适用于父对象指向子对象，但反过来不能用强引用，听说过父亲拥有儿子的，可没听说过儿子拥有父亲的。事实上，在大多数情况下，`strong`都是默认引用类型。
* 当两个实例是_optionally_关联在一起的，确保其中一个实例是用`weak`弱引用指向另一个实例。
* 两个实例A和B，如果实例A必须在实例B存在的前提下才能存在，那么实例A必须用`unowned`无主引用指向实例B。也就是说，有强制依赖性的那个实例必须对另一个实例持有无主引用。

想具体了解我是如何得出这些结论的，就要看接下来的详细解说了，你可以直接跳到你感兴趣的部分也可以顺序读下去。

##ARC和内存管理
对很多人来说，一提到“内存管理”就云里雾里的。

内存管理的本质之所以被蒙上各种神奇的面纱，是因为大多数我们熟悉的编程语言都把这一块给提炼了出去。大多数时候，我们只要简单的敲我们的代码而不用管我们的实例对象占用了内存的几个字节以及当我们不需要这些对象的时候，他们是如何被清除的。总之程序就是会跑起来。

自动引用计数（ARC）就是对内存管理的一种提炼。这是苹果用来清除内存中不用的类实例来释放内存空间所采用的一种方法。

ARC只对类起作用（所以没结构体和枚举什么事），因为自动引用计数只对**引用类型**有效。结构体和枚举属于值类型，所以ARC不会对值类型的实例做任何内存管理。

引起我们关于引用类型关键字讨论的问题是这：“ARC是如何知道某个实例已经“用完”它所占据的内存空间呢？”。

##Strong 强引用
在ARC的世界里，`strong`，`weak`和`unowned`就是修饰引用本质的关键字。

所有的引用在没有特殊说明之前都是`strong`强引用。大多数情况下，这就是对的。在Swift中，当你声明一个变量或是常量时，默认就是`strong`强引用，所以不用在前面添加`strong`。

那么，一个引用是强是弱和ARC的内存管理有什么关系吗？

在ARC中，只有当指向一个类实例的**所有**`strong`强引用都被断开了，那么这个类实例才会被清除，内存才会被释放。那么问题来了，`strong`强引用在什么情况下才会断开呢？

`strong`强引用在下列情况下会被断开...

* 当指向某一实例的变量被设为`nil`
* 当持有一个子类实例引用的父类变量被设为`nil`，这样会同时断开父类和子类之间的相互引用
* 当变量或常量离开了自己的作用域，比如在`if/else`或者`for`循环中初始化了某些变量，当程序执行完这些控制流语句时，相关引用就断开了，同时ARC会释放内存。

ARC中内存管理的所有事情都取决于到底有**几个**`strong`强引用指向实例。当指向一个实例的`strong`强引用为零的时候，那么就是这个实例被清除，所占据内存空间被释放的时候。

那么`weak`弱引用和`unowned`无主引用又是怎么一回事？

##weak弱引用和unowned无主引用
当我们谈到类实例与类实例之间的关系时，就应该要想到`weak`弱引用和`unowned`无主引用。

###实例之间的关系
类与类之间的关系是面对对象编程的一个核心。不管你对类之间的关系有什么规划，总之这些关系是存在的，而且他们影响着ARC内存管理模式。

那么类与类之间到底有哪些关系类型呢？哪些关系类型需要我们用到`weak`弱引用，哪些需要`unowned`无主引用呢？

####层级类关系 Hierarchical Relationships
在Swift中，能经常看到这些层级关系的身影。经常是某一个类的实例对一个或多个子类实例拥有`strong`强引用，而这些子类实例又对自己的一个或多个子类实例持有`strong`强引用。在这种情况下，`strong`强引用方向都一致：从父类指向子类。

在这种层级结构中，用`strong`强引用是没问题的。但是，如果某一个子类要反向持有一个父类的引用怎么办呢？如果没考虑好，在ARC中就会遇到麻烦。

####可选，相互依赖类关系 Optional, mutually dependent relationships
在这一部分，我的目的是讲明`weak`弱引用所扮演的角色。

当类实例互相依赖时，它们之间需要相互持有引用。但有时候，依赖性不是强制的，在这种情况下，就允许某一个实例在不持有其他实例引用的情况下也能够存在。

如果用`database cardinality`来打个比方，我们可以说双方的关系是0：1。

如果我们面前有个例子那是最好不过的了 - 在这过去的一周，Oklahoma州的一个动物园被龙卷风袭击了，有几头珍奇异兽逃脱了，这事给了我灵感!

在动物园里，一个`Animal`生活在一个`Exhibit`（展区）里。有时`Exhibits`可能是空的，可能正在被清理，或者原本生活在这里的`Animal`生病了或被转移了，又或者一个龙卷风来袭了，等等...

同样的，一个`Animal`可能不住在`Exhibit`中。它可能被临时放在某个地方等`Exhibit`被打扫干净后再回去，或者是在接受治疗也可能是被转移去另一个动物园，也有可能是趁龙卷风来袭逃走了...

这动物园的例子可以用代码这样来表示:

```
class Animal {
    let name: String
    let species: String
    init(name: String, species: String) {
        self.name = name
        self.species = species
    }

    var exhibit: Exhibit? // 注意这是个可选类型变量
}

class Exhibit {
    let title: String
    init(title: String) { self.title = title}

    var animal: Animal? // 注意这也是个可选类型
}
```
现在有一个`Animal`生活在一个`Exhibit`里。如果我们给这两者建立某种联系，让它们互相拥有对方的信息这样可能会更好。所以我们先实例化一个`Animal`和一个`Exhibit`，然后紧接着把实例赋给对方中相对应的属性。

现在我们考虑龙卷风袭击了动物园，然后`Animal`逃离了它的`Exhibit`这事。假设，这个`Animal`没有被找回（实际上他们已经找到那些逃走的动物），再假设这只逃跑的`Animal`正在Oklahoma的平原上自由的奔跑。而动物园，却不得不关闭它的`Exhibit`。在代码要体现这的话，我们只要把`Animal`的实例设为nil，同时把`Exhibit`的实例也设为nil。

####内存泄漏
刚刚给出的这个动物园例子正好说明了在你的应用中造成内存泄漏是可能的。这如何说起?

这一切都和ARC有关，只有当指向某个类实例的引用计数为**零**时，ARC才会释放内存。

在我们的`Animal` - `Exhibit`例子中，两个实例互相拥有指向对方的引用。当我们把`Animal`设为nil时，`Exhibit`实例仍然会通过自己的animal属性持有对`Animal`的引用。

反过来说，既然`Animal`实例仍然存在，那么它也通过自己的`exhibit`属性持有一个指向`Exhibit`的引用。所以当我们把`Exhibit`设为nil时，｀Animal`还是会指向它不放手。

那么现在我们就处在一个尴尬的境地。 `Animal`和`Exhibit`就这么一直互相持有对方的引用（所以谁的内存空间也不会被释放）而除此之外再没其他东西再指向它们了，那么就没办法再访问这两个实例了，它们就永远存在于内存中，内存就这么泄漏了。

但是解决的办法总是有的！在这种“可选，相互依赖的关系”中，`weak`弱引用将起着至关重要的作用。

####打破强引用循环
刚刚提到的尴尬场面就是“强引用循环”。

好在我们有方法破除这类循环从而避免导致内存泄漏。

在互相依赖的关系中，就像上文提到的`Animal`和`Exhibit`这种，只要将其中的一个引用从`strong`强引用转为`weak`弱引用就能破除强引用循环。

至于哪个类持有`weak`弱引用不是重点，只要有其中一个是持有即可。

```
class Animal {
    let name: String
    let species: String
    init(name: String, species: String) {
        self.name = name
        self.species = species
    }

    var exhibit: Exhibit? // 用强引用指向exhibit
}

class Exhibit {
    let title: String
    init(title: String) {self.title = title}

    weak var animal: Animal? // 这里用到_weak_弱引用来指向animal
}
```
让`Exhibit`中的`animal`属性拥有一个对`Animal`实例的`weak`弱引用会消除形成强引用循环的可能性。

那么`unowned`无主引用又是怎么一回事?

####强制，单向依赖关系
这一部分，我的目标是讲明`unowned`无主引用所扮演的角色。

还有最后一类关系类型是直接涉及到ARC这个主题。在有些情况下，两个类实例是相互有联系的，但是其中一个实例必须要在另一个实例存在的前提下才能存在。

让我们继续刚才动物的那个例子。假设我们的动物园向`Visitors`游客发行`AnnualPasses`年票。一个`Visitor`游客可以不用`AnnualPass`年票就能进动物园，就光他自己存在就足够欣赏各种展厅了。但是，一个`AnnualPass`年票，却必须需要有一个`Visitor`来持有才有存在的意义。

在代码中，我们这样来写：

```
class Visitor {
    let name: String
    var annualPass: AnnualPass?
    init(name: String) {
        self.name = name
    }
}

class AnnualPass {
    let number: UInt64
    unowned let passholder: Visitor // 注意这里我们用了_unowned_无主引用
    init(number: UInt64, passholder: Visitor) {
        self.name = number
        self.passholder = passholder
    }
}
```
注意下面几点：

1. 游客`Visitor`对`AnnualPass`年票实例有一个可选的`strong`强引用
2. 年票`AnnualPass`对`Visitor`实例持有一个不可选的`unowned`无主引用

这样做的目的在于，当`Visitor`变量被设为nil时，`strong`强引用计数能为零，从而也能够避免因强引用循环而导致的内存泄漏。

##总结
我知道我在一开始就写了总结，但为了文章的完整性，我在下面再总结一次...

* 在你开始担心应该是用`strong`，`weak`还是`unowned`之前，先问自己一个问题：“我是在和引用类型打交道吗？”。如果是在和结构体或是枚举类型打交道，你就压根不用管是用`weak`还是`unowned`来修饰这些常量或变量，因为ARC就没做这些类型的内存管理。
* `strong`强引用适用于父对象指向子对象，但反过来不能用强引用，听说过父亲拥有儿子的，可没听说过儿子拥有父亲的。事实上，在大多数情况下，`strong`都是默认引用类型。
* 当两个实例是_optionally_关联在一起的，确保其中一个实例是用`weak`弱引用指向另一个实例。
* 两个实例A和B，如果实例A必须在实例B存在的前提下才能存在，那么实例A必须用`unowned`无主引用指向实例B。也就是说，有强制依赖性的那个实例必须对另一个实例持有无主引用。
