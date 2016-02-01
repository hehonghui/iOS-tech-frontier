> * 原文链接 : [UIStackView Tutorial: Introducing Stack Views](http://www.raywenderlich.com/114552/uistackview-tutorial-introducing-stack-views)
* 原文作者 : [ Jawwad Ahmad](http://www.raywenderlich.com/u/jawwad)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [kmyhy](https://github.com/kmyhy) 


> Ray注：恭喜你！为了推广《iOS 9 Feast》一书，这本书的第一部分教程已经为你解锁了。本文来自于《iOS 9 Tutorials》中的一个章节，是该章节的简化版，通过本文，便可对全书内容窥见一斑。祝你阅读愉快！

家家有本难念的经。现在有一个新需求，要你在运行时增加或删除一个视图，这就需要我们重新调整邻近视图的位置。

你会怎么办？也许你会在故事板中新建一些布局约束连接，以便能够安装或卸载其中的一些约束？或者你会使用第三方库来实现？或者看任务复杂程度完全用代码实现？

也许这个在视图附近的视图树中的所有View都不需要在运行时改变，但当你将新视图添加到故事板时，仍然要想方设法为它挤出空间来。

你是否有过这样的时候：因为觉得要解决的布局约束冲突太过棘手，最终不得不清除所有约束并一条条地重新添加约束。

通过对UIStackView的介绍，完成上述任务变得微不足道。Stack View提供了一个对多个视图进行水平或垂直布局的方法。通过对几个属性进行简单设置，比如对齐、分布和间距，可以让我们让其所包含的视图适应于其有效空间。

> 注意：UIStackView教程假设你基本掌握了自动布局。如果你还不熟悉自动布局，请参考[“自动布局教程：第1部分”](http://www.raywenderlich.com/83129/beginning-auto-layout-tutorial-swift-part-1)。

##开始

在本教程，你将创建一个名为Vacation Spots的App。这个App非常简单，仅仅显示了一个能让你原离一切尘嚣的景点列表。

先不要忙着打点行装，因为这个App还有几个问题需要你用Stack View来进行解决，当然，这比你光用自动布局来解决要简单多了。

首先下载本教程的[开始项目](http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/VacationSpots_Starter.zip)，然后在iPhone6S 上运行。

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/01-table-view-is-now-correct_750x1334-281x500.png"/>

点击London单元格，跳转到伦敦的介绍页面。

一瞥之下，这个页面似乎是OK的，但它其实存在着这几方面的问题。

 查看视图下方的那排按钮。它们当前的位置是以固定的间距排列的，因此它们无法适应屏幕宽度的变化。要看出问题之所在，你只需按下command+左箭头旋转模拟器屏幕为横屏即可。

 <img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/02-issues-visible-in-landscape-view_1334x750-480x270.png"/>

 点击WEATHER旁边的Hide按钮。它成功地隐藏了文本，但它下面的内容却保持在原来的位置，从而留下了一大块的空白区域。
 
 <img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/03-hide-weather-issue_750x1334-281x500.png"/>

 各版块的顺序也应当调整。如果将"what to see"版块放到"why visit"版块的右边，而不是将"wheather"版块插在两者之间会更好。

 在横屏模式下，底部的一排按钮太靠下了。如果在横屏模式下，将各版块之间的距离缩小一点会更好。

现在，你已经了解了将要做的工作，是该进入项目中进行工作的时候了。

打开Main.storyboard，找到Spot Info View Controller这个Scene。哇！在你的Stack View上有很多颜色！

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/04-colorful-scene-in-storyboard_504x636-396x500.png"/>

将这些标签和按钮设为不同的背景色，是为了在运行时效果更直观。就是在故事板中，这也有助于看到Stack View属性的改变导致其内部视图的变化。

如果你想在运行App时看见这些颜色，你可以在SpotInfoViewController的viewDidLoad()方法中将下列语句注释。当然现在，还不需要这样做：

```
// Clear background colors from labels and buttons
// 清空标签和按钮的背景色
for view in backgroundColoredViews {
  view.backgroundColor = UIColor.clearColor()
}
```


同时，所有已连接Outlet的标签都会有一个提示文本，用于指明它们所连接的Outlet变量名。这将在运行时指明是哪个标签。例如，带有<whyVisitLabel>字样的标签将连接到下面这个Outlet变量：

```
@IBOutlet weak var whyVisitLabel: UILabel!
```

还要注意另外一点，故事板中Scene的大小并不是默认的启用了Size类时的600x600。

Size类仍然可用，但在初始导航控制器的属性面板中，Simulated Metrics下的Size属性却被设置成了iPhone 4-inch。这仅仅是为了让我们更容易使用故事板一点，Simulated Metrics属性在运行时并没有任何影响——不同设备上视图的大小仍然会自动改变。

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/05-simulated-metrics-iphone-4-inch_639x173.png"/>

##第一个Stack View

我们首先要做的是底下一排按钮之间的间距。一个Stack View能够将它所含的View以各种方式沿其轴向进行分布，同时也可以将View沿某个方向等距分布。

幸运的是，将已有的View添加到一个新的Stack View中并不是一件多复杂的事情。首先，用Command+左键同时选中Spot Info View Controller底下一排的所有按钮：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/06-select-bottom-row-of-buttons_420x80.png"/>

如果Outline视图未打开，先点击故事板画布左下角的Show Document Outline按钮，打开Outline视图：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/07-document-outline-button_120x40.png"/>

在Outline视图中查看是否3个按钮都已经选中：

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/08-verify-button-selection_360x90.png"/>

如果没有全部选中，你仍然可以用Command+左键在Outline视图中重新选择它们。

一旦选好这3个按钮，找到故事板画布左下角Auto Layout工具栏中新增的Stack按钮并点击它：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/09-stack_button_outlined_148x52.png"/>

这些按钮将被嵌到一个新的Stack View中：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/10-bottom-row-is-now-in-stack-view_640x100.png"/>

3个按钮现在一个挤着一个——你将很快解决这个问题。

虽然Stack View会管理这些按钮的位置，但它自身仍然需要我们为它添加自动布局约束。

当你将View添加到Stack View中时，任何相对于其他视图的约束都被删除。以之前添加的按钮为例，Submit Rating按钮上方有一个相对于Rating标签底部的垂直间距约束：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/11-prior-constraint_420x90.png"/>

现在点击Submit Rating 按钮，可以看到它已经丢失了所有约束：

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/12-no-more-constraints_400x80.png"/>

另一种检验这些约束已经删除的方法是用Size面板进行查看（⌥⌘5）：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/13-check-size-inspector_640x80.png"/>

为了给Stack View添加布局约束，首先需要选中它。要在故事板选取一个充满了子视图的Stack View还是比较难的。

一个简单的法子是在Outline视图中选取Stack View：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/14-stack-view-document-outline-selection_660x80.png"/>

另一个技巧是在Stack View 的任意地方按下Shift+右键或者Control+Shift+左键（如果你正在用触控板的话）。这时将弹出一个上下文菜单，列出了位于所点击的地方的View树，你可以在这个菜单中选择Stack View。

现在，我们用Shift+右键的方式选取Stack View：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/15-select-stack-view-in-view-hierarchy-menu_400x280.png"/>

然后点击自动布局工具栏中的Pin按钮，添加一个约束：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/16-pin-button_142x57.png"/>

首先勾选Constrain to margins。然后在Stack View四周添加下列约束：

```
Top: 20, Leading: 0, Trailing: 0, Bottom: 0
```

仔细检查top、leading、trailing、bottom中的数字并确保它们的I型柱都被选中。然后点击Add 4 Constraints：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/17-bottom-stack-view-constraints_264x364.png"/>

现在Stack View的尺寸正确了，但它会拉伸第一个按钮的大小使其填充所有剩余空间：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/18-first-button-is-stretched_400x80.png"/>

决定Stack View如何将它的subview沿轴向分布的属性是它的distribution属性。当前，这个属性的值是Fill，这表示subview会沿轴向完全占据Stack View。因此，Stack View会拉伸其中一个subview使其填充剩余空间，尤其是水平内容优先级最低的那个，如果所有subview优先级相同，则拉伸第一个subview。

当然，你并不希望按钮将整个Stack View都填充——你想让它们等间距分布。

确保Stack View处于选中状态，打开属性面板。将Distribution属性由Fill修改为Equal Spacing：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/19-change-distribution-to-equal-spacing_640x148.png"/>

编译运行，点击某个单元格，旋转模拟器（⌘→）。你将看到最下一排按钮现在按照等间距排列了！

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/20-now-buttons-are-equally-spaced_1334x750-480x270.png"/>

如果不使用Stack View，则解决这个问题只能使用空白的View来分隔这些按钮，在按钮之间摆放上一些用于分隔空间的 Spacer View。所有的Spacer View都要添加等宽约束，以及许多额外的约束，才能将这些Spacer View布局正确。

这看起来如下图所示。为了直观起见，这些Spacer View的背景色设置成了浅灰色：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/21-alternate-solution-1_346x76.png"/>

一旦你不得不在故事板中这样做的时候，就会导致出许多问题，尤其是许多视图都是动态的时候。如果要在运行时添加一个按钮或者隐藏/删除一个按钮时，要想调整这些Spacer View和约束真的是不太容易。

要隐藏Stack View中的视图，你只需要设置该View的Hidden属性为true，剩下的工作Stack View会自己完成。这也是我们解决用户隐藏WEATHER标签下文本的主要思路。在本文后面的内容中，当你将weather标签添加到Stack View之后，我们再来介绍这个问题。

##将各版块转换到Stack View

我们将把SpotInfoViewController中的所有版块都添加到Stack View中。这将有助于我们完成剩下的任务。接下来我们先调整Rating版块。

###Rating版块

就在前面创建的Stack View上方，选取RATING标签，以及旁边的显示为几个星形图标的标签：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/22-select-rating-label-and-stars-label_640x74.png"/>

然后点击Stack按钮将它们嵌到一个Stack View中：

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/23-after-clicking-stack-button_640x74.png"/>

然后点击Pin按钮。勾选Constrain to margins，并添加如下约束：

```
Top: 20, Leading: 0, Bottom: 20
```

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/24-add-second-stack-view-constraints_264x171.png"/>

打开属性面板，将间距设置为8：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/25-set-spacing-to-8_259x87.png"/>

你可能会看到一个 Misplaced Views的布局约束警告，同时星星标签会显示将会被拉伸到视图之外：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/26-stars-label-weirdly-stretched_640x85.png"/>

有时候Xcode会临时提示一些警告，或者显示Stack View的位置不正确，这些警告会在你添加其他约束后消失。你完全可以忽略这些警告。

要解决这个警告，我们可以修改一下Stack View的Frame然后又改回，或者临时修改它的一条布局约束。

让我们试一下。先将Alignment 属性从Fill修改为Top，然后又改回原来的Fill。你将看到这下星星标签显示正常了：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/27-change-alignment-to-top-and-back_640x85.png"/>

编译运行，进行测试。
###解散一个Stack View
在继续后面的内容之前，先接受一些“急救”训练。有时你发现一些Stack View不再需要了，可能因为过时了，代码进行了重构或者仅仅是突发奇想。

幸运的是，有一种简单的方法可以解散一个Stack View。

首先，选定想解散的Stack View。按下Option键，点击Stack 按钮。这将弹出一个上下文菜单，然后点击Unembed：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/28-how-to-unembed_186x71.png"/>

另一种解散的方法是选中Stack View，然后点击Editor\Unemebed菜单。

###垂直的Stack View

接下来，你将创建一个垂直的Stack View。选中WHY VISIT标签及下面的<whyVisitLabel>标签：

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/29-select-why-visit-labels_640x90.png"/>

Xcode会自动根据这两者的位置推断出这将是一个垂直的Stack View。点击Stack 按钮将二者嵌到一个Stack View：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/30-embed-why-visit-labels_640x90.png"/>

下面的那个标签原先有一个right margin of the view的约束，但嵌到Stack View之后被移除了。现在，这个Stack View还没有约束，因此它自动适应了两个标签中的最宽的一个的宽度。

选中Stack View，点击Pin按钮。勾选Constrain to margins，设置Top、Leading、Trainling为0。

然后，点击Bottom右边的下拉按钮，从列表中选择WEATHER（curent distance =20）：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/31-dont-select-nearest-neighbor-constraint_463x417.png"/>

默认，约束是相对于距离最近的对象，对于Bottom约束来说就是距离它15像素的Hide按钮。但我们其实是想让约束相对于WEATHER标签。

最后点击Add 4 Constraints按钮。显示结果如下图所示：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/32-why-visit-stack-view-stretched_640x90.png"/>

现在，我们有了第二个Stack View，它的右边对齐于View的右边。但是底下的标签仍然是原来的宽度。你接下来要修改Stack View的alignment属性以解决这个问题。

###Alignment属性

Alignment属性决定了Stack View如何沿它轴向的垂直方向摆放它的subview。对于一个垂直的Stack View，这个属性可以设置为Fill、Leading、Center和Trailing。

对于水平的Stack View，这个属性则稍有不同：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/33-horizontal-and-vertical-alignment_594x171.png"/>

.Top取代了.Leading，.Bottom取代了.Trailing。此外，水平Stack View还多出了两个属性值：.FirstBaseLine和.LastBaseLine。

这是对于垂直Stack View，将Alignment设置为不同属性值所造成的显示效果：

Fill:

 <img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/34-alignment-fill_640x64.png"/>
 
Leading:

 <img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/35-alignment-leading_640x64.png"/>

Center:

 <img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/36-alignment-center_640x64.png"/>

Trailing:

 <img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/37-alignment-trailing_640x64.png"/>

当你测试完所有Alignment值的布局效果后，将Alignment修改为Fill：

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/38-now-select-alignment-fill_640x64.png"/>

然后编译运行，测试是否正常。

将Alignment设置为Fill，表示所有View将沿与Stack View轴向垂直的方向进行全占式分布。这会让WHY VISIT标签扩展它的宽度到100%.

如果我们只想让底下的标签将宽度扩展到100%怎么办？

这个问题现在看来还不是多大的问题，因为两个标签在运行时的背景色都是透明的。但对于Weather版块来说就不同了。

我们将用另外一个Stack View来说明这个问题。

##将"What to see"换成Stack View

这个版块和前面一个版块类似，因此我们会简述。

 1.首先，选中 WHAT TO SEE标签和<whatToSeeeLabel>标签。

 2.点击Stack按钮。

 3.点击Pin 按钮。

 4.勾选Constrain to margins，添加4个约束：
 
 
  Top: 20, Leading: 0, Trailing: 0, Bottom: 20

 5.将Stack View的Alignment设置为Fill。

现在你的故事板看起来像这样：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/39-after-what-to-see-section_640x308.png"/>

编译运行，测试是否有任何改变。

现在就只剩下weather版块了。

##将Weather版块转换成Stack View

Weather版块相对复杂一些，因为它多了一个Hide按钮。

一种方法是使用嵌套的Stack View，先将WEATHER标签和Hide按钮嵌到一个水平StackView，再将这个Stack View和<weatherInfoLabel>标签嵌到一个垂直Stack View。

看起来是这个样子：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/40-weather-stack-in-stack_640x92.png"/>

注意，WEATHER标签被拉伸为和Hide按钮一样高了。这并不合适，因为这会导致WEATHER标签和下面的文本之间多出了一些空间。

注意Alignment属性负责Stack View轴向垂直的方向上的布局。所以，我们需要将Alignment属性设置为 Bottom：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/41-weather-stack-in-stack-alignment-bottom_640x92.png"/>

但我们并不想让Hide按钮的高度来决定Stack View的高度。

正确的方法是让Hide 按钮不要和Weather 版块呆在同一个Stack View中，或者任何别的Stack View中。

这样，在顶层View中还会保留一个subview，你将为它添加一个相对于WEATHER标签的约束——WEATHER标签嵌在Stack View里的。也就是说，你要为位于Stack View之外的按钮加一个约束，这个约束是相对于Stack View内的一个标签！

选中WEATHER标签和<weatherInfoLabel>标签：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/42-select-weather-and-info-label_640x92.png"/>

点击 Stack 按钮：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/43-weather-click-stack-button_640x92.png"/>

点击Pin 按钮，勾上Constrain to margins，然后添加如下约束：

```
Top: 20, Leading: 0, Trailing: 0, Bottom: 20
```

将Stack View的Alignment设为Fill ：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/44-weather-alignment-fill_640x92.png"/>

我们需要在 Hide 按钮左边和WEATHER标签右边加一条约束，这样WEATHER 标签的宽度就不会拉满整个Stack View了。

当然，底下的<weatherInfoLabel>标签宽度还是需要100%占满的。

我们是通过将WEATHER标签嵌到一个垂直Stack View 来实现的。注意，垂直Stack View的Alignment 属性可以设置为 .Leading，如果将Stack View拉宽，则它里面的View 会保持左对齐。

从Outline视图中选取WEATHER 标签，或者用Control+Shift+左键的方式选取WEATHER 标签：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/45-select-just-the-weather-label_640x92.png"/>

然后点击Stack 按钮：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/46-weather-in-horizontal-stack_640x92.png"/>

确保Axis 为 Vertical 的情况下，将Alignment 设置为 Leading：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/47-vertical-and-leading_640x92.png"/>

好极了！这样外面的Stack View 会将内部的 Stack View 拉伸为完全填充，而内部的 Stack View 则允许标签保持原有宽度！

编译运行。这究竟是为什么？Hide按钮跑到了文字中间去了？

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/48-hide-label-incorrect-position_750x573-419x320.png"/>

这是因为WEATHER标签是嵌在 Stack View 中的，任何它和 Hide 按钮之间的约束都被删除了。

从Hide 按钮用右键拖一条新的约束到 WEATHER 标签：

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/49-drag-to-weather-label_380x94.png"/>

按下Shift键，同时选择Horizontal Spacing 和 Baseline。然后点击 Add Constraints：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/50-add-multiple-constraints_380x224.png"/>

编译运行。Hide 按钮的位置现在对了，而且当按下Hide 按钮，位于Stack View 中的标签被隐藏后，下面的视图也会被调整——根本不需要我们进行手动调整。

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/51-hide-button-works_750x732-328x320.png"/>

选择是有的版块都在自己的 Stack View中，我们将在它们外边在套上一个 Stack View，这样最后的两个任务就会变得很轻松。

##顶级 Stack View

在Outline 视图中，用Command+左键选择5个最顶级的 Stack View：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/52-select-all-stack-views-in-outline_640x260.png"/>

然后点击 Stack 按钮：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/53-stack-all-the-views_640x185.png"/>

点击Pin 按钮，勾上 Constrain to margins，将 4 个边的约束都设为0。然后将Spacing 设置为20，Alignment 设为 Fill。现在故事板会是这个样子：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/54-set-the-spacing-to-20-and-alignment-to-fill_640x300.png"/>

编译运行：

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/55-hide-button-lost-again_750x487-480x312.png"/>

噢！这个 Hide 按钮又失去了它 的约束！因为包含 WEATHER 标签的Stack View的外边又套了一层 Stack View。这不是什么大问题，就像之前你做过的那样，再重新为它添加约束就是了。

右键从Hide 按钮拖一条约束到 WEATHER标签，按下 Shift 键，同时选择 Horizontal Spacing 和 Baseline。然后点击 Add Constraints：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/09/56-add-constraints-to-button-again_380x223.png"/>

编译运行。Hide按钮的位置正确了。

###重新调整视图位置
现在，所有的版块都被嵌到一个顶级的 Stack View中了，我们想修改一下 what to see版块的位置，让它位于 weather 版块之后。

从 Outline 视图中选择中间的的 Stack View，然后将它拖到第一、二个 Stack View 之间。

> Note: Keep the pointer slightly to the left of the stack views that
> you’re dragging it between so that it remains a subview of the outer
> stack view. The little blue circle should be positioned at the left
> edge between the two stack views and not at the right edge:

> 注意：让箭头稍微偏向你正在拖的Stack View左边一点，以便它能够作为外层 Stack View 的 subview 添加。蓝色的小圆圈应当位于两个 Stack View 之间的左端而不是右端：


 <img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/57-drag-and-drop-to-reposition-section_639x130.png"/>

现在，weather版块是从上到下的第三个版块，由于 Hide 按钮它并不是 Stack View的subview，所以它不会参与移动，它的frame当前是不正确的。

点击 Hide 按钮，选中它：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/58-hide-button-not-moved_640x130.png"/>

然后点击自动布局工具栏中的 Resolve Auto Layout Issues 按钮，选择 Update Frames：

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/09/59-resolve-auto-layout-issues_356x269.png"/>

现在 Hide 按钮将回到正确的位置：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/60-hide-button-back-to-correct-position_640x130.png"/>

好，对于你来说，用自动布局调整视图的位置或者重新添加约束都不再是什么问题了，但为什么还是有一种不太完美的感觉呢？

###基于配置的 Size 类

最后还有一个任务没有完成。在横屏模式，垂直空间是比较珍贵的，你想将这些版块之间靠得更近一些。要实现这个，你需要判断当垂直Size类为compact时，将顶层 Stack View的 Spacing属性由 20 改成 10.

选择顶层 Stack View，点击 Spacing 前面的 + 按钮：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/61-select-plus-button_260x120.png"/>

选择 Any Width > Compact Height：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/09/62-anywidth-compact-height_403x108.png"/>

在新出现的 wAny hC 一栏中，将 Spacing 设为 10：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/63-set-spacing-to-10_260x160.png"/>

编译运行。在竖屏模式下Spacing不会改变。旋转模拟器（⌘←），你会看到各版块之间的间距减少了，现在底部按钮之间的空间也变大了：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/09/64-spacing-in-iphone-landscape_1334x750-480x270.png"/>

如果你没有添加最外层的 Stack View，你仍然可以使用 Size 类将每个版块之间的垂直间距设置为 10，但这就不是仅仅设置一个地方就能够办到的了。

剩下的时间将会有更加愉快的事情发生，那就是动画！

##动画

现在，在隐藏和显示天气信息时仍然会觉得有一些突兀。你将增加一个动画使这个转换变得更平滑。

Stack View完全支持 UIView 动画。也就是说要以动画方式显示/隐藏它所包含的subview，只需要简单地在一个动画块中切换它的 hidden 属性。

让我们来看看代码怎么实现。打开 SpotInfoViewController.swift，找到
updateWeatherInfoViews(hideWeatherInfo:animated:)方法。

将方法的最后一行：

```
weatherInfoLabel.hidden = shouldHideWeatherInfo
```

替换为：

```
if animated {
  UIView.animateWithDuration(0.3) {
    self.weatherInfoLabel.hidden = shouldHideWeatherInfo
  }
} else {
  weatherInfoLabel.hidden = shouldHideWeatherInfo
}
```

编译运行，点击Hide 按钮或 Show 按钮。是不是加入动画之后看起来要好得多呢？

除了对 Stack View 中的视图以动画的方式设置 hidden 属性，你也可以对 Stack View 自身的属性使用 UIView 动画，例如 Alignment 属性、 Distribution 属性、 Spacing 属性和 Axis 属性。 

##接下来做什么

你可以从[这里](http://cdn1.raywenderlich.com/wp-content/uploads/2015/09/VacationSpots_Complete.zip)下载完整的示例项目。

在这篇 UIStackView 教程里，你学习了什么是 Stack View，以及它的各个属性，通过这些属性可以对它的 subview 进行布局。Stack View 是高度可配置的，要达到同样的效果往往不止一种实现方式。

最好的学习方式是自己修改各种属性并体验最终效果。不要一个属性一个属性地单独测试，而是要试验各种属性相互搭配后的布局效果。

本教程是"iOS 9 Tutorials"第6章"UIStackView and Auto Layout changes"，以及第7章"Intermediate UIStackView"的精简版。如果你想学习更多关于 UIStackView以及其它 iOS9 的新特性，请看这本书！

同时，如果你对本文有任何问题和建议，请加入到下面的讨论中来！




