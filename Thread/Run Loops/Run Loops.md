#Run Loops

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW1) 

 翻译人:王谦 翻译日期:2015.10.12
 
 
 Run Loops 是与线程关联的基础设施的一部分。一个 run loop 是一个处理循环事件,你要安排的工作和调整接收传入的事件。run loop 的目的是保持你的线程忙碌并且有工作要做,让你的线程不沉睡。
 
 Run loop 是不完全自动管理的。你你必须设计线程的代码来启动 Run loop 在适当的时间和响应传入的事件。两者 Coco 和 Core Foundation 提供 run loop 对象去帮助你配置管理你的线程 run loop 。 你的应用程序不需要明确的创建这些对象;每个线程,包含应用主要的线程,有一个关联 run loop 的对象。仅仅第二个线程需要明确运行他们的 run loop,无论以何种方式。应用程序框架自动设置和运行 run loop 在主要的线程中作为应用程序启动过程的一部分。
 
 以下部分提供更多的信息关于 run loop 和如何为应用程序配置它们。
关于 run loop 对象附加的信息,看 [NSRunLoop Class Reference] 和 [CFRunLoop Reference]。

[NSRunLoop Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSRunLoop_Class/index.html#//apple_ref/doc/uid/TP40003725

[CFRunLoop Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFRunLoopRef/index.html#//apple_ref/doc/uid/20001441



##剖析一个 run loop

run loop 听起来是非常像它的名字。它是一个循环,你的线程进入使用运行事件处理来响应即将到达的事件。 你的代码提供控制声明使用完成关于 run loop 当前的循环部分 － 换句话说,你的代码提供 while 或 for 循环来达成 run loop。在你的循环之内,你使用一个 run loop 对象“ run ”事件处理代码接收到的事件并调用安装处理程序。



一个 run loop 接收事件来自两个源的不同类型。输入源实现异步事件,通常消息来自另外一个线程或者来自一个不同的应用程序。计时器实现同步事件源,出现在一个预定时间或重复间隔。两个类型源使用一个应用程序特性处理日常过程当它成功时的方法。

清单 3-1 显示 run loop 的概念结构和各种各样的来源。输入源实现异步事件的相应处理和引起[runUntilDate:]方法(调用线程上的[NSRunLoop]() 关联对象)退出。计时器源实现处理他们的日常事件但是不引起 run loop 退出。

清单 3-1 构成 run loop 和它的源

![runloop.jpg](./runloop.jpg)


除了处理输入的源,run loop 也产生关于 run loop 行为的通知。注册 run－loop 观察者可以接收这些通知和使用它们来处理额外的线程。你使用 Core Foundation 在你的线程上安装 run－loop 观察者。

以下部分提供更多关于run loop 和它们的操作方式组件信息。在处理事件的时候描述它们通知产生不同的时间。


##Run Loop Modes

一个 run loop 下运行是输入源的集合,监视定时器和一组 run loop 观察者被通知。每一次你运行你的 run loop,你指定(显式或隐式)一个详细说明" mode "运行。在通过 run loop 的时候,仅仅和模式关联的源是被监控和被允许实现它们的事件。(同样的,只有观察者与该模式关联通知 run loop 的进展。)源关联其他模式控制任何新的事件直到随后经过适当的循环模式。


在你的代码,你通过名称识别模式。两者 Coco 和 Core Foundation 定义一个默认的模式和几个常用的模式,随着字符串指定模式的代码。你可以定义自定义模式通过简单指定一个自定义字符串的模式名字。虽然名字你分配到自定义模式是任意的,这些模式的内容。你必须确定添加一个或多个输入源,计时器,或你创建 run-loop 观察者或任何模式对他们是有用的。


您使用模式在一个特殊的时候的通过 run loop 来过滤掉不必要的来源的事件。大多数时候,你想要将运行你的 run loop 在系统默认" default "的模式。一个模型面板,无论以何种方式,可以在" model "的模式下运行。然后再这个模式,仅仅关联源的模型面板将实现线程事件。第二个线程,你可以使用自定义模式阻止 低优先权的源来自在 time-critical 时候的操作。

>注意:在源事件中辨别基础的模式,没有事件的类型。举一个例子,你将不使用模式只匹配 mouse-down 事件或仅仅键入事件。你可以使用模式监听一组不同的端口,延缓暂时的时间,或改变另外的源和 run loop 当前被监控的观察者。


表格 3-1 通过 Coco 和 Core Foundation 定义标准的模式列表 以及当你使用模式的描述。


表格 3-1 预先定义的 run loop 模式

|模式|名字|描述|
|---:|:---|:---|
| Default |[NSDefaultRunLoopMode]() (Cocoa) [kCFRunLoopDefaultMode]() (Core Foundation)|是一个用于大多数操作的默认模式。大多数时候,你应该使用这个模式启动你的 run loop 和配置你的输入流。|
| Connection | `NSConnectionReplyMode` (Cocoa)|Cocoa 使用这个模式结合`NSConnection`对象来监控回复。你应该很少使用这个模式。|
| Modal |`NSModalPanelRunLoopMode` (Cocoa)|Cocoa 使用这个模式用于来识别事件的模型面板。|
|Event tracking|`NSEventTrackingRunLoopMode` (Cocoa)|Cocoa 使用这个模式约束进入的事件在  mouse-dragging 循环和其他类型的追踪用户循环接口的时候。|
|Common modes|[NSRunLoopCommonModes]() (Cocoa) [kCFRunLoopCommonModes]() (Core Foundation)|这是一个普通的使用模式配置组。与这个模式关联一个输入源并且把每个组的模式联合起来。Cocoa 应用程序,包含默认的设置,模型和通过默认追踪模式事件。 包括最初只是 Core Foundation 的默认模式。你能添加自定义模式设置使用[CFRunLoopAddCommonMode]() 函数。|
 
 
##输入源

在你的线程中实现异步输入源事件。源事件取决于输入源的类型,通常是一到两个类别。Port-based 输入源监控你的应用程序 Mach ports。自定义输入源监控自定义源事件。至于和你有关的 run loop,它应该无论是否输入源是 port-based 或 自定义。该系统通常实现这两种类型的输入源,您可以使用。两个来源之间唯一的区别是它们是如何表示的。Port-based 源是通过核心自动表示的,自定义源必须从另外一个线程中手动表示。

你创建一个输入源的时候,你将它分配给一个或多个模式的 run loop。在任何给定的时刻对输入源监控的模式进行影响。大多数时候,你在默认的模式下运行 run loop,但是你也能指定自定义的模式。如果一个输入源不在当前监控模式,它生成的任何事件都被保留,直到 run loop 运行在正确的模式。

以下的部分描述一些输入源。


##Port-Based 源

Cocoa 和 Core Foundation 提供内置的支持,使用 port-related 对象和函数创建port-based 输入流。举一个例子,在 Cocoa,你从不需要直接在所有的地方创建一个输入源。你简单的创建一个端口对象和使用[NSPort]()方法在 run loop 中添加端口。处理创建的端口对象和配置你的输入源端口。

在 Core Foundation,你必须手动创建两者的端口和它的 run loop 源。在这两种情况下,你使用关联端口透明类型的函数 ([CFMachPortRef](), [CFMessagePortRef](), 或 [CFSocketRef]())创建合适的对象。

举一个例子怎么设置和配置自定义 port-based 源,看 [Configuring a Port-Based Input Source]。

[Configuring a Port-Based Input Source]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-131281

##自定义输入源

创建一个自定义输入源,你必须使用在与Core Foundation 关联的[CFRunLoopSourceRef] 透明类型的函数。你使用几个回调函数配置一个自定义输入源。处理任何传入的事件,和拆除源时从 run loop 中移除。

[CFRunLoopSourceRef]:
 https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFRunLoopSourceRef/index.html#//apple_ref/c/tdef/CFRunLoopSourceRef
 
除了定义自定义源在事件发生后的行为,你必须也定义事件传递机制。这部分源运行在一个单独的线程和提供一个可靠的输入源与它的数据和在它数据被读取处理的时候发一个信号。事件传递机制取决于你,但不需要过于复杂。


举一个例子怎么创建一个自定义输入源,看[Defining a Custom Input Source]。自定义输入源的参考信息,参考[CFRunLoopSource Reference]。

[Defining a Custom Input Source]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW3

[CFRunLoopSource Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFRunLoopSourceRef/index.html#//apple_ref/doc/uid/20001443

###Cocoa 执行选择来源

除了基于端口的源,Cocoa 定义一个自定义的输入源允许你在任何一个线程执行选择。像一个基于端口的源,目标线程上执行选择器请求序列化,减轻许多同步问题和在运行一个线程时可能发生的多种方法。与基于端口的源不同,在执行它的选择器之后,执行删除来自 run loop 它自身的一个选择器源。

>注意:优先在 OS X v10.5,  通常使用执行选择器源在主线程中发送消息,但是在iOS OS X v10.5 以后,你能使用它们发送消息到任何线程。

在另外一个线程执行一个选择器的时候, 目标线程必须有一个有效的 run loop。你创建的线程,这是直到你代码明确的启动 run loop 等待的方法。因为主线程启动它自己的 run loop,无论如何,你可以在线程上发出调用邀请,一旦应用程序调用`applicationDidFinishLaunching:` 应用程序代理方法。run loop 过程所有队列执行选择器每次通过循环调用，而不是在每次循环迭代过程中处理一次。

列表 3-2 清单 NSObject 可以用来在其他线程执行选择器中定义方法。因为这些方法是在 `NSObject` 已经声明,你可以使用来自任何线程来获得 objective - c的对象,包括 POSIX 线程。这些方法实际上没有执行选择器创建一个新的线程。

列表 3-2 在线程上执行选择器

|方法|描述|
|:---|:---|
|[performSelectorOnMainThread:withObject:waitUntilDone:]() [performSelectorOnMainThread:withObject:waitUntilDone:modes:]()|在线程下次 run loop 循环的时候在应用程序主线程执行指定的选择器。这些方法给你阻塞现在的线程的权利直到选择器执行。|
|[performSelector:onThread:withObject:waitUntilDone:]() [performSelector:onThread:withObject:waitUntilDone:modes:]()|在任何线程执行指定的选择器,你有一个 [NSThread]()对象。这些方法给你当下线程的阻塞选项直到选择器执行。|
|[performSelector:withObject:afterDelay:]() [performSelector:withObject:afterDelay:inModes:]()|  执行指定选择器在当下的线程,在下一个 run loop 循环和在选择的周期延迟时。因为它等待直到下一个 run loop 循环执行选择器,从当前执行代码中这些方法提供了一种自动迷你延迟。多个选择器排队一个接一个地执行的顺序队列。|
|[cancelPreviousPerformRequestsWithTarget:]() [cancelPreviousPerformRequestsWithTarget:selector:object:]()| 当前线程使用[performSelector:withObject:afterDelay:]() 或[performSelector:withObject:afterDelay:inModes:]()方法,让你取消一个消息发送。|


关于这些每个方法的详细信息,看[NSObject Class Reference]

[NSObject Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSObject_Class/index.html#//apple_ref/doc/uid/TP40003706


##计时器源
你的线程在未来的一个预定时间实现同步计时器源事件。计时器是一个线程方法,通知它自身做重要的事。举一个例子,检索一个字段可以使用一个计时器开始自动搜索,一旦一定数量的时间内用户通过连续的击键。使用这种延迟时间为用户提供一个机会来尽可能多的搜索所需的字符串,然后再开始搜索。

虽然它产生基于时间的通知,不是一个实时的计时器进程。像输入源,计时器是你的 run loop 与特殊的模式关联。如果一个计时器不在当前的模式监听 run loop,它就不开启直到你在一个支持计时器的模式下运行 run loop。同样的,如果一个计时器是在执行一个处理程序之间开启 run loop,这个计时器等待知道下一个时间通过 run loop 调用它处理程序。如果 run loop 没有全部运行,那么计时器从不开启。

您可以配置定时器来产生事件只有一次或多次。一个重复计时器自动重新安排基于预定发射时间,不是实际的开始时间。举一个例子,如果一个计时器的开始是在一个特别时间和每5秒钟之后,预定的开始时间将一直指向在原来的 5 秒钟时间间隔,如果事件当前的开始时间得到延时。如果开始时间的延时的以至于错过一个或多个预定开始时间,计时器触发一次错过了时间。在开始错过之后,计时器被重新预定开始时间。

更多的信息在配置计时器源,看[Configuring Timer Sources]。参考信息,看[NSTimer Class Reference] 或[CFRunLoopTimer Reference]。


[Configuring Timer Sources]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW6

[CFRunLoopTimer Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSTimer_Class/index.html#//apple_ref/doc/uid/TP40003747

##Run Loop 观察者

与来源相反,开始的时候伴随着一个适当的异步或同步事件,开始在执行 run loop 本身的时候指定 run loop 观察者位置。你可能使用 run loop 观察者让你的线程准备进行一个发送的事件或在它休眠之前准备一个线程。你可以关联 run loop观察者与以下事件在你的 run loop 中:

* run loop 入口.
* 关于 run loop 进行一个计时器的时候。
* 关于 run loop 进行一个输入源的时候。
* 关于 run loop 休眠的时候。
* run loop 唤醒的时候,但是在把进行的事件唤醒之前。
* 退出 run loop。

你可以使用 Core Foundation 添加一个 run loop。创建一个 run loop 观察者,你创建一个新的实例 [CFRunLoopObserverRef] 不透明类型。这个类型保留你的自定义回调函数的踪迹和使它感兴趣的活动。

类似的计时器, run loop 观察者可以使用一次或反复使用。一个一次性的观察者从 run loop 开始之后 删除它本身,而重复观察者保持连接。当你创建它的时候,你指定一个观察者是否运行一次或重复多次。

举一个例子,怎样创建一个 run loop 观察者,看 [Configuring the Run Loop]。参考信息看[CFRunLoopObserver Reference]。

[CFRunLoopObserverRef]:
https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFRunLoopObserverRef/index.html#//apple_ref/c/tdef/CFRunLoopObserverRef

[Configuring the Run Loop]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW18

[CFRunLoopObserver Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/CoreFoundation/Reference/CFRunLoopObserverRef/index.html#//apple_ref/doc/uid/20001442

##run loop 的事件序列

每次运行它,你的线程在 run loop 进行事件期间生成的通知连着任何观察者。它的顺序是非常具体的,如下:

1.通知观察者 run loop 已经进入。
2.通知观察者准备好的任何计时器开启。
3.通知观察者任何输入源不是基于端口开启的。
4.开启任何没有基于端口的输入源是准备开启的。
5.如果一个基于端口的输入源是准备和等待开启,立即进行事件。第9步。
6.通知观察者线程休眠。
7.线程休眠直到以下事件出现:

* 一个事件到达一个基于端口的输入源。
* 一个计时器开启。
* 设置 run loop 有效期的超时值。
* 明确的唤醒 run loop。

8.通知观察者线程刚唤醒。
9.在进行期间的事件。

* 如果一个用户定义的计时器开启了,进行的计时器事件重新开始循环。第2步。
* 如果一个输入源开启,传递事件。
* 如果 run loop 明确醒来但尚未超时,重新启动循环。第2步。

10.通知观察者 run loop 退出。


因为观察者的通知计时器和输入源在这些事件实际发生之前,可能会有差距的时候通知和当前事件的时间。如果这些事件之间的时间间隔是紧凑的,你可以使用休眠和唤醒休眠通知帮你联系实际的事件之间的时间。

因为定时器和其他周期性事件完成运行 run loop 时,绕过那些循环被破坏完成的事件。这种行为发生的典型例子是当你通过输入一个循环反复从应用程序请求事件实现一个鼠标跟踪程序。因为你的代码是直接抓住事件,而不是让应用程序分派其他正常的事件,有效的计时器不会开启直到你鼠标追踪程序退出并且回程控制的应用程序之后。

一个 run loop 可以明确的使用的 run loop 唤醒对象。其他事件也可以引起 run loop 唤醒。举一个例子, 添加另一个非基于端口的输入源来唤醒 run loop,以便输入源可以立即处理,而不是等待直到一些事件发生。

##什么时候你可以使用一个 Run Loop?

唯一一次你需要运行一个明确的 run loop 是在你创建应用程序第二个线程的时候。run loop 在你的应用程序主线程中是一个重要的基础块。作为一个结果,应用程序框架提供代码运行应用程序主要的循环和启动自动循环。[UIApplication]()的 `run` 方法在 iOS(或在 OS X的   `NSApplication`)启动一个应用程序主循环作为部分正常的启动序列。如果你使用 Xcode 模版工程创建你的应用程序,你不应该明确的调用这些例程。

第二个线程,你需要决定一个 run loop 是否是必要的,如果是,配置和启动它本身。你不需要在全部的案列中都启动一个 run loop 的线程。举一个例子,如果你使用一个线程执行一些长时间运行和已经决定的任务,你可以避开启动的 run loop。run loop 是预期你想要和线程更多交互性的状况。举一个例子,你需要启动一个 run loop,如果你打算做下列:


* 使用端口或自定义输入源传递其他的线程。
* 在线程上使用计时器。
* 使用任何`performSelector…` 方法在一个 Coco 应用程序。
* 线程在执行周期任务。

如果你选择使用一个 run loop,明确的设置和配置。正如所有线程的编程,你应该有一个计划在适当的情况下退出你的第二个线程。让它退出比强迫终止它总是更好干净的结束线程,怎样陪着和退出一个 run loop 的信息在 [Using Run Loop Objects] 描述。

[Using Run Loop Objects]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW5

##使用 Run Loop 对象

一个 run loop 对象提供主要接口添加输入源, run - loop 观察者到你的 run loop 运行它,事件线程有一个 run loop 对象和它关联。在 Cocoa,这个对象是一个 [NSRunLoop]() 对象类的实例。在一个低水平的应用程序,它是一个 [CFRunLoopRef]()不透明类型的指针。

###获取一个  Run Loop 对象

获取当前线程的 run loop,你使用一下一个:

* 在一个 Cocoa 应用程序,使用 [currentRunLoop]() 类方法的 [NSRunLoop]() 重新得到一个 ｀NSRunLoop` 对象。
* 使用 [CFRunLoopGetCurrent]() 函数。

虽然他们不是免费连接类型,你可以从 `NSRunLoop` 对象需要的时候获取一个不透明类型的 `CFRunLoopRef`。你可以通过核心基础的例程定义一个`NSRunLoop`的[getCFRunLoop]()方法返回一个`CFRunLoopRef`类型。因为两者的对象涉及一些 run loop,你可以混合调用 `NSRunLoop` 对象和 `CFRunLoopRef` 不透明类型。

###配置 Run Loop

在你运行一个 run loop 在第二个线程上之前,您必须添加至少一个输入源或计时器。如果一个 run loop 没有任何来源监控,它在你师徒运行它的时候立即退出。举一个例子怎么样添加一个 run loop 源,看[Configuring Run Loop Sources]()。

除了安装源,你可以安装 run loop 观察者和使用它们探测不同的 run loop 使用阶段。安装一个 run loop 观察者,你可以创建一个透明类型的 [CFRunLoopObserverRef]() 和使用[CFRunLoopAddObserver]() 函数添加到你的 run loop。 run loop 观察者必须使用 Core Foundation 创建,即使对于 Cocoa 应用程序。

清单 3-1 显示一个主程序的线程连接一个 run loop 观察者到它的 run loop。这个例子的目的是展示如何创建一个 run loop 的观察者,所以代码简单的设置一个 run loop 观察者来监控全部的 run loop 活动。最基本的处理程序例程(不表明)简单的 run loop 活动的日志文件作为计时器请求的进程。


清单 3-1 创建一个 run loop 观察者

	- (void)threadMain
	{
    // The application uses garbage collection, so no autorelease pool is needed.
    NSRunLoop* myRunLoop = [NSRunLoop currentRunLoop];
 
    // Create a run loop observer and attach it to the run loop.
    CFRunLoopObserverContext  context = {0, self, NULL, NULL, NULL};
    CFRunLoopObserverRef    observer = CFRunLoopObserverCreate(kCFAllocatorDefault,
            kCFRunLoopAllActivities, YES, 0, &myRunLoopObserver, &context);
 
    if (observer)
    {
        CFRunLoopRef    cfLoop = [myRunLoop getCFRunLoop];
        CFRunLoopAddObserver(cfLoop, observer, kCFRunLoopDefaultMode);
    }
 
    // Create and schedule the timer.
    [NSTimer scheduledTimerWithTimeInterval:0.1 target:self
                selector:@selector(doFireTimer:) userInfo:nil repeats:YES];
 
    NSInteger    loopCount = 10;
    do
    {
        // Run the run loop 10 times to let the timer fire.
        [myRunLoop runUntilDate:[NSDate dateWithTimeIntervalSinceNow:1]];
        loopCount--;
    }
    while (loopCount);
	}

为 run loop 配置一个长线程的时候,这样更好接收至少一个输入源的消息。虽然你可以进入 run loop 仅仅连着一个计时器。一旦计时器开启,它分配宣告无效,将要引起 run loop 退出。附上一个重复的计时器可以让 run loop 运行越过一段长的时间。但是包含一个定期开启计时器来唤醒你的线程,实际上是另一种形式的查询。通过对比,一个输入源等待一个事件发生,保持你的线程,直到它休眠。


###启动 Run Loop

要是没有第二个线程在你的应用程序里启动 run loop 是必要的。一个 run loop 必须有至少一个输入源或定时监控。如果没有附上,run loop 就立即退出。

这几个方法启动 run loop,包含以下:

* 没有附加条件的
* 设置时间限制
* 在一个特别的模式

进入无条件的 run loop 是最简单的选择,但也是最不可取的。运行你的 run loop 无条件将线程放置到一个永久的循环中,它给你有限的 run loop 本身的控制权限。你可以添加和删除输入源和计时器,但停止 run loop 的唯一方法是杀死它。也没有办法运行在一个自定义的模式下 run loop。

而不是无条件运行一个 run loop,这样更好的运行 run loop 一个请求超时的值。你使用一个请求超时的值的时候,run loop 运行直到一个事件到达或指派时间的有效期。如果时间到达,处理一个程序调度事件和退出 run loop。你的代码可以重新启动 run loop 处理下一个事件。如果在规定时间到期,你可以简单的重新启动 run loop 或使用所需的时间做任何整理工作。

除了一个请求超时的值,你可以运行你的 run loop 使用一个特殊的模式。模式和请求超时的值是不互相排斥的,当启动一个 run loop 的时候可以使用两者。限制源的模式类型传递 run loop 事件,更多描述的详细信息在 [Run Loop Modes]。

[Run Loop Modes]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW12


清单 3-2 显示了一个骨架的线程的主要入口。这个例子的关键部分显示一个 run loop 的基础结构。在本质上,你添加你的输入源和计时器到 run loop 反复的调用一个例程启动 run loop。每次 run loop 例程返回,你检查任何条件,保证出现退出线程。这个例子使用  Core Foundation  run loop 例程,以便检查返回结果,确定 run loop 退出的原因。如果你是使用 Cocoa 和不需要检车返回值,你可以使用 [NSRunLoop]()类方法以类似的方式运行 run loop。( run loop 的一个例子, NSRunLoop 类的调用方法,见清单 3-14。)

清单 3－2 运行一个 run loop


	- (void)skeletonThreadMain
	{
    // Set up an autorelease pool here if not using garbage collection.
    BOOL done = NO;
 
    // Add your sources or timers to the run loop and do any other setup.
 
    do
    {
        // Start the run loop but return after each source is handled.
        SInt32    result = CFRunLoopRunInMode(kCFRunLoopDefaultMode, 10, YES);
 
        // If a source explicitly stopped the run loop, or if there are no
        // sources or timers, go ahead and exit.
        if ((result == kCFRunLoopRunStopped) || (result == kCFRunLoopRunFinished))
            done = YES;
 
        // Check for any other exit conditions here and set the
        // done variable as needed.
    }
    while (!done);
 
    // Clean up code here. Be sure to release any allocated autorelease pools.
	}	

可能运行一个 run loop 的递归。换句话说,你可以调用 [CFRunLoopRun](),[CFRunLoopRunInMode](),或任何`NSRunLoop` 方法启动 run loop 从内部处理程序的一个输入源或计时器。当这样做,你可以使用任何模式运行你想要嵌入的 run loop,包含通过外部的 run loop 的使用模式。


###退出 Run Loop

这是两个方法可以使 run loop 退出之前处理一个事件:

* 配置 run loop 运行一个请求超时的值。
* 告诉 run loop 停止。

无疑是首选使用一个超时值,如果你能管理它。指定一个超时值让 run loop 完成所有的正常处理,包含 run loop 传递的观察者通知,在这之前退出。

明确的停止 run loop [CFRunLoopStop]()函数产生一个类似请求超时的结果。run loop 发送任何一个剩下的 run-loop 通知然后退出。不同的是你能使用这个技巧无条件启动 run loop。 

虽然删除一个 run loop 输入源也可以引起 run loop 定时器退出,这不是一个可靠的停止一个 run loop 的方法。一些系统例程添加输入源到一个 run loop 处理需要的事件。因为你的代码可能不知道这些输入源,它将不会删除它们,将防止从 run loop 退出。


###线程安全和 Run Loop 对象

改变线程安全取决于在你使用 API 控制你的 run loop。可以从任何线程调用,通常线程安全是在 Core Foundation 函数中。如果你是执行操作改变 run loop 的配置,这仍然是良好的做法,只要有可能就从线程控制 run loop。

Cocoa [NSRunLoop]() 类本身也没有线程安全,它对应 Core Foundation。如果你使用`NSRunLoop`类修改你的 run loop,你应该这样做,只有从同一个线程控制 run loop。 添加一个输入源或者计时器到一个 run loop 属于一个不同的线程可以引起你的代码奔溃或以一个想不到的方法表示。

##配置 Run Loop 源

下面的例子在Cocoa 和 Core Foundation里展示如何设置不同类型的输入源。

###定义一个自定义输入源

创建一个自定义输入源包含以下定义:

* 你想要处理你的输入源信息。
* 调度程序例程让感兴趣的客户端知道如何联系你的输入源。
* 一个处理例程通过任何客户端执行发送请求。
* 一个取消例程使你的输入源无效。

因为你创建一个自定义输入源处理自定义信息,实际的配置设计是灵活的。调度器,处理程序,取消例程是关键例程,你通常需要你的自定义输入源。其余大部分输入源的行为,无论如何, 发生在超出这些处理程序之外。举一个例子,由你定义的机制通过你的输入源数据和传递你的存在的输入源到其他线程。

插图 3-2 显示一个自定义输入源配置例子。这个例子,应用程序主线程继续提及输入源参考,为输入源自定义命令缓冲区, run loop 的输入源安装。主线程有一个任务,它想要占用唤醒线程,它布置一个命令控制缓冲区沿着任何需要的信息唤醒线程从而启动任务。(应为两者的主线程和输入源唤醒线程可以利用命令缓冲区,必须使用异步。)一旦命令发出,主线程发信号给输入源唤醒工作线程 run loop 。根据接收的唤醒命令,run loop 调用处理程序输入源,处理命令发现控制缓冲区。


**插图 3-2 操作一个自定义输入源**

![custominputsource-1.jpg](./custominputsource-1.jpg)

以下部分解释自定义输入源的实现从前面的图显示您需要实现的关键代码。

###定义输入源

定义一个自定义输入源,要求使用  Core Foundation 例程配置你的 run loop 源和附加一一个 run loop。尽管基础程序是 C-基础函数,这并不排除你写包装的功能和使用 objective - C 或 c++ 实现的代码。

输入源介绍在插图 3-2 使用一个 Objective-C 对象管理一个控制缓冲区和调整 run loop。清单 3-3 显示定义这个对象。`RunLoopSource`对象管理一个控制缓冲区和使用缓冲区接收来自其它线程的消息。这些清单也显示定义`RunLoopContext`对象,实际上只是一个容器对象通过使用一个`RunLoopSource` 对象和一个 run loop 引用应用程序的主线程。

**清单 3-3 自定义输入源对象的定义**

	@interface RunLoopSource : NSObject
	{
    CFRunLoopSourceRef runLoopSource;
    NSMutableArray* commands;
	}
 
	- (id)init;
	- (void)addToCurrentRunLoop;
	- (void)invalidate;
 
	// Handler method
	- (void)sourceFired;
 
	// Client interface for registering commands to 	process
	- (void)addCommand:(NSInteger)command withData:		(id)data;
	- (void)fireAllCommandsOnRunLoop:(CFRunLoopRef)runloop;
 
	@end
 
	// These are the CFRunLoopSourceRef callback functions.
	void RunLoopSourceScheduleRoutine (void *info, CFRunLoopRef rl, CFStringRef mode);
	void RunLoopSourcePerformRoutine (void *info);
	void RunLoopSourceCancelRoutine (void *info, CFRunLoopRef rl, CFStringRef mode);
 
	// RunLoopContext is a container object used during registration of the input source.
	@interface RunLoopContext : NSObject
	{
    	CFRunLoopRef        runLoop;
    	RunLoopSource*        source;
	}
	@property (readonly) CFRunLoopRef runLoop;
	@property (readonly) RunLoopSource* source;
 
	- (id)initWithSource:(RunLoopSource*)src andLoop:(CFRunLoopRef)loop;
	 @end
	 
虽然 Objective-C 代码管理自定义数据输入源,附上输入源一个 run loop C-基础回调需求。首先在你实际上附加 run loop 源到你的 run loop的时候这些函数是调用的。是显示在清单 3-4。因为这是输入源只有一个客户端(主线程),它使用调度程序函数发送一个消息到注册与应用程序本身代表的线程里。代理想要传递输入源的时候,它使用`RunLoopContext` 对象中的信息。

**清单 3-4 把一个 run loop 源列入计划**

	void RunLoopSourceScheduleRoutine (void *info, CFRunLoopRef rl, CFStringRef mode)
	{
    RunLoopSource* obj = (RunLoopSource*)info;
    AppDelegate*   del = [AppDelegate sharedAppDelegate];
    RunLoopContext* theContext = [[RunLoopContext alloc] initWithSource:obj andLoop:rl];
 
    [del performSelectorOnMainThread:@selector(registerSource:)
                                withObject:theContext waitUntilDone:NO];
	}
	
当你像输入源发送信号通知的时候,最重要的一个回调例程是一个自定义数据使用的进程。清单 3-5 显示执行回调例程关联 `RunLoopSource` 对象。这个函数简单地向前请求使用 `sourceFired` 方法,然后处理存在命令缓冲区中的任何命令。

**清单 3-5 执行工作的输入源**

	void RunLoopSourcePerformRoutine (void *info)
	{
    RunLoopSource*  obj = (RunLoopSource*)info;
    [obj sourceFired];
	}

如果你从它的 run loop 使用 [CFRunLoopSourceInvalidate]() 函数来删除你的输入源的事件,系统调用你的输入源取消例程。你可以使用例程通知客户端 你的输入源是不再有效而且它们应该删除任何引用。清单 3-6 显示取消回调例程向`RunLoopSource` 登记的对象。这个函数发送另外的 `RunLoopContext` 对象到应用程序代理,但这一次代理问道删除关于 run loop 的源。

**清单 3-6 使一个输入源无效**

	void RunLoopSourceCancelRoutine (void *info, CFRunLoopRef rl, CFStringRef mode)
	{
    RunLoopSource* obj = (RunLoopSource*)info;
    AppDelegate* del = [AppDelegate sharedAppDelegate];
    RunLoopContext* theContext = [[RunLoopContext alloc] initWithSource:obj andLoop:rl];
 
    [del performSelectorOnMainThread:@selector(removeSource:)
                                withObject:theContext waitUntilDone:YES];
	}

>注意:应用程序代理`registerSource:` 和 `removeSource:` 方法的代码是显示在[Coordinating with Clients of the Input Source]。

[Coordinating with Clients of the Input Source]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW37


###在  Run Loop 上安装输入源

清单 3-7 显示 `init`和`addToCurrentRunLoop` 方法的`RunLoopSource`类。`init` 方法创建[CFRunLoopSourceRef]() 透明类型,实际上必须附加到 run loop。它通过 `RunLoopSource` 对象本身作为上下文的信息所以回调例程有一个指向对象的指针。安装输入源不发生直到唤醒线程引起 `addToCurrentRunLoop` 方法,此时`RunLoopSourceScheduleRoutine`调用回调函数。一旦输入源添加 run loop,线程能等待运行它的 run loop。

**清单 3-7 安装 run loop 输入源**

	- (id)init
	{
    CFRunLoopSourceContext    context = {0, self, NULL, NULL, NULL, NULL, NULL,
                                        &RunLoopSourceScheduleRoutine,
                                        RunLoopSourceCancelRoutine,
                                        RunLoopSourcePerformRoutine};
 
    runLoopSource = CFRunLoopSourceCreate(NULL, 0, &context);
    commands = [[NSMutableArray alloc] init];
 
    return self;
	}
 
	- (void)addToCurrentRunLoop
	{
    CFRunLoopRef runLoop = CFRunLoopGetCurrent();
    CFRunLoopAddSource(runLoop, runLoopSource, kCFRunLoopDefaultMode);
	}
	
###协调客户端的输入源

对于你的输入源很有用,你需要从另一个线程上操作和发信号。整体的指向一个输入源是把它关联休眠的线程直到有事情要做。事实上迫使所有的线程在你的应用程序知道关于输入源和有一个方法与它交流。

当你的输入源是第一次安装在它的 run loop 上时,通知客户端关于你的输入源的一个方法是发送注册请求。你能注册你的输入源与你想要尽可能多的客户,或者你能简单的注册它与一些与一些主要的代理然后出售你的输入源到感兴趣的客户。当`RunLoopSource` 对象是调用调度方法的时候,清单 3-8 显示通过应用程序代理和调用定义的注册方法。这个方法提供接收`RunLoopContext`对象通过`RunLoopSource` 对象和它的清单源。这个清单也显示例程使用未注册的输入源当它从 run loop 删除。

**清单 3-8 注册和删除应用程序代理的输入源**

	- (void)registerSource:(RunLoopContext*)sourceInfo;
	{
    [sourcesToPing addObject:sourceInfo];
	}
 
	- (void)removeSource:(RunLoopContext*)sourceInfo
	{
    id    objToRemove = nil;
 
    for (RunLoopContext* context in sourcesToPing)
    {
        if ([context isEqual:sourceInfo])
        {
            objToRemove = context;
            break;
        }
    }
 
    if (objToRemove)
        [sourcesToPing removeObject:objToRemove];
	}

>注意:回调函数调用在前面的清单的方法如清单3 - 4和清单3 - 6所示。


###信号输入源
在不干涉它的输入源数据,一个客户端必须发信号给源唤醒它的 run loop。发信号给源让 run loop 知道为处理源准备好了。当信号重现的时候,因为线程可能会休眠。你应该明确的一直唤醒 run loop。未能这样做可能导致延误处理输入源。

清单 3-9 显示`fireCommandsOnRunLoop` `RunLoopSource`对象的方法。当它们为源命令进行添加缓冲区时,客户端调用方法。

**清单 3-9 唤醒 run loop**

	- (void)fireCommandsOnRunLoop:(CFRunLoopRef)runloop
	{
    CFRunLoopSourceSignal(runLoopSource);
    CFRunLoopWakeUp(runloop);
	}
>注意:通过自定义输入源消息,你不应该试图处理一个`SIGHUP` 或者其他类型的 process-level 信号。Core Foundation 函数唤醒 run loop 信号是不安全的,不应该使用在你的应用程序内部的信号处理例程。更多信息关于信号处理例程,看[sigaction]手册页。

[sigaction]:
https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man2/sigaction.2.html#//apple_ref/doc/man/2/sigaction


###配置计时器源

创建一个计时器源,你所要做的是创建一个计时器对象和在你的 run loop 上安排计划,你使用[NSTimer]() 类创建一个新的计时器对象, 你在 Core Foundation 中使用不透明类型的 [CFRunLoopTimerRef]()。内部的,`NSTimer` 类只是一个扩展的核心基础,提供一些方便的功能,能够创建和安排一个计时器使用相同的方法。

在 Cocoa,你可以创建安排一个计时器,一次性使用这些类的方法:

* [scheduledTimerWithTimeInterval:target:selector:userInfo:repeats:]
* [scheduledTimerWithTimeInterval:invocation:repeats:]

[scheduledTimerWithTimeInterval:target:selector:userInfo:repeats:]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSTimer_Class/index.html#//apple_ref/occ/clm/NSTimer/scheduledTimerWithTimeInterval:target:selector:userInfo:repeats:

[scheduledTimerWithTimeInterval:invocation:repeats:]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSTimer_Class/index.html#//apple_ref/occ/clm/NSTimer/scheduledTimerWithTimeInterval:invocation:repeats:

这些方法创建计时器和添加它到当前的 run loop 线程 默认模式([NSDefaultRunLoopMode])。你可以安排一个手动的计时器,如果你想要通过创建你的[NSTimer]() 对象和使用 [SRunLoop]() 的 [addTimer:forMode:]() 方法添加它到 run loop。但是这两种技术基本上做同样的事情,但是给你不同的计时器配置控制级别。举一个例子,如果你创建计时器和手动添加它到 run loop,可以使用一个模式以外的默认模式。清单 3-10 显示怎样使用两者的技术创建计时器。第一个定时器的初始延迟1秒然后每0.1秒定期开启。第二个计时器开始后最初的0.2秒的延迟,然后每0.2秒之后开启。


[NSDefaultRunLoopMode]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSRunLoop_Class/index.html#//apple_ref/c/data/NSDefaultRunLoopMode

**清单 3-10 创建和使用NSTimer调度计时器**

	NSRunLoop* myRunLoop = [NSRunLoop currentRunLoop];
 
	// Create and schedule the first timer.
	NSDate* futureDate = [NSDate dateWithTimeIntervalSinceNow:1.0];
	NSTimer* myTimer = [[NSTimer alloc] initWithFireDate:futureDate
                        interval:0.1
                        target:self
                        selector:@selector(myDoFireTimer1:)
                        userInfo:nil
                        repeats:YES];
	[myRunLoop addTimer:myTimer forMode:NSDefaultRunLoopMode];
 
	// Create and schedule the second timer.
	[NSTimer scheduledTimerWithTimeInterval:0.2
                        target:self
                        selector:@selector(myDoFireTimer2:)
                        userInfo:nil
                        repeats:YES];


清单 3-11 代码显示需要使用 Core Foundation 函数配置一个计时器。尽管这个例子不通过任何用户定义的上下文中的信息结构,你能够使用这个结构为你的计时器传递任何你需要的数据。更多关于这个结构的内容信息,看它在[CFRunLoopTimer Reference] 的描述。

**清单 3-11 创建和使用 Core Foundation 把一个计时器列入计划**

	CFRunLoopRef runLoop = CFRunLoopGetCurrent();
	CFRunLoopTimerContext context = {0, NULL, NULL, NULL, NULL};
	CFRunLoopTimerRef timer = CFRunLoopTimerCreate(kCFAllocatorDefault, 0.1, 0.3, 0, 0,
                                        &myCFTimerCallback, &context);
 
	CFRunLoopAddTimer(runLoop, timer, kCFRunLoopCommonModes);


##配置一个基于端口的输入源

 Cocoa 和 Core Foundation 两者提供基于端口的对象之间的线程或进程之间的通信。以下部分向您展示如何设置端口通信使用几种不同类型的端口。

###配置一个 NSMachPort 对象

建立一个局部的连接与一个 [NSMachPort]() 对象,你创建端口对象和添加它到你的 run loop 主要线程。你第二个线程发动的时候,你通过相同的对象到你的线程的指令变化点函数。第二个线程能使用相同的对象发送消息返回到你的主要线程。

####执行主线程代码

清单 3-12 显示基本的线程代码启动一个次要的工作线程。Cocoa framework 执行许多干预措施的配置端口和 run loop ,`launchThread` 方法是明显较短的比 Core Foundation 相等([Listing 3-17]()),无论如何,两种反应是差不多的一致的。一个区别是,不是发送本地端口的名称工作线程,这个方法立即发送`NSPort` 对象。

**清单 3-12 主线程发动方法**

	- (void)launchThread
	{
    NSPort* myPort = [NSMachPort port];
    if (myPort)
    {
        // This class handles incoming port messages.
        [myPort setDelegate:self];
 
        // Install the port as an input source on the current run loop.
        [[NSRunLoop currentRunLoop] addPort:myPort forMode:NSDefaultRunLoopMode];
 
        // Detach the thread. Let the worker release the port.
        [NSThread detachNewThreadSelector:@selector(LaunchThreadWithPort:)
               toTarget:[MyWorkerClass class] withObject:myPort];
    }
	}

命令在你的线程之中设置一个双向的通信通道,你可能想要一个工作线程发送自己的局部端口的登记信息到你的主线程。接收登记信息让你的主线程知道一切顺利在开启的第二个线程也给你进一步把消息发送给该线程的方法。

清单 3-13 显示 [handlePortMessage:]() 主线程方法。这个方法是调用当数据到达自己线程的局部端口时。到一个登记信息到达的时候,方法恢复辅助线程直接从端口通知的端口信息,保存供日后使用。

**清单 3-13 处理 Mach 端口信息**

#define kCheckinMessage 100
 
	// Handle responses from the worker thread.
	- (void)handlePortMessage:(NSPortMessage *)portMessage
	{
    unsigned int message = [portMessage msgid];
    NSPort* distantPort = nil;
 
    if (message == kCheckinMessage)
    {
        // Get the worker thread’s communications port.
        distantPort = [portMessage sendPort];
 
        // Retain and save the worker port for later use.
        [self storeDistantPort:distantPort];
    }
    else
    {
        // Handle other messages.
    }
	}

###执行第二个线程的代码

第二个工作线程,你必须配置线程和使用指定端口通信,信息回到主线程。

清单 3-14 显示的代码设置工作线程。在创建一个自动释放池线程之后,这个方法创建一个工作对象发动线程执行。工作对象`sendCheckinMessage:` 方法(显示在 清单 3-15)创建一个局部端口给工作线程发送一个登记信息返回主线程。

**清单 3-14 反对工作线程使用 Mach 端口**

	+(void)LaunchThreadWithPort:(id)inData
	{
    NSAutoreleasePool*  pool = [[NSAutoreleasePool alloc] init];
 
    // Set up the connection between this thread and the main thread.
    NSPort* distantPort = (NSPort*)inData;
 
    MyWorkerClass*  workerObj = [[self alloc] init];
    [workerObj sendCheckinMessage:distantPort];
    [distantPort release];
 
    // Let the run loop process things.
    do
    {
        [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode
                            beforeDate:[NSDate distantFuture]];
    }
    while (![workerObj shouldExit]);
 
    [workerObj release];
    [pool release];
	}

当使用`NSMachPort` 的时候,局部和远程线程能使用一样的端口对象之间的单向通信线程。。

清单 3-15 显示第二个线程的登记例程。这个方法设置自己的本地端口为未来的通信,然后发送一个登记消息回主线程。这个方法适用端口对象接收在 `LaunchThreadWithPort:` 方法作为目标消息。

**清单 3-15  使用 Mach 端口发送登记信息**

	// Worker thread check-in method
	- (void)sendCheckinMessage:(NSPort*)outPort
	{
    // Retain and save the remote port for future use.
    [self setRemotePort:outPort];
 
    // Create and configure the worker thread port.
    NSPort* myPort = [NSMachPort port];
    [myPort setDelegate:self];
    [[NSRunLoop currentRunLoop] addPort:myPort forMode:NSDefaultRunLoopMode];
 
    // Create the check-in message.
    NSPortMessage* messageObj = [[NSPortMessage alloc] initWithSendPort:outPort
                                         receivePort:myPort components:nil];
 
    if (messageObj)
    {
        // Finish configuring the message and send it immediately.
        [messageObj setMsgId:setMsgid:kCheckinMessage];
        [messageObj sendBeforeDate:[NSDate date]];
    }
	}

###配置一个 NSMessagePort  对象
建立一个 [NSMessagePort]() 对象的一个局部连接,你无法简单的通过端口对象两者之间的线程。远程消息端口必须获得名字。使这一切成为可能在 Cocoa 需要注册你的局部端口时指定名称然后通过远程线程传递这个名字,这样就可以获得一个适当的端口对象进行通信。清单 3-16 显示创建端口和注册过程在这种情况下,您希望使用消息端口。

**清单 3-16 注册一个消息端口**

	NSPort* localPort = [[NSMessagePort alloc] init];
 
	// Configure the object and add it to the current run loop.
	[localPort setDelegate:self];
	[[NSRunLoop currentRunLoop] addPort:localPort forMode:NSDefaultRunLoopMode];
 
	// Register the port using a specific name. The name must be unique.
	NSString* localPortName = [NSString stringWithFormat:@"MyPortName"];
	[[NSMessagePortNameServer sharedInstance] registerPort:localPort
                     name:localPortName];


###在 Core Foundation 配置一个基于端口的输入源

这部分显示怎样使用 Core Foundation 设置一个双向通信通道在你的应用程序主线程和工作线程之间。

清单 3-17 显示了应用程序的主线程调用代码来启动工作线程。代码所做的第一件事是设置一个不透明类型的 [CFMessagePortRef]()从工作线程侦听消息。工作线程需要端口的名称进行连接,所以字符串值是传递到端口函数的工作线程入口。端口名称在当前的用户上下文重通常应该独一无二的；否则,你可能运行冲突。


**清单 3-17 附加一个 Core Foundation 消息端口到一个新线程**

	#define kThreadStackSize        (8 *4096)
 
	OSStatus MySpawnThread()
	{
    // Create a local port for receiving responses.
    CFStringRef myPortName;
    CFMessagePortRef myPort;
    CFRunLoopSourceRef rlSource;
    CFMessagePortContext context = {0, NULL, NULL, NULL, NULL};
    Boolean shouldFreeInfo;
 
    // Create a string with the port name.
    myPortName = CFStringCreateWithFormat(NULL, NULL, CFSTR("com.myapp.MainThread"));
 
    // Create the port.
    myPort = CFMessagePortCreateLocal(NULL,
                myPortName,
                &MainThreadResponseHandler,
                &context,
                &shouldFreeInfo);
 
    if (myPort != NULL)
    {
        // The port was successfully created.
        // Now create a run loop source for it.
        rlSource = CFMessagePortCreateRunLoopSource(NULL, myPort, 0);
 
        if (rlSource)
        {
            // Add the source to the current run loop.
            CFRunLoopAddSource(CFRunLoopGetCurrent(), rlSource, kCFRunLoopDefaultMode);
 
            // Once installed, these can be freed.
            CFRelease(myPort);
            CFRelease(rlSource);
        }
    }
 
    // Create the thread and continue processing.
    MPTaskID        taskID;
    return(MPCreateTask(&ServerThreadEntryPoint,
                    (void*)myPortName,
                    kThreadStackSize,
                    NULL,
                    NULL,
                    NULL,
                    0,
                    &taskID));
	}
	
安装端口启动线程,主线程能定期执行连接虽然它等候线程登记。当登记信息到达的时候,它发送到主线程 `MainThreadResponseHandler` 函数,显示在清单 3-18。这个函数提取工作线程的端口名称并创建一个为未来的沟通管道。

**清单 3-18 接收登记信息**

	#define kCheckinMessage 100
 
	// Main thread port message handler
	CFDataRef MainThreadResponseHandler(CFMessagePortRef local,
                    SInt32 msgid,
                    CFDataRef data,
                    void* info)
	{
    if (msgid == kCheckinMessage)
    {
        CFMessagePortRef messagePort;
        CFStringRef threadPortName;
        CFIndex bufferLength = CFDataGetLength(data);
        UInt8* buffer = CFAllocatorAllocate(NULL, bufferLength, 0);
 
        CFDataGetBytes(data, CFRangeMake(0, bufferLength), buffer);
        threadPortName = CFStringCreateWithBytes (NULL, buffer, bufferLength, kCFStringEncodingASCII, FALSE);
 
        // You must obtain a remote message port by name.
        messagePort = CFMessagePortCreateRemote(NULL, (CFStringRef)threadPortName);
 
        if (messagePort)
        {
            // Retain and save the thread’s comm port for future reference.
            AddPortToListOfActiveThreads(messagePort);
 
            // Since the port is retained by the previous function, release
            // it here.
            CFRelease(messagePort);
        }
 
        // Clean up.
        CFRelease(threadPortName);
        CFAllocatorDeallocate(NULL, buffer);
    }
    else
    {
        // Process other messages.
    }
 
    return NULL;
	}
	
配置主线程,唯一剩下的就是为新创建工作线程创建自己的端口和登记。清单 3-19 显示工作线程的端口入口函数。函数提取主线程的端口名称,并使用它来创建一个远程连接返回主线程。函数创建一个它本身的局部端口,在线程端口上安装 run loop,发送一个登记信息到主线程,包含局部端口名称。

**清单 3-19 设置线程结构**

	OSStatus ServerThreadEntryPoint(void* param)
	{
    // Create the remote port to the main thread.
    CFMessagePortRef mainThreadPort;
    CFStringRef portName = (CFStringRef)param;
 
    mainThreadPort = CFMessagePortCreateRemote(NULL, portName);
 
    // Free the string that was passed in param.
    CFRelease(portName);
 
    // Create a port for the worker thread.
    CFStringRef myPortName = CFStringCreateWithFormat(NULL, NULL, CFSTR("com.MyApp.Thread-%d"), MPCurrentTaskID());
 
    // Store the port in this thread’s context info for later reference.
    CFMessagePortContext context = {0, mainThreadPort, NULL, NULL, NULL};
    Boolean shouldFreeInfo;
    Boolean shouldAbort = TRUE;
 
    CFMessagePortRef myPort = CFMessagePortCreateLocal(NULL,
                myPortName,
                &ProcessClientRequest,
                &context,
                &shouldFreeInfo);
 
    if (shouldFreeInfo)
    {
        // Couldn't create a local port, so kill the thread.
        MPExit(0);
    }
 
    CFRunLoopSourceRef rlSource = CFMessagePortCreateRunLoopSource(NULL, myPort, 0);
    if (!rlSource)
    {
        // Couldn't create a local port, so kill the thread.
        MPExit(0);
    }
 
    // Add the source to the current run loop.
    CFRunLoopAddSource(CFRunLoopGetCurrent(), rlSource, kCFRunLoopDefaultMode);
 
    // Once installed, these can be freed.
    CFRelease(myPort);
    CFRelease(rlSource);
 
    // Package up the port name and send the check-in message.
    CFDataRef returnData = nil;
    CFDataRef outData;
    CFIndex stringLength = CFStringGetLength(myPortName);
    UInt8* buffer = CFAllocatorAllocate(NULL, stringLength, 0);
 
    CFStringGetBytes(myPortName,
                CFRangeMake(0,stringLength),
                kCFStringEncodingASCII,
                0,
                FALSE,
                buffer,
                stringLength,
                NULL);
 
    outData = CFDataCreate(NULL, buffer, stringLength);
 
    CFMessagePortSendRequest(mainThreadPort, kCheckinMessage, outData, 0.1, 0.0, NULL, NULL);
 
    // Clean up thread data structures.
    CFRelease(outData);
    CFAllocatorDeallocate(NULL, buffer);
 
    // Enter the run loop.
    CFRunLoopRun();
	}

一旦它进入它的 run loop,未来所有的事件通过 `ProcessClientRequest` 函数发送到处理的线程的端口。这个函数的实现取决于工作线程的类型,这里没有显示。