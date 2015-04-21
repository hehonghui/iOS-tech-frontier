`iOS开发-可滑动的单元格`
---

>* 原文链接 : [Swipeable table view cells in iOS apps](https://medium.com/ios-os-x-development/swipeable-table-view-cells-in-ios-apps-472da0af1935)
* 译者 : [Harries Chen](https://github.com/mrchenhao) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  校对中

I’ve been diving into iOS for the past month building an app for my startup. One of the things I want the app to do is to let the user swipe a cell in a table view to reveal more options (similar to how the Mail app reveals “More” and “Delete” options). In this article I will discuss a few way of doing this, including how to build it yourself.

我在过去的一个月里致力于开发我的创业项目，其中有一个功能就是我想让用户通过左右滑动表格来显示更多的选项（就像系统自带的邮件程序现实更多和删除一样，在这篇文章中我将讨论一些关于这方面的方法，以及如何动手实现它）

First, let’s go through the existing solutions out there:

UITableViewRowAction (new in iOS 8)
This option is the best if you’re just looking to add a few buttons that appear when swiping left-to-right on the table view cell.
SWTableViewCell
A full-featured implementation of buttons for both right-to-left swipe and left-to-right swipe. This will work for older iOS versions too.
MGSwipeTableCell
Another full-featured implementation, with even more functionality.

首先，让我们来看一下现有的解决方法。

* UITableViewRowAction (new in iOS 8)

这是最好的方法如果你只是希望加一些从左滑到右才会出现的按钮。

* [SWTableViewCell](https://github.com/CEWendel/SWTableViewCell)

这是一个功能很全的按钮库，既支持从左滑到右也支持右滑到左，同时也可以在iOS8以前的版本中使用

* [MGSwipeTableCell](https://github.com/MortimerGoro/MGSwipeTableCell)

另外一个功能更加强大的库

Hopefully, if you’re looking for swipeable table view cells, you’ll find what you need in one of the above solutions. But then why am I writing about how to build it yourself? In my case, I needed some of the functionality of MGSwipeTableCell, but it turned out to do things in ways that didn’t work for me. If you’re also looking to build it from scratch, or just want to know how it works, read on!

如果你想要实现表格的左右滑动功能，你可以从上面的解决方按照找到你需要的。但是我为什么要写通过自己来实现它呢？就我而言，我需要MGSwipeTableCell的一些功能，当时有时候它对我并没有用，如果你想要自己来实现它，或者仅仅只是想要知道它的原来，那么接下去看。

Anatomy of a swipeable UITableViewCell
Making a table view cell swipeable is actually pretty simple. The gist of it is to create a UIScrollView inside the cell’s contentView and then populate it with one UIView for the buttons and one UIView for the content. The hardest part is really configuring the scroll view. Here’s an image showing the main values we’ll be touching on the UIScrollView:


##原理剖析

制作一个可滑动的UITableViewCell其实是一件非常简单的事情，它的要点就是创建一个在UIScrollView在单元格的contentView里面，在UIScrollView里面有两个UIView，一个用于放置按钮，一个用于放置单元格的内容，它的难点在于如何配置滑动页面，下面是一张图片来展示UIScrollView里的主要内容。

![这里写图片描述](http://img.blog.csdn.net/20150420235653232)

frame
The size of the view (the actual area that will be rendered on screen). In the picture above I gave it more height to make room for the labels, but in our code it will have the same height as the content.
contentSize
In scroll views, the content will often be a different size than the view itself. When content is bigger than the view, the view will scroll to let the user see all of the content. This property represents the width and height of the content inside of the scroll view. In our case, we’ll actually keep it the same size as the view, thanks to a property called contentInset.
contentInset
This is the property that will make our scroll view actually scroll. Normally, when the contentSize property is set to the same (or smaller) size as the view itself, the view won’t scroll. However, contentInset will extend the area that is scrollable outside the content, so that you can scroll past the content boundary. Why is this useful? It’s used by pull-to-refresh controls, for example, to allow a throbber to show above the content. In our case, we’ll use it to make room for revealing the buttons.
contentOffset
This is the value that represents how much the scroll view is currently scrolled. As the user scrolls around, this value will change. You can also change it programmatically, which we’ll do later to snap the scrolling to just where the buttons end.


* frame

视图的大小，（也就是实际在屏幕上显示的面积），在上图中，外面的高度看起来会比UIScrollView的高度高很多，实际上在代码中他们的高度是一样的，只是为了更好的展示。

* contentSize

在可滚动的视图中，通常显示的大小和实际内容的大小是不同的，当实际内容比显示的内容要大的时候，就可以进行滚动，以便让用户看到所有的内容。这个属性代表了在scrollview里面的内容的宽和高。在我们的例子中，我们实际上会保持它和视图大小相同，这要归功于一个叫做contentInset属性

* contentInset

这个属性将使我们的滚动视图实际上滚动。通常，当contentSize属性被设置为与frame相同的（或更小）的尺寸作为视图本身，视图并不会产生滚动的效果。然而，contentInset将延长使内容产生滚动，让你可以滚动过去的内容边界的区域。这有什么用吗？它可以用在上拉至刷新的控制上，例如，以允许活动指示器，以显示上述内容。在这个例子中，我们将使用它来腾出空间已显示额外的按钮。

* contentOffset

这个值代表当前滚动视图的滚动量，当用户滚动的时候，这个值将会被改变，你也可以通过程序来改变它的值，我们随后将会用这个值来让按钮出现。

Implementing the cell, step by step
This section will demonstrate how to actually make the cell display buttons when swiping on it. The code examples will be simplified to focus on the concept. At the end I will provide a full implementation.

First, let’s subclass UITableViewCell to begin implementing our swipeable table cell.

The easiest way to subclass classes is to go to File > New > File… (⌘N). Pick UITableViewCell as the class to subclass.

##一步步实现

本节将展示如何通过滑动来使单元格显示按钮。代码将被简化以便更好的理解这一概念。最后我将提供完整的代码。

第一步，子类化UITableViewCell

![这里写图片描述](http://img.blog.csdn.net/20150420235749907)


子类化最简单的方法就是文件>新建>文件...（⌘N）。选择的UITableViewCell作为类的子类。

Since we’ll want to have control over scroll behaviors, we need to specify that our class supports the UIScrollViewDelegate protocol. Do this by adding the protocol in angle brackets after the interface declaration:


因为我们希望实现对滚动控制，所以我们需要指定我们的类实现UIScrollViewDelegate协议。方法是后面加上尖括号，在里面写上需要实现的协议

```
@interface  SwipeableTableViewCell：的UITableViewCell <UIScrollViewDelegate>

```


Next up, we’ll set up the scroll view. This is done when the cell initializes. Depending on how you create your cells, the cell may initialize in either initWithCoder: or initWithStyle:reuseIdentifier:. I would recommend creating a common method for setting up your cell and calling it from both places.


接下来，我们将设置滚动视图。这会在初始化的时候被完成。选择你初始化的方式，可以是`initWithCoder：`也可以是 `initWithStyle：reuseIdentifier：` 。我建议创建一个common方法来配置单元格，以便从不同的初始化放来调用。

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

There’s a few things happening here after we create the scroll view. First, we set its resizing mask. This means how the view will act when its super view changes size. We want the scroll view to fill the entire cell when it changes size at runtime (which is likely to happen with iPhone 5, 6 and 6 Plus all having different screen widths). Then we set the content size to be the same, but also add a left inset with the width of the buttons we intend to add. Setting scrollsToTop to be turned off means this scroll view doesn’t care about the status bar being tapped (which normally scrolls a view to the top). This’ll let our table view take care of that instead of breaking that behavior. We also turn off horizontal and vertical scroll indicators so that the little translucent black bar doesn’t appear when we scroll.

当我们我们创建滚动视图后，我们需要做一些事情。首先，我们设置它的大小的。这意味着在父视图大小发生改变的时候应该怎样改变。我们希望滚动视图填充整个单元格当它运行时改变大小的时候（这很可能发生在iPhone 5，6和6 plus都具有不同的屏幕宽度）。然后我们设置与滚动视图相同大小的内容，在左边插入与我们所需要添加的按钮宽度相同的视图。关闭scrollsToTop事件，因为我们不关心这个状态栏是否被点击（通常用于滚动视图顶部）。这将让我们的表视图利用这特性，而不是打破这一行为。同时我们也关掉水平和垂直滚动栏，这样，当我们滚动的时候半透明的黑条不会出现。

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

Nothing really surprising going on above, we’re just adding in the necessary parts for presenting the main area of the table cell (which is normally in the contentView).

必须惊讶于上述代码，我们只是增加了必要的部分呈现的表格单元格的主要区域

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

This creates a view which contains the action buttons. The important things to note here is that the view has the same width as all the buttons together, and it’s inserted as the first view in the hierarchy (which will make it appear under everything else). Then we just add the buttons we want there. I didn’t add tap handlers to keep the example short.

If you run the app now, it should let you scroll to make the button appear. If you’re running this on an iPhone 6 or larger you may notice a glitch that we’ll fix later. Another thing missing is the snapping in place of the button being either visible or not visible when you stop scrolling. Let’s fix that now.


这将创建一个包含按钮的视图。这里需要注意的是，视图宽度应该是所有的按钮宽度的总和，它的插入在页面的第一次层次结构（为了让显示下面的视图）。然后，我们只需要添加我们想要的按钮。为了使代码尽可能的简洁，我没有添加任何按钮的点击事件处理代码。

如果你此时运行程序，它可以滑动从而让按钮出现，但是如果你再iPhone6 或者更大的屏幕上运行的时候，将会遇到一个问题，我们稍后会解决它。另外一个需要解决的问题就是当我们在滑到一般就停止的时候会出现按钮显示一般的情况。我们先来修复它。

```
- (void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset {
    if (scrollView.contentOffset.x < -scrollView.contentInset.left) {
        targetContentOffset->x = -scrollView.contentInset.left;
    } else {
        *targetContentOffset = CGPointZero;
    }
}

```

When your class gets notified that the user has stopped swiping (via the UIScrollViewDelegate protocol), you make the scroll view scroll back to either exactly where the buttons end (if they swiped past the buttons), or back to hide the buttons completely (if they didn’t swipe far enough).

That’s pretty much the basics of building a swipeable cell! We’ve got a few loose ends that we’ll tie up now, so keep reading.

The biggest problem with our code right now is handling of bigger screens (such as iPhone 6). We can fix that with this simple “hack” (please let me know if this can be done better!)

当你的类得到用户停止滑动的通知（通过UIScrollViewDelegate协议）你需要将滚动视图滚动到按钮的最左端（如果滑动量超过按钮）或者重新隐藏按钮（如果滑动量不够）

到了这里我们完成了一些基本的工作。接下来我们来处理一些细节上的问题。继续往下看


我们当前最大的问题就是就是来适应更大的屏幕，（例如iPhone6）我们可以通过一个简单的方法来修复这个问题（如果你有更好的方法，务必告诉我）。

```
- (void)layoutSubviews {
    [super layoutSubviews];
    // This is necessary to ensure that the content size scales with the view.
    self.scrollView.contentSize = self.contentView.bounds.size;
    self.scrollView.contentOffset = CGPointZero;
}


```

That’ll avoid graphical glitches. Next up, we may want to prevent the cell from being scrollable in the other direction (which doesn’t have any buttons to show). That’s simple:

这段代码的作用就是在单元格的面积发生变化的时候，修复内容大小和偏移量

这会避免图形出现问题。接下来，我们需要阻止滚动视图向另外一个方向滚动，（因为没有任何按钮来显示）。这非常简单：

```
- (void)scrollViewDidScroll:(UIScrollView *)scrollView {
    if (scrollView.contentOffset.x > 0) {
        scrollView.contentOffset = CGPointZero;
    }
}
```

Finally, you may want the buttons to stay in place under the cell instead of moving with the cell content. To make that happen, we will simply move the buttons container view to counteract the scrolling offset. We’ll do this by changing the scrollViewDidScroll: method above to the following:

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

Where to go from here
Now that we’ve gone through the basics there’s many things that can still be improved. Here’s a few:

Auto-hiding buttons when scrolling the table view
Auto-hiding buttons when swiping open another cell
Allowing buttons on both sides of the cell
Enabling button creation outside the class (e.g., in the view controller)
Dynamically calculating the buttons container size
Making opening/closing of the actions menu behave better
To fast-forward a bit, I’ve implemented the features above and made the code available on GitHub: https://github.com/blixt/SwipeableTableViewCell

Thank you for reading! Please reach out to me with any comments. The easiest way to reach me is on Twitter, where I’m known as @blixt.


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




