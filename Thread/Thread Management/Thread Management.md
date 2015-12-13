#线程管理


[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/CreatingThreads/CreatingThreads.html#//apple_ref/doc/uid/10000057i-CH15-SW2) 

 翻译人:Chaos 翻译日期:2015.10.12 
 

  OS X 或 IOS 的每一个进程（或APP）都由一个或多个线程组成，每一个线程代表一段代码的执行过程。应用开始运行的时候都是单线程的，也就是主线程（main thread）。应用可以衍生出多个其他的线程，每个线程都执行一段具体功能的代码。

  当应用产生新的线程时，这些线程就会成为应用内部单独的的实体。每一个线程都被处理器核心分开和安排去拥有自己的运行堆（stack）。一个线程可以和其他线程或进程传达相连，通过 I/O 流操作，并且做别的任何事你可能都会用到它。因为它们在同一个进程空间内，所有的线程在一个应用里面读取相同的虚拟内存并且拥有相同级别的权限。
  
  这一章节将介绍 OS X和 IOS 中线程技术并举例如何将这技术应用到你的程序中。

> 注意:过往版本的 MAC OS 线程解构和更多关于线程的背景信息，请看 TN2028, “Threading Architectures”。


##线程成本

就内存占用和性能来说，线程对程序有实际的消耗。每一个线程都需要占用核心内存和程序内存空间。核心结构需要管理你的线程和整合被储存在核心内存的计划表。线程堆空间和每个线程数据被储存在程序内存空间。当你创造一个线程时，大部分这些解构被创造和初始化，因为需要和内核交互所以相当昂贵。
表2-1 列出了在应用中创建一个新的用户线程时消耗的大概成本（costs）。这些成本都是可配置的,如为次要线程分配堆栈空间的数量。创建一个线程耗费的时间知识一个粗略的估计，应该只用于相对彼此的比较。线程的创建时间可能根据处理器负载,计算机的速度,系统和程序内存的数量的不同有很大波动。

|项目|大约成本|备注 |
|:---|:---|:---|
|内核数据结构| 约1KB |内存被用于存储线程数据结构和属性,其中大部分被分配作为连接分配内存,因此不能交换到磁盘中。|
|栈空间| 512 KB(次要线程) <br>8 MB(OS X主线程)<br>1 MB(iOS主线程) |辅助线程的最小允许堆栈大小是16 KB并且堆栈大小必须是4 KB的倍数。|
|创建时间| 约90微秒 |这个值是初始调用创建线程时和线程的入口点程序开始执行时的间隔。数据通过分析生成的均值和中位数，由基于在intel 2 GHz酷睿双核处理器,1 GB的RAM运行OS X v10.5的MAC上创建线程时测出。|


>注意:由于底层内核的支持,操作对象通常可以更快地创建线程。而不是每次都从头创建线程,为了节省分配时间它们使用的线程池已经储存在内核中。有关使用操作对象的更多信息,请参见并发编程指南。

另一个需要考虑的是编写代码时的生产成本。设计一个线程化的应用需要对应用程序的数据结构做出根本性的改变。这样做可能需要避免使用同步，对于设计很差的程序来说会有巨大的性能损失。设计的数据结构,调试线程代码问题,会增加开发一个线程化应用的时间。设法减少这些时间成本会让程序运行时产生更大的问题。	除非你的线程浪费太多时间在等待线程锁或什么都不做。

##创建线程

创建底层线程是相对简单的。在所有情况下,你必须有一个函数或方法作为线程的主要入口点并且必须使用一个可用的常规线程来开始你的线程。下面的小节介绍通过最常用线程技术创建基本线程的过程。使用这些技术创建的线程继承一组默认的属性,取决于您所使用的技术。如何配置你的线程信息,请参见配置线程属性。,根据您所使用的技术的不同，使创建的线程继承一组默认的属性。配置你的线程的更多信息,请参见配置线程属性。

##使用 NSThread

使用 NSThread 类创建线程的两种方法：

* 使用 [detachNewThreadSelector:toTarget:withObject:]() 类方法创建新的线程。

* 创建新的 `NSThread` 对象并调用 `start` 方法。 （只支持 iOS 和 OS X v10.5及以后版本）


两种技术都在应用程序中创建一个独立线程。分离线程意味着当线程退出时线程的资源将有系统自动回收。这是你不必要通过代码参与线程资源控制工作。
因为 detachNewThreadSelector:toTarget:withObject:  所以版本的 OS X 支持此方法，也常见于使用了线程的Cocoa应用。分离一个新线程，只需提供你想作为线程入口的方法名（指定选择器）。定义对象的方法，和初始时你想传给线程的数据。下面的例子展示了对当前对象调用常规方法产生一个线程。


	[NSThread detachNewThreadSelector:@selector(myThreadMainMethod:) toTarget:self withObject:nil];
	

在 OS X v10.5 之前，使用 NSThread 来产生线程。在 OS X v10.5 ,支持添加创建 NSThread 对象时不立即产生相应的新线程。(这也可以在 iOS 的支持。)这种支持使它可以在线程启动之前获取和设置不同的线程属性。它还可以使线程对象引用以后运行的线程。

在 OS X v10.5 及之后版本中简单初始化一个 NSThread 对象，使用 [initWithTarget:selector:object:]()  方法。这方法和 detachNewThreadSelector:toTarget:withObject: 方法获取完全相同的信息，并使用它初始化一个新的 NSThread 实例。然而他不开始一个线程。你可以明确调用 start 方法来开始。下面是一个例子：

	NSThread* myThread = [[NSThread alloc] initWithTarget:self
                                        selector:@selector(myThreadMainMethod:)
                                        object:nil];
	[myThread start];  // Actually create the thread


>注意:另一个使用 initWithTarget:selector:object：是子类 NSThread 和覆盖它的主要方法。你会使用这种覆写的方法来实现线程的主要入口点。有关更多信息,请参见 [NSThread Class Reference] 中的子类笔记。

[NSThread Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSThread_Class/index.html#//apple_ref/doc/uid/TP40003746

如果是一个正在运行的 NSTread 对象，可以对应用内任何对象调用[performSelector:onThread:withObject:waitUntilDone]() 方法来发送信息到线程。OS X v10.5支持线程上执行选择器(除了主线程)，它是线程间一种方便的沟通方式。（ IOS 也支持。）使用这种技术发送的消息直接由其他线程执行，作为常规 run-loop 的一部分。这意味着目标线程必须运行在 Run-Loop 中。可能你仍然需要某种形式的沟通方法，但是他比建立线程之间的通讯更简单。

>注意:虽然适合偶尔线程之间的通信,但不应该使用`performSelector:onThread:withObject:waitUntilDone:`方法来做时间严重情况下或频繁的线程之间的通信。

其他线程通信选项的列表,请参阅 [Setting the Detached State of a Thread].

[Setting the Detached State of a Thread]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/CreatingThreads/CreatingThreads.html#//apple_ref/doc/uid/10000057i-CH15-SW3

##Using POSIX Threads

OS X 和IOS提供基于C语言的POSIX thread API来创建线程。这种技术可以用于任何类型的应用程序(包括Cocoa和Cocoa touch应用程序)并且更方便为多个平台写软件。POSIX常规用来创建线程恰到好处, pthread_create。

清单2-1使用 POSIX 创建线程的两种方法。The LaunchThread 函数创建一个新的线程，这个线程的主线程由 PosixThreadMainRoutine 函数实现。因为POSIX创建的线程默认下是可接合的。这个例子改变线程属性去创建一个分离的线程。将线程标记为分离给使系统可以当线程退出时立即收回该线程的资源。

**清单 2-1 C语言创建一个线程**

	#include <assert.h>
	#include <pthread.h>
 
	void* PosixThreadMainRoutine(void* data)
	{
    // Do some work here.
 
    return NULL;
	}
 
	void LaunchThread()
	{
    // Create the thread using POSIX routines.
    pthread_attr_t  attr;
    pthread_t       posixThreadID;
    int             returnVal;
 
    returnVal = pthread_attr_init(&attr);
    assert(!returnVal);
    returnVal = pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_DETACHED);
    assert(!returnVal);
 
    int     threadError = pthread_create(&posixThreadID, &attr, &PosixThreadMainRoutine, NULL);
 
    returnVal = pthread_attr_destroy(&attr);
    assert(!returnVal);
    if (threadError != 0)
    {
         // Report an error.
    }
	}
	
如果你将前面清单中的代码添加到源文件中并调用 LaunchThread 函数，将在应用中创建一个新的分离线程。当然，用这段代码创建的线程不会做任何事情。这个线程登陆，并几乎同时立即退出。使事情更有趣去做一些实际的工作，你需要添加代码到 PosixThreadMainRoutine 函数中。为保证线程知道去做什么工作，创建线程时你可以传递给他指向数据的指针。传递指针使用 pthread_create 函数的最后一个参数。

通过在线程间建立沟通路径，可以使新线程和主线程间沟通信息。对于基于c的应用程序,有几个线程间通信的方法,包括使用端口,条件,或共享内存。对于长时间存在的线程，你应该设置一些内线程交流机制使应用主线程可以去检查线程状态并在应用退出时关闭它们。

关于 POSIX线程函数的更多信息，请查阅 [pthread]() 手册页。


###使用NSObject衍生一个线程
在 IOS 和 OS X v10.5 及之后，所以的对象可以衍生一个新的线程并通过它来执行它们的方法。[performSelectorInBackground:withObject:]()方法创建了一个新的分离线程并且用特殊的方法作为新线程的入口。例如，如果你有一些对象（如变量myObj）和对象拥有一个方法（doSomething）并想把它们运行在后台线程中，你需要用下面的代码:

	[myObj    performSelectorInBackground:@selector(doSomething) withObject:nil];
	
调用这段代码的效果是一样的，和你对 NSThread 调用 [detachNewThreadSelector:toTarget:withObject:]()伴随当前对象，选择器，和参数对象作为参数。
将引发使用默认配置的新线程并开始运行。选择器中，你必须配置线程就像普通线程一样。例如，如果你打算使用它需要建立一个autorelease池(如果你没有使用垃圾收集)和配置线程的运行循环。更多信息关于如何配置新线程，请查阅 [Configuring Thread Attributes]()。


###在Cocoa应用中使用POSIX Threads

虽然在 Cocoa 应用中 NSThread 类是主要的接口，你也可以自由的使用 POSIX Threads 如果它对你更方便的话。例如，如果你已经有代码使用过 POSIX 并且不想重写时。如果你计划使用 POSIX 线程在 Cocoa 应用程序中,您仍然应该意识在接下来的部分中的Cocoa和线程之间的交互和遵守的指导方针。



####保护Cocoa框架

对于多线程应用程序,可可框架使用锁和其他形式的内部同步,以确保他们的行为正确。为了防止在单线程下这些锁降低性能，然而，Cocoa 不创建它们直到应用程序第一次使用 NSThread 类创建新的线程。如果你只使用 POSIX 线程例程衍生线程, Cocoa 不接收通知并且需要知道您的应用程序是多线程的。当这种情况发生时,运行涉及 Cocoa 框架时可能会破坏应用程序或崩溃。

想要让 Cocoa 知道你打算使用多个线程，你只需通过 NSThread 类生成一个线程并让此线程立即退出。线程入口点不需要做任何事。只生成一个线程使用的行为 NSThread 足以确保 Cocoa 框架所需的锁到位。
如果你不确定 Cocoa 是否知道你的应用是多线程的，你可以使用 NSThread 的 isMultiThreaded 方法来判断。


####混合POSIX和线程锁

在同一个程序中混合使用 POSIX 和 Cocoa 锁是安全的。Cocoa 锁和状况对象本质上只是包装  POSIX 互斥锁和状况。对于一个给定的锁,无论如何,你必须总是使用相同的接口来创建和操纵这个锁。换句话说,您不能使用一个 Cocoa NSLock 对象来操作使用 [pthread_mutex_init]() 函数创建互斥锁,反之亦然。


##配置线程属性

在您创建一个线程,有时,你可能想要配置线程环境的不同部分。以下部分描述当你可能使他们时的一些变化。


###配置一个线程的堆栈大小

对于你创建的每个新线程，系统在你拥有的空间分配特定数量的内存作为线程的堆栈。堆栈管理堆栈框架,也是任何线程被声明的局部变量。分配给线程的内存大小列在线程成本中
如果你想改变给定线程的堆栈大小,你必须创建线程前设定。所有的线程技术提供一些方法设置堆栈大小,尽管使用 NSThread 设置栈大小只能在 iOS和OS X v10.5 及之后的版本。

**清单 2-2 设置线程的堆栈尺寸**

|技术||
|:---|:---|
|Cocoa|在IOS和OS X v10.5及之后的版本，分配和初始化一个对象（不要使用detachNewThreadSelector:toTarget:withObject: 方法）。对线程对象调用start方法之前，先用 setStackSize:方法设定堆栈大小。|
|POSIX|创建一个新的pthread_attr_t结构和使用pthread_attr_setstacksize函数来改变默认的堆栈大小。当创建线程时将属性传递给pthread_create函数。|
|Multiprocessing Services|当创建线程时，传递合适的堆栈大小值到MPCreateTask函数。|

表2 - 2列出了每种技术的不同选项。


###配置本地线程存储

每个线程包含一个键-值对的字典,可以从线程的任何地方访问。你可以用这个字典储存你想持久遍及在线程运行中的信息。例如,您可以使用它来存储线程的运行循环持续通过多次迭代的状态信息。
Cocoa 和 POSIX 用不同的方式储存线程字典，所以你不能混合和搭配调用者两种技术。只要你在线程代码中使用了其中一中的技术，无论怎样，最好的结果应该是相同的。在 Cocoa 中，你使用 NSThread 对象的 threadDictionary 方法检索一个 NSMutableDictionary 对象，你可以添加线程需要的任何键。在 POSIX ,您使用 pthread_setspecific 和 pthread_getspecific 功能来设置和获取线程的键和值。



###设定线程分离状态

大多数高级的线程技术默认创建分离线程。大多数情况下，分离线程更好。因为它容许线程结束时系统自动释放线程的数据结构。分离线程也不需要与你的程序有明确的交流。这意味着从线程取回的结果由你来裁定。相比之下，系统对可连接线程不回收资源可能造成另一个线程明确连接时阻塞线程的连接执行。
你可以把可接合线程看做子线程。虽然他们仍然作为独立的线程运行,可接合线程必须接入到一个资源可以被系统回收线程。可接合线程也提供了一个明确的方式从一个存在的线程传递数据到其他的线程。在退出之前，可接合线程能够向 pthread_exit 功能传递数据指针或其他返回值。然后另一个线程通过调用 pthread_join 功能可以认领这些数据。

>重要:在应用程序退出时,分离线程可以立即终止但可接合线程不能。每个可接合线程之前,必须加入过程允许退出。因此可接合线程可能更可取的情况下线程是不应该打断做重要的工作,比如保存数据到磁盘。每个可接合线程必须在进程允许退出之前连接。因此可接合线程可能更适宜的情况是在运行不应该被打断做重要的工作时，比如保存数据到磁盘。

如果你要想创建可接合线程,唯一的方法是使用 POSIX 线程。POSIX 默认创建可接合线程。要把一个线程标记被分离或可接合，要在创建线程之前使用 `pthread_attr_setdetachstate` 修改线程属性。线程开始后，你可以调用 `pthread_detach` 函数把接合线程改为分离线程。关于这些 POSIX 线程函数的更多信息,请参见 pthread 手册页。如何加入一个线程的信息,看到[pthread_join]()手册页。


###设定线程优先级

任何创建的新线程都有一个与之关联的默认优先级。内核的调度算法根据线程优先级安排哪些线程先运行，优先级高的比低的更大几率先运行。更高的优先级并不能保证一个特定数量的线程执行时间,只是它更可能被调度程序所选择优先执行。

>重要： 把你的线程优先级设置为默认值是个好做法。增加一些线程的优先级同时也意味着增加相对较低优先级的饥饿度。如果您的应用程序包含高优先级和低优先级的线程必须相互交流,低优先级线程可能阻止其他线程并创建性能瓶颈。

如果你想修改线程优先级, Cocoa 和 POSIX 提供一种方法。对于 Cocoa 线程,您可以使用 NSThread 的setThreadPriority: 类方法设置当前运行的线程的优先级。对于POSIX线程,使用pthread_setschedparam 函数。有关更多信息,请参见 [NSThread Class Reference]或 [pthread_setschedparam]()手册页。 


##编写你的线程入口例程

在大多数情况下,线程的入口点的例程的结构都是相同的在 OS X 或在其他平台上。你初始化数据结构,做一些工作或选择建立一个循环运行,和清理运行完线程的代码。根据你的设计,编写你的入口例程时可能会有一些额外的步骤。

###创建自动释放池  autorelease

链接 Objective-C 框架的应用程序必须至少创建一个自动释放池。如果应用采取让应用处理对象的保留和释放的管理模式，自动释放池捕获线程上任意需要释放的对象。

如果一个应用使用垃圾回收机制，而不是内存管理模式，就并不必需要创建一个自动释放池。自动释放池在垃圾回收应用程序的存在并不是有害的,并且在大多数情况下只是被忽略掉了。是允许的情况下,代码模块必须支持垃圾收集和内存管理模型。自动释放池在这种情况下,必须存在去支持内存管理模型代码,如果应用程序启用了垃圾收集时它就会被简单的忽略掉。

如果你的应用程序使用了内存管理模型，在线程例程中你做的第一件事是创建一个自动释放池。同样，线程中的最后一件事就是毁掉这个自动释放池。这个池子可以确保可自动释放的对象可以被捕获，尽管知道线程结束它们才被释放。

**表2-2展示了使用自动释放池的线程入口例程的结构**

	- (void)myThreadMainRoutine
	{
    NSAutoreleasePool *pool = [[NSAutoreleasePool alloc] init]; // Top-level pool
 
    // Do thread work here.
 
    [pool release];  // Release the objects in the pool.
	}
	
因为最高级自动释放池知道线程退出才释放它的对象，长时间运行的线程应该多次的创建其他的自动释放池去释放对象。　
　例如,一个使用运行循环的线程每次执行循环一次运行创建和释放一个自动释放池。频繁地释放对象可以防止应用程序的内存增长占用太大空间,这可能导致性能问题。与任何绩效行为,你应该测量代码的实际性能和调优适当使用自动释放池。
　　
　　关于内存管理和自动释放池的更多信息,参考[Advanced Memory Management Programming Guide]。
　　
[Advanced Memory Management Programming Guide]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/MemoryMgmt.html#//apple_ref/doc/uid/10000011i
　　
###设置异常处理程序

如果你程序捕获并处理异常，那么你的线程代码也要准备好处理可能发生的异常。最好最好是在发生异常的点处理异常，但是如果捕获异常失败会导致程序退出。在你的线程入口例程的最后加入一个 try/catch 允许您捕获任何未知的异常,并能提供一个合适的回应。当你使用 Xcode 编写程序时，你可以使用 C++ 或者 OC 的异常处理风格。在 Objective - c 中如何提高信息设置和捕获异常,请查看异常编程。

###建立运行循环

　你想运行在一个单独的线程，编写代码时你有两个选择。第一个是写一个线程的代码作为一个很少或没有中断执行的长期的任务,线程退出时完成。第二个选择是把你的线程写入一个循环,它处理动态地到来的请求。第一个选择不需要为代码写特殊设置。直接开始你需要做的工作就可以。第二个选择涉及设置线程循环。OS X 和 IOS 内置支持每个线程实现运行循环。应用程序框架自动启动循环运行应用程序的主线程。如果你创建任何辅助线程,您必须配置循环运行,手动启动它。
　　
关于使用和配置运行循环的更多信息,请查阅 [Run Loops]()。

##终止线程

退出一个线程的推荐方法是让它正常退出入口点。虽然 Cocoa , POSIX 和多处理服务提供程序直接杀死线程,但不提倡使用这些例程。直接杀死一个线程阻止了线程对自己的清理。线程分配的内存有潜在被泄露的可能并且当前使用的其他资源可能未被清理干净，这都会创造潜在的问题。

如果你有在一个操作中途需要终止线程的需要，你应该从一开始就设置你的线程对取消或退出信息做出反应。对于长时间运行的操作,这可能意味着定期停止工作去检查是否有这样一个消息到达。如果收到一个请求退出的消息，线程有机会去执行清理并优雅的退出。相反，就只是仅仅继续运行并处理下一块的数据。
响应取消信息的一种方式是使用 run loop 输入源去接受这类信息。表2-3展示了在主线程入口例程中这类代码的结构。（这个示例仅展示了主线程循环的一部分并没有包括创建自动释放池和配置实际需要的工作。）示例在run loop 中安装了一个自定义输入源可以从你的其他线程传递信息。关于设置输入源的信息，请看[Configuring Run Loop Sources](). 在执行总量任务的一部人之后，线程简单运行 run loop 去看输入源是否收到信息。如果没有收到，run loop 立即退出并运行下一块工作。推出的情况通过线程字典的键-值对来沟通。

**Listing 2-3 长任务检查意外状况**

	- (void)threadMainRoutine
	{
    BOOL moreWorkToDo = YES;
    BOOL exitNow = NO;
    NSRunLoop* runLoop = [NSRunLoop currentRunLoop];
 
    // Add the exitNow BOOL to the thread dictionary.
    NSMutableDictionary* threadDict = [[NSThread currentThread] threadDictionary];
    [threadDict setValue:[NSNumber numberWithBool:exitNow] forKey:@"ThreadShouldExitNow"];
 
    // Install an input source.
    [self myInstallCustomInputSource];
 
    while (moreWorkToDo && !exitNow)
    {
        // Do one chunk of a larger body of work here.
        // Change the value of the moreWorkToDo Boolean when done.
 
        // Run the run loop but timeout immediately if the input source isn't waiting to fire.
        [runLoop runUntilDate:[NSDate date]];
 
        // Check to see if an input source handler changed the exitNow value.
        exitNow = [[threadDict valueForKey:@"ThreadShouldExitNow"] boolValue];
    }
	}