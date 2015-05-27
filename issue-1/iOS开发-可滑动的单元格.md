`iOS开发-可滑动的单元格`
---

>* 原文链接 : [Swipeable table view cells in iOS apps](https://medium.com/ios-os-x-development/swipeable-table-view-cells-in-ios-apps-472da0af1935)
* 译者 : [Harries Chen](https://github.com/mrchenhao) 
* 校对者: [Mr.Simple](https://github.com/mrchenhao)  

我在过去的一个月里致力于开发我的创业项目，其中有一个功能就是我想让用户通过左右滑动表格来显示更多的选项（就像系统自带的邮件程序现实更多和删除一样，在这篇文章中我将讨论一些关于这方面的方法，以及如何动手实现它）

首先，让我们来看一下现有的解决方法。

* UITableViewRowAction (new in iOS 8)

这是最好的方法如果你只是希望加一些从左滑到右才会出现的按钮。

* [SWTableViewCell](https://github.com/CEWendel/SWTableViewCell)

这是一个功能很全的按钮库，支持左滑和右滑，同时也可以在iOS8以前的版本中使用

* [MGSwipeTableCell](https://github.com/MortimerGoro/MGSwipeTableCell)

如果你想要实现表格的左右滑动功能，你可以从上面的解决方按照找到你需要的。但是为什么我们还要自己来实现呢？就我而言，我需要MGSwipeTableCell的一些功能，但是有的功能它对我并没有用，如果你想要自己来实现它，或者仅仅只是想要知道它的原理，那么接下去看。


##原理剖析

制作一个可滑动的UITableViewCell其实是一件非常简单的事情，它的要点就是在contentView里面包含了UIScrollView，在UIScrollView里面有两个UIView，一个用于放置按钮，一个用于放置单元格的内容，难点在于如何配置滑动页面，下面是一张图片来展示UIScrollView里的主要内容。

![这里写图片描述](http://img.blog.csdn.net/20150420235653232)

* frame

视图的大小，（也就是实际在屏幕上显示的面积），在上图中，外面的高度看起来会比UIScrollView的高度高很多，实际上在代码中他们的高度是一样的，只是为了更好的展示。

* contentSize

在可滚动的视图中，显示的大小和常常与实际内容的大小不同，当实际内容比显示的内容要大的时候，就可以进行滚动，以便让用户看到所有的内容。这个属性代表了在scrollview实际内容的宽和高。在这个例子中，我们实际上会保持它和视图大小相同，这要归功于一个叫做contentInset属性

* contentInset

这个属性将使我们的扩大内容区域之外的可滚动范围。通常，当contentSize属性被设置为与frame相同的（或更小）的尺寸作为视图本身，视图并不会产生滚动的效果。然而，contentInset将延长使内容产生滚动，让你可以滚动至内容边界的区域。这有什么用吗？例如，它可以用在显示上拉刷新的加载画面之中。在这个例子中，我们将使用它来腾出空间已显示额外的按钮。

* contentOffset

这个值代表当前滚动视图的滚动量，当用户滚动的时候，这个值将会被改变，你也可以通过程序来改变它的值，我们随后将会通过改变这个值来控制按钮的出现。

Implementing the cell, step by step
This section will demonstrate how to actually make the cell display buttons when swiping on it. The code examples will be simplified to focus on the concept. At the end I will provide a full implementation.

First, let’s subclass UITableViewCell to begin implementing our swipeable table cell.

The easiest way to subclass classes is to go to File > New > File… (⌘N). Pick UITableViewCell as the class to subclass.

##一步步实现

本节将展示如何通过滑动来使单元格显示按钮。代码将被简化以便更好的理解这一概念。最后我将提供[完整的代码](https://github.com/blixt/SwipeableTableViewCell)。

第一步，子类化UITableViewCell

![这里写图片描述](http://img.blog.csdn.net/20150420235749907)


子类化最简单的方法就是文件>新建>文件...（⌘N）。选择的UITableViewCell作为类的子类。

因为我们希望实现对滚动控制，所以我们需要指定我们的类实现UIScrollViewDelegate协议。方法是后面加上尖括号，在里面写上需要实现的协议

```
@interface  SwipeableTableViewCell：UITableViewCell <UIScrollViewDelegate>

```

接下来，我们将在单元格初始化的时候配置滚动视图。选择你想要的初始化方式，可以是`initWithCoder：`也可以是 `initWithStyle：reuseIdentifier：` 。我建议创建一个common方法来配置单元格，以便从不同的初始化放来调用。

```
// 创建一个可以横向滚动的滚动视图
UIScrollView *scrollView = [[UIScrollView alloc] initWithFrame:self.bounds];
scrollView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
scrollView.contentSize = self.bounds.size;
scrollView.contentInset = UIEdgeInsetsMake(0, 160, 0, 0);
scrollView.delegate = self;
scrollView.scrollsToTop = NO;
scrollView.showsHorizontalScrollIndicator = NO;
scrollView.showsVerticalScrollIndicator = NO;
[self.contentView addSubview:scrollView];
self.scrollView = scrollView;

```

当我们我们创建滚动视图后，我们需要做一些事情。首先，我们设置它的大小的。这意味着在父视图大小发生改变的时候应该怎样改变。我们希望滚动视图填充整个单元格当它运行时改变大小的时候（这很可能发生，因为在iPhone 5，6和6 plus都具有不同的屏幕宽度）。然后我们设置与滚动视图相同大小的内容，在左边插入与我们所需要添加的按钮宽度相同的视图。关闭scrollsToTop事件，因为我们不关心这个状态栏是否被点击（这个功能通常用于滚动视图顶部）。这将让我们的表视图利用这特性，而不是打破这一行为。同时我们也关掉水平和垂直滚动栏，这样，当我们滚动的时候半透明的黑条不会出现。

```
// 设置内容大小
UIView *contentView = [[UIView alloc] initWithFrame:scrollView.bounds];
contentView.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
contentView.backgroundColor = [UIColor whiteColor];
[scrollView addSubview:contentView];
self.scrollViewContentView = contentView;
 
UILabel *label = [[UILabel alloc] initWithFrame:CGRectInset(contentView.bounds, 10, 0)];
label.autoresizingMask = UIViewAutoresizingFlexibleWidth | UIViewAutoresizingFlexibleHeight;
[self.scrollViewContentView addSubview:label];
self.scrollViewLabel = label;

```

不必惊讶于上述代码，我们只是增加了必要的部分呈现的表格单元格的主要区域

```
// 创建按钮的容器.
UIView *buttonsView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 160, self.bounds.size.height)];
buttonsView.autoresizingMask = UIViewAutoresizingFlexibleHeight;
self.buttonsView = buttonsView;
[self.scrollView insertSubview:buttonsView atIndex:0];
 
// 创建两个按钮并放入容器.
UIButton *yesButton = [UIButton buttonWithType:UIButtonTypeCustom];
yesButton.autoresizingMask = UIViewAutoresizingFlexibleHeight;
yesButton.backgroundColor = [UIColor greenColor];
yesButton.frame = CGRectMake(0, 0, 80, self.bounds.size.height);
[yesButton setTitle:@"Yes" forState:UIControlStateNormal];
[yesButton setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
[self.buttonsView addSubview:yesButton];
 
UIButton *noButton = [UIButton buttonWithType:UIButtonTypeCustom];
noButton.autoresizingMask = UIViewAutoresizingFlexibleHeight;
noButton.backgroundColor = [UIColor redColor];
noButton.frame = CGRectMake(80, 0, 80, self.bounds.size.height);
[noButton setTitle:@"No" forState:UIControlStateNormal];
[noButton setTitleColor:[UIColor whiteColor] forState:UIControlStateNormal];
[self.buttonsView addSubview:noButton];
```

这将创建一个包含按钮的视图。这里需要注意的是，视图宽度应该是所有的按钮宽度的总和，它的需要插入在滚动视图之前（为了让显示在下面）。然后，我们只需要添加我们想要的按钮。为了使代码尽可能的简洁，我没有添加任何按钮的点击事件处理代码。

如果你此时运行程序，它可以滑动从而让按钮出现，但是如果你再iPhone6 或者更大的屏幕上运行的时候，将会遇到一个问题，我们稍后会解决它。另外一个需要解决的问题就是当我们在滑到一半就停止的时候会出现按钮显示一半的情况。我们先来修复它。

```
- (void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset {
    if (scrollView.contentOffset.x < -scrollView.contentInset.left) {
        targetContentOffset->x = -scrollView.contentInset.left;
    } else {
        *targetContentOffset = CGPointZero;
    }
}

```

当你的类得到用户停止滑动的通知（通过UIScrollViewDelegate协议）你需要将滚动视图滚动到按钮的最左端（如果滑动量超过按钮）或者重新隐藏按钮（如果滑动量不够）

到了这里我们完成了一些基本的工作。接下来我们来处理一些细节上的问题。继续往下看


接下来最大的问题就是就是来适应更大的屏幕，（例如iPhone6）我们可以通过一个简单的方法来修复这个问题（如果你有更好的方法，务必告诉我）。

```
- (void)layoutSubviews {
    [super layoutSubviews];
    // This is necessary to ensure that the content size scales with the view.
    self.scrollView.contentSize = self.contentView.bounds.size;
    self.scrollView.contentOffset = CGPointZero;
}


```

这段代码的作用就是在单元格的面积发生变化的时候，修复内容大小和偏移量

这会避免图形出现问题。接下来，我们需要阻止滚动视图向另外一个方向滚动，（因为没有任何按钮来显示）。这非常简单：

```
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    if (scrollView.contentOffset.x > 0) {
        scrollView.contentOffset = CGPointZero;
    }
}
```

如果用户向另外一个方向滑动，将偏移量设置为0来阻止它。

最后，你可能想让按钮安静的呆在原地，而不是与单元格一起移来移去。要做到这一点，我们将简单地移动按钮容器视图来抵消滚动偏移量。我们可以做到这一点，通过改变scrollViewDidScroll：上述方法如下：

```
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    if (scrollView.contentOffset.x > 0) {
        scrollView.contentOffset = CGPointZero;
    }
 
    self.buttonsView.frame = CGRectMake(scrollView.contentOffset.x, 0, 160, self.buttonsView.frame.size.height);
}

```

##还可以做些什么
现在，我们已经制作了最基本功能，还有很多地方仍然可以提高。比如说：

* 滚动表视图时自动隐藏按钮
* 在滑动一个单元格的时候自动隐藏其它的单元格按钮
* 允许在单元的两侧的按钮
* 启用在外部创建按钮（例如，在视图控制器）
* 动态计算按钮的大小
* 改进开启和关闭的动画效果


最后，我实现上面的功能和所提供的代码在GitHub上：[GitHub](https://github.com/blixt/SwipeableTableViewCell)
感谢您的阅读！如果有任何问题。最简单的方式就是在我Twitter下留言，我得名字叫做@blixt。




