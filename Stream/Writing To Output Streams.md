写入输出流
===
[原文地址](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/WritingOutputStreams.html#//apple_ref/doc/uid/20002274-BAJCABBC) 

 翻译人:王谦 翻译日期:2015.10.4
 

使用一个 `NSOutputStream` 实例写入输出流需要几个步骤:

1.[Create and initialize]() 一个实例 `NSOutputStream` 写数据的存储库
2.在 run loop 中安排流对象并打开流。

3.流对象处理事件报告代理。

4.如果流对象写入数据到内存中,通过 `NSStreamDataWrittenToMemoryStreamKey` 属性的要求获得数据。

5.当没有更多的数据读取，[dispose]() 流的对象。


下面的讨论深入到每一个更详细的步骤中。

>注意:本文档中的示例展示的策略调度 run loop 流对象,设置一个代理处理流事件。你可以使用查询,而不是 run loop 调度如果你喜欢这种方式。然而,run - loop 调度和委托是多方面原因的优先方法(在[Polling Versus Run-Loop Scheduling] 描述),这就是为什么在本文档中突出显示。

[Polling Versus Run-Loop Scheduling]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/PollingVersusRunloop.html#//apple_ref/doc/uid/20002275-CJBEDDBG


##准备流对象


开始使用一个`NSOutputStream` 你必须指定一个目的地将数据写入到流中,这个目的地可以是一个输出流的文件,一个 C 缓冲区,应用程序内存,一个网络 socket。


>重要的:输出流对象程序初始化,从不同的网络 socket 程序及其他的两个数据源,本文中未涉及。了解关于网络连接初始化一个`NSOutputStream `实例,看[Setting Up Socket Streams]。

[Setting Up Socket Streams]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/NetworkStreams.html#//apple_ref/doc/uid/20002277-BCIDFCDI



NSOutputStream 的初始化和工厂方法允许你创建和初始化一个实例从一个文件,一个缓冲区,或内存,清单1显示创建一个 `NSOutputStream` 实例将写入数据到应用程序内存中。



清单1 创建和实列化一个 NSOutputStream 对象到内存

	- (void)createOutputStream {
    	NSLog(@"Creating and opening NSOutputStream...");
    	// oStream is an instance variable
    	oStream = [[NSOutputStream alloc] initToMemory];
    	[oStream setDelegate:self];
    	[oStream scheduleInRunLoop:[NSRunLoop currentRunLoop]
        	forMode:NSDefaultRunLoopMode];
    	[oStream open];
	}
    
    
如清单 1 代码所示,在你创建对象之后你应该设置代理(更多常常不是本身)。代理接收`stream:handleEvent:`[messages]()来自`NSOutputStream` 对象,当对象 stream-related 事件报告,如当流具有字节的空间。

在打开流之前开始的数据流,发送一个`scheduleInRunLoop:forMode:` 消息给流对象安排它接收流事件 run loop。通过做这个,你帮助代理避开 block 在流不能接收更多字节的时候。如果流是发生在另一个线程,一定要流对象安排在线程的 run loop。你应该从不试图进入一个流计划从一个不同的线程拥有一个流的 run loop。最后,发送`NSOutputStream`实例 一个打开消息从输入源启动数据流。


##处理流事件

在一个流对象打开发送后,你可以找出它的状态,无论是否可以读取字节,和任何错误的本质与以下信息:

`streamStatus`
<br>`hasBytesAvailable`
<br>`streamError`


返回的状态是一个`NSStreamStatus` 恒定的指示因此流是打开的,读取,在流的最后,等等。返回的错误是一个`NSError`对象封装所发生的任何错误信息。(详见参考文档)NSStream NSStreamStatus的描述和其他流类型。)


更多的重点,一但流对象是打开的,它控制发送`stream:handleEvent:`消息其代理直到它遇到的流。这些消息包括一个参数与一个`NSStreamEvent`常数表明事件的类型。`NSInputStream对象`,最常见的类型的事件,[NSStreamEventOpenCompleted](), [NSStreamEventHasBytesAvailable](),`NSStreamEventEndEncountered`。代理通常是[NSStreamEventHasBytesAvailable]()最感兴趣的事件。清单2展示了一个好方法来处理这种类型的事件。


清单2 处理一个空间 - 可用的事件

	- (void)stream:(NSStream *)stream handleEvent:(NSStreamEvent)eventCode
	{
    switch(eventCode) {
        case NSStreamEventHasSpaceAvailable:
        {
            uint8_t *readBytes = (uint8_t *)[_data mutableBytes];
            readBytes += byteIndex; // instance variable to move pointer
            int data_len = [_data length];
            unsigned int len = ((data_len - byteIndex >= 1024) ?
                1024 : (data_len-byteIndex));
            uint8_t buf[len];
            (void)memcpy(buf, readBytes, len);
            len = [stream write:(const uint8_t *)buf maxLength:len];
            byteIndex += len;
            break;
        }
        // continued ...
    }
	}
        
       
在这个实现的`stream:handleEvent:`委托使用 switch 语句来识别传入`NSStreamEvent`常数。如果这个常数是`NSStreamEventHasSpacesAvailable `,代理 `NSMutableData`对象首先缓慢地创建(如果需要)一个(_data)检索到的字节。然后它声明了一个缓冲区的大小(1024字节,在这种情况下),声明一个缓冲区的大小和数量的数据复制到缓冲区。下一个委托调用输出流对象的`write:maxLength:` 方法,填满缓冲区的指定的字节数。最后它提出了指数用于推进`readBytes`指针在接下来的操作。



如果代理接收一个 [NSStreamEventHasSpaceAvailable]事件和不输入任何到流。它不接收更多的可用空间事件从 run loop,直到`NSOutputStream` 对象接收多个字节。当发生这种情况时,可用空间事件的重新启动 run loop 。如果这个场景很可能实现,你可以代理设置一个标志当它不写入流根据接收一个`NSStreamEventHasSpaceAvailable`事件。稍后,当您的程序有更多字节写,它可以检查这个标志,如果设置,直接写入输出流实例。


在一个时间里有没有一个明确的字节可以读取的牢固指南,虽然它或许能够可能在一个流事件中阅读全部的数据,这取决于流的长度(也就是说它的字节数)以及核心的反应,包含设备和 socket 的特性。最好的方法是使用一些合理缓冲区的大小,如 512 字节,1 kb(在上面的示例中),或者一个页面大小(4 kb)。

`NSOutputStream `对象经历流错误处理的时候,它数据流和通知停止代理和一个`NSStreamEventErrorOccurred`。代理应该在`stream:handleEvent:`方法中处理错误,描述在[Handling Stream Errors]。


[Handling Stream Errors]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/HandlingStreamError.html#//apple_ref/doc/uid/20002276-BCIDDFHF


##安排流对象

当一个 `NSOutputStream` 对象结束写入数据到一个输出流,它发送代理`NSStreamEventEndEncountered` 事件`stream:handleEvent:` 消息。此时代理应该处理的流对象通过它所做准备的镜像对象。换句话说,它应该首先关闭流对象,从 run loop 删除它,最后释放它。此外,如果目的地`NSOutputStream`对象应用程序内存(也就是说,您创建了实例使用`initToMemory`或工厂方法`outputStreamToMemory`),你可能要在内存中检索数据。清单3展示了如何做所有这些事情。

清单 3 关闭和释放 NSOutputStream 对象。

	- (void)stream:(NSStream *)stream handleEvent:(NSStreamEvent)eventCode
	{
    switch(eventCode) {
        case NSStreamEventEndEncountered:
        {
            NSData *newData = [oStream propertyForKey:
                NSStreamDataWrittenToMemoryStreamKey];
            if (!newData) {
                NSLog(@"No data written to memory!");
            } else {
                [self processData:newData];
            }
            [stream close];
            [stream removeFromRunLoop:[NSRunLoop currentRunLoop]
                forMode:NSDefaultRunLoopMode];
            [stream release];
            oStream = nil; // oStream is instance variable
            break;
        }
        // continued ...
    }
	}
	
你获取了流数据写入内存通过发送`NSOutputStream`对象一个 `propertyForKey:`消息,指定一个key `NSStreamDataWrittenToMemoryStreamKey` 流对象返回数据在一个 `NSData` 对象。