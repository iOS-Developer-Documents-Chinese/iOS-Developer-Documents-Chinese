#读取输入流

[原文地址](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/ReadingInputStreams.html#//apple_ref/doc/uid/20002273-BCIJHAGD) 

 翻译人:王谦 翻译日期:2015.10.2
 

在 Cocoa,阅读从一个`NSInputStream`实例包含的几个步骤:

1.[Create and initialize]() `NSInputStream`从源数据的实例。

2.在 run loop 中安排流对象并打开流。

3.流对象处理事件报告代理。

4.当没有更多的数据读取，[dispose]() 流的对象。


下面的讨论深入到每一个更详细的步骤中。

>注意:本文档中的示例展示的策略调度 run loop 流对象,设置一个代理处理流事件。你可以使用查询,而不是 run loop 调度如果你喜欢这种方式。然而,run - loop 调度和委托是多方面原因的优先方法(在 [Polling Versus Run-Loop Scheduling] 描述),这就是为什么在本文档中突出显示。

[Polling Versus Run-Loop Scheduling]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/PollingVersusRunloop.html#//apple_ref/doc/uid/20002275-CJBEDDBG


##准备流对象


开始使用一个`NSInputStream` 对象你必须有(在第一次定位查找后,如果有必要的话)流的一个数据源。数据源可以是一个文件,一个 `NSData`对象,或一个网络 socket。


>重要的:输入流对象程序初始化,从不同的网络 socket 程序及其他的两个数据源,本文中未涉及。了解关于网络连接初始化一个`NSInputStream`实例,看[Setting Up Socket Streams]。

[Setting Up Socket Streams]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/NetworkStreams.html#//apple_ref/doc/uid/20002277-BCIDFCDI



NSInputStream 的初始化和工厂方法允许你创建和初始化一个实例从一个 NSData 或文件。



清单1 创建和实列化一个 NSInputStream 对象

	- (void)setUpStreamForFile:(NSString *)path {
    // iStream is NSInputStream instance variable
    iStream = [[NSInputStream alloc] initWithFileAtPath:path];
    [iStream setDelegate:self];
    [iStream scheduleInRunLoop:[NSRunLoop currentRunLoop]
        forMode:NSDefaultRunLoopMode];
    [iStream open];
    }
    
    
如这个例子所示,在你创建对象之后你应该设置代理(更多常常不是本身)。代理接收`stream:handleEvent:`[messages]()来自NSInputStream 对象,对象是安排在 run loop 和相关流事件报告,比如当有流读取字节。


在打开流之前开始的数据流,发送一个`scheduleInRunLoop:forMode:` 消息给流对象安排它接收流事件 run loop。通过做这个,你帮助代理避开block在读取流没有数据的时候。如果流是发生在另一个线程,一定要流对象安排在线程的 run loop。你应该从不试图进入一个流计划从一个不同的线程拥有一个流的 run loop。最后,发送`NSInputStream `实例 一个打开消息从输入源启动数据流。


##处理流事件

在一个流对象打开发送后,你可以找出它的状态,无论是否可以读取字节,和任何错误的本质与以下信息:

`streamStatus`
<br>`hasBytesAvailable`
<br>`streamError`


返回的状态是一个`NSStreamStatus` 恒定的指示因此流是打开的,读取,在流的最后,等等。返回的错误是一个`NSError`对象封装所发生的任何错误信息。(详见参考文档)NSStream NSStreamStatus的描述和其他流类型。)


更多的重点,一但流对象是打开的,它控制发送`stream:handleEvent:`消息其代理直到它遇到的流。这些消息包括一个参数与一个`NSStreamEvent`常数表明事件的类型。`NSInputStream对象`,最常见的类型的事件,[NSStreamEventOpenCompleted](), [NSStreamEventHasBytesAvailable](),[NSStreamEventEndEncountered]()。代理通常是[NSStreamEventHasBytesAvailable]()最感兴趣的事件。清单2展示了一个好方法来处理这种类型的事件。


清单2 处理一个字节 - 可用的事件

	- (void)stream:(NSStream *)stream handleEvent:(NSStreamEvent)eventCode {
 
    switch(eventCode) {
        case NSStreamEventHasBytesAvailable:
        {
            if(!_data) {
                _data = [[NSMutableData data] retain];
            }
            uint8_t buf[1024];
            unsigned int len = 0;
            len = [(NSInputStream *)stream read:buf maxLength:1024];
            if(len) {
                [_data appendBytes:(const void *)buf length:len];
                // bytesRead is an instance variable of type NSNumber.
                [bytesRead setIntValue:[bytesRead intValue]+len];
            } else {
                NSLog(@"no buffer!");
            }
            break;
        }
        // continued
        
       
在这个实现的`stream:handleEvent:`委托使用 switch 语句来识别传入`NSStreamEvent`常数。如果这个常数是`NSStreamEventHasBytesAvailable `,代理 `NSMutableData对象首先缓慢地创建(如果需要)一个(_data)检索到的字节。然后它声明了一个缓冲区的大小(1024字节,在这种情况下),并调用流对象的 read:maxLength: 方法,填满缓冲区的指定的字节数。如果读取操作成功从流中读取字节,代理添加这些字节到 `NSMutableData` 对象。

在一个时间里有没有一个明确的字节可以读取的牢固指南,虽然它或许能够可能在一个流事件中阅读全部的数据,这取决于流的长度(也就是说它的字节数)以及核心的反应,包含设备和 socket 的特性。最好的方法是使用一些合理缓冲区的大小,如 512 字节,1 kb(在上面的示例中),或者一个页面大小(4 kb)。

`NSInputStream`对象经历流错误处理的时候,它数据流和通知停止代理和一个`NSStreamEventErrorOccurred `。代理应该在`stream:handleEvent:`方法中处理错误,描述在[Handling Stream Errors]。


[Handling Stream Errors]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/HandlingStreamError.html#//apple_ref/doc/uid/20002276-BCIDDFHF


##安排流对象

当一个 `NSInputStream` 对象到达流的末尾,它发送代理`NSStreamEventEndEncountered` 事件`stream:handleEvent:` 消息。对象的代理处理应该通过它所做准备的镜像的对象。换句话说,它应该首先就关联流对象。从 run loop 删除它,最后在释放它。清单3给出了如何做到这一点的一个例子。

清单 3 关闭和释放 NSInputStream 对象。

	- (void)stream:(NSStream *)stream handleEvent:(NSStreamEvent)eventCode
	{
    switch(eventCode) {
        case NSStreamEventEndEncountered:
        {
            [stream close];
            [stream removeFromRunLoop:[NSRunLoop currentRunLoop]
                forMode:NSDefaultRunLoopMode];
            [stream release];
            stream = nil; // stream is ivar, so reinit it
            break;
        }
        // continued ...
    }
	}