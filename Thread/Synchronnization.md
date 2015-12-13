同步
===

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/ThreadSafety/ThreadSafety.html#//apple_ref/doc/uid/10000057i-CH8-SW1)

翻译人:彭兆旻 翻译时间:2015.10.18

APP 中多线程的存在带来了一些潜在的问题,关于执行的多个线程安全访问资源,修改相同资源的两个线程可能会以意想不到的方式干扰对方.例如,一个线程可能会覆盖掉其他人的修改,或者将程序置于不知情和潜在的无效状态.如果你比较幸运,被毁坏的数据可能会带来明显的性能问题或者崩溃,你可以相对容易的追踪并且解决它.相反的,如果你不够幸运,这可能会带来难以察觉的错误,并且不会显示出来直到很久以后.而且这些错误可能需要对你的代码猜想进行显著的修改.

当说到线程安全,最好的保护就是一个好的设计.避免共享数据并且在你的线程中尽量最小化线程之间的关联.然后,完全没有关联的设计并不太可能.在你的线程之间需要互相影响的情况下,你就需要使用同步工具确保安全.

OS X 和 IOS 提供了很多的同步工具供你使用，从提供互斥访问工具，到保证事件按顺序执行的工具。下面的描述了这些工具以及如何在代码中使用他们, 确保安全访问你程序的资源.

##同步工具

为了防止不同的线程意外的更改数据,你可以设计你的程序让它不存在同步的问题,或者你可以使用同步工具.虽然避免同步问题是可取的,但并不是总是可能的.接下来的章节描述了一些同步工具的基本类别,来供你选择.

###原子操作

原子操作是一种简单的同步方式,工作在简单的数据类型上.原子操作的优点是他们不会阻塞竞争的线程.对于一些简单的操作,比如说增加一个变量的值,他们就比锁有更高的性能优势.

OS X 和 IOS 有很多操作来执行 32 位和 64 位的基本的数学运算和逻辑运算.这些操作中包括原子的比较交换 (compare-and-swap)，比较幅值 (test-and-set)，比较清除 (test-and-clear) 操作,支持原子操作的列表,参见 `/usr/include/libkern/OSAtomic.h`头文件或者[atomic](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/atomic.3.html#//apple_ref/doc/man/3/atomic)手册页.

###内存屏障和 Volatile 变量

为了实现最佳的性能,编译器常常对程序集指令重新排序,使得流水线满负荷.作为这种优化的一部分,编译器可能会对访问主存储器的指令重新排序,它认为这样做并不会生成不正确的数据.不幸的是,编译器并不总是能检测到所有的内存依赖操作.如果看似独立的变量实际上相互影响,编译器优化可能会以一种错误的顺序更新这些变量,产生错误的结果.

内存屏障是一种非阻塞的同步工具,用来确保内存的操作都在正确的顺序下.内屏屏障看起来就像一个栅栏,强制处理器先完成屏障之前的读取和存储指令,然后才能执行屏障之后的存储和读取指令.内存屏障通常用来确保一个线程的内存操作（对其他线程可见）始终以期望的顺序执行.在这种情况下如果缺乏内存屏障,可能会使得其他的线程看到看似不可能的结果.(例子详情请参见维基百科 [memory barriers](https://en.wikipedia.org/wiki/Memory_barrier)).要想使用内存屏障,你可以在代码中得合适位置简单的调用 `OSMemoryBarrier`函数.

Volatile 类型的变量有另一种类型的内存约束.编译器通常会把变量的值加载到寄存器来优化指令.对于局部变量,这通常没有问题.然而,如果这个变量对另一个线程是可见的,这样的优化可能会使其他的线程看不到这个变量值的变化.对一个变量使用 `volatile` 关键字,让编译器每次读取变量的时候从内存读.如果说一个变量随时可能被外部改变,而编译器不能够察觉,你可以声明这个变量是 `volatile` 变量.

因为内存屏障和 volatile 变量减少了编译器可以使用的优化方法，所以要谨慎使用，应该仅在需要保证程序正确性的地方.关于使用内存屏障的信息,参见 [OSMemoryBarrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSMemoryBarrier.3.html#//apple_ref/doc/man/3/OSMemoryBarrier).

###锁

锁是最常见的一种同步工具.你可以在你的代码使用锁保护临界区,临界区是一段代码,一次只能有一个线程访问.比如说,临界区可以操纵一个特殊的数据结构,或者一次至多只能支持一个客户端的资源.在临界区的周围设置锁,就排除了其他线程对数据进行更改,从而影响代码的正确性.

表 4-1 列举了通常在程序中会使用到的锁. OS X 和 IOS 对大部分的锁都进行了说明.对不支持的锁的类型,说明列举了为什么这些锁没有在平台实现的原因.

| 锁       | 说明       | 
| ------------- |:-------------:|
| 互斥锁   | 互斥锁是资源的保护屏障.互斥锁是一种信号,一次只允许一个线程访问,如果一个互斥锁正在被使用并且其他线程想要访问他,这个线程就一直阻塞.如果有多个线程竞争同一个锁,一次也只能有一个线程访问|
|递归锁| 递归锁是互斥锁的一种.递归锁允许一个线程在释放它之前获取锁多次.其他的线程则一直阻塞直到锁的拥有者释放了它. 递归锁主要在递归迭代中使用，也可能用在多个方法各自需要获取锁的情形.|
|读写锁| 读写锁也被成为共享—独占锁.通常用在大规模的操作中,如果被保护的数据结构被频繁的读但是只是偶尔改写的情况下,使用读写锁会显著的提高性能.通常的操作中,对数据可以多个线程同时读.当有线程想去写数据,就一直阻塞直到所有读的人都释放了锁.之后便得到锁并更新数据.当一个写的线程等待着锁,读线程就阻塞直到写线程完成.系统只支持 POSIX 线程的读写锁.关于如何使用这些锁,参见[pthread](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/pthread.3.html#//apple_ref/doc/man/3/pthread)手册页.
|分布式锁| 分布式锁在进程级别提供互斥访问.与互斥锁不同的是,分布式锁不阻塞进程.他只是报告给进程锁在忙,让进程自己决定怎么继续|
|自旋锁| 自旋锁会反复轮询锁的状态直到条件变成真.自旋锁通常被用在多处理器系统,等待锁的时间预期比较小.通常情况下，轮询比阻塞线程更有效率，因为阻塞线程会有上下文切换和更新线程的数据结构.系统不提供自旋锁的实现因为他们的轮询性质,但是你可以在特定的情况下轻松使用他们.关于自旋锁可以看`Kernel Programming Guide`.|
|双重检查锁| 双重检查锁,通过在获取锁之前先去测试下是否需要获取,来减少使用锁.因为双重检查锁不安全,所以系统没有明确提供它们也并不提倡使用|

> 大多数类型的锁都包含内存屏障,确保加载和存储操作在进入临界区之前完成.

关于如何使用锁,看 [Using Locks](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/ThreadSafety/ThreadSafety.html#//apple_ref/doc/uid/10000057i-CH8-SW16).

###条件变量

条件变量是另一种类型的信号量，它允许线程间在某个条件为真时相互通知.条件变量被用来确保资源的可用性或者任务以特定的顺序执行.当一个线程测试一个条件变量时,线程阻塞除非条件为真.它持续阻塞直到其他线程显式改变和触发条件.条件变量和互斥锁的区别是它可以允许同一时间多个线程访问条件变量.条件变量是一个通过一些特定的标准,允许不同的线程通过的一个看门人.

你可能使用到条件变量的情况是管理一个挂起的事件池.事件队列将使用条件变量通知等待的线程,当队列中有事件.如果一个事件到达,队列会适当通知条件变量.如果一个线程在等待,他将会被唤醒,处理队列中的事件.如果两个事件在几乎同一时间进队列,队列将出触发条件两次唤醒两个线程.

系统提供支持在不同环境下的条件变量.条件变量的正确实现需要细心的编码,因此,在你的代码中使用他们时,你可以参考 [Using Conditions](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/ThreadSafety/ThreadSafety.html#//apple_ref/doc/uid/10000057i-CH8-SW4).

###选择器

Cocoa 程序有有一个简便的方式,以线程同步的方式传送消息. [NSObject](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/index.html#//apple_ref/occ/cl/NSObject) 类在活动线程里执行一个选择器方法.这些方法让你的线程以异步的方式传递消息,保证目标线程同步执行.例如,你可以使用选择器消息,将结果从分布式计算传递到主线程或者指定的线程.每个执行选择器的请求都在目标线程的运行循环里排队,然后按接收到的顺序处理.

总结选择器和更多的信息请见 [Cocoa Perform Selector Source](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW44).

## 同步的代价和性能

同步有助于确保你代码的正确性,但以牺牲性能为代价.使用同步工具会引入延时，即使在没有竞争的情况.锁和原子操作通常涉及使用内存屏障和内核级的同步,确保代码的正确保护.如果再对锁有竞争,你的线程可能会阻塞并感受到更大的延迟.

表 4-2 列出了互斥锁和原子操作的大致开销.这些测量是采集几千个样本得出的平均时间.获取互斥锁所花的时间（即使在没有竞争的情况下），根据处理器负载，电脑的速度，系统和应用可用内存的大小不同而不同.

**表 4-2 互斥锁和原子操作的开销**

| 项目       | 大约开销       |  说明  | 
| ------------- |:-------------:|:----------:|
|互斥锁捕获时间| 大约 0.2 微秒| 这是在无争议情况下获取锁的时间.如果这个锁被另一个线程持有,捕获的时间会更大.数据是运行在 Intel 的iMac 双核处理器 OS X v10.5 RAM 为1 GB 的机器上通过分析得到的|
|原子操作的比较和交换| 大约 0.05 微秒| 这是在无争议情况下,对交换的时间比较.通过分析平均值和中值得到.数据产生基于 Intel 的iMac 双核处理器 OS X v10.5 RAM 为1 GB的机器|

在设计并发的任务时,正确性总是最重要的因素.但是也应该考虑到性能因素.在多个线程下正确执行的代码,比在单线程上运行相同的代码慢,这几乎没有改进.

如果你正在对一个现有的单线程应用程序进行改造,你应该使用一组关键任务的性能测量值.为那些相同的任务,你应该采取新的措施,比较在多线程和单线程情况下的性能.如果在调整了你的代码以后,线程并没有提高性能,你应该希望重新考虑你的具体实施或者使用线程.

关于性能和工具的度量标准,看 [Performance Overview](https://developer.apple.com/library/prerelease/ios/documentation/Performance/Conceptual/PerformanceOverview/Introduction/Introduction.html#//apple_ref/doc/uid/TP40001410) 关于锁和原子操作的详细信息请看 [Thread Costs](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/CreatingThreads/CreatingThreads.html#//apple_ref/doc/uid/10000057i-CH15-SW7).

###线程安全和信号

当涉及到线程的应用程序时,没有比处理信号的问题更能引起恐惧和问题的.信号是一个 BSD的底层 机制，可以用来给进程传递消息，或者以某种方式操作它。一些程序用信号来检测具体的事件.如子进程的死亡.系统使用信号来终止失控的进程和通信其他类型的信息.

信号的问题不是他们做什么,是当你的程序有多线程时他们的行为.在一个单线程程序里,所有的信号处理程序都在主线程上运行.不依赖特定硬件错误（如非法指令）的信号，会被扔给当前正在运行的线程。如果某个特定的线程需要处理给定的信号，需要找个方法在该信号到来的时候通知该处理线程.换句话说,信号可以被传递到你的应用程序的任何线程.

在应用程序中实现信号处理程序是第一条规则,避免哪个线程处理这个线程的假设.你不能假定设置信号处理函数的线程，就是该信号发生时处理的线程.

关于信号和信号处理程序的更好信息在 [signal](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/signal.3.html#//apple_ref/doc/man/3/signal) 和 [signction](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man2/sigaction.2.html#//apple_ref/doc/man/2/sigaction) 手册页.

###线程安全设计的技巧

同步工具是是一种有效的方法,来使你的线程安全.但他们不是万能的.使用过多时,和不是多线程相比,锁和其他类型的同步工具会降低你应用程序的性能.在安全和性能之间找到平衡是一种需要经验的艺术.以下各节提供了一些建议,帮助你选择适合你应用程序的同步级别.

####完全避免同步

对于你工作的任何新项目,甚至是现有的项目,设计你的代码和数据结构,避免使用同步是最好的可能的解决方案.虽然锁和其他的同步工具是有用的,但是确实是会影响到程序的性能.如果整体设计会导致特定的资源之间的高度竞争,你的线程可能等待更长的时间.

实现并发的最佳方案是减少并发任务之间的相互作用和相互依赖关系.如果每个任务都在自己的私有数据上运行,就不需要使用锁来保护数据.即时两个任务有共享数据集，你可以看下将数据集分开，每个任务各自有一份拷贝数据的方法当然,复制数据集也有他的成本,所以你必须权衡这些同步的成本,在作出决定之前.

####理解同步的限制

只有在应用所有线程中一致地使用同步工具，同步才是有效的.如果你创建了一个互斥锁来保护特定的资源,你所有的线程必须在获取资源之前获取互斥锁.这样做会导致互斥锁的保护失效，是一个程序错误.

####要意识到代码正确性的风险

当使用锁和内存屏障时,你应该经常小心放置你的代码在你的代码中.即使锁的放置会带给你虚假的安全感.下面的一系列的例子来说明这个问题.指出在看似没有错误的代码的缺陷.基本的前提是,你有一个可变的数组来包含一组不变的数据.假设你想调用数组中第一个对象的方法,你可以使用下面的代码:

	NSLock* arrayLock = GetArrayLock();
	NSMutableArray* myArray = GetSharedArray();
	id anObject;
 
	[arrayLock lock];
	anObject = [myArray objectAtIndex:0];
	[arrayLock unlock];
 
	[anObject doSomething];

因为数组是可变的,数组周围的锁防止了其他线程的数据修改数组的数据,直到你拿到这个对象之前.并且因为你使用的这个对象本身是不可变的,因此在 `doSomething` 周围不需要锁.

之前的例子有一个问题,如果你释放了锁,另外一个线程移除所有的对象,在你 `doSomethig` 之前？在一个没有垃圾收集的应用,代码中引用的对象可能已经释放.留下一个指向无效内存地址的指针.要解决这个问题.你可以简单的重新安排你现有的代码,在调用 `doSomething` 后释放锁:

	NSLock* arrayLock = GetArrayLock();
	NSMutableArray* myArray = GetSharedArray();
	id anObject;
 
	[arrayLock lock];
	anObject = [myArray objectAtIndex:0];
	[anObject doSomething];
	[arrayLock unlock];
	
将 `doSomething`的调用放置在锁内,代码保证当方法被调用的时候对象是有效的.不幸的是,如果 `doSomething` 方法执行了很长时间,这导致你的代码很长时间的占用锁,可能造成性能障碍.

代码的问题不是说是临界区的定义不明确,而是对实际的问题没有理解.真正的问题是其他线程引发的内存管理问题.因为它可以被其他的线程释放,更好的方案是在释放锁之前retain这个对象.这个解决方法使得对象被释放的真正问题,并且没有造成潜在的性能损失.

	NSLock* arrayLock = GetArrayLock();
	NSMutableArray* myArray = GetSharedArray();
	id anObject;
 
	[arrayLock lock];
	anObject = [myArray objectAtIndex:0];
	[anObject retain];
	[arrayLock unlock];
 
	[anObject doSomething];
	[anObject release];	
	
尽管前面的例子非常简单,他们确实做到了很重要的一点.说道正确性,你必须考虑到很明显的问题.内存管理和其他方面的设计都可能被多线程影响.所以应该提前考虑这些问题.此外,当涉及到安全的时候,你应该尽可能的假设编译器做的最坏的事情.这种意识和警惕性会帮助你避免潜在的问题,确保你代码行为的正确性.

怎么样使你的代码线程安全,请看 [Thread Safety Summary](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/ThreadSafetySummary/ThreadSafetySummary.html#//apple_ref/doc/uid/10000057i-CH12-SW1).

####死锁和活锁

任何时候,一个线程同一时间试图得到超过一个锁,有可能发生死锁.当两个不同线程各自拿着对方线程需要获取的锁，并试图获取对方线程已经拿到的锁时，会发生死锁.结果是,两个线程都一直阻塞,因为永远无法获取另外一个锁.

活锁类似于死锁发生在两个线程竞争同一个资源.活锁的情况下,一个线程首先放弃了第一个锁企图获取第二个锁,一旦得到第二个锁,就返回又去获取第一个锁.它被锁因为它花费了时间在释放一个锁,并获取另外一个锁,而不做实际的工作.

避免死锁和活锁的方法是同一时间只持有一个锁,如果你必须同一时间获取超过一个锁,你需要确保其他线程不会做相同的事情.

####正确使用 Volatile 变量

如果你已经用互斥锁来保护你的代码,不要想当然的确信你应该在代码中使用 `Volatile` 关键字来保护关键的变量.互斥锁包含了内存屏障来确保加载操作的适当顺序.对变量添加 `Volatile` 关键字,在临界区使得每次获取变量的时候从内存加载.这两个同步技术的结合在特定的情况下可能是必要的.但也导致性能的缺陷.如果互斥锁已经保护了变量,省略关键字 `Volatile` .

同样重要的是,你试图避免使用互斥锁,不使用 `Volatile` 变量.一般来说,互斥锁和其他的同步机制是保护变量和你的数据结构的好方式,比较 `Volatile` 变量来说.`Volatile` 关键字只是确保变量从内存中加载而不是存储在寄存器中.它不能保证你的代码正确的访问这个变量.

###使用原子操作

非阻塞同步是一个方法来执行某些类型的操作和避免锁的开销.尽管锁是同步线程的有效途径,获取锁是比较昂贵的操作,即时在无争议的情况下.相比这下,许多原子操作占用更少的时间,可以和锁一样有效.

原子操作可以执行简单的32位和64位的数学和逻辑运算.这些操作依赖于特定的硬件指令(和一个可选的内存屏障),以确保操作完成在内存访问之前.在多线程的情况下,你应该使用包含内存屏障的原子操作,以确保线程的同步是正确的.

表 4-3 列出了数学和逻辑操作的原子操作函数名,这些函数都定义在 `/usr/include/libkern/OSAtomic.h` 头文件中,这里你可以找到完整的用法.这些函数的64位的版本只在64伟进程中适用.

**表 4-3 原子的数学和逻辑运算**

| 操作      | 函数名       |  说明  | 
| ------------- |:-------------:|:----------:|
|相加|[AtomicAdd32](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicAdd32.3.html#//apple_ref/doc/man/3/OSAtomicAdd32) [OSAtomicAdd32Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicAdd32Barrier.3.html#//apple_ref/doc/man/3/OSAtomicAdd32Barrier) [OSAtomicAdd64](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicAdd64.3.html#//apple_ref/doc/man/3/OSAtomicAdd64) [OSAtomicAdd64Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicAdd64Barrier.3.html#//apple_ref/doc/man/3/OSAtomicAdd64Barrier)|将两个整型变量的值相加并存储在一个指定的变量中|
|递增|[OSAtomicIncrement32](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicIncrement32.3.html#//apple_ref/doc/man/3/OSAtomicIncrement32) [OSAtomicIncrement32Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicIncrement32.3.html#//apple_ref/doc/man/3/OSAtomicIncrement32) [OSAtomicIncrement64](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicIncrement32.3.html#//apple_ref/doc/man/3/OSAtomicIncrement32) [OSAtomicIncrement64Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicIncrement32.3.html#//apple_ref/doc/man/3/OSAtomicIncrement32)|指定的整型值递增1|
|递减|[OSAtomicDecrement32](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicDecrement32.3.html#//apple_ref/doc/man/3/OSAtomicDecrement32) [OSAtomicDecrement32Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicIncrement32.3.html#//apple_ref/doc/man/3/OSAtomicIncrement32) [OSAtomicDecrement64](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicIncrement32.3.html#//apple_ref/doc/man/3/OSAtomicIncrement32) [OSAtomicDecrement64Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicIncrement32.3.html#//apple_ref/doc/man/3/OSAtomicIncrement32)|指定的整型值递减1|
|逻辑 或|[OSAtomicOr32](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicOr32.3.html#//apple_ref/doc/man/3/OSAtomicOr32) [OSAtomicOr32Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicOr32.3.html#//apple_ref/doc/man/3/OSAtomicOr32)| 对32位值和32位值执行或运算|
|逻辑 与| [OSAtomicAnd32](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicAnd32.3.html#//apple_ref/doc/man/3/OSAtomicAnd32) [OSAtomicAnd32Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicAnd32.3.html#//apple_ref/doc/man/3/OSAtomicAnd32)| 对32位值和32位值执行与 运算|
|逻辑 非| [OSAtomicXor32](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicAnd32.3.html#//apple_ref/doc/man/3/OSAtomicAnd32) [OSAtomicXor32Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicAnd32.3.html#//apple_ref/doc/man/3/OSAtomicAnd32)|对32位值和32位值执行逻辑非运算 |
|比较和交换|[OSAtomicCompareAndSwap32](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32) [OSAtomicCompareAndSwap32Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32) [OSAtomicCompareAndSwap64](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32) [OSAtomicCompareAndSwap64Barrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32) [OSAtomicCompareAndSwapPtr](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32) [OSAtomicCompareAndSwapPtrBarrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32) [OSAtomicCompareAndSwapInt](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32)[OSAtomicCompareAndSwapIntBarrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32) [OSAtomicCompareAndSwapLong](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32) [OSAtomicCompareAndSwapLongBarrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32)|对指定的旧指进行比较.如果两者的值是相等的,这个函数就将新的值赋值给该变量,否则,就没有.比较和赋值操作作为一个原子操作,返回的是 BOOL 值,表明是否发生交换操作|
|测试设置| [OSAtomicTestAndSet](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32) [OSAtomicTestAndSetBarrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32)|在指定的变量中测试一个位,将该位设置为1,并将原来的那位的值作为一个布尔值.位根据公式测试 ` (0x80 >> (n & 7))` 位是`((char*)address + (n >> 3)` `n`是位数,`address`是指向变量的地址.这个公式有效的分解成8位大小的块,并且在每一个块中位顺序相反.例如,为了测试一个32位整型值的最低序位(0位),将指定实际的位号为7;同样,要测试最高点位(32位),将指定24位|
|测试清除| [OSAtomicTestAndClear](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32) [OSAtomicTestAndClearBarrier](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/OSAtomicCompareAndSwap32.3.html#//apple_ref/doc/man/3/OSAtomicCompareAndSwap32)|在指定的变量中测试一个位,将该位设置为0,并将原来的那位的值作为一个布尔值.位根据公式测试 ` (0x80 >> (n & 7))` 位是`((char*)address + (n >> 3)` `n`是位数,`address`是指向变量的地址.这个公式有效的分解成8位大小的块,并且在每一个块中位顺序相反.例如,为了测试一个32位整型值的最低序位(0位),将指定实际的位号为7;同样,要测试最高点位(32位),将指定24位|

大多数原子操作的行为是相对简单的.列表 4-1,测试,比较和交换行为,这里稍微复杂一点.`OSAtomicTestAndSet` 函数展示位操作公式对于一个整型值来说,结果可能不是你所期望的.最后展示 `OSAtomicCompareAndSwap`,这些函数都是在无争议的情况下被调用,并且没有其他线程操作值.

**列表 4-1 执行原子操作**

	int32_t  theValue = 0;
	OSAtomicTestAndSet(0, &theValue);
	// theValue is now 128.
 
	theValue = 0;
	OSAtomicTestAndSet(7, &theValue);
	// theValue is now 1.
 
	theValue = 0;
	OSAtomicTestAndSet(15, &theValue)
	// theValue is now 256.
 
	OSAtomicCompareAndSwap32(256, 512, &theValue);
	// theValue is now 512.
 
	OSAtomicCompareAndSwap32(256, 1024, &theValue);
	// theValue is still 512.
	
原子操作更多的信息,在 [atomic](https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/atomic.3.html#//apple_ref/doc/man/3/atomic)手册页,和 `/usr/include/libkern/OSAtomic.h`头文件中.

###使用锁

锁是线程编程的基本同步工具.锁使得你能够比较容易的保护大段代码,这样可以确保代码的正确性. OS X 和 IOS 提供基本的互斥锁对所用程序类型,Fuondation 框架定义了额外的互斥锁的变量对于特殊的情况.下面的章节告诉你如何使用这些锁的类型.

####使用 POSIX 互斥锁

POSIX 的在程序中的使用比较简单.创建互斥锁,先声明 `pthread_mutex_t`结构体.对互斥锁加锁和解锁,使用 `pthread_mutex_lock` 和 `pthread_mutex_unlock`函数.列表 4-2 展示了基本的代码,关于初始化和使用 POSIX 互斥锁.当你是使用完锁,简单的调用 `pthread_mutex_destroy` 释放锁数据结构.

**列表 4-2 使用互斥锁**

	pthread_mutex_t mutex;
	void MyInitFunction()
	{
    pthread_mutex_init(&mutex, NULL);
	}
 
	void MyLockingFunction()
	{
    pthread_mutex_lock(&mutex);
    // Do work.
    pthread_mutex_unlock(&mutex);
	}
	
>前面的代码是一个简化的例子,为了说明 POSIX 的基本用法.你自己的代码中应该检查这些函数的错误代码,并正确的处理他们.

####使用 NSLock 类

一个 [NSLock](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSLock_Class/index.html#//apple_ref/occ/cl/NSLock) 对象为 Cocoa 程序实现了基本的互斥锁.所有锁(包括 `NSLock`)的接口都定义在 [NSLocking](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Protocols/NSLocking_Protocol/index.html#//apple_ref/occ/intf/NSLocking) 协议里.定义了 `lock` 和 `unlock` 的方法.你可以使用这些方法去释放你现有的锁.

除了标准的锁行为, `NSLock`类增加了 [tryLock](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSLock_Class/index.html#//apple_ref/occ/instm/NSLock/tryLock) 和 [lockBeforeDate:](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSLock_Class/index.html#//apple_ref/occ/instm/NSLock/lockBeforeDate:) 方法.[tryLock](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSLock_Class/index.html#//apple_ref/occ/instm/NSLock/tryLock) 方法当锁并不可用时去获取锁不产生阻塞,而是返回 [NO](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html#//apple_ref/doc/c_ref/NO).
[lockBeforeDate:](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSLock_Class/index.html#//apple_ref/occ/instm/NSLock/lockBeforeDate:) 方法尝试去获取锁但是取消阻塞锁(并且返回 [NO](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/ObjCRuntimeRef/index.html#//apple_ref/doc/c_ref/NO)),如果没有在指定的时间内得到锁.

下面的例子展示了怎么使用 `NSLock` 对象来协调更新视觉显示,数据是被几个线程计算出来的.如果线程不能立即得到锁,它就只是继续计算直到可以获取锁更新显示.

	BOOL moreToDo = YES;
	NSLock *theLock = [[NSLock alloc] init];
	...
	while (moreToDo) {
    /* Do another increment of calculation */
    /* until there’s no more to do. */
    if ([theLock tryLock]) {
        /* Update display used by all threads. */
        [theLock unlock];
    }
	}
	
####使用 @synchronized 指令

`@synchronized` 指令是一个创建互斥锁的简便方法.`@synchronized` 指令做了互斥锁能做的事情-它阻止不同的线程同时获取同一个锁.这种情况下,你不必再去直接创建互斥锁或者一个锁对象,而是直接可以使用任何 Objective-C 对象作为一个锁,下面是例子:

	- (void)myMethod:(id)anObj
	{
    @synchronized(anObj)
    {
        // Everything between the braces is protected by the @synchronized directive.
    }
	}

对象使用 `@synchronized` 指令是一个区分保护块的唯一标识符.如果你执行上述方法在两个不同的线程,每个线程的 `anObj` 传递不同的参数,每个都将其锁定继续处理不被其他的阻塞.如果你传递相同的对象在这两种情况,一个线程会先获取锁,另外一个阻塞,直到第一个线程完成临界区代码.

作为一种预防的措施,`@synchronized` 隐含地将异常处理代码程序添加到受保护的代码.处理器会自动释放互斥的事件并抛出一个异常.这意味着为了使用 `@synchronized` 指令,你必须在 Objective-C 代码中处理异常.如果你不希望由隐式的异常处理程序引起额外的开销,你应该使用锁类.

关于 `@synchronized` 的更多信息,请看 Objective-C Programming Language.

####使用其他的 Cocoa 锁

接下来的部分介绍其他几种类型的 Cocoa 锁.

#####使用 NSRecursiveLock 对象

[NSRecursiveLock](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSRecursiveLock_Class/index.html#//apple_ref/occ/cl/NSRecursiveLock) 定义了一个锁,可以在一个线程获取多次而不会死锁.递归锁可以跟踪他被成功获取了多少次.每一次成功的获取锁必须通过相应的调用来解锁.只有当获取锁和解锁的调用是平衡的,其他的线程才能获取到锁.

就像它名字锁暗示的那样,这种类型的锁通常使用在一个递归函数中,以防止递归阻止该线程.在非递归的情况下,你依然可以根据它的语法调用它.下面是一个简单的递归函数,通过递归获取锁.如果你没有在代码中使用 NSRecursiveLock ,调再次调用函数时会死锁.

	NSRecursiveLock *theLock = [[NSRecursiveLock alloc] init];
 
	void MyRecursiveFunction(int value)
	{
    [theLock lock];
    if (value != 0)
    {
        --value;
        MyRecursiveFunction(value);
    }
    [theLock unlock];
	}
 
	MyRecursiveFunction(5);

>说明:因为递归锁直到所有的解锁被调用后才释放,应该仔细考虑使用它存在的潜在性能影响.在一个较长的时间内保持任何锁,可以使其他的线程阻塞直到递归完成.如果你可以重写的你的代码,不使用递归或者不使用递归锁,可以实现更好的性能.

#####使用 NSConditionLock 对象

[NSConditionLock](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSConditionLock_Class/index.html#//apple_ref/occ/cl/NSConditionLock) 定义可以用特定值来进行加锁和解锁.不应该把这种类型的锁与一个条件变量(见[Conditions]())混淆起来.这种行为有点类似于条件变量,但是实施方式非常不同.

通常,当线程需要以一个特定的饿顺序执行任务时,你需要使用 `NSConditionLock` 对象.比如一个线程制造数据,另外一个线程使用这个数据.当生产者正在执行时,消费者根据程序设定的条件来得到锁.(条件变量本身只是你定义的一个整型值).当生产者的操作完成,它打开锁设置锁的条件变量魏一个适当的整型值,由此来唤醒消费者线程,来进行相应的的数据处理.

`NSConditionLock ` 的加锁和解锁的方法可以任意组合.例如,你可以组合加锁 [unlockWithCondition:](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSConditionLock_Class/index.html#//apple_ref/occ/instm/NSConditionLock/unlockWithCondition:) 和 解锁 [lockwhenCondition](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSConditionLock_Class/index.html#//apple_ref/occ/instm/NSConditionLock/lockWhenCondition:).当然,后者解锁的操作不会释放线程等待条件变量的值.

下面的例子展示了生产-消费者问题是怎么用条件锁来解决的.想象程序有一个数据队列.生产者向队列中插入数据,消费者将数据从队列中移除.生产者线程不需要等待特殊状态,但是它需要等待锁确保数据安全的插入进队列中.

	id condLock = [[NSConditionLock alloc] 	initWithCondition:NO_DATA];
 
	while(true)
	{
    [condLock lock];
    /* Add data to the queue. */
    [condLock unlockWithCondition:HAS_DATA];
	}

锁的初始状态设置为 `NO_DATA`,生产者线程可以没有任何问题的得到初始化的锁.它将队列插入数据并且设置状态为 `HAS_DATA`.随后,生产者可以插入产生的新数据,不论这个队列是空的或者有数据.它唯一阻塞的时候是当消费者从队列中移除数据的时候.

因为消费者线程必须有数据操作,它使用特定条件等待队列.当生产者向队列中插入数据,消费者线程就被唤醒并且得到锁.接下来它可以从队列中移除数据更新队列状态.下面的例子展示了线程处理循环的基本结构.

	while (true)
	{
    [condLock lockWhenCondition:HAS_DATA];
    /* Remove data from the queue. */
    [condLock unlockWithCondition:(isEmpty ? NO_DATA : HAS_DATA)];
 
    // Process the data locally.
	}

#####使用 NSDistributedLock 锁

`NSDistributedLock` 类可以由多个主机上的多个应用程序限制访问一些共享资源,比如文件.锁本身是一种有效的互斥锁,被使用在文件系统中,如一个文件或者目录. `NSDistributedLock` 对象可用,这些使用的程序必须在文件系统里,并且要可写.这意味着要把它放在可以访问应用程序的所有计算机上的文件系统中.

不像其他几种锁,`NSDistributedLock` 没有遵从 [NSLocking](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Protocols/NSLocking_Protocol/index.html#//apple_ref/occ/intf/NSLocking) 协议本身没有 `lock` 的方法.`lock` 方法会阻塞线程并且使得程序以特定的速度轮询锁.相比较这种方法,`NSDistributedLock` 提供 `tryLock` 方法让你决定是否去轮询.

因为它的实现使用了文件系统, `NSDistributedLock` 对象不会释放除非拥有者明确地释放它.如果应用程序崩溃当持有分布式锁,其他客户端就不能访问保护的资源.这种情况下,你应该使用 `breakLock` 方法打破锁,这样你就可以访问了.破坏锁通常是要避免的,除非你确信你所拥有的进程已死并且不能释放锁.

和其他类型的锁一样,当你使用 `NSDistributedLock` 时,调用 `unlock` 方法来释放锁.

###使用 conditions

conditions 是一种特殊类型的锁,可以使用它来同步操作执行顺序.他们与互斥锁的不同点在一个微秒的方式上.等待 condition 的线程一直被阻塞,直到另外一个线程显式的发出 condition 的信号为止.

因为操作系统实现的区别,condition 锁是允许伪造的成功,即使你的代码里没有 signal.为了避免这个问题,应该和一个断言（判断）一起使用condition,这个断言（判断）来判断继续是否是安全的,而 condition只是用来保证这个线程一直在休眠,知道断言（判断）已经由 signal 的线程设置好.

以下是展示如何在你的程序中使用条件变量.

####使用 NSCondition 类

[NSCondition](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/NSCondition_class/index.html#//apple_ref/occ/cl/NSCondition) 提供了和 POSIX conditions 一样的语义.NSCondition 封装了使用的锁和条件变量数据结构到一个对象里.结果是一个对象可以像互斥锁一样加锁然后像条件变量一样等待.

列表 4-3 展示了一段 demo,当有一连串的事件等待一个 `NSCondition` 对象.`cocoaCondition` 对象包含 `NSCondition`对象,`timeToDoWork` 变量是一个递增的整型值,另一个线程将这个变量自增1之后,触发(signal)这个条件

**列表 4-3 使用 Cocoa conditions**

	[cocoaCondition lock];
	while (timeToDoWork <= 0)
    [cocoaCondition wait];
 
	timeToDoWork--;
 
	// Do real work here.
 
	[cocoaCondition unlock];
	
列表 4-4 使用 Cocoa conditions 和增加断言的值.应该在收到信号前为条件变量加锁.

**通知 Cocoa conditions**

	[cocoaCondition lock];
	timeToDoWork++;
	[cocoaCondition signal];
	[cocoaCondition unlock];
	
####使用 POSIX Conditions

POSIX 线程状态锁需要条件变量数据结构和互斥锁一起.尽管两个锁的结构是分离的,互斥锁紧密的捆绑运行时的条件变量结构.线程等待信号同时使用互斥锁和条件变量.改变这个组合会导致错误.

列表 4-5 显示了一个条件变量和断言的基本初始化和使用.经过初始化的条件变量和互斥锁,等待线程进入循环使用 `ready_to_go` 变量作为判断.只有当设置了判断并且条件变量被触发,等待的线程被唤醒开始工作.

**列表 4-5 使用 POSIX condition**

	pthread_mutex_t mutex;
	pthread_cond_t condition;
	Boolean     ready_to_go = true;
 
	void MyCondInitFunction()
	{
    pthread_mutex_init(&mutex);
    pthread_cond_init(&condition, NULL);
	}
 
	void MyWaitOnConditionFunction()
	{
    // Lock the mutex.
    pthread_mutex_lock(&mutex);
 
    // If the predicate is already set, then the while loop is bypassed;
    // otherwise, the thread sleeps until the predicate is set.
    while(ready_to_go == false)
    {
        pthread_cond_wait(&condition, &mutex);
    }
 
    // Do work. (The mutex should stay locked.)
 
    // Reset the predicate and release the mutex.
    ready_to_go = false;
    pthread_mutex_unlock(&mutex);
	}

信号线程负责设置断言,并将信号发送到状态锁.列表 4-6 显示这个行为的代码.这个例子中,条件变量是互斥锁里面的标志,防止等待条件变量的线程之间的竞争.

**列表 4-6 通知 condition lock**

	void SignalThreadUsingCondition()
	{
    // At this point, there should be work for the other thread to do.
    pthread_mutex_lock(&mutex);
    ready_to_go = true;
 
    // Signal the other thread to begin work.
    pthread_cond_signal(&condition);
 
    pthread_mutex_unlock(&mutex);
	}

>前面的代码是一个简化的例子,意在表明 POSIX 线程状态函数的基本用法.你的代码应该检查这些函数返回的错误代码,并正确的处理他们.
	






















,