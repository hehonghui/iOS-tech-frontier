Core Image入门教程(swift)
---

> * 原文链接 : [Core Image Tutorial: Getting Started](http://www.raywenderlich.com/76285/beginning-core-image-swift)
* 原文作者 : [Nick Lockwood](http://www.raywenderlich.com/u/nicklockwood)
* 译文出自 :[开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [Sam Lau](https://github.com/samlaudev) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  未完成

Core Image is a powerful framework that lets you easily apply filters to images. You can get all kinds of effects, such as modifying the vibrance, hue, or exposure. It can use either the CPU or GPU to process the image data and is very fast — fast enough to do real-time processing of video frames!

Core Image filters can also be chained together to apply multiple effects to an image or video frame at once. The multiple filters are combined into a single filter that is applied to the image. 

In this tutorial, you will get hands-on experience playing around with Core Image. You’ll apply a few different filters, and you’ll see how easy it is to apply cool effects to images in real time!

Core Image是一个强大的框架，它能够让你轻松地对图像进行过滤。你能够通过修改图像的饱和度、色调或曝光率来获取各种特效。你也可以使用CPU或GPU更快地来处理图像数据，快到能够实时处理视频帧数据(video frame)。

Core Image过滤器能够以链式的方式结合将多个特效应用到一个图像或视频帧数据。多个过滤器能够组合成单个过滤器应用在一个图像。通过每次与每个过滤器比较，这使它非常有效地处理图像。

在这个教程中，你将会亲手实践如何使用Core Image.通过使用几个不同的过滤器，你会看到实时使用各种炫酷的特效是多么容易。

#Getting Started（星期二）
Before you get started, let’s discuss some of the most important classes in the Core Image framework:

* **CIContext**. All of the processing of a core image is done in a CIContext. This is somewhat similar to a Core Graphics or OpenGL context.
* **CIImage**. This class hold the image data. It can be created from a UIImage, from an image file, or from pixel data.
* **CIFilter**. The CIFilter class has a dictionary that defines the attributes of the particular filter that it represents. Examples of filters are vibrance, color inversion, cropping, and many more.

You’ll be using each of these classes in this project.

在你开始之前，让我们先讨论在Core Image框架中几个很重要的类：

* **CIContext**. 所有处理core image的工作都在CIContext完成。这个有几分与Core Graphics或OpenGL context相似
* **CIImage**. 这个类保存图像数据，它能够从UIImage，图像文件或像素数据等方式来创建。
* **CIFilter**. CIFilter类有一个字典(dictionary)来定义特定属性的过滤器，比如常用过滤器有饱和度，颜色翻转，裁剪等属性，还有很多没列出来。

###CoreImageFun
Open up Xcode and create a new project with the iOS \ Application \ Single View Application template. Enter CoreImageFun for the Product Name, select iPhone for the Devices option, and make sure that Language is set to Swift.

Download the resources for this tutorial, and add the included image.png to your project.

Next, open Main.storyboard, and drag an image view in as a subview of the existing view. In the Attributes Inspector, set the image view’s content mode to Aspect Fit, so it won’t distort images.

Next, ensure the Document Outline (the hierarchy left of the canvas in Interface Builder) is visible – you can enable it from the menu at Editor \ Show Document Outline.

Control-drag from the image view to its superview three times to add three constraints:

1. Add a constraint Top Space to Layout Guide, using the Size Inspector to set the constraint’s constant to zero if necessary.
2. Add a constraint to Center Horizontally in Container (also setting its constant to zero).
3. Add an Equal Width constraint.

Finally, to constrain the image view’s height, control drag from the image view to itself, and add an Aspect Ratio constraint, using the Size Inspector to set its multiplier 8:5 for the ratio of width to height and a constant factor of zero. Finally, navigate to Editor \ Resolve Auto Layout Issues \ All Views in View Controller \ Update Frames, so Interface Builder updates the layout based on these new constraints.

Next, open the Assistant Editor and make sure it’s displaying ViewController.swift. Control-drag from the UIImageView to just after the opening brace of the ViewController class. Name the outlet imageView, and click Connect.

Build and run the project just to make sure everything is good so far – you should just see an empty screen. The initial setup is complete – now onto Core Image!

###CoreImageFun
打开Xcode并使用**iOS \ Application \ Single View Application**模板来创建一个新工程。输入**CoreImageFun**作为Product Name，选择iPhone为Devices option和确定使用**swift**作为编程语言。

下载[教程的资源](http://cdn5.raywenderlich.com/downloads/CIResources.zip)，并添加图片**image.png**到工程

下一步，打开**Main.storyboard**你文件，拖拽一个image view到已存在的view，image view作为view的子视图。在Attributes Inspector中，设置image view的content mode属性为Aspect Fit，这样它就不会扭曲图片。

下一步，确定Document Outline(在Interface Builder里canvas左边)是可见的 - 你可以从menu中Editor \ Show Document Outline来启用。

按着control键从image view拖动到它的superview三次来添加三个约束(constraint)：

1. 添加一个Top Space to Layout Guide的约束，如果有必要的话，使用Size Inspector来设置约束的constant为0 
2. 添加一个Center Horizontally in Container的约束(同样也需要设置constant为0)
3. 添加一个Equal Width约束

最后，为了约束image view的高度，按着control键从image view拖动到本身，然后添加一个Aspect Ratio约束，使用Size Inspector来设置它的multiplier的值为8:5对应宽高比和constant factor为0。最后，选择Editor \ Resolve Auto Layout Issues \ All Views in View Controller \ Update Frames，那么Interface Builder会根据这些约束来更新布局

下一步，打开Assistant Editor，然后确保它显示ViewController.swift。按着control键从UIImageView拖动到刚刚打开的ViewController类的大括号。命名outlet为imageView，然后点击connect。

编译和运行这个项目来确保目前进展顺利 - 你应该看到一个空屏。初始化设置完成，现在进入Core Image的世界。


#Basic Image Filtering（星期二）
You’re going to get started by simply running your image through a CIFilter and displaying it on the screen. Every time you want to apply a CIFilter to an image you need to do four things:

1. Create a CIImage object. CIImage has several initialization methods, including: CIImage(contentsOfURL:), CIImage(data:), CIImage(CGImage:), CIImage(bitmapData:bytesPerRow:size:format:colorSpace:), and several others. You’ll most likely be working with CIImage(contentsOfURL:) most of the time.
2. Create a CIContext. A CIContext can be CPU or GPU based. A CIContext is relatively expensive to initialize so you reuse it rather than create it over and over. You will always need one when outputting the CIImage object.
3. Create a CIFilter. When you create the filter, you configure a number of properties on it that depend on the filter you’re using.
4. Get the filter output. The filter gives you an output image as a CIImage – you can convert this to a UIImage using the CIContext, as you’ll see below.

Let’s see how this works. Add the following code to ViewController.swift inside viewDidLoad():

```
// 1
let fileURL = NSBundle.mainBundle().URLForResource("image", withExtension: "png")
 
// 2
let beginImage = CIImage(contentsOfURL: fileURL)
 
// 3
let filter = CIFilter(name: "CISepiaTone")
filter.setValue(beginImage, forKey: kCIInputImageKey)
filter.setValue(0.5, forKey: kCIInputIntensityKey)
 
// 4
let newImage = UIImage(CIImage: filter.outputImage)
self.imageView.image = newImage
```

Let’s go over this section by section:

1. This line creates an NSURL object that holds the path to your image file.
2. Next you create your CIImage with the CIImage(contentsOfURL:) constructor. 
3. Next you’ll create your CIFilter object. The CIFilter constructor takes the name of the filter, and a dictionary that specifies the keys and values for that filter. Each filter will have its own unique keys and set of valid values. The CISepiaTone filter takes only two values, the KCIInputImageKey (a CIImage) and the kCIInputIntensityKey, a float value between 0 and 1. Here you give that value 0.5. Most of the filters have default values that will be used if no values are supplied. One exception is the CIImage, this must be provided as there is no default.
4. Getting a CIImage back out of a filter is as easy as using the outputImage property. Once you have an output CIImage, you will need to convert it into a UIImage. The UIImage(CIImage:) constructor creates a UIImage from a CIImage. Once you’ve converted it to a UIImage, you just display it in the image view you added earlier.

Build and run the project, and you’ll see your image filtered by the sepia tone filter.

<div align="center">

<img src="http://cdn5.raywenderlich.com/wp-content/uploads/2014/07/CI-Sepia-Crop.jpg"/>
</div>

#Putting It Into Context（星期三）
Before you move forward, there’s an optimization that you should know about.

I mentioned earlier that you need a CIContext in order to apply a CIFilter, yet there’s no mention of this object in the above example. It turns out that the the UIImage(CIImage:) constructor does all the work for you. It creates a CIContext and uses it to perform the work of filtering the image. This makes using the Core Image API very easy.

There is one major drawback – it creates a new CIContext every time it’s used. CIContext instances are meant to be reusable to increase performance. If you want to use a slider to update the filter value, as you’ll be doing in this tutorial, creating a new CIContext each time you change the filter would be way too slow.

Let’s do this properly. Delete step 4 from the code you added to viewDidLoad(), and replace it with the following:

```
// 1
let context = CIContext(options:nil)
 
// 2
let cgimg = context.createCGImage(filter.outputImage, fromRect: filter.outputImage.extent())
 
// 3
let newImage = UIImage(CGImage: cgimg)
self.imageView.image = newImage
```
Again, let’s go over this section by section.

1. Here you set up the CIContext object and use it to draw a CGImage. The CIContext(options:) constructor takes an NSDictionary that specifies options such as the color format, or whether the context should run on the CPU or GPU. For this app, the default values are fine and so you pass in nil for that argument.
2. Calling createCGImage(outputImage:fromRect:) on the context with the supplied CIImage will return a new CGImage instance.
3. Next, you use the UIImage(CGImage:) constructor to create a UIImage from the newly created CGImage instead of directly from the CIImage as before. Note that there is no need to explicitly release the CGImage after we are finished with it, as there would have been in Objective-C. In Swift, ARC can automatically release Core Foundation objects.

Build and run, and make sure it works just as before.

In this example, handling the CIContext creation yourself doesn’t make much difference. But in the next section, you’ll see why this is important for performance, as you implement the ability to modify the filter dynamically!

#Changing Filter Values（星期三）
This is great, but it’s just the beginning of what you can do with Core Image filters. Lets add a slider and set it up so you can adjust the filter settings in real time.

Open Main.storyboard, and drag in a slider, and drop it in below the image view, centered horizontally. With the view selected, navigate to Editor \ Resolve Auto Layout Issues \ Selected Views \ Reset to Suggested Constraints, increasing the width constraint if desired.

Make sure the Assistant Editor is visible and displaying ViewController.swift, then control-drag from the slider down below the previously added @IBOutlet, set the name to amountSlider, and click Connect.

While you’re at it let’s connect the slider to an action method as well. Again control-drag from the slider, this time to just above the closing } of the ViewController class. Set the Connection to Action, the name to amountSliderValueChanged, make sure that the Event is set to Value Changed, and click Connect.

Every time the slider changes, you need to redo the image filter with a different value. However, you don’t want to redo the whole process, that would be very inefficient and would take too long. You’ll need to change a few things in your class so that you hold on to some of the objects you create in your viewDidLoad method.

The biggest thing you want to do is reuse the CIContext whenever you need to use it. If you recreate it each time, your program will run very slowly. The other things you can hold onto are the CIFilter and the CIImage that holds your original image. You’ll need a new CIImage for every output, but the image you start with will stay constant.

You need to add some instance variables to accomplish this task. Add the following three properties to your ViewController class:

```
var context: CIContext!
var filter: CIFilter!
var beginImage: CIImage!

```

Note that you have declared these values as implicitly-unwrapped optionals using the ! syntax, because you aren’t going to initialize them until viewDidLoad. You could have used ? instead, but you know that the way the code is designed will prevent the optionals from being nil by the time you use them. The implicitly-unwrapped syntax makes it much easier to read, without all the exclamation marks everywhere.

Change the code in viewDidLoad so it uses these properties instead of declaring new local variables, as follows:

```
beginImage = CIImage(contentsOfURL: fileURL)
 
filter = CIFilter(name: "CISepiaTone")
filter.setValue(beginImage, forKey: kCIInputImageKey)
filter.setValue(0.5, forKey: kCIInputIntensityKey)
 
let outputImage = filter.outputImage
 
context = CIContext(options:nil)
let cgimg = context.createCGImage(outputImage, fromRect: outputImage.extent())

```

Now you’ll implement the changeValue method. What you’ll be doing in this method is altering the value of the inputIntensity key in your CIFilter dictionary.

Once you’ve altered this value you’ll need to repeat a few steps:

* Get the output CIImage from the CIFilter.
* Convert the CIImage to a CGImage.
* Convert the CGImage to a UIImage, and display it in the image view.

Replace amountSliderValueChanged(sender:) with the following:

```
@IBAction func amountSliderValueChanged(sender: UISlider) {
 
    let sliderValue = sender.value
 
    filter.setValue(sliderValue, forKey: kCIInputIntensityKey)
    let outputImage = filter.outputImage
 
    let cgimg = context.createCGImage(outputImage, fromRect: outputImage.extent())
 
    let newImage = UIImage(CGImage: cgimg)
    self.imageView.image = newImage
}

```

You’ll notice that you’ve changed the argument type from AnyObject to UISlider in the method definition. You know you’ll only be using this method to retrieve values from your UISlider, so you can go ahead and make this change. If you’d left it as AnyObject, you’d need to cast it to a UISlider or the next line would throw an error.

You retrieve the value from the slider (which returns a Float). Your slider is set to the default values – min 0, max 0, default 0.5. How convenient, these happen to be the right values for this CIFilter!

The CIFilter has methods that will allow you to set the values for the different keys in its dictionary. Here, you’re just setting the inputIntensity to whatever you get from your slider. Swift automatically converts the primitive CFloat value into an NSNumber object suitable for use with setValue(value:forKey:).

The rest of the code should look familiar, as it follows the same logic as viewDidLoad. You’re going to be using this code over and over again. From now on, you’ll use amountSliderValueChanged(sender:) to render the output of a CIFilter to your UIImageView.

Build and run, and you should have a functioning live slider that will alter the sepia value for your image in real time!

#Getting Photos from the Photo Album（星期四）
Now that you can change the values of the filter on the fly, things are starting to get real interesting! But what if you don’t care for this image of flowers? Next you’ll set up a UIImagePickerController so you can get pictures from out of the photo album and into your app so you can play with them.

You need to create a button that will bring up the photo album view, so open up Main.storyboard, drag in a button to the bottom right of the scene, and label it “Photo Album”. As before, use Auto Layout to Reset to Suggested Constraints. The button should be underneath the slider on the right side.

Make sure the Assistant Editor is visible and displaying ViewController.swift, then control-drag from the button down to just above the closing } for the ViewController class. Set the Connection to Action, the name to loadPhoto, make sure that the Event is set to Touch Up Inside, and click Connect.

Implement the loadPhoto method as follows:

```
@IBAction func loadPhoto(sender : AnyObject) {
  let pickerC = UIImagePickerController()
  pickerC.delegate = self
  self.presentViewController(pickerC, animated: true, completion: nil)
}

```

The first line of code instantiates a new UIImagePickerController. You then set the delegate of the image picker to self (the ViewController).

You get a warning here. You need to declare that ViewController conforms to the UIImagePickerControllerDelegate and UINavigationControllerDelegate protocols.

Still in ViewController.swift, change the class definition at the top of the file as follows:

```
class ViewController: UIViewController, UINavigationControllerDelegate, UIImagePickerControllerDelegate {
```
Now implement the following method:

```
func imagePickerController(picker: UIImagePickerController!, didFinishPickingMediaWithInfo info: NSDictionary!) {
  self.dismissViewControllerAnimated(true, completion: nil);
  println(info);
}

```

This UIImagePickerControllerDelegate method isn’t completed yet – it’s just a placeholder to log some information about the chosen image. Note that whenever you implement any of the UIImagePickerControllerDelegate methods, you have to dismiss the UIImagePickerController explicitly in your implementation. If you don’t, then you’ll just stare at the image picker forever!

Build and run the app, and tap the button. It will bring up the image picker with the photos in your photo album.

> If you are running this in the simulator, you probably won’t have any photos. On the simulator (or on a device without a camera), you can use Safari to save images to your photo album. Open Safari, find an image, tap and hold, and you’ll get an option to save that image. Next time you run your app, it will appear in your photo library.

Here’s what you should see in the console after you’ve selected an image (something like this):

```
{
UIImagePickerControllerMediaType = "public.image";
UIImagePickerControllerOriginalImage = " size {1165, 770} orientation 0 scale 1.000000";
UIImagePickerControllerReferenceURL = "assets-library://asset/asset.PNG?id=DCFE1435-2C01-4820-9182-40A69B48EA67&ext=PNG";
}

```

Note that it has an entry in the dictionary for the “original image” selected by the user. This is what you want to pull out and filter!

Now that you’ve got a way to select an image, how do you use that as your beginImage?

Simple, just update the delegate method to look like this:

```
func imagePickerController(picker: UIImagePickerController!, didFinishPickingMediaWithInfo info: NSDictionary!) {
  self.dismissViewControllerAnimated(true, completion: nil);
 
  let gotImage = info[UIImagePickerControllerOriginalImage] as UIImage
 
  beginImage = CIImage(image:gotImage)
  filter.setValue(beginImage, forKey: kCIInputImageKey)
  self.amountSliderValueChanged(amountSlider)
}

```
You need to create a new CIImage from your selected photo. You can get the UIImage representation of the photo by finding it in the dictionary of values, under the UIImagePickerControllerOriginalImage key constant. Note that it’s better to use a constant for this, rather than a hardcoded string, because Apple could change the name of the key in the future.

You need to convert the image into a CIImage object with the CIImage(image:) constructor. You then set the key in the filter dictionary so that the input image is the new CIImage you just created.

The last line may seem odd. Remember how I pointed out that the code in changeValue ran the filter with the latest value and updated the image view with the result?

Well you need to do that again, so you can just call changeValue. Even though the slider value hasn’t changed, you can still use that method’s code to get the job done. You could break that code into its own method (and if you were going to be working with more complexity, you would, to avoid confusion), but in this case your purpose is served by re-using the amountSliderValueChanged method. Pass in the amountSlider as the sender so that it has the correct value to use.

Build and run, and now you’ll be able to update any image from your photo album!

What if you create the perfect sepia image, how do you hold on to it? You could take a screenshot, but the proper way is to save the filtered photo back into the photo album.

#Saving to Photo Album（星期四）
To save to the photo album, you need to use the AssetsLibrary framework. Add the following import statement to the top of ViewController.swift:

```
import AssetsLibrary
```
One thing you should know is that when you save a photo to the album, it may take a few seconds, and that process could continue even after you close the app.

This could be a problem, as the GPU stops whatever it’s doing when you switch from one app to another. If the photo hasn’t finished saving, it won’t be there when you go looking for it later!

The solution to this is to use a CPU-based CIContext for rendering. The default behavior is to use the GPU because it’s much faster, and you don’t want to slow down the filtering performance for the sake of adding save functionality. Instead, you will create a second CIContext to use for saving the image. Note that the software renderer won’t work properly in the simulator.

Add a new button to your app that will let you save the photo you are currently modifying with all the changes you’ve made. Open Main.storyboard, add a new button labeled “Save to Album”. Place the button under the slider on the left side, and add the suggested constraints.

Then connect it to a new savePhoto(sender:) method, like you did last time, and implement the method as follows:

```
@IBAction func savePhoto(sender: AnyObject) {
    // 1
    let imageToSave = filter.outputImage
 
    // 2
    let softwareContext = CIContext(options:[kCIContextUseSoftwareRenderer: true])
 
    // 3
    let cgimg = softwareContext.createCGImage(imageToSave, fromRect:imageToSave.extent())
 
    // 4
    let library = ALAssetsLibrary()
    library.writeImageToSavedPhotosAlbum(cgimg,
      metadata:imageToSave.properties(),
      completionBlock:nil)
}

```
In this code block you:

1. Get the CIImage output from the filter.
2. Create a new, software based CIContext that uses the CPU renderer.
3. Generate the CGImage.
4. Save the CGImage to the photo library.

Build and run the app (remember to run on an actual device, since you’re using software rendering), and now you can save that “perfect image” to your photo library so it’s preserved forever!

#What About Image Metadata?（星期五）
Let’s talk about image metadata for a moment. Image files taken on mobile phones have a variety of data associated with them, such as GPS coordinates, image format, and orientation.

Orientation in particular is something that you’ll need to preserve. The process of loading a UIImage into a CIImage, rendering to a CGImage, and converting back to a UIImage strips the metadata from the image. In order to preserve orientation, you’ll need to record it and then pass it back into the UIImage constructor.

Start by adding a new property inside the ViewController class definition:

```
var orientation: UIImageOrientation = .Up
```

Next, add the following line to imagePickerController(picker:didFinishPickingMediaWithInfo:) just before setting beginImage:

```
orientation = gotImage.imageOrientation

```

This will save the original image orientation to the property.

Finally, alter the line in amountSliderValueChanged that creates the UIImage that you set to the imageView object:

```
let newImage = UIImage(CGImage: cgimg, scale:1, orientation:orientation)

```
Now, if you take a picture taken in something other than the default orientation, it will be preserved.

#What Other Filters are Available?（星期五）
The CIFilter API has more than 160 filters on Mac OS, 126 of which are available on iOS 8. As of iOS 8, it is now possible to create you own custom filters as well.

In order to find out what filters are available on a given device, you can use the CIFilter method filterNamesInCategory(kCICategoryBuiltIn). This method will return an array of filter names.

In addition, each filter has an attributes() method that will return a dictionary containing information about that filter. This information includes the filter’s name and category, the kinds of inputs the filter takes, and the default and acceptable values for those inputs.

Let’s put together a method for your class that logs the information for all the currently available filters. Add this method inside the ViewController class definition:

```
func logAllFilters() {
  let properties = CIFilter.filterNamesInCategory(kCICategoryBuiltIn)
  println(properties)
 
  for filterName: AnyObject in properties {
    let fltr = CIFilter(name:filterName as String)
    println(fltr.attributes())
  }
}

```
This method simply gets the array of filters from filterNamesInCategory(). It prints the list of names first. Then, for each name in the list, it instantiates the filter and logs its attributes dictionary.

Call this method at the end of viewDidLoad():

```
self.logAllFilters()
```

You will see the many filters listed in the console like the following:

```
[CIAttributeFilterDisplayName: Color Monochrome, inputColor: {
    CIAttributeClass = CIColor;
    CIAttributeDefault = "(0.6 0.45 0.3 1)";
    CIAttributeType = CIAttributeTypeColor;
}, inputImage: {
    CIAttributeClass = CIImage;
    CIAttributeType = CIAttributeTypeImage;
}, CIAttributeFilterCategories: (
    CICategoryColorEffect,
    CICategoryVideo,
    CICategoryInterlaced,
    CICategoryNonSquarePixels,
    CICategoryStillImage,
    CICategoryBuiltIn
), inputIntensity: {
    CIAttributeClass = NSNumber;
    CIAttributeDefault = 1;
    CIAttributeIdentity = 0;
    CIAttributeSliderMax = 1;
    CIAttributeSliderMin = 0;
    CIAttributeType = CIAttributeTypeScalar;
}, CIAttributeFilterName: CIColorMonochrome]
```

Wow, that’s a lot of filters! That should give you some ideas for other filters to try out in your own app!

#More Intricate Filter Chains（星期六）
Now that we’ve looked at all the filters that are available on the iOS platform, it’s time to create a more intricate filter chain. In order to do this, you’ll create a dedicated method to process the CIImage. It will take a CIImage, filter it to look like an old aged photo, and return a modified CIImage.

Add the following method to ViewController:

```
func oldPhoto(img: CIImage, withAmount intensity: Float) -> CIImage {
  // 1
  let sepia = CIFilter(name:"CISepiaTone")
  sepia.setValue(img, forKey:kCIInputImageKey)
  sepia.setValue(intensity, forKey:"inputIntensity")
 
  // 2
  let random = CIFilter(name:"CIRandomGenerator")
 
  // 3
  let lighten = CIFilter(name:"CIColorControls")
  lighten.setValue(random.outputImage, forKey:kCIInputImageKey)
  lighten.setValue(1 - intensity, forKey:"inputBrightness")
  lighten.setValue(0, forKey:"inputSaturation")
 
  // 4
  let croppedImage = lighten.outputImage.imageByCroppingToRect(beginImage.extent())
 
  // 5
  let composite = CIFilter(name:"CIHardLightBlendMode")
  composite.setValue(sepia.outputImage, forKey:kCIInputImageKey)
  composite.setValue(croppedImage, forKey:kCIInputBackgroundImageKey)
 
  // 6
  let vignette = CIFilter(name:"CIVignette")
  vignette.setValue(composite.outputImage, forKey:kCIInputImageKey)
  vignette.setValue(intensity * 2, forKey:"inputIntensity")
  vignette.setValue(intensity * 30, forKey:"inputRadius")
 
  // 7
  return vignette.outputImage
}
```

Here’s what’s going on, section by section:

1. Set up the sepia filter the same way you did in the simpler scenario. You’re passing in a Float value in the method to set the intensity of the sepia effect. This value will be provided by the slider.
2. Set up a filter that creates a random noise pattern that looks like this:

   It doesn’t take any parameters. You’ll use this noise pattern to add texture to your final “old photo” look.
   
3. Alter the output of the random noise generator. You want to change it to grayscale, and lighten it up a little bit so the effect is less dramatic. You’ll notice that the input image key is set to the outputImage property of the random filter. This is a convenient way to pass the output of one filter as the input of the next.
4. imageByCroppingToRect() takes an output CIImage and crops it to the provided rect. In this case, you need to crop the output of the CIRandomGenerator filter because it tiles infinitely. If you don’t crop it at some point, you’ll get an error saying that the filters have ‘an infinite extent’. CIImages don’t actually contain image data, they describe a ‘recipe’ for creating it. It’s not until you call a method on the CIContext that the data is actually processed.
5. Combine the output of the sepia filter with the output of the CIRandomGenerator filter. This filter performs the exact same operation as the ‘Hard Light’ setting does in a photoshop layer. Most (if not all) of the filter options in photoshop are achievable using Core Image.
6. Run a vignette filter on this composited output that darkens the edges of the photo. You’re using the value from the slider to set the radius and intensity of this effect.
7. Run a vignette filter on this composited output that darkens the edges of the photo. You’re using the value from the slider to set the radius and intensity of this effect.

That’s all for this filter chain. You should now have an idea of how complex these filter chains may become. By combining Core Image filters into these kinds of chains, you can achieve an endless variety of effects.

The next thing to do is implement this method in amountSliderValueChanged(). Change these two lines:

```
filter.setValue(sliderValue, forKey: "inputIntensity")
let outputImage = filter.outputImage
```

To this one line:

```
let outputImage = self.oldPhoto(beginImage, withAmount: sliderValue)
```

This just replaces the previous sepia effect with your new, more complex filter method. You pass in the slider value for the intensity and you use the beginImage, which you set in the viewDidLoad method as the input CIImage. Build and run now and you should get a more refined old photo effect, complete with sepia, a little noise, and some vignetting.

That noise could probably be more subtle, but I’ll leave that experiment to you, dear reader. Now you have the full power of Core Image at your disposal. Go crazy!

#Where To Go From Here?（星期六）
Here is the example project with all of the code from the above tutorial.

That about covers the basics of using Core Image filters. It’s a pretty handy technique, and you should be able to use it to apply some neat filters to images quite quickly!

Remember, the sample app prints out a list of all the available filters to the console when it starts up. Why not try playing around with some of those? You can check out the Core Image filter reference documentation for notes on all the filters.

If you have any questions or comments on this tutorial or Core Image in general, please join the forum discussion below!