> * 原文链接 : [Swift Programming 101: Mastering Dynamic Type in iOS 8](http://www.iphonelife.com/blog/31369/swift-programming-101-mastering-dynamic-type-ios)
* 原文作者 : [Kevin McNeish](http://www.iphonelife.com/blog/kevin-mcneish)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [kmyhy](https://github.com/kmyhy) 

Apple has announced they expect third party apps to support Dynamic Type. However, if you have tried to implement it in your apps, you know there are some unexpected land mines along the way (such as static table view cells and custom cell styles). In this article, you will learn how Dynamic Type works under the hood and how to get it working properly in a variety of scenarios. You will also get some Swift code that makes it easier to implement Dynamic Type in your apps.

Apple声称鼓励第三方App能够支持动态文本。但是，如果你尝试在App中实现这个特性，你会发现其中有很多坑（例如静态cell和定制cell样式）。在本文中，我们将介绍动态文本的机理以及它在各种场景中的应用。我们也会介绍一些Swift代码，这将极大地帮助你在自己的App中实现动态文本。

##What is Dynamic Type?
##什么是动态文本？
In iOS 7, Apple introduced Dynamic Type technology to iOS devices. It allows users to specify their preferred font size (for apps that support Dynamic Type) in the Settings app.

在iOS7中，Apple引入了动态文本的概念。动态文本允许用户通过设置程序修改App的字体大小（只是针对支持动态文本的App）。

This makes it easy for visually impaired users to increase the size of the text, or, on the other end of the spectrum, those with sharp vision to decrease the font size to fit more information on each screen.

对于视力不好的用户，很容易就能将文本字体增大，另一方面，对于视力较好的用户，则可以将字体改小，以便在同一屏中容纳更多的内容。

To change the Dynamic Type setting, in the Settings App, select General > Accessibility > Larger Text, which displays the Accessibility screen (Figure 1). The user can move the slider to the left to decrease the font size or to the right to increase it. To get even larger font sizes, the user can turn on the Larger Accessibility Sizes option at the top of the screen.

要在设置App中修改动态文本设置，选择通用->辅助功能->更大字体，如图1所示。用户通过拖动滑条来改变字体的大小。要使用更大的字体，可以打开屏幕上方的“辅助功能中的更大字体”开关。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig001SettingsDynamicType-300x533.jpg"/>
图 1 - 更大字体设置
The left side of Figure 2 shows the smallest font size in the Contacts app. The right side shows the largest non-accessibility font setting.

图2左边的图显示的是联系人App在最小字体下的显示效果，右边的图是在没有打开“辅助功能中的更大字体"时的最大字体的显示效果。

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig002SmallLargeContactApp-600x506.jpg"/><img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig002SmallLargeContactApp-600x506.jpg"/>
图 2 - 联系人 app 的小字体和大字体
Here are some of the built-in apps that currently support Dynamic Type:

- Messages
- Calendar
- Maps
- Notes
- Health
- Reminders
- Contacts
- Weather

下面是系统内置的支持动态文本的App:

- 信息
- 日历
- 地图
- 备忘录
- 健康
- 事项提醒
- 联系人
- 天气

Since these built-in apps support Dynamic Type, users are beginning to expect the custom apps you create to support it as well. Let's dive in and see what it takes.

正是因为这些App都支持动态文本，用户也会要求第三方的App也支持动态文本。先让我们看看最终的效果。

##Running the Sample App
##运行示例App

Let's begin by checking out an existing project. You can download the sample app from this link.

1. In Xcode, open the iDeliverMobile.xcodeproj file.
2. Before running the app, in the menu, select Xcode > Open Developer Tool > iOS Simulator.
3. In the Simulator's menu, select Hardware > Device > iPhone 5s.
4. Next, launch the Settings app and navigate to General > Accessibility > Larger Text. Drag the slider all the way to the right to 5. select the largest text.
6. Go back to Xcode, and in the Scheme control at the top left of the Xcode screen, select iPhone 5s from the list of devices.
7. Click Xcode's Run button. When the app appears in the Simulator, you can see that the font is still small. Obviously, we need to make a change to support Dynamic Type.
9. Go back to Xcode and click the Stop button.


我们先把项目check out出来。你可以在[这里](http://www.iosappsfornonprogrammers.com/media/blog/iDeliverMobileDynamicType.zip）下载示例项目。

1. 在Xcode中，打开iDeliverMobile.xcodeproj文件。
2. 运行程序之前，打开Xcode->Open->Developer Tool->iOS Simulator菜单。
3. 在模拟器菜单中，选择Hardware->Device->iPhone 5S。
4. 然后，打开设置程序，找到General->Accessibility->Larger Text。将滑块向右拖动至最大。
5. 回到Xcode，点击Scheme，从设备列表中选择iPhone 5S。
6. 点击Run，在模拟器中运行程序，你会看到字体仍然是小字体。显然，我们的App还不支持动态文本。
7. 回到Xcode，退出App。

## Working with Text Styles
##使用文本样式
Currently, all the user interface controls in the sample app have hard-coded font names and sizes. To support Dynamic Type, you need to change these hard-coded fonts to text styles.

当前，示例程序中的所有UI控件的字体名称和大小都是硬编码的。要支持动态文本，我们需要将这些硬编码的内容替换成文本样式。

Text styles are similar to styles in a word processing app. They allow you to specify the relative size and weight of the font used for each text element in your app. Figure 3 contains a list of the font styles to choose from.

文本样式是类似文字处理程序中的”样式“的概念。样式能够让我们以相对大小和字重的方式指定某段文本的字体。图3列出了可选的字体风格。

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/u31369/Fig003TextStylesRuntime.jpg"/>

图 3 - 动态文本中使用的文本样式
Let's give some of these styles a try.

1. In the Project Navigator, select the Main.storyboard file.
2. In the Deliveries scene, select the Feng Wong label, then go to the Attributes Inspector and change the Font to Headline (Figure 4).
Set the Font to Headline
Figure 4 - Set the Font to Headline.
3. Select the address label and set its Font to Subhead.
4. To see the full effect of the Dynamic Type change set  the address label's Autoshrink setting to Fixed Font Size (Figure 5). This causes the address label text to be cut off, but we'll fix that later.
Set the address label
Figure 5 - Set the address label's Font to Subhead and Autoshrink to Fixed Font Size.
5. Click Xcode's Run button. When the app appears in the Simulator, the font size has increased to match the new setting (Figure 6).

让我们先来试一试。

1. 在项目导航窗口中，选择Main.storyboard文件。
2. 在Deliveries场景中，选择Feng Wong标签，打开属性面板，将Font修改为Headline（图4）。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig004HeadlineFont-600x236.jpg"/>
图 4 - 设置Font 为 Headline.
3. 选择address标签，将Font设置为Subhead。
4. 要预览全部动态文本效果，将address标签的AutoShrink设为Fixed Font Size(图5)。这会让address标签的text被截断，但随后我们会解决这个问题。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig005AutoshrinkFixedFontSize-600x307.jpg"/>
图 5 - 设置address 的 Font 为 Subhead ， Autoshrink 为 Fixed Font Size
5. 点击Run，在模拟器中运行程序，你会发现，字体的大小已经发生改变（图6）。

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig006DeliveriesDynamicTypeRuntime-300x155.jpg"/>
图 6 - 动态文本已经起作用了
Also, notice the row height has increased slightly to accommodate the larger font size.

注意，为了适应大文本，行高被稍微增高了一点。

What happens if the user changes the font size in Settings while the app is running? Let's find out.

1. If the iDeliverMobileCD app is not currently running, click the Run button in Xcode.
2. When the app appears in the Simulator, press the Shift+Command+H keys to go to the Home screen.
3. Launch the Settings app and navigate to the General > Accessibility > Larger Text screen. Drag the slider to the far left to decrease the text size.
4. Press Shift+Command+H to go back to the Home screen and launch the iDeliverMobileCD app again. Notice the labels' fonts have change to match the new smaller font (Figure 7).
The labels fonts change dynamically
Figure 7 - The fonts change dynamically.
5. Go back to Xcode and click the Stop button.

现在，让我们来看看，当用户在设置程序中改变字体大小后，又会发生什么情况？

1. 如果 iDeliverMobileCD 未启动，请点击Xcode中的Run按钮。
2. 当App一启动，按 Shift+Command+H ，切到Home屏幕。
3. 打开设置程序，找到 General -> Accessibility -> Larger Text ，将滑块向左拖动，以减少字体大小。
4. 按下 Shift+Command+H ，回到Home屏，重新打开iDeliverMobileCD 。注意标签的字体已经变小了（图7）。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig007BuiltInLabelAdaptsRuntime-300x158.jpg"/>
Figure 7 - The fonts change dynamically.
5. 回到Xcode ，退出程序。

##Dynamic Type and Prototype Cells
##动态文本和模板单元格
The behavior you are seeing with the labels in this table view is unique to table views with:

1. Table view Content set to Dynamic Prototype
2. Cells using one of the built-in styles

你看到的例子实际上相当于我们进行了以下动作：

1. 将Table view 的Content 设为Dynamic Prototype（动态模板）
2. 将Cell的style设为任意一种内置类型

As you can see in Figure 5, the table view in the sample app is using prototype cells.

就如你在图5中所见，示例中的Table View确实使用了模板单元格。

If you select the table view cell in the Deliveries scene and go to the Attributes Inspector, you can see the Style is set to Subtitle (Figure 8).

如果你选择Deliveries场景中的Table View中的单元格，在属性面板中你会看到其style是Subtitle(图8）。

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig008TableViewSubtitleStyle-300x95.jpg"/>

图 8 - 单元格的Style 为 Subtitle.
As you will learn, table views with static and custom cells behave differently.

呆会你会明白，Table View的静态单元格和动态单元格是截然不同的。

##Line Wrapping Label Text
##标签文本的换行
In some of the built-in iOS apps, Apple allows text to be truncated when the user increases the font size. You can see this in the email address of the Contacts app shown in Figure 9.

在一些iOS内置应用中，苹果允许文本在加大字体后被截断。在联系人应用中，你会在email地址中看到这样的例子（图9）。

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig009ContactsTruncated-300x533.jpg"/>
图 9 -  email 地址被截断 

You can truncate text in your own apps, or you can have the text wrap to the next line. Let's see how wrapping the text works.

在你的App中，你可以允许文本被截断，或者换到下一行。现在，让我们看看如何换行。

In the Deliveries scene, select the detail text label then go to the Attributes Inspector and set the number of lines to zero. This causes the address text to wrap to the next line (Figure 10).

在Deliveries场景中，选择detail text标签，打开属性面板，将number of lines设置为0。这会导致email地址换行（图10）。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig010LabelsExceedRowHeight-300x158.jpg"/>
图 10 - 标签文字超出了行高
Unfortunately, iOS is no longer able to properly determine the height of the row. This brings us to a bigger discussion of dynamic row heights.

然而，iOS却不能正确地计算行高。接下来我们就来讨论动态单元格的行高。

##Dynamic Row Heights
##动态行高
When implementing Dynamic Type in a table view, its row height must be dynamic to accommodate changes in font size. Apple provides three strategies to achieve this:

1. Set the table view's rowHeight property.
2. Implement the tableView:heightForRowAtIndexPath: delegate method.
3. Self-sizing cells

当在Table View中使用动态文本时，表格的行高必须也能够自动适应字体大小的变化。苹果提供了3种解决办法：

1. 修改表格的rowHeight属性。
2. 实现tableView:heightForRowAtIndexPath: 协议方法。
3. 自适应大小的单元格。

###Using the rowHeight Property
###使用rowHeight属性

Even though your table view row heights need to be dynamic, you can still use the classic table view rowHeight property. Whenever the font size changes (you will learn how to be notified of this later in this article), you can recalculate a new height and store it in the table view's rowHeight property.

尽管你的表格的行高应该是动态计算的，但你仍然可以像过去一样使用rowHeight属性。每当字体大小改变（后面我们会讲到如何获得相应的通知），我们都需要重新计算新的行高，并设置表格的rowHeight属性。

The advantage of using the rowHeight property is speed. It provides the best scrolling performance because no calculations need to be performed on the fly while the user is scrolling.

使用rowHeight属性的优点是速度。它提供了最优的滚动性能，因为当用户滚动表格时，不需要进行任何计算。

The downside of this approach is that you have to perform your own calculations to determine the proper height for rows. In addition, all rows must be the same height.

缺点是我们必须手动计算正确的行高。另外，所有的单元格都必须使用相同的行高。

In iOS 7, the default height of a table view was 44 points In iOS 8, the default height of a table view is UITableViewAutomaticDimension (a constant that equates to -1). If you want to use the rowHeight property, you need to set its initial value in the Attributes Inspector or in your view controller's viewDidLoad method.

在iOS7中，默认行高为44，在iOS8中，默认行高是UITableViewAutomaticDimenssion(一个常量，等于-1)。如果你要使用rowHeight属性，你需要在属性面板中或者viewDidLoad方法中设置它的初始值。

###Implementing heightForRowAtIndexPath
###实现heightForRowAtIndexPath方法
You can use the tableView:heightForRowAtIndexPath: view controller method to individually size each row based on your own calculations.

我们可以用tableView:heightForRowAtIndexPath: 方法单独计算每一行的行高。

There is one big disadvantage to this approach. The height of all rows is requested up front, even before the rows have been created. If you have tens of thousands of rows, this can be a real performance drag.

这种方法没有什么明显的优点。每一行的行高都会事先被询问，不管该行是不是已经被创建。如果你的表格有上千行，这会导致性能上的延迟。

###Self-Sizing Table View Cells
###自适应大小单元格
When using self-sizing table view cells, rather than setting the rowHeight property, you either set the table view's estimatedRowHeight property or implement the tableView:estimatedHeightForRowAtIndexPath: delegate method.

如果使用自适应大小单元格，而不是使用rowHeight属性，则我们既不用设置estimatedRowHeight属性，也不用实现tableView:estimatedHeightForRowAtIndexPath:协议方法。

Here are the basic steps performed by the runtime to create a self-sizing cell:
创建自适应大小单元格的步骤大致如下：

1. Before a row comes on screen, it's sized using the estimatedRowHeight property or the associated delegate method.
在绘制每个单元格之前，它会使用estimatedRowHeight属性或者调用相关的委托方法。

2. When the row is scrolled onto the screen, the cell is created.
当表格滚动，该行即将显示到屏幕时，单元格被创建。

3. The cell is asked how bit it should be.
此时单元格会被询问其大小。
4. If the height is different from the estimated height, it's used to adjust he content size of the table view cell.
如果这个高度和estimatedHeight不同，则使用该高度进行调整。
5. The cell is displayed on screen.
显示单元格。

In step 3, there are two ways a cell can communicate the height it needs to be:

在第3步，又有两种计算单元格高度的方式：

1. Auto Layout
自动布局
2. Manual sizing code
手动计算大小


The table view calls systemLayoutSizeFittingSize on your cell. This method knows if you have implemented constraints on your cell, and if so, the Auto Layout engine specifies the cell size.

Table View会调用每个单元格的systemLayoutSizeFittingSize方法。该方法返回单元格是否已经实现了布局约束，如果实现，则自动布局引擎负责指定单元格的大小。

If you haven't implemented Auto Layout constraints, the table view calls the sizeThatFits method on your cell. You can return a cell height from this method based on your own calculations—the cell width is already calculated for you.

如果没有实现自己的布局约束，TableView调用单元格的sizeThatFits方法。在这个方法中我们可以自行计算单元格高度并返回——而单元格的宽度是已经计算好的。

###Using Auto Layout with Dynamic Type
###在动态文本中使用自动布局
Let's use Auto Layout in our sample project to see how it works with Dynamic Type. We first need to make sure Auto Layout is turned on for the project's storyboard.

先让我们在示例项目中试下自动布局，看如何在动态文本中使用。首先需要确定故事板是否支持自动布局。

1. Click on the white background of the storyboard to select it.
点击故事板的白色背景
2. Next, go to the File Inspector by selecting the button on the far left in the Inspector toolbar. Under the Interface Builder Document section, make sure Use Auto Layout is selected (Figure 11).
打开文件面板。在Interface Builder Document栏下，确保Use Auto Layout 已选中（图11）。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig011SelectUseAutoLayout-300x159.jpg"/>
图 11 -选中 Use Auto Layout
3. Now we need to change the Deliveries scene's table view cell to use a custom style. To do this, click on the table view cell to select it, go to the Attributes Inspector and change the Style to Custom. This removes both labels from the cell.
将Deliveries场景的表格单元格的风格修改为自定义。选中表格单元格，打开属性面板，将Sytle设置为Custom。这会将单元格的两个标签删除。

4. Let's change the design-time height of the cell to make it easier to lay out in Interface Builder. Click in the gray area below the table view to select it. Next, go to the Size Inspector by clicking the second button from the right in the Inspector toolbar. Set the Row Height to 60.
在IB中改变单元格的高度是非常简单的。点击表格的灰色区域，在Size面板，将 Row Height设置为 60。

5. Next, drag a label from the Object Library and position it in the cell so you can see the horizontal and vertical guide lines shown in Figure 12.
从Object Library中拖一个标签到单元格中，你可以看到它的水平和垂直导线，如图12所示。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig011SelectUseAutoLayout-300x159.jpg"/>
图 12 -加一个标签到单元格中
6. Grab the resizing handle on the right side of the label and drag it to the right side of the cell until the vertical guidelines shown in Figure 13 appears.
拖住标签右边的resizing手柄，将它向右拖，直到垂直导线到达图13中所示的位置。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig013ResizeMainLabel-300x207.jpg"/>
图 13 - 设置标签的宽度
7. With the label still selected, in the Attributes Inspector, set Font to Headline and Tag to 1. 
保持标签选中状态，打开属性面板，设置Font为Headline，Tag为1。
8. Drag a second label from the Object Library and position it below the first label so you see the guide lines shown in Figure 14.
再拖一个标签放到第一个标签下方，使其导线如图14所示。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig014AddSubLabelToCell-300x206.jpg"/>
图 14 - 添加第2个标签
9. Grab the resizing handle on the right side of the label and drag it to the right side of the cell until the vertical guidelines appear, just as with the first label.
拖住标签右边的resizing手柄，向右拖，直到其垂直导线和第一个标签的垂直导线对齐。
10. With the label still selected, in the Attributes Inspector set Font to Subhead, Lines to 0 and Tag to 2. Setting Lines to 0 causes the label to wrap to the next line when displaying long text strings.
保持标签选中，打开属性面板，设置Font为Subhead，Lines为0，Tag为2。将Lines设置为0，这样当标签显示长文本时会自动换行显示。
11. Click on the Headline label at the top of the cell to select it in the design surface.
选中单元格上面的Headline标签。
12. At the bottom right of the Interface Builder editor, click the Pin button (Figure 15). In the popup dialog, uncheck the Constrain to margins check box, and then click on each of the four red I-bars at the top of the dialog. This pins the label's top, bottom, left, and right sides to their nearest neighbors. Next, select the Height check box, and then click the Add 5 Constraints button at the bottom of the dialog.
点击IB编辑器右下角Pin按钮（图15），在弹出的窗口中，反选Constrain to margin勾选框，然后点击窗口上半部分的4个边距，使其显示为4条红线。这使得标签的上、下、左、右4条边都分别向最近的控件对齐。然后将Height勾选，再点击Add 5 Constraints按钮。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig015MainLabelAddConstraints-500x481.jpg"/>
图 15 - 为上端的标签添加布局约束
13. Select the Subhead label at the bottom of the cell and click the Pin button again to display the Constraints popup dialog.
选择单元格下方的Subhead标签，点击Pin按钮。
14. You need to select the same constranst as you did with the Headline label: Uncheck the Constraints to margin check box, select all four I-bars at the top of the dialog, select the Height check box, and then click the Add 5 constraints button at the bottom of the dialog.
和Headline标签所做的一模一样：反选Constraints to margin选项，选择4边对齐，勾选Heightg，然后点击Add 5 constraints按钮。
15. With the lower Subhead label still selected, go to the Size Inspector by clicking the second button from the right in the Inspector toolbar.
继续选中Subhead标签，打开Size面板。
Click the Height constraint's Edit button (Figure 16) and change the operator to "greater than or equal to". This allows the height of the button to grow to accommodate multiple lines of text.
点击Height约束右边的Edit按钮（图16），将operator改为”great than or equal to“。这将使标签的高度自动和文本的行数匹配。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig016EditHeightConstraint-300x149.jpg"/>
图 16 - 修改Height约束的operator
16. Now you need to do the same for the Heading label. Click the Heading label in the design surface to select it. In the Size Inspector, click the Height constraint's Edit button and change the operator to "greater than or equal to".
然后对Heading标签是重复上面的操作。点击Heading标签，在Size面板，点击Height约束的Edit按钮，将operator修改为”greater than or equal to“。
17. Now you need to change the code in the table view controller to work with the new custom labels.
然后修改Table View Controller的代码。
Select DeliveriesViewController.swift in the Project Navigator. In the tableView:cellForRowAtIndexPath: method, change the code that configures the cell to the following:
选择DeliveriesViewController.swift文件。在tableView:cellForRowAtIndexPath:方法中，将代码修改为：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code1_10-400x315.jpg"/>
This code uses the viewWithTag method to get a reference to the new labels using the Tag numbers you set for each. The last line of highlighted code is necessary to get around a "feature" that sometimes prevents labels from wrapping to the next line properly. Setting a label's prefferedMaxLayoutWidth property to the label's current width does the trick.
这段代码用viewWithTag方法获得指定Tag值的标签。在代码中高亮的部分，最后一行是不能少的，因为标签某些时候会无法正确换行，因此需要将prefferedMaxLayoutWidth属性设置为当前的宽度以解决这个问题。
18. There is one more change we need to make. Scroll to the top of the DeliveriesViewController.swift file and add the following code to the bottom of the viewDidLoad method:
还有几个地方需要改。拉到DeliveriesViewController.swift文件顶部，在viewDidLoad方法中加入代码：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code2_9-400x164.jpg"/>
Setting the table view's estimatedRowHeight property specifies the approximate height of the cell. Setting the rowHeight property of the table view to UITableViewAutomaticDimension tells iOS that you want it to automatically resize the cells for you. Let's give it a try!
将表格的estimatedRowHeight属性设为单元格的正确高度。将表格的rowHeight属性设置为UITableViewAutomaticDimenssion，告诉iOS我们需要它自动调整单元格大小。让我们来看看运行效果！
19. Go to the Simulator, launch the Settings app and set the text size to the largest setting. Next, click Xcode's Run button. When the app appears in the Simulator, the address wraps to the next line (Figure 17)!
回到模拟器，点开设置程序，将文本大小设为最大。点击Xcode的Run按钮，当程序运行，可以看到email地址已经换行了（图17）！
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig017LabelTextWrapRuntime-300x194.jpg"/>
图 17 - 邮箱地址换行

##Type Changes with Custom Cells
##字体在自定义单元格上的改变
Currently, when the Deliveries scene first loads, the labels in the table view take on the font size the user has specified in the Settings app. Previously, when the cell was set to the built-in Subtitle style, if the user changed the font size while the app was running, the font size of the labels changed dynamically. Unfortunately, this is not the case when using a custom cell style. Let's try it.

现在，当Deliveries场景第一次加载时，表格中的标签采用用户在设置程序中已经设好的字体大小显示。显然，当单元格采用内置的Subtitle样式时，如果用户改变了字体大小，则标签上的字体大小也会随之改变。但不幸的是，如果使用的是自定义单元格，这个机制就无效了。我们先来测试一下。

1. Click Xcode's Run button to the run the app in the Simulator.
点击Run按钮。

2. When the app appears in the Simulator, press the Shift+Command+H keys to go to the Simulator's Home screen.
当App启动后，按Shift+Command+H键退回到Home屏。

3. Launch the Settings app and navigate to the General > Accessibility > Larger Text screen. Drag the slider to the far left to decrease the text size.
点开设置程序，进入General->Accessibility->Larger Text界面，将滑条向左拖动，将字体大小改小。

4. Press Shift+Command+H to go back to the Home screen and launch the iDeliverMobileCD app again. As you can see, the label's font size hasn't changed. Go back to Xcode and click the Stop button and we'll address this issue.
按下Shift+Command+H键，返回Home屏幕，切换到iDeliverMobileCD程序。我们发现，标签文本的字体没有发生丝毫改变。关闭App，我们来解决这个问题。

To get labels (or any other text-based control) in a custom cell to dynamically change font size in response to changing the font size in the Settings app, you must:

要让自定义单元格中的标签（或其他任何文本控件）能够根据设置程序中的字体大小来改变其文本字体，我们必须：

1. Register with the NSNotificationCenter for the UIContentSizeCategoryDidChangeNotification in your view controller's init or viewDidLoad method.
在viewDidLoad方法中向通知中心注册UIContentSizeCategoryDidChangeNotification通知。

2. In the code that responds to the font change notification, store the label's style setting back into the font property. For example:
在代码中响应字体改变通知，将标签的样式重新设置正确。例如：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code3_7-300x64.jpg"/>

3. Unregister for notifications in the view controller's deinit method.
在ViewController的deinit方法中注销通知。

Let's try this with the Deliveries scene.
让我们以Deliveries为例进行演示。

1. In the DeliveriesViewController.swift file, add the following code to the bottom of the viewDidLoad method:
打开DeliveriesViewController.swift文件，在viewDidLoad方法最后加入：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code4_12-400x103.jpg"/>
This code asks the Notification Center to call the handleDynamicTypeChange method when the user changes the Dynamic Type setting.
上述代码让通知中心在用户改变了动态文本设置之后调用handleDynamicTypeChange方法。

2. Add the following handler method below the viewDidLoad method:
在viewDidLoad方法下面新增方法：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code5_8-400x80.jpg"/>
This handler method reloads the table view's data.
在这个方法中重新加载Table View。

3. Now add the following code to the bottom of the tableView:cellForRowAtIndexPath: method:
现在在tableView:cellForRowAtIndexPath:方法最后加入代码：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code6_8-300x158.jpg"/>
This code resets the label's font style.

 这段代码重新设置标签的字体风格。

4. Finally, add the following deinit method below the viewDidLoad method:
最后，在viewDidLoad方法下面增加deinit方法：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code7_9-375x77.jpg"/>

5. Let's try this code to see how it works at run time. Click Xcode's Run button. When the app appears in the Simulator, you should see the small text from when you previously changed font size. Press the Shift+Command+H keys to go to the Simulator's Home screen.
让我们测试一下上述代码。点击Run按钮，当App启动后，我们将看到标签文本变成了先前改变的小字体。按下Shift+Command+H键回到Home屏。

6. Launch the Settings app and navigate to the General > Accessibility > Larger Text screen. Drag the slider to the far right to increase the text size.
打开设置程序，进入General->Accessibility->Larger Text界面，将滑块向右拖到，调大字体。

7. Press Shift+Command+H keys to go to the Simulator's Home screen and launch the iDeliverMobileCD app again. As you can see, the label's font size has increased without having to restart the app!
按下Shift+Command+H键，回到Home屏，切到iDeliverMobileCD程序。我们将看到，标签文本已经在没有重启App的前提下变大了！

8. Go back to Xcode and click the Stop button.
回到Xcode，终止程序。
##What's Bad About This Model
##这种方法的弊端
Here are a few things that make this model undesirable:
这种方法有以下几个弊端：

1. You have to set the font for all controls twice. Once in the Attributes Inspector and a second time in code.
我们必须每个控件都要设置两次字体。一次是在属性面板，一次是在代码中。

2. You have to create outlets for all text-based controls, even if you don't need them for any other purpose.
我们必须为每个文本控件都创建一个IBOutlet，哪怕你根本不需要使用这些IBOutlet。

3. You have to add this same code to all view controllers in your app.
我们必须在每个View Controller中增加同样的代码。

When you come across situations where you must add redundant code in multiple places in your app, it's time to create a universal solution you can reuse in all your projects.

每当我们需要在不同的地方重复加入冗余的代码时，我们就应该考虑创建一种通用的解决方法以在所有项目中重用代码。

I have create a set of class you can add to any project that makes it easy to implement Dynamic Type handing in your app. Before giving it a try, let's get rid of the code you added in the previous section.

我已经创建了几个类，你可以在自己的项目中更容易地实现动态文本。在测试运行之前，先移除我们在前面添加的代码。

1. Delete the following code from the viewDidLoad method:
从viewDidLoad方法中移除下列代码：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code4_12-400x103.jpg"/>

2. Delete the following handler method below the viewDidLoad method:
从viewDidLoad方法下移除该处理方法：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code5_8-400x80.jpg"/>

3. Delete the following code from the bottom of the tableView:cellForRowAtIndexPath: method:
从tableView:cellForRowAtIndexPath:方法中移除下列代码：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code6_8-325x158.jpg"/>

4. Remove the deinit method located below the viewDidLoad method:
删除位于viewDidLoad下面的deinit方法：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code7_9-400x82.jpg"/>

##A Better Solution
##更好的解决方案
Now let's try a better solution.
现在来看看更好的解决方案。

1. Right-click the Main.storyboard file in the Project Navigator and select Add Files to iDeliverMobileCD... from the shortcut menu.

 在项目导航窗口，右键点击Main.storyboard，选择Add Files to iDeliverMobileCD...。
 
2. In the Add Files dialog, uncheck Copy items if needed.

 在添加文件对话框，反选Copy items if needed。

3. In the project's folder, select the mmDynamicTypeExtensions.swift file and click Add. We'll take a closer look at this code later, but let's first see how the code works at design time and run time.

 在项目文件夹，选择mmDynamicTypeExtensions.swift文件，然后点击Add。等一会我们在查看代码，现在先看一下如何在设计时和运行时使用这些代码。

4. Select the Main.storyboard file in the Project Navigator. In the Deliveries scene, select the Heading label at the top of the cell.

 在项目导航窗口，选中Main.storyboard。在Deliveries场景，选择单元格中位于上方的Heading Label。

5. Go to the Attributes Inspector. Notice there is a new Type Observer attribute (Figure 18).

 打开属性面板，注意，显示了一个新的Type Observer属性（图18）。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig018TypeObserverAttribute-298x72.jpg"/>
图 18 - Type Observer 属性
The code you just included in this project added this Type Observer property to the label.

 刚才添加到项目中的代码为标签添加了一个Type Observer属性。

6. Change the Type Observer setting to On.

 将Type Observer属性设置为On。

7. Select the Subhead label at the bottom of the cell. In the Attributes Inspector, set the Type Observer attribute to On.

 选择Subhead标签，在属性面板，将Type Observer属性设置为On。

8. That's it! Let's give it a try. Click Xcode's Run button. When the app appears in the Simulator, you should see the large text from when you previously changed the font size. Press the Shift+Command+H keys to go to the Simulator's Home screen.

 所有工作完成，让我们来测试一下。点Run按钮，当程序启动后，我们将看到显示了先前我们设置的大字体文本。按下Shift+Command+H键回到Home屏。

9. Launch the Settings app and navigate to the General > Accessibility > Larger Text screen. Drag the slider to the far left to decrease the text size.

打开设置程序，进入General->Accessibility->Larger Text界面。将滑块向左拖动以减小字体。

10. Press  Shift+Command+H to go to the Home screen and run the iDeliverMobileCD app again. As you can see, the label's font size has changed!

 按下Shift+Command+H返回Home屏，切回iDeliverMobileCD程序。你会看到，标签字体大小已然改变！

11. Go back to Xcode and click the Stop button.

返回Xcode，终止程序。

##Dynamic Type Handling
##动态文本的处理
Let's take a closer look at the code that makes this work.

让我们来看看代码。

1. Select the mmDynamicTypeExtensions.swift file in the Project Navigator.

 在项目导航窗口，打开mmDynamicTypeExtension.swift文件。

2. At the top of the file is a protocol that declares a single typeObserver property of type Bool. This is the property you set to On for the table view labels:

 在文件顶部，是一个协议，该协议仅包含了一个叫做typeObserver的Bool属性。也就是你在标签中设置为On的属性。
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code8_9-325x68.jpg"/>

3. Just below the protocol declaration is an extension of the UILabel class:
 在协议声明之后，又定义了一个UILabel的扩展：
<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code9_7-400x240.jpg"/>
This extension adopts the DynamicTypeChangeHandler protocol and implements the typeObserver property. The @IBInspectable attribute is what makes the property appear in the Attributes Inspector. The property setter makes a call to a Dynamic Type Manager's registerControl method.

 这个扩展声明了对DynamicTypeChangeHandler协议的实现并实现了typeObserver属性。@IBInspectable属性表明这个属性可以显示在属性面板中。这个属性的setter方法调用了动态文本管理器的registerControler方法。

4. Scroll a little further down in the code file and you can see the DynamicTypeManager is declares as a Singleton:

 向下滚动代码，我们可以看到DynamicTypeManager对象被实现为一个单例对象：
 
 <img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code10_7-400x163.jpg"/>

 The Singleton design pattern restricts the number of instances of a class to one. When an instance is requested, one is created if it doesn't already exist. If an instance exists, it returns a reference to that object.

 单例模式使得类的实例始终只有一个。当创建一个类的实例时，如果类还未被实例化，则创建新的实例。如果类已经被实例化，则返回现有的实例对象。

 Figure 19 contains a sequence diagram that depicts the architecture I have created for handling Dynamic Type changes.

 图19是一张序列图，显示了动态文本改变的处理逻辑。

 <img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig18.019DynamicTypeSequenceDiagram-695x539.jpg"/>
图 19 - 动态文本处理的序列图

Here are the key steps:
这是几个关键步骤：

1. User interface controls register themselves with the Dynamic Type Manager when their typeObserver property is set to true (On in the Attribute Inspector), passing a path to their font property.

 当typeObserver属性为true时（通过属性面板中），UI控件向动态文本管理器进行注册，将一个 keypath传递给控件的字体属性。

2. When the first control registers itself, an instance of the Dynamic Type Manager is created and registers itself with the NSNotificationCenter for Dynamic Type changes.

 当第一个控件进行注册时，Dynamic Type Manager实例被创建，并开始向通知中心注册动态文本改变通知。

3. A reference to the control and its font style is stored in an NSMapTable. A map table is a special kind of dictionary that holds weak references to objects, so entries are removed automatically when either the key or value is deallocated. This is a perfect fit for this scenario where we don't want to hold strong references to user interface controls. When the UI controls are released (for example, when the user navigates to a different scene and the view controller is deallocated), the control reference are automatically removed from the NSMapTable (Thanks to the folks at Big Nerd Ranch for this tip!).

创建一个对该控件的引用并将它的字体样式保存到一个NSMapTable中。一个Map Table是字典的一种，保存的是对象的弱引用，因此当key或value被解构时保存的对象自动被移除。这对我们来说再恰当不过了：我们并不想保持对UI控件的强引用。当UI控件释放后（例如，用户导航到另一个View Controller，当前View Contoller被解构），该控件在NSMapTable（感谢[Big Nerd Ranch](https://www.bignerdranch.com/)分享了这个技巧）中的引用将被自动移除。

4. When the user changes the font size in the Settings app, NSNotificationCenter alerts the Dynamic Type Manager.

当用户在设置程序中改变字体大小，通知中心会通知DynamicTypeManager对象。

5. The Dynamic Type Manager iterates through the list of user interface controls in the map table. For each control, it sets the font style and calls the control's sizeToFit method.

DynamicTypeManager对象遍历Map Table中的UI控件，对每个控件，都设置它们的字体样式，并调用sizeToFit方法。

What is there to like about this architecture?

上图这种方式有什么好处？

- It uses extensions rather than subclasses. This allows you to use the out-of-the-box UIKit controls.

 它使用的是扩展而不是继承。因此我们可以使用“盒子之外的”UIKit组件。

- When you add the mmDynamicTypeExtensions.swift code file to your project, it "just works".

 你只需要将mmDynamicTypeExtension.swift添加到项目中就可以使用它。

- There is no need to create outlets for UI controls. You simply set a property in the Attributes Inspector.

 不再需要为UI控件创建IBOutlet。只需要简单地在属性面板中设置一下就好。

- This architecture is loosely coupled. The UI controls provide information about themselves to the Dynamic Type Manager. This means you can register your own custom controls (or new controls that Apple releases in the future) without changing the Dynamic Type Manager.

 这种方式使用的是松散耦合。UI控件将自己的属性提供给动态文本管理器。这意味着你注册自定义控件（或者苹果未来发布的新控件），而不需要修改动态文本管理器。

- Since you don't need to use this feature for prototype table view cells that are set to one of the default styles, this "opt in" model lets you choose which controls you want to register with the Dynamic Type Manager.

 不需要在设为默认样式的模板单元格上使用这个特性，你只需要选择将哪个控件注册到动态文本管理器就行了。

####Dynamic Type and Static Text
####动态文本和静态文本
Let's check out how Dynamic Type works with static table view cells.

让我们来看一下如何在静态单元格中使用动态文本。

 1. In the iDeliverMobileDynamicType project, select the Main.storyboard file and scroll over to the Deliveries scene (Figure 20).

 在iDeliverMobileDynamicType项目中，选中Main.storyboard文件，找到Deliveries场景（图20）。

 <img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig18.020ShipmentScene-300x450.jpg"/>

 图 20 -  Shipment 场景
 2. The table view in this cene contains dynamic protototype cells, just as the Delivieries scene. The main difference is the Shipment scene contains both dynamic and static text. The blue text (Phone, Text, and ID) as well as the Status text is static. This means the text doesn't change as you examine different shipments. The rest of the text is dynamic, changing for each shipment.

 在这个场景的Table View中，如同Deliveries场景一样包含了动态模板单元格。不同的是Shipment场景中既包含了动态文本也包含了静态文本。蓝色的文本（Phone、Text、和ID）和Status是静态的。也就是说这些文本在不同的发货单中是固定不变的。其他文本则是动态的，每个发货单都不一样。
 
 3. To get the labels on this scene to adapt to Dynamic Type, select each label in the design surface, then go to the Attributes Inspector and change the Font to one of the iOS text styles. Here are some recommendations:

 要让这些标签也使用动态文本，选择每个标签，然后在属性面板中将Font设为任意一种iOS字体风格，比如：

 Name - Headline
 Address Line 1 - Body
 Address Line 2 - Subhead
 Phone labels - Body
 Text labels - Body
 Status labels - Body
 ID labels - Body
 iPod Touch label - Body
 
 4. In the ShipmentViewController.swift file, add the following code to the bottom of the viewDidLoad method:

 在ShipmentViewController.swift文件中，在viewDidLoad方法最后一行加入代码：

 <img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code11_6-400x219.jpg"/>

 Remember, this code tells the table view to use self-sizing cells.

 记住，这些代码用于告诉Table View使用自适应大小单元格。
 
 5. Let's see how this works at run time. Click Xcode's Run button, and when the app appears in the Simulator, select the shipment in the Deliveries scene to navigate to the Shipment scene. You should see the small type that was last specified in the Settings app.

 现在让我们看看效果。点击Run按钮，当程序启动，在Deliveries窗口选择shipment进入Shipment窗口。我们将看到显示的是我们先前在设置程序中设置的小字体。

 6. Now let's see if the app responds to Dynamic Type change while the app is running. Go to the Settings app and select the largest font size. Afterward go back to the iDeliverMobileDynamicType app.

 现在让我们看看在程序运行的情况下App如何处理动态文本的改变。切换到设置程序，选择最大字体。再回到iDeliverMobileDynamicTypeApp。

As shown in Figure 21, all the static text is missing! This is an iOS bug, and unfortunately, it still isn't fixed in the latest version of Xcode 6.2. I'm hoping Apple will fix it, but for now you can get around the issue without resorting to creating custom cells. You just need to add code to the tableView:cellForRowAtIndexPath: method that resets the static text.

如图21所示，所有的静态文本都不见了！这是iOS本身的一个Bug，不幸的是，在Xcode6.2中仍然未得到解决。我希望苹果以后能修正这个Bug，但目前我们不需要自定义单元格就可以解决这个问题。我们只需要在tableView:cellForRowAtIndexPath: 方法中增加一点代码去重置静态文本：

<img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig18.021MissingStaticText-300x534.jpg"/>

图 21 -静态文本不见了!
 
One other problem is that the name text in the first cell is no longer centered. We can also work around this problem by adding code to the same method.

还有一个问题是，第一个单元格不再居中对齐。这个问题也是在同一个方法中增加代码来解决。

In the tableView:cellForRowAtIndexPath: method of the  file, add the following highlighted code:

1. 在文件的tableView:cellForRowAtIndexPath: 方法中，添加高亮部分的代码：

 <img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Code12_6-400x383.jpg"/>

2. Click Xcode's Run button and when the app appears in the Simulator, navigate to the Shipment scene.

 点击Run按钮，当程序启动，进入Shipment页面。

3. Go to the Settings app and set the font size to the smallest setting. Go back to the iDeliverMobileDynamicType app and you should see the static text is back (Figure 22)! This works because the table view's reloadData method is automatically called when a Dynamic Type change is made.

 切到设置程序将字体设置为最小。回到iDeliverMobileDynamicType，我们将发现静态文本又回来了（图22）！这是因为当动态文本字体发生改变时，Table View的reloadData方法自动会调用。

 <img src="http://static.iphonelife.com/sites/iphonelife.com/files/resize/u31369/Fig18.022StaticTextRestored-300x533.jpg"/>
 
 图 22 - 静态文本又回来了

##结语
Last year, my company had a booth at the MacWorld trade show, promoting my iOS app development book series. A visually impaired attendee approached the booth and asked if I was teaching new developers how to create apps that can be used by the visually impaired. Starting with this article, I can finally say yes, and I hope the information presented here helps you to do the same!

去年，我们公司在 MacWorld 展会上有一个展台，展示我的iOS App开发图书系列。一个有弱视的读者来展位上问我，能不能教一下开发者们如何创建适用于弱视患者的App。这导致了本文的产生，我终于可以说Yes了，我希望本文能够让你在面对这个问题的时候能够同样说Yes。