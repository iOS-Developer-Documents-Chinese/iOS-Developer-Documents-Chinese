#CFHTTPStream参考

[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFHTTPStreamRef/index.html#//apple_ref/doc/uid/TP40003363)

翻译人:王谦 翻译时间:2015.9.11

>重要的

>这是一个API 的初步技术开发文档。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实施的软件，应与最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供未来的 API 或技术。



| 继承自        |   符合     |    导入语句     |
|   --------   |   --------:  | :----    |
| 不适用      | 不适用   |   @import CFNetwork       |
|          |       |    可用性 |
|          |      |  不适用 |

本文档描述了CFStream函数用于处理HTTP连接。


##函数

###CFHTTP 函数


**1.**[CFHTTPReadStreamSetRedirectsAutomatically]()
  弃用在 OS X 10.3版本

这个功能启用或禁用自动重定向 read stream。

####弃用声明

使用[kCFStreamPropertyHTTPShouldAutoredirect]()属性。

####演示

>OBJECTIVE-C
>
>void CFHTTPReadStreamSetRedirectsAutomatically ( CFReadStreamRef httpStream, Boolean shouldAutoRedirect );

####参数
| httpStream |自动 read stream 重定向启用或禁用。|
|---:|:---|
| shouldAutoRedirect |自动重定向指定流 就是 TURE.禁用重定向流就是 FALSE。|



####论述
调用这个函数之前调用[CFReadStreamOpen](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamOpen)打开 read stream。如果URL发送一个请求重定向到另一个URL,启用自动重定向允许 read stream 重定向。如果没有启用自动重定向,向发送请求的URL进行重定向请求,这个消息的响应将包含一个响应代码从 300 到 307 不等。

####可用性

可用在 OS X10.1 或以后版本

弃用在 OS X10.3 版本


<br>**2.**[CFHTTPReadStreamSetProxy]()
 弃用在 OS X 10.3版本

这个函数设置代理主机 read stream。

####弃用声明

使用 `kCFStreamPropertyHTTPProxy` 代替.

####演示
>OBJECTIVE-C
> 
>void CFHTTPReadStreamSetProxy ( CFReadStreamRef httpStream, CFStringRef proxyHost, CFIndex proxyPort );


####参数
| httpStream |read stream 的代理设置。|
|---:|:---|
| proxyHost |代理的完全限定域名或IP地址在点分十进制格式。|
| proxyPort |代理使用的端口号。|

####论述

调用这个函数如果读的 URL 流只能通过一个代理。

####可用性

可用在 OS X10.1 或以后版本

弃用在 OS X10.3 版本

**3.**[CFReadStreamCreateForHTTPRequest]()
 (OS X v10.11)
 
 创建一个读CFHTTP请求消息流。

####演示
>SWIFT
> 
>void CFHTTPReadStreamSetProxy ( CFReadStreamRef httpStream, CFStringRef proxyHost, CFIndex proxyPort );

>OBJECTIVE-C
>
>CFReadStreamRef CFReadStreamCreateForHTTPRequest ( CFAllocatorRef alloc, CFHTTPMessageRef request );


####参数

| alloc |分配器使用为新对象分配内存。通过NULL或kCFAllocatorDefault使用当前默认的分配器。|
|---:|:---|
| request |CFHTTP请求消息的 body 和 headers 设置。|


####返回值

一个新的 read stream,如果创建的对象有问题,返回NULL.请参考[Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029) 。

####论述

这个函数创建一个读取流，并将其指定要求。自动重定向默认情况下是禁用的。在创建 read stream,你可以调用 [CFReadStreamGetError]()检查流的状态。你可以调用[CFHTTPReadStreamSetRedirectsAutomatically]()启用自动重定向,或者[CFHTTPReadStreamSetProxy]()代理设置名称和端口号。序列化请求并将其发送,调用[CFReadStreamOpen]()。

如果请求的身体太长时间保持在内存中,调用[CFReadStreamCreateForStreamedHTTPRequest]()代替这个函数。


####可用性

可用在 OS X10.1 或以后版本

弃用在 OS X10.3 版本


<br>**4.**[CFReadStreamCreateForStreamedHTTPRequest]()
 (OS X v10.11)
 
 创建一个read stream CFHTTP请求 消息对象的body太长控制在内存中。
 
####演示
>SWIFT
> 
>func CFReadStreamCreateForStreamedHTTPRequest(_ alloc: CFAllocator?, _ requestHeaders: CFHTTPMessage, _ requestBody: CFReadStream) -> Unmanaged < CFReadStream >

>OBJECTIVE-C
>
>CFReadStreamRef CFReadStreamCreateForStreamedHTTPRequest ( CFAllocatorRef alloc, CFHTTPMessageRef requestHeaders, CFReadStreamRef requestBody );


####参数
| alloc |分配器使用为新对象分配内存。通过NULL或`kCFAllocatorDefault`使用当前默认的分配器。|
|---:|:---|
| requestHeaders |一个 CFHTTP 的请求头|
| requestBody |Read stream  参考请求头|

####返回值
一个新的 read stream,如果你创建一个有问题的对象。请参考[Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

####论述

这个函数创建一个 read stream 响应 requestHeaders 和 requestBody。当请求的身体太长,你不希望它是驻留在内存中,调用[CFReadStreamCreateForHTTPRequest]()代替,

因为流不能重置,read streams 创建这种方式不能为autoredirection 启用。如果 requestHeaders 内容长度头被设置,假设是正确的长度,requestBody 将报告 end-of-stream 后精确内容长度字节以便读取它。如果没有设置内容长度头,分块传输编码将被添加到 requestHeaders,和字节读取 requestBody 将分块传输。requestHeaders的主体被忽略。

创建 read stream,你可以调用  [CFReadStreamGetError]() 在任何时间检查流的状态。你可能想要调用[CFHTTPReadStreamSetProxy]()来设置一个代理的名称和端口号。序列化请求并将其发送,[CFReadStreamOpen]() 打电话。

可用在 OS X10.2 或以后版本
弃用在 OS X10.11 版本


##常量


**1.**[CFStreamErrorHTTP]()

HTTP 请求返回 read stream 的错误代码。

####演示

>SWIFT
> 
>enum CFStreamErrorHTTP : Int32 {
    case ParseFailure
    case RedirectionLoop
    case BadURL
}

>OBJECTIVE-C
>
>typedef enum {
   kCFStreamErrorHTTPParseFailure = -1,
   kCFStreamErrorHTTPRedirectionLoop = -2,
   kCFStreamErrorHTTPBadURL = -3
} CFStreamErrorHTTP;

####常量

* `kCFStreamErrorHTTPParseFailure`

 一个解析错误发生而传入消息被反序列化和附加到一个消息对象。传入消息的标题可能是格式化的不当。

 可用在 OS X v10.1 和以后


* `kCFStreamErrorHTTPRedirectionLoop`

 发现一个重定向循环。

 可用在 OS X v10.1 和以后

* `kCFStreamErrorHTTPBadURL`

 URL 没有正确的格式化

 可用在 OS X v10.1 和以后

###重要声明

>OBJECTIVE-C
>
>@import CFNetwork;
>
>SWIFT
>
> import CFNetwork


可用性

可用在 OS X 10.1 版本和以后


<br>**2.**[CFStream HTTP Property Constants]()

常量设置和复制CFStream HTTP属性。


####演示

>SWIFT
>
>let kCFStreamPropertyHTTPAttemptPersistentConnection: CFString
>
>let kCFStreamPropertyHTTPFinalURL: CFString
>
>let kCFStreamPropertyHTTPFinalRequest: CFString
>
>let kCFStreamPropertyHTTPProxy: CFString
>
>let kCFStreamPropertyHTTPProxyHost: CFString
>
>let kCFStreamPropertyHTTPProxyPort: CFString
>
>let kCFStreamPropertyHTTPRequestBytesWrittenCount: CFString
>
>let kCFStreamPropertyHTTPResponseHeader: CFString
>
>let kCFStreamPropertyHTTPSProxyHost: CFString
>
>let kCFStreamPropertyHTTPSProxyPort: CFString
>
>let kCFStreamPropertyHTTPShouldAutoredirect: CFString
>
>
<br>OBJECTIVE-C
>
>const CFStringRef kCFStreamPropertyHTTPAttemptPersistentConnection;
>
>const CFStringRef kCFStreamPropertyHTTPFinalURL;
extern const CFStringRef kCFStreamPropertyHTTPFinalRequest;

>const CFStringRef kCFStreamPropertyHTTPProxy;
>
>const CFStringRef kCFStreamPropertyHTTPRequestBytesWrittenCount;
>
>const CFStringRef kCFStreamPropertyHTTPResponseHeader;
>
>const CFStringRef kCFStreamPropertyHTTPShouldAutoredirect;


####常量

* `kCFStreamPropertyHTTPAttemptPersistentConnection`

 HTTP尝试持久连接。这个属性的值是一个 CFBoolean 。如果将此属性设置为 kCFBooleanTrue,使用现有 HTTP 流寻找一个适当的持久连接。如果不能找到一个,将尝试创建一个HTTP流。

 可用在 OS X 10.2 版本和以后

 弃用在 OS X 10.11

* `kCFStreamPropertyHTTPFinalURL`

 最后的 HTTP URL 属性。一个值类型的 CFURL 包含最后的 HTTP URL 。这个值不同于原始 HTTP 请求中的 URL  如果autoredirection 发生。不能设置这个属性。

 可用在 OS X 10.2 版本和以后

 弃用在 OS X 10.11


* `kCFStreamPropertyHTTPFinalRequest`

 HTTP最后请求属性。一个值类型的CFHTTPMessage包含所有修改后的最终消息传输的流(包括身份验证、连接策略,重定向,等等)。不能设置这个属性。

 可用在 OS X 10.5 版本和以后

 弃用在 OS X 10.11


* `kCFStreamPropertyHTTPProxy`

 HTTP代理属性。导致一个HTTP CFStream 使用 HTTP 代理,CFDictionary设置此属性的值,包括至少一个主机/端口组合中描述“CFStream 代理关键常量”[CFStream Reference](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFStreamConstants/index.html#//apple_ref/doc/uid/TP40003362)。SystemConfiguration 返回一个可用CFDictionary 没有修改。
  
 可用在 OS X 10.2 版本和以后

 弃用在 OS X 10.11


* `kCFStreamPropertyHTTPProxyHost`

 HTTP 代理主机属性.如果一个HTTP CFStream使用HTTP代理,这个属性的值是一个CFString包含代理服务器的主机名或IP地址。

 可用在 OS X 10.2 版本和以后

 弃用在 OS X 10.11

* `kCFStreamPropertyHTTPProxyPort`

 HTTP代理主机属性。如果一个HTTP CFStream 用了HTTP 代理,这个属性的值是一个 CFNumber 包含代理服务器的端口号。

 可用在 OS X 10.2 版本和以后

 弃用在 OS X 10.11

* `kCFStreamPropertyHTTPRequestBytesWrittenCount `

 HTTP请求写入字节属性。这个属性只能检索;它不能被设置,这个属性的值是一个 CFNumber 包含主体字节的 number 已经写入服务器为止。不包括HTTP header 字节数。您可以使用这个属性来的进步跟踪 HTTP 上传,需要大量的时间才能完成。

 可用在 OS X 10.3 版本和以后

 弃用在 OS X 10.11

* `kCFStreamPropertyHTTPResponseHeader`

 HTTP 响应头的性质。当复制 [CFReadStreamCopyProperty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamCopyProperty),返回一个HTTP响应消息的 heade 。

 可用在 OS X 10.1 版本和以后

 弃用在 OS X 10.11


* `kCFStreamPropertyHTTPSProxyHost `

 HTTPS代理主机属性。如果使用 HTTPS CFStream 代理,这个属性的值是一个 CFString 包含代理服务器的主机名或 IP 地址。

 可用在 OS X 10.1 版本和以后

 弃用在 OS X 10.11

* `kCFStreamPropertyHTTPSProxyPort `

 HTTPS 代理主机属性。如果使用 HTTPS CFStream 代理,这个属性的值是一个 CFNumber 包含代理服务器的端口号。


 可用在 OS X 10.1 版本和以后

 弃用在 OS X 10.11

* `kCFStreamPropertyHTTPShouldAutoredirect`

 HTTP 应该自动重定向的性质。将此属性设置为启用 autoredirection `kCFBooleanTrue` ;将此属性设置为禁用autoredirection `kCFBooleanFalse`。


 可用在 OS X 10.2 版本和以后

 弃用在 OS X 10.11

####论述

CFStream HTTP 性质常量用于指定 HTTP 属性设置当调用 [CFReadStreamSetProperty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamSetProperty) 或 [CFWriteStreamSetProperty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/c/func/CFWriteStreamSetProperty)。他们也用于指定 HTTP 属性复制当调用 [CFReadStreamCopyProperty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamCopyProperty) or [CFWriteStreamCopyProperty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/c/func/CFWriteStreamCopyProperty).

####可用性

可用在 OS X 10.1 版本和以后



<br>[Error Domains]()

网域详细的 CFHTTPStream 调用错误。

####演示
>SWIFT
>
>let kCFStreamErrorDomainHTTP: Int32

>OBJECTIVE-C
>
>extern const SInt32 kCFStreamErrorDomainHTTP;


####常量

* `kCFStreamErrorDomainHTTP`

 CFHTTPStream 错误网域返回错误。

 可用在 OS X 10.1 版本和以后

####论述

确定一个错误的来源,检查用户信息字典包含在 CFError 对象返回的函数调用或调用 CFErrorGetDomain 传入 CFError 对象和网域返回你想读取的值。
