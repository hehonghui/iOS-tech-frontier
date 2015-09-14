React Navtive框架教程
---

> * 原文链接 : [React Native Tutorial: Building Apps with JavaScript](http://www.raywenderlich.com/99473/introducing-react-native-building-apps-javascript)
* 原文作者 : [ColinEberhardt](http://www.raywenderlich.com/u/ColinEberhardt)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [kmyhy](https://github.com/kmyhy) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  校对中 


A few months ago Facebook announced React Native, a framework that lets you build native iOS applications with JavaScript – and the official repository just came out of beta today!

几个月前Facebook推出了React Native框架，允许开发者用JavaScript编写本地iOS App——今天，官方代码库的beta版本终于放出！

People have been using JavaScript and HTML5 to create iOS applications using the PhoneGap wrapper for a number of years, so is React Native really such a big deal?

早在几年前，开发者就已经在使用JavaScript和HTML5加上PhoneGap编译器来编写iOS App了，因此React Native框架是不是有点多余？

React Native is a big deal, and people are getting very excited about it for two main reasons:
1. With React Native your application logic is written and runs in JavaScript, whereas your application UI is fully native; therefore you have none of the compromises typically associated with HTML5 UI.
2. React introduces a novel, radical and highly functional approach to constructing user interfaces. In brief, the application UI is simply expressed as a function of the current application state.

但React Native确实是一个很了不起的东东，开发者为之欢欣鼓舞，这是因为：
1. 通过React Native框架，你可以用JavaScript来编写和运行应用程序逻辑，而UI却可以是真正的本地代码编写的；因此，你完全不需要一个HTML5编写的UI。
2. React框架采用了一种新颖的、激进的和高度函数式的方式来构建UI。简单说，应用程序的UI可以简单地用一个函数来表示应用程序当前的状态。

The key point with React Native is that it aims to primarily bring the power of the React programming model to mobile app development. It is not aiming to be a cross platform, write-once run-anywhere, tool. It is aiming to be learn-once write-anywhere. An important distinction to make. This tutorial only covers iOS, but once you’ve learned the concepts here you could port that knowledge into creating an Android app very quickly.

React Native的重点是把React编程模型引进到移动App的开发中去。它的目的并不是跨平台，一次编写到处运行。它真正的目标是“一次学习多处编写”。这是一个重大的区别。本教程只涉及iOS，但一旦你学会了它的思想，你就可以快速将同样的知识应用到Android App的编写上。

If you have only ever written applications in Objective-C or Swift, you might not be particularly excited about the prospect of using JavaScript instead. Although, as a Swift developer, the second point above should pique your interest!
如果你过去只使用O-C或Swift编写过iOS应用程序，你可能不会因为可以用JavaScript编写iOS App而激动不已。但是，作为一个Swift开发者，上面所说的第二点原因肯定会激起你的兴趣！

Through Swift, you’ve no doubt been learning new and more functional ways to encode algorithms, and techniques that encourage transformation and immutability. However, the way in which you construct your UI is very much the same as it was when developing with Objective-C: it’s still UIKit-based and imperative.

通过Swift，你毫无疑问已经学习到那些新颖的、函数式的编写代码的方法；以及一些和过去不同或相同的技术。但是，你构建UI的方式仍然和O-C时代没有太大的不同：仍然要离不开UIKit。

Through intriguing concepts such as a virtual DOM and reconciliation, React brings functional programming directly to the UI layer.
This tutorial takes you through the process of building an application for searching UK property listings:

通过一些有趣的概念，比如虚拟DOM和reconciliation，React直接将函数式编程的理念用到了UI层面。
这个教程带你一起构建一个搜索英国房产信息的应用：

![这里写图片描述](http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/PropertyFinder.png)

If you’ve never written any JavaScript before, fear not; this tutorial leads you through each and every step of the coding. React uses CSS properties for styling which are generally easy to read and understand, but if you need to, you can always refer to the excellent Mozilla Developer Network reference.
Want to learn more? Read on!

如果你之前从未写过任何 JavaScript ，别担心；这篇教程带着你一点一点编写代码。React 使用 CSS 属性来定义样式，这些样式通常都很易于阅读和理解，但是如果你想进一步了解，可以参考 Mozilla Developer Network reference。
要想学习更多内容，请往下看！

##Getting Started
##开始
The React Native framework is available via GitHub. You can grab the framework by either cloning the repository using git, or you can choose download it as a zip file. If you are not interested in the source code, you can also use the command line interface (CLI) to create your React Native project, which is the approach this tutorial will take.

React Native 框架托管在GitHub。要获得这个框架，你可以使用git命令克隆项目到本地，或者直接下载zip包。 如果你不想使用源代码，也可以用命令行界面（CLI）创建React Native项目，本文将使用这种方式。

React Native uses Node.js, a JavaScript runtime, to build your JavaScript code. If you don’t already have Node.js installed, it’s time to get it!
First install Homebrew using the instructions on the Homebrew website, then install Node.js by executing the following in a Terminal window:

React Native 使用了 Node.js，如果你的机器上没有安装Node.js，请先安装它。 
首先需要安装 Homebrew，安装指南请参考Homebrew网站。然后用brew命令来安装Node.js:

```
brew install node
```

Next, use homebrew to install watchman, a file watcher from Facebook:

然后安装 watchman（Facebook推出的文件改动监听器）:

```
brew install watchman
```
This is used by React Native to figure out when your code changes and rebuild accordingly. It’s like having Xcode do a build each time you save your file.
Next use npm to install the React Native CLI tool:

React Native通过watchman来监视代码文件的改动并适时进行编译。这就好比Xcode，它会在每次文件被保存时对文件进行编译。
然后用npm命令安装React Native 的CLI工具：

```
npm install -g react-native-cli
```
This uses the Node Package Manager to fetch the CLI tool and install it globally; npm is similar in function to CocoaPods or Carthage.
Navigate to the folder where you would like to develop your React Native application, and use the CLI tool to construct the project:

这个命令通过Node Package Manager来下载和安装CLI工具，npm是一个类似CocoPods或Carthage工具。
定位到要创建React Native 项目的文件夹，使用CLI工具创建一个新的React Native项目：

```
react-native init PropertyFinder
```
This creates a starter-project containing everything you need to build and run a React Native application.
If you look at the created folders and files you will find a node_modules folder, which contains the React Native framework. You will also find an index.ios.js file, which is the skeletal app created by the CLI tool. Finally, there is an Xcode project file and an iOS folder, containing the small amount of code required to ‘bootstrap’ your application.
Open the Xcode project file then build and run. The simulator will start and display the following greeting:

这将创建一个默认的React Native项目，其中包含有能够让React Native项目编译运行的必要内容。
在React Native项目文件夹中，有一个node_modules文件夹，它包含React Native 框架文件。此外还有一个 index.ios.js 文件，这是CLI创建的脚手架代码。最后，还有一个Xcode项目文件及一个iOS文件夹，后者会有一些iOS代码用于引导React Navtive App。
打开Xcode项目文件，build&run。模拟器启动并显示一句问候语：

![这里写图片描述](http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/ReactNative-Starter.png)

You might also have noticed that a terminal window has popped up, displaying the following:

与此同时Xcode还会打开一个终端窗口，并显示如下信息：
>  ===============================================================
 |  Running packager on port 8081.       
 |  Keep this packager running while developing on any JS         
 |  projects. Feel free to close this tab and run your own      
 |  packager instance if you prefer.                              
 |                                                              
 |     https://github.com/facebook/react-native                 
 |                                                              
 ===============================================================
> 
> Looking for JS files in    /Users/colineberhardt/Temp/TestProject  
> React packager ready.

This is the React Native packager, running under node. You’ll find out what it does shortly.
Don’t close the Terminal window; just keep it running in the background. If you do close it by mistake, simply stop and re-run the project via Xcode.

这是React Navtive Packager，它在node容器中运行。你待会就会发现它的用处。
千万不要关闭这个窗口，让它一直运行在后面。如果你意外关闭它，可以在Xcode中先停止程序，再重新运行程序。

> Note: One final thing before you get too deep in the code — you’re
> going to be writing a lot of JavaScript code in this tutorial, and
> Xcode is certainly not the best tool for this job! I use Sublime Text,
> which is a cheap and versatile editor, but atom, brackets or any other
> lightweight editor will do the job. 
> 
> 注意:
> 在开始接触具体的代码之前（在本教程中，主要是js代码），我们将推荐 Sublime
> Text这个文本编辑工具，因为Xcode并不适合用于编写js代码的。当然，你也可以使用 atom, brackets
> 等其他轻量级的工具来替代。

##Hello React Native
##你好， React Native
Before getting started on the property search application, you’re going to create a very simple Hello World app. You’ll be introduced to the various components and concepts as you go along.

在开始编写这个房产搜索App之前，我们先来创建一个简单的Hello World项目。我们将通过这个例子来演示React Native的各个组件和概念。
用你喜欢的文本编辑器（例如Sublime Text）打开index.ios.js ，删除所有内容。然后加入以下语句：

```
'use strict';
```
This enables Strict Mode, which adds improved error handling and disables some less-than-ideal JavaScript language features. In simple terms, it makes JavaScript better!

这将开启严谨模式，这会改进错误的处理并禁用某些js语法特性，这将让JavaScript表现得更好。

> Note: For a more detailed overview of Strict Mode, I’d encourage you
> to read Jon Resig’s article “ECMAScript 5 Strict Mode, JSON, and
> More”.
> 
> 注意: 关于严谨模式，读者可以参考 Jon Resig的文章：“ECMAScript 5 Strict Mode, JSON, and More”。

Next, add the following line:
然后加入这一句：

```
var React = require('react-native');
```

这将加载 react-native 模块，并将其保存为React变量。React Native 使用和Node.js 一样的 require 函数来加载模块，类似于Swift中的import语句。

> Note: For more information about JavaScript modules I’d recommend
> reading this article by Addy Osmani on writing modular JavaScript. 
> 
> 注意:
> 关于JavaScript 模块的概念，请参考 Addy Osmani的[这篇文章](http://addyosmani.com/writing-modular-js/)。

Just below the require statement, add the following:

然后加入如下语句：

```
var styles = React.StyleSheet.create({
  text: {
    color: 'black',
    backgroundColor: 'white',
    fontSize: 30,
    margin: 80
  }
});
```
This defines a single style that you will shortly apply to your “Hello World” text. If you’ve done any web development before, you’ll probably recognize those property names; React Native uses Cascading Style Sheets (CSS) to style the application UI.
Now for the app itself! Still working in the same file, add the following code just beneath the style declaration above:

这将定义一个css样式，我们将在显示“Hello World”字符串时应用这个样式。
在React Native 中，我们可以使用 [Cascading Style Sheets (CSS)](http://www.w3schools.com/css/) 语法来格式化UI样式。
接下来敲入如下代码:

```
class PropertyFinderApp extends React.Component {
  render() {
    return React.createElement(React.Text, {style: styles.text}, "Hello World!");
  }
}
```
Yes, that’s a JavaScript class!
Classes were introduced in ECMAScript 6 (ES6). Although JavaScript is constantly evolving, web developers are restricted in what they can use due to the need to maintain compatibility with older browsers. React Native runs within JavaScriptCore; as a result, you can use modern language features without worrying about supporting legacy browsers.

这里我们定义了一个JavaScript 类。JavaScript类的概念出现自ECMAScript 6。由于JavaScript是一门不断演变的语言，因此web开发者必须保持与浏览器的向下兼容。由于React Native基于JavaScriptCore，因此我们完全可以放心使用它的现代语法特性，而不需要操心与老版本浏览器兼容的问题。

> Note: If you are a web developer, I’d thoroughly encourage you to use
> modern JavaScript, and then convert to older JavaScript using tools
> such as Babel to maintain support for older and incompatible browsers.
> 
> 注意:如果你是Web开发人员，我建议你使用新的JavaScript语法。有一些工具比如 Babel，可以将现代JavaScript语法转变为传统JavaScript语法，这样就能和老式浏览器进行兼容。

PropertyFinderApp extends React.Component, the basic building block of the React UI. Components contain immutable properties, mutable state variables and expose a method for rendering. Your current application is quite simple and only requires a render method.
React Native components are not UIKit classes; instead they are a lightweight equivalent. The framework takes care of transforming the tree of React components into the required native UI.

PropertyFinderApp 类继承自 React.Componen，后者是React UI中的基础。Components包含了一些不可变属性、可变属性和一些渲染方法。当然，这个简单App中，我们就用到一个render方法。
React Native 的Components不同于UIKit 类，它们是轻量级的对象。框架会将React Components转换为对应的本地UI对象。
最后敲入如下代码：

```
React.AppRegistry.registerComponent('PropertyFinder', function() { return PropertyFinderApp });
```
AppRegistry defines the entry point to the application and provides the root component.
Save your changes to index.ios.js and then return to Xcode. Ensure the PropertyFinder scheme is selected with one of the iPhone simulators, and then build and run your project. After a few seconds you’ll see your “Hello World” app in action:

AppRegistry 代表了App的入口以及根组件。保存文件，返回Xcode。确保当前Scheme为PropertyFinder ，然后在模拟器运行App。你将看到如下效果：
<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/react-helloworld.png" width="281px" />

That’s a JavaScript application running in the simulator, rendering a native UI, without a browser in sight!
Still don’t trust me? :] Verify it for yourself: within Xcode, select Debug\View Debugging\Capture View Hierarchy and take a look at the native view hierarchy. You will see no UIWebView instances anywhere! Just a proper, real, view! Neat :]!

看到了吧，模拟器将JavaScript代码渲染为本地UI组件，你不会看到任何浏览器的痕迹。
你可以这样来确认一下：
在Xcode中，选中 Debug\View Debugging\Capture View Hierarchy，查看本地视图树。你将找不到任何UIWebView实例。
![这里写图片描述](http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/ViewDebugging.png)

Curious as to how it all works? In Xcode open AppDelegate.m and locate application:didFinishLaunchingWithOptions:. This method constructs a RCTRootView, which loads the JavaScript application and renders the resultant view.
When the application starts, the RCTRootView loads the application from the following URL:

你一定会惊奇这一切是怎么发生的。在 Xcode 中打开 AppDelegate.m，找到application:didFinishLaunchingWithOptions:方法。
在这个方法中，创建了一个RCTRootView，该对象负责加载JavaScript App并渲染相关视图。
App一启动，RCTRootView会加载如下URL的内容:

```
http://localhost:8081/index.ios.bundle
```
Recall the Terminal window that was opened when you ran this application; this starts a packager and server that handles the above request.
Open this URL in Safari; you’ll see the JavaScript code for your app. You should be able to find your “Hello World” code embedded among the React Native framework.
When your app starts, this code is loaded and executed by the JavaScriptCore framework. In the case of your application, it loads the PropertyFinderApp component, then constructs the native UIKit view. You’ll learn a bit more about this later in the tutorial.

还记得App启动时弹出的终端窗口吗？终端窗口中运行的packager和server会处理上述请求。
你可以用Safari来打开上述URL，你将会看到一些JavaScript代码。在React Native 框架代码中你会找到“Hello World”相关代码。
当App打开时，这些代码会被加载并执行。以我们的App来说，PropertyFinderApp组件会被加载，然后创建相应的本地UI组件。

##Hello World JSX
##你好， JSX
Your current application uses React.createElement to construct the simple UI for your application, which React turns into the native equivalent. While your JavaScript code is perfectly readable in its present form, a more complex UI with nested elements would rapidly become quite a mess.
Make sure the app is still running, then return to your text editor to edit index.ios.js. Modify the return statement of your component’s render method as follows:

前面我们用React.createElement构建了一个简单的UI ，React 会将之转换为对应的本地对象。但对于复杂UI来说（比如那些组件嵌套的UI），代码会变得非常难看。
确保App保持运行，回到文本编辑器，修改index.ios.js中的return语句为：

```
return <React.Text style={styles.text}>Hello World (Again)</React.Text>;
```
This is JSX, or JavaScript syntax extension, which mixes HTML-like syntax directly in your JavaScript code; if you’re already a web developer, this should feel rather familiar. You’ll use JSX throughout this article.

这里使用了JSX语法，即JavaScript 语法扩展，它基本上是将JavaScript代码混合了HTML风格。如果你是一个web开发人员，对此你应该不会陌生。 在本文中，JSX随处可见。

Save your changes to index.ios.js and return to the simulator. Press Cmd+R, and you’ll see your application refresh to display the updated message “Hello World (Again)”.

保存 index.ios.js回到iPhone模拟器，按下快捷键 Cmd+R，你会看到App的显示变成了 “Hello World (Again)”。

Re-running a React Native application is really as simple as refreshing a web browser! :]

重新运行React Navtive App如同刷新Web页面一样简单。

Since you’ll be working with the same set of JavaScript, you can leave the app running and simply refresh the app in this fashion after modifying and saving index.ios.js.

因为你实际上是在和JavaScript打交道，所以只需修改并保存index.ios.js，即可让App内容得到更新，同时不中断App的运行。

> Note: If you are feeling inquisitive, take a look at your ‘bundle’ in
> the browser to see what the JSX is transformed into. 
> 
> 注意:
> 如果你还有疑问，你可以用浏览器在看一下你的“Bundle”内容，它应该也发生了变化。

Okay, enough of this “Hello World” fun; it’s time to build the real application!
好了，“Hello World" 的演示就到此为止；接下来我们要编写一个真正的React App了！

##Adding Navigation
##实现导航

这个demo使用了标准的UIKit中的导航控制器来提供”栈式导航体验“。接下来我们就来实现这个功能。

Within index.ios.js, rename the PropertyFinderApp class to HelloWorld:

在 index.ios.js, 将 PropertyFinderApp 类修改为 HelloWorld:

```
class HelloWorld extends React.Component {
```

You’ll keep the “Hello World” text display around a little longer, but it won’t be the root component of your app anymore.
Next add the following class below the HelloWorld component:

我们仍然要显示“Hello World”字样，但不再将它作为App的根视图。
然后为HelloWorld加入以下代码：

```
class PropertyFinderApp extends React.Component {
  render() {
    return (
      <React.NavigatorIOS
        style={styles.container}
        initialRoute={{
          title: 'Property Finder',
          component: HelloWorld,
        }}/>
    );
  }
}
```
This constructs a navigation controller, applies a style and sets the initial route to the HelloWorld component. In web development, routing is a technique for defining the navigation structure of an application, where pages — or routes — are mapped to URLs.
Within the same file, update the styles declaration to include the container style as shown below:

这将创建一个导航控制器，并指定了它的外观样式和初始route（相对于HelloWorld视图）。在web开发中，routing是一种技术，用于表示应用程序的导航方式，即哪个一页面（或route）对应哪一个URL。
然后修改css样式定义，在其中增加一个container样式：

```
var styles = React.StyleSheet.create({
  text: {
    color: 'black',
    backgroundColor: 'white',
    fontSize: 30,
    margin: 80
  },
  container: {
    flex: 1
  }
});
```

You’ll find out what flex: 1 means a bit later in this tutorial.
Head back to the simulator and press Cmd+R to see your new UI in action:

flex: 1的意思稍后解释。
回到模拟器，按 Cmd+R 查看效果:

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/react-helloworldagain.png" width="281"/>

There’s the navigation controller with its root view, which is currently the “Hello World” text. Excellent — you now have the basic navigation structure for your application in place. It’s time to add the ‘real’ UI!
这个导航控制器有一个根视图，即图中显示的”Hello World“文本。非常好——我们的App已经具备了基本的导航功能。是时候显示一些”真正的“UI了！
##Building the Search Page
##实现查找
Add a new file to the project named SearchPage.js and place it in the same folder as index.ios.js. Add the following code to this file:

新建一个 SearchPage.js 文件，保存在 index.ios.js同一目录。在这个文件中加入代码：

```
'use strict';
 
var React = require('react-native');
var {
  StyleSheet,
  Text,
  TextInput,
  View,
  TouchableHighlight,
  ActivityIndicatorIOS,
  Image,
  Component
} = React;
```

You’ve already seen the strict mode and the react-native import before, but the assignment statement that follows it is something new.
This is a destructuring assignment, which lets you extract multiple object properties and assign them to variables using a single statement. As a result, the rest of your code can drop the React prefix; for example, you can refer directly to StyleSheet rather than React.StyleSheet. Destructuring is also useful for manipulating arrays and is well worth learning more about.
Still working in the same file, SearchPage.js, add the following style:

这里使用了一个解构赋值（destructuring assignment），可以将多个对象属性一次性赋给多个变量。这样，在后面的代码中，我们就可以省略掉React前缀，比如用StyleSheet 来代替 React.StyleSheet。解构赋值对于数组操作来说尤其方便，请参考[well worth learning more about.](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)
然后定义如下Css样式:

var styles = StyleSheet.create({
  description: {
    marginBottom: 20,
    fontSize: 18,
    textAlign: 'center',
    color: '#656565'
  },
  container: {
    padding: 30,
    marginTop: 65,
    alignItems: 'center'
  }
});

Again, these are standard CSS properties. Setting up styles like this is less visual than using Interface Builder, but it’s better than setting view properties one by one in your viewDidLoad() methods! :]
Add the component itself just below the styles you added above:

这里，再次使用了标准的 CSS 属性。虽然用CSS设置样式在可视化方面比起在IB中要差一些，但总比在viewDidLoad()方法中用代码写要好一些。
然后加入以下代码：

```
class SearchPage extends Component {
  render() {
    return (
      <View style={styles.container}>
        <Text style={styles.description}>
          Search for houses to buy!
        </Text>
        <Text style={styles.description}>
          Search by place-name, postcode or search near your location.
        </Text>
      </View>
    );
  }
}
```

render is a great demonstration of JSX and the structure it provides. Along with the style, you can very easily visualize the UI constructed by this component: a container with two text labels.
Finally, add the following to the end of the file:

在render方法中使用了大量的JSX语法来构造UI组件。通过这种方式，你可以非常容易地构造出如下组件：在一个Container View中包含了两个label。
在源文件的最后加入这一句：

```
module.exports = SearchPage;
```

This exports the SearchPage class, which permits its use in other files.
这一句将使 SearchPage 类可被其他js文件引用。

The next step is to update the application routing in order to make this the initial route.

然后需要修改App的导航。

Open index.ios.js and add the following just after the current require import near the top of the file:

打开 index.ios.js 在文件头部、现有的require 语句后加入 require 语句:

```
var SearchPage = require('./SearchPage');
```

Within the render function of the PropertyFinderApp class, update initialRoute to reference the newly added page as shown below:

在 PropertyFinderApp 类的  render 函数中，修改 initialRoute 为:

```
component: SearchPage
```

At this point you can remove the HelloWorld class and its associated style, if you like. You won’t be needing that code any longer.

这里我们可以将HelloWorld类和它对应的样式移除了，我们不再需要它。

Return to the simulator, hit Cmd+R and check out the new UI:

回到模拟器，按下 Cmd+R 查看效果:

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/react-searchstarter.png" width="281"/>

This is using the new component, SearchPage, which you added.

你新创建的组件SearchPage显示在屏幕中。
##Styling with Flexbox
##弹性盒子模型
So far, you’ve seen basic CSS properties that deal with margins, paddings and color. However, you might not be familiar with flexbox, a more recent addition to the CSS specification that is very useful for application UI layout.

一直以来，我们都在用原始的CSS属性来设置外边距、内边距和颜色。但是，最新的CSS规范中增加了弹性盒子的概念，非常利于我们对App的UI进行布局，虽然你可能还不太熟悉它。

React Native uses the css-layout library, a JavaScript implementation of the flexbox standard which is transpiled into C (for iOS) and Java (for Android).

React Native 使用了 css-layout 库，在这个库中实现了弹性盒子，而这种模型无论对iOS还是Android来说都很好理解。

It’s great that Facebook has created this as a separate project that targets multiple languages, since this allows for novel applications, such as applying flexbox layout to SVG (yes, that was written by me … and no, I don’t sleep much!).

更幸运的是，Facebook针对许多语言单独实现了这个项目，这就引申出了许多新颖的用法，比如[在SVG中应用弹性盒子布局](http://blog.scottlogic.com/2015/02/02/svg-layout-flexbox.html)（是的，这篇文章也是我写的，为此我不得不熬到深夜）。

In your app, the container has the default flow direction of column, which means its children are arranged in a vertical stack, like so:

在这个App中，采用了默认的垂直流式布局，即容器中的子元素按照从上到下的顺序进行布局。比如：
<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/FlexStack.png" width="281"/>

This is termed the main axis, and can run either vertically or horizontally.

这被称作主轴, 主轴可能是水平方向，也可能是垂直方向。

The vertical position of each child is determined from a combination of its margin, height and padding. The container also sets the alignItems property to center, which determines the placement of children on the cross axis. In this case, it results in center-aligned text.

每个子元素的纵向位置由它们的边距（margin）、间距（padding）和高决定。容器的alignItems属性会被设置为居中（center），这决定了子元素在交叉轴上的位置。在本例里，将导致子元素水平居中对齐。

It’s time to add the input field and buttons. Open SearchPage.js and insert the following just after the closing tag of the second Text element:

接下来我们添加一些文本输入框和按钮。打开SearchPage.js 在第二个 Text 元素后添加:

```
<View style={styles.flowRight}>
  <TextInput
    style={styles.searchInput}
    placeholder='Search via name or postcode'/>
  <TouchableHighlight style={styles.button}
      underlayColor='#99d9f4'>
    <Text style={styles.buttonText}>Go</Text>
  </TouchableHighlight>
</View>
<TouchableHighlight style={styles.button}
    underlayColor='#99d9f4'>
  <Text style={styles.buttonText}>Location</Text>
</TouchableHighlight>
```
You’ve added two top-level views here: one to hold a text input and a button, and one with only a button. You’ll read about how these elements are styled in a little bit.

这段代码添加了两个顶级的视图：一个文本输入框外加一个按钮，以及一个单独的按钮。它们所使用的样式待会我们再介绍。

Next, add the accompanying styles to your styles definition:

接着，在styles中增加如下样式：

```
flowRight: {
  flexDirection: 'row',
  alignItems: 'center',
  alignSelf: 'stretch'
},
buttonText: {
  fontSize: 18,
  color: 'white',
  alignSelf: 'center'
},
button: {
  height: 36,
  flex: 1,
  flexDirection: 'row',
  backgroundColor: '#48BBEC',
  borderColor: '#48BBEC',
  borderWidth: 1,
  borderRadius: 8,
  marginBottom: 10,
  alignSelf: 'stretch',
  justifyContent: 'center'
},
searchInput: {
  height: 36,
  padding: 4,
  marginRight: 5,
  flex: 4,
  fontSize: 18,
  borderWidth: 1,
  borderColor: '#48BBEC',
  borderRadius: 8,
  color: '#48BBEC'
}
```
Take care with the formatting; each style property should be separated by a comma. That means you’ll need to add a trailing comma after the container selector.

不同样式属性间以逗号分隔，这样你在container选择器后必须以一个逗号结尾。

These styles are used by the text input and buttons which you just added.

这些样式将被文本输入框和按钮所用。

Return to the simulator and press Cmd+R to see the updated UI:

回到模拟器，按下Cmd+R ，你将看到如下效果：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/react-searchpageinput.png" width="281"/>

The text field and ‘Go’ button are on the same row, so you’ve wrapped them in a container that has a flexDirection: 'row' style. Rather than explicitly specifying the widths of the input field and button, you give each a flex value instead. The text field is styled with flex: 4, while the button has flex: 1; this results in their widths having a 4:1 ratio.

Go按钮和其紧随的文本框在同一行，因此我们将它们用一个容器装在一起，同时容器的flexDirection: 样式属性设置为'row' 。我们没有显式指定文本框和按钮的宽度，而是分别指定它们的flex样式属性为4和1。也就是说，它们的宽度在整个宽度（屏幕宽度）中所占的份额分别为4和1。

You might have noticed that your buttons…aren’t actually buttons! :] With UIKit, buttons are little more than labels that can be tapped, and as a result the React Native team decided it was easier to construct buttons directly in JavaScript. The buttons in your app use TouchableHighlight, a React Native component that becomes transparent and reveals the underlay colour when tapped.

而且，视图中的两个按钮都不是真正的按钮。对于UIKit，按钮不过是可以点击的标签而已，因此React Native开发团队能够用JavaScript以一种简单的方式构建按钮：TouchableHighlight是一种React Native组件，当它被点击时，它的前景会变得透明，从而显示其隐藏在底部的背景色。

The final step to complete the search screen of the application is to add the house graphic. The images are available to download as a zip file. Download and unzip the file.

最后我们还要在视图中添加一张图片。这些图片可以在[此处](http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/ReactNative-HouseImage.zip)下载。下载后解压缩zip文件。

Within Xcode open the Images.xcassets file and tap the plus button to add a new image set. Next drag the required images into the correct ‘slots':

在 Xcode 打开 Images.xcassets 文件，点击加号按钮，添加一个新的image set。然后将需要用到的图片拖到image set右边窗口对应的位置。

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/AddingHouseImage.png" widht="400"/>

You’ll have to stop the running application and restart so that these images become available to your React Native app.

要让这些图片显示，必须停止你的 React Native App并重新启动。

Add the following beneath the TouchableHighlight component for the location button:

在location按钮对应的 TouchableHighlight 组件下加入：

`<Image source={require('image!house')} style={styles.image}/>`

Now, add the image’s corresponding style to the end of the style list, remembering to add a trailing comma to the previous style:

然后，为图片添加适当的样式定义，记得在上一个样式之后添加一个逗号结尾：

```
image: {
  width: 217,
  height: 138
}
```

The require('image!house') statement is used to reference an image located within your application’s asset catalogue. Within Xcode, if you open up Images.xcassets you will find the ‘house’ icon that the above code refers to.

由于我们将图片添加到了Images.xcasset资源包中，我们需要用require('image!house')语句获得图片在App包中的正确路径。在Xcode中，打开Images.xcassets ，你可以找到名为house的image set。

Returning to the simulator, hit Cmd+R and admire your new UI:

回到模拟器，按下Cmd+R 查看运行效果:

<img src="http://cdn2.raywenderlich.com/wp-content/uploads/2015/03/react-searchpagehouse.png" width="281"/>

> Note: If you don’t see the house image at this point and see an image
> that “image!house” cannot be found instead, try restarting the
> packager (i.e. the “npm start” command you have in the terminal).
> 
> 注意: 如果图片没有显示，却看到一个““image!house” cannot be
> found”的提示，则可以重启packager(在终端中输入npm start命令)。

Your current app looks good, but it’s somewhat lacking in functionality. Your task now is to add some state to your app and perform some actions.

到目前为止，我们的App看上去有模有样，但它还缺少很多实际的功能。接下来的任务就是为App增加一些状态，执行一些动作。
##Adding Component State
##组件状态
Each React component has its own state object, which is used as a key-value store. Before a component is rendered you must set the initial state.

每个React组件都有一个state对象，它是一个键值存储对象。在组件被渲染之前，我们可以设置组件的state对象。

Within SearchPage.js, add the following code to the SearchPage class, just before render():

打开SearchPage.js,在 SearchPage 类的 render()方法前，加入以下代码：

```
constructor(props) {
  super(props);
  this.state = {
    searchString: 'london'
  };
}
```

Your component now has a state variable, with searchString set to an initial value of london.

现在组件就有一个state变量了，同时我们在state中存放了一个 searchString:'london' 的键值对象。

Time to make use of this component state. Within render, change the TextInput element to the following:

然后我们来使用这个state变量。在render方法中，修改TextInput元素为：

```
<TextInput
  style={styles.searchInput}
  value={this.state.searchString}
  placeholder='Search via name or postcode'/>
```

This sets the TextInput value property — that is, the text displayed to the user — to the current value of the searchString state variable. This takes care of setting the initial state, but what happens when the user edits this text?

这将改变 TextInput 的value 属性，即在TextInput中显示一个london的文本——即state变量中的searchString。这个值在我们初始化state时指定的，但如果用户修改了文本框中文本，那又怎么办？

The first step is to create a method that acts as an event handler. Within the SearchPage class add the following method:

 首先创建一个事件处理方法。在 SearchPage 类中增加一个方法：

onSearchTextChanged(event) {
  console.log('onSearchTextChanged');
  this.setState({ searchString: event.nativeEvent.text });
  console.log(this.state.searchString);
}

This takes the value from the event’s text property and uses it to update the component’s state. It also adds some logging code that will make sense shortly.

首先从事件参数event中获得text属性，然后将它保存到组件的state中，并用控制台输出一些感兴趣的内容。

To wire up this method so it gets called when the text changes, return to the TextInput field within the render method and add an onChange property so the tag looks like the following:

为了让文本内容改变时这个方法能被调用，我们需要回到TextInput的onChange事件属性中，绑定这个方法，即新加一个onChange属性，如以下代码所示：

`<TextInput
  style={styles.searchInput}
  value={this.state.searchString}
  onChange={this.onSearchTextChanged.bind(this)}
  placeholder='Search via name or postcode'/>`

Whenever the user changes the text, you invoke the function supplied to onChange; in this case, it’s onSearchTextChanged.

一旦用户改变了文本框中的文本，这个函数立即就会被调用。

> Note: You might be wondering what the bind(this) statement is for.
> JavaScript treats the this keyword a little differently than most
> other languages; its counterpart in Swift is self. The use of bind in
> this context ensures that inside the onSearchTextChanged method, this
> is a reference to the component instance. For more information, see
> the MDN page on this.
> 
> 注意:  bind(this) 的使用有点特殊。JavaScript 中 this
> 关键字的含义其实和大部分语言都不相同，它就好比Swift语言中的self。bind方法的调用使得onSearchTextChanged
> 方法中能够引用到this，并通过this引用到组件实例。更多内容请参考 MDN page on this。

There’s one final step before you refresh your app again: add the following logging statement to the top of render(), just before return:

然后，我们在render方法顶部、return语句之前加一条Log语句：

```
console.log('SearchPage.render');
```

ou are about to learn something quite intriguing from these log statements! :]

通过这些log语句，你应该能明白大致发生了什么事情！

Return to your simulator, then press Cmd+R. You should now see that the text input has an initial value of ‘london’ and that editing the text causes some statements to be logged to the Xcode console:

返回模拟器，按下Cmd+R，我们将看到文本框中一开始就有了一个london的字样，当你编辑这段文本后，控制台中的内容将显示：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/react-renderconsole.png" width="200"/>

Looking at the screenshot above, the order of the logging statement seems a little odd:

 1. This is the initial call to render() to set up the view.
 2. You invoke onSearchTextChanged() when the text changes.
 3. You then update the component state to reflect the new input text,which triggers another render.
 4. onSearchTextChanged() then wraps things up by logging the new search string.

查看上面的截屏，log语句输出的顺序似乎有点问题：
1.	组件初始化后调用 render() 方法
2.	当文本被改变， onSearchTextChanged() 被调用
3.	我们在代码中改变了组件的state 属性，因此render()方法会被调用
4.	onSearchTextChanged() 打印新的search string.

Whenever the app updates the state of any React component, this triggers an entire UI re-rendering that in turn calls render of all of your components. This is a great idea, as it entirely de-couples the rendering logic from the state changes that affect the UI.

当React 组件的状态被改变时，都会导致整个UI被重新渲染——所有组件的render方法都会被调用。这样做的目的，是为了将渲染逻辑和组件状态的改变完全进行分离。

With most other UI frameworks, it is either your responsibility to manually update the UI based on state changes, or use of some kind of binding framework which creates an implicit link between the application state and its UI representation; see, for example, my article on implementing the MVVM pattern with ReactiveCocoa.

在其他所有的UI框架中，要么程序员在状态改变时自己手动刷新UI，要么使用一种绑定机制在程序状态和UI之间进行联系。就像我另一篇文章 [MVVM pattern with ReactiveCocoa](http://www.raywenderlich.com/74106/mvvm-tutorial-with-reactivecocoa-part-1)所讲。

With React, you no longer have to worry about which parts of the UI might be affected by a state change; your entire UI is simply expressed as a function of your application state.

而在React中，我们不再操心状态的改变会导致那一部分UI需要刷新，因为当状态改变所有的UI都会刷新。

At this point you’ve probably spotted a fundamental flaw in this concept. Yes, that’s right — performance!

当然，你也许会担心性能问题。

Surely you can’t just throw away your entire UI and re-build it every time something changes? This is where React gets really smart. Each time the UI renders itself, it takes the view tree returned by your render methods, and reconciles — or diffs — it with the current UIKit view. The output of this reconciliation process is a simple list of updates that React needs to apply to the current view. That means only the things that have actually changed will re-render.

难道每次状态改变时，整个UI都会被舍弃然后重新创建吗？
这就是React真正智能的地方。每当UI要进行渲染时，它会遍历整个视图树并计算render方法，对比与当前UIKit视图是否一致，并将需要改变的地方列出一张列表，然后在此基础上刷新视图。也就是说，只有真正发生变化的东西才会被重新渲染。

It’s amazing to see the novel concepts that make ReactJS so unique — the virtual-DOM (Document Object Model, the visual-tree of a web document) and reconciliation — applied to an iOS app.
You can wrap your head around all that later; you still have some work to do in the app. Remove the logging code you just added above, since it’s no longer necessary and just adds cruft to the code.

ReactJS将一些新奇的概念应用到了iOS App中，比如虚拟DOM（Document Object Modal，web文档可视树）和一致性。这些概念我们可以稍后再讨论，先来看下这个App接下来要做的工作。删除上面添加的Log语句。

##Initiating a Search
##开始搜索
n order to implement the search functionality you need handle the ‘Go’ button press, create a suitable API request, and provide a visual indication to the user that a query is in progress.
Within SearchPage.js, update the initial state within the constructor:

为了实现搜索功能，我们需要处理Go按钮点击事件，创建对应的API请求，显示网络请求的状态。
打开SearchPage.js, 在constructor方法中修改state的初始化代码：

```
this.state = {
  searchString: 'london',
  isLoading: false
};
```

The new isLoading property will keep track of whether a query is in progress.
Add the following logic to the start of render:

isLoading 属性用于表示查询是否正在进行。
在render方法最上面增加：

```
var spinner = this.state.isLoading ?
  ( <ActivityIndicatorIOS
      hidden='true'
      size='large'/> ) :
  ( <View/>);
```

这里用了一个三目运算，这是一个if语句的简化形式。如果isLoading为true，显示一个网络指示器，否则显示一个空的view。
在return语句中，在Image下增加：

```
{spinner}
```

Now, add the following property within the TouchableHighlight tag that renders the ‘Go’ text:

在Go按钮对应的 TouchableHighlight 标签中增加如下属性：

```
onPress={this.onSearchPressed.bind(this)}
```

Next, add the following methods to the SearchPage class:

在 SearchPage 类中新增如下方法:

```
_executeQuery(query) {
  console.log(query);
  this.setState({ isLoading: true });
}
 
onSearchPressed() {
  var query = urlForQueryAndPage('place_name', this.state.searchString, 1);
  this._executeQuery(query);
}
```

_executeQuery() will eventually run the query, but for now it simply logs a message to the console and sets isLoading appropriately so the UI can show the new state.

_executeQuery() 目前仅仅是在控制台中输出一些信息，同时设置isLoading属性为true，剩下的功能我们留到后面完成。

> Note: JavaScript classes do not have access modifiers, so they have no concept of ‘private’. As a result you often see developers prefixing methods with an underscore to indicate that they should be considered private.
> 
> 注意: JavaScript 类没有访问器，因此也就没有私有的概念。因此我们会在方法名前加一个下划线，以表示该方法视同为私有方法。

You’ll invoke onSearchPressed() and initiate the query when the ‘Go’ button is pressed.
Finally, add the following utility function just above the SearchPage class declaration:

当Go按钮被点击， onSearchPressed() 即被调用。
然后，在 SearchPage 类声明之前，声明如下实用函数：

```
function urlForQueryAndPage(key, value, pageNumber) {
  var data = {
      country: 'uk',
      pretty: '1',
      encoding: 'json',
      listing_type: 'buy',
      action: 'search_listings',
      page: pageNumber
  };
  data[key] = value;
 
  var querystring = Object.keys(data)
    .map(key => key + '=' + encodeURIComponent(data[key]))
    .join('&');
 
  return 'http://api.nestoria.co.uk/api?' + querystring;
};
```

This function doesn’t depend on SearchPage, so it’s implemented as a free function rather than a method. It first creates the query string based on the parameters in data. Following this, it transforms the data into the required string format, name=value pairs separated by ampersands. The => syntax is an arrow function, another recent addition to the JavaScript language that provides a succinct syntax for creating anonymous functions.

这个函数不依赖SearchPage类，因此被定义为函数而不是方法。它首先将key\value参数以键值对形式放到了data集合中，然后将data集合转换成以&符分隔的“键=值”形式。=>语法是箭头函数的写法，一种创建匿名函数简洁写法，具体请参考[recent addition to the JavaScript language](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions) 。

Head back to the simulator, press Cmd+R to reload the application and tap the ‘Go’ button. You’ll see the activity indicator spin; take a look at the Xcode console to see what it’s telling you:

回到模拟器，按下 Cmd+R 重启App，然后点击‘Go’ 按钮。你将看到网络指示器开始转动。同时控制台将输出：

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/SearchAcitivityIndicator.png" width="500"/>

The activity indicator renders and the URL for the required query appears in the log. Copy and paste that URL into your browser to see the result. You’ll see a massive JSON object. Don’t worry — you don’t need to understand that! You’ll add code to parse that now.

网络指示器显示，同时要请求的URL也打印出来了。拷贝并粘贴URL到Safari，查看搜索结果。你将看到一堆JSON对象。我们将用代码解析这些JSON对象。

> Note: This app makes use of the Nestoria API for searching property listings. The JSON response coming back from the API is pretty straightforward, but you can have a look at the documentation for all  the details on the expected request URL and response formats. The next step is to make the request from within your application.
> 
> 注意: 这个App使用 [Nestoria API 来查找房子](http://www.nestoria.co.uk/help/api)。查找结果以JSON格式返回。官方文档中列出了所有请求的URL规范及响应格式。

##Performing an API Request
##发送请求
Still within SearchPage.js, update the initial state in the class constructor to add a message variable:

打开 SearchPage.js，在初始化状态过程中增加一个message属性：

```
this.state = {
  searchString: 'london',
  isLoading: false,
  message: ''
};
```

Within render, add the following to the bottom of your UI:

在render方法中，在UI元素的最后加入：

```
<Text style={styles.description}>{this.state.message}</Text>
```

You’ll use this to display a range of messages to the user.

这个Text用于向用户显示一些文本。

Within the SearchPage class, add the following code to the end of _executeQuery():

在 SearchPage 类中，在 _executeQuery()方法最后加入:

```
fetch(query)
  .then(response => response.json())
  .then(json => this._handleResponse(json.response))
  .catch(error => 
     this.setState({
      isLoading: false,
      message: 'Something bad happened ' + error
   }));
```

This makes use of the fetch function, which is part of the Web API, and provides a vastly improved API versus XMLHttpRequest. The asynchronous response is returned as a promise, with the success path parsing the JSON and supplying it to a method which you are going to add next.

fetch 函数在 [Fetch API](https://fetch.spec.whatwg.org/)中定义，这个新的JavaScript规范被Firefox 39（Nightly版）以及Chrome 42（开发版）支持，它在XMLHttpRequest的基础上进行了极大的改进。结果是异步返回的，同时使用了 [promise](http://www.html5rocks.com/en/tutorials/es6/promises/)规范，如果response中包含有效的JSON对象则将JSON对象的response成员（另一个JSON）传到_handleResponse方法（后面实现）。

The final step is to add the following function to SearchPage:

然后在 SearchPage类中增加方法：

```
_handleResponse(response) {
  this.setState({ isLoading: false , message: '' });
  if (response.application_response_code.substr(0, 1) === '1') {
    console.log('Properties found: ' + response.listings.length);
  } else {
    this.setState({ message: 'Location not recognized; please try again.'});
  }
}
```

This clears isLoading and logs the number of properties found if the query was successful.

如果查询结果成功返回，我们重置 isLoading 属性为false，然后打印结果集的总行数。

> Note: Nestoria has a number of non-1** response codes that are  potentially useful. For example, 202 and 200 return a list of  best-guess locations. When you’ve finished building your app, why not  try handling these and present a list of options to the user?
> 
> 注意: Nestoria 有 不以1开头的响应码， 这些代码都非常有用。例如202 和  200表示返回一个推荐位置的列表。当完成这个实例后，你可以尝试处理这些返回码，并将列表提供给用户选择。

 Save your work, then in the simulator press Cmd+R and try searching for london’; you should see a log message saying that 20 properties were  found. Next try a non-existent location, such as ‘narnia’ (*sniff*),  and you’ll be greeted by the following message:

保存，返回模拟器，按下Cmd+R ，然后搜索 ‘london’你将在控制台看到一条消息，表示搜索到20条房子信息。尝试输入一个不存在的地名，比如 ‘narnia’ 你将看到如下信息：

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2015/03/react-narnia.png" width="281"/>

It’s time to see what those 20 properties in real places such as London look like!

接下来我们在伦敦或者别的什么城市搜索20座房子。

##Displaying the Results
##显示搜索结果
Create a new file SearchResults.js, and add the following:

新建一个文件： SearchResults.js, 编写如下代码：

```
'use strict';
 
var React = require('react-native');
var {
  StyleSheet,
  Image, 
  View,
  TouchableHighlight,
  ListView,
  Text,
  Component
} = React;
```

Yes, that’s right, it’s a require statement that includes the react-native module, and a destructuring assignment. You’ve been paying attention haven’t you? :]

你注意到了吗？一切都是老样子，一条requires语句和一个结构赋值。

Next add the component itself:

然后定义一个Componet子类：

```
class SearchResults extends Component {
 
  constructor(props) {
    super(props);
    var dataSource = new ListView.DataSource(
      {rowHasChanged: (r1, r2) => r1.guid !== r2.guid});
    this.state = {
      dataSource: dataSource.cloneWithRows(this.props.listings)
    };
  }
 
  renderRow(rowData, sectionID, rowID) {
    return (
      <TouchableHighlight
          underlayColor='#dddddd'>
        <View>
          <Text>{rowData.title}</Text>
        </View>
      </TouchableHighlight>
    );
  }
 
  render() {
    return (
      <ListView
        dataSource={this.state.dataSource}
        renderRow={this.renderRow.bind(this)}/>
    );
  }
 
}
```

The above code makes use of a more specialized component — ListView — which displays rows of data within a scrolling container, similar to UITableView. You supply data to the ListView via a ListView.DataSource, and a function that supplies the UI for each row.

上述代码中使用了一个专门的组件——ListView ——该组件非常像ITableView。通过ListView.DataSource, 我们可以向ListView提供数据。renderRow函数则用于为每个行提供UI。

When constructing the data source, you provide a function that compares the identity of a pair of rows. The ListView uses this during the reconciliation process, in order to determine the changes in the list data. In this instance, the properties returned by the Nestoria API have a guid property, which is a suitable check for this purpose.

在构建数据源的时候，我们使用箭头函数对不同的行进行识别。这个函数在ListView进行“一致化”的时候被调用，以便判断列表中的数据是否被改变。在本例中，Nestoria API有一个guid属性，刚好可以用来作为判断的标准。

Now add the module export to the end of the file:

最后，加入一条模块输出语句：

```
module.exports = SearchResults;
```

Add the following to SearchPage.js near the top of the file, underneath the require call for React:

在SearchPage.js 头部，require 下方加入:

```
var SearchResults = require('./SearchResults');
```

This allows us to use the newly added SearchResults class from within the SearchPage class.
Modify the current _handleResponse method by replacing the console.log statement with the following:

这样我们就可以 SearchPage 类中使用SearchResults类了。
在_handleResponse 方法，将console.log 一句替换为:

```
this.props.navigator.push({
  title: 'Results',
  component: SearchResults,
  passProps: {listings: response.listings}
});
```

The above code navigates to your newly added SearchResults component and passes in the listings from the API request. Using the push method ensures the search results are pushed onto the navigation stack, which means you’ll get a ‘Back’ button to return to the root.

上述代码将导航至SearchResults 页面，并将请求到的列表数据传递给它。Push方法可以将页面添加到导航控制器的ViewController堆栈中，同时你的导航栏上将出现一个Back按钮，点击它可以返回到上一页面。

Head back to the simulator, press Cmd+R and try a quick search. You’ll be greeted by a list of properties:

回到模拟器, 按下Cmd+R ，进行一个查找动作。你将看到搜索结果如下：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/react-searchresults1.png" width="281"/>

好了，房子清单已经列出来了，不过列表有一点丑陋。接下来我们会让它变得漂亮一点。
##A Touch of Style
##表格样式
This React Native code should be starting to look familiar by now, so this tutorial is going to pick up the pace.

现在，React Native的代码对我们来说已经不陌生了，接下来我们的教程可以稍微加快一点节奏了。

Add the following style definition just after the destructuring assignment in SearchResults.js:

在 SearchResults.js文件的解构赋值语句之后，添加样式定义：

```
var styles = StyleSheet.create({
  thumb: {
    width: 80,
    height: 80,
    marginRight: 10
  },
  textContainer: {
    flex: 1
  },
  separator: {
    height: 1,
    backgroundColor: '#dddddd'
  },
  price: {
    fontSize: 25,
    fontWeight: 'bold',
    color: '#48BBEC'
  },
  title: {
    fontSize: 20,
    color: '#656565'
  },
  rowContainer: {
    flexDirection: 'row',
    padding: 10
  }
});
```

This defines all the styles that you are going to use to render each row.

这些代码中的样式将在渲染单元格时用到。

Next replace renderRow() with the following:

修改renderRow() 方法如下:

```
renderRow(rowData, sectionID, rowID) {
  var price = rowData.price_formatted.split(' ')[0];
 
  return (
    <TouchableHighlight onPress={() => this.rowPressed(rowData.guid)}
        underlayColor='#dddddd'>
      <View>
        <View style={styles.rowContainer}>
          <Image style={styles.thumb} source={{ uri: rowData.img_url }} />
          <View  style={styles.textContainer}>
            <Text style={styles.price}>£{price}</Text>
            <Text style={styles.title} 
                  numberOfLines={1}>{rowData.title}</Text>
          </View>
        </View>
        <View style={styles.separator}/>
      </View>
    </TouchableHighlight>
  );
}
```

This manipulates the returned price, which is in the format ‘300,000 GBP’, to remove the GBP suffix. Then it renders the row UI using techniques that you are by now quite familiar with. This time, the data for the thumbnail image is supplied via a URL, and React Native takes care of decoding this off the main thread.

其中价格将以‘300,000 GBP’的格式显示，记得将GBP 后缀删除。上述代码用你已经很熟悉的方式来渲染单元格UI。缩略图以URL方式提供，React Native 自动将其解码（主线程中）。

Also, note the use of an arrow function in the onPress property of the TouchableHighlight component; this is used to capture the guid for the row.

在TouchableHightlight组件的onPress属性中再次使用了箭头函数，并将该行数据的guid作为传递的参数。

The final step is to add this method to the class to handle the press:

最后一个方法，用于处理点击事件

```
rowPressed(propertyGuid) {
  var property = this.props.listings.filter(prop => prop.guid === propertyGuid)[0];
}
```

This method locates the property that was tapped by the user. It doesn’t do anything with it yet, but you’ll fix that shortly. But right now, it’s time to admire your handiwork.
Head back to the simulator, press Cmd+R and check out your results:

这里，当用户点击某行时，通过guid去房产列表中找到对应的房屋信息。
回到模拟器，按下 Cmd+R ，观察运行结果：

<img src="http://cdn4.raywenderlich.com/wp-content/uploads/2015/03/react-searchresults2.png" width="281"/>

这下看起来好多了——只不过，那些住在London的人居然住得起这么贵房子？真是令人难以置信！

Time to add the final view to the application.

接下来，我们就来实现App的最后一个界面了。
##Property Details View
##查看房屋详情

Add a new file PropertyView.js to the project, then add the following to the top of the file:

新建一个 PropertyView.js 文件到项目中，编辑如下内容：

```
'use strict';
 
var React = require('react-native');
var {
  StyleSheet,
  Image, 
  View,
  Text,
  Component
} = React;
```

Surely you can do this in your sleep by now! :]

确保进行到这一步的时候，你还没有睡着！:]

Next add the following styles:

继续添加如下样式：

```
var styles = StyleSheet.create({
  container: {
    marginTop: 65
  },
  heading: {
    backgroundColor: '#F8F8F8',
  },
  separator: {
    height: 1,
    backgroundColor: '#DDDDDD'
  },
  image: {
    width: 400,
    height: 300
  },
  price: {
    fontSize: 25,
    fontWeight: 'bold',
    margin: 5,
    color: '#48BBEC'
  },
  title: {
    fontSize: 20,
    margin: 5,
    color: '#656565'
  },
  description: {
    fontSize: 18,
    margin: 5,
    color: '#656565'
  }
});
```

Then add the component itself:

然后将组件加入视图：

```
class PropertyView extends Component {
 
  render() {
    var property = this.props.property;
    var stats = property.bedroom_number + ' bed ' + property.property_type;
    if (property.bathroom_number) {
      stats += ', ' + property.bathroom_number + ' ' + (property.bathroom_number > 1
        ? 'bathrooms' : 'bathroom');
    }
 
    var price = property.price_formatted.split(' ')[0];
 
    return (
      <View style={styles.container}>
        <Image style={styles.image} 
            source={{uri: property.img_url}} />
        <View style={styles.heading}>
          <Text style={styles.price}>£{price}</Text>
          <Text style={styles.title}>{property.title}</Text>
          <View style={styles.separator}/>
        </View>
        <Text style={styles.description}>{stats}</Text>
        <Text style={styles.description}>{property.summary}</Text>
      </View>
    );
  }
}
```

The first part of render() performs some manipulation on the data. As is often the case, the data returned by the API is of mixed quality and often has missing fields. This code applies some simple logic to make the data a bit more presentable.

render() 方法的第一步，是封装数据。因为从API获得的数据经常不太规范而且某些字段不全。代码采用简单手段让数据变得更便于展示一些。

The rest of render is quite straightforward; it’s simply a function of the immutable state of this component.

剩下来的事情就非常简单了，填充组件的状态到UI上。

Finally add the following export to the end of the file:

在文件最后加入export语句：

```
module.exports = PropertyView;
```

Head back to SearchResults.js and add the require statement to the top of the file, just underneath the React require line:

回到SearchResults.js 在文件头部加入 require 语句：

```
var PropertyView = require('./PropertyView');
```

Next update rowPressed() to navigate to your newly added PropertyView:

修改 rowPressed() 方法，调用 PropertyView类：

```
rowPressed(propertyGuid) {
  var property = this.props.listings.filter(prop => prop.guid === propertyGuid)[0];
 
  this.props.navigator.push({
    title: "Property",
    component: PropertyView,
    passProps: {property: property}
  });
}
```

You know the drill: head back to the Simulator, press Cmd+R, and go all the way to the property details by running a search and tapping on a row:

老规矩：返回模拟器，按下 Cmd+R, 点击搜索结果列表中的某行：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/react-property-281x500.png" width="281"/>

Affordable living at it’s finest — that’s a fancy looking pad!

能住得起的房子才是最好的房子——在Pad上看到的这个房子确实很有吸引力！

Your app is almost complete; the final step is to allow users to search for nearby properties.

你的App快接近完成了，最后一步是让用户能够查找距离他们最近的房子。
##Geolocation Search
##根据位置查找
Within Xcode, open Info.plist and add a new key, by right clicking inside the editor and selecting Add Row. Use NSLocationWhenInUseUsageDescription as the key name and use the following value:

在Xcode中打开 Info.plist ，右键，Add Row，增加一个key。 使用NSLocationWhenInUseUsageDescription 作为键名，使用
下列字符串作为键值：

> PropertyFinder would like to use your location to find nearby
> properties

Here’s how your plist file will look once you’ve added the new key:
添加完新值后，你的Plist文件将如下图所示：

<img src="http://cdn3.raywenderlich.com/wp-content/uploads/2015/03/Screen-Shot-2015-03-20-at-21.49.06-480x162.png" width="500"/>

This key details the prompt that you’ll present to the to the user to request access to their current location.

这将在询问用户是否允许App使用他们的当前位置时，以这串文本作为提示信息。

Open SearchPage.js, locate the TouchableHighlight that renders the ‘Location’ button and add the following property value:

打开 SearchPage.js, 找到TouchableHighlight 中渲染 ‘Location’ 按钮的代码，加入下列属性值：

```
onPress={this.onLocationPressed.bind(this)}
```

When you tap the button, you ‘ll invoke onLocationPressed — you’re going to add that next.

这样，当点击Location按钮，会调用 onLocationPressed 方法。

Add the following within the body of the SearchPage class:

在SearchPage 类中，增加方法：

```
onLocationPressed() {
  navigator.geolocation.getCurrentPosition(
    location => {
      var search = location.coords.latitude + ',' + location.coords.longitude;
      this.setState({ searchString: search });
      var query = urlForQueryAndPage('centre_point', search, 1);
      this._executeQuery(query);
    },
    error => {
      this.setState({
        message: 'There was a problem with obtaining your location: ' + error
      });
    });
}
```

The current position is retrieved via navigator.geolocation; this is an interface defined by the Web API, so it should be familiar to anyone who has used location services within the browser. The React Native framework provides its own implementation of this API using the native iOS location services.

navigator.geolocation可获取当前位置。这个方法定义在 Web API中，这对于曾经在浏览器中使用过位置服务的人来说并不陌生。React Native 框架用本地iOS服务重新实现了这个API。

If the current position is successfully obtained, you invoke the first arrow function; this sends a query to Nestoria. If something goes wrong, you’ll display a basic error message instead.

如果当前位置获取成功，我们将调用第一个箭头函数，否则调用第二个箭头函数简单显示一下错误信息。

Since you’ve made a change to the plist, you’ll need to relaunch the app to see your changes. No Cmd+R this time — sorry. Stop the app in Xcode, and build and run your project.

因为我们修改了plist文件，因此我们需要重新启动App，而不能仅仅是Cmd+R了。请在Xcode中终止App，然后重新编译运行App。

Before you use the location-based search, you need to specify a location that is covered by the Nestoria database. From the simulator menu, select Debug\Location\Custom Location … and enter a latitude of 55.02 and a longitude of -1.42, the coordinates of a rather nice seaside town in the North of England that I like to call home!

在使用基于地理定位的搜索之前，我们需要指定一个Nestoria数据库中的默认位置。在模拟器菜单中，选择Debug\Location\Custom Location … 然后输入 55.02的纬度和-1.42的经度。这是一个位于英格兰北部的非常优美的海边小镇，我的家。

<img src="http://cdn1.raywenderlich.com/wp-content/uploads/2015/03/WhitleyBaySearch-647x500.png" width="500"/>

它远没有伦敦那么繁华——但住起来真的很便宜！:]
##Where To Go From Here?
##接下来做什么
Congratulations on completing your first React Native application! You can find the complete project on GitHub with a commit for each ‘run’ step in the tutorial, very useful if you get lost along the way :]

恭喜你，你的第一个React Native App终于完成了！你可以在GitHub上找到每一个”可运行的“步骤的项目源文件，如果你搞不定的时候它们会非常有用的 :]

If you come from the web world, you can see how easy it is to define your interface and navigation with JavaScript and React to get a fully native UI from your code. If you work mainly on native apps, I hope you’ve gained a feel for the benefits of React Native: fast app iteration, modern JavaScript and clear style rules with CSS.

如果你来自Web领域，你可能觉得在代码中用JS和React框架建立基于本地化UI的App的界面并实现导航不过是小菜一碟。但如果你主要开发的是本地App，我希望你能从中感受到React Native的优点：快速的App迭代，现代JavaScript语法的支持和清晰的CSS样式规则。

Perhaps you might write your next app using this framework? Or then again, perhaps you will stick with Swift or Objective-C? 

在你的下一个App中，你是会使用这个框架，还是会继续顽固不化地使用Swift和O-C呢？

Whichever path you take, I hope you have learned something new and interesting in this article, and can carry some of the principles forward into your next project.

无论你怎么选择，我都希望你能从本文的介绍中学习到一些有趣的新东西，并把其中一些原理应用到你的下一个项目中。

If you have any questions or comments on this tutorial, feel free to join the discussion in the forums below!

如果你有任何问题及建议，请参与到下面的讨论中来！





