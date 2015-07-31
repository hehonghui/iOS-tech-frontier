如何做一个iOS分形App
---

> * 原文链接 : [如何做一个iOS分形App](https://www.weheartswift.com/make-ios-fractal-app/)
* 原文作者 : [Silviu Pop](https://www.weheartswift.com/author/tspop/)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [alier1226](https://github.com/alier1226) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  校对中

##Introduction
## 介绍

In this tutorial we’ll be making an iOS app that renders the Mandelbrot Set and allows us to pan and zoom around it exposing beautiful and complex imagery.
The end result will look like this:

在这个教程中，我们会做一个可以渲染Mandelbrot Set的应用程序，我们可以缩放和平铺它来看分形那令人惊叹的复杂之美。最终的结果如下：


Shader code

着色程序的代码

```
void main() {
	#define iterations 128
    vec2 position = v_tex_coord; // gets the location of the current pixel in the intervals [0..1] [0..1]
    vec3 color = vec3(0.0,0.0,0.0); // initialize color to black

    vec2 z = position; // z.x is the real component z.y is the imaginary component


    // Rescale the position to the intervals [-2,1] [-1,1]
    z *= vec2(3.0,2.0);
    z -= vec2(2.0,1.0);

    vec2 c = z;

    float it = 0.0; // Keep track of what iteration we reached
    for (int i = 0;i < iterations; ++i) {
        // zn = zn-1 ^ 2 + c

        // (x + yi) ^ 2 = x ^ 2 - y ^ 2 + 2xyi
        z = vec2(z.x * z.x - z.y * z.y, 2.0 * z.x * z.y);
        z += c;

        if (dot(z,z) > 4.0) { // dot(z,z) == length(z) ^ 2 only faster to compute
            break;
        }

        it += 1.0;
    }

    if (it < float(iterations)) {
        color.x = sin(it / 3.0);
        color.y = cos(it / 6.0);
        color.z = cos(it / 12.0 + 3.14 / 4.0);
    }

    gl_FragColor = vec4(color,1.0);
}
```

Download the Starter Project to follow along with this tutorial. You can find the final code at the end of the article.

你可以下载起始版本跟着教程一起做，也可以在本文结尾找到最终版本的代码。

##Project setup
##项目设置

The `GameScene.sks` file contains a single sprite named `fractal` that fills the full scene with the shader `Fractal.fsh` attached to it.

`Gamescene.sks`文件里包含一个名为 `fractal` 的子画面，它填充了整个界面并且着色程序程序 `Fractal.fsh`也附在它上。

`Fractal.fsh` contains the shader code from above

`Fractal.fsh`包含了上面着色程序的代码

`GameViewController.swift` contains code for setting up the game scene.

`GameViewController.swift`包含了设置游戏场景的代码

`GameScene.swift` is empty

`GameScene.swift` 为空

If you run the code right now you’ll get the following result:

如果你现在运行代码，你将会得到如下的结果：

![Starter](https://camo.githubusercontent.com/c8da0f4c7cc439957d21dc20efb330245faf2d92/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30362f7374617274696e672d696d6167652e706e67)
![起始](https://camo.githubusercontent.com/c8da0f4c7cc439957d21dc20efb330245faf2d92/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30362f7374617274696e672d696d6167652e706e67)

Notice that the aspect ratio is fixed at 3/2. We’ll have to make it adjust based on the screen size.

请注意纵横比固定为3/2，我们需要先根据屏幕大小调节它。

Also, the image is static and you can’t interact with it in any way.

并且由于画面是静态的，所以你不可能与它有任何方式的交互。

##Setting up the view
##设置界面

We’ll be using a transparent scrollview for handling panning and zooming. The scrollview will automatically keep track of our position and our zoom level in the fractal.

我们将用一个透明的scrollview来处理平铺缩放。scrollview将自动跟踪我们的位置以及我们在分形中的缩放程度。

Open the ｀Main.storyboard｀ file and drag a scrollview into the view. Make the scrollview fill the view and add constraints for width, height leading space and bottom space.

打开｀Main.storyboard｀文件，拖进去一个scrollview。将scrollview设置成fill the view，并对它的宽度，到顶部距离，到底部距离设置限制。

Set the scrollview’s max zoom level to 100000. We’ll be able to magnify the fractal up to one hundred thousand times! We can’t go any higher than that with this approach because we’ll reach the precision limit of ｀float｀.

将scrollview的最大缩放程度设置为100000，意味着我们将可以把分享放大到**十万倍**！我们不能再放大更多了因为已经接近了｀float｀类型的准确极限。

Drag a view into the scrollview. This view will be used for zooming the scrollview. The view itself will not display anything, we’ll use the contentOffset and zoom properties of the scrollView to update our shader. Make sure that the view fills the scrollView and you set constraints for width, height leading space, trailing space, top space and bottom space. Also set the view’s background color to Clear Color.

拖一个view（画面）到scrollview里，它将用作处理缩放。这个view本身不会展示任何东西，我们将用到它的`contentOffset`和scrollView的`zoom`属性来更新我们的着色程序。要确保这个画面可以填满scrollView，并且设定好宽度,到顶部底部左右距离的限制。将画面的背景色设置为 Clear Color （透明色）。

Next we’ll hook up the outlets we need and the scrollView’s delegate.

接下来我们将连接我们所需要的outlet和scrollView的代理。

Drag outlets for the scrollView and the scrollView’s contentView.

给scrollView和scrollView的contentView拖进outlet。

```
class GameViewController: UIViewController, UIScrollViewDelegate  {

    @IBOutlet weak var contentView: UIView!
    @IBOutlet weak var scrollView: UIScrollView!
    ...
}
```

Next well stub out our delegate methods and implement the viewForZoomingInScrollView(scrollView: UIScrollView) -> UIView? method.

接下来我们去掉代理方法，并且实现 `viewForZoomingInScrollView(scrollView: UIScrollView) -> UIView? ` 这个方法

```
class GameViewController: UIViewController, UIScrollViewDelegate  {

    ...

    func scrollViewDidScroll(scrollView: UIScrollView) {

    }

    func scrollViewDidZoom(scrollView: UIScrollView) {

    }

    func viewForZoomingInScrollView(scrollView: UIScrollView) -> UIView? {
        return contentView
    }

    ...
}
```

##Sending data to the shader
##向着色程序发送数据

A fragment shader can receive data from your Swift code via uniform variables. Uniform variables can be declared via the SpriteKit Editor. Let’s define some uniforms.

着色程序可以从你的swift代码里的uniform变量里获得数据。uniform变量可以在SpriteKit编辑器里声明。那现在我们来声明一下uniform变量。


Open the `GameScene.sks` file and select the mandelbrot sprite. Scroll to the bottom of the inspector on the right and under “Custom Shader Uniforms” add 2 new items `zoom` of type `float` and value `1` and `offset` of type `vec2`. We’ll populate this uniforms with the scrollViews contentOffset and zoom properties.

打开 `GameScene.sks`	文件，选择 mandelbrote sprite。将insepctor拖到底部，在“Custom shader Uniforms”里添加两项：`float` 类型的 `zoom` ，值为`1`， 以及 `vec2`类型的`offset`。我们将用这两项uniform变量储存scrollView的 `contentOffset` 以及 `zoom` 属性。

![Uniforms](https://camo.githubusercontent.com/91bd7f6d345fa6e57647a466eb3167c09a037c4f/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f756e69666f726d732e706e67)

**WARNING**: Xcode 6.3 has a bug with uniform variables. Uniforms won’t be initialized with the values you provide in the editor. You’ll have to initialize them from code.

**警告**：Xcode 6.3的uniform变量有bug。它不能直接在编辑器里赋值初始化，你必须在代码里初始化它们。

We can access the shader on our node via the shader property. To get a uniform from the shader we use `theuniformeNamed()` method. For example to retrieve our zoom uniform we use:

我们可以通过shader属性来获取节点上(node)着色程序，用 `theuniformedName()`方法来从着色程序得到uniform变量。以下是我们获取zoom uniform变量的例子：

```
let zoomUniform = node.shader!.uniformNamed("zoom")!
```

Once we have a uniform we can change its value via one of of the properties

当我们有了uniform变量后，我们可以通过它的属性来改变它的值。

```
var textureValue: SKTexture!
var floatValue: Float
var floatVector2Value: GLKVector2
var floatVector3Value: GLKVector3
var floatVector4Value: GLKVector4
var floatMatrix2Value: GLKMatrix2
var floatMatrix3Value: GLKMatrix3
var floatMatrix4Value: GLKMatrix4
```

We’re only interested in using `floatValue` and `floatVector2Value` for this tutorial.

在本教程里，我们只对 `floatValue`和`floatVector2Value`感兴趣。

Ex: to set the zoom to 2 we use

例子：将zoom的值设置成2

```
zoomUniform.floatValue = 2
```

##Coordinate systems and mapping intervals
## 坐标系以及映射出区间

We’ll have to map between different coordinate systems while maintaining ratio. We’ll use this to convert the coordinates of our scrollview to the complex plane.

我们将在保持比例的基础上映射不同的坐标系。我们将用这个来转化scrollview的坐标到复平面。

Lets first look at the 1D case:

让我们先看一下一维的情况：

To map a value x from the interval [0,a] to the interval [0,1] we just divide by the length of the interval `x' = x / a`.

将x从区间[0,a]映射到区间[0,1]，我们只需要除以区间长度 `x' = x / a`。

To map a value x from the interval [0,1] to the interval [a,b] we multiply the value by the length of the interval and add the starting value of the interval to it `x' = x * (b - a) + a`

将x从区间[0,1]映射到区间[a,b]，我们可以乘上区间长度，然后再加上区间起始值，`x' = x * (b - a) + a`。

For example consider the x coordinate of an iPhone 4 in landscape. The x coordinate will be between 0 and 480. To map a value `x` to `[0,1]` we use `x' = x / 480`. To map `x'` from `[0,1]` to `[-2,2]` we use `x'' = x' * 4 - 2`

举个例子，比如iPhone4的x坐标，x坐标为0到480之间。映射 `x` 到`[0，1]`, 我们用 `x' = x / 480`。映射`x'` 从 `[0,1]`  到`[-2,2]`，我们用 `x'' = x' * 4 - 2`

If we have a point with x coordinate 120 on our screen the corresponding point in the interval `[0,1]` will be `120 / 480 = 0.25` and in the interval `[-2,2]` it will be `0.25 * 4 - 2 = -1` as seen below.

如果我们屏幕上有一点x，坐标值为120，那么对应到区间`[0,1]` 将成为 `120 / 480 = 0.25`，以及在区间 `[-2,2]`，如下所见它将成为 `0.25 * 4 - 2 = -1`。

![Intervals](https://camo.githubusercontent.com/f13898a25d2805f99f514540477dcb091c3d485c/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f696e74657276616c73322e706e67)


##Mapping between the scrollview and the complex plane

##scrollView及复平面互相映射

We’ll have to convert points on our scrollView to points in the complex plane. The first step is to convert points from the scrollView to points in the range `[0,1]`. To do this we need to divide the `contentOffset` by the `contentSize`. This will bring our `contentOffset` to the range `[0,1]`.


我们需要讲scrollView上的点转换到复平面。第一步，先将scrollView上的点转换到区间`[0,1]`。通过将`contentOffset` 除以`contentSize`可以将 `contentOffset`转换到区间`[0,1]`。

```
var offset = scrollView.contentOffset

offset.x /= scrollView.contentSize.width
offset.y /= scrollView.contentSize.height
```

Our fragment shader provides points in the range `[0,1]` for both x and y coordinates which we’ll have to map to points that are in the interior of the scrollView’s contentView.

我们着色程序x，y坐标都有点在区间`[0,1]`，所以我们要在scrollView的contentView里映射出这些店。

The normalized size of our contentView is `1.0 / zoom` so the normalized coordinates of points in the contentView will be in the interval `[contentOffset / contentSize,contentOffset / contentSize + 1.0 / zoom]`

标准化过的contentView为 `1.0 / zoom` ，所以contentView里标准化过的点坐标讲在区间`[contentOffset / contentSize,contentOffset / contentSize + 1.0 / zoom]`。

One more thing that we have to keep in mind is that the y-Axis points upwards in GLSL and the point (0,0) is in the **bottom left** corner. We’ll have to flip the y-Axis so that it matches our scrollView.

还有我们必须牢记的是，y轴的点在GLSL上，而点(0,0)在**左下**角，所以我们必须翻转y轴来对应我们的scrollView。

The following GLSL code converts the position to a point in the scrollView’s contentView:

下面的GLSL代码转换scrollView的contentView里点的位置。

```
// Fractal.fsh
void main {

    vec2 position = v_tex_coord;
    position.y = 1.0 - position.y; // flip y coordinate

    vec2 z = offset + position / zoom;

...
}
```

Below you can see in blue the frame of our scrollView’s contentView in both non-normalized and normalized coordinates.`contentSize = (960,640)` `contentOffset = (240,160)`, and `zoom = 2.0`

如下你可以看见蓝色的scrollView的contentView在标准化与未标准化过的边框。`contentSize = (960,640)`，`contentOffset = (240,160)`，`zoom = 2.0`


ScrollView

ScrollView
![ScrollView](https://camo.githubusercontent.com/966e51af3a589644eb963c62a96db52a0bae3eb1/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f7363726f6c6c56696577322e706e67)

Normalized ScrollView

标准化过的ScrollView

![Normalized ScrollView](https://camo.githubusercontent.com/9a902b0499599ba09bb57d7afc1702a1c4b17c7d/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f6e6f726d616c697a65645363726f6c6c566965772e706e67)

Finally we have to map our point to the complex plane. To get a good look at the mandelbrot set we’ll want to map to the region `[-1.5,0.5] x [-1,1]` in the complex plane.

最后我们将点映射到复平面。为了在mandelbrot里得到好看的效果，我们将希望映射区域`[-1.5,0.5] x [-1,1]`复平面。

We’ll also want to correct for aspectRatio. Currently Both our x and y coordinate have the same scale. We’ll want to scale our x coordinate by the aspect ratio so that the image isn’t distorted.

我们还想使纵横比正确。现在我们的x、y轴的比例一样，我们要乘以x和纵横比使得图片不会变形。

What is Aspect Ratio

纵横比是什么

Aspect ratio is the ratio of a screen’s width to its height.

纵横比是屏幕宽度和高度的比例。

```
// Fractal.fsh

void main {
...
    z *= 2.0;
    z -= vec2(1.5,1.0);

    float aspectRatio = u_sprite_size.x / u_sprite_size.y;
    z.x *= aspectRatio;
...
}
```

Below you can see our scrollView’s contentView mapped to the complex plane and corrected for aspect ratio.

下面你可以看到我们scrollView的contentView映射到的平复面以及纠正过纵横比的结果。

![Complex Plane](https://camo.githubusercontent.com/565c2123454cfacc4ad421a29a36e263ed32e440/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f636f6d706c6578506c616e65322e706e67)

To integrate all the above code we’ll create a new method updateShader that will pass the coordinates of our contentView to the shader. We’ll need to call the updateShader method in the scrollview’s delegate methods.

为了整合上面所有代码，我们建了一个新的方法叫updateShader,它可以传一个contentView坐标到着色程序。我们所需要做的就是在scrollView的代理方法里调用updateShader方法。

```
class GameViewController: UIViewController, UIScrollViewDelegate  {

...

    func updateShader(scrollView: UIScrollView) {
        let zoomUniform = node.shader!.uniformNamed("zoom")!

        let offsetUniform = node.shader!.uniformNamed("offset")!

        var offset = scrollView.contentOffset

        offset.x /= scrollView.contentSize.width
        offset.y /= scrollView.contentSize.height

        zoomUniform.floatValue = Float(scrollView.zoomScale)
        offsetUniform.floatVector2Value = GLKVector2Make(Float(offset.x), Float(offset.y))
    }

    func scrollViewDidScroll(scrollView: UIScrollView) {
        updateShader(scrollView)
    }

    func scrollViewDidZoom(scrollView: UIScrollView) {
        updateShader(scrollView)
    }
...
}
```

Also don’t forget to call the updateShader method when the view appears so that you initialize the uniforms.

同时也别忘了当view出现时调用updateShader方法，这样你才可以初始化uniform变量。

```
class ViewController {
...
    override func viewDidAppear(animated: Bool) {
        super.viewDidAppear(animated)

        updateShader(scrollView)
    }
...
}
```

The shader code will finally look like this:

最终着色程序的如下所示：

```
void main() {
#define iterations 128

    vec2 position = v_tex_coord; // gets the location of the current pixel in the intervals [0..1] [0..1]

    position.y = 1.0 - position.y;

    vec2 z = offset + position / zoom;

    z *= 2.0;
    z -= vec2(1.5,1.0);

    float aspectRatio = u_sprite_size.x / u_sprite_size.y;
    z.x *= aspectRatio;

    vec2 c = z;

    float it = 0.0; // Keep track of what iteration we reached
    for (int i = 0;i < iterations; ++i) {
        // zn = zn-1 ^ 2 + c

        // (x + yi) ^ 2 = x ^ 2 - y ^ 2 + 2xyi
        z = vec2(z.x * z.x - z.y * z.y, 2.0 * z.x * z.y);
        z += c;

        if (dot(z,z) > 4.0) { // dot(z,z) == length(z) ^ 2 only faster to compute
            break;
        }

        it += 1.0;
    }

    vec3 color = vec3(0.0,0.0,0.0); // initialize color to black

    if (it < float(iterations)) {
        color.x = sin(it / 3.0);
        color.y = cos(it / 6.0);
        color.z = cos(it / 12.0 + 3.14 / 4.0);
    }

    gl_FragColor = vec4(color,1.0);
}
```

[Complete Source Code](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Complete.zip)

[完整代码](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Complete.zip)


##Challenges

##挑战

1 . Optimization

1 . 优化

The black areas of the set are the slowest to render. Luckily we can quickly determine if a point is in one of 2 large black regions (the cardioid or the region 2) as seen in the image below. Here you can find formulas for determining if a point is within one of these 2 regions. Improve the fragment shader by adding code that only performs the mandelbrot iterations if a point is outside these regions. This will greatly improve the apps performance when these regions are visible.

黑色部分渲染的最慢。幸好根据下图，我们可以很快知道一个点是否在两块黑色部分之一里 （心形部分或者区域2）。[这里](http://en.wikibooks.org/wiki/Fractals/Iterations_in_the_complex_plane/Mandelbrot_set#Cardioid_and_period-2_checking)你可以找到如何判断点是否在两块黑色区域之一里的方法。加上这些代码来改进着色程序，它们只会在点不在这两个区域里执行mandelbrot循环。这将大幅度提高app在这些区域可见时的表现。

The main cardioid can be seen below in red and the region 2 in green.

见下图，主要的心形为红色，区域2为绿色。

![Bulb region 2](https://camo.githubusercontent.com/be10da9e8b6a359e8d5a61bc4bfe94931a5c882a/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30362f63617264696f69642d3262756c622e706e67)

**Hint**

**提示**

Perform the mandelbrot iterations only if the point is outside one of these regions.

只当点在这些区域中的一个以外的时候执行mandelbrot循环。

**Solution**

**答案**

```
void main() {
    #define iterations 128

        vec2 position = v_tex_coord; // gets the location of the current pixel in the intervals [0..1] [0..1]

        position.y = 1.0 - position.y;

        vec2 z = offset + position / zoom;

        z *= 2.0;
        z -= vec2(1.5,1.0);

        float aspectRatio = u_sprite_size.x / u_sprite_size.y;
        z.x *= aspectRatio;

        vec2 c = z;


        bool skipPoint = false;

        //     cardioid checking
        if ((z.x + 1.0) * (z.x + 1.0) + z.y * z.y < 0.0625) {
            skipPoint = true;
        }

        //     period 2 checking
        float q = (z.x - 0.25) * (z.x - 0.25) + z.y * z.y;

        if (q * (q + (z.x - 0.25)) < 0.25 * z.y * z.y) {
            skipPoint = true;
        }

        float it = 0.0; // Keep track of what iteration we reached

        if (!skipPoint) {
            for (int i = 0;i < iterations; ++i) {
                // zn = zn-1 ^ 2 + c

                // (x + yi) ^ 2 = x ^ 2 - y ^ 2 + 2xyi
                z = vec2(z.x * z.x - z.y * z.y, 2.0 * z.x * z.y);
                z += c;

                if (dot(z,z) > 4.0) { // dot(z,z) == length(z) ^ 2 only faster to compute
                    break;
                }

                it += 1.0;
            }
        }

        vec3 color = vec3(0.0,0.0,0.0); // initialize color to black

        if (it < float(iterations) && !skipPoint) {
            color.x = sin(it / 3.0);
            color.y = cos(it / 6.0);
            color.z = cos(it / 12.0 + 3.14 / 4.0);
        }

        gl_FragColor = vec4(color,1.0);
    }
    
  ```
 [Complete Source Code](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-1.zip)
 
  [完整代码](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-1.zip)

2 . Make a similar app that lets you explore the Julia set for some point c.

2 . 做一个类似的app，可以让你探索Julia set 的某点c。

Ex: vec2 c = vec2(-0.76, 0.15);

例子: vec2 c = vec2(-0.76, 0.15);

![Julia](https://camo.githubusercontent.com/29cf99df9aeb340c50ba8c1d7687489bb56ecefe/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30362f6a756c69612e706e67)

**Solution**

**答案**

    void main() {
    #define iterations 128

        vec2 position = v_tex_coord; // gets the location of the current pixel in the intervals [0..1] [0..1]

        position.y = 1.0 - position.y;

        vec2 z = offset + position / zoom;

        z *= 2.0;
        z -= vec2(1.0,1.0);

        float aspectRatio = u_sprite_size.x / u_sprite_size.y;
        z.x *= aspectRatio;

        vec2 c = vec2(-0.76, 0.15);


        float it = 0.0; // Keep track of what iteration we reached

        for (int i = 0;i < iterations; ++i) {
            // zn = zn-1 ^ 2 + c

            // (x + yi) ^ 2 = x ^ 2 - y ^ 2 + 2xyi
            z = vec2(z.x * z.x - z.y * z.y, 2.0 * z.x * z.y);
            z += c;

            if (dot(z,z) > 4.0) { // dot(z,z) == length(z) ^ 2 only faster to compute
                break;
            }

            it += 1.0;
        }

        vec3 color = vec3(0.0,0.0,0.0); // initialize color to black

        if (it < float(iterations)) {
            color.x = sin(it / 3.0);
            color.y = cos(it / 6.0);
            color.z = cos(it / 12.0 + 3.14 / 4.0);
        }

        gl_FragColor = vec4(color,1.0);
    }
    
[Complete Source Code](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-2.zip)
    
[完整代码](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-2.zip)

3 . Add the point c as a uniform allow the user to adjust it’s value by panning around with 2 fingers

3 . 添加一个点c的uniform变量，使用户可以用两个手指改变其值。


**Hint**

**提示**

Use a UIPanGestureRecognizer to detect the 2 finger pan. You’ll have to normalize the translation that the gesture recognizer gives you

用UIPanGestureRecognizer来检测两个手指的范围。你需要标准化手势识别器传来的结果。

**Solution**

**答案**


```
class GameViewController: UIViewController, UIScrollViewDelegate {
    ...
        var c: GLKVector2 = GLKVector2Make(0, 0)

        override func viewDidLoad() {
        ...
            let panGr = UIPanGestureRecognizer(target: self, action: "didPan:")

            panGr.minimumNumberOfTouches = 2

            view.addGestureRecognizer(panGr)
        }

            func didPan(panGR: UIPanGestureRecognizer) {

            var translation = panGR.translationInView(view)

            translation.x /= view.frame.size.width
            translation.y /= view.frame.size.height

            c = GLKVector2Make(Float(translation.x) + c.x, Float(translation.y) + c.y)

            let cUniform = node.shader!.uniformNamed("c")!

            cUniform.floatVector2Value = c
            panGR.setTranslation(CGPointZero, inView: view)
        }
    }
```   
[Complete Source Code](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-3.zip)

[完整代码](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-3.zip)

4 . Use an image to color a julia style fractal. There are many ways to accomplish this, an interesting one is using the following approach:

4 . 用一个图片来给julia分形涂色。有很多方法都可以实现，其中有一个很有意思的方法如下：

At each iteration get the color from the image coresponding to z. If the color is not transparent break out of the loop.
If the obtained color is non transparent after running all the iterations use it to color the coresponding pixel
If the obtained color is transparent use a different formula to color the point. for example the normalized number of iterations.
Here’s a julia set colored with the image of a bunny.

1. 每一次循环都从图片里得到对应z的颜色。如果颜色不是透明的就跳出循环。
2. 如果跑完所有循环，得到的颜色依旧不是透明的，那么就用它来填色对应的像素。
3. 如果是透明的，那么就用另外一个公式来填点的颜色。比如标准化过的循环次数。


下面是一个用兔子照片来填色的julia分形。

![Bunny](https://camo.githubusercontent.com/0be3f31e3f64d34053ad0627224aebcb37f53e7b/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f62756e6e792e706e67)
![兔子](https://camo.githubusercontent.com/0be3f31e3f64d34053ad0627224aebcb37f53e7b/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f62756e6e792e706e67)

![Fractal Bunny](https://camo.githubusercontent.com/cee740f50001ef98b64243995fb580caf2746bb0/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f62756e6e794672616374616c2e706e67)

![分形兔子](https://camo.githubusercontent.com/cee740f50001ef98b64243995fb580caf2746bb0/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f62756e6e794672616374616c2e706e67)

**Hint**

**提示**

You’ll have to add another uniform of type Texture named image. To get a color from the texture at position p you can use vec4 color = texture2D(image,p);

你需要再添加一个Texture类型的uniform变量，命名为image。你可以用`vec4 color = texture2D(image,p)` 来得到texture在p位置的颜色。

**Solution**

**答案**

```
	class GameViewController: UIViewController, UIScrollViewDelegate {
    ...

    override func viewDidLoad() {
    ...
        let imageUniform = node.shader!.uniformNamed("image")!

        imageUniform.textureValue = SKTexture(imageNamed: "bunny")
    	}
    ... 
	}

```

```
	vec4 getColor(vec2 p) {

    if (p.x > 0.99 || p.y > 0.99 || p.x < 0.01 || p.y < 0.01) {
        return vec4(0.0);
    }

    return texture2D(image,p);
	 }
	 
	void main() {
    	#define iterations 128

        vec2 position = v_tex_coord; // gets the location of the current pixel in the intervals [0..1] [0..1]

        position.y = 1.0 - position.y;

        vec2 z = offset + position / zoom;

        z *= 2.0;
        z -= vec2(1.0,1.0);

        float aspectRatio = u_sprite_size.x / u_sprite_size.y;
        z.x *= aspectRatio;

        vec2 c = vec2(-0.76, 0.15);

        vec4 color = vec4(0.0); // initialize color to black

        float it = 0.0; // Keep track of what iteration we reached
        for (int i = 0;i < iterations; ++i) {
            // zn = zn-1 ^ 2 + c

            // (x + yi) ^ 2 = x ^ 2 - y ^ 2 + 2xyi
            z = vec2(z.x * z.x - z.y * z.y, 2.0 * z.x * z.y);
            z += c;

            color = getColor(z);

            if (dot(z,z) > 4.0 || color.w > 0.1) { // dot(z,z) == length(z) ^ 2 only faster to compute
                break;
            }

            it += 1.0;
        }

        if (color.w < 0.1) {

            float s = it / 80.0;
            color = vec4(s,s,s,1.0);
        }

        gl_FragColor = color;
    }

```
[Complete Source Code](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-4.zip)

[完整代码](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-4.zip)

5 . Experiment with formulas for Mandelbrot like fractals **This is a open ended challenge.** 2 examples are provided below

5 .类比分形，实验一下Mandelbrot的公式。**这个是开放性的挑战。**以下提供了两个例子

**Burning Ship Fractal**

**燃烧之船的分形**

![Burning Ship](https://camo.githubusercontent.com/f91ac892791b93dfc03df9b4237fe3ed56d280f5/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f6275726e696e67536869702e706e67)

**Formula** `zn = abs(zn-12 + c)`

**公式** `zn = abs(zn-12 + c)`

**GLSL**

**GLSL**
```
 z = vec2(z.x * z.x - z.y * z.y, 2.0 * z.x * z.y);
 z += c;

 z = abs(z);
 ```
 
[Source Code](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-5-1.zip)

[源代码](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-5-1.zip)

**Sierpinski Julia**

**Sierpinski Julia**

![Sierpinski Julia](https://camo.githubusercontent.com/44c1795c1996bda8ad6c06e80343c0da10dde8ae/68747470733a2f2f7777772e7765686561727473776966742e636f6d2f77702d636f6e74656e742f75706c6f6164732f323031352f30352f7369657270696e736b692d6a756c69612e706e67)

**Formula** `zn = zn-12 + 0.5 * c / (zn-12)`

**公式** `zn = zn-12 + 0.5 * c / (zn-12)`

**GLSLS**

**GLSLS**

```
vec2 powc(vec2 z,float p) {
    vec2 polar = vec2(length(z),atan(z.y,z.x));

    polar.x = pow(polar.x,p);
    polar.y *= p;

    return vec2(polar.x * cos(polar.y),polar.x * sin(polar.y));
}

void main() {
... 
        z = vec2(z.x * z.x - z.y * z.y, 2.0 * z.x * z.y);
        z += 0.5 * c * powc(z,-2.0);
...
}
```
[Source Code](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-5-2.zip)

 [源代码](https://www.weheartswift.com/wp-content/uploads/2015/06/MandelbrotTutorial-Challenge-5-2.zip)

           
            