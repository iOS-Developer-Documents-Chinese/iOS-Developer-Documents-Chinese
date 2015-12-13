

#CFFTPStream 参考

[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFFTPStreamRef/index.html#//apple_ref/doc/uid/TP40003359
)
翻译人:[王谦](https://github.com/iOS-Developer-Documents-Chinese/iOS-Developer-Documents-Chinese) 日期:2015.9.7


    
>重要的
>
>这是一个初步的技术开发文档 API 。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实施的软件，应与最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供未来的 API 或技术。


| 继承自        |   符合     |    导入语句     |
|   --------   |   --------:  | :----    |
| 不适用      | 不适用   |   @import CFNetwork       |
|          |       |    可用性 |
|          |      |  不适用 |




本文档描述了 CFStream 函数使用 FTP 连接。这是 CFFTP API 的一部分。



###功能


##CFFTP功能



**1.**[CFReadStreamCreateWithFTPURL]()
(OS X v10.11)

>创建一个 FTP read stream

####演示

>SWIFT
>
>func CFReadStreamCreateWithFTPURL(_ alloc: CFAllocator?, _ ftpURL: CFURL) -> Unmanaged<CFReadStream> 

>Objective-C
>
>CFReadStreamRef _Nonnull CFReadStreamCreateWithFTPURL ( CFAllocatorRef _Nullable alloc, CFURLRef _Nonnull ftpURL ); 

####参数
| alloc |使用分配器为新对象分配内存。通过 `NULL`或 `kCFAllocatorDefault `使用当前的默认分配器。|
| -----|:-----|
| *ftpURL*|一个指针指向的 URL 下载 CFUR L结构可以通过调用创建的任何 `CFURLCreate` 函数,如 `CFURLCreateWithString `。|

####返回值
一个新的 read stream ，或 NULL , 如果调用失败.所有权遵循 [Create Rule.](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029) 

####讨论
这个函数创建一个 FTP read stream 通过一个 FTP URL 下载数据。如果这个 `ftpURL`参数创建的用户名和密码作为 URL 的一部分(比如`ftp://username:password@ftp.example.com`)那么用户名和密码将自动在`CFReadStream`设置。否则，叫[CFReadStreamSetProperty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamSetProperty) 设置 steam 的属性。如`kCFStreamPropertyFTPUserName`和`kCFStreamPropertyFTPPassword`与 stream 用于登录时打开关联的用户名和密码。见[Constants](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFFTPStreamRef/index.html#//apple_ref/doc/uid/TP40003359-CH4-SW1) 所有的 FTP stream 属性的描述。

与 FTP 服务器发起连接,调用 [CFReadStreamOpen](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamOpen).读取 FTP stream ,调用 [CFReadStreamRead](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamRead).如果指的是一个目录的 URL , stream 提供了由服务器发送结果的清单。如果指的是一个文件的 URL , stream 提供了该文件的数据。

关闭与 FTP 服务器的连接,调用 [CFReadStreamClose](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamClose).

####可用性
可用在OS X 10.3和更高版本。

在OS X v10.11弃用。



<br>**2.**[CFWriteStreamCreateWithFTPURL]()
(OS X v10.11)

>创建一个 FTP write stream.

####演示
>SWIFT
>
>func CFWriteStreamCreateWithFTPURL(_ alloc: CFAllocator?, _ ftpURL: CFURL) ->
>
>Unmanaged < CFWriteStream >

>OBJECTIVE-C
>
>CFWriteStreamRef _Nonnull CFWriteStreamCreateWithFTPURL ( CFAllocatorRef _Nullable alloc, CFURLRef _Nonnull ftpURL );

####参数
| alloc |使用分配器为新对象分配内存。通过 `NULL`或 `kCFAllocatorDefault `使用当前的默认分配器。|
| -----|:-----|
| *ftpURL*|一个指针指向的 URL 下载 CFUR L结构可以通过调用创建的任何 `CFURLCreate` 函数,如 `CFURLCreateWithString `。|  

####返回值
一个新的 read stream ，或 NULL , 如果调用失败.所有权遵循 [Create Rule.](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029) 

####论述
这个函数创建一个 FTP write stream 通过一个 FTP URL 上传数据.如果这个`ftpURL`参数创建的用户名和密码作为 URL 的一部分(比如`ftp://username:password@ftp.example.com`)那么用户名和密码将自动在CFWriteStream设置。否则，调用 [CFWriteStreamSetProperty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/c/func/CFWriteStreamSetProperty) 设置 steam 的属性。如`kCFStreamPropertyFTPUserName`和`kCFStreamPropertyFTPPassword`与 stream 用于登录时打开关联的用户名和密码。见[Constants]() 所有的 FTP stream 属性的描述。

创建 write stream,你可以调用 [CFWriteStreamGetStatus](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/c/func/CFWriteStreamGetStatus) 在任何时间检查 stream 的状态.

与 FTP 服务器发起连接,调用 [CFWriteStreamOpen](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/c/func/CFWriteStreamOpen) 如果 URL 指定一个目录,打开的是紧跟在后面的事件 `kCFStreamEventEndEncountered` (和 stream 传递的状态 `kCFStreamStatusAtEnd` )
一旦 stream 达到这种状态,就已经创建了目录.

写入一个 FTP stream,调用 [CFWriteStreamWrite](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/c/func/CFWriteStreamWrite).

关闭与 FTP 服务器的连接,调用[CFWriteStreamClose](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/c/func/CFWriteStreamClose).

####可用性

可用在OS X 10.3和更高版本。

在OS X v10.11弃用。


<br>**3.**[CFFTPCreateParsedResourceListing]()
(OS X v10.11)
>解析一个 FTP 清单目录

####演示
>SWIFT
>
>func CFFTPCreateParsedResourceListing(_ alloc: CFAllocator?, _ buffer: UnsafePointer < UInt8 > , _ bufferLength: CFIndex, _ parsed: UnsafeMutablePointer < Unmanaged< CFDictionary > ? > ) -> CFIndex

>OBJECTIVE-C
>
>CFIndex CFFTPCreateParsedResourceListing ( CFAllocatorRef _Nullable alloc, const UInt8 * _Nonnull buffer, CFIndex bufferLength, CFDictionaryRef _Nullable * _Nullable parsed );

####参数

| alloc |  使用分配器为新对象分配内存。通过 `NULL`或 `kCFAllocatorDefault `使用当前的默认分配器。 |
|---:|:---|
|  **buffer**  |一个指向支持缓冲区零个或多个资源列表。|
|   **bufferLength**  |指向 buffer 缓冲区的字节长度。   |
|  **parsed**   |在返回时,包含一个字典包含资源信息进行解析。如果解析失败,则返回一个空指针。|

####返回值
解析的字节数,如果解析没有可用的字节数就返回0,如果解析失败返回1.

####讨论
这个函数检查缓冲区的内容作为一个 FTP 目录清单并解析成 CFDictionary 为单个文件或文件夹的信息.返回CFDictionary `parsed` 参数,返回使用缓冲区的字节数。

####可用性

可用在OS X 10.3和更高版本。

在OS X v10.11弃用。


##常量




[CFStream FTP Property Constants]()

常量设置和复制CFStream FTP属性。

####演示

>SWIFT
>
>let kCFStreamPropertyFTPUserName: CFString
>
>let kCFStreamPropertyFTPPassword: CFString
>
>let kCFStreamPropertyFTPUsePassiveMode: CFString
>
>let kCFStreamPropertyFTPResourceSize: CFString
>
>let kCFStreamPropertyFTPFetchResourceInfo: CFString
>
>let kCFStreamPropertyFTPFileTransferOffset: CFString
>
>let kCFStreamPropertyFTPAttemptPersistentConnection: CFString
>
>let kCFStreamPropertyFTPProxy: CFString
>
>let kCFStreamPropertyFTPProxyHost: CFString
>
>let kCFStreamPropertyFTPProxyPort: CFString
>
>let kCFStreamPropertyFTPProxyUser: CFString
>
>let kCFStreamPropertyFTPProxyPassword: CFString


<br>
>OBJECTIVE-C

>const CFStringRef kCFStreamPropertyFTPUserName;
>
>const CFStringRef kCFStreamPropertyFTPPassword;
>
>const CFStringRef kCFStreamPropertyFTPUsePassiveMode;
>
>const CFStringRef kCFStreamPropertyFTPResourceSize;
>
>const CFStringRef kCFStreamPropertyFTPFetchResourceInfo;
>
>const CFStringRef kCFStreamPropertyFTPFileTransferOffset;
>
>const CFStringRef kCFStreamPropertyFTPAttemptPersistentConnection;
>
>const CFStringRef kCFStreamPropertyFTPProxy;
>
>const CFStringRef kCFStreamPropertyFTPProxyHost;
>
>extern const CFStringRef kCFStreamPropertyFTPProxyPort;
>
>extern const CFStringRef kCFStreamPropertyFTPProxyUser;
>
>extern const CFStringRef kCFStreamPropertyFTPProxyPassword;


####常量

* `kCFStreamPropertyFTPUserName`

 FTP 用户名流属性键设置和拷贝操作。值类型 CFString 存储登录用户名。不需要匿名 FTP 时设置此属性。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。
　

* `kCFStreamPropertyFTPPassword`

 FTP 密码流属性键设置和拷贝操作。值类型 CFString 存储登录密码。不需要匿名  FTP 时设置此属性。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。

* `kCFStreamPropertyFTPUsePassiveMode`

 FTP 被动模式流属性键设置和拷贝操作。设置这个属性 `kCFBooleanTrue` 使被动模式,设置这个属性 `kCFBooleanFalse` 禁用被动模式。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。


* `kCFStreamPropertyFTPResourceSize`

 FTP 资源大小读流属性键复制操作。这个属性存储 CFNumber 类型  `kCFNumberLongLongType` 代表资源大小的字节。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。


* `kCFStreamPropertyFTPFetchResourceInfo`

 FTP 获取资源信息流属性键设置和拷贝操作。设置此属性 kCFBooleanTrue 要求资源信息,比如尺寸,必须提供下载开始前,设置这个属性 kCFBooleanFalse 允许下载开始没有资源信息。这个版本里,大小是唯一的资源信息。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。

* `kCFStreamPropertyFTPFileTransferOffset`

 FTP 文件传输抵消流属性键设置和拷贝操作。这个属性的值是一个 CFNumber 类型`kCFNumberLongLongType`代表的文件偏移量开始转移。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。
　　 

* `kCFStreamPropertyFTPAttemptPersistentConnection`


 持久连接 FTP 尝试流属性键设置和拷贝操作。这个属性设置为 `kCFBooleanTrue` 允许重用现有的服务器连接,设置这个属性`kCFBooleanFalse`不重复使用现有的服务器连接。默认情况下,此属性设置为`kCFBooleanTrue`。


 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。




* `kCFStreamPropertyFTPProxy`

 FTP 代理流属性键设置和拷贝操作。的属性是一个值类型 CFDictionary 持有代理字典键-值对。返回的字典 SystemConfiguration 也可以设置为这个属性的值。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。



* `kCFStreamPropertyFTPProxyHost`

 FTP 代理主机流属性键或FTP代理字典键设置和拷贝操作。这个属性的值是一个 CFString 包含代理服务器的主机名。这个属性可以单独设置和复制或通过 CFDictionary 。这个属性是`kSCPropNetProxiesFTPProxy`  `SCSchemaDefinitions.h` 中定义一样的属性。


 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。




* `kCFStreamPropertyFTPProxyPort`

 FTP 代理端口流属性键或FTP代理字典键设置和拷贝操作。这个属性的值是一个CFNumber 类型`kCFNumberIntType`包含代理服务器的端口号。这个属性可以设置和复制单独或通过CFDictionary。这个属性是一样的`kSCPropNetProxiesFTPPort` `SCSchemaDefinitions.h`中定义的属性。



 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。


* `kCFStreamPropertyFTPProxyUser`


 FTP 代理主机流属性键或 FTP 代理字典键设置和拷贝操作。这个属性的值是一个包含要使用的用户名 CFString 当连接到代理服务器。


 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。


* `kCFStreamPropertyFTPProxyPassword`

 FTP 代理端口流属性键或 FTP 代理字典键设置和拷贝操作。这个属性的值是一个包含要使用的密码CFString 当连接到代理服务器。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。


####论述

CFStream 属性常量用于指定属性设置当调用[CFReadStreamSetProperty]()或[CFWriteStreamSetProperty]()当调用[CFReadStreamCopyProperty]()或[CFWriteStreamCopyProperty]()复制。也可以传递给CFDictionary创造者或一个项目访问或修改。

可用在 OS X v10.3或以后。

在 OS X v10.11弃用。


<br>**2.**[CFStream FTP Resource Constants]()

FTP资源常量。

####演示

>SWIFT
>
>let kCFFTPResourceMode: CFString
>
>let kCFFTPResourceName: CFString
>
>let kCFFTPResourceOwner: CFString
>
>let kCFFTPResourceGroup: CFString
>
>let kCFFTPResourceLink: CFString
>
>let kCFFTPResourceSize: CFString
>
>let kCFFTPResourceType: CFString
>
>let kCFFTPResourceModDate: CFString

>OBJECTIVE-C

>const CFStringRef kCFFTPResourceMode;
>
>const CFStringRef kCFFTPResourceName;
>
>const CFStringRef kCFFTPResourceOwner;
>
>const CFStringRef kCFFTPResourceGroup;
>
>const CFStringRef kCFFTPResourceLink;
>
>const CFStringRef kCFFTPResourceSize;
>
>const CFStringRef kCFFTPResourceType;
>
>const CFStringRef kCFFTPResourceModDate;

####常量

* `kCFFTPResourceMode`

 CFDictionary 关键包含访问权限的 CFNumber ,定义在`sys/types.h`,的 FTP 资源。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。

* `kCFFTPResourceName`

 CFDictionary 键获取 CFString 包含 FTP 资源的名称。

 可用在 OS X v10.3或以后。
 
 在 OS X v10.11弃用。

* `kCFFTPResourceOwner`

 CFDictionary 键获取 CFString 包含 FTP 资源的所有者的名称。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。

* `kCFFTPResourceGroup`

 CFDictionary 键获取 CFString 包含一组名称,共享 FTP 资源。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。

* `kCFFTPResourceLink`

 CFDictionary 获得 CFString 包含符号链接的信息。如果项目是一个符号链接, CFString 包含路径链接引用的项目。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。



* `kCFFTPResourceSize`

 CFDictionary 键获取 CFNumber 包含大小字节的 FTP 资源。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。


* `kCFFTPResourceType`

 CFDictionary 获取 CFNumber 包含 FTP 资源定义的类型在`sys/dirent.h`。

 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。


* `kCFFTPResourceModDate`

 CFDictionary 键获取 CFDate 包含最后一个 FTP 资源修改日期和时间。


 可用在 OS X v10.3或以后。

 在 OS X v10.11弃用。


####论述

 FTP资源键的值是在一系列的目录列表中

CFFTPCreateParsedResourceListing函数。

可用性

可用在操作系统×10.3版本。

<br>**3.**[Error Domains]()

错误域 调用CFFTPStream 。

####演示

>SWIFT
>
>let kCFStreamErrorDomainFTP: Int32

>OBJECTIVE-C
>
>extern const SInt32 kCFStreamErrorDomainFTP;


####常量

*`kCFStreamErrorDomainFTP`

 域返回最后一个FTP服务器返回的错误结果代码。

 可以在OS X 10.3及以后版本。


####论述

确定一个错误的来源,检查调用包含在 CFError 对象返回函数的用户信息字典或调用CFErrorGetDomain传入CFError对象和你想读的域值。
