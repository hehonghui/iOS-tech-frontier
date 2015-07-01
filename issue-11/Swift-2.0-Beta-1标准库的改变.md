#Swift 2.0 Beta 1 标准库的改变
---

> * 原文链接 : [Changes to the Swift Standard Library in 2.0 Beta 1](http://airspeedvelocity.net/2015/06/09/changes-to-the-swift-standard-library-in-2-0-beta-1/?utm_campaign=iOS%2BDev%2BWeekly&utm_medium=web&utm_source=iOS_Dev_Weekly_Issue_202)
* 原文作者 : [airespeedvelocity](http://airspeedvelocity.net)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [samw00](https://github.com/samw00) 
* 校对者: [Mr.Simple](https://github.com/bboyfeiyu) 
* 状态 : 完成

OK 先不要慌 － 看起来好像有很多东西都改变了，但事实上不是。说是改造了，朝着更好的方向...可能更加贴切。总之，没有什么是Migration Assitant处理不了的。

目前为止，给标准库带来最大变化的是新的扩展协议。`map`函数已死，集合类型和序列类型的`map`扩展万万岁。这意味着现在你永远只能把`map`当作一个方来来调用 － 再也不用在函数和方法之间来回穿梭，就像你在看一场网球比赛一样。

为了展示我所说的，先定义一个我常用的例子，`mapSome`：

```swift
extension SequenceType {
	/// Return an `Array` containing the results of mapping `transform`
	/// over `self`, discarding any elements where the result is `nil`.
	///
	/// - Complexity: O(N).
	func mapSome<U>(@noescape transform: Generator.Element -> U?) -> [U] {
		var result: [U] = []
		for case let x? in self.map(transform) {
			result.append(x)
		}
		return result
	}
}
```

你可以在method-calling风格中使用它，比如，在全新的字符串类型转浮点类型可失败构造器中：

```swift
let a = ["3.14", "foo", "6.02e23"]
let double = a.mapSome{ Double($0) }
print(doubles) // no more println!
// prints [3.14, 6.02e+23]
```

顺带地， `mapSome`使用了`for`的新的格式匹配能力来将所有的`nils`都过滤掉了。函数中的`case let`语句是要匹配一个枚举类型 － 刚好可选值类型本质就是枚举类型，所以对它们就正起作用。同时`for`语句也意味着跟着一个`where`语句：<sup>1</sup>

```swift
let isEven = { $0%2 == 0 }
for even in 0..<10 where isEven(even) {
	print(even)
}
```

不单单就这些。你可以给扩展添加约束，就好比你可以给一个泛型函数的占位符添加约束一样。这就意味着你可以使扩展中的方法在拥有制定的属性前提下才能起作用。举个例子，现在有一个用在数组（或者任何集合类型）上的新`sort`方法是不用传一个`isOrderedBefore`参数的，只要数组的内容是具有可比性就行了。如果你想要更加自定义的东西，总可以写个重载然后传一个闭包，但是如果你只要一些很默认的行为（升序）,你就不需要搞那么复杂了。

下面就是一个简单的例子，当所有的值都等于某个值的时候就返回真：

```swift
// a version that only applies when the contents of the
// sequence are equatable
extension SequenceType where Generator.Element: Equatable {
	/// Return `true` if every element of `self` is `x`.
	func all(equalTo: Generator.Element) -> Bool {
		// of course, contains is now a method too
		return [self.contains { $0 != equalTo }
	}
}

// and an unconstrained version where the caller supplies a predicate
extension SequenceType {
	/// Return `true` if every element of `self` satisfies `predicate`.
	func all(criteria: Generator.Element -> Bool) -> Bool {
		return [self.contains { !criteria($0) }
	}
}

[1, 1, 1].all(1)		// true

let isEven = { $0%2 == 0 }
[2, 4, 6].all(isEven) 		// true
```

其结果就是，标准库中的自由函数数量从101个减少到77个。我在想还会减少多少个呢？－ 最终是否只会一些`unsafeBitCast`杂牌军之流吗？`abs()`会成为`SignedNumberType?`的扩展吗？现在它就行了，那么它应该成为一个属性吗？统统这些还有更多将再下一期Swift发布中揭晓...

#Grab Bag

下面是关于标准库的其他一些改变：

* `Array.withUnsafeMutableBufferPointer`会产生一个警告：调用这个方法时，不要使用数组本身，而只要将指针指向数组内容就行。想必数组的更新可能会推迟到方法调用完成之后。同理适用于`ContiguousArray`和`Slice`。
* 新标准库中一些`internal-type`的构造器被私有化了。
* 一些已`_`开头的协议也开始消失了。比如`_BidirectionalIndexType`就被去掉了，然后`BidirectionalIndexType`继承它前任的方法。还有`_Comparable`也没了，`Comparable`先在有`<`操作符。
* `CollectionOfOne`和`EmptyCollection`现在有一个`count`属性 － 可以用来检查你是否被它们所误导了。
* 现在开启了伟大的扩展时代...｀CollectionType`现在有`isEmpty, count, map, filter, first, last, indexOf, indices 和 reverse`。这些方法所有的集合类型都能使用了。而集合类型对应的方法则被去掉了。
* `find`曾经经常调用`indexOf`。现在有个新版本可以传`predicate`。
* 新加入了一个`ErrorType`协议，用来支持新的错误处理机制。
* 原来的`CustomStringConvertible`和`CustomDebugStringConvertible`现在变为`Printable`和`DebugPrintable`。
* 还有`CustomReflectable, CustomLeafReflectable` 和 `CustomPlaygroundQuickLookable`协议让供你在playground对你类型的行为进行微调。
* 还有一个`Mirror`类型供你配合和`CustomReflectable`协议使用。
* 还有一个新的`DictionaryLiteral`类型，现在你可以放心大胆的给除字典以外用`[:]`，而不必担心会有相同的`key`碰撞在一起。
* `Double, Float`和`Float80`从`strings`那里获得了可失败构造器。
* 现在整数型有一个一样，替换了字符串类型的`toInt`方法。并且接受一个`radix`参数！你仍然不能从头文件看出默认参数就是什么默认值，但是我猜这一次是10.
* `FloatingPointType`中的`_toBitsPattern`和`_fromBitPattern`没了。如果你喜欢和`bits`打交道你可以使用`unsafeBitCast`。
* 所有延迟加载集合类型都有一个`underestimateCount`属性。
* `MutableCollectionType`是一个带有`where`语句很好的扩展例子 － 比如为了让某个集合类型有`partition`和`sortInPlace`支持需要该集合类型能够被随机访问。
* `SequenceType`在调用`contains, sort, underestimateCount, enumerate, minElement, maxElement, elementsEqual, lexicographicalCompare`和`flatMap`时表现会非常糟糕。
* 比较两个`sequence`是否`equal`的新方法名是`elementsEqual`。
* `minElement`和`maxElement`现在返回的是可选值类型，而且还接受`isOrderedBefore`闭包。
* 有一个新的协议叫`SetAlgebraType`，适用于'set里面每一个独特元素不一定不相交‘的类型。`Set`目前并没有遵守该协议(但已有所有必要的属性了)。
* `Set`倒是遵守`OptionSetType`协议，这都是为了`bitfield`枚举。
* `print`也没了！现在`println`就是`print`而老的`print`是`print(1, newLine: false)`。
* `toString`不再被使用了。简单粗暴就用String的构造器。
* `readLine`从标准输出流读取，返回一个可选字符串类型，所以你可以用`while let`来循环。

#协议扩展为默认

先把愚蠢的玩笑放一边，`CollectionOfOne`和`EmptyCollection`之所以会有`count`是有一个非常站得住脚的理由的。如果协议有一个默认的实现方式，但是你知道你的类型自己自带一个更好的实现方法能够更加快速的解决问题，那么这个类型自带的方法则会覆盖协议当中的那个版本。

假设，我们实现了下面这个集合类型中的`next`逻辑(没啥特别意义）：`CollectionOfTwo:`

```swfit
struct CollectionOfTwo<T>: CollectionType {
	let first: T, second: T
	subscript(idx: Int) -> T {
		precondition(idx < 2, "Index out of bounds")
		return idx == 0 ? first : second
	}
	var startIndex: Int { return 0 }
	var endIndex: Int { return 2 }
}

let two = CollectionOfTwo(first: "42", second: "420")
",".join(two) // "42,420"
```

注意我并没有定义一个generator - 全托这个就手的新协议扩展：

```swift
// CollectionType 遵守 _CollectionGeneratorDefaultsType
// 而后者被加了如下扩展
extension _CollectionGeneratorDefaultType {
	func generate() -> IndexingGenerator<Self>
}
```

不管怎么讲，`CollectionOfTwo`遵守了集合类型所有它自然而然的有`count`属性。但是它得到结果的方式是从一开始就减去最后的索引，这看起来是一个挺迂回的方式。所以你可以直接把值写死然后明确的说明要实现它：

```swift
extension CollectionOfTwo {
    var count: Int { return 2 }
}
```

现在，当调用`count`的时候，则会返回代码中的数而不是默认值了。

协议当中的方法也可以覆盖其他协议中定义的方法 － 所以集合类型可以定义`map`虽然序列类型也定了`map`。

虽然我们在实现一些简单的小类型 － 自定义字符串来渲染自定义类型现在要变的好多了。虽然它们现在并不遵守`CustomeStringConvertible`，如果你打印`CollectionOfTwo`，你会得到的东西看起来像`CollectionOfTwo(first:42, second 420)`，这要比你之前会得到的`__lldb_expr_11.CollectionOfTwo`要好的多了。

#字符串再也不是集合类型了

如果有些东西让你觉得惊讶 － 那就是字符串类型再也不是集合类型了。尽管它有所有必要的属性(`extension String: CollectionType { }`这样是不会抱错的)，它再也不会被标注为集合类型了。这肯定是因为，就算字符串用一个个字符来表示，将集合的算法作用于字符串上也有可能导致一些`non-Unicode-correct`结果。

当然，你仍然需要改变你的字符串，与其死盯着它们看，你不如使用字符的新属性`CharacterView`：

```swift
let s = "comma,separated,strings"
let fields = split(s.characters) { $0 == "," }.map { String($0) }
Since String's Index is just a typealias for String.CharacterView.Index, you can use them interchangeably:

let s = "Hello, world!"
if let comma = s.characters.indexOf(",") {
    print(s[s.startIndex..<comma])
}
```

然后，你需要从一个个字符的角度来审视一个字符串而不是直接把字符串当作一个整体来进行操作可能会提醒你在某些环境下，你可能在得不到你想要的结果。

#Type-Erased容器

最后，现在有一个“type－erased”容器类型可供使用：`AnyBidirectionalCollection, AnyRandomAccessCollection`和`AnyForwardCollection(跟着的索引）`加上`AnyGenerator`和`AnySequence`. 它们的作用是讲不通种类的集合类型放在同一个容器里面：

```swift
let set: Set = [1,2,3]
let array = [4,5,6]
let anySet = AnyForwardCollection(set)
let anyArray = AnyForwardCollection(array)
let anys = [anySet,anyArray]
anys.flatMap { $0 } // [2, 3, 1, 4, 5, 6]
```

但是，这可能不像看上去的那么有用。除了名字，它不同于`Any`类型 － 你无法将类型转换成原来的类型，这更多的是一个单向转换。

如果你想暴露一个内在的集合但是又不想给出这个集合的类型，那么上面说的可能更适合。其实你也可以从你的内部数据中创建一个`AnyXXXCollection`，然后再返回它，这样能安全地确保当你切换了一个不同的内部数据结构时，你的类的用户不会抱怨。

好了，就这么多。标准库有一个新的绚丽的[线上文档](https://developer.apple.com/library/prerelease/ios//documentation/General/Reference/SwiftStandardLibraryReference/index.html)。然后作为WWDC示例代码的一部分，这[playground](https://developer.apple.com/sample-code/wwdc/2015/)有一些关于Swift标准库的范例值得去研究一下。另外Swift的[开发者论坛](https://forums.developer.apple.com/community/xcode/swift)好像也不需要开发者账户才能登录或看帖了，你可以不用注册就能看到里面的内容。

***

1. 当然你是不会这样写的 － 你会写`for even in stride(from: 0, to: 10, by: 2)`不是吗？
