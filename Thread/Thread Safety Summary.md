#线程安全摘要

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/ThreadSafetySummary/ThreadSafetySummary.html#//apple_ref/doc/uid/10000057i-CH12-SW1) 

 翻译人:王谦 翻译日期:2015.10.12
 
这个附录描述 OS X 和 iOS 里高级线程安全的一些关键framework。这个附录中的信息可能发生变化。


##Cocoa

以下包含从多线程使用 Cocoa 的参考:

* 线程安全通常是不可改变的。一旦你创建他们,你能从线程安全的通过这些线程对象。另一方面,线程安全通常是没有线程安全的。在一个应用程序线程里使用可变对象,应用程序必须适当的同步。更多信息,看[Mutable Versus Immutable](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/ThreadSafetySummary/ThreadSafetySummary.html#//apple_ref/doc/uid/20000736-126010)。

* 许多对象认为 “线程安全” 仅仅从多线程使用是不安全的。这些对象大多对于任何线程每次只要一个线程。就是说对象专门限制应用程序的主线程调用出来。

* 应用程序主线程是可靠的处理事件。虽然 Application Kit 连续运转,如果其他线程是需要线程路径,可以不按顺序运作。

* 如果你想要抽出一个视图使用线程,在`lockFocusIfCanDraw` 和 `unlockFocus` 方法 `NSView`之间排除全部抽出代码。
* Cocoa 使用 POSIX 线程,你必须首先放置 Cocoa 到多线程模式之中。更多信息,看[ Using POSIX Threads in a Cocoa Application](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/CreatingThreads/CreatingThreads.html#//apple_ref/doc/uid/20000738-125024) 。

##Foundation Framework Thread Safety

Foundation Framework 是线程安全的而 Application Kit framework 不是,这是一种错误的想法。遗憾的是,这是一个多少有点误解的总概括。每一个框架都有线程安全的区域和不是线程安全的区域。以下部分描述 Foundation framework 的线程安全。

###Thread-Safe Classes and Functions

以下类和函数通常被认为是线程安全的。没有首先从多线程获得锁,你能使用相同的实例。

	NSArray
	NSAssertionHandler
	NSAttributedString
	NSCalendarDate
	NSCharacterSet
	NSConditionLock
	NSConnection
	NSData
	NSDate
	NSDecimal functions
	NSDecimalNumber
	NSDecimalNumberHandler
	NSDeserializer
	NSDictionary
	NSDistantObject
	NSDistributedLock
	NSDistributedNotificationCenter
	NSException
	NSFileManager (in OS X v10.5 and later)
	NSHost
	NSLock
	NSLog/NSLogv
	NSMethodSignature
	NSNotification
	NSNotificationCenter
	NSNumber
	NSObject
	NSPortCoder
	NSPortMessage
	NSPortNameServer
	NSProtocolChecker
	NSProxy
	NSRecursiveLock
	NSSet
	NSString
	NSThread
	NSTimer
	NSTimeZone
	NSUserDefaults
	NSValue
	NSXMLParser
	
对象分配,保留计数功能区域和内存功能



###Thread-Unsafe Classes

以下类和函数通常不是线程安全的。在大多数情况下,你能从任何线程使用这些类,只要你使用他们,一次只有一个线程。检查类文档的更多细节。

	NSArchiver
	NSAutoreleasePool
	NSBundle
	NSCalendar
	NSCoder
	NSCountedSet
	NSDateFormatter
	NSEnumerator
	NSFileHandle
	NSFormatter
	NSHashTable functions
	NSInvocation
	NSJavaSetup functions
	NSMapTable functions
	NSMutableArray
	NSMutableAttributedString
	NSMutableCharacterSet
	NSMutableData
	NSMutableDictionary
	NSMutableSet
	NSMutableString
	NSNotificationQueue
	NSNumberFormatter
	NSPipe
	NSPort
	NSProcessInfo
	NSRunLoop
	NSScanner
	NSSerializer
	NSTask
	NSUnarchiver
	NSUndoManager
	
 用户名和主目录功能
 
 注意,尽管`NSSerializer`, `NSArchiver`, `NSCoder`,和 `NSEnumerator` 对象本身就是线程安全的,他们就在这里列出,当他们正在使用来改变数据对象的包裹,因为这是不安全的。举一个例子,在一个文档, 更改对象图表被归档是不安全的。对于一个枚举器,任何线程改变枚举器采集是不安全的。
 
 
###Main Thread Only Classes
只有在应用程序的主线程以下类必须使用。

	NSAppleScript


###Mutable Versus Immutable

不可变的对象通常是线程安全的;一旦你创建他们,你能安全通过这些对象和线程。当然,使用不可变对象的时候,你仍然需要记得正确的使用引用计数。如果你不适当的释放一个对象没有保留,你以后可能会导致一个异常。

可变的对象通常是没有线程安全的。在一个应用程序线程使用可变对象,应用程序必须使用锁同步访问它们。(更多信息,看[Atomic Operations]())。总之,集合类(举一个例子,NSMutableArray,NSMutableDictionary) 就转变而言的时候是没有线程安全的。也就是说,如果一个或多个线程正在改变相同的数组,会发生问题。你必须锁在存在读和写的地方保证线程安全。

虽然要求的方法返回一个不可变的对象,你应该不仅仅是承担返回的对象是不可变的。这取决于实现的方法,返回的对象可能是可变的或不可变的。举一个例子,由于其实现,一个方法的返回类型是  [NSString]() 或许事实上则返回一个 [NSMutableString]()。


###Class Initialization

Objective-C 系统的运行时发送一个 `initialize` 初始化消息到每个类对象,在这之前类接收任何其他的消息。这是给类一个改变设置它之前使用的运行时环境。在一个多线程的应用程序,运行时保证只有一个线程－－线程碰巧发生首先发送一个消息到类－－执行`initialize` 方法。如果第二个线程尝试发送消息到类,虽然第一个线程仍然在`initialize` 方法,第二个线程 blocks 直到`initialize`方法执行完成。期间,第一个线程继续在类中调用其他方法。`initialize` 方法应该不依靠在第二个线程中调用的类方法;如果真的,这两个线程变得陷入僵局。

由于在 OS X 10.1.x 早先版本的一个 bug。一个线程可以发送消息到一个类,在这之前另一个线程成功执行类的 `initialize` 方法。线程可以访问的值没有充分的初始化,可能会应用程序崩溃。如果你遇到这种问题,你需要在两者之中采用锁来阻止访问值,直到它们初始化或强迫类本身初始化之后,在这之前执行多线程。

###Autorelease Pools

每个线程维护自己的堆栈 [NSAutoreleasePool]() 对象。在当前的堆栈中,Cocoa 期望那里有自动释放池一直可用。如果一个池不可用,对象没有得到释放那么你的内存会泄漏。一个 `NSAutoreleasePool`对象是自动创建和破坏在主线程基于  Application Kit 的应用程序,但是第二个线程(and Foundation-only applications)在使用 Cocoa 之前必须创建它们自己。如果你的线程是长期存在的和潜在生成大量的自动释放对象,你应该定期破坏和创建自动释放池(像应用程序工具包在主线程);否则,自动释放对象会积累在你的内存中占用空间。如果你的分开线程不使用 Cocoa,你不需要创建一个自动释放池。


###Run Loops
