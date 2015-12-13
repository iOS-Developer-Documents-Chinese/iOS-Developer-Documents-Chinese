理解高速缓存
===

[原文地址](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Conceptual/URLLoadingSystem/Concepts/CachePolicies.html#//apple_ref/doc/uid/20001843-BAJEAIEE)

翻译人:彭兆旻 日期:2015.10.5

系统对URL加载提供了基于磁盘和内存缓存的响应机制.缓存使得 APP 减少其对网络连接的依赖并且提高了它的性能.

##对请求使用缓存

一个 `NSURLRequest` 实例说明了本地缓存是如何使用的通过设定缓存策略为
`NSURLRequestCachePolicy` 的值之一:`NSURLRequestUseProtocolCachePolicy`, `NSURLRequestReloadIgnoringCacheData`, `NSURLRequestReturnCacheDataElseLoad`, 或者 `NSURLRequestReturnCacheDataDontLoad`.

一个 `NSURLRequest` 实例的默认缓存策略是 `NSURLRequestUseProtocolCachePolicy`.`NSURLRequestUseProtocolCachePolicy` 行为是一个特定的协议并且被定义为协议的最佳整合策略.

设置缓存策略为 `NSURLRequestReloadIgnoringCacheData` 使得 URL 加载系统从数据源加载数据,完全忽略缓存.

`NSURLRequestReturnCacheDataElseLoad` 缓存策略使得 URL 加载系统使用缓存数据,忽略它的年限和过期的日期,并且在没有缓存版本的情况下从数据源加载数据.

`NSURLRequestReturnCacheDataDontLoad` 缓存策略允许指定 APP 只返回缓存中的数据.在此策略下试图创建一个 `NSURLConnection` 或者 `NSURLDownload` 的实例会立即返回 nil 如果响应不在缓存内.这和脱机状态下永远无法建立一个网络连接是一样的道理.

>注意:目前,只有响应 HTTP 和 HTTPS 的请求被缓存. FTP 和文件协议尝试访问数据源根据它所允许的缓存策略.自定义的 `NSURLProtocol` 类可以选择提供缓存.

## HTTP 协议的高速缓存使用语义

最复杂的缓存使用策略是当请求使用 HTTP 协议并且设置缓存策略为 `NSURLRequestUseProtocolCachePolicy`.

如果一个请求的 `NSCachedURLResponse` 不存在，URL 加载系统会从数据源获取数据.

如果对一个请求的缓存相应存在, URL 加载系统会检查响应来确定如果说它指定内容需要被重新验证.

如果内容必须要重新验证, URL 加载系统会发送一个 HEAD 请求给数据源来查看数据是否被改动. 如果没有改变，URL 加载系统返回缓存的响应。如果已经改变,URL 加载系统从数据源获取数据.

如果缓存的相应并没有指定内容必须重新验证,URL 加载系统会检查最大时效或者缓存响应中指定的期限.如果一个缓存请求时间足够近,URL 加载系统会返回缓存内容.如果缓存响应时间比较旧,加载系统会发送一个 HEAD 请求到数据源来确定数据是否发生更改.如果发生了更改, URL 加载系统从数据的原始源获取数据,否则,将返回缓存的数据.

RFC 2616，13节 [http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html#sec13) 指定语义所涉及到的细节.


##以编程的方式控制缓存

默认情况下,链接的数据被缓存下来通过请求的缓存策略,通过 `NSURLProtocol` 的子类处理请求.

如果你的 APP 需要更精确的编程控制缓存（当然如果协议支持缓存）,你可以实现一个委托方法允许你的 APP 去确定基于每一个请求的基础上的特定响应需要被缓存.

* 对于 `NSURLSession` 数据和上传任务，实现在 [URLSession:dataTask:willCacheResponse:completionHandler:](https://developer.apple.com/library/prerelease/watchos/documentation/Foundation/Reference/NSURLSessionDataDelegate_protocol/index.html#//apple_ref/occ/intfm/NSURLSessionDataDelegate/URLSession:dataTask:willCacheResponse:completionHandler:) 方法里.
  
  这个委托方法被调用仅用于数据和上传任务.而对于下载的缓存策略通过特定的缓存策略所决定.
 
* 对于 `NSURLConnection`,实现在 [connection:willCacheResponse:](https://developer.apple.com/library/prerelease/watchos/documentation/Foundation/Reference/NSURLConnectionDataDelegate_protocol/index.html#//apple_ref/occ/intfm/NSURLConnectionDataDelegate/connection:willCacheResponse:)方法.

对于 `NSURLSession`,你的委托方法调用了一个 block 去告诉回话需要缓存. 对于 `NSURLConnect`，你的委托方法返回了连接需要被缓存的对象.

在这两种情况下,委托提供以下值:

* 所提供响应的对象允许缓存

* 新创建的缓存响应缓存修改后的响应——比如说,一个存储策略的响应要求缓存到内存中而不是磁盘里

* NULL 用来阻止缓存

你的委托方法还可以插入对象到 `userInfo` 的字典里与 `NSCachedURLResponse` 对象相关联,使得这些对象被储存在缓存里作为响应的一部分.

>重要:如果你使用 `NSURLSession` 并且你实现了这个委托方法,你的委托方法必须调用完成的处理方法,否则你的 APP 将会内存泄露.

列表 7-1 的例子阻止对 HTTPS 的响应缓存在磁盘上.它也将添加当前日期到用户信息字典内的响应缓存.
 
Listing 7-1 connection:withCacheResponse: 实现

	-(NSCachedURLResponse *)connection:(NSURLConnection 	*)connection
                 willCacheResponse:(NSCachedURLResponse 	*)cachedResponse
	{
   		 NSCachedURLResponse *newCachedResponse = cachedResponse;
 
    	 NSDictionary *newUserInfo;
         newUserInfo = [NSDictionary dictionaryWithObject:[NSDate date]
                                                 forKey:@"Cached Date"];
    	 if ([[[[cachedResponse response] URL] scheme] isEqual:@"https"]) {
    #if ALLOW_IN_MEMORY_CACHING
         newCachedResponse = [[NSCachedURLResponse alloc]
                                initWithResponse:[cachedResponse response]
                                    data:[cachedResponse data]
                                    userInfo:newUserInfo
                                    storagePolicy:NSURLCacheStorageAllowedInMemoryOnly];
	#else // !ALLOW_IN_MEMORY_CACHING
         newCachedResponse = nil
	#endif // ALLOW_IN_MEMORY_CACHING
    } else {
        newCachedResponse = [[NSCachedURLResponse alloc]
                                initWithResponse:[cachedResponse response]
                                    data:[cachedResponse data]
                                    userInfo:newUserInfo
                                    storagePolicy:[cachedResponse storagePolicy]];
    }
        return newCachedResponse;
	}
			




