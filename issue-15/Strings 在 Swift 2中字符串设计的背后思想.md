* 原文链接：[Strings in Swift 2](https://developer.apple.com/swift/blog/?id=30)
* 原文作者：[ Apple ](https://developer.apple.com/)
* [译文出自：开发者前线 www.devtf.cn](www.devtf.cn)
* 译者：[MrLoong](https://github.com/MrLoong)
* 校对者：MrLoong
* 状态：完成

#Strings 在 Swift 2中字符串设计的背后思想

swift提供高性能，兼容Unicode的string作为标准库的一部分，在 Swift 2.0，**String**类型不再符合**CollectionType**协议，曾经**String**是字符的集合，类似于array。现在，**String**提供了一个公开字符集视图的**charactes**属性

为什么会变？虽然模拟一个字符串作为字符集合他看起来很自然，但是**String**类型的行为还是与**Array**，**Set**，**Dictionary**这种集合类型有很大的区别的。这一直存在着，但是随着Swift 2协议扩展的增加，这些差异使得有必要做出一些基本的改变。

##与之不同的Sum

当你添加一一个元素到集合中，你期望集合将包含这个元素。就是说，当你将一个值添加到数组中时，该数组包含该值。就像应用dictionary或set。然而，当你在一个string中添加一个组合标记字符串，字符串本身的内容被更改。

考虑字符串cafe，其中有四个字符串：c，a，f和e：

```
var letters: [Character] = ["c", "a", "f", "e"]
var string: String = String(letters)

print(letters.count) // 4
print(string) // cafe
print(string.characters.count) // 4

```

如果你添加一个组合字符**U+0301**和**´**，字符串仍然有四个字符，但是现在最后一位时**é**

```
let acuteAccent: Character = "\u{0301}" // ´ COMBINING ACUTE ACCENT' (U+0301)

string.append(acuteAccent)
print(string.characters.count) // 4
print(string.characters.last!) // é

```
在刚刚这个字符串的字符属性不包涵原始的小写字母**e**，也不包涵**´**,知识附加。相反字符串中现在包涵一个小写“e”，带有**é**:

```
string.characters.contains("e") // false
string.characters.contains("´") // false
string.characters.contains("é") // true

```

如果我们像其它集合那样处理字符串，这个结果将会让我们惊讶，添加**UIColor.redColor()**和**UIColor.greenColor()**然后设置他包涵**UIColor.yellowColor().**

##根据文字内容判断

字符串和集合之间的另一个区别是他们确定相等的方式

* 如果两个array都有相同的数目，并且每对元素在相应的指数是相等的，两个array相等。
* 如果两个sets都有相同的数目，并且并且每个元素包含在第一个元素也包含在第二个。
* 如果两个dictionaries有相同的key，value，则两个dictionaries相等

然而**string**基于正则等价平等。如果有相同的语言意思和外观特征，甚至如果他们由不同的Unicode组成，则表示等效。

考虑下韩语协作系统，由24个字母组成，它由24个字母组成，或是Jamo，代表个别的辅音和元音。当把这些字母写出来的时候，每个音节都是字母组合起来的。字符“가”（[GA]）是由字母“ᄀ”（[ ]）和“ᅡ”[一]。在swift，字符串是相同的无论是由分解或预作字符序列：

```
let decomposed = "\u{1100}\u{1161}" // ᄀ + ᅡ
let precomposed = "\u{AC00}" // 가

decomposed == precomposed // true
```
同样，这种行为与任何快速的收集类型有很大的不同。这将是令人惊讶的价值🐟和🍚被认为等于🍣阵列。

##取决于你的观点


* characters is a collection of Character values, or [extended grapheme clusters](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/Strings/Articles/stringsClusters.html).
* unicodeScalars is a collection of [Unicode scalar values](http://www.unicode.org/glossary/#unicode_scalar_value).
* utf8 is a collection of [UTF–8](http://www.unicode.org/glossary/#UTF_8) code units.
* utf16 is a collection of [UTF–16](http://www.unicode.org/glossary/#UTF_16) code units.


如果我们把“CAFé”前面的例子，由分解的特征【C，A，F，E ]和[´]，这里有各种字符串的视图将包括：

![](https://devimages.apple.com.edgekey.net/swift/blog/images/swift-string-views.png)

* **characters**属性段的文本扩展字形集群,这是一个近似的用户感知的字符（在这种情况下，c++，f）。因为一个字符串必须遍历它的每一个位置（每个位置称为一个代码点）为了确定字符边界.访问此属性的线性输出**O(n)**时间。当处理字符串包含人类可读的文本，高级语言环境敏感的Unicode编码算法，尤其使用localizedstandardcompare用（_：）方法和localizedlowercasestring财产，应优先采用字符处理的特点。
* UTF8，UTF16性质为UTF 8和16表示–utf–提供代码.这些值对应于将实际的字节写入到一个文件，当翻译到和从一个特定的编码。UTF-8编码单元被许多POSIX字符串处理API.而UTF-16代码单元中使用的Cocoa & Cocoa Touch表示字符串的长度和偏移量。

有关字符串和字符的快速工作的更多信息, 阅读  [The Swift Programming Language](https://developer.apple.com/library/prerelease/ios/documentation/Swift/Conceptual/Swift_Programming_Language/StringsAndCharacters.html#//apple_ref/doc/uid/TP40014097-CH7-ID285) 和 [the Swift Standard Library Reference](https://developer.apple.com/library/prerelease/ios//documentation/Swift/Reference/Swift_String_Structure/index.html#//apple_ref/swift/struct/s:SS).
