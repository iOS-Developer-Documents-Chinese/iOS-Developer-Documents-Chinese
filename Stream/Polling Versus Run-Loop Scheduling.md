获得与 Run-Loop 相对的调度
===
[原文地址](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/PollingVersusRunloop.html#//apple_ref/doc/uid/20002275-CJBEDDBG) 

 翻译人:王谦 翻译日期:2015.10.4
 
 
 一个潜在的流处理问题阻塞,一个线程写入或读取从一个流中可以有不确定的等待直到那些是(各种为)空在流放置字节或字节在流中可以读取。产生的影响,线程是在流的线程中,这可以为应用程序带来麻烦。socket 流阻塞会成为一个难题,因为是依靠在远程主机响应。
 
 
 Cocoa 流你又2个方法处理流事件:
 
 * Run - loop 安排。你安排一个流对象在一个 run loop 所以代理接收 [messages]() 报告相关的流事件不可能仅仅发生在阻塞时。读取和写入操作,`NSStreamEvent` 有关的包含是`NSStreamHasBytesAvailable`和`NSStreamHasSpaceAvailable`。

 * 查询。在一个封闭的循环中,只有在流的结束或错误,你保持流对象的请求如果它有(读取流)字节,	可获得读取或(写入流)可用的写入空间。相关的方法是`hasBytesAvailable`(NSInputStream) 和`hasSpaceAvailable`（NSOutputStream）。

 
 Run - loop 安排几乎是优于查询,这就是为什么阅读代码示例从[ Reading From Input Streams]() 和[ Writing To Output Streams]() 只显示 run loop 的使用。与查询程序被锁定在一个紧密的循环,等待流事件可能会或不会马上出现。程序 run loop 安排,可以去做其他的事情,知道它有一个流时会通知事件处理。此外,run loop 保存你来自管理状态和更有效率的查询。查询是 cpu 密集型;还有其他的事情,你可以做你的处理时间。
 
 
因此说,可以有查询的情况下是一个可行的选择。例如,如果你是移植遗留代码,你可能会选择使用查询,因为它更适合遗留代码的线程模型。清单1演示了一个方法,使用查询函数将数据写入输出流。

清单1 使用查询写入到输出流

	- (void)createNewFile {
    oStream = [[NSOutputStream alloc] initToMemory];
    [oStream open];
    uint8_t *readBytes = (uint8_t *)[data mutableBytes];
    uint8_t buf[1024];
    int len = 1024;
 
    while (1) {
        if (len == 0) break;
        if ( [oStream hasSpaceAvailable] ) {
        (void)strncpy(buf, readBytes, len);
        readBytes += len;
        if ([oStream write:(const uint8_t *)buf maxLength:len] == -1) {
            [self handleError:[oStream streamError]];
            break;
        }
        [bytesWritten setIntValue:[bytesWritten intValue]+len];
        len = (([data length] - [bytesWritten intValue] >= 1024) ? 1024 :
            [data length] - [bytesWritten intValue]);
        }
    }
    NSData *newData = [oStream propertyForKey:
        NSStreamDataWrittenToMemoryStreamKey];
    if (!newData) {
        NSLog(@"No data written to memory!");
    } else {
        [self processData:newData];
    }
    [oStream close];
    [oStream release];
    oStream = nil;
	}
	

应该指出的是，无论是查询或run loop 安排方法都是密封的防御系统。如果 NSInputStream `hasBytesAvailable` 方法或 NSOutputStream `hasSpaceAvailable` 方法返回 NO,这意味着在这两种情况下,流绝对没有可用的字节或空间。无论如何,如果任何一个方法返回 YES,它可以意味着可用字节或空间或发现的唯一方法就是尝试读或写操作(这可能导致一个瞬间的 block)。[NSStreamEventHasBytesAvailable]() 和[NSStreamEventHasSpaceAvailable]() 流事件具有相同的语义。