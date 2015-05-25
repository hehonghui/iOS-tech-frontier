通过减少动态分发来提高Swift的执行效率
---

>
* 原文链接 : [Increasing Performance by Reducing Dynamic Dispatch](https://developer.apple.com/swift/blog/?id=27)
* 原文作者 : [Apple官方博文](https://developer.apple.com/swift)
* 译文出自 : [开发技术前线 www.devtf.cn](www.devtf.cn)
* 译者 : [samw00](https://github.com/samw00/) 
* 校对者:  
* 状态 :  校对中

Like many other languages, Swift allows a class to override methods and properties declared in its superclasses. This means that the program has to determine at runtime which method or property is being referred to and then perform an indirect call or indirect access. This technique, called dynamic dispatch, increases language expressivity at the cost of a constant amount of runtime overhead for each indirect usage. In performance sensitive code such overhead is often undesirable. This blog post showcases three ways to improve performance by eliminating such dynamism: final, private, and Whole Module Optimization.

和其他很多编程语言一样，Swift允许一个类重写其父类中声明的方法和属性。这意味着程序在运行时需要先确定要调用的方法和访问的属性是属于哪个类的，确定之后，则会执行间接调用（indirect call）或间接访问（indirect access）。这个运行机制称为**动态分发**（dynamic dispatch）。这个机制能够提高语言的表现力，但其付出的代价是在每次间接调用或间接访问时，会产生一定量的运行时开销（runtime overhead）。在执行敏感代码时，最好能避免产生这样的开销。这篇博客将罗列三种通过消除这动态分发机制来提高程序执行效率的方式：`final`，`private`和全模块优化（Whole Module Optimization）。

Consider the following example:

先来看看下面这段代码

```
class ParticleModel {
	var point = ( 0.0, 0.0 )
	var velocity = 100.0

	func updatePoint(newPoint: (Double, Double), newVelocity: Double) {
		point = newPoint
		velocity = newVelocity
	}

	func update(newP: (Double, Double), newV: Double) {
		updatePoint(newP, newVelocity: newV)
	}
}

var p = ParticalModel()
for i in stride(from: 0.0, through: 360, by: 1.0) {
	p.update((i * sin(i), i), newV: i * 1000)
}
```

As written, the compiler will emit a dynamically dispatched call to:

1. Call update on p.
2. Call updatePoint on p.
3. Get the property point tuple of p.
4. Get the property velocity of p.

正如上面代码所写的那样，编译器会通过动态分发机制来：

1. 调用`p`的`update()`方法。
2. 调用`p`的`updatePoint`方法。
3. 获取`p`中`point`这个属性元组。
4. 获取`p`中的`velocity`属性。

This might not be what you would expect when looking at this code. The dynamic calls are necessary because a subclass of ParticleModel might override point or velocity with a computed property or override updatePoint() or update() with new implementations.

这可能和你当初看到代码时所期望的不一样。因为某个`PracticalModel`的子类可能会通过一个计算过的属性来重写`point`或`velocity`属性或者重写`updatePoint()`或`update()`中的实现方法，所以通过一个动态分发的机制去调用是有必要的。

In Swift, dynamic dispatch calls are implemented by looking up a function from a method table and then performing an indirect call. This is slower than performing a direct call. Additionally, indirect calls also prevent many compiler optimizations, making the indirect call even more expensive. In performance critical code there are techniques you can use to restrict this dynamic behavior when it isn’t needed to improve performance.

在Swift中，动态分发调用既是在一个方法表中找寻要调用的函数，然后执行间接调用，这样的执行效率会低于直接调用。除此之外，间接调用也阻止编译器各种优化，从而使得间接调用的成本更高。在执行某些关键代码时，并不需要动态分发来提高执行效率的时候，你可以使用一些技术来对动态分发调用进行一些限制和约束。

Use final when you know that a declaration does not need to be overridden.

##当你确定某个声明不会被重写时，用final关键字修饰

The final keyword is a restriction on a class, method, or property that indicates that the declaration cannot be overridden. This allows the compiler to safely elide dynamic dispatch indirection. For instance, in the following point and velocity will be accessed directly through a load from the object’s stored property and updatePoint() will be called via a direct function call. On the other hand, update() will still be called via dynamic dispatch, allowing for subclasses to override update() with customized functionality.

加上关键字`final`就意味着类，方法或者属性的声明不能被重写。这就能让编译器安全的省略动态分发的间接调用。比方说在下面的代码中，属性`point`和`velocity`会通过加载对象已存的属性来被直接访问,`updatePoint()`也会被直接调用。但是`update()`这个方法还是会通过动态分发来间接调用，这样能允许子类自定义`update()`中的实现。

```
class ParticlModel {
	final var point = (x: 0.0, y: 0.0)
	final var velocity = 100.0

	final func updatePoint(newPoint: (Double, Doulbe), newVelocity: Double) {
		point = newPoint
		velocity = newVelocity
	}

	func update(newP: (Double, Double), newV: Double) {
		updatePoint(newP, newVelocity: newV)
	}
}
```

It is possible to mark an entire class as final by attaching the attribute to the class itself. This forbids subclassing the class, implying that all functions and properties of the class are final as well.

也可以给整个类加一个`final`关键字来修饰。这样就禁止了给当前类添加任何子类也意味着类里所有的方法和属性都是`final`。

```
final class ParticleModel {
	var point = (x: 0.0, y:0.0)
	var velocity = 100.0
	// ...
}
```

Infer final on declarations referenced in one file by applying the private keyword.

##用private关键字来暗示在同一文件中的声明为final

Applying the private keyword to a declaration restricts the visibility of the declaration to the current file. This allows the compiler to find all potentially overriding declarations. The absence of any such overriding declarations enables the compiler to infer the final keyword automatically and remove indirect calls for methods and property accesses.

给声明添加关键字`private`会把该声明限制在当前文件可见。这允许编译器去寻找所有潜在能被重写的声明。任何没有标明能被重写的声明，编译器都会自动推断为`final`并去除访问该属性或方法时的间接调用。

Assuming there is no class overriding ParticleModel in the current file, the compiler can replace all dynamically dispatched calls to private declarations with direct calls

假设在当前文件中，没有类会重写`ParticleModel`，编译器会把所有`priavte`修饰的声明的动态分发调用替换成直接调用。

```
class ParticleModel {
	private var point = (x:0.0, y:0.0)
	private var velocity = 100.0

	private fun updatePoint(newPoint: (Double, Double), newVelocity: Double) {
		point = newPoint
		velocity = newVelocity
	}

	func update(newP: (Double, Double), newV: Double) {
		updatePoint(newP, newVelocity: newV)
	}
}
```

As in the previous example, point and velocity are accessed directly and updatePoint() is called directly. Again, update() will be invoked indirectly due to update() not being private.

就像上面这个例子，属性`point`和`velocity`被直接访问，`updatePoint()`也是被直接调用。同样的，`update()`仍会被间接调用，因为没有添加`private`修饰。

Just like with final, it is possible to apply the private attribute to the class declaration itself causing the class to be private and thus all of the properties and methods of the class as well.

和关键字`final`一样，同样可以用`private`来修饰一个类，这会使类成为一个私有类，类中的方法和属性也同样为私有`private`。

```
private class ParticleModel {
	var point = (x:0.0, y:0.0)
	var velocity = 100.0
	// ...
}
```

Use Whole Module Optimization to infer final on internal declarations.

##使用全模块优化将internal声明推断为final

Declarations with internal access (the default if nothing is declared) are only visible within the module where they are declared. Because Swift normally compiles the files that make up a module separately, the compiler cannot ascertain whether or not an internal declaration is overridden in a different file. However, if Whole Module Optimization is enabled, all of the module is compiled together at the same time. This allows the compiler to make inferences about the entire module together and infer final on declarations with internal if there are no visible overrides.

带有`internal`访问的声明（如果没有特别声明，此为默认）只在当前模块中可见。通常来讲Swift会分开编译同一模块中的不同文件，所以编译器是无法确定某个`internal`声明是否在另一个文件被重写。但是，如果开启了全模块优化，则所有的模块都会在同一时被一起编译。这就能让编译器来对整个模块进行处理，如果某个`internal`的声明没有可见重写，则推断为`final`。

Let’s go back to the original code snippet, this time adding some extra public keywords to ParticleModel.

现在我们回过头去看之前的代码段，这次添加一些额外的`public`关键字。

```
public class ParticleModel {
	var point = (x:0.0, y:0.0)
	var velocity = 100.0

	func updatePoint(newPoint: (Double, Double), newVelocity: Double) {
		point = newPoint
		velocity = newVelocity
	}

	public func update(newP: (Double, Double), newV: Double) {
		updatePoint(newP, newVelocity: newV)
	}
}

var p = ParticleModel()
for i in stride(from: 0.0, through: times, by: 1.0) {
	p.update((i * sin(i), i), newV: i * 1000)
}
```

When compiling this snippet with Whole Module Optimization the compiler can infer final on the properties point, velocity, and the method call updatePoint(). In contrast, it can not be inferred that update() is final since update() has public access.

当用全局模块优化来编译这段代码时，编译器能够推断`point`，`velocity`属性和`updatePoint()`方法为`final`。相反的，因为`update()`方法被public修饰，则不能推断为`final`。
