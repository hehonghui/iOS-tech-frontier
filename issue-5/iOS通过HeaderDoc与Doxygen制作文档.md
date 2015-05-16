`iOS通过HeaderDoc与Doxygen制作文档`
---

>
* 原文链接 : [Documenting Your Objective-C and Swift Code in Xcode with HeaderDoc and Doxygen](http://www.appcoda.com/documenting-source-code-in-xcode/)
* 原文作者: [gabriel theodoropoulos](http://www.appcoda.com/author/gabrielth/)
* 译文出自: [开发技术前线http://www.devtf.cn/](http://www.devtf.cn/)
* 译者 : [Lollypo](https://github.com/Lollypo) 
* 校对者: []()  
* 状态 :  未完成

During the development of an application there are various steps involved in the whole process. Some of them are the definition of its specifications, the creation of graphics, the implementation, and the testing phase following the implementation. Writing the code maybe consists of the most important part, as this brings the application to life, but further than that, equally important is the proper documentation of the code. No matter how well-written the code is, if there’s lack of a good documentation accompanying it, future troubles it’s possible to arise. Unfortunately, many developers overlook or ignore the importance of the code documentation, and that’s really bad, as good software is not just good code. It’s more than that.

在应用程序的整个开发过程中会涉及到各种步骤.其中有定义代码规范、创建图形界面、代码的实现以及代码实现后的测试阶段.编写代码也许是其中最重要的一个环节,因为它赋予了应用程序生命.但更进一步的说,合理的代码文档也是同等重要的.无论代码写的多么好,假如缺乏对应的文档,在将来很可能会出现麻烦.不幸的是,许许多多的开发人员都忽视了代码文档的重要性,这是极其糟糕的,优秀的的软件不仅仅只是优秀的代码.事情没那么简单.

When talking about documentation, apparently I don’t mean just to add a few lines of comments somewhere in the implementation files. It’s definitely more than that. But first, why is it such a big deal to document the code? Well, there are two cases: Whether you’re working on your own, or you are a part of a team. Let’s see in short each one.

当谈论到文档的时候,显然我的意思不是在实现代码中加几行注释.肯定没这么简单.不过首先想一下为什么代码文档如此重要?昂,有两种情况:一是你为自己工作或者是作为团队中的一员.让我们简单看看这两种情况.

![xcode-macbook-featured](http://www.appcoda.com/wp-content/uploads/2015/02/xcode-macbook-featured.jpg)

If you are the only developer of the under-development application, then it’s reasonable to believe that writing code documentation costs in time, so skipping doing that will bring you right into your target much sooner. Additionally, it’s easy to convince yourself that as you’re the sole developer there’s really no need to do that. But trust me, that’s the worst decision you might make during the app creation period. Suppose that you successfully implement the application, you sell it either on the app store or in a client of yours,and then you put it in the shelf.And after six months or so, you must create a new version of it by adding new features. When you open the project again and look to the existing code, a long before you write the first new line, you realize one killing truth:That you don’t remember almost anything! It’s hard to remember what you did, how you did it, and why! You must follow the one, painful way to wake that project up in your mind, which is no other than taking the project from the beginning and trying to “decode” your implementation line by line. Just a few comments here and there are not helpful, and eventually you end up making a super-effort for a long time until you understand everything. Many of you that you are now reading these lines may have come to that point, and I ensure you that there were times that I’ve been there too. This case is a real nightmare, and you often want to start building the project from scratch. And of course, the scenario described here would just be a… scenario, if we all invested a little time to write proper code documentation.

如果你是应用程序惟一的开发人员,那么就知道编写代码文档需要花费一定的时间的,因此,跳过这一步将会使你尽早的达成目标.此外,这也很容易使你觉得你是惟一的开发者没有必要这样做.但是相信我,这也许会是你在创建应用程序期间做的最糟糕的决定.假设你成功的实现了这个应用程序,你在App Store或是你自己的客户端的柜台上销售它.六个月之后,你需要在新版本中添加些新特性.当你再次打开项目的时候,看着之前的代码许久之后才写下第一行代码,然后你意识到一个残酷的现实:你几乎不记得任何东西了!很难记起来你做过什么,怎么做的,为什么这么做!你必须遵循着这个痛苦的方法,在你的脑海中从头开始逐行解析.少量的注释对此也没什么帮助,最终你明白了一切,花了很长时间费了超级大的劲才结束.许许多多的你现在可能正在这一点上,而且我确信你不止一次陷在这里,因为我曾经也这样过.

On the other hand, if you’re working on a project as a member of a team, then avoiding documenting your code would be catastrophic. When you share code with other developers, you must explain up to a point what you do (in the code) and how you do it, and of course other developers are required to do that too. There’s no case developers in big projects to fully understand the code of other fellow developers, as among all, that leads to unneeded waste of time. So, writing documentation in this case it’s like some sort of communication, but also an assistance to other members of the team to get the meaning of your code. After all, each programmer writes code differently than others, so making clear all the points of your code is a must-do task.

另一方面,如果你作为团队的一员在为某个项目工作,逃避记录代码文档将是灾难性的.当你与其他开发人员共享代码的时候,你必须在一定程度上解释你的代码做了些什么,怎么做的,当然其他开发人员也需要这么做.在所有的大项目中任何情况下都不需要开发人员理解全部的代码,这避免了不必要的时间浪费.因此,这种情况下,编写文档不仅像是某种形式的交流.而且也帮助团队成员理解你的代码意义.毕竟每个程序员编写的代码不同,所以确保代码逻辑清晰是一个必做的任务.

So, hoping that I’ve made my point, let me carry on by saying that the proper documentation and commenting regards all programming languages and all platforms. No matter whether you write apps for iOS, web or desktop systems: The point is that you should document as you go, so it’s easy for you (and everyone else) to revise your code really easy and without much effort.

希望我讲述我的观点后,可以继续讲述所有的编程语言与系统平台下的合理的文档与注释.无论你是为iOS、Web或是桌面系统编写应用程序.关键是你应该去编写文档,这样你或者其他人修改你代码会变得非常简单而不用耗费太多精力.

As you understand, in this tutorial I am going to highlight the most important aspects of the code documentation. I’m going to talk for both Objective-C and Swift, as there are developers writing apps in both languages, and of course I’m planning to show you what the similarities and differences between those two languages are. Furthermore, I’ll show you how to produce full, web-based documentation for your app, but I’m afraid that I have to say that this is still an option for the Objective-C only.

正如你所知道的, 在本教程中,我将着重讲述代码文档最重要的方面.我将同时讲述Objective-C与Swift,因为现在有同时使用两种编程语言编写应用程序的开发者,当然我也打算告诉你这两者之间的异同.此外,我将向你展示完整的、基于互联网的应用程序文档,但我恐怕不得不说目前为止这仍只有Objective-C的选项.

As an iOS developer using the Xcode IDE, you might have been thought that the documenting possibilities would be the same for both languages. That’s not the case though, as Swift supports too few documenting options, compared always to the Objective-C, at least at the time of the writing of this post. However, they both give you enough “supplies” so you can write nice documentation. We will begin with Objective-C, as there are more than enough things to say there, and we’ll close this tutorial with code documentation in Swift. There is no need to get in more details now, as we’ll see them in the next parts.

作为一名使用Xcode IDE的iOS开发者,你可能会认为记录两种编程语言文档可能是一样的.事实并非如此,与Objective-C相比,至少在本博客写下的时候,Swift支持的文档选项还非常少.然而,它们都提供了充足的选项,因此你可以编写精美的文档.我们将会使用Objective-C,因为这样有足够多的事情来讲述.此外在文档结束的时候会讲到Swift的代码文档.现在不需要知道更多的细节,在接下来我们会看到这些.

Before we begin, let me note two last things. First, I’m not trying to make you mad about documentation, just to convince you that writing proper comments will improve your programming life. Second, writing code documentation it’s just a habit you have to adopt, and definitely it’s not a waste of time.

在我们开始之前,让我提醒最后两件事情.首先,我不想让你对文档着迷,只是为了说服你,合理的注释将改善你的编程的生活.其次,编写代码文档只是一种你必须遵守的习惯,而且绝对不是浪费时间.



### Creating a Demo App in Objective-C

### 创建Objective-C的演示程序

Let’s get started by creating a new project in Objective-C, which we will use as the testing base for what we are going to see next. If you haven’t done yet, launch Xcode and create a new project. As we are not going to create a real demo application, selecting the Single View Application is just fine.

让我们创建一个Objective-C项目来开始,接下来我们将使用这个作为基本测试项目.如果你还没做这个,那就打开Xcode创建一个新项目吧.由于我们不是要创建一个真正的演示程序,所以选择Single View Application就好了.

In the next step, name the project DocDemoObjC, and make sure to select the Objective-C option in the Language drop down menu:

在下一步中,给项目命名为DocDemoObjC,并确保Language的下拉菜单选项为Objective-C.

![t28_2_new_project_ObjC_2](http://www.appcoda.com/wp-content/uploads/2015/02/t28_2_new_project_ObjC_2.png)

Get finished with the guide by selecting a directory in your drive to save the project.

在指引中结束创建然后保存项目到选中的文件夹中.



### Documentation Specifics

### 文档细节描述

As you know, the simplest way to write a comment in both Objective-C and Swift is to use the two slashes as shown below:

如你所知,在Objective-C与Swift中编写一个注释最简单的方式是使用如下的双斜杠:

```
// This is a comment.
```

You can (and must) place anywhere in your code comments like that, so you clarify each part of it. However, when talking about code documentation, definitely I am not talking about the above notation. It would be pointless after all to devote a whole tutorial to that only. Code documentation regards a structured way to write comments using special keywords, also named tags, and marking the comments area with special characters, so the compiler perfectly understands it. There are a few simple rules that should be followed only. The result of all that is that your documentation can be displayed in three different places:

你可以(而且必须)这样在任何地方注释你的代码,然后阐述代码的每个部分.然而,当谈及到代码文档时,明显我不是在说上面的符号.毕竟在整篇教程中这个是没什么意义的.代码文档是一个结构化的方式使用特殊的关键字,也叫标记来编写注释,标记区域有特殊字符,因此编译器能完全理解.这里有一些简单的规则必须要遵守.这一切的结果是你的文档可以在三个不同的地方显示.

1. In the **Quick Help** Inspector of the Utilities panel.
2. In the **Help Popup** that is displayed when you press the Option key and click on the name of method, class or property.
3. In the code-completion popup.

1. 在Utilities面板中的**Quick Help** Inspector中.
2. 当你按住Option键点击方法名,类或是属性时弹出的**Help菜单**.
3. 代码自动完成的弹出菜单.

Additionally, proper code documentation enables you to produce complete HTML documentation for your app using various tools, such as the [HeaderDoc](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/HeaderDoc/intro/intro.html) and [Doxygen](http://www.stack.nl/~dimitri/doxygen/). We’ll talk about both of them later, and we’ll see how you can do what I just said.

此外,适当的代码文档可以让你使用各种工具为你的应用程序创建完整的HTML文档,例如[HeaderDoc](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/HeaderDoc/intro/intro.html)与[Doxygen](http://www.stack.nl/~dimitri/doxygen/).我们将在后面讨论它们,而且会看到如何做到我在这里所说的.

With all the above in mind, it’s time to make one more step further and say that there are three possible ways to mark a documentation area when writing code in Objective-C:

记住以上的,是时候再进一步了,在编写Objective-C代码时,有三种可能的方法来标记文档区域:

1. To include your comments in a /** – */ block.
2. To include your comments in a /*! – */ block.
3. To begin each commented line with three slashes: ///

1. 在/** – */ 块中包含你的注释.
2. 在/*! – */ 块中包含你的注释.
3. 在每个注释的行开始处打上三个斜杠: ///

In our examples in this tutorial we are going to use the second way to write our documentation. I’m choosing this way for two reasons: First, it’s the only one recognised by HeaderDoc, and if the comment blocks don’t begin with that, no help pages will be created. At second, even though Doxygen prefers the first way, it also recognises this too. So, it’s going to suit us in both cases. The third commenting style is usually used when documenting single lines, such as properties, but still, we’re going to stick to the second option.

在本教程的案例中,我们将采用第二种方式来编写文档.我选择这种方式有两种原因:首先,这是可以被HeaderDoc识别的惟一一种方式,而且要是注释语句块没有这样开始就不会有帮助界面创建.其次:即使Doxygen更倾向于第一种方式,它也可以识别第二种.因此,这同时适应两种情况.第三种方式常用于单行注释,例如属性,不过,我们还是坚持使用第二种方式.

Now, there are specific keywords (or tags) you can use when writing documentation. Tags are divided in two categories: The first one regards top level tags, which they can be used to specify what kind of code exactly is commented, such as classes, structs, files, etc. Note that top level tags are not required to be used, but definitely help exporting tools (such as HeaderDoc and Doxygen) to create better results. In the second category exist the second level tags, which specify each details for each part of the documentation block. This kind of tags is actually what you need, as each one of them define another documentation part.

现在,你可以在编写文档的时候使用特定的关键字(或标签).标签分为两类:第一类是顶级标签,可以用来标识被注释代码的类型,如类、结构体、文件等等.需要注意的是顶级标签一般不需要使用,不过对文档导出工具(如HeaderDoc与Doxygen)生成更好的结果肯定会有帮助.第二类则是第二层标签.它指定哪一个细节为哪个文档的一部分.这种标签是你实际需要的.因为其中的某个部分也在其他文档中定义了.

Right next I give you the most important second level tags, but note that they’re not just them. We’ll see a few top level tags later. What I list is what is mostly used:

接下来我会讲解常见的二级标签,但它们肯定不止这些的.在稍后我也会部分顶级标签.这里列出了一些常用的二级标签:

- @brief: Use it to write a short description about the method, property, class, file, struct, or enum you’re documenting. No line breaks are allowed.
- @discussion: Use it to write a thorough description. You can add line breaks if needed.
- @param: With this you can describe a parameter of a method or function. You can have multiple such tags.
- @return: Use it to specify the return value of a method or function.
- @see: Use it to indicate other related method or functions. You can have multiple such tags.
- @sa: Similar to the previous one.
- @code: With this tag, you can embed code snippets in the documentation. When viewing the documentation in Help Inspector, the code is represented with a different font, inside a special box. Always use the @endcode tag when you finish writing code.
- @remark: Use it to highlight any special facts about the code you’re currently documenting.

- @brief: 编写简单的描述,可用于方法、属性、类、结构体或者是枚举,不允许有换行符.
- @discussion: 编写一个全面的描述.如果需要,可以添加换行符.
- @param: 描述一个方法或函数的一个参数.可以有多个这样的标签.
- @return: 指定方法或者函数的返回值.
- @see: 显示相关的方法或者函数.可以有多个这样的标签.
- @sa: 类似于上一个.
- @code: 使用这个标签,可以在文档中嵌入代码片段.在Help Inspector查看文档时,代码是在一个盒子中用不同的字体来表示.需要使用@endcode 来标记代码结束.
- @remark: 高亮当前记录的代码中任何特殊的事实.

You can find a full list of all supported tags [here](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/HeaderDoc/tags/tags.html).
 在[这里](https://developer.apple.com/library/mac/documentation/DeveloperTools/Conceptual/HeaderDoc/tags/tags.html)你可以查看完整的支持列表.

Note that the @ character is a prefix to each tag. Also, you can use special switches inside text, so you change its style and formatting. For example, the `<b>Text</b>` will make the Text word bold, while the `<i>Text</i>` will make the Text italic. It’s also interesting that you can represent part of the text as code (not a code snippet), if you write @cText. This will result to a different font formatting when the help is displayed in Xcode.

注意@符号是任意标签的前缀.当然,你也可以在文本中使用指定的转换器,这样就可以改变其样式了.举个粟子,`<b>Text</b>`可以让字体变粗,而`<i>Text</i>`则让字体倾斜.如果使用@cText标记文本,就可以让部分文字表现为代码的形式,这也是很有趣的.这些将会造成在Xcode中显示的帮助样式变化多端.

Alternatively to the above, you can replace the @ symbol with the backslash `(\)`. That way the tags will be represented like this: \brief, \param, \return, etc. Note that the backslash is mostly used by the Doxygen documenting system, while the @ is used by the HeaderDoc. Here we’ll use everywhere the @, as it’s compatible with both documenting systems.

此外,你也可以使用反斜杠`(\)`来替代@符号.这种情况时,这些标签被表现为:\brief, \param, \return等.注意,反斜杠大多用于Doxygen文档记录.而@则被HeaderDoc使用.在这里,我们使用@来兼容记录系统.



### Code Documentation in Objective-C

### Objective-C中的代码文档

Let’s see now how everything I mentioned above is used. Open the *ViewController.m* file, and in the private section of the class add the next property:

现在来看看如何使用我上面提到的这些.打开*ViewController.m*,在类的私有部分添加这样一个属性:

```
@interface ViewController ()

@property (nonatomic, strong) NSString *myName;

@end
```

Now, document it as shown next:

然后,向下面这样记录:

```
/*! @brief This property knows my name. */
@property (nonatomic, strong) NSString *myName;
```

Go then to the *viewDidLoad* method, and start typing it. You will see that in the code completion popup the description we just wrote is there!

然后去到*viewDidLoad*方法,并开始输入.你会在代码提示中发现我们刚才编写的描述!

![t28_3_myname_code_completion](http://www.appcoda.com/wp-content/uploads/2015/02/t28_3_myname_code_completion.png)

But not only. While holding down the Option key in your keyboard, click on the myName property name to let the help popup come up:

而且不止如此.当按住Option键的时候,点击*myName*属性,接着在弹出的帮助窗口中:

![t28_4_myname_help_popup](http://www.appcoda.com/wp-content/uploads/2015/02/t28_4_myname_help_popup.png)

Even more, if you open the Help Inspector in the Utilities panel, you’ll find the same documentation there too:

此外,如果你打开工具栏的Help Inspector,你会在这里发现一模一样的文档:

![Help inspector](http://www.appcoda.com/wp-content/uploads/2015/02/t28_5_myname_help_inspector.png )

Note that in the above comment the @brief tag could be omitted without any problem at all, as it’s inferred. That means that the next comment is valid as well:

注意上面的@brief标签可以被忽略而不会有任何问题,这是由于它的自动推断.这位这下一个注释也是合理的:

```
/*! This property knows my name. */
@property (nonatomic, strong) NSString *myName;
```

Also, the next notation is equal too:

当然,下面这个也是一样的:

```
/** This property knows my name. */
@property (nonatomic, strong) NSString *myName;
```

And this is also equal:

这个也是:

```
/// This property knows my name. */
@property (nonatomic, strong) NSString *myName;
```

Let’s see a first, easy example on how to document methods. At this point you must acknowledge that if your goal is to produce an HTML documentation, then only the documentation in the public methods (the header .h files) is visible. Whatever you write in the private parts of your classes is still visible in Xcode help, but no implementation is exported to the documentation. So, knowing that, let’s define a public method in the ViewController.h file:

我们先来看一个简单的例子,如何创建方法的文档.在这点上,你必须要知道,如果你的目的是生成HTML文档,那只需要在可见的公共方法(.h头文件)上记录.不论你在类的私有部分记录了什么,都只能在Xcode中看见,而不会导出到文档中,因此必须要知道这一点.让我们在*ViewController.h*中定义一个公共方法:

```
@interface ViewController : UIViewController

-(float)toCelcius:(float)fromFahrenheit;

@end
```

Obviously, this method is going to convert the given Fahrenheit degrees to Celsius. Let’s add its documentation now:

显然,这个方法是将给定的华氏温度转换成摄氏温度.现在让我们添加它的文档:

```
/*!
    @brief It converts temperature degrees from Fahrenheit to Celsius scale.

    @discussion This method accepts a float value representing the temperature in &lt;b&gt;Fahrenheit&lt;/b&gt; scale and it converts it to the &lt;i&gt;Celsius&lt;/i&gt; scale.

                To use it, simply call @c[self toCelsius: 50];

    @param  fromFahrenheit The input value representing the degrees in the Fahrenheit scale.

    @return float The degrees in the Celsius scale.
 */
-(float)toCelcius:(float)fromFahrenheit;
```

Note that above we use the and HTML switches to make the included words bold and italic respectively. Also notice how we mark the inline code using the @c switch.

注意上面我们使用了HTML转换器分别将包含的单词转换为粗体或斜体.同时,我们也使用@c标记内联的代码.

To see in the Xcode help how that documentation looks, open the ViewController.m file and define the method:

如果要看看这份文档在Xcode的帮助中看起来如何,就打开*ViewController.m*然后定义这样一个方法

```
-(float)toCelcius:(float)fromFahrenheit{
    return (fromFahrenheit - 32) / 1.8;
}
```

Then place the caret on the method’s name, and look in the Quick Help Inspector:

然后移动到方法名上,查看Quick Help Inspector:

![t28_6_method1_help_inspector](http://www.appcoda.com/wp-content/uploads/2015/02/t28_6_method1_help_inspector1.png)

You see that Xcode properly formats each part of the documentation. Here’s the same in the help popup (Option + Click on the method’s name):

可以看到Xcode正确的格式化了文档的每个部分.下面是在help菜单中一模一样的效果(Option + 点击方法名):

![t28_7_method1_help_popup](http://www.appcoda.com/wp-content/uploads/2015/02/t28_7_method1_help_popup.png)

If you call it in the *viewDidLoad* method, then you can see the brief description in the code-completion popup:

如果在*viewDidLoad*方法中调用它,那么你可以看到自动提示弹出的简要描述:

![method call pop over](http://www.appcoda.com/wp-content/uploads/2015/02/t28_8_method1_call.png)

Great, isn’t it? Just imagine how helpful and self-explaining your code can be that way, especially if you’re working with other people in a team.

是不是很棒?这对于你的代码成为意义明确的是多么有帮助的,特别是如果你的与他人在一个团队工作时.

To make this example even more interesting, let’s add one more public method that will do the exact opposing thing: Conversion from Celsius to Fahrenheit. Open the *ViewController.h* file, and add the next method declaration, along with its documentation:

为了使这个例子更加有趣,我们再加一个公共方法做完全相反的一件事:将摄氏温度转换为华氏温度.打开*ViewController.h*,然后加上方法的声明以及文档:

```
/*!
    @brief It converts temperature degrees from Celsius to Fahrenheit scale.

    @param  fromCelcius The celsius degrees value.

    @return float The degrees in the Fahrenheit scale.

    @code
        float f = [self toCelsius:80];
    @endcode

    @remark This is a super-easy method.
 */
-(float)toFahrenheit:(float)fromCelcius;
```

In the above snippet we added two new tags: The @code – @endcode pair, and the @remark. You’ll see how they’re displayed right next.

在上面的代码片段中我们加入了两个新的标签: @code – @endcode,以及@remark.接下来你会看到它们是如何显示的:

Let’s go to implement the method in the ViewController.m file:

我们在*ViewController.m*中实现这个方法

```
-(float)toFahrenheit:(float)fromCelcius{
    return fromCelcius * 1.8 + 32;
}
```

And let’s see the Help Popup now:

然后看看Help窗口:

![t28_9_method2_help_popup](http://www.appcoda.com/wp-content/uploads/2015/02/t28_9_method2_help_popup.png)

Pretty nice! Now your documentation has nothing to be jealous of the Xcode default documentation.

非常好!现在你的文档与Xcode默认的文档没什么区别啦.

Before we move to the next part, open the *ViewController.h* file, and add the following property declaration (commented of course):

在我们进入下一步之前,打开*ViewController.h*加上下面的属性声明(当然也要加上注释):

```
/*! An application delegate object. */
@property (nonatomic, strong) AppDelegate *appDelegate;
```

Along with it, import the *AppDelegate* class:

此外,导入*AppDelegate*类:

```
#import <AppDelegate.h>
```

The reason we added the above property to the class is to make us able to see later how it is exported using the documentation tools (HeaderDoc and Doxygen), along with the methods we already created.

我们加入以上的属性到类里面的原因是让我们在之后如何使用文档工具(HeaderDoc与Doxygen)连同早先创建的方法一起导出的.



# Files, Classes, Structs and Enums

# 文件,类,结构体与枚举值

In the previous part we went through the basic documenting principles that you should be aligned with when writing properties or methods/functions. I intentionally chose to begin demonstrating from that point, as the most of your developing time will be consumed there. Now that we’ve seen some of the essentials of our topic, let’s keep going by taking a look on how to add documentation notes regarding files, classes, structs and enums.

在上一个部分我们了解了在编写属性或是方法/函数时如何记录的基本原则.现在我们已经知道了主题中的基本要素,让我们继续看一下如何为文件、类、结构体与枚举记录文档吧.

Let’s get started by files, and how you write informative documentation in Objective-C. When sharing programming work with others, or you distribute your code as an open source component, then adding file documentation is almost mandatory, as that’s the best place to provide specific information to your partners or users of your code. Normally, you are going to give extra attention to the header file (.h) and the descriptions in it, as this is the one in the final documentation that will be exported (not in Xcode); however this doesn’t mean that you should not add description to implementation files. Don’t forget that when a project opens in Xcode, everything it’s there, not just the header files, so make sure you don’t leave any part of it undocumented. Besides that, implementation is not shown in exported documentation, but the description of all files is always visible.

让我们从如何在Objective-C文件记录文档开始吧.当你与人分享你的编程成果或者是将之作为一个开源组建分发出去的时候,这时候文档几乎类似于一个委托者.因为这是为你的合作伙伴或用户提供特定信息的最佳场所.通常,你会额外关注头文件(.h)并描述它,因为这是最后导出的文档(不是在Xcode中).然而这并不意味着你不需要在实现文件中添加描述信息.别忘了当你在Xcode中打开它的时候,不只是头文件,所有的都在这里.所以要确保任意部分都有文档.除此之外,实现部分并不会在导出的文档中,但所有文件的描述都是可见的.

Let me introduce you a few new tags that you can use when documenting a file:

下面我来介绍一些为文件记录文档时可用的新标签吧:

@file: Use this tag to indicate that you’re documenting a file (header or not). If you’re about to use Doxygen to produce documentation, then you’d better set the file name right after this tag. It’s a top level tag.
@header: Similar to the above, but used with HeaderDoc. Don’t use the above if you won’t use Doxygen.
@author Use it to write the author info of the file.
@copyright: Add copyright info.
@version: Use it to write the current version of the file. Pretty important if versioning matters during the project’s lifetime.
There are more tags of course you could use, but those are the most usual ones. I would advice you to go either through the HeaderDoc or the Doxygen documentation, so you can find extra keywords to use if you want so.

@file: 表明当前文件是否是头文件. 如果你要使用Doxygen生成文档,那么你最好在文件名前设置这个标签.这是一个顶级标签.
@header: 与上面的类似,不过是用于HeaderDoc的.如果你不是使用Doxygen的就不要使用上面那个标签.
@author 描述文件作者的信息.
@copyright: 添加版权信息.
@version: 描述当前的版本.在项目的生命周期中版本问题非常重要.

There are more tags of course you could use, but those are the most usual ones. I would advice you to go either through the HeaderDoc or the Doxygen documentation, so you can find extra keywords to use if you want so.

当然这里有更多的标签供你使用,不过这些差不多就是最常用的.我建议你去浏览HeaderDoc与Doxygen的文档.这样你就可以找到你想使用的额外的关键字.

Let’s go to add documentation now to the *ViewController.h* header file. Go to the top of the file, right before the *import* command. There, add the next lines:

现在让我们在*ViewController.h*中添加文档吧.去到文件的顶部,在*import*语句后.加上下面的几行:

```
/*!
 @header ViewController.h

 @brief This is the header file where my super-code is contained.

 This file contains the most importnant method and properties decalaration. It's parted by two methods in total, which can be used to perform temperature conversions.

 @author Your_Name
 @copyright  2015 Your_Name
 @version    15.12.7
 */
 ```
 
You can replace the Your_Name string with your actual name, or your company’s name. Also, it’s always a good idea to use the *brief* tag instead of just omitting it, as this will enable the documenting systems (you’ll see later in HeaderDoc and Doxygen) to display the short description you add here in the output HTML pages. I remind you once again that instead of the “@” symbol, you can use the backslash, but only for Doxygen. Also, we are not going to see here how the above documentation appears, but we’ll do so a bit later when we’ll produce HTML files.

你可以替换Your_Name为你的名字,或你的公司的名字.此外,使用*brief*标记而不是省略它是一个好主意,因为这将使记录系统(稍后您将在HeaderDoc和Doxygen中看到)在导出的HTML中显示你添加的描述.我再次提醒你,你可以使用反斜杠,而不是“@”符号,但只能用于Doxygen.同时,我们不会在这里看到上面的文档如何出现,但稍后我们生成HTML文件的时候会看到。

All the above are great, but the truth is that the default informative comments added by Xcode in each new file you create are pretty good and enough in many cases. You would want to create a file description block when you work with other people in a team and each member must clarify the details of the file(s) he’s in charge for, or when you’re planning to produce complete documentation of the project using HeaderDoc or Doxygen, or lastly, when you’re the sole developer but the project is parted by a big number of files. Anyway, it’s up to you to decide the level of documentation you’re willing to go up to.

所有上面的都是很好的.但是许多情况下,Xcode默认在创建新文件时默认添加的注释信息已经足够好了.只有当你与其他人在团队工作时,每个人都必须澄清自己负责的文件的细节.又或是你计划使用HeaderDoc或Doxygen生成完整的文档时,最后就是你是该项目的惟一开发者,不过该项目由许许多多的文件组成.这时候你才会需要制作描述文件.总之,由你自己决定需要制作何种级别的文档.

Let’s see now how you can document a class or a protocol. Once again, I give you the most usual tags only. Look up in the documentation for more tags.

这次我们来看看如何给类或协议记录文档.同样的,我只会给你介绍最常用的标签.你可以查阅文档以了解更多的标签.

@class: Use it to point the starting point of a documentation block regarding a class. It’s a top level tag, and after it you should provide the class name.
@interface: Same as above.
@protocol: Just like the above two, just for protocols.
@superclass: The superclass of the current class.
@classdesign: Use this tag to mention any special design you follow or apply in the current class (for example, you could mention a Singleton class design, or something like this).
@coclass: The name of another class that the current one works with.
@helps: Name the class which the current one works as a helper for.
@helper: Name any other class(es) that work as helpers for the current one.

Actually, you will rarely need any of the above tags, except maybe for the superclass. The list is much longer, but I find it pointless to add more of them here. Let me underline that the tags starting from the superclass and below, are not recognised by Doxygen, only by HeaderDoc. Also, the Quick Help and Help Popup windows in Xcode display the values next to each tag, but not the tags themselves. So, decide if you’re about to use them or not, always depending on whether you’re going to use a documenting system, and which one.

@class:标记类的文档起始处.这是一个顶级标记,在此标签后你需要提供类名。
@interface:同上.
@protocol:与上面两个一样,不过是对应协议的。
@superclass:当前类的超类。
@classdesign:标识你当前的类遵循的设计原则(例如,单例模式或类似的).
@coclass:标识当前类的协作类.
@helps:标识当前的类作为其辅助工作的类.
@helper:标识当前类中的辅助类.

实际上,除了@superclass你几乎不需要上面的标签,这份列表略长,但是我发现添加更多的也没什么意义.我强调一下从@superclass开始到之后的标签只有HeaderDoc会识别,而 Doxygen是不识别它们的的.此外, Quick Help 和Xcode的Help弹出会显示每个标签标记的,而不是标签本身.如果你决定要使用它们,总是取决于你想使用哪一个记录系统.

Let’s see an example by documenting our class in the ViewController.h file. Just right before the interface body opening, add these:

让我们看看在ViewController.h记录我们的类的文档的例子吧.在interface部分前加上这些:

```
/*!
 @class ViewController

 @brief The ViewController class

 @discussion    This class was designed and implemented to help people covert temperatures between the Fahrenheit and Celsius scales.

 @superclass SuperClass: UIViewController\n
 @classdesign    No special design is applied here.
 @coclass    AppDelegate
 @helps It helps no other classes.
 @helper    No helper exists for this class. 
 */
```
 
Now, if you place the caret on the *ViewController* class name, or you Option+Click on it, you can see how the above description is shown in the Xcode Help.

现在,如果你移动到*ViewController*的名字上,或者按住Option点击它,你可以看到Xcode Help窗口显示的描述.

![t28_10_class_name_help_popup](http://www.appcoda.com/wp-content/uploads/2015/02/t28_10_class_name_help_popup.png)

As you see the tags are not displayed, but in this case it doesn’t consist of a problem.

如你所见这些标签并未显示,但是这并不是问题.

It’s also great that you can see the class short description as you declare objects of it. To really watch this, open the *ViewController.m* file and go straight ahead in the *viewDidLoad* method. In there, start typing so you declare a local ViewController object. You’ll see the short description of the file appearing in the code completion popup window:

同样的你也可以看到你在类声明中的简短描述.要看到这个,可以打开*ViewController.m*然后去到*viewDidLoad*方法上.在哪里你声明一个*ViewController.m*对象.你就会在代码提示的弹出窗口中看到类的简介.

![t28_11_viewcontroller_declare_code_completion](http://www.appcoda.com/wp-content/uploads/2015/02/t28_11_viewcontroller_declare_code_completion.png)

In the same way as above you can document a protocol. Add the next lines before the class in the *ViewController.h* file:

你可以像上面那种方式给protocol添加文档.添加下面这行到*ViewController.h*类的类名前

```
/*!
    @protocol ViewControllerDelegate

    @brief The ViewControllerDelegate protocol

    It's a protocol used as a demo here. In a real application it would be quite useful.
 */
@protocol ViewControllerDelegate

/*!
    Nothing to say here... Just testing documentation.
 */
-(void)thisIsADelegateMethod;

@end
```

Option+Click on the protocol’s name to see it appearing in the Help Popup window.

按住Option然后点击protocol的名字可以在帮助弹出窗口看到.

![](http://www.appcoda.com/wp-content/uploads/2015/02/t28_12_protocol_help_popup.png)

As you can see above, we also declared a delegate method. You might think at the moment that is meaningless, however I purposely want this method to be there for demo reasons only; when we’ll use HeaderDoc and Doxygen to export documentation later it will be nice to see it in the exported HTML pages.

如你所见，我们还声明了委托方法。你可能会认为此刻是毫无意义的，然而我故意让此出现只是为了这个演示程序；当我们使用 HeaderDoc 和 Doxygen 将文件导出后可以看到在导出的HTML页面中精致的输出。

Now that we’ve talked about files, classes and protocols, and you have hopefully got the big picture, let’s see a couple more special cases: How to document structs and enumerations. Their common element is that you use the @typedef top level tag in both of them, so you indicate the opening of the documentation block (I remind you that using top level tags is absolutely optional).

现在我们已经谈论了在文件、类和协议中如何制作文档，你可能已经有了解大概的方法，让我们看看几个更为特殊的情况：如何为结构体以及枚举制作文档。两者的共同点是都使用顶级标签@typedef，你可以在文档块中标注它们(提醒一下，顶级标签是可选的)

For Doxygen specifically, you should use the *@struct* tag for structs, and the *@enum* tag for enums, instead of the *@typedef*.

对于Doxygen来说，你需要对结构使用*@struct*标签，对枚举使用*@enum*标签，而不是*@typedef*标签

Let’s see the following code struct. Add it to the *ViewController.h* file right before the opening line of the interface:

让我们看看下面的代码结构。将其添加到*ViewController.h*文件的起始处：

```
typedef struct {
    int sun;
    int clouds;
    int rain;
    int snow;
} WeatherConditionsInDays;
```

Now add these few lines above it:

现在在上面加上这几行:

```
/*!
 @typedef WeatherConditionsInDays

 @brief  A struct about the weather.

 @discussion
 The values of this structure represent how many sunny, cloudy, rainy, and snowy days existed over the last year. If this was a real app, they could be perfectly used.

 @field sun Good weather
 @field clouds  Where's the sun?
 @field rain    Get an umbrella
 @field snow    Watch out... A snowball is coming!
 */
 
```

As you see here, there’s a new tag named *@field*. This tag is handy to describe each single variable of the struct. Again, I must highlight the difference between the HeaderDoc and Doxygen. In HeaderDoc, this tag is acceptable and valid. However, in Doxygen things change as it’s not compatible with this tag. What we could do in this case is to simply comment each variable separately. Doing that will also display the comment for each one when we’ll access them in the implementation file. Let’s see that (note that the *@field* tags and the comments above variables contain different values so it’s easier to be identified later):

如你所见，这里出现了新标签*@field*。此标签是描述结构体中的每个变量。此外，我必须强调一下Doxygen与HeaderDoc之间的差异。在HeaderDoc中，这个标签是有效的。然而，在Doxygen中并不兼容此标签。这种情况下我们只能分别注释每个变量。这样我们也能在实现文件中查看到每一个变量的注释。我们看看(注意*@field*标签和以上变量注释包含不同的值，所以以后很容易区别):

```
/*!
 @typedef WeatherConditionsInDays

 @brief  A struct about the weather.

 @discussion
 The values of this structure represent how many sunny, cloudy, rainy, and snowy days existed over the last year. If this was a real app, they could be perfectly used.

 @field sun Good weather
 @field clouds  Where's the sun?
 @field rain    Get an umbrella
 @field snow    Watch out... A snowball is coming!
 */
typedef struct {
    /*! Good weather */
    int sun;
    /*! At least it's not raining */
    int clouds;
    /*! Don't forget to get your umbrella */
    int rain;
    /*! Time to go skiiiiiing */
    int snow;
} WeatherConditionsInDays;
```

If you go now to the ViewController.m file and declare an variable of this struct, then by accessing any of the above values you’ll see the description in the code completion popup window.

如果你现在去*ViewController.m*中，声明一个该结构体的变量，然后访问任何一个以上的值，你会看到代码完成弹出窗口中的描述。

![](http://www.appcoda.com/wp-content/uploads/2015/02/t28_13_weather_sun_code_completion.png)

Enumerations are handled in the exact same way, so I leave it to you to as an exercise to create an enum type and document it. Easy task to accomplish, isn’t it? Once your enum is ready, use it in the *viewDidLoad* method to see if your documentation is appeared in any of the Xcode Help options.

枚举处理的方式与此几乎一致，因此我将这个留下来作为一个练习，你需要为枚举创建文档。这个任务并不困难，一旦你的枚举文档准备就绪，在*viewDidLoad*方法中使用，在任何的Xcode的帮助选项看看你的文档。

### Watching For Errors

### 查看错误

As you have seen in all the previous examples, next to the @param tag you must always write the parameter’s name, and after that a proper description. Of course, it’s quite possible that you mistype a parameter name, especially if it’s a bit complex, and to create documentation containing errors. However, there’s a way to be protected against that, and I suspect that you didn’t know that Xcode can be your trusty assistant in that.

看完上面所有的例子后，下一个轮到*@param*标签了，你必须总是编写参数的名称，之后是一个适当的描述。当然，也可能是你输错一个参数名称，尤其是如果它有点复杂的时候，这样创建的文档中会有错误。然而，有一种方法可以防止这一点，我怀疑你不知道的Xcode是你最值得信赖的助手。

Indeed, even though Xcode deals with documentation once its written, it can help you by preventing you mistyping any parameter names. All it gets to do that, is to enable a setting. Let’s see what I mean exactly:

事实上，即使文档刚刚编写，Xcode也能帮助你防止打错任何参数名。要做到这样需要开启一个设置。让我们来看看究竟：

In the Project Navigator, click on the Project’s group, and then go straight ahead to the **Build Settings** tab, as shown in the next figure:

在项目浏览器中，单击该项目的组，然后直到**Build Settings**选项卡，如下图:

![](http://www.appcoda.com/wp-content/uploads/2015/02/t28_14_select_build_settings.png)

Once you do so, in the Search box just type the comments keyword and wait to see the results right below. One of the returned results is a setting named **Documentation Comments**, and (normally) its current value is set to **NO**. All you have to do is to set the **YES** value, and you’re ready.

一旦你这样做完，在搜索框中只需键入关键字，就可以迅速看到下方的结果。一个返回的结果是**Documentation Comments**设置，（通常）其当前值被设置为**NO**。所有你需要做的是设置为**YES**，准备好。

![](http://www.appcoda.com/wp-content/uploads/2015/02/t28_15_enable_documentation_settings.png)

Now, let’s go to see how the above setting affects the way we document our code. Open the ViewController.m file, and go to the private class section, where we are about to declare a private method. Here it is:

现在，让我们去看看上面的设置会如何影响我们记录代码文档的方式。打开ViewController.m文件，然后转到私有类部分，在这里我们声明一个私有方法：

```
@interface ViewController ()

-(float)showCurrentTemperatureInCity:(NSString *)targetCityName showInScale:(NSString *)preferredScale;

@end
```

If we were really going to implement it, this method would return the temperature in the selected city, expressed in the preferred scale (Fahrenheit or Celsius). Now, let’s add a few comments above it:

如果我们真的要实现它，这个方法将返回所选城市的温度，用的首选标量（华氏或摄氏）。现在，让我们在上面添加几点注释：

```
/*!
    This method returns the current temperature in the selected city, expressed in either Fahrenheit or Celsious degrees.

    @param  targetcityName  The city that the temperature will be returned for.
    @param  preferredScale  Fahrenheit or Celsius.

    @return float   The current temperature of the city.
 */
-(float)showCurrentTemperatureInCity:(NSString *)targetCityName showInScale:(NSString *)preferredScale;
```

Once you copy-paste the above comments in your Xcode, a warning will appear in the line where the first parameter is documented, telling us that the targetcityName parameter was not found in the function declaration. And guess what? That’s true! Instead of writing the parameter name properly (targetCityName), I wrote it wrongly by not capitalizing the first character of the “city” word (targetcityName). That’s something that in a big project with a lot of comments we would probably not realize, but thankfully Xcode is here to give us a hand and an extra “pair of eyes”.

一旦你复制粘贴以上注释到你的Xcode中，在第一个记录了参数的文档那一行将会出现一个警告，它告诉我们， targetcityName参数未在函数声明中找到。你猜怎么着？这是真的！我错误地未将“city”的首字母大写（ targetcityName ），而不是写成参数名称正确（targetCityName） 。这件事情，在有很多注释的一个大项目中，我们可能不会发现，但幸运的Xcode是在这里给我们一个手和额外的“一双眼睛” 。

Now, if you click in the warning triangle icon at the left, you will notice that Xcode auto-suggests the proper parameter name, which you can click so it’s fixed. Alternatively, go and fix it on your own.

现在，如果你在左边的三角警示牌图标点击，你会发现， Xcode自动提示正确的参数名称，你可以点击来修正。也可以自己去解决。

![](http://www.appcoda.com/wp-content/uploads/2015/02/t28_16_fix_param_name_error.png)

With all the above, you don’t need to be afraid of making any typo while writing parameter names during documentation.

综上上述，你在文档编写时不必害怕写的参数名有任何错字。