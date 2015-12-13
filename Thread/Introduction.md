#介绍

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/Introduction/Introduction.html#//apple_ref/doc/uid/10000057i-CH1-SW1) 

 翻译人:王谦 翻译日期:2015.10.12
 
##介绍

>重要的:这是一个初步的技术开发文档 API 。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实施的软件，应与最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供未来的 API 或技术。


线程是在一个应用程序里使它合理的同时在内部执行多种代码路径的几种技术之一。尽管新技术如操作对象和 Grand Central Dispatch (GCD) 提供一个更现代和高效的基础设施实现并发性,OS X 和 iOS 也提供接口创建和管理线程。

这是文档提供一个介绍,鼓励你探索替代的操作系统的技术用于实现并发。这是尤其如此如果你没有早先熟悉与设计技术需要实现一个线程化的应用程序。与传统的线程比较,这些替代技术简化你的工作量来实现并发的执行路径和提供更好的性能。关于这些信息技术,看[Concurrency Programming Guide]。

[Concurrency Programming Guide]:
https://developer.apple.com/library/prerelease/ios/documentation/General/Conceptual/ConcurrencyProgrammingGuide/Introduction/Introduction.html#//apple_ref/doc/uid/TP40008091


##本文的组织

这个文档有以下章节和附录:

* [About Threaded Programming] 介绍线程的概念和他们在应用程序设计中的作用。


[About Threaded Programming]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/AboutThreads/AboutThreads.html#//apple_ref/doc/uid/10000057i-CH6-SW2

* [Thread Management] 提供关于在 OS X 和你怎么样使用它们的线程技术信息。

[Thread Management]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/CreatingThreads/CreatingThreads.html#//apple_ref/doc/uid/10000057i-CH15-SW2

* [Run Loops] 提供关于怎么样管理在第二个线程中 loop 处理事件。

[Run Loops]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/RunLoopManagement/RunLoopManagement.html#//apple_ref/doc/uid/10000057i-CH16-SW1

* [Synchronization] 描述同步发生的问题和你用来防止多个线程数据损坏或崩溃你的程序的工具。

[Synchronization]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/ThreadSafety/ThreadSafety.html#//apple_ref/doc/uid/10000057i-CH8-SW1

* [Thread Safety Summary] 提供 iOS 和 OS X 和一些主要框架固有的线程安全的高度概括。

 [Thread Safety Summary]:
 https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Multithreading/ThreadSafetySummary/ThreadSafetySummary.html#//apple_ref/doc/uid/10000057i-CH12-SW1
 
##另行参考

关于线程的替代方案的信息,看[Concurrency Programming Guide]。

这个文档仅仅提供一个亮的范围使用 POSIX 线程 API。有关可用的 POSIX 线程例程的更多信息,看 [pthread] 手册页。对于 POSIX 线程和一个更深入的使用解释,看 Programming with POSIX Threads by David R. Butenhof

[pthread]:
https://developer.apple.com/library/prerelease/ios/documentation/System/Conceptual/ManPages_iPhoneOS/man3/pthread.3.html#//apple_ref/doc/man/3/pthread