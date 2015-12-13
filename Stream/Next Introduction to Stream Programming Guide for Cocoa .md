#介绍 Cocoa 的流编程指南

[原文地址](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Streams.html#//apple_ref/doc/uid/10000188-SW1) 

 翻译人:王谦 翻译日期:2015.10.2
 
 在编程中流是一个基本的抽象概念:一系列连续的二进制传输从一个点到另外一个点。Cocoa 提供了三个类来表示流和方便它们在你的程序中使用: NSStream、NSInputStream 和 NSOutputStream。使用这些类的实例可以读取数据,和写入数据到文件和应用程序内存中去。你可以使用这些对象在 socket-based 连接中和远程主机交换数据。你可以在子类中数据处理类的流中获得流表现的详细说明。
 
 
 
##本文的组织

这些文档包含下列文章:


* [Cocoa Strams] Cocoa 流类的概述,描述的架构,功能,和一般用法。

* [Reading From Input Streams] 解释怎样去创建和准备一个输入流对象。通过 NSInputStream 对象的全部类型去描述怎样去处理产生的流事件。

* [Writing To Output Streams] 解释怎样去创建和准备一个输入流对象。通过 NSOutputStream 对象的全部类型去描述怎样去处理产生的流事件。

* [Polling Versus Run-Loop Scheduling] 论述两个技术之间的优劣避免使用阅读和写入时阻塞。它还说明了如何查询流数据使用 API 的数据流类。

* [Handling Stream Errors] 描述怎样在流处理发生时处理错误。

* [Setting Up Socket Streams] 解释怎么设置流对象通过 sockets 使用远程主机通讯。


[Cocoa Strams]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/CocoaStreamsOverview.html#//apple_ref/doc/uid/20002272-BABJFBBB


[Reading From Input Streams]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/ReadingInputStreams.html#//apple_ref/doc/uid/20002273-BCIJHAGD

[Writing To Output Streams]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/WritingOutputStreams.html#//apple_ref/doc/uid/20002274-BAJCABBC


[Polling Versus Run-Loop Scheduling]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/PollingVersusRunloop.html#//apple_ref/doc/uid/20002275-CJBEDDBG


[Handling Stream Errors]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/HandlingStreamError.html#//apple_ref/doc/uid/20002276-BCIDDFHF


[Setting Up Socket Streams]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/NetworkStreams.html#//apple_ref/doc/uid/20002277-BCIDFCDI


##另行参考

如果你执行 socket-based 网络流你可以发现下列有利的外部资源:

* OpenSSL - [http://www.openssl.org/](
http://www.openssl.org)

* Apache SSL - [http://www.apache-ssl.org/](http://www.apache-ssl.org/)

* SOCKS - [http://tools.ietf.org/html/rfc1928](http://tools.ietf.org/html/rfc1928)





