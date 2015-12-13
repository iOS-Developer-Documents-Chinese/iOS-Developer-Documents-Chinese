#CFHTTPMessage参考

[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/c/func/CFHTTPMessageCreateCopy)

翻译人:王谦 翻译时间:2015.9.7

>重要的

>这是一个API 的初步技术开发文档。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实施的软件，应与最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供未来的 API 或技术。

| 继承自        |   符合     |    导入语句     |
|   --------   |   --------:  | :----    |
| 不适用      | 不适用   |   @import CFNetwork       |
|          |       |    可用性 |
|          |      |  不适用 |

该CFHTTPMessage难理解的类型是HTTP消息。

###函数

##创建一个 Message

**1.**[CFHTTPMessageCreateCopy]()

获取CFHTTPMessage对象的副本。

####演示
>SWIFT
>func CFHTTPMessageCreateCopy(_ alloc: CFAllocator?, _ message: CFHTTPMessage) -> Unmanaged< CFHTTPMessage >

>OBJECTIVE-C
>CFHTTPMessageRef _Nonnull CFHTTPMessageCreateCopy ( CFAllocatorRef _Nullable alloc, CFHTTPMessageRef _Nonnull message );


####参数
| allocator | 分配器用来分配内存为新对象。传递 NULL 或kCFAllocatorDefault 使用当前默认的分配。|
| -----|:-----|
| **message** |**该消息复制**|


####返回值
一个 CFHTTPMessage 对象,如果有在创建对象的一个问题。所有权遵循 [The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

讨论

这个函数返回一个`CFHTTPMessage`对象的副本,您可以修改。例如,通过调用 [CFHTTPMessageCopyHeaderFieldValue]() 或[callingCFHTTPMessageSetBody]()。然后将消息序列化调用[CFHTTPMessageCopySerializedMessage]() 发送序列化的消息到客户端或服务器。

可用性

可用在 OS X 10.1或更高版本

**2.**[CFHTTPMessageCreateEmpty]()

创建并返回一个新的空CFHTTPMessage对象。

####演示
>SWIFT
>
>func CFHTTPMessageCreateEmpty(_ alloc: CFAllocator?, _ isRequest: Bool) -> Unmanaged < CFHTTPMessage >

>OBJECTIVE-C
>
>CFHTTPMessageRef _Nonnull CFHTTPMessageCreateEmpty ( CFAllocatorRef _Nullable alloc, Boolean isRequest );

####参数
| allocator | 分配器用来分配内存为新对象。传递 NULL 或kCFAllocatorDefault 使用当前默认的分配。|
| -----|:-----|
| **isRequest** |**标志创建一个空消息请求或空消息响应。**|

####返回值
如果创建一个新的`CFHTTPMessage` 对象返回NULL,那么请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。



讨论

调用[CFHTTPMessageAppendBytes]()存储传入,在空的消息序列化HTTP请求或响应消息对象。

可用性
可用在 OS X 10.1或更高版本


**3.**[CFHTTPMessageCreateRequest]()

创建并返回一个CFHTTPMessage对象为一个HTTP请求。

####演示
>SWIFT
>
>func CFHTTPMessageCreateRequest(_ alloc: CFAllocator?, _ requestMethod: CFString, _ url: CFURL, _ httpVersion: CFString) -> Unmanaged < CFHTTPMessage >

>OBJECTIVE-C
>
>CFHTTPMessageRef _Nonnull CFHTTPMessageCreateRequest ( CFAllocatorRef _Nullable alloc, CFStringRef _Nonnull requestMethod, CFURLRef _Nonnull url, CFStringRef _Nonnull httpVersion );


####参数
| allocator  |分配器使用为新对象分配内存。通过 NULL 或kCFAllocatorDefault 使用当前默认的分配器。|
|---:|:----|
| **requestMethod**  |**使用任何 HTTP 请求方法允许的httpVersion 规定的版本**      |
|  **url** | **URL 的请求将被发送**。      |
|   **httpVersion** | **HTTP版本对于这个消息。通过`kCFHTTPVersion1_0`或`kCFHTTPVersion1_1`**。     |

####返回值

如果创建新的`CFHTTPMessage`对象返回为NULL。请参考 [The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029).


####讨论

这个函数返回一个 CFHTTPMessage 对象,您可以使用它们来构建一个 HTTP 请求。继续构建请求通过调用 [CFHTTPMessageSetBody]() 设置消息的 body。调用[CFHTTPMessageCopyHeaderFieldValue]()设置消息的头。


如果你使用一个`CFReadStream`对象发送消息，调用 [CFReadStreamCreateForHTTPRequest]() 创建一个 read stream 请求。如果你没有使用 `CFReadStream`，调用[CFHTTPMessageCopySerializedMessage]() 使消息准备传输序列化。

可用性
可用在 OS X 10.1或更高版本



**4.**[CFHTTPMessageCreateResponse]()

创建并返回一个 HTTP 响应 CFHTTPMessage 对象。

####演示
>SWIFT
>
>func CFHTTPMessageCreateResponse(_ alloc: CFAllocator?, _ statusCode: CFIndex, _ statusDescription: CFString?, _ httpVersion: CFString) -> Unmanaged < CFHTTPMessage >

>OBJECTIVE-C
>
>CFHTTPMessageRef _Nonnull CFHTTPMessageCreateResponse ( CFAllocatorRef _Nullable alloc, CFIndex statusCode, CFStringRef _Nullable statusDescription, CFStringRef _Nonnull httpVersion );

####参数

| allocator |使用分配器为新对象分配内存。通过`NULL`或`kCFAllocatorDefault`使用当前默认的分配器。|
|---:|:----|
| statusCode |这个消息响应的状态码。状态码的状态代码可以是任何部分,是6.1.1 RFC 2616中定义的。|
| statusDescription |对应于状态代码的描述。通过 NULL 使用标准的描述给定的状态代码,在 RFC 2616中。|
| httpVersion |这个消息响应 HTTP 版本。通过   kCFHTTPVersion1 _ 0 或 kCFHTTPVersion1 _ 1。 |


####返回值

如果创建一个新的`CFHTTPMessage` 对象返回`NULL`,那么请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。


讨论

这个函数返回一个 CFHTTPMessage 对象,您可以使用它们来构建一个HTTP响应。继续构建通过调用 [CFHTTPMessageSetBody]() 设置消息的 body。调用 [CFHTTPMessageSetHeaderFieldValue]() 设置消息的头。然后调用 [CFHTTPMessageCopySerializedMessage]() 准备让传输的消息序列化。  


可用性

可用在 OS X 10.1 或更高版本。


##修改一条 message


**1.**[CFHTTPMessageAppendBytes]()

添加数据 CFHTTPMessage 对象。


####演示
>SWIFT
>
>func CFHTTPMessageAppendBytes(_ message: CFHTTPMessage, _ newBytes: UnsafePointer < UInt8 > , _ numBytes: CFIndex) -> Bool

>OBJECTIVE-C
>
>Boolean CFHTTPMessageAppendBytes ( CFHTTPMessageRef _Nonnull message, const UInt8 * _Nonnull newBytes, CFIndex numBytes );


####参数

| message |修改的 message。|
|---:|:----|
| **newBytes** |**一个引用到数据的附加。**|
| **numBytes** |**`newBytes`是指向的数据的长度。**|	

####返回值

如果数据成功添加 返回 TURE,否则 返回 FALSE。

####讨论

这个函数附加 newBytes 指定的数据到指定的消息对象通过调用 [CFHTTPMessageCreateEmpty]() 创建。传入数据的序列化的HTTP请求和响应被客户端或者服务器收到。而附加的数据,这个函数反序列化,删除可能包含任何基于http的消息格式,并存储消息的消息对象。你可以调用  [CFHTTPMessageCopyVersion]() ,  [CFHTTPMessageCopyBody]() ,  [CFHTTPMessageCopyHeaderFieldValue]() ,和 [CFHTTPMessageCopyAllHeaderFields]()  分别获取 HTTP 消息的版本,消息的 body,一个特定的头字段,所有消息的头.

如果是请求消息,你可以调用 [CFHTTPMessageCopyRequestURL]() 和
[CFHTTPMessageCopyRequestMethod]() 分别获取消息请求的 URL 和请求方法。


如果是消息的响应,你可以调用[CFHTTPMessageGetResponseSttusCode]()和[CFHTTPMessageCopyResponseStatusLine]() 分别获得消息的状态代码和状态栏。

可用性

可用在 OS X 10.1 或更高版本。


**2.**[CFHTTPMessageSetBody]()

设置 CFHTTPMessage 对象的 body。

####演示
>SWIFT
>
>func CFHTTPMessageSetBody(_ message: CFHTTPMessage, _ bodyData: CFData)


>OBJECTIVE-C
>
>void CFHTTPMessageSetBody ( CFHTTPMessageRef _Nonnull message, CFDataRef _Nonnull bodyData );


####参数
| message |修改的 message。|
|---:|:---|
| **bodyData** |**为数据设置消息的 body。**|

可用性

可用在 OS X 10.1 或更高版本。


**3.**[CFHTTPMessageSetHeaderFieldValue]()

设置 HTTP 消息 header 字段的值。


####演示
>SWIFT
>
>func CFHTTPMessageSetHeaderFieldValue(_ message: CFHTTPMessage, _ headerField: CFString, _ value: CFString?)


>OBJECTIVE-C
>
>void CFHTTPMessageSetHeaderFieldValue ( CFHTTPMessageRef _Nonnull message, CFStringRef _Nonnull headerField, CFStringRef _Nullable value );


####参数

| message |修改的 meassge。|
|---:|:---|
| **headerField** |**设置 header 字段。**|
| **value** |**设置值。***|

可用性

可用在 OS X 10.1 或更高版本。

##得到消息的信息

**1.**[CFHTTPMessageCopyBody]()


从主体数据中得到`CFHTTPMessage`对象。


####演示

>SWIFT
>
>func CFHTTPMessageCopyBody(_ message: CFHTTPMessage) -> Unmanaged < CFData > ?


>OBJECTIVE-C
>
>CFDataRef CFHTTPMessageCopyBody ( CFHTTPMessageRef message );


####参数

| message |检查消息|
|--:|:---|

####返回值

如果创建的对象有问题或者如果那个消息没有主体,一个 CFData 对象返回NULL。请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。


可用性

可以用在 OS X version 10.1 和以后版本

<br>**2.**[CFHTTPMessageCopyAllHeaderFields]()


获取所有 `CFHTTPMessage` 对象的 header 字段。

####演示

>SWIFT
>
>func CFHTTPMessageCopyAllHeaderFields(_ message: CFHTTPMessage) -> Unmanaged < CFDictionary >?

>OBJECTIVE-C
>
>CFDictionaryRef CFHTTPMessageCopyAllHeaderFields ( CFHTTPMessageRef message );

####参数
| message |检查消息|
|--:|:---|


####返回值
`CFDictionaryRef`对象包含 `CFStringRef` 对象的键和值,其中关键是头字段名和字典值是头字段的值。如果头字段不能被复制就返回 NULL。请参考 [The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029) 。

论述

HTTP头是不区分大小写。简化你的代码,某些标题字段名称规范化的标准形式。比如,服务器发送一个 `content-length` 的 header,它会自动的调整 `Content-Length`。

返回的字典的头配置为 case-preserving 设置操作(除非键已经存在有不同的情况),并查找键时不区分大小写。

举一个例子,如果设置头 `X-foo`, 然后设置 `X-Foo`,字典的关键 `X-foo`,但值将取自`X-Foo头`。

可用性

可以用在 OS X version 10.1 和以后版本



<br>**3.**[CFHTTPMessageCopyHeaderFieldValue]()

获得 `CFHTTPMessage` 对象的 header 字段的值。

####演示

>SWIFT
>
>func CFHTTPMessageCopyHeaderFieldValue(_ message: CFHTTPMessage, _ headerField: CFString) -> Unmanaged < CFString > ?


>OBJECTIVE-C
>
>CFStringRef CFHTTPMessageCopyHeaderFieldValue ( CFHTTPMessageRef message, CFStringRef headerField );


####返回值
| message |检查消息|
|--:|:---|
| headerField |copy header字段|


####返回值

CFString对象包含headerField指定的字段的副本,如果创建的对象有问题如果指定的`header`是不存在的,返回 NULL。请参考 [The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

可用性

可以用在 OS X version 10.1 和以后版本


<br>**4.**[CFHTTPMessageCopyRequestMethod]()

获取一个 CFHTTPMessage 对象的请求方法。

####演示
>SWIFT
>
>func CFHTTPMessageCopyRequestMethod(_ request: CFHTTPMessage) -> Unmanaged < CFString > ?

>OBJECTIVE-C
>
>CFStringRef CFHTTPMessageCopyRequestMethod ( CFHTTPMessageRef request );

####参数

| request |检查消息。这一定是一个请求消息|
|---:|:--|

####返回值

CFString对象包含消息的请求方法的副本,如果创建一个有问题的对象,返回 NULL.请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

可用性

可以用在 OS X version 10.1 和以后版本

<br>**5.**[CFHTTPMessageCopyRequestURL]()

获取一个`CFHTTPMessage`对象的 URL。

####演示
>SWIFT
>
>func CFHTTPMessageCopyRequestURL(_ request: CFHTTPMessage) -> Unmanaged < CFURL > ?

>OBJECTIVE-C
>
>CFURLRef CFHTTPMessageCopyRequestURL ( CFHTTPMessageRef request );

####参数
| request |检查消息。这一定是一个请求消息|
|--:|:---|

####返回值

CFURLRef对象包含URL,如果创建一个有问题的对象,返回 NULL.请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。


可用性

可以用在 OS X version 10.1 和以后版本


<br>**6.**[CFHTTPMessageCopySerializedMessage]()

序列化一个 CFHTTPMessage 对象。

####演示
>SWIFT
>
>func CFHTTPMessageCopySerializedMessage(_ message: CFHTTPMessage) -> Unmanaged < CFData > ?

>OBJECTIVE-C
>
>CFDataRef CFHTTPMessageCopySerializedMessage ( CFHTTPMessageRef message );

####参数
| request |序列化消息|
|--:|:---|


####返回值

CFURLRef对象包含序列化消息,如果创建一个有问题的对象,返回 NULL.请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。


####论述

这个函数传递返回一个序列化`CFHTTPMessage`对象副本。

可用性

可以用在 OS X version 10.1 和以后版本

<br>**7.**[CFHTTPMessageCopyVersion]()

获取一个 CFHTTPMessage 对象 HTTP 的版本。

####演示
>SWIFT
>
>func CFHTTPMessageCopyVersion(_ message: CFHTTPMessage) -> Unmanaged < CFString >

>OBJECTIVE-C
>
>CFStringRef CFHTTPMessageCopyVersion ( CFHTTPMessageRef message );



####参数
| request |检查参数|
|--:|:---|

####返回值

CFURLRef对象,如果创建一个有问题的对象,返回 NULL.请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

可用性

可以用在 OS X version 10.1 和以后版本




<br>**8.**[CFHTTPMessageIsRequest]()

返回一个布尔值表示CFHTTPMessage是否请求或响应。

####演示
>SWIFT
>
>func CFHTTPMessageIsRequest(_ message: CFHTTPMessage) -> Bool

>OBJECTIVE-C
>
>Boolean CFHTTPMessageIsRequest ( CFHTTPMessageRef message );


可用性

可以用在 OS X version 10.1 和以后版本


<br>**9.**[CFHTTPMessageIsHeaderComplete]()

决定一个消息头就完成了。

####演示
>SWIFT
>
>func CFHTTPMessageIsHeaderComplete(_ message: CFHTTPMessage) -> Bool
>
>OBJECTIVE-C
>
>Boolean CFHTTPMessageIsHeaderComplete ( CFHTTPMessageRef message );

####参数
| message |消息验证。|
|--:|:--|
|function result|如果消息头完成了为 TURE ,否则为 FALSE |

####论述

用[CFHTTPMessageAppendBytes]()之后,调用这个函数,看看消息头完成了没。

可用性

可以用在 OS X version 10.1 和以后版本


<br>**10.**[CFHTTPMessageGetResponseStatusCode]()

获得的状态代码`CFHTTPMessage`对象代表一个HTTP响应。

####演示
>SWIFT
>
>func CFHTTPMessageGetResponseStatusCode(_ response: CFHTTPMessage) -> CFIndex
>
>OBJECTIVE-C
>
>CFIndex CFHTTPMessageGetResponseStatusCode ( CFHTTPMessageRef response );

| response |消息验证。这必须是一个响应消息|
|--:|:--|
|function result|RFC 2616定义的状态代码,这是部分。|

可用性

可以用在 OS X version 10.1 和以后版本


<br>**11.**[CFHTTPMessageCopyResponseStatusLine]()

得到`CFHTTPMessage`对象的状态。

####演示
>SWIFT
>
>func CFHTTPMessageCopyResponseStatusLine(_ response: CFHTTPMessage) -> Unmanaged < CFString > ?
>
>OBJECTIVE-C
>
>CFStringRef CFHTTPMessageCopyResponseStatusLine ( CFHTTPMessageRef response );

####参数
| response |消息验证。|
|--:|:--|

####返回值

一个字符串包含消息的状态行,如果创建一个有问题的对象,返回 NULL.状态行包括消息的协议版本和一个成功或错误代码。请参考[The Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

可用性

可以用在 OS X version 10.1 和以后版本


##消息身份验证

**1.**[CFHTTPMessageApplyCredentials]()

执行 `CFHTTPAuthentication` 对象指定的身份验证方法。

####演示
>SWIFT
>
>func CFHTTPMessageApplyCredentials(_ request: CFHTTPMessage, _ auth: CFHTTPAuthentication, _ username: CFString?, _ password: CFString?, _ error: UnsafeMutablePointer < CFStreamError > ) -> Bool
>
>OBJECTIVE-C
>
>Boolean CFHTTPMessageApplyCredentials ( CFHTTPMessageRef request, CFHTTPAuthenticationRef auth, CFStringRef username, CFStringRef password, CFStreamError *error );


####参数

| request |执行请求的身份验证方法。|
|---:|:---|
| auth |执行FHTTPAuthentication对象指定身份验证方法。|
| username |用户名执行身份验证。|
| password |密码进行身份验证。|
| error |如果出现错误,返回包含 CFStreamError 对象,描述了错误和错误的地方。如果你不想收到错误信息,就返回 NULL 。|

####返回值

如果身份验证成功,就返回 TURE ,否则返回 FALSE .

####论述
这个函数执行指定的身份验证方法代表指定的请求身份验证请求使用指定的凭据的用户名和密码。如果,除了用户名和密码,您还需要指定一个帐户域,调用[CFHTTPMessageApplyCredentialDictionary]()这个函数代替。

这个函数是适合执行多个身份验证请求,如果你只需要一个身份验证请求,考虑使用[CFHTTPMessageAddAuthentication]()代替。

####特殊注意事项

这个函数是线程安全的,只要另一个线程不会在同一时间改变同一个CFHTTPMessage对象。

可用性

可以用在 OS X version 10.4 和以后版本


<br>**2.**[CFHTTPMessageApplyCredentialDictionary]()

使用字典包含身份验证凭证来执行指定的身份验证方法CFHTTPAuthentication对象。

####演示
>SWIFT
>
>func CFHTTPMessageApplyCredentialDictionary(_ request: CFHTTPMessage, _ auth: CFHTTPAuthentication, _ dict: CFDictionary, _ error: UnsafeMutablePointer < CFStreamError > ) -> Bool
>
>OBJECTIVE-C
>
>Boolean CFHTTPMessageApplyCredentialDictionary ( CFHTTPMessageRef request, CFHTTPAuthenticationRef auth, CFDictionaryRef dict, CFStreamError *error );


####参数

| request |请求的身份验证方法。|
|---:|:----|
| auth |CFHTTPAuthentication对象指定身份验证方法执行。|
| dict |一个字典包含适用于请求的身份验证凭证。在这本字典键的更多信息,请参阅[CFHTTPAuthenticationRef](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFHTTPAuthenticationRef/index.html#//apple_ref/c/tdef/CFHTTPAuthenticationRef)。|
| error |如果出现错误,返回包含[CFStreamError](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFStreamConstants/index.html#//apple_ref/c/tdef/CFStreamError)对象,描述了错误和错误的领域。如果你不想收到错误信息就返回 NULL。|


####返回值

如果身份验证成功,返回 NULL ,否则返回 FALSE 。


####论述

函数执行指定的身份验证方法代表指定的请求身份验证请求使用指定的凭据字典中包含的字典。这本字典必须包含键值 `kCFHTTPAuthenticationUsername` 和 'kCFHTTPAuthenticationPassword'。如果身份验证[CFHTTPAuthenticationRequiresAccountDomain](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFHTTPAuthenticationRef/index.html#//apple_ref/c/func/CFHTTPAuthenticationRequiresAccountDomain)返回TRUE,字典`kCFHTTPAuthenticationAccountDomain`密钥也必须包含一个值。

####特殊注意事项
这个函数是线程安全的,只要另一个线程不会改变在同一时间中同一个`CFHTTPAuthentication`对象。

可用性

可以用在 OS X version 10.4 和以后版本


<br>**3.**[CFHTTPMessageAddAuthentication]()

添加请求信息的身份验证。

####演示
>SWIFT
>
>func CFHTTPMessageAddAuthentication(_ request: CFHTTPMessage, _ authenticationFailureResponse: CFHTTPMessage?, _ username: CFString, _ password: CFString, _ authenticationScheme: CFString?, _ forProxy: Bool) -> Bool
>
>OBJECTIVE-C
>
>Boolean CFHTTPMessageAddAuthentication ( CFHTTPMessageRef request, CFHTTPMessageRef authenticationFailureResponse, CFStringRef username, CFStringRef password, CFStringRef authenticationScheme, Boolean forProxy );


####参数
| request |添加的消息身份验证信息。|
|--:|:---|
| authenticationFailureResponse |响应消息,其中包含认证失败信息。|
| username |添加用户名到请求。|
| password |添加密码到请求。|
| authenticationScheme |使用的身份验证方案(`kCFHTTPAuthenticationSchemeBasic,kCFHTTPAuthenticationSchemeNegotiate、kCFHTTPAuthenticationSchemeNTLM或kCFHTTPAuthenticationSchemeDigest`),或通过零使用最强的支持authenticationFailureResponse参数提供的身份验证方案。|
| forProxy |标志指示是否添加身份验证数据是一个代理的使用(`TRUE`)或远程服务器的使用(`FALSE`)。如果`authenticationFailureResponse`参数提供的错误代码是407,`forProxy`设置为`TRUE`。如果错误代码是401,`forProxy`设置为`FALSE`。|


####返回值
如果身份验证信息成功地补充返回 TURE ,否则 FALSE.


####论述
这个函数添加身份验证信息指定的用户名、密码,`authenticationScheme`,`forProxy`参数指定的请求消息。消息被`authenticationFailureResponse`参数通常包含一个 401 或 407 错误代码。


这个函数是最适合发送一个请求到服务器。如果你需要发送多个请求,使用[CFHTTPMessageApplyCredentials]()。

可用性

可以用在 OS X version 10.1 和以后版本


##获取 CFHTTPMessage 类型标识符

[CFHTTPMessageGetTypeID]()

返回类型标识符是 `CFHTTPMessage` 类型的核心基础。

####演示
>SWIFT
>
>func CFHTTPMessageGetTypeID() -> CFTypeID


>OBJECTIVE-C
>
>CFTypeID CFHTTPMessageGetTypeID ( void );


####返回值

核心基础类型为`CFHTTPMessage`类型标识符。

可用性

可以用在 OS X version 10.1 和以后版本

##数据类型


[CFHTTPMessageRef]()

代表一个不透明类型的HTTP消息。

####演示
>SWIFT
>
>typealias CFHTTPMessageRef = CFHTTPMessage


>OBJECTIVE-C
>
>typedef struct __CFHTTPMessage *CFHTTPMessageRef;

可用性

可以用在 OS X version 10.1 和以后版本


##常量


**1.**[CFHTTP Version Constants]()

设置HTTP版本CFHTTPMessage请求或响应对象。

####演示
>SWIFT
>
>let kCFHTTPVersion1_0: CFString
>
>let kCFHTTPVersion1_1: CFString


>OBJECTIVE-C
>
>const CFStringRef kCFHTTPVersion1_0;
>
>const CFStringRef kCFHTTPVersion1_1;

####常量
* `kCFHTTPVersion1_0`

 指定HTTP 1.0版本。

　　
 用在OS X v10.1以后。
　　
　　

*  `kCFHTTPVersion1_1`　　

 指定HTTP 1.0版本。

　　
 用在OS X v10.1以后。
　　

####论述

使用 HTTP 版本常数当你调用[CFHTTPMessageCreateRequest]()[CFHTTPMessageCreateResponse]()创建一个请求或响应消息。


<br>**2.**[Authentication Schemes]()

常量用于指定所需的身份验证方案要求。

####演示
>SWIFT
>
>let kCFHTTPAuthenticationSchemeBasic: CFString
>
>let kCFHTTPAuthenticationSchemeDigest: CFString
>
>let kCFHTTPAuthenticationSchemeNTLM: CFString
>
>let kCFHTTPAuthenticationSchemeNegotiate: CFString
>
>let kCFHTTPAuthenticationSchemeKerberos: CFString
>
>let kCFHTTPAuthenticationSchemeNegotiate2: CFString
>
>let kCFHTTPAuthenticationSchemeOAuth1: CFString
>
>let kCFHTTPAuthenticationSchemeXMobileMeAuthToken: CFString



>OBJECTIVE-C
>
>extern const CFStringRef kCFHTTPAuthenticationSchemeBasic;
>
>extern const CFStringRef kCFHTTPAuthenticationSchemeDigest;
>
>extern const CFStringRef kCFHTTPAuthenticationSchemeNTLM;
>
>extern const CFStringRef kCFHTTPAuthenticationSchemeNegotiate;
>
>extern const CFStringRef kCFHTTPAuthenticationSchemeKerberos;
>
>extern const CFStringRef kCFHTTPAuthenticationSchemeNegotiate2;
>
>extern const CFStringRef kCFHTTPAuthenticationSchemeOAuth1;
>
>extern const CFStringRef kCFHTTPAuthenticationSchemeXMobileMeAuthToken;

####常量

* `kCFHTTPAuthenticationSchemeBasic`


  请求的HTTP基本身份验证方案。

　
　可用在OS X 10.2及以后版本。

* `kCFHTTPAuthenticationSchemeDigest`

  请求HTTP摘要认证方案。

　
　可用在OS X 10.2及以后版本。
　
　

* `kCFHTTPAuthenticationSchemeNTLM`

 请求的HTTP NTLM认证方案。

　
　可用在OS X 10.5及以后版本。
　

* `kCFHTTPAuthenticationSchemeNegotiate`

  请求的HTTP身份验证方案进行谈判。

　
　可用在OS X 10.5及以后版本。
　
　

 * `kCFHTTPAuthenticationSchemeKerberos`
 
   请求的HTTP Kerberos身份验证方案。

　
　可用在OS X 10.7及以后版本。
 
 
 * `kCFHTTPAuthenticationSchemeNegotiate2`

 
   请求的HTTP v2身份验证方案进行谈判。

　
　可用在OS X 10.6及以后版本。
　

* `kCFHTTPAuthenticationSchemeOAuth1`

  请求的HTTP 1.0 OAuth身份验证方案。

　
　可用在OS X 10.7及以后版本。
　

* `kCFHTTPAuthenticationSchemeXMobileMeAuthToken`
　
　
　    <br>请求的HTTP XMobileMeAuthToken认证方案。
　    
       <br>可以在OS X 10.6及以后版本。