#CFNetServices参考

[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFNetServiceRef/index.html#//apple_ref/doc/uid/TP40003365)
翻译人:王谦  日期:2015.9.16

>重要的

>这是一个API 的初步技术开发文档。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实施的软件，应与最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供未来的 API 或技术。


| 继承自        |   符合     |    导入语句     |
|   --------   |   --------:  | :----    |
| 不适用      | 不适用   |   @import CFNetwork       |
|          |       |    可用性 |
|          |      |  不适用 |


CFNetServices API Bonjour的一部分,苹果 zero-configuration 网络( ZEROCONF )的实现。CFNetServices API允许您注册一个网络服务,如打印机或文件服务器,这样就可以找到的名字或浏览的服务类型和域。应用程序可以使用 CFNetServices API 来发现网络上可用的服务和找到所有访问信息,如名字,IP地址和端口号,需要使用每个服务。


实际上,Bonjour注册和发现结合本地DNS服务器的功能和可路由协议组,允许应用程序提供用户友好的浏览可用的可路由协议组选择器使用开放协议,如  Multicast DNS (mDNS)。Bonjour 给应用程序方便地访问服务在本地 IP 网络不需要服务来支持一个可路由协议组堆栈,和不需要一个DNS服务器在本地网络。

Bonjour 一个完整的描述,请看 [Bonjour Overview]()。



##函数

####创建网络服务对象

**1.**[CFNetServiceCreate]()

创建一个网络服务对象的一个实例。


####演示
>SWIFT
>
>func CFNetServiceCreate(_ alloc: CFAllocator?, _ domain: CFString, _ serviceType: CFString, _ name: CFString, _ port: Int32) -> Unmanaged < CFNetService >
>OBJECTIVE-C
>
>CFNetServiceRef CFNetServiceCreate ( CFAllocatorRef alloc, CFStringRef domain, CFStringRef serviceType, CFStringRef name, SInt32 port );

####参数
| alloc |使用分配器为新对象分配内存。通过 `NULL` 或 [kCFAllocatorDefault]() 使用当前默认的分配器。|
|---:|:---|
| domain |要注册的域名 CFNetService ;不能为空。调用[CFNetServiceBrowserCreate]()和[CFNetServiceBrowserSearchForDomains]() 注册域名。|
| type |服务注册的类型;不能为 `NULL`。有效的服务类型的列表,请看 [http://www.iana.org/assignments/port-numbers](http://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml)。 |
| name |如果实例将用于一个注册服务，需要唯一的 name 。在被创建的 DNS 记录服务注册时这个name 将成为实例 name 的一部分。如果实例将被用来解析服务,这个 name 应该是用来将机构或服务解析的。|
| port |本地 IP 端口,主机字节顺序,该服务接受连接,通过 zero 占位服务。用占位服务，该服务将不被发现,如果另一个客户端试图注册相同的 name 那么与这个 name 的冲突将发生。大多数应用程序不需要使用占位服务。|


####返回值

一个新的网络服务对象,如果不能创建实例就返回 NULL。请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。


####论述

如果服务在 DNS TXT 取决于信息记录,调用[CFNetServiceSetProtocolSpecificInformation]()

如果 CFNetService 在异步模式下运行,调用[CFNetServiceSetClient]()为在异步模式下运行的服务做准备。调用 CFNetServiceScheduleWithRunLoop 在 run loop 调度服务。调用 [CFNetServiceRegister]() 使服务可用。

如果 CFNetService 在同步模式下运行,调用 [CFNetServiceRegister]() 。


终止服务,是在异步模式下运行。调用 [CFNetServiceCancel]() 和 [CFNetServiceUnscheduleFromRunLoop]()。

终止服务,是在同步模式下运行。调用 [CFNetServiceCancel]()。

####特殊注意事项
 
这个函数是线程安全的。

####可用性

可用在 OS X10.2 和以后版本 
 

<br>**2.** [CFNetServiceCreateCopy]()

CFNetService 对象创建一个副本。


####演示
>SWIFT
>
>func CFNetServiceCreateCopy(_ alloc: CFAllocator?, _ service: CFNetService) -> Unmanaged < CFNetService >
>
>
>OBJECTIVE-C
>
>CFNetServiceRef CFNetServiceCreateCopy ( CFAllocatorRef alloc, CFNetServiceRef service );

####参数

| alloc |使用分配器为新对象分配内存。通过 NULL 或 [kCFAllocatorDefault]() 使用当前默认的分配器。|
|---:|:---|
| service |CFNetServiceRef 被复制,不能为空。如果不是一个有效的 CFNetServiceRef 服务,这个函数是未定义的行为。|


####返回值

复制服务,包括之前解决所有数据,如果服务不能被复制就返回 NULL。请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

####论述

这个函数创建指定的副本 CFNetService 服务。


####特殊注意事项
 
这个函数是线程安全的。

####可用性

可用在 OS X10.2 和以后版本 

<br>**3.**[CFNetServiceMonitorCreate]()

创建 NetServiceMonitor 对象的一个实例,注意记录变化。

####演示
>SWIFT
>
>func CFNetServiceMonitorCreate(_ alloc: CFAllocator?, _ theService: CFNetService, _ clientCB: CFNetServiceMonitorClientCallBack, _ clientContext: UnsafeMutablePointer < CFNetServiceClientContext > ) -> Unmanaged < CFNetServiceMonitor >
>
>
>OBJECTIVE-C
>
>CFNetServiceMonitorRef CFNetServiceMonitorCreate ( CFAllocatorRef alloc, CFNetServiceRef theService, CFNetServiceMonitorClientCallBack clientCB, CFNetServiceClientContext *clientContext );


####参数

| alloc |使用分配器为新对象分配内存。通过`NULL 或 [kCFAllocatorDefault]() 使用当前默认的分配器。|
|---:|:---|
| theService |CFNetService 监控。|
| clientCB |指向回调函数的指针也被称为当用服务改变相关记录;不能为空。|
| clientContext |指针传递给用户定义的上下文信息,调用回调时的指定的回调 clientCB;不能为空。详情,请参阅[CFNetServiceClientContext]()。 |


####返回值

CFNetServiceMonitor 的一个新实例,如果不能创建监视器就返回 NULL。请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

####论述

这个函数创建一个 CFNetServiceMonitor,注意纪录变化。

如果 CFNetServiceMonitor 运行在异步模式下,调用 [CFNetServiceMonitorScheduleWithRunLoop]()安排监控 run loop。然后调用 [CFNetServiceMonitorStart]()开始监控。发生变化时,`clientCB` 指定的回调函数将被调用。详情,请参阅[CFNetServiceMonitorClientCallBack]()。

如果 CFNetServiceMonitor 在同步模式下运行,调用  [CFNetServiceMonitorStart]()。

停止一个监视器在异步模式下运行,调用 [CFNetServiceMonitorStop]() 和 [CFNetServiceMonitorUnscheduleFromRunLoop]()。


停止监控,在同步模式下运行,调用 CFNetServiceMonitorStop。

如果您不再需要更改监控记录,调用 [CFNetServiceMonitorStop]() 停止监控 和调用 [CFNetServiceMonitorInvalidate]() 监控失效所以它不能被再次使用。然后调用 `CFRelease` 释放与CFNetServiceMonitorRef 相关联的内存。


####特殊注意事项
 
这个函数是线程安全的。

####可用性

可用在 OS X10.4 和以后版本 

<br>**4.**[CFNetServiceBrowserCreate]()

创建一个网络服务浏览器的一个实例对象。

####演示
>SWIFT
>
>func CFNetServiceBrowserCreate(_ alloc: CFAllocator?, _ clientCB: CFNetServiceBrowserClientCallBack, _ clientContext: UnsafeMutablePointer < CFNetServiceClientContext > ) -> Unmanaged < CFNetServiceBrowser >
>
>
>OBJECTIVE-C
>
>CFNetServiceBrowserRef CFNetServiceBrowserCreate ( CFAllocatorRef alloc, CFNetServiceBrowserClientCallBack clientCB, CFNetServiceClientContext *clientContext );

####参数
| alloc |使用分配器为新对象分配内存。通过`NULL`或[kCFAllocatorDefault]()使用当前默认的分配器。|
|---:|:---|
| clientCB |回调函数时被称为域和求得服务;不能为 NULL。有关详细信息,看 [CFNetServiceBrowserClientCallBack]()|
| clientContext |调用 `clientCB` 时使用上下文信息;不能为 NULL。细节, 看[CFNetServiceClientContext]。|

####返回值

一个新的浏览器对象,如果不能创建实例就返回 NULL。请参考 [The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。


####论述

这个函数创建一个网络服务浏览器的一个实例对象,称为 CFNetServiceBrowser ,可以用来搜索领域和服务。


在异步模式下使用 CFNetServiceBrowser,调用 [CFNetServiceBrowserScheduleWithRunLoop]() 。然后调用 [CFNetServiceBrowserSearchForDomains]() 和 [CFNetServiceBrowserSearchForServices]() 分别使用 CFNetServiceBrowser 搜索服务和域。通过搜索您的应用程序结果从一个 run loor指定具体的回调函数 。通过调用 [CFNetServiceBrowserStopSearch]() 持续搜索直到你停止搜索。

如果你没有调用 [CFNetServiceBrowserScheduleWithRunLoop](),搜素 CFNetServiceBrowser 在同步模式的结果。调用了 [CFNetServiceBrowserSearchForDomains ]() 和 [CFNetServiceBrowserSearchForServices]() block 直到有搜索结果,在这种情况下,调用 clientCB 指定的回调函数,直到停止搜索 通过从另一个线程调用 [CFNetServiceBrowserStopSearch](),或发生错误。


关闭一个在异步模式下运行的 CFNetServiceBrowser,调用 [CFNetServiceBrowserStopSearch](),接着 [CFNetServiceBrowserUnscheduleFromRunLoop]() 和[CFNetServiceBrowserInvalidate]()。

####特殊注意事项
 
这个函数是线程安全的。

####可用性

可用在 OS X10.2 和以后版本 


##CFNetServices 函数

**1.**[CFNetServiceBrowserInvalidate]()

无效网络服务浏览器的一个实例对象。

####演示
>SWIFT
>
>func CFNetServiceBrowserInvalidate(_ browser: CFNetServiceBrowser)
>
>
>OBJECTIVE-C
>
>void CFNetServiceBrowserInvalidate ( CFNetServiceBrowserRef browser );


####参数

| browser | CFNetServiceBrowser 无效,
通过先前的调用 [CFNetServiceBrowserCreate]()。|
|---:|:---|


####论述

这个函数使指定网络服务浏览器对象的实例无效。任何搜索使用指定的实例调用该函数时的进步都停止了。一个无效的浏览器不能安排在一个 run loop 中,其回调函数从不调用。


####特殊注意事项
 
这个函数是线程安全的,只要另一个线程不会改变 CFNetServiceBrowserRef 同时相同。

####可用性

可用在 OS X10.2 和以后版本 

<br>**2.**[CFNetServiceBrowserScheduleWithRunLoop]()

安排一个 CFNetServiceBrowser run loop 。

####演示
>SWIFT
>
>
> func CFNetServiceBrowserScheduleWithRunLoop(_ browser: CFNetServiceBrowser, _ runLoop: CFRunLoop, _ runLoopMode: CFString)
OBJECTIVE-C

>OBJECTIVE-C
>
>
>void CFNetServiceBrowserScheduleWithRunLoop ( CFNetServiceBrowserRef browser, CFRunLoopRef runLoop, CFStringRef runLoopMode );


####参数
| browser |把 CFNetServiceBrowser 安排一个 run loop;不能为 NULL 。|
|---:|:---|
| runLoop |浏览器的 run loop 计划;不能为 NULL 。|
| runLoopMode |安排的浏览器模式;不能为 NULL 。|

####论述

这个函数指定 CFNetServiceBrowser 安排 run loop,从而将浏览器在异步模式。 run loop 将调用浏览器的回调函数传递域和服务搜索的结果。调用者负责确保至少把一个 run loop 浏览器列入运行计划。

####特殊注意事项

这个函数是线程安全的。

####可用性

可用在 OS X10.2 和以后版本


<br>**3.**[CFNetServiceBrowserSearchForDomains]()

搜索域。

####演示
>SWIFT
>
>
> func CFNetServiceBrowserSearchForDomains(_ browser: CFNetServiceBrowser, _ registrationDomains: Bool, _ error: UnsafeMutablePointer<CFStreamError>) -> Bool

>OBJECTIVE-C
>
>
>Boolean CFNetServiceBrowserSearchForDomains ( CFNetServiceBrowserRef browser, Boolean registrationDomains, CFStreamError *error );


####参数
| browser |CFNetServiceBrowser,获得之前的调用[CFNetServiceBrowserCreate](),是完成搜索,不能为`NULL`。|
|---:|:---|
| registrationDomains |搜索仅仅注册域名就是 TURE;搜索域,可以浏览服务就是 FALSE。对于这个 CFNetServices API 的版本,注册域名是由 mDNS 应答运行在同一台机器上调用应用程序维护本地域。| 
| error |一个指向 CFStreamError 结构,如果发生错误,将被设置为错误和错误的域和传递给回调函数。由于这个特殊的调用传递 NULL 如果你不想接收错误。|

####返回值

如果搜索就开始了(异步模式)返回 TURE;如果另一个搜索已经在进行CFNetServiceBrowser 或者发生错误 就返回 FALSE。


####论述

这个函数使用一个 CFNetServiceBrowser 搜索域。搜索将继续,直到搜索通过调用 [CFNetServiceBrowserStopSearch]() 来取消。如果 registrationDomains 是 TURE, 这个函数只搜索服务可以注册的域名。如果 registrationDomains 是 FALSE,这个函数搜索域,可以浏览服务。当找到一个域, CFNetServiceBrowser 创建时指定的回调函数,通过 CFStringRef 包含域的一个实例发现。


在异步模式,如果开始搜索这个函数返回 TURE 。否则,它将返回FALSE。

在同步模式,这个功能块,直到搜索停止通过从另一个线程,调用 [CFNetServiceBrowserStopSearch ](),在这种情况下,返回FALSE, 或者 直到错误发生。

####特殊注意事项

这个函数是线程安全。


####可用性

可用在 OS X 10.2 和以后版本


<br>**4.**[CFNetServiceBrowserSearchForServices]()

搜索指定类型的服务领域。

####演示
>SWIFT
>
>
> func CFNetServiceBrowserSearchForServices(_ browser: CFNetServiceBrowser, _ domain: CFString, _ serviceType: CFString, _ error: UnsafeMutablePointer < CFStreamError > ) -> Bool

>OBJECTIVE-C
>
>
>Boolean CFNetServiceBrowserSearchForServices ( CFNetServiceBrowserRef browser, CFStringRef domain, CFStringRef serviceType, CFStreamError *error );


####参数
| browser |通过之前调用CFNetServiceBrowserCreate 的 [CFNetServiceBrowser](),执行搜索,不能为`NULL`。|
|---:|:---|
| domain |域搜索服务类型;不能为`NULL`。可用的域搜索,调用 [CFNetServiceBrowserSearchForDomains]()。| 
| type |搜索的服务类型;不能为`NULL`。为有效的服务类型的列表,看[http://www.iana.org/assignments/port-numbers。]()|
| error |一个指向CFStreamError结构,如果出现错误,将被设置为错误和错误的域传递给回调函数。由于这个特殊的调用,通过 NULL 如果你不想接收可能发生的错误。|


####返回值
如果开始搜索(异步模式)就返回 TURE ;如果另一个搜索已经在进行或者这CFNetServiceBrowser发生错误 就是 FALSE 。

####论述

这个函数搜索指定的域服务匹配指定的服务类型。搜索将继续,直到取消搜索 调用 [CFNetServiceBrowserStopSearch]()。当找到匹配,指定的回调函数调用 CFNetServiceBrowser 创建并通过一个实例的CFNetService 代表服务被发现。


在异步模式,如果开启搜索,函数返回 TURE。否则返回 FALSE。


在同步模式,这个函数 blosks ,直到搜索停止通过调用 任何线程的CFNetServiceBrowserStopSearch ,在这种情况下,该函数返回 FALSE,或者直到出现错误。


####特殊注意事项

这个函数是线程安全的。

对于任何一个 CFNetServiceBrowser,只有一个域搜索或一个服务可以同时进行。

####可用性

可用在 OS X10.2和以后版本 


<br>**5.**[CFNetServiceBrowserStopSearch]()

停止服务域搜索。

####演示
>SWIFT
>
>
>func CFNetServiceBrowserStopSearch(_ browser: CFNetServiceBrowser, _ error: UnsafeMutablePointer < CFStreamError > )

>OBJECTIVE-C
>
>void CFNetServiceBrowserStopSearch ( CFNetServiceBrowserRef browser, CFStreamError *error );

####参数
| browser |用于启动 CFNetServiceBrowser 的搜索;不能为 NULL 。|
|---:|:---|
| error |[CFStreamError]()结构指针将被传递给回调函数与此相关CFNetServiceBrowser(如果搜索正在进行异步模式)或所指出的错误参数当[CFNetServiceBrowserSearchForDomains]()或[CFNetServiceBrowserSearchForServices]()返回(如果搜索正在进行同步模式)。设置域字段 `kCFStreamErrorDomainCustom`和错误字段一个适当的值。|


####论述

这个函数停止搜索开始前调用一个 [CFNetServiceBrowserSearchForDomains]() 或 [CFNetServiceBrowserSearchForServices]()。异步和同步搜索,调用这个函数会导致回调函数与 CFNetServiceBrowser 每个域或服务被称为一次发现。如果搜索是异步的,错误传递给回调函数。如果搜索是同步的,调用这个函数的原因`CFNetServiceBrowserSearchForDomains` 或 `CFNetServiceBrowserSearchForServices` 返回 FALSE。如果错误参数调用指着 CFStreamError 结构,当这个函数被称为[CFStreamError]()结构包含错误代码和错误代码的域设置。

####特殊注意事项
这个函数是线程安全。

如果你停止了异步搜索,在这之前调用函数,调用[CFNetServiceBrowserUnscheduleFromRunLoop](),接着是 [CFNetServiceBrowserInvalidate。]()


可用性

可用在 OS X 10.2 和以后版本

<br>**6.**[CFNetServiceBrowserUnscheduleFromRunLoop]()

未计划一个 CFNetServiceBrowser 的 run loop 和 模式。


####演示
>SWIFT
>
>
>func CFNetServiceBrowserUnscheduleFromRunLoop(_ browser: CFNetServiceBrowser, _ runLoop: CFRunLoop, _ runLoopMode: CFString)

>OBJECTIVE-C
>
>void CFNetServiceBrowserUnscheduleFromRunLoop ( CFNetServiceBrowserRef browser, CFRunLoopRef runLoop, CFStringRef runLoopMode );


####参数

| browser |未安排的 CFNetServiceBrowser;不能为`NULL`。 |
|---:|:---|
| runLoop | run loop; 不能为`NULL` 。|
| runLoopMode |浏览器是计划外的模式;不能为`NULL`。|


####论述

调用这个函数异步关闭浏览器运行。完成关闭,调用 [CFNetServiceBrowserInvalidate]() 接着是 [ CFNetServiceBrowserStopSearch]()。


####特殊注意事项

这个函数是线程安全。

####可用性

可用在 OS X 10.2 和以后版本


<br>**7.**[CFNetServiceCancel]()

取消注册服务或解析服务。


####演示
>SWIFT
>
>
>func CFNetServiceCancel(_ theService: CFNetService)

>OBJECTIVE-C
>
>void CFNetServiceCancel ( CFNetServiceRef theService );


####参数

| theService | CFNetService,获得之前调用的[CFNetServiceCreate](),注册或解析被取消。|
|---:|:---|


####论述

这个函数取消服务注册,启动一个 [CFNetServiceRegister],从而使服务不可用。并且取消解析服务,启动 [ CFNetServiceResolve]()。


如果你关闭异步服务,你应该首先调用[CFNetServiceUnscheduleFromRunLoop]() 和 [ CFNetServiceSetClient]() `clientCB` 设置为 `NULL`。然后调用这个函数。

如果关闭同步服务,从另一个线程调用这个函数。

并且这个函数取消解析服务,如果成功连接使用了服务,你的回调函数收到一个 IP 地址,你将要取消一个解析服务。除了,你也许想要取消一个解析服务,如果解析用户觉得长时间等待用户可以取消操作。


####特殊注意事项

这个函数是线程安全的。


####可用性

可用在 OS X 10.2 和以后版本


<br>**8.**[CFNetServiceCreateDictionaryWithTXTData]()

使用三种记录数据来创建一个字典。


####演示
>SWIFT
>
>
>func CFNetServiceCreateDictionaryWithTXTData(_ alloc: CFAllocator?, _ txtRecord: CFData) -> Unmanaged < CFDictionary > ?

>OBJECTIVE-C
>
>CFDictionaryRef CFNetServiceCreateDictionaryWithTXTData ( CFAllocatorRef alloc, CFDataRef txtRecord );

####参数

| alloc | 使用分配器为新对象分配内存。通过`NULL`或[kCFAllocatorDefault]()使用当前默认的分配器。|
|---:|:---|
| txtRecord |TXT [CFNetServiceGetTXTData]()返回的记录数据。|


####返回值

`txtRecord` 解析的字典包含一对  key/value,如果 `txtRecord` 不能解析就返回 `NULL` 。每个 key 在字典是一个 CFString 对象。每个 value 是一个 CFData 对象。请参考[The Create Rule]  [The Create Rule]。

[The Create Rule]:https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029


####特殊注意事项

这个函数是线程安全的。


####可用性

可用在 OS X 10.4 和以后版本


<br>**9.**[CFNetServiceCreateTXTDataWithDictionary]()

通过[CFNetServiceSetTXTData]()设置使CFDataRef 相匹配的key/value。

####演示
>SWIFT
>
>func CFNetServiceCreateTXTDataWithDictionary(_ alloc: CFAllocator?, _ keyValuePairs: CFDictionary) -> Unmanaged < CFData > ?


>OBJECTIVE-C
>
>CFDataRef CFNetServiceCreateTXTDataWithDictionary ( CFAllocatorRef alloc, CFDictionaryRef keyValuePairs );

####参数
|alloc|使用分配器为新的对象分配内存。通过 NULL 或 [kCFAllocatorDefault]() 使用默认的分配器。|
|---:|:---|
| keyValuePairs | CFDictionaryRef 包含key/value对将被放置在一个TXT记录。每个 key 必须是一个 CFStringRef 和 每个 value 应该是一个 CFDataRef 或 一个 CFStringRef 。(参见下面讨论 CFStringRefs 有关价值观的额外信息。) 如果提供任何其他的数据类型,这个函数失败。它的 key 和 value 长度应该不超过 255 字节。|

####返回值

一个 CFData 对象停止包含 keyValuePairs,如果字典不能停止,就返回 NULL。请参考 [The Create Rule][The Create Rule]

[The Create Rule]:https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029


####论述

这个函数停止指定的 key/value 对字典`keyValuePairs`CFDataRef 通过 CFNetServiceSetTXTData 相匹配。注意,这个函数不是停止一个通用函数 CFDictionaryRefs 。

字典中的键引用 `keyValuePairs` 必须 CFDataRefs 和必须CFStringRefs 值。停止CFStringRefs 的任何值转换为 CFDataRefs 代表utf - 8 字节的字符串。值的类型不是 CFDataRefs 编码,所以任何 CFStringRefs 转换为 CFDataRefs 保持 CFDataRefs CFDataRef 这个函数由 [CFNetServiceCreateDictionaryWithTXTData]() 处理。


####特殊注意事项

这个函数是线程安全的。


####可用性

可用在 OS X 10.2 和以后版本


<br>**10.** [CFNetServiceGetAddressing]()


从 CFNetService 获得 IP 地址。


####演示
>SWIFT
>
>func CFNetServiceGetAddressing(_ theService: CFNetService) -> Unmanaged < CFArray > ?


>OBJECTIVE-C
>
>CFArrayRef CFNetServiceGetAddressing ( CFNetServiceRef theService );


####参数

| theService | CFNetService 获得 IP 地址。不能为 NULL 。|
|---:|:---|

####返回值

CFArray 包含一个 CFDataRef 让每个 IP 地址 返回 NULL。每个 CFDataRef 由一个包含 IP 地址和服务 sockaddr 构成。
这个函数 返回 NULL 如果 不知道服务地址,因为 [CFNetServiceResolve]() 不能被 theService 调用。


####论述

这个函数从 CFNetService 获取 IP 地址。代表性的, 调用 
[ CFNetServiceBrowserSearchForServices]() 获得 CFNetService。在这之前调用函数,调用 [ CFNetServiceResolve]() 更新 CFNetService 的 IP 地址。

####特殊注意事项

这个函数在一个 thread-safe 获得数据,如果这个服务是从另一个线程上改变,目标主机名是不安全的。



####可用性

可用在 OS X 10.2 和以后版本


<br>**11.**[CFNetServiceGetTargetHost]()

把一个 CFNetService 作为主机来查询。

####演示
>SWIFT
>
>func CFNetServiceGetTargetHost(_ theService: CFNetService) -> Unmanaged < CFString > ?


>OBJECTIVE-C
>
>CFStringRef CFNetServiceGetTargetHost ( CFNetServiceRef theService );


####参数
| theService | 网络服务查询。|
|---:|:----|

####返回值

机构提供的目标主机名,不知道目标主机就返回 NULL。(如果它不知道目标主机将要解析。)


####特殊注意事项

这个函数是线程安全的,如果这个服务是从另一个线程上改变,目标主机名是不安全的。


####可用性

可用在 OS X 10.4 和以后版本


<br>**12.**[CFNetServiceGetDomain]()

从 CFNetService 得到域。

####演示
>SWIFT
>
>func CFNetServiceGetDomain(_ theService: CFNetService) -> Unmanaged < CFString >


>OBJECTIVE-C
>
>CFStringRef CFNetServiceGetDomain ( CFNetServiceRef theService );


####参数
| theService |获得 CFNetService 的域;不能为 NULL。|
|---:|:---|

####返回值

一个 CFString 包含一个 CFNetService 域。

####论述

这个函数获取一个 CFNetService 的域。

####特殊注意事项

这个函数是线程安全的。这个函数在一个 thread-safe 获得数据,如果这个服务是从另一个线程上改变,目标主机名是不安全的。


####可用性

可用在 OS X 10.2 和以后版本


<br>**13.**[CFNetServiceGetName]()

获得 CFNetService 的名字。

####演示
>SWIFT
>
>func CFNetServiceGetName(_ theService: CFNetService) -> Unmanaged < CFString >


>OBJECTIVE-C
>
>CFStringRef CFNetServiceGetName ( CFNetServiceRef theService );


####参数

| theService |获得 CFNetService  的名字。不能为 NULL。|
|---:|:---|


####返回值

一个 CFString 对象包含一个名字,表现在 CFNetService 的服务。

####论述

这个函数获得一个 CFNetService。


####特殊注意事项

这个函数是线程安全的。这个函数在一个 thread-safe 获得数据,如果这个服务是从另一个线程上改变,目标主机名是不安全的。



####可用性

可用在 OS X 10.2 和以后版本



<br>**14.**[CFNetServiceGetPortNumber]()

这个函数获取 CFNetService 的端口号。


####演示
>SWIFT
>
>func CFNetServiceGetPortNumber(_ theService: CFNetService) -> Int32


>OBJECTIVE-C
>
>SInt32 CFNetServiceGetPortNumber ( CFNetServiceRef theService );


####参数
| theService |获得 CFNetService 特定的信息协议。不能为 NULL。请注意,为了获得特定于协议的信息,你必须在这个函数之前调用[CFNetServiceResolve]()或[CFNetServiceResolveWithTimeout]() 解析 `theService`。|
|---:|:---|


####返回值

服务的端口号。


####可用性

可用在 OS X 10.5 和以后版本


<br>**15.**[CFNetServiceGetProtocolSpecificInformation]() 在 OS X 10.4 版本弃用

这个函数获取 CFNetService 特定协议的信息。

####弃用的声明

用 [CFNetServiceGetTXTData ]()代替。


####演示
>OBJECTIVE-C
>
>CFStringRef CFNetServiceGetProtocolSpecificInformation ( CFNetServiceRef theService );


####参数
| theService |获得 CFNetService 特定的信息协议。不能为 NULL。请注意,为了获得特定于协议的信息,你必须在这个函数之前调用[CFNetServiceResolve]()或[CFNetServiceResolveWithTimeout]() 解析 `theService`。|
|---:|:---|

####返回值

CFString 对象包含一个特定协议信息,如果没有信息就返回 NULL。

####特殊注意事项

这个函数通过线程安全的方式获得数据。如果从另一个线程上改变,那么这个数据本身是不安全的。


####可用性

可用在 OS X 10.2 和以后版本

弃用在 OS X 10.4 版本

<br>**16.**[CFNetServiceGetTXTData]()

查询包含 TXT 记录的网络服务。

####演示
>SWIFT
>
>func CFNetServiceGetTXTData(_ theService: CFNetService) -> Unmanaged < CFData >?


>OBJECTIVE-C
>
>CFDataRef CFNetServiceGetTXTData ( CFNetServiceRef theService );


####参数
| theService |获得网络服务下 TXT 记录的数据。不能为 NULL 。请注意,为了获得 TXT 记录的数据,你必须在这函数之前调用 [CFNetServiceResolve ]() 或 [ CFNetServiceResolveWithTimeout]()用来解析 `theService`。|
|---:|:---|

####返回值

通过[CFNetServiceCreateDictionaryWithTXTData]() 匹配 CFDataRef 请求对象包含的 TXT 数据。如果  TXT 数据服务没有解析就返回 NULL。

####论述

这个函数获得 TXT 服务记录的数据。


####特殊注意事项

这个函数通过线程安全的方式获得数据,如果从另一个线程上改变,那么这个数据本身是不安全的。

####可用性

可用在 OS X 10.4 和以后版本


<br>**17.**[CFNetServiceGetType]()

获取 CFNetService 的类型。

####演示
>SWIFT
>
>func CFNetServiceGetType(_ theService: CFNetService) -> Unmanaged < CFString >


>OBJECTIVE-C
>
>CFStringRef CFNetServiceGetType ( CFNetServiceRef theService );

####参数
| theService |获得 CFNetService 的类型。不能为 NULL。|
|---:|:---|

####返回值

CFString 对象包含的类型来自 CFNetService。

####论述

这个函数获得一个 CFNetService 类型。

####特殊注意事项

这个函数是线程安全。这个函数通过线程安全的方式获得数据,如果从另一个线程上改变,那么这个数据本身是不安全的。



####可用性

可用在 OS X 10.2 和以后版本



<br>**18.**[CFNetServiceMonitorInvalidate]()

使一个网络服务监控对象无效。



####演示
>SWIFT
>
>func CFNetServiceMonitorInvalidate(_ monitor: CFNetServiceMonitor)


>OBJECTIVE-C
>
>void CFNetServiceMonitorInvalidate ( CFNetServiceMonitorRef monitor );


####参数
| monitor | CFNetServiceMonitor 无效。不能为 NULL。|
|---:|:---|


####论述

这个函数监控指定的无效网络服务,不能再一次使用。你在这之前调用函数，你应该调用 [CFNetServiceMonitorStop]()。如果监控之前已经停止,那么这个啊函数已经停止对你监控。

####特殊注意事项

这个函数是线程安全。



####可用性

可用在 OS X 10.4 和以后版本


<br>**19.**[CFNetServiceMonitorScheduleWithRunLoop]()

安排一个 CFNetServiceMonitor run loop。

####演示
>SWIFT
>
>func CFNetServiceMonitorScheduleWithRunLoop(_ monitor: CFNetServiceMonitor, _ runLoop: CFRunLoop, _ runLoopMode: CFString)


>OBJECTIVE-C
>
>void CFNetServiceMonitorScheduleWithRunLoop ( CFNetServiceMonitorRef monitor, CFRunLoopRef runLoop, CFStringRef runLoopMode );



####参数
| theService | 安排一个 CFNetServiceMonitor run loop。不能为 NULL。|
|---:|:---|
| runLoop |安排 run loop 监控。不能为 NULL。|
| runLoopMode |安排监控模式。不能为 NULL。|


####论述

安排一个指定的 run loop 监控,在异步模式下的监视器。调用者负责确保至少一个的 run loop 监控计划正在运行。


####特殊注意事项

这个函数是线程安全。



####可用性

可用在 OS X 10.4 和以后版本


<br>**20.**[CFNetServiceMonitorStart]()

启动监控。


####演示
>SWIFT
>
>func CFNetServiceMonitorStart(_ monitor: CFNetServiceMonitor, _ recordType: CFNetServiceMonitorType, _ error: UnsafeMutablePointer < CFStreamError > ) -> Bool


>OBJECTIVE-C
>
>Boolean CFNetServiceMonitorStart ( CFNetServiceMonitorRef monitor, CFNetServiceMonitorType recordType, CFStreamError *error );



####参数
| monitor | CFNetServiceMonitor,创建调用 CFNetServiceMonitorCreate,要开始了。|
|---:|:---|
| recordType | CFNetServiceMonitorType 记录监控的类型。合适的值请看[CFNetServiceMonitorType Constants]()。|
| error |指针指向一个CFStreamError结构。如果错误出现,就输出,结构域的字段将被设置为错误代码的域和错误字段将被设置为一个适当的错误代码。如果不想接收到错误代码和域就设置参数为 NULL 。|


####返回值

如果异步模式启动成功就返回 TURE 。如果启动监控 同步 和异步 发生错误就返回 FALSE。或者 如果调用异步监控 [CFNetServiceMonitorStop]()。

####论述

这个由 recordType 指定类型函数开始监测变化的记录。如果监视器已经运行 CFNetServiceMonitorRef 指定的相关服务,这个函数返回 FALSE。

同步监控,这个函数的 blocks 直到监控停止调用 CFNetServiceMonitorStop,
在这种情况下,这个函数返回 FALSE。

异步监控,这个函数返回 TURE 或者 FALSE,成功取决于监测开始。

####特殊注意事项

这个函数是线程安全。



####可用性

可用在 OS X 10.4 和以后版本


<br>**21.**[CFNetServiceMonitorStop]()

停止 CFNetServiceMonitor 。

####演示
>SWIFT
>
>func CFNetServiceMonitorStop(_ monitor: CFNetServiceMonitor, _ error: UnsafeMutablePointer < CFStreamError > )


>OBJECTIVE-C
>
>void CFNetServiceMonitorStop ( CFNetServiceMonitorRef monitor, CFStreamError *error );


####参数
| monitor | CFNetServiceMonitor,创建调用 CFNetServiceMonitorCreate,要停止了。|
|---:|:---|
| error |指针指向一个CFStreamError结构或 NULL 。同步的监控,设置错误的字段结构 non-zero 值,当CFNetServiceMonitorStart返回时设置你想要CFStreamError结构。注意,当它返回, `CFNetServiceMonitorStart` 返回 FALSE.如果监视异步启动,将错误字段设置为 non-zero 值希望您监视的回调时得到调用。如果参数是 NULL, CFStreamError 结构使用的默认值:域设置为`kCFStreamErrorDomainNetServices` 和错误代码设置 kCFNetServicesErrorCancel 。|


####论述

这个函数停止指定的监控。调用 [CFNetServiceMonitorStart ]() 如果你想要再一次启动监控。

如果你想要停止监控不在需要记录监控的变化,调用[CFNetServiceMonitorInvalidate]() 代替这个函数。

####特殊注意事项

这个函数是线程安全。



####可用性

可用在 OS X 10.4 和以后版本


<br>**22.**[CFNetServiceMonitorUnscheduleFromRunLoop]()

不安排一个 CFNetServiceMonitor 的 run loop。

####演示
>SWIFT
>
>func CFNetServiceMonitorUnscheduleFromRunLoop(_ monitor: CFNetServiceMonitor, _ runLoop: CFRunLoop, _ runLoopMode: CFString)


>OBJECTIVE-C
>
>void CFNetServiceMonitorUnscheduleFromRunLoop ( CFNetServiceMonitorRef monitor, CFRunLoopRef runLoop, CFStringRef runLoopMode );


####参数
| monitor |未安排 CFNetServiceMonitor ; 不能为 NULL。|
|---:|:---|
| runLoop |run loop;不能为 NULL。|
| runLoopMode |监视器是未安排的模式;不能为 NULL。|


####论述

Unschedules 指定监控利用指定的 run loop模式。调用这个函数来关闭监视器异步运行。

改变一个监视器,以便它不能指定,所以它的回调将永远不会被调用,调用 CFNetServiceMonitorInvalidate 。

####特殊注意事项

这个函数是线程安全。



####可用性

可用在 OS X 10.4 和以后版本


<br>**23.**[CFNetServiceRegister]() 弃用在 OS X 10.4 版本

使一个 CFNetService 在网络上可用。


####弃用声明

实用 [CFNetServiceRegisterWithOptions]()代替。


####演示
>OBJECTIVE-C
>
>Boolean CFNetServiceRegister ( CFNetServiceRef theService, CFStreamError *error );


####参数
| theService | CFNetService 注册;不能为 NULL。如果一个服务没有域这个注册将失败,一个类型,一个名称,和一个 IP 地址。|
|---:|:---|
| error |一个指向 CFStreamError 结构,如果错误发生将要设置错误代码和错误代码域。如果不想接收错误代码和域就设置为 NULL 。|

####返回值

如果启动异步模式的服务,你必须调用 [CFNetServiceSetClient]() 在 CFNetService 调用函数之前关联一个回调函数。


当注册在异步模式下运行的服务,这个函数返回 TURE,如果服务包含所有必需的属性和注册过程可以开始。如果注册过程成功完成,网络上的服务是可用的,直到你关闭服务通过调用 [CFNetServiceUnscheduleFromRunLoop](),[CFNetServiceSetClient]() 和 [CFNetServiceCancel]()。如果服务不包含所有必需的属性或如果注册过程没有成功完成,这个函数返回 FALSE。

当注册的服务在同步模式下运行,这个函数的 blocks ,直到出现错误,在这种情况下,该函数返回 FALSE。直到这个函数返回 FALSE,在网络上可用的服务。迫使这个函数返回 FALSE,从而关闭该服务,从另一个线程调用 [CFNetServiceCancel]() 。


####特殊注意事项

这个函数是线程安全。



####可用性

可用在 OS X 10.2 和以后版本

弃用在 OS X 10.4 版本



<br>**24.**[CFNetServiceRegisterWithOptions]()


使一个 CFNetService 在网络上可用。


####演示
>SWIFT
>
>func CFNetServiceRegisterWithOptions(_ theService: CFNetService, _ options: CFOptionFlags, _ error: UnsafeMutablePointer<CFStreamError>) -> Bool


>OBJECTIVE-C
>
>Boolean CFNetServiceRegisterWithOptions ( CFNetServiceRef theService, CFOptionFlags options, CFStreamError *error );


####参数
| theService |注册网络服务;不能为 NULL。如果这个没有域那么将要注册失败,一个类型,一个名称,和一个 IP 地址。|
|---:|:----|
| options |控制标志指定注册选项。目前唯一注册选项`kCFNetServiceFlagNoAutoRename `。细节请看 [CFNetService Registration Options]()|
| error |指针指向一个 CFStreamError 结构,如果出现错误将被设置为一个错误代码和错误代码域;如果你不想收到错误代码和它的域。就设置为 NULL。|


####返回值

如果开启异步注册服务就返回 TURE;如果异步活着同步注册失败或者如果一个同步注册取消了。就返回 FALSE。

####论述

如果服务是运行在异步模式,你必须调用 [CFNetServiceSetClient]() 在CFNetService 这之前调用函数关联一个回调函数。

当在异步模式下运行注册服务,这个函数返回 TURE 如果服务包含所有必需的属性和注册过程可以开始。如果注册过程成功完成,网络上的服务是可用的,直到你关闭服务通过调用 [CFNetServiceUnscheduleFromRunLoop](),[CFNetServiceSetClient](),和 [CFNetServiceCancel]()。如果服务不包含所有必需的属性或如果注册过程没有成功完成,这个函数返回 FALSE。


注册服务在同步模式下运行时,这个函数 blocks 直到出现错误,在这种情况下,该函数返回 FALSE。直到这个函数返回 FALSE ,服务在网络上可用。迫使这个函数返回 FALSE ,从而关闭服务,在另外的线程上调用 [CFNetServiceCance]()。


指定服务的选项参数设置注册选项。目前,`kCFNetServiceFlagNoAutoRename`是唯一支持注册选项。如果这个位设置和服务的运行时,注册将会失败。如果这一点没有设置同名和服务正在运行,被注册的服务将自动重命名 (n) 附加服务名称,其中 n 是一个数字递增直到服务可以注册一个惟一名称。



####特殊注意事项

这个函数是线程安全。



####可用性

可用在 OS X 10.4 和以后版本


<br>**25.** [CFNetServiceResolve]() 弃用在 OS X 10.4版本

这个函数指定更新 CFNetService 与服务相关联的IP地址或地址。调用 [CFNetServiceGetAddressing]() 获取地址。

####弃用声明

使用 [CFNetServiceResolveWithTimeout]()代替。


####演示
>OBJECTIVE-C
>
>Boolean CFNetServiceResolve ( CFNetServiceRef theService, CFStreamError *error );


####参数
| theService |CFNetService来解析;不能为 NULL。如果没有域将解析失败,一个类型,和一个名称。|
|---:|:---|
| error |一个指向 CFStreamError 的结构。如果出现错误将被设置为一个错误代码和错误代码的领域;如果你不想收到错误代码和它的域。就设置 NULL 。|

####返回值

如果异步服务解析启动或者同步服务更新 CFNetServic 的解析,就返回 TURE。如果异步或同步解析失败或者同步解析被取消了。就返回 FALSE。


####论述

当解决一个服务,在异步模式下运行,这个函数返回 TRUE 如果CFNetService 域,类型,和名称,和底层解析过程就开始了。否则函数返回 FALSE。一旦启动,直到调用 [ CFNetServiceCancel]() 取消解析。

解决服务在同步模式下运行时,这个函数阻塞直到CFNetService更新至少一个IP地址,直到错误出现,或者直到调用 [ CFNetServiceCancel ]()。

####特殊注意事项

这个函数是线程安全。

如果服务使用异步模式,你必须在这个函数之前调用 [CFNetServiceSetClient]()。


####可用性

可用在 OS X 10.2 和以后版本

弃用在 OS X 10.4版本



<br>**26.**[CFNetServiceResolveWithTimeout]()

获取一个 CFNetService 地址或 IP 地址。


####演示
>SWIFT
>
>func CFNetServiceResolveWithTimeout(_ theService: CFNetService, _ timeout: CFTimeInterval, _ error: UnsafeMutablePointer<CFStreamError>) -> Bool


>OBJECTIVE-C
>
>Boolean CFNetServiceResolveWithTimeout ( CFNetServiceRef theService, CFTimeInterval timeout, CFStreamError *error );



####参数
| theService |CFNetService来解析;不能为 NULL。如果没有域将解析失败,一个类型,和一个名称。|
|---:|:---|
| timeout | CFTimeInterval 类型的值指定最大的时间允许执行解析。如果该解析没有在指定的时间执行,将返回一个超时错误。如果超时小于或等于零,无限的时间是被允许的。|
| error |指针指向一个 [CFStreamError]() 结构,如果出现错误将被设置为一个错误代码和错误代码的域。如果你不想收到错误代码和它的域。就返回 NULL 。|


####返回值

如果启动异步服务解析或者 如果 CFNetService 更新异步服务解析。就返回 TURE。如果一个异步或同步解析超时失败,或者如果取消一个异步解析。就返回 FALSE。

####论述

这个函数更新指定的 CFNetService 和IP 地址或者地址关联到服务。
调用[ CFNetServiceGetAddressing]() 获取地址。

当解析一个服务,在异步模式下运行,这个函数返回 TURE。如果CFNetService域,类型,和名称,和底层解析过程就开始了。否则这个函数返回 FALSE。一旦启动,该决议继续,直到取消了通过调用 [ CFNetServiceCancel]()。

当解决一个服务,在同步模式下运行,这个函数阻塞直到 CFNetService 更新至少一个IP地址,直到出现错误,或者直到调用 [CFNetServiceCancel]()。


####特殊注意事项

这个函数是线程安全。

如果服务使用异步模式,你必须在这个函数之前调用 [CFNetServiceSetClient ]()。


####可用性

可用在 OS X 10.4 和以后版本


<br>**27.**[CFNetServiceScheduleWithRunLoop]()

安排一个 CFNetService 的 run loop。


####演示
>SWIFT
>
>func CFNetServiceScheduleWithRunLoop(_ theService: CFNetService, _ runLoop: CFRunLoop, _ runLoopMode: CFString)


>OBJECTIVE-C
>
>void CFNetServiceScheduleWithRunLoop ( CFNetServiceRef theService, CFRunLoopRef runLoop, CFStringRef runLoopMode );

####参数
| theService |安排 CFNetService 的 run loop。不能为 NULL。|
|---:|:---|
| runLoop |安排 run loop 服务。不能为 NULL。|
| runLoopMode |安排服务模式。不能为 NULL。|

####论述

安排指定服务 run loop , 哪些地方的服务在异步模式。调用者负责确保至少有一个 run loop 服务的计划正在运行。


####特殊注意事项

这个函数是线程安全。

你必须在这个函数之前调用 [CFNetServiceSetClient ]()。调用
[CFNetServiceSetClient]() 准备 CFNetService 用于异步模式。


####可用性

可用在 OS X 10.2 和以后版本


<br> **28.** [CFNetServiceSetClient]()


将一个回调函数与CFNetService联系起来或从CFNetService分离一个回调函数。



####演示
>SWIFT
>
>func CFNetServiceSetClient(_ theService: CFNetService, _ clientCB: CFNetServiceClientCallBack?, _ clientContext: UnsafeMutablePointer < CFNetServiceClientContext > ) -> Bool


>OBJECTIVE-C
>
>Boolean CFNetServiceSetClient ( CFNetServiceRef theService, CFNetServiceClientCallBack clientCB, CFNetServiceClientContext *clientContext );

####参数
| theService | CFNetService。不能为 NULL。|
|---:|:---|
| clientCB |回调函数与 CFNetService 关联。如果你关闭服务,设置 CFNetService 的 `clientCB `为 NULL 。使之前相关的回调函数分离。|
| clientContext |用于调用`clientCB ` 的上下文信息。不能为 NULL。|


####返回值

如果客户端设置就返回 NULL。否则返回 FALSE。

####论述

这个函数 指定 `clientCB ` 将要调用 IP 地址报告。(在的情况下 `CFNetServiceResolve `)或者或报告登记错误(在这种情况下 `CFNetServiceRegister `)。


####特殊注意事项

这个函数是线程安全。

CFNetService的异步操作,调用这个函数和调用 [CFNetServiceScheduleWithRunLoop]() 在 run loop 调度服务。然后调用 [CFNetServiceRegister]() 或者 [ CFNetServiceResolve]()。


####可用性

可用在 OS X 10.2 和以后版本


<br>**29.**[CFNetServiceSetTXTData]()


设置 CFNetService 的 TXT 记录。


####演示
>SWIFT
>
>func CFNetServiceSetTXTData(_ theService: CFNetService, _ txtRecord: CFData) -> Bool


>OBJECTIVE-C
>
>Boolean CFNetServiceSetTXTData ( CFNetServiceRef theService, CFDataRef txtRecord );


####参数
| theService | 设置 CFNetServiceRef TXT 的记录;不能为 NULL。|
|---:|:---|
| txtRecord |设置 TXT 记录的内容。内容必须不能超过 1450字节。|

####返回值

如果设置 TXT 记录就返回 TURE。否则返回 FALSE。


####论述

这个函数设置一个指定的 TXT 记录服务。如果这个服务当前在网络中注册,这个记录是广播。设置一个 TXT 记录服务 仍然存在解析是不允许的。




####特殊注意事项

这个函数是线程安全。


####可用性

可用在 OS X 10.4 和以后版本


<br>**30.**[CFNetServiceUnscheduleFromRunLoop]()


未安排 CFNetService run loop。


####演示
>SWIFT
>
>func CFNetServiceUnscheduleFromRunLoop(_ theService: CFNetService, _ runLoop: CFRunLoop, _ runLoopMode: CFString)


>OBJECTIVE-C
>
>void CFNetServiceUnscheduleFromRunLoop ( CFNetServiceRef theService, CFRunLoopRef runLoop, CFStringRef runLoopMode );


####参数
| theService | 未安排的 CFNetService ;不能为 NULL。|
|---:|:---|
| runLoop |run loop;不能为 NULL。|
| runLoopMode |未安排的服务模式;不能为 NULL。|


####论述

未安排指定 run loop 和 模式来指定服务。运行异步调用函数关闭服务。完全关闭,调用 [CFNetServiceSetClient]() 和设置 `clientCB `为 NULL。然后调用 [CFNetServiceCancel]()。


####特殊注意事项

这个函数是线程安全。


####可用性

可用在 OS X 10.2 和以后版本



##修改网络服务


[CFNetServiceSetProtocolSpecificInformation]() 弃用在 OS X 10.4版本

设置 CFNetService 拟定具体的信息。

####弃用声明

用`CFNetServiceSetTXTData `代替。


####演示

>OBJECTIVE-C
>
>void CFNetServiceSetProtocolSpecificInformation ( CFNetServiceRef theService, CFStringRef theInfo );


####参数
| theService | 设置 CFNetService 拟定具体的信息;不能为 NULL。|
|---:|:---|
| theInfo |设置 拟定具体的信息。通过 NULL 去删除拟定具体信息的服务。|


####论述

拟定具体信息呈现在 DNS TXT 记录服务。每个 TXT 记录由 zero 或者更多的字符串构成,这些字节排成一条直线垫补缝隙。每个组成部分的格式字符串是一个长度字节,紧随其后的是 0 到 255 字节的文本数据。




####特殊注意事项

这个函数是线程安全。


####可用性

可用在 OS X 10.2 和以后版本

弃用在 OS X 10.4 版本




##获得网络服务类型 ID


**1.** [CFNetServiceGetTypeID]()

获取网络服务对象的 Core Foundation 类型标示符。


####演示
>SWIFT
>
>func CFNetServiceGetTypeID() -> CFTypeID


>OBJECTIVE-C
>
>CFTypeID CFNetServiceGetTypeID ( void );


####返回值

类型的 ID。


####特殊注意事项

这个函数是线程安全。


####可用性

可用在 OS X 10.2 和以后版本



<br>**2.**[CFNetServiceMonitorGetTypeID]()

获取 CFNetServiceMonitor 实例的 Core Foundation 类型标示符。

####演示
>SWIFT
>
>func CFNetServiceMonitorGetTypeID() -> CFTypeID


>OBJECTIVE-C
>
>CFTypeID CFNetServiceMonitorGetTypeID ( void );


####返回值

类型的 ID。


####特殊注意事项

这个函数是线程安全。


####可用性

可用在 OS X 10.2 和以后版本

<br>**3.**[CFNetServiceBrowserGetTypeID]()

获取网络服务浏览对象的 Core Foundation 类型标示符。

####演示
>SWIFT
>
>func CFNetServiceBrowserGetTypeID() -> CFTypeID


>OBJECTIVE-C
>
>CFTypeID CFNetServiceBrowserGetTypeID ( void );

####返回值

类型的 ID。


####特殊注意事项

这个函数是线程安全。


####可用性

可用在 OS X 10.2 和以后版本



#`回调`


###群

**1.**[CFNetServiceBrowserClientCallBack]()

定一个指向 CFNetServiceBrowser 的回调函数。

####演示
>SWIFT
>
>typealias CFNetServiceBrowserClientCallBack = (CFNetServiceBrowser, CFOptionFlags, AnyObject, UnsafeMutablePointer < CFStreamError >, UnsafeMutablePointer < Void >) -> Void


>OBJECTIVE-C
>
>typedef void (*CFNetServiceBrowserClientCallBack) ( CFNetServiceBrowserRef browser, CFOptionFlags flags, CFTypeRef domainOrService, CFStreamError * error, void* info);


####参数
| browser | CFNetServiceBrowser 和回调函数关联。|
|---:|:---|
| flags |添加传输标识信息。设置｀kCFNetServiceFlagIsDomain｀ bit 如果｀domainOrService｀包含一个域;如果没有设置 bit,`domainOrService `包含一个 CFNetService  实例。添加 bit 值,看[CFNetServiceBrowserClientCallBack Bit Flags]()|
| domainOrService |一个字符串包含一个域名如果这个回调函数调用[CFNetServiceBrowserSearchForDomains](),或者一个 CFNetService  实例如果回调函数是存在一个回调结果。|
| error |一个指向 [CFStreamError]() 结构字段可能包含错误代码的错误。|
| info |用户定义的上下文信息.信息的值是同样的信息字段 [CFNetServiceClientContext]() 结构提供 [CFNetServiceBrowserCreate]() 调用创建 CFNetServiceBrowser 与这个回调函数相关联。|


####论述

CFNetServiceBrowser 的回调函数调用一个或多个时间域或服务调用的结果发现[CFNetServiceBrowserSearchForDomains]() 和[CFNetServiceBrowserSearchForServices]()。



####可用性

可用在 OS X 10.2 和以后版本


**2.**[CFNetServiceClientCallBack]()

定义了一个指针指向CFNetService回调函数。

####演示
>SWIFT
>
>typealias CFNetServiceClientCallBack = (CFNetService, UnsafeMutablePointer<CFStreamError>, UnsafeMutablePointer<Void>) -> Void


>OBJECTIVE-C
>
>typedef void (*CFNetServiceClientCallBack) ( CFNetServiceRef theService, CFStreamError * error, void* info);

####参数
| theService | CFNetService 和回调函数关联。|
|---:|:---|
| error |指针指向一个 [CFStreamError]() 结构字段包含可能包含一个错误代码的错误。|
| info |用户定义的上下文信息。提供信息的值是一样的信息字段值 [CFNetServiceClientContext]() 结构当 [CFNetServiceSetClient]() 与 CFNetService 与这个回调函数相关联。|

####论述

你的回调函数将被称为 CFNetService 报告或注册错误报告结果解析。在实例解析,如果服务有多个 IP 地址,你将回调之前调用的每一个地址。


####可用性

可用在 OS X 10.2 和以后版本


<br>**3.**[CFNetServiceMonitorClientCallBack]()

当一个监控记录类型的变化时候,定义了一个指针指向要调用的回调函数。


####演示
>SWIFT
>
>typealias CFNetServiceMonitorClientCallBack = (CFNetServiceMonitor, CFNetService, CFNetServiceMonitorType, CFData, UnsafeMutablePointer < CFStreamError >, UnsafeMutablePointer < Void > ) -> Void


>OBJECTIVE-C
>
>typedef void (*CFNetServiceMonitorClientCallBack) ( CFNetServiceMonitorRef theMonitor, CFNetServiceRef theService, CFNetServiceMonitorType typeInfo, CFDataRef rdata, CFStreamError * error, void* info);


####参数
| theMonitor | CFNetService 和回调函数关联。|
|---:|:---|
| theService | CFNetService 所调用的回调。|
| typeInfo |记录改变的类型。合适的值,看[ CFNetServiceMonitorType Constants]()。|
| rdata |记录改变的内容。|
| error |指针 [CFStreamError]() 结构的错误字段包含一个错误代码,如果发生错误。|
| info |任意指向用户定义数据的信息字段中指定是由`CFNetServiceClientContext` [CFNetServiceMonitorCreate]() 结构。|


####论述

这个回调函数将要调用监控改变记录类型或者当监控停止时调用[ CFNetServiceMonitorStop]()。


####可用性

可用在 OS X 10.2 和以后版本




##数据类型

**1.**[CFNetServiceBrowserRef]()

一个不透明的代表 CFNetServiceBrowser 参考。



####演示
>SWIFT
>
>typealias CFNetServiceBrowserRef = CFNetServiceBrowser


>OBJECTIVE-C
>
>typedef struct __CFNetServiceBrowser* CFNetServiceBrowserRef;


####可用性

可用在 OS X 10.2 和以后版本

<br>**2.**[CFNetServiceClientContext]()

当CFNetService结构与一个回调函数或创建CFNetServiceBrowser时提供。


####演示
>SWIFT
>
>struct CFNetServiceClientContext { var version: CFIndex var info: UnsafeMutablePointer < Void >  var retain: CFAllocatorRetainCallBack? var release: CFAllocatorReleaseCallBack? var copyDescription: CFAllocatorCopyDescriptionCallBack? init() init(version version: CFIndex, info info: UnsafeMutablePointer < Void >, retain retain: CFAllocatorRetainCallBack?, release release: CFAllocatorReleaseCallBack?, copyDescription copyDescription: CFAllocatorCopyDescriptionCallBack?) }


>OBJECTIVE-C
>
>struct CFNetServiceClientContext { CFIndex version; void *info; CFAllocatorRetainCallBack retain; CFAllocatorReleaseCallBack release; CFAllocatorCopyDescriptionCallBack copyDescription; }; typedef struct CFNetServiceClientContext CFNetServiceClientContext;


####字段
***version***

这种结构的版本号。目前唯一有效的值为零。

***info***

任意用户分配内存指针包含用户定义的数据与服务相关联,浏览,或监视和传递给各自的回调函数。只要数据必须在 CFNetService ,CFNetServiceBrowser 或 CFNetServiceMonito 是有效的。如果你不想接收回调函数定义的数据,设置这个字段为 `NULL` 。

***retain***

回调用于为服务添加一个保留或浏览使用的生活服务信息或浏览。这个回调可以用于临时服务或浏览需要的引用。这个回调函数返回指针的实际信息,因此它可以存储在服务或浏览。这个字段可以为 `NULL` 。


***release***

回调,可去除保留以前添加的服务或浏览信息指针。这个字段可以为空,但设置这个字段为 `NULL` 可能会导致内存泄漏。

***copyDescription***

回调函数用来创建一个描述性的字符串表示的数据指出,信息。在执行这个函数,返回一个引用 CFString 对象描述你的分配器和一些你的用户定义的数据特征,由CFCopyDescription() 使用 。你可以设置这个字段为 `NULL` ,在这种情况下,将提供一个基本的描述的 Core Foundation。

####可用性

可用在 OS X 10.2 和以后版本


<br>**3.**[CFNetServiceMonitorRef]()

一个不透明的参考服务监控。


####演示
>SWIFT
>
>typealias CFNetServiceMonitorRef = CFNetServiceMonitor


>OBJECTIVE-C
>
>typedef struct __CFNetServiceMonitor* CFNetServiceMonitorRef;


####论述

服务监测引用用于监控记录 CFNetServiceRef 变化。


####可用性

可用在 OS X 10.4 和以后版本


<br>***4.***[CFNetServiceRef]()

一个不透明的代表CFNetService参考。


####演示
>SWIFT
>
>typealias CFNetServiceRef = CFNetService


>OBJECTIVE-C
>
>typedef struct __CFNetService* CFNetServiceRef;


####可用性

可用在 OS X 10.4 和以后版本



##常量

**1.**[CFNetService Registration Options]()

bit 标志注册时使用的一个服务。


####演示
>SWIFT
>
>struct CFNetServiceRegisterFlags : OptionSetType {
    init(rawValue rawValue: CFOptionFlags)
    static var NoAutoRename: CFNetServiceRegisterFlags { get }
}


>OBJECTIVE-C
>
>enum {
   kCFNetServiceFlagNoAutoRename = 1
};

####常量

* `kCFNetServiceFlagNoAutoRename`

如果发生名字冲突导致注册失败。


可用在 OS X 10.4 和以后版本

####可用性

可用在 OS X 10.2 和以后版本


<br>**2.**[CFNetServiceBrowserClientCallBack Bit Flags]()


bit 标志提供额外的信息当客户端 `CFNetServiceBrowserClientCallBack` 函数返回的结果。


####演示
>SWIFT
>
>struct CFNetServiceBrowserFlags : OptionSetType {  
      <br>init(rawValue rawValue: CFOptionFlags)
    <br>static var MoreComing: CFNetServiceBrowserFlags { get }
      <br>static var IsDomain: CFNetServiceBrowserFlags { get }
      <br>static var IsDefault: CFNetServiceBrowserFlags { get }
      <br>static var IsRegistrationDomain: CFNetServiceBrowserFlags { get }
      <br>static var Remove: CFNetServiceBrowserFlags { get }
      
>}


>OBJECTIVE-C
>
>enum {
  <br>kCFNetServiceFlagMoreComing = 1,
  <br>kCFNetServiceFlagIsDomain = 2,
  <br>kCFNetServiceFlagIsDefault = 4,
  <br>kCFNetServiceFlagIsRegistrationDomain = 4, /* For compatibility  */
  <br>kCFNetServiceFlagRemove = 8
  
>};


####常量

* `kCFNetServiceFlagMoreComing `

 如果设置,表示客户端的回调函数很快就会再次调用,因此,客户端不应该做任何耗时,如更新屏幕。


可用在 OS X 10.2 和以后版本

* `kCFNetServiceFlagIsDomain `

 如果设置,属于搜索域的结果。如果没有设置,结果属于搜索服务。

 可用在 OS X 10.2 和以后版本
 
* `kCFNetServiceFlagIsDefault `

  如果设置,生成的域是默认注册或浏览域,这取决于上下文。对于这个CFNetServices API的版本,默认注册域名是当地的域。在早期版本的这个API,这个常数是`kCFNetServiceFlagIsRegistrationDomain`,保留的向后兼容性。
  
   可用在 OS X 10.4 和以后版本
   
   
* kCFNetServiceFlagRemove

 如果设置,客户端应该删除项的结果而不是增加。

 可用在 OS X 10.2 和以后版本
 
 
 
####论述

看 [CFNetServiceBrowserClientCallBack]() 附加信息。


####可用性

可用在 OS X 10.2 和以后版本


<br>**3.**[CFNetServiceMonitorType]()

记录类型说明符用于告诉服务监控记录变化的类型。




####演示
>SWIFT
>
>enum CFNetServiceMonitorType : Int32 {
   <br> case TXT
   
>}


>OBJECTIVE-C
>
>enum {
<br>kCFNetServiceMonitorTXT = 1

>}typedef enum CFNetServiceMonitorType CFNetServiceMonitorType;


####常量


* `kCFNetServiceMonitorTXT `

 观察三种变化记录。

 可用在 OS X 10.4 和以后版本
 
 
####重要声明
>OBJECTIVE-C
>
>@import CFNetwork

>SWIFT
>
>import CFNetwork


####可用性

可用在 OS X 10.2 和以后版本

<br>**4.**[CFNetServicesError]()

返回的错误代码可能 CFNetServices 函数或传递给 CFNetServices 回调函数。



####演示
>SWIFT
>
>enum CFNetServicesError : Int32 {
>   <br>case Unknown
    <br>case Collision
    <br>case NotFound
    <br>case InProgress
    <br>case BadArgument
    <br>case Cancel
    <br>case Invalid
    <br>case Timeout
    
>}


>OBJECTIVE-C
>
>typedef enum {
   <br>kCFNetServicesErrorUnknown = -72000,
  <br> kCFNetServicesErrorCollision = -72001,
   <br>kCFNetServicesErrorNotFound = -72002,
   <br>kCFNetServicesErrorInProgress = -72003,
   <br>kCFNetServicesErrorBadArgument = -72004,
  <br> kCFNetServicesErrorCancel = -72005,
  <br> kCFNetServicesErrorInvalid = -72006,
  <br> kCFNetServicesErrorTimeout = -72007
><br>} CFNetServicesError;


####常量

* `kCFNetServicesErrorUnknown `

 一个未知 CFNetService 错误发生。

 可用在 OS X 10.2 和以后版本
 
* `kCFNetServicesErrorCollision `

 尝试使用这个名字时已经在使用了。

 可用在 OS X 10.2 和以后版本
 
 
* `kCFNetServicesErrorNotFound `

 不使用。
 
  可用在 OS X 10.2 和以后版本
  
  
* `kCFNetServicesErrorInProgress `

   搜索已经在进行。
 
   可用在 OS X 10.2 和以后版本
   
   
* `kCFNetServicesErrorBadArgument `

   没有提供所必需的。  
   
   可用在 OS X 10.2 和以后版本
   
  
* `kCFNetServicesErrorCancel `

    搜索或服务被取消了。 
    
    可用在 OS X 10.2 和以后版本
    
    
* `kCFNetServicesErrorInvalid `

    无效的数据传递给一个 CFNetServices 函数。

    可用在 OS X 10.2 和以后版本
    
    
* `kCFNetServicesErrorTimeout `

   解析失败,因为超时了。

   可用在 OS X 10.2 和以后版本
   
   
####重要声明

>OBJECTIVE-C
>
>@import CFNetwork

>SWIFT
>
>import CFNetwork


####可用性

可用在 OS X 10.2 和以后版本



<br>**5.**[Error Domains]()

错误域。

####演示
>SWIFT
>
>let kCFStreamErrorDomainMach: Int32
<br>let kCFStreamErrorDomainNetServices: Int32




>OBJECTIVE-C
>
>extern const SInt32 kCFStreamErrorDomainMach;
<br>extern const SInt32 kCFStreamErrorDomainNetServices;


####常量

* `kCFStreamErrorDomainMach `

 Mach 所报告的错误域返回错误。有关更多信息,请参考`/usr/include/mach/error.h`的头文件

 可用在 OS X 10.5 和以后版本
 
* `kCFStreamErrorDomainNetServices `

 误差域返回错误报告的服务发现 api 。这些错误只是返回如果你使用`CFNetServiceBrowser` API 或任何 API 介绍在 OS X v10.4 或更高版本。



 可用在 OS X 10.5 和以后版本