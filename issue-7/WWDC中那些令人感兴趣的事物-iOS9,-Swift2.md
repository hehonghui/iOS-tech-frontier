## WWDC中那些令人感兴趣的事物:iOS 9, Swift 2

> - 原文链接 : [Notes: The interesting things from WWDC, iOS 9, Swift 2](http://iosdevtips.co/post/121053658888/wwdc-ios-9-swift-2-notes)
- 原文作者: [Rounak Jain](http://iosdevtips.co)
- 译文出自: [开发技术前线http://www.devtf.cn/](http://www.devtf.cn/)
- 译者 : [Lollypo](https://github.com/Lollypo) 
- 校对者:
- 状态 :  完成

The WWDC keynote today was great, except for the part where it just wouldn’t end. Tons of new stuff on the development front including Swift 2, iOS 9, CloudKit etc.

今天WWDC上的Keynote非常棒,即便到目前为止还未结束.在开发者方面,已经有大量的新事物出现,其中包括有Swift 2, iOS 9, CloudKit等等.

Here are some notes I gathered while browsing release notes, API diffs, feature pages and so on.

这里是一些我在浏览发布日志,API差异,新特性时摘的笔记.

- You no longer need a $99 developer membership to run iOS apps on your device using Xcode. Additionally, Mac and iOS developer membership have been merged, the price remaining the same:
- 你不需要订阅$99的开发者计划就可以在真机上调试应用程序.此外,Mac与iOS开发者计划合并,可以节省一半支出:
![](http://40.media.tumblr.com/ab8cf0d95f459e63bb10ded612e2fa34/tumblr_inline_npnb64YpK31qh9cw7_500.png)
- Interface Builder can render blurs and transparency
- Interface Builder支持实时预览模糊与透明效果
![](http://40.media.tumblr.com/ed19a7cbd80fe5ccb49f5a9102744eec/tumblr_inline_npnb8aLtBC1qh9cw7_500.png)
- Objective-C gets generics, which means collection objects like arrays, dictionaries can have specific types, just like Swift:
- Objective-C支持泛型,这意味着集合对象,如数组,字典可以像Swift一样指定类型
![](http://40.media.tumblr.com/4ed77823bca27d89db84f654080d7d28/tumblr_inline_npnbbdudd01qh9cw7_500.png)
- There’s a new UI class called [UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/index.html#//apple_ref/doc/uid/TP40015256), which is a port of Mac’s NSStackView.
- 新的名为 [UIStackView](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIStackView_Class_Reference/index.html#//apple_ref/doc/uid/TP40015256)UI类,是Mac中NSStackView的一种实现.
- [CloudKit can now be used on web too.](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240)
- [CloudKit现在也可以在Web中使用](https://developer.apple.com/library/prerelease/ios/documentation/DataManagement/Conceptual/CloutKitWebServicesReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40015240)
- CASpringAnimation is public!
- 开放 CASpringAnimation!
![](http://40.media.tumblr.com/65c41c82d295034c23ef986214404c6d/tumblr_inline_npnbg2mCrc1qh9cw7_500.png)
- Apps can plug in to Safari as extensions and block content. Ad blockers incoming? Additionally, apps can populate content in Safari’s Shared Links section.
- 应用程序现在可以作为拓展插入到Safari中,听起来有点像广告拦截器.此外,应用程序可以在Safari的共享链接填充内容
- Developers can use `SFSafariViewController`, which has shared cookies, auto fill and other features from Safari.
- 开发者现在可以使用 `SFSafariViewController`, 这可以共享Safari中中cookies, 自动填充以及其他Safari中的特性.
- `UIPickerView` can be resized to any size. No more ugly transform hacks.
- `UIPickerView` 可转换为任意尺寸,而不必再使用那些丑陋的转换技巧.
- iOS apps can show a text field in notifications, which was previously restricted to Messages.
- iOS应用程序可在发出提醒展示一段文本域,此前仅限于Messages使用.
- Contacts can be finally accessed via an object oriented API, as opposed to the earlier C API.
- 通讯录可通过面向对象的API来访问,而不是之前的C
- Calling API methods not available on minimum deployment target causes a compile time error. There’s a new available condition to conditionally execute code and avoid crashes for using new APIs:
- 电话API在低版本设备上不支持,这也许会导致编译时错误.现在有新的判断条件来决定是否执行代码,这可以有效避免使用新的API导致的应用程序崩溃:
![](http://41.media.tumblr.com/c1875f851f168467e8e3bd435fa31f8d/tumblr_inline_npnbnvrUp31qh9cw7_500.png)
- `UICollectionView` got a lot of new methods to support interactively moving items
- `UICollectionView` 新添加了许多方法来支持交互式移动
- There’s a new `UIFieldBehavior` in UIKit Dynamics
- 新的名为 `UIFieldBehavior` 的 UIKit Dynamics类
- Xcode Assets Catalog: Devices won’t download assets of all sizes, only 1x, 2x or 3x depending on its traits, reducing the app size.
- Xcode Assets Catalog: 设备不会一次性下载所有尺寸的图片,而根据其自身的需要下载 1x, 2x 或 3x 的图片,大大减少了应用程序的大小.

## Some interesting links: 
## 其他一些你可能感兴趣的链接:

- [API Diffs](https://t.co/cOdmXdQ0EQ)
- [Apple’s Swift 2 prerelease ebook](https://itunes.apple.com/us/book/swift-programming-language/id1002622538?mt=11)
- [Xcode 7 release notes](http://t.co/5pU8P4iNEH)
- [API差异](https://t.co/cOdmXdQ0EQ)
- [Swift 2提前发布版电子书](https://itunes.apple.com/us/book/swift-programming-language/id1002622538?mt=11)
- [Xcode 7 发布日志](http://t.co/5pU8P4iNEH)