#iOS编程101:如何生成圆形和圆角的图像
---

>* 原文链接：[iOS programming 101:How To Create Circular Profile Picture and Rounded Corner Image](http://www.appcoda.com/ios-programming-circular-image-calayer/)
* 原文作者：[Simon ng](https://github.com/simonng)
* 译文出自：[开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [7heaven](https://github.com/7heaven)
* 校对者：[Mr.Simple](https://github.com/bboyfeiyu)
* 状态：完成


iOS7带来的一个比较大的变化是圆形图像的使用比方形图像更频繁。在系统默认的应用，比如通讯录和电话中都能找到圆形图像。在这个文章中，我们一起来研究一下如何使用CALayer类来创建圆形或者圆角的图像。

可能你之前并没有听过CALayer类。但是在你构建你的应用的时候肯定用到了它。UIKit里面的每一种view(例如：UIView, UIImageView)都是建立在CALayer的实例基础上的(layer对象)。layer对象被设计用来管理view的后备储存和处理view相关的动画。layer对象提供一系列的标签来控制view的可视化内容：

* 背景颜色
* 边缘以及边缘宽度
* 阴影颜色，阴影宽度等
* 透明度
* 圆角半径


下面我们就使用圆角半径这个标签来挥之圆角和圆形的图像。

![art1](http://www.appcoda.com/wp-content/uploads/2014/04/circular-image-featured.jpg)


要明白CALayer是如何工作的，最好的办法就是实践。我们来创建一个简单的包含圆形图片的个人资料view.

##demo项目

首先，我们从下载[这个项目](https://www.dropbox.com/s/l7tfno7d89ip9em/ProfileDemoTemplate.zip)开始。项目中已经预搭建了profile view 但是你编译运行app的时候会发现图像还是方形的。整个demo项目非常简单。唯一需要注意的是ProfileViewController是和Storyboard中的view相关联的。

并且profile图像(UIImageView)已经链接到ProfileViewController.h里的profileImageView属性上。

![art2](http://www.appcoda.com/wp-content/uploads/2014/04/square-image-profile.jpg)

##创建圆形图像

接下来我们来研究一下如何通过边缘半径来把普通图像转换成圆形图像。

打开ProfileViewController.m，在viewDidLoad:方法中添加以下代码

```
self.profileImageView.layer.cornerRadius = self.profileImageView.frame.size.width / 2;
self.profileImageView.clipsToBounds = YES;
```

每个view都绑定了一个layer属性。上面的代码中第一行便是设置这个layer对象(即CALayer类的实例)的边缘半径属性。要把方形图像变成圆形图像，边缘半径必须设成图像宽度的一半。举个例子，如果方形图像的宽度是100像素，那半径就是50像素。第二步则是要把clipsToBounds属性设为YES来使layer正常工作。

现在你再重新编译并运行app的话，你会发现图像已经变成圆形了。

![art3](http://www.appcoda.com/wp-content/uploads/2014/04/circular-image-profile.jpg)

简单吧？只需要两行代码，方形图像就变成圆形图像了。完全不需要ps。

##添加边缘

接下来，我们给图像添加个边缘，让它看起来更完美。和上面一样，仅需两行代码便能实现这个效果。在viewDidLoad:方法的设置边缘半径的代码下添加以下两行代码:

```
self.profileImageView.layer.borderWidth = 3.0f;
self.profileImageView.layer.borderColor = [UIColor whiteColor].CGColor;
```

上面的代码非常简单明了。我们只是设置了边缘的宽度还有边缘颜色。现在编译运行app后你会看到有一圈白色包围在图像边缘上。

##创建圆角图像

相同的方法也可以应用到圆角图像上。技巧是把边缘半径改成其他值。比如10，

```
self.profileImageView.layer.cornerRadius = 10.0f;
```

现在图像就会变成圆角矩形图像了。

![art4](http://www.appcoda.com/wp-content/uploads/2014/04/square-corner-image-profile.jpg)

完整的项目可以从[这边](https://www.dropbox.com/s/8dova2z23z42ppp/ProfileDemo.zip)下载。
