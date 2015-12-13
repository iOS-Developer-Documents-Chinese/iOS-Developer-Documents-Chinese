#CFHost 参考


[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFHostRef/index.html#//apple_ref/doc/uid/TP40003333)

翻译人:王谦 翻译日期:2015.9.16

>重要的

>这是一个API 的初步技术开发文档。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实施的软件，应与最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供未来的 API 或技术。

| 继承自        |   符合     |    导入语句     |
|   --------   |   --------:  | :----    |
| 不适用      | 不适用   |   @import CFNetwork       |
|          |       |    可用性 |
|          |      |  不适用 |



CFHost API 允许您创建的实例 CFHost 对象,您可以使用它们来获得主机信息,包括名字,地址,和可达性信息。

获取主机信息的过程称为解析。首先调用 CFHost 的 `CFHostCreateWithAddress`或`CFHostCreateWithName`分别创建一个实例使用一个地址或一个名字。如果你想解决异步主机。调用 CFHostSetClient 把你客户端的上下文和用户定义的回调函数联系起来。然后调用 CFHostScheduleWithRunLoop 安排主机 run loop。

开始解决,调用 CFHostStartInfoResolution。如果设置为异步解析, CFHostStartInfoResolution 会立刻返回。你的回调函数将会称为决议完成时。如果你没有设置为异步解析, CFHostStartInfoResolution block 决议完成之前,出现错误,或者解析取消了。

当解析完成,调用 CFHostGetAddressing  或 CFHostGetNames 分别得到一个数组的地址或名称,主机。 调用 CFHostGetReachability 获取标示,在 SystemConfiguration / SCNetwork.h声明,描述主机的可达性。 



当你不再需要一个 CFHost 对象,调用 CFHostUnscheduleFromRunLoop 和 CFHostSetClient 当主机从你的用户定义的客户端上下文和回调函数(如果设置为异步解析)。然后处理它。

##函数

####创建主机

**1.** [CFHostCreateCopy]()

通过复制创建一个新的主机对象。

####演示
>SWIFT
>
>func CFHostCreateCopy(_ alloc: CFAllocator?, _ host: CFHost) -> Unmanaged < CFHost >


>OBJECTIVE-C
>
>CFHostRef CFHostCreateCopy ( CFAllocatorRef alloc, CFHostRef host );

####参数

| alloc |分配器使用为新对象分配内存。通过 NULL 或 kCFAllocatorDefault 使用当前默认的分配器。|
|---:|:---|
| addr |主机复制。这个值不能为 NULL 。|

####返回值

一个有效 CFHost 对象如果不能创建副本返回 NULL。新的主机包含一份从原始主机之前解决所有数据。请参考 [Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。


####特殊注意事项

这个函数是线程安全的。

####可用性

可用在 OS X10.3 和以后版本

<br>**2.** [CFHostCreateWithAddress]()

使用一个地址创建许多对象的一个实例。

####演示
>SWIFT
>
>func CFHostCreateWithAddress(_ allocator: CFAllocator?, _ addr: CFData) -> Unmanaged < CFHost >

>OBJECTIVE-C
>
> CFHostRef CFHostCreateWithAddress ( CFAllocatorRef allocator, CFDataRef addr );

####参数
| alloc |分配器使用为新对象分配内存。通过 NULL 或 kCFAllocatorDefault 使用当前默认的分配器。|
|---:|:---|
| addr |CFDataRef 对象包含 sockaddr 结构主机的地址。这个值不能为 NULL 。|

####返回值

一个有效的 CFHostRef 对象所能解决的，,如果不能创建主机 返回 NULL。请参考 [Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

####论述

调用 [CFHostStartInfoResolution]() 解决返回对象的名称和可达性信息。

####特殊注意事项

这个函数是线程安全的。

####可用性

可用在 OS X10.3 和以后版本


<br>**3.**[CFHostCreateWithName]()

使用一个名称来创建主机对象的一个实例。

####演示
>SWIFT
>
>func CFHostCreateWithName(_ allocator: CFAllocator?, _ hostname: CFString) -> Unmanaged < CFHost >

>OBJECTIVE-C
>
> CFHostRef CFHostCreateWithName ( CFAllocatorRef allocator, CFStringRef hostname );

####参数
| alloc |使用分配器为新对象分配内存。通过`NULL`或`kCFAllocatorDefault`使用当前默认的分配器。|
|---:|:---|
|hostname	|一个字符串代表主机的名称。这个值必须不能为 NULL。|


####返回值

一个有效 CFHostRef 对象,可以解决,如果不能创建主机就返回 NULL 。请参考 [Create Rule]。


[Create Rule]:https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029


####论述

调用 [CFHostStartInfoResolution]()解决对象的地址和可达性信息。


####特殊注意事项

这个函数是线程安全的。

####可用性

可用在 OS X10.3 和以后版本


##FHost 函数

**1.**[CFHostCancelInfoResolution]()

取消一个主机的解析。


####演示
>SWIFT
>
>func CFHostCancelInfoResolution(_ theHost: CFHost, _ info: CFHostInfoType)

>OBJECTIVE-C
>
> void CFHostCancelInfoResolution ( CFHostRef theHost, CFHostInfoType info );

####参数
| theHost |主机的解析被取消。这个值必须不能为 NULL。|
|---:|:---|
| info	|值类型 CFHostInfoType 指定类型的解析也被取消。看 [CFHostInfoType Constants]() 合适的值。|


####论述

这个函数取消指定的异步或同步解决theHost指定的主机的信息。

####特殊注意事项

这个函数是线程安全的。

####可用性

可用在 OS X10.3 和以后版本


<br>**2.**[CFHostGetAddressing]()

得到主机地址。


####演示
>SWIFT
>
>func CFHostGetAddressing(_ theHost: CFHost, _ hasBeenResolved: UnsafeMutablePointer < DarwinBoolean >) -> Unmanaged < CFArray > ? 

>OBJECTIVE-C
>
> CFArrayRef CFHostGetAddressing ( CFHostRef theHost, Boolean *hasBeenResolved );


####参数
| theHost |得到主机地址。这个值必须不能为 NULL。|
|---:|:---|
| hasBeenResolved	|在返回时,一个指向一个布尔就是 TURE 。如果地址是可用的和不可用的就 FALSE。此参数可以为空。|
|function result|一个 CFArray 地址，地址是一个sockaddr 结构体的 CFDataRef 包裹，如果没有有效地址就空。|

####论述

这个函数从 CFHost 获得地址。之前 CFHost 必须被解析。解析一个 CFHost,调用 [ CFHostStartInfoResolution]()。


####特殊注意事项

这个函数的地址是一个线程安全的方法,但数据结果不是线程安全的。返回的数据作为一个“get”而不是一个 copy,数据是不安全的,如果 CFHost 是从另一个线程改变。


####可用性

可用在 OS X10.3 和以后版本


<br>**3.**[CFHostGetNames]()

从CFHost 得到了名称。


####演示
>SWIFT
>
>func CFHostGetNames(_ theHost: CFHost, _ hasBeenResolved: UnsafeMutablePointer < DarwinBoolean >) -> Unmanaged < CFArray > ?

>OBJECTIVE-C
>
> CFArrayRef CFHostGetNames ( CFHostRef theHost, Boolean *hasBeenResolved );

####参数
| theHost |主机检查。主机必须之前解析。(解析主机调用[CFHostStartInfoResolution]())。 这个值不能为 NULL。|
|---:|:---|
| hasBeenResolved	|在返回时,如果包含一个有效的名字就是 TURE,否则就是 FALSE。这个值可能是`NULL`。|


####返回值

数组包含 theHost 的名称,如果没有名称就返回 `NULL`。


####特殊注意事项

这个函数的地址是一个线程安全的方法,但数据结果不是线程安全的。返回的数据作为一个“get”而不是一个 copy,数据是不安全的,如果 CFHost 是从另一个线程改变。


####可用性

可用在 OS X10.3 和以后版本


<br>**4.**[CFHostGetReachability]()


获得来自主机的可达性信息。

####演示
>SWIFT
>
>func CFHostGetReachability(_ theHost: CFHost, _ hasBeenResolved: UnsafeMutablePointer < DarwinBoolean > ) -> Unmanaged < CFData > ?

>OBJECTIVE-C
>
> CFDataRef CFHostGetReachability ( CFHostRef theHost, Boolean *hasBeenResolved );


####参数
| theHost |主机的可达性。主机必须在之前解析。(解析一个主机,调用[CFHostStartInfoResolution]()。) 这个值必须不为 NULL。|
|---:|:---|
| hasBeenResolved |在返回,如果包含有效的可达性就是 TURE。否则就是 FALSE。这个值可以为 NULL。 |

####返回值

CFData 对象封装了可达性标志(`SCNetworkConnectionFlags `)定义在`SystemConfiguration/SCNetwork.h`如果没有有效的可达性信息就返回 NULL。


####特殊注意事项

这个函数的地址是一个线程安全的方法,但数据结果不是线程安全的。返回的数据作为一个“get”而不是一个 copy,数据是不安全的,如果 CFHost 是从另一个线程改变。


####可用性

可用在 OS X10.3 和以后版本


<br>**5.**[CFHostStartInfoResolution]()

开始解析主机对象。

####演示
>SWIFT
>
>
>func CFHostStartInfoResolution(_ theHost: CFHost, _ info: CFHostInfoType, _ error: UnsafeMutablePointer < CFStreamError >) -> Bool

>OBJECTIVE-C
>
>
>Boolean CFHostStartInfoResolution ( CFHostRef theHost, CFHostInfoType info, CFStreamError *error );


####参数
| theHost |主机,获得之前调用 [CFHostCreateCopy](),[CFHostCreateWithAddress](),或[CFHostCreateWithName](),然后解析。这个值必须不能为 NULL。|
|---:|:---|
| info | 一个值的类型 CFHostInfoType 指定检索信息的类型。看 [CFHostInfoType Constants]() 合适的值。 |
| error |一个指向CFStreamError结构,然而,如果出现错误,是设置错误和错误域。在同步模式,显示解析失败错误的原因,和在同步模式下,显示为什么启动解析错误失败。|


####返回值

如果启动解析(异步模式)就返回 TURE。如果另一个解析正在进行 theHost 如果错误发生,就返回 FALSE。

####论述

这个函数指定解析信息通过在主机的 info 和存储。 

在同步模式,这个函数 blocks 直到完成解析,在这种情况下,该函数返回 TRUE ,直到停止解析通过调用 [ CFHostCancelInfoResolution]() 从另一个线程,在这种情况下,这个函数返回 FALSE ,或者直到出现错误。


####特殊注意事项

这个函数的地址是一个线程安全的。


####可用性

可用在 OS X10.3 和以后版本


<br>**6.**[CFHostSetClient]()

将客户端上下文和一个回调函数与 CFHost 对象或一个客户端之前设置的背景和回调函数分离。


####演示
>SWIFT
>
>
>func CFHostSetClient(_ theHost: CFHost, _ clientCB: CFHostClientCallBack?, _ clientContext: UnsafeMutablePointer < CFHostClientContext > ) -> Bool

>OBJECTIVE-C
>
>
>Boolean CFHostSetClient ( CFHostRef theHost, CFHostClientCallBack clientCB, CFHostClientContext *clientContext );


####参数
| theHost |修改主机。这个值必须不为 NULL。|
|---:|:---|
| clientCB |这个函数与 theHost 关联。这个回调函数在解析完成或取消后调用。如果你调用函数使主机上下文和回调 theHost 分离。通过`clientCB` NULL。 |
| clientContext | [CFHostClientContext]() 结构信息的字段将被传递给指定的回调函数`clientCB` `clientCB`时调用。这个值必须不为 NULL 设置文件关联。<br><br>通过 NULL  从主机脱离客户端上下文和一个回调。|


####返回值

如果文件关联可以设置或不设置就返回 TURE。否则就返回 FALSE。

####论述

`clientCB`指定的回调函数将被称为当解析完成或取消了。

####特殊注意事项

这个函数的地址是一个线程安全的。


####可用性

可用在 OS X10.3 和以后版本


<br>**7.**[CFHostScheduleWithRunLoop]()

安排一个 CFHost run loop。


####演示
>SWIFT
>
>
>func CFHostScheduleWithRunLoop(_ theHost: CFHost, _ runLoop: CFRunLoop, _ runLoopMode: CFString)

>OBJECTIVE-C
>
>
>void CFHostScheduleWithRunLoop ( CFHostRef theHost, CFRunLoopRef runLoop, CFStringRef runLoopMode );


####参数
| theHost |主机被安排在一个 run loop。这个值必须不为 NULL。|
|---:|:---|
| runLoop |安排theHost run loop。这个值必须不为 NULL。 |
| runLoopMode |安排 theHost 模式。这个值必须不为 NULL。|


####论述

安排一个 theHost run loop,引起异步主机执行的解析。调用者负责确保至少有一个 run loop 的主机正在运行计划。


####特殊注意事项

这个函数的地址是一个线程安全的。


####可用性

可用在 OS X10.3 和以后版本


<br>**8.**[CFHostUnscheduleFromRunLoop]()

未安排一个 CFHost run loop。


####演示
>SWIFT
>
>
>func CFHostUnscheduleFromRunLoop(_ theHost: CFHost, _ runLoop: CFRunLoop, _ runLoopMode: CFString)

>OBJECTIVE-C
>
>
>void CFHostUnscheduleFromRunLoop ( CFHostRef theHost, CFRunLoopRef runLoop, CFStringRef runLoopMode );


####参数
| theService |主机被取消。这个值必须不为 NULL。|
|---:|:---|
| runLoop | run loop。这个值必须不为 NULL。 |
| runLoopMode |未安排服务的模式。这个值必须不为 NULL。|



####特殊注意事项

这个函数的地址是一个线程安全的。


####可用性

可用在 OS X10.3 和以后版本



##获取 CFHost 类型 ID


[CFHostGetTypeID]()

得到的 Core Foundation 类型标识 CFHost 不透明类型。


####演示
>SWIFT
>
>
>func CFHostGetTypeID() -> CFTypeID

>OBJECTIVE-C
>
>
>CFTypeID CFHostGetTypeID ( void );

####返回值

 Core Foundation 类型为 CFHost 不透明类型标识符。

####特殊注意事项

这个函数的地址是一个线程安全的。


####可用性

可用在 OS X10.3 和以后版本


#`回调`


##群

[CFHostClientCallBack]()

定义了一个指针时调用的回调函数异步解决 CFHost 完成或发生错误异步 CFHost 决议。



####演示
>SWIFT
>
>
>typealias CFHostClientCallBack = (CFHost, CFHostInfoType, UnsafePointer < CFStreamError >, UnsafeMutablePointer < Void >) -> Void

>OBJECTIVE-C
>
>
>typedef void (CFHostClientCallBack) ( CFHostRef theHost, CFHostInfoType typeInfo, const CFStreamError *error, void *info);


####参数
| theHost |主机被取消。这个值必须不为 NULL。|
|---:|:---|
| typeInfo | run loop。这个值必须不为 NULL。 |
| error |未安排服务的模式。这个值必须不为 NULL。|
| info |用户定义的上下文信息。指向是的一样的值信息,值指向的信息字段[CFHostClientContext]() 结构时提供主机与这个回调函数相关联。|


####论述

CFHost 对象的回调函数调用一个或多次异步解析,完成指定的主机,当异步解析取消,或者当一个错误发生在异步解析。


####可用性

可用在 OS X10.3 和以后版本



##数据类型


[CFHostRef]()

一个不透明的代表一个 CFHost 对象引用。

####演示
>SWIFT
>
>
>typealias CFHostRef = CFHost

>OBJECTIVE-C
>
>
>typedef struct __CFHost* CFHostRef;



####可用性

可用在 OS X10.3 和以后版本


<br>[CFHostClientContext]()

一个包含用户定义的数据结构为 CFHost 对象和回调。

####演示
>SWIFT
>
>
>struct CFHostClientContext { var version: CFIndex var info: UnsafeMutablePointer < Void > var retain: CFAllocatorRetainCallBack? var release: CFAllocatorReleaseCallBack? var copyDescription: CFAllocatorCopyDescriptionCallBack }

>OBJECTIVE-C
>
>
>struct CFHostClientContext { CFIndex version; void *info; CFAllocatorRetainCallBack retain; CFAllocatorReleaseCallBack release; CFAllocatorCopyDescriptionCallBack copyDescription; } CFHostClientContext; typedef struct CFHostClientContext CFHostClientContext;


####字段

***version***

版本号的结构类型作为参数传递给主机客户端功能。唯一有效的版本号是0。

***info***

任意分配的内存指针包含用户定义的数据可以与主机传递给回调函数。

***retain***

回调用于添加一个保留的主机信息指向主机的生命周期,和可以用于临时引用主机需要。这个回调函数返回指针存储在主机实际的信息,于指针作为参数传递。

***release***

回调用于删除一个保留以前添加的主机信息的指针。

***copyDescription***

回调用于创建一个描述性信息的字符串表示指针(或指针指向的数据信息)用于调试目的。这个回调是由 [CFCopyDescription]() 调用函数。


####可用性

可用在 OS X10.3 和以后版本


##常量

[CFHostInfoType]()

指示值的数据类型是解析或数据类型的解析。


####演示
>SWIFT
>
>
>enum CFHostInfoType : Int32 {
>    <br>case Addresses
    <br>case Names
    <br>case Reachability
    
>}

>OBJECTIVE-C
>
enum CFHostInfoType {
   <br>kCFHostAddresses = 0,
   <br>kCFHostNames = 1,
   <br>kCFHostReachability = 2
<br>};
<br>typedef enum CFHostInfoType CFHostInfoType;


####常量

* `kCFHostAddresses `

 指定地址要解决或地址被解决。

 可用在 OS X10.3 和以后版本
 
* `kCFHostNames `

 指定名称要解决或者名称被解决。

 可用在 OS X10.3 和以后版本
 
* `kCFHostReachability `

 指定可达性信息是解决或解决可达性信息。

 可用在 OS X10.3 和以后版本
 
 
####重要声明

>OBJECTIVE-C
>
>@import CFNetwork;

>SWIFT
>
>import CFNetwork

####可用性

可用在 OS X10.3 和以后版本


<br>[Error Domains]()

特定域 CFHost 调用错误。


####演示
>SWIFT
>
<br>let kCFStreamErrorDomainNetDB: Int32
<br>let kCFStreamErrorDomainSystemConfiguration: Int32

>OBJECTIVE-C
>
<br>extern const SInt32 kCFStreamErrorDomainNetDB;
<br>extern const SInt32 <br>kCFStreamErrorDomainSystemConfiguration;


####常量

* `kCFStreamErrorDomainNetDB `

 网络数据库的域错误,返回错误(DNS解析器)层(/usr/include/netdb.h中描述)。

 可用在 OS X10.5 和以后版本


* `kCFStreamErrorDomainSystemConfiguration `

 错误从系统配置层域返回错误。(在[System Configuration Framework Reference] 描述。)
 
 [System Configuration Framework Reference]:https://developer.apple.com/library/prerelease/mac/documentation/Networking/Reference/SysConfig/index.html#//apple_ref/doc/uid/TP40001027
 
  可用在 OS X10.5 和以后版本
  
  
####论述

确定一个错误的来源,检查用户信息字典包含在 CFError 对象返回的函数调用或调用[CFErrorGetDomain]() 传入 `CFError` 对象和域你想读的值。