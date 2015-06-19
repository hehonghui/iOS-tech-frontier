iOS依赖注入
---

> * 原文链接 : [Dependency Injection: Give Your iOS Code a Shot in the Arm
June 05, 2015](https://corner.squareup.com/2015/06/dependency-injection-in-objc.html)
* 原文作者 : [Square Engineering Blog](https://corner.squareup.com)
* 译文出自 : [开发技术前线 www.devtf.cn](http://www.devtf.cn)
* 译者 : [HarriesChen](https://github.com/mrchenhao) 
* 校对者: [这里校对者的github用户名](github链接)  
* 状态 :  未完成 / 校对中 / 完成 

##What Is Dependency Injection?
Dependency injection (DI) is a popular design pattern in many languages, such as Java and C# , but it hasn't seen wide adoption in Objective-C. This article aims to give a brief introduction to dependency injection using Objective-C examples, as well as practical methods for using dependency injection in Objective-C code. Although the article focuses on Objective-C, all of the concepts apply to Swift as well.

##什么是依赖注入呢?
依赖注入（DI）是一种非常流行的设计模式在许多的语言之中，比如说Java和C#,但是它似乎并没有在Objective-C广泛的被采用，这篇文章的目的就是简要的介绍一下在Objective-C中使用依赖注入的例子。虽然代码是用Objective-C写的，但是也可以应用到Swift。

The concept of dependency injection is very simple: an object should require you to pass in any dependencies rather than creating them itself. Martin Fowler's excellent discussion on the subject is highly suggested background reading.

依赖注入的概念很简单：一个对象应该被依赖而不是被创建。强烈建议阅读Martin Fowler的[excellent discussion on the subject](http://martinfowler.com/articles/injection.html)作为背景阅读。

Dependencies can be passed to an object via the initializer (or "constructor"), or via properties (or "setters"). These are commonly referred to as "constructor injection" and "setter injection."

注入可以通过对象的初始化（或者可以说构造器）或者通过特性（setter），这些就指的是构造器注入和setter方法注入。


##Constructor Injection:
##构造器注入

```
- (instancetype)initWithDependency1:(Dependency1 *)d1 
                        dependency2:(Dependency2 *)d2;
                        
```

##Setter Injection:
##Setter 注入

```
@property (nonatomic, retain) Dependency1 *dependency1;
@property (nonatomic, retain) Dependency2 *dependency2;
```

As Fowler describes, [constructor injection is preferred](http://martinfowler.com/articles/injection.html#ConstructorVersusSetterInjection), and as a general rule you should only fall back to setter injection if constructor injection is not possible. With constructor injection, you'll likely still have @property definitions for these dependencies, but you can make them read only to simplify your object's API.

根据福勒的描述，[首选构造器注入](http://martinfowler.com/articles/injection.html#ConstructorVersusSetterInjection)，一般来说只有在构造器注入不可行的情况下才会选择settter注入。在构造器注入中，你可以还是会使用`@property`来定义这些依赖，但是在接口中你应该让他们为只读，


##Why Use Dependency Injection?
Dependency injection offers a number of benefits, but some of the more important ones are:

##为什么使用依赖注入
依赖注入提供了一系列的好处，其中重要的是一下几点：

* **Clear declaration of dependencies** It becomes obvious what an object needs in order to operate, and dangerous hidden dependencies—like globals—disappear.
* **Composition** DI encourages [composition over inheritance](https://en.wikipedia.org/wiki/Composition_over_inheritance), which improves the reusability of your code.
* **Easy customization** When creating an object, it's easy to customize parts of the object for specific scenarios.
* **Clear ownership** Particularly when using constructor injection, the object ownership rules are strictly enforced—helping to build a [directed acyclic object graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph).
* **Testability** More than anything else, dependency injection improves the testability of your objects. Because they can be created simply by filling in the initializer, no hidden dependencies need to be managed. Furthermore, it becomes simple to mock out the dependencies to focus your tests on the object being tested.

* **依赖定义清晰** 可以很明显的看出对象需要什么来进行操作，从而可以避免依赖类似（dependencies—like），全部变量消失（globals—disappear）。
* **组合** 依赖注入提倡[组合而不是继承](https://en.wikipedia.org/wiki/Composition_over_inheritance), 提高代码的可重用性。
* **定制简单** 当创建一个对象的时候，可以轻松的定制对象的每一个部分来面对最糟糕的情况。
* **所有权清晰** 特别是当使用构造函数注入，严格执行对象的所有权规则，帮助建立一个[有向无循环对象图](https://en.wikipedia.org/wiki/Directed_acyclic_graph)。
* **可测试性** 更重要的是，依赖注入提高了你的对象的可测性。因为他们可以被非常简单的创建通过初始化方法，没有隐藏的依赖关系需要管理。此外，可以简单的模拟出依赖让你关注于被测试的对象。


##Switching to Dependency Injection
##使用依赖注入

Your codebase may not already be designed using the dependency injection design pattern, but it's easy to get started. One nice aspect of dependency injection is that you don't need to adopt "all or nothing." Instead, you can apply it to particular areas of the codebase and expand from there.

你的代码可能还没有使用依赖注入的设计模式来设计，但是很容易上手。依赖注入的一个很好的方面是，你不需要采用“全或无”。相反，你可以将它应用到代码的特定区域并从那里展开。

###Types of Classes to Inject
###类型注入

First, let's classify classes into two buckets: basic classes and complex classes. Basic classes are ones which have no dependencies, or which depend only on other basic classes. Basic classes are highly unlikely to be subclassed, because their functionality is clear, invariable, and doesn't reference external resources. Many examples of basic classes come from Cocoa itself, such as NSString, NSArray, NSDictionary, and NSNumber.

首先，让我们把类分成两种：基本类和复合类。基本类是没有依赖的，或只依赖于其他基础类。基本类被继承的可能性极低，因为它们的功能是明确的，不变的，不引用外部资源。Cocoa本身有很多的基本类，如`NSString` `NSArray`，`NSDictionary` `NSNumber`……。

Complex classes are the opposite. They have other complex dependencies, include application-level logic (which may need to change), or access external resources such as the disk, the network, or a global in-memory service. Most of the classes in your application will be complex, including almost any controller object and most model objects. Many Cocoa classes are complex as well, such as NSURLConnection or UIViewController.

复杂类则相反，他们有复杂的依赖关系，包括应用级别的逻辑（可能被改变），或者访问外部资源，比如磁盘，网络或者一些全局的服务。在你应用内的大多数类都是复杂的，包括大多数的控制器对象和模型对象。需要Cocoa类也食非常复杂的，比如说`NSURLConnection `或者`UIViewController `

Given these classifications, the easiest way to get started is to pick a complex class in your application and look for places where you initialize other complex objects within that class (search for "alloc] init" or "new]"). To introduce dependency injection into the class, change this instantiated object to be passed in as an initializer parameter on the class rather than the class instantiating the object itself.

判断这个的最简单方法就是拿起一个复杂类然后看它初始化其他复杂类的地方（搜索"alloc] init" or "new]"），介绍了类的依赖注入，改变这个实例化的对象是通过作为一个类的初始化参数而不是类的实例化对象本身。


###Dependencies Allocated During Initialization
###初始化中的依赖分配

Let's look at an example where the child object (the dependency) is being initialized as part of the parent's initialization. The original code might look something like this:

让我们来看一个例子，一个子对象（依赖）被初始化作为父对象的一部分。一般代码如下：

```
@interface RCRaceCar ()

@property (nonatomic, readonly) RCEngine *engine;

@end


@implementation RCRaceCar

- (instancetype)init
{
   ...

   // Create the engine. Note that it cannot be customized or
   // mocked out without modifying the internals of RCRaceCar.
   _engine = [[RCEngine alloc] init];

   return self;
}

@end

```
The dependency injection version of this is a simple change:

依赖注入版本的有一些小小的不同

```
@interface RCRaceCar ()

@property (nonatomic, readonly) RCEngine *engine;

@end


@implementation RCRaceCar

// The engine is created before the race car and passed in
// as a parameter, and the caller can customize it if desired.
- (instancetype)initWithEngine:(RCEngine *)engine
{
   ...

   _engine = engine;

   return self;
}

@end

```
###Lazily Initialized Dependencies
###依赖惰性初始化

Some objects aren't needed until a later time, after initialization, or may never be needed at all. An example (before using dependency injection) might look like this:

一些对象有时会在很后面才会被用到，有时甚至在初始化之后不会被用到，举个例子（在依赖注入之前）也许看起来是这样子的：

```
@interface RCRaceCar ()

@property (nonatomic) RCEngine *engine;

@end


@implementation RCRaceCar

- (instancetype)initWithEngine:(RCEngine *)engine
{
   ...

   _engine = engine;

   return self;
}

- (void)recoverFromCrash
{
   if (self.fire != nil) {
      RCFireExtinguisher *fireExtinguisher = [[RCFireExtinguisher alloc] init];
      [fireExtinguisher extinguishFire:self.fire];
   }
}

@end

```

In this case the race car hopefully never crashes, and we never need to use our fire extinguisher. Because the chance of needing this object is low, we don't want to slow down the creation of every race car by creating it immediately in initialization. Alternatively, if our race car needs to recover from multiple crashes, it will need to create multiple extinguishers. For these situations, we can use a factory.

在这种情况下，赛车希望不会出事，而我们也不需要使用灭火器。由于需要该对象的机会很低，我们不想每一个赛车的创建的时候慢下来。另外，如果我们的赛车需要从多个事故恢复，就需要创建多个灭火器。针对这些情况，我们可以使用工厂。


Factories are standard Objective-C blocks that require no arguments and return a concrete instance of an object. An object can control when its dependencies are created using these blocks without needing to know the details of how to create them.

工厂标准的Objective-C块不需要参数和返回一个对象的实例。一个对象可以控制当其依赖的是使用这些块而不需要详细了解如何创建。

Here's an example using dependency injection that uses a factory to create our fire extinguisher:
这里是一个例子使用了依赖注入和工厂模式来创建灭火器。

```
typedef RCFireExtinguisher *(^RCFireExtinguisherFactory)();


@interface RCRaceCar ()

@property (nonatomic, readonly) RCEngine *engine;
@property (nonatomic, copy, readonly) RCFireExtinguisherFactory fireExtinguisherFactory;

@end


@implementation RCRaceCar

- (instancetype)initWithEngine:(RCEngine *)engine
       fireExtinguisherFactory:(RCFireExtinguisherFactory)extFactory
{
   ...

   _engine = engine;
   _fireExtinguisherFactory = [extFactory copy];

   return self;
}

- (void)recoverFromCrash
{
   if (self.fire != nil) {
      RCFireExtinguisher *fireExtinguisher = self.fireExtinguisherFactory();
      [fireExtinguisher extinguishFire:self.fire];
   }
}

@end

```


Factories are also useful in cases where we need to create an unknown number of a dependency, even if that creation is done during initialization. For example:

工厂模式在我们创建未知数量的依赖的时候也是非常有用的，即使是在初始化期间，例子如下：

```
@implementation RCRaceCar

- (instancetype)initWithEngine:(RCEngine *)engine 
                  transmission:(RCTransmission *)transmission
                  wheelFactory:(RCWheel *(^)())wheelFactory;
{
   self = [super init];
   if (self == nil) {
      return nil;
   }

   _engine = engine;
   _transmission = transmission;

   _leftFrontWheel = wheelFactory();
   _leftRearWheel = wheelFactory();
   _rightFrontWheel = wheelFactory();
   _rightRearWheel = wheelFactory();

   // Keep the wheel factory for later in case we need a spare.
   _wheelFactory = [wheelFactory copy];

   return self;
}

@end

```

##Avoiding Cumbersome Configuration
##避免麻烦的配置

If objects shouldn't be allocated within other objects, where are they allocated? And aren't all these dependencies hard to configure? Aren't most of them the same every time? The solution to these problems lies in class convenience initializers (think +[NSDictionary dictionary]). We'll pull the configuration of our object graph out of our normal objects, leaving them with pure, testable, business logic.

如果对象不应该在其他对象分配，他们在哪里分配？是不是所有的这些依赖关系很难配置？大多不都是一样的吗？这些问题的解决在于类方便的初始化（想一想+[NSDictionary dictionary]）。我们会把我们的对象图的配置移出我们正常的对象，把他们抽象出来，测试，业务逻辑。

Before adding a class convenience initializer, make sure that it's necessary. If an object has only a few arguments to its init method, and those arguments don't have reasonable defaults, then a class convenience initializer isn't necessary and the caller should use the standard init method directly.

在我们添加简单初始化方法之前，先确认它是有必要的。如果一个对象只有少量的参数在初始化方法之中，这些参数没有规律，那么简单初始化方法是没有必要的。调用者应该使用标准初始化方法。

To configure our object we'll gather our dependencies from four places:

我们需要从四个地方搜集依赖来配置我们的对象：

* **Values without a reasonable default** These include booleans or numbers that will likely be different for each instance. These values should be passed into the class convenience initializer as arguments.
* **Existing shared objects** These should be passed into the class convenience initializer as arguments (such as a pit radio frequency, for example). These are the objects that previously might have been assessed as singletons or via "parent" pointers.
* **Newly created objects** If our object does not share this dependency with another object, then a collaborator object should be newly instantiated in the class convenience initializer. These are objects that previously were allocated directly within the implementation of the object.
* **System singletons** These are Cocoa-provided singletons and can be accessed directly from the singleton. This applies to singletons such as [NSFileManager defaultManager], where there is a reasonable expectation that only one instance will ever be used in your production app. There is more on system singletons below.
A class convenience initializer for our race car would look like this:

* **没有明确默认值的变量**	这些包括布尔类型和数字类型的有可能在每个实例中是不同的。这些变量应该在简单初始化方法的参数中。
* **共享对象** 这些应该在简单初始化方法中作为参数（例如电台频率），这些对象先前可能被访问作为一个单例或者通过父指针。
* **新创建的对象** 如果我们的对象不与另一个对象分享这种依赖关系，合作者对象应该是新实例化的类中简单初始化。这些都是以前分配的对象直接在该对象的实现。
* **系统单例** Cocoa提供了很多单例可以直接被访问，例如`[NSFileManager defaultManager]`，那里有一个明确的目的只需要一个实例被使用在应用中，还有很多单例在系统中。

A class convenience initializer for our race car would look like this:

一个对于赛车的简单初始化看起来如下：

```
+ (instancetype)raceCarWithPitRadioFrequency:(RCRadioFrequency *)frequency;
{
   RCEngine *engine = [[RCEngine alloc] init];
   RCTransmission *transmission = [[RCTransmission alloc] init];

   RCWheel *(^wheelFactory)() = ^{
      return [[RCWheel alloc] init];
   };

   return [[self alloc] initWithEngine:engine 
                          transmission:transmission 
                     pitRadioFrequency:frequency 
                          wheelFactory:wheelFactory];
}
```

Your class convenience initializer should live where it feels most appropriate. A commonly used (and reusable) configuration will live in the same .m file as the object, while a configuration that is used specifically by a Foo object should live in a @interface RaceCar (FooConfiguration) category and be named something like fooRaceCar.

你的类的简单初始化方法应该是最合适的。一个经常被用到的（可重用）的配置在一个.m文件中，而一个特定的配置文件应该在在`@interface RaceCar (FooConfiguration)`的类别文件中比如`fooRaceCar `。

##System Singletons
##系统单例

在Cocoa的许多对象中，只有一种实例将一直存在。
例如`[UIApplication sharedApplication]`, `[NSFileManager defaultManager]`, `[NSUserDefaults standardUserDefaults]`, 和 `[UIDevice currentDevice]`，如果一个对象对他们存在依赖，那么应该在初始化的参数中包含他们，即使也许在你的代码中只有一个实例，你的测试可能要模拟实例或创建一个实例每个测试避免测试相互依存。


It's recommended that you avoid creating globally-referenced singletons in your own code, and instead create a single instance of an object when it is first needed and inject it into all the objects that depend on it.

建议避免创建全局引用单例，而是创建一个对象的单个实例当它第一次被需要的时候并把它注入到所有依赖它的对象中去。

##Unmodifiable Constructors
##不可变构造器

Occasionally there are cases where the initializer/constructor for a class can't be changed or can't be called directly. In these cases you should use setter injection. For example:

偶尔在某个类的构造器或者初始化方法不能被改变或者调用的时候，我们可以使用setter注入。例如：

```
// An example where we can't directly call the the initializer.
RCRaceTrack *raceTrack = [objectYouCantModify createRaceTrack];

// We can still use properties to configure our race track.
raceTrack.width = 10;
raceTrack.numberOfHairpinTurns = 2;

```
Setter injection allows you to configure the object, but it introduces additional mutability that must be tested and handled in the object's design. Luckily, there are two primary scenarios that cause initializers to be inaccessible or unmodifiable, and both can be avoided.

setter注入允许你配置对象，但是它也引入了易变性，你必须进行测试和处理，幸运的是，有两个场景导致初始化方法不能改变和调用，他们都是可以避免的。

###Class Registration
###类注册

The use of the "class registration" factory pattern means that objects cannot modify their initializers.

使用类注册的工厂模式意味着不能修改初始化方法。

```
NSArray *raceCarClasses = @[
   [RCFastRaceCar class],
   [RCSlowRaceCar class],
];

NSMutableArray *raceCars = [[NSMutableArray alloc] init];
for (Class raceCarClass in raceCarClasses) {
   // All race cars must have the same initializer ("init" in this case).
   // This means we can't customize different subclasses in different ways.
   [raceCars addObject:[[raceCarClass alloc] init]];
}
```

An easy replacement for this is to use factory blocks rather than classes to declare your list.

一个简单的替代方法就是使用工厂块而不是类的声明列表。


```
typedef RCRaceCar *(^RCRaceCarFactory)();

NSArray *raceCarFactories = @[
   ^{ return [[RCFastRaceCar alloc] initWithTopSpeed:200]; },
   ^{ return [[RCSlowRaceCar alloc] initWithLeatherPlushiness:11]; }
];

NSMutableArray *raceCars = [[NSMutableArray alloc] init];
for (RCRaceCarFactory raceCarFactory in raceCarFactories) {
   // We now no longer care which initializer is being called.
   [raceCars addObject:raceCarFactory()];
}
```
###Storyboards
###故事版

Storyboards offer a convenient way to lay out your user interface, but they also introduce problems when it comes to dependency injection. In particular, instantiating the initial view controller in a storyboard does not allow you to choose which initializer is called. Similarly, when following a segue that is defined in a storyboard, the destination view controller is instantiated for you without letting you specify the initializer.

故事提供了一个方便的方式来设计你的用户界面，但也带来一些问题的时候。特别是依赖注入，实例化初始视图控制器在故事板中不允许你选择初始化方法。同样，当下面的事情就是在故事板中定义，目标视图控制器被实例化也不允许你指定的初始化方法。

The solution here is to avoid using storyboards. This seems like an extreme solution, but we've found that storyboards have other issues scaling with large teams. Additionally, there's no need to lose most of the benefit of storyboards. XIBs offer all of the same benefits that storyboards provide, minus segues, but still let you customize the initializer.

解决这个问题的办法是避免使用故事板。这似乎是一个极端的解决方案，但是我们发现故事板有其他问题在大型团队的开发中。此外，没有必要失去大部分故事版的好处。xibs提供所有相同的好处和故事板，除了`segues`，但仍然让你自定义初始化。

##Public vs. Private
##公共和私有

Dependency injection encourages you to expose more objects in your public interface. As mentioned, this has numerous benefits. But when building frameworks, it can significantly bloat your public API. Prior to applying dependency injection, public Object A may have used private Object B (which in turn uses private Object C), but Objects B and C were never exposed outside of the framework. With dependency injection Object A has Object B in its public initializer, and Object B in turn makes Object C public in its initializer.

依赖注入鼓励你暴露更多的对象在你的公共接口。如前所述，这有许多好处。但当你构建框架，它会明显的膨胀公共API。使用依赖注入之前，公共对象A可能使用私有对象B（这反过来使用私有对象C），但对象B和C分别从未曝光的外部框架。使用依赖注入的对象A会使对象B暴露在公共初始化方法，对象B反过来将对象C暴露在公共初始化方法。。

```
// In public ObjectA.h.
@interface ObjectA
// Because the initializer uses a reference to ObjectB we need to
// make the Object B header public where we wouldn't have before.
- (instancetype)initWithObjectB:(ObjectB *)objectB;
@end

@interface ObjectB
// Same here: we need to expose ObjectC.h.
- (instancetype)initWithObjectC:(ObjectC *)objectC;
@end

@interface ObjectC
- (instancetype)init;
@end
```
Object B and Object C are implementation details, and you don't want the framework consumer to have to worry about them. We can solve this by using protocols.

对象B和对象C都是具体的实现，但你并不需要框架的使用者来担心他们，我们可以通过协议的方式来解决。

```
@interface ObjectA
- (instancetype)initWithObjectB:(id <ObjectB>)objectB;
@end

// This protocol exposes only the parts of the original ObjectB that
// are needed by ObjectA. We're not creating a hard dependency on
// our concrete ObjectB (or ObjectC) implementation.
@protocol ObjectB
- (void)methodNeededByObjectA;
@end
```

##Closing
##结束语

Dependency injection is a natural fit for Objective-C (and Swift by extension). Applied properly it makes your codebase easy to read, easy to test, and easy to maintain.

依赖注入在 Objective-C(同样在Swift)，合理的使用会使你的代码更加易读、易测试和易维护。



