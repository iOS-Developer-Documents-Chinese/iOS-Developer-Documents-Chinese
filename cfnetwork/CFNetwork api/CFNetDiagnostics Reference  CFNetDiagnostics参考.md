#CFNetDiagnostics参考

[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFNetDiagnosticsRef/index.html#//apple_ref/doc/uid/TP40003364)
翻译人:王谦  日期:2015.9.10


>重要:这是一个初步的文档的 API 或技术发展。苹果是提供这些信息帮助你计划采用的技术和编程接口介绍 Apple-branded 产品使用。这些信息变更,软件实现根据本文档应与最后的操作系统软件测试和最终的文档。新版本的文档可以提供未来的 API 或技术。



| 继承自        |   符合     |    导入语句     |
|   --------   |   --------:  | :----    |
| 不适用      | 不适用   |   @import CFNetwork       |
|          |       |    可用性 |
|          |      |  不适用 |


CFNetDiagnostics类型允许您诊断难懂的网络相关问题。


##函数

###创建一个网络诊断对象


[CFNetDiagnosticCreateWithStreams]()

从网络创建一对 CFStreams 诊断对象。


####演示

>SWIFT
>
>func CFNetDiagnosticCreateWithStreams(_ alloc: CFAllocator?, _ readStream: CFReadStream?, _ writeStream: CFWriteStream?) -> Unmanaged < CFNetDiagnostic >

>OBJECTIVE-C
>
>CFNetDiagnosticRef CFNetDiagnosticCreateWithStreams ( CFAllocatorRef alloc, CFReadStreamRef readStream, CFWriteStreamRef writeStream );

####参数

| alloc |使用分配器为新对象分配内存。通过 `NULL` 或 `kCFAllocatorDefault` 使用当前默认的分配器。|
|---:|:---|
| **readStream** |**引用一个 read stream 的连接失败,或 `NULL` 如果你不想 `CFNetDiagnosticRef` 读流。**|
| **writeStream** |**引用 write stream 的连接失败,或 `NULL` 如果你不想 CFNetDiagnosticRef 有一个 write stream 。**|
|**function result**|**你可以通过CFNetDiagnosticRef,[CFNetDiagnosticDiagnoseProblemInteractively]() 或 CFNetDiagnosticCopyNetworkStatusPassively。参考 [Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).**|


####论述

参考这个函数来 read steam 和 write stream (或仅仅read steam 或仅仅 write stream )创建一个引用CFNetDiagnostic对象的一个实例。你可以通过引用 [CFNetDiagnosticDiagnoseProblemInteractively]() 打开一个网络诊断窗口或用[CFNetDiagnosticCopyNetworkStatusPassively]() 得到一个连接 `readStream` 和 `writeStream` 的描述。


####特殊注意事项

他的功能是线程安全的,只要另一个线程不会改变 CFNetDiagnosticRef 同时相同。


####可用性

可用在 OS X10.4 版本以后



<br>[CFNetDiagnosticCreateWithURL]()

创建一个CFURLRef CFNetDiagnosticRef。


####演示

>SWIFT
>
>func CFNetDiagnosticCreateWithURL(_ alloc: CFAllocator, _ url: CFURL) -> Unmanaged < CFNetDiagnostic >

 
>OBJECT
>
> CFNetDiagnosticRef CFNetDiagnosticCreateWithURL ( CFAllocatorRef alloc, CFURLRef url ); 


####参数

| alloc |分配器使用为新对象分配内存。通过 `NULL` 或 `kCFAllocatorDefault` 使用当前默认的分配器。|
|---:|:---|
| url | CFURLRef 指失败的连接。|


####返回值

CFNetDiagnosticRef 你可以通过 [CFNetDiagnosticDiagnoseProblemInteractively]() 或者
[CFNetDiagnosticCopyNetworkStatusPassively]()。请参考
[Create Rule] 

[Create Rule]:https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029


####论述

这个函数使用一个 URL 创建一个 CFNetDiagnostic 实例对象作为参考。你可以通过引用[CFNetDiagnosticDiagnoseProblemInteractively]()打开网络诊断窗口或[CFNetDiagnosticCopyNetworkStatusPassively]()得到连接引用`readStream`和`writeStream`的描述。


####特殊注意事项

这个函数是线程安全的,只要同样的时间 CFNetDiagnosticRef 另一个线程不会改变。


####可用性

可用在 OS X 10.4和以后版本


##CFNetDiagnostics 函数


**1.** [CFNetDiagnosticSetName]()

重写显示应用程序的名称。


####演示
>SWIFT
>
>func CFNetDiagnosticSetName(_ details: CFNetDiagnostic, _ name: CFString)

>OBJECTIVE-C
>
>void CFNetDiagnosticSetName ( CFNetDiagnosticRef details, CFStringRef name );


####参数
| details |网络诊断对象是应用程序的名称。|
|---:|:---|
| name |要设置的名称。|

####论述

框架要求应用程序名称显示给用户应用程序,名称来自包标识符的当前运行的应用程序,该应用程序本地化。如果你想覆盖应用程序名称,使用这个函数来设置显示的名称。



####特殊注意事项

这个函数是线程安全的,只要同样的时间 CFNetDiagnosticRef 另一个线程不会改变。


####可用性

可用在 OS X 10.4和以后版本


<br>**2.**[CFNetDiagnosticDiagnoseProblemInteractively]()

打开一个网络诊断窗口。


####演示
>SWIFT
>
>func CFNetDiagnosticDiagnoseProblemInteractively(_ details: CFNetDiagnostic) -> CFNetDiagnosticStatus

>OBJECTIVE-C
>
>CFNetDiagnosticStatus CFNetDiagnosticDiagnoseProblemInteractively ( CFNetDiagnosticRef details );


####参数
| details |网络诊断对象,通过创建 [ CFNetDiagnosticCreateWithStreams]() 或 [CFNetDiagnosticCreateWithURL](), 打开窗口。|
|---:|:---|

####返回值

如果 CFNetDiagnosticNoErr 没有发生错误,或如果发生错误阻止成功调用 CFNetDiagnosticErr 。

####论述

这个函数打开网络诊断窗口,一旦窗口是开着的并立即返回。

####特殊注意事项

这个函数是线程安全的,只要同样的时间 CFNetDiagnosticRef 另一个线程不会改变。


####可用性

可用在 OS X 10.4和以后版本


**3.**[CFNetDiagnosticCopyNetworkStatusPassively]()

获得一个网络状态值。

####演示
>SWIFT
>
>func CFNetDiagnosticCopyNetworkStatusPassively(_ details: CFNetDiagnostic, _ description: UnsafeMutablePointer < Unmanaged<CFString > ? >) -> CFNetDiagnosticStatus

>OBJECTIVE-C
>
>CFNetDiagnosticStatus CFNetDiagnosticCopyNetworkStatusPassively ( CFNetDiagnosticRef details, CFStringRef _Nullable *description );

####参数
| details | CFNetDiagnosticRef,通过创建 [ CFNetDiagnosticCreateWithStreams]() 或 [CFNetDiagnosticCreateWithURL]()的网络诊断状态。|
|---:|:---|
| description|如果不为 NULL,返回包含一个本地化的字符串和包含一个描述当前的网络状态。请参考 [Create Rule]。|

[Create Rule]:https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029


####返回值

一个网络状态值。

####论述

这个函数返回一个状态值,可以用来显示基本信息连接,和可选的本地化字符串包含一个描述当前的网络状态。

这个函数是保证不产生网络活动。


####特殊注意事项

这个函数是线程安全的,只要同样的时间 CFNetDiagnosticRef 另一个线程不会改变。


####可用性

可用在 OS X 10.4和以后版本


####数据类型

[CFNetDiagnosticRef]()

一个不透明的 CFNetDiagnostic 参考定义。


####演示
>SWIFT
>
>typealias CFNetDiagnosticRef = CFNetDiagnostic

>OBJECTIVE-C
>
>typedef struct __CFNetDiagnostic* CFNetDiagnosticRef;


####可用性

可用在 OS X 10.4和以后版本


<br>[CFNetDiagnosticStatus]()

CFIndex类型,用于返回状态值 CFNetDiagnostic 状态和诊断功能。对于一个可能值的列表, 看 [CFNetDiagnosticStatusValues Constants]()。


####演示
>SWIFT
>
>typealias CFNetDiagnosticStatus = CFIndex

>OBJECTIVE-C
>
>typedef CFIndex CFNetDiagnosticStatus;

####可用性

可用在 OS X 10.4和以后版本


##常量

[CFNetDiagnosticStatusValues
]()

常量为诊断状态值。


####演示
>SWIFT
>
>enum CFNetDiagnosticStatusValues : Int32 {
>   <br>case NoErr
    <br>case Err
    <br>case ConnectionUp
    <br>case ConnectionIndeterminate
    <br>case ConnectionDown
><br>}

>OBJECTIVE-C
>
>enum CFNetDiagnosticStatusValues { <br>
    <br>kCFNetDiagnosticNoErr = 0,
    <br>kCFNetDiagnosticErr = -66560L,
    <br>kCFNetDiagnosticConnectionUp = -66559L,
    <br>kCFNetDiagnosticConnectionIndeterminate = -66558L,
    <br>kCFNetDiagnosticConnectionDown = -66557L 
 <br>};
 <br>typedef enum CFNetDiagnosticStatusValues CFNetDiagnosticStatusValues;


####常量
* `kCFNetDiagnosticNoErr`

 没有错误发生但没有状态。

 可用在 OS X 10.4和以后版本
 

* `kCFNetDiagnosticErr`

  一个错误发生,阻止完成调用。

  可用在 OS X 10.4和以后版本
  
* `kCFNetDiagnosticConnectionUp`

  呈现连接活动。

  可用在 OS X 10.4和以后版本
  
* `kCFNetDiagnosticConnectionIndeterminate `

  连接的状态尚不清楚。

  可用在 OS X 10.4和以后版本
  
  
* `kCFNetDiagnosticConnectionDown `

  连接似乎没有活动。


  可用在 OS X 10.4和以后版本
  
  
####论述

诊断状态返回的值通过调用[CFNetDiagnosticDiagnoseProblemInteractively]() 和 [CFNetDiagnosticCopyNetworkStatusPassively]()。

####重要声明
>OBJECTIVE-C
>
>
>@import CFNetwork;


>SWIFT
>
>
>import CFNetwork


####可用性

可用在 OS X 10.4和以后版本
