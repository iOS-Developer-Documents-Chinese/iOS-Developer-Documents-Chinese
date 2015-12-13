处理流的错误
===
[原文地址](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/NetworkStreams.html#//apple_ref/doc/uid/20002277-BCIDFCDI) 

 翻译人:王谦 翻译日期:2015.10.8
 
 
你可以使用 CFStream API 去建立一个 socket连接, 与流对象(或对象)创建一个结果,从远程主机发送数据和接收数据。你可以配置安全连接。



####基础步骤

在 iOS 中 NSStream 类不支持远程主机连接。CFStream 支持这个行为,然而,你一旦用 CFStream API 创建你的流,你可以拿 toll-free 抢先桥接 CFStream 和 NSStream 把你的 CFStreams 扔到 NSStreams。假如调用  [CFStreamCreatePairWithSocketToHost] 函数,提供一个主机名和一个端口号,接收`CFReadStreamRef` 和`CFWriteStreamRef` 两者的给定主机。然后你可以将这些对象的 NSInputStream 和NSOutputStream 进行。


清单1阐述了使用 [CFStreamCreatePairWithSocketToHost]。做为创建`CFReadStreamRef` 对象和 `CFWriteStreamRef` 对象的示例。如果你想要接收仅仅是这些对象之一,只需为不想要的对象的参数值指定空。

[CFStreamCreatePairWithSocketToHost]:
https://developer.apple.com/library/ios/documentation/CoreFoundation/Reference/CFStreamConstants/index.html#//apple_ref/c/func/CFStreamCreatePairWithSocketToHost

[CFStreamCreatePairWithSocketToHost]:
https://developer.apple.com/library/ios/documentation/CoreFoundation/Reference/CFStreamConstants/index.html#//apple_ref/c/func/CFStreamCreatePairWithSocketToHost



清单 1 设置一个 socket 流网络

	- (IBAction)searchForSite:(id)sender
	{
    NSString *urlStr = [sender stringValue];
    if (![urlStr isEqualToString:@""]) {
        NSURL *website = [NSURL URLWithString:urlStr];
        if (!website) {
            NSLog(@"%@ is not a valid URL");
            return;
        }
 
        CFReadStreamRef readStream;
        CFWriteStreamRef writeStream;
        CFStreamCreatePairWithSocketToHost(NULL, (CFStringRef)[website host], 80, &readStream, &writeStream);
 
        NSInputStream *inputStream = (__bridge_transfer NSInputStream *)readStream;
        NSOutputStream *outputStream = (__bridge_transfer NSOutputStream *)writeStream;
        [inputStream setDelegate:self];
        [outputStream setDelegate:self];
        [inputStream scheduleInRunLoop:[NSRunLoop currentRunLoop] forMode:NSDefaultRunLoopMode];
        [outputStream scheduleInRunLoop:[NSRunLoop currentRunLoop] forMode:NSDefaultRunLoopMode];
        [inputStream open];
        [outputStream open];
 
        /* Store a reference to the input and output streams so that
           they don't go away.... */
        ...
    }
	}
	
如果你通过无效的参数,一个或两个请求 `CFReadStreamRef` 和 `CFWriteStreamRef` 对象是空的。一旦你抛下 CFStreams 到 NSStreams,设置 [delegate](),在一个 run loop 安排流,像往常一样打开流。代理应该开始接收流事件消息(`stream:handleEvent:`)。看 [Reading From Input Streams] 和 [Writing To Output Streams] 的更多信息。


[Reading From Input Streams]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/ReadingInputStreams.html#//apple_ref/doc/uid/20002273-BCIJHAGD

[Writing To Output Streams]:
https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/Streams/Articles/WritingOutputStreams.html#//apple_ref/doc/uid/20002274-BAJCABBC



##配置连接安全

在你打开流对象之前,你可能需要设置安全和其他特性为连接到远程主机(可能是,举个例子,一个 HTTPS 服务)。`NSStream` 定义属性影响的安全 TCP / IP socket 连接在两个方面:

* Socket 安全层(SSL)。

 一个安全协议使用数字验证提高数据加密,服务认证,完整的信息,和(可选的)客户端认证 TCP / IP 连接。
 
* SOCKS 代理服务

服务器,客户端应用程序和一个真正的服务器之间通过 TCP / IP连接。它拦截请求的服务器,如果它不能从一个最近请求文件的缓存中实现,把它们转发到真正的服务器。SOCKS 代理服务帮助提高性能,在网络上也可以用于过滤请求。

对于SSL安全,`NSStream` 定义多种安全程度属性(举一个例子,[NSStreamSocketSecurityLevelSSLv2])。你设置这些属性通过发送 `setProperty:forKey:` 使用关键 `NSStreamSocketSecurityLevelKey` 流对象,在这个示例消息:

[NSStreamSocketSecurityLevelSSLv2]:
https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSStream_Class/index.html#//apple_ref/doc/c_ref/NSStreamSocketSecurityLevelSSLv2

	[inputStream setProperty:NSStreamSocketSecurityLevelTLSv1 forKey:NSStreamSocketSecurityLevelKey];
	
你必须在你打开流之前设置属性。一旦它打开了,它通过握手协议来找出什么程度的SSL安全连接在另一侧使用。如果与指定的属性安全程度不能兼容,流对象生成一个错误事件。然而,如果你请求协商安全级别([(NSStreamSocketSecurityLevelNegotiatedSSL]),安全级别成为最高的可以实现两边的连接。不过,如果你想设置一个 SSL 安全级别时,远程主机是不安全的,会生成一个错误。


[(NSStreamSocketSecurityLevelNegotiatedSSL]:
https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSStream_Class/index.html#//apple_ref/doc/c_ref/NSStreamSocketSecurityLevelNegotiatedSSL


配置 SOCKS 代理服务器连接,你需要从 NSStreamSOCKSProxy NameKey 建立一个字典的 key(举一个例子,[NSStreamSOCKSProxyHostKey])。每个 key 的值是名称所指的 SOCKS 代理设置。使用 `setProperty:forKey:`,设置字典值 [NSStreamSOCKSProxyConfigurationKey]。


[NSStreamSOCKSProxyHostKey]:
https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSStream_Class/index.html#//apple_ref/doc/c_ref/NSStreamSOCKSProxyHostKey


[NSStreamSOCKSProxyConfigurationKey]:
https://developer.apple.com/library/ios/documentation/Cocoa/Reference/Foundation/Classes/NSStream_Class/index.html#//apple_ref/doc/c_ref/NSStreamSOCKSProxyConfigurationKey


##开始一个 HTTP 请求

如果你打开了一个 HTTP 服务连接(即,一个网址),然后你可能需要启动一个事务和服务器发送一个 HTTP 请求。好时机,让这个请求当 `NSOutputStream` 的委托对象接收一个 `NSStreamEventHasSpaceAvailable` 事件通过一个 `stream:handleEvent:` 消息。清单2显示了代理创建一个 HTTP GET 请求并将其写入输出流,之后立即关闭流对象。


清单 2 制作一个 HTTP GET 请求

	- (void)stream:(NSStream *)stream handleEvent:(NSStreamEvent)eventCode {
    NSLog(@"stream:handleEvent: is invoked...");
 
    switch(eventCode) {
        case NSStreamEventHasSpaceAvailable:
        {
            if (stream == oStream) {
                NSString * str = [NSString stringWithFormat:
                    @"GET / HTTP/1.0\r\n\r\n"];
                const uint8_t * rawstring =
                    (const uint8_t *)[str UTF8String];
                [oStream write:rawstring maxLength:strlen(rawstring)];
                [oStream close];
            }
            break;
        }
        // continued ...
    }
	}
	
	
##更多信息


更多地了解使用流网络,阅读 [Networking Overview]。

[Networking Overview]:
https://developer.apple.com/library/ios/documentation/NetworkingInternetWeb/Conceptual/NetworkingOverview/Introduction/Introduction.html#//apple_ref/doc/uid/TP40010220