处理流的错误
===
[原文地址](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/HandlingStreamError.html#//apple_ref/doc/uid/20002276-BCIDDFHF) 

 翻译人:王谦 翻译日期:2015.10.8
 
 
偶尔,特别是与 socket,流可能遇到错误以至于进一步妨碍到处理流错误。通常,错误指示大约不在一个流的一端,如远程主机的崩溃或删除文件的数据流。有少许的客户端流能做在大多数错误发生时除了向用户报告错误。虽然一个流对象有报告一个错误,在关闭之前可以查询它的状态,它不能重复的操作读取和写入。


如果一个错误发生在几个方面,`NSStream` 和`NSOutputStream` 类通知你:

* 如果流对象是安排在一个 run loop,对象记录一个`NSStreamEventErrorOccurred` 事件通过它的 [delegate]() 在一个`stream:handleEvent:`[message]() 。

* 在任何时间,客户端会发送一个`streamStatus` 消息到一个流对象和看它是否返回`NSStreamStatusError`。

* 如果你视图写入到一个`NSOutputStream` 对象通过发送它`write:maxLength:` 和它返回－1,写入错误发生。


一旦你有了决定,一个流对象经历了一个错误,你可以查询对象和一个`streamError` 消息从而获得关于错误的更多信息(来自一个`NSError`对象)。接下来,告知用户错误。清单 1 展示了如何运行的代理 loop-scheduled 流对象可能处理一个错误。


清单 1 处理流错误

	- (void)stream:(NSStream *)stream handleEvent:(NSStreamEvent)eventCode {
    NSLog(@"stream:handleEvent: is invoked...");
 
    switch(eventCode) {
        case NSStreamEventErrorOccurred:
        {
            NSError *theError = [stream streamError];
            NSAlert *theAlert = [[NSAlert alloc] init];
            [theAlert setMessageText:@"Error reading stream!"];
            [theAlert setInformativeText:[NSString stringWithFormat:@"Error %i: %@",
                [theError code], [theError localizedDescription]]];
            [theAlert addButtonWithTitle:@"OK"];
            [theAlert beginSheetModalForWindow:[NSApp mainWindow]
                modalDelegate:self
                didEndSelector:@selector(alertDidEnd:returnCode:contextInfo:)
                contextInfo:nil];
            [stream close];
            [stream release];
            break;
        }
        // continued ....
    }
	}