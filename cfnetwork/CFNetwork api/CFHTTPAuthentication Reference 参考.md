#CFHTTPAuthentication 参考

[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFHTTPAuthenticationRef/index.html#//apple_ref/c/func/CFHTTPAuthenticationCreateFromResponse)
翻译人:王谦 日期:2015.9.13

>重要的

>这是一个API 的初步技术开发文档。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实施的软件，应与最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供未来的 API 或技术。



| 继承自        |   符合     |    导入语句     |
|   --------   |   --------:  | :----    |
| 不适用      | 不适用   |   @import CFNetwork       |
|          |       |    可用性 |
|          |      |  不适用 |

CFHTTPAuthentication 不透明类型提供一个抽象的 HTTP 身份验证信息。


##函数

创建一个HTTP身份验证


[CFHTTPAuthenticationCreateFromResponse]()

使用身份验证失败响应创建CFHTTPAuthentication对象。

####演示
>SWIFT
>
>func CFHTTPAuthenticationCreateFromResponse(_ alloc: CFAllocator?, _ response: CFHTTPMessage) -> Unmanaged < CFHTTPAuthentication >

>OBJECTIVE-C
>
>CFHTTPAuthenticationRef CFHTTPAuthenticationCreateFromResponse ( CFAllocatorRef alloc, CFHTTPMessageRef response );


####参数

| alloc |使用分配器为新对象分配内存。通过 `NULL` 或`kCFAllocatorDefault`使用当前默认的分配器。|
|---:|:---|
| response |反应表明身份验证失败;通常是 401 或 407 响应。|


####返回值

CFHTTPAuthentication 对象可用于添加将来的请求
凭证。请参考 [Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。


####论述
这个函数使用一个响应包含认证失败信息创建一个引用 CFHTTPAuthentication 对象。您可以使用对象将证书添加到将来的请求。你可以查询对象获取以下信息:

* 是否可以使用和重用与相应的服务器进行身份验证

 [CFHTTPAuthenticationIsValid]()

* 将使用的身份验证方法是用于执行身份验证时

 [CFHTTPAuthenticationCopyMethod]()

* 是否与特定 CFHTTPMessageRef 相关联

 [CFHTTPAuthenticationAppliesToRequest]()

* 是否需要用户名和密码时,它是用来执行身份验证

 [CFHTTPAuthenticationRequiresUserNameAndPassword]()

* 是否需要一个帐户域用来执行身份验证

 [CFHTTPAuthenticationRequiresAccountDomain]()

* 身份验证请求是否应该发送一次到相应的服务器

 [CFHTTPAuthenticationRequiresOrderedRequests]()

* 使用域的名称空间(如果有的话)提示输入名字和密码

 [CFHTTPAuthenticationCopyRealm]()

* 适用于域url实例 

 [CFHTTPAuthenticationCopyDomains]()

当你已经确定需要什么信息来执行身份验证和积累信息,调用  [CFHTTPMessageApplyCredentials]() 或  [CFHTTPMessageApplyCredentialDictionary]() 执行身份验证。

####可用性

可用在 OS X 10.4版本以后

##CFHTTP身份验证函数

本节描述 CFNetwork 身份验证功能用于管理身份验证信息与请求相关联。功能与 CFHTTPAuthentication 对象,这从 HTTP 响应创建失败 401 或 407 错误代码。

当你分析了CFHTTPAuthentication对象并获得必要的凭证执行身份验证,你可以调用 [CFHTTPMessageApplyCredentials]() 或 [CFHTTPMessageApplyCredentialDictionary]()执行身份验证。



**1.**[CFHTTPAuthenticationAppliesToRequest]()

　　返回一个布尔值,用于显示 CFHTTPAuthentication 对象与 CFHTTPMessage 对象相关联。
　　
####演示
>SWIFT
>
>func CFHTTPAuthenticationAppliesToRequest(_ auth: CFHTTPAuthentication, _ request: CFHTTPMessage) -> Bool
>
>OBJECTIVE-C
>
>Boolean CFHTTPAuthenticationAppliesToRequest ( CFHTTPAuthenticationRef auth, CFHTTPMessageRef request );


####参数

| auth |CFHTTPAuthentication 对象检查。|
|---:|:---|
| request |要求身份验证测试。|


####返回值

如果身份验证与请求相关联返回 TURE ,否则 FALSE。
　　
####论述
如果函数返回 TURE,您可以使用身份验证使用请求时提供的身份验证信息。

####可用性

可用在 OS X v10.2和以后

<br>**2.** [CFHTTPAuthenticationCopyDomains]()

返回一个数组给定的域url CFHTTPAuthentication对象可以应用。

####演示
>SWIFT
>
>func CFHTTPAuthenticationCopyDomains(_ auth: CFHTTPAuthentication) -> Unmanaged < CFArray >

>OBJECTIVE-C
>
>CFArrayRef CFHTTPAuthenticationCopyDomains ( CFHTTPAuthenticationRef auth );


####参数
| auth |CFHTTPAuthentication对象检查。|
|---:|:---|

####返回值

CFArray 对象包含域身份验证应该应用 URL。请参考[Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

####论述

这个函数提供仅作参考之用。

####可用性

可用在 OS X10.4 和以后版本

<br>**3.**[CFHTTPAuthenticationCopyMethod]()

得到最强大的身份验证方法时,将使用一个 CFHTTPAuthentication 对象应用于一个请求。

####演示

>SWIFT
>
>func CFHTTPAuthenticationCopyMethod(_ auth: CFHTTPAuthentication) -> Unmanaged < CFString >


>OBJECTIVE-C
>
>CFStringRef CFHTTPAuthenticationCopyMethod ( CFHTTPAuthenticationRef auth );


####参数
| auth |检查 CFHTTPAuthentication 对象。|
|---:|:---|

####返回值

一个字符串包含身份验证使用的身份验证方法是应用于一个请求。
如果不止一个身份验证方法那么可以用,返回最强大的身份验证方法。请参考 [Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。


####可用性

可用在 OS X 10.4 版本以后



 <br>**4.**[CFHTTPAuthenticationCopyRealm]()
 
 得到一个身份验证信息的名称空间。
 
 
####演示
>SWIFT
>
>func CFHTTPAuthenticationCopyRealm(_ auth: CFHTTPAuthentication) -> Unmanaged < CFString > 

>OBJECTIVE-C
>
>CFStringRef CFHTTPAuthenticationCopyRealm ( CFHTTPAuthenticationRef auth );


####参数

| auth |检查 CFHTTPAuthentication  对象。|
|---:|:---|

####返回值

命名空间，如果有的话。否则返回 NULL.请参考 [Create Rule](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Conceptual/CFMemoryMgmt/Concepts/Ownership.html#//apple_ref/doc/uid/20001148-103029)。

####论述

一些身份验证方法提供一个命名空间,它通常是用来提示用户名和密码。

####可用性

可用在 OS X 10.4 版本以后

<br>**5.**[CFHTTPAuthenticationIsValid]()

返回一个布尔值,表示 CFHTTPAuthentication 对象是否有效。


####演示
>SWIFT
>
>func CFHTTPAuthenticationIsValid(_ auth: CFHTTPAuthentication, _ error: UnsafeMutablePointer < CFStreamError > ) -> Bool

>OBJECTIVE-C
>
>Boolean CFHTTPAuthenticationIsValid ( CFHTTPAuthenticationRef auth, CFStreamError *error );

####参数

| auth |检查 CFHTTPAuthentication  对象。|
|---:|:---|
| error |一个[CFStreamError](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFStreamConstants/index.html#//apple_ref/c/tdef/CFStreamError)结构域，其指针，如果出现了一个错误。|

####返回值

如果身份验证包含足够的信息应用到一个请求就返回 TURE 。

如果这个函数返回 FALSE ,CFHTTPAuthentication 对象仍可能包含有用的信息,比如一个不受支持的身份验证方法的名称。


####论述

如果这个函数返回的身份验证为 TURE,对象是适合使用 [CFHTTPMessageApplyCredentials](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/c/func/CFHTTPMessageApplyCredentials) 和 [CFHTTPMessageApplyCredentialDictionary](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/c/func/CFHTTPMessageApplyCredentialDictionary) 等函数。如果这个函数返回 FALSE ,身份验证是无效的,并使用它不会认证成功。


####可用性

可用在 OS X 10.4 和以后版本


<br>**6.** [CFHTTPAuthenticationRequiresAccountDomain]()

返回一个布尔值,用于显示 CFHTTPAuthentication 对象使用身份验证方法,需要一个帐户域。

####演示
>SWIFT
>
>func CFHTTPAuthenticationRequiresAccountDomain(_ auth: CFHTTPAuthentication) -> Bool

>OBJECTIVE-C
>
>Boolean CFHTTPAuthenticationRequiresAccountDomain ( CFHTTPAuthenticationRef auth );

####参数

| auth |检查 CFHTTPAuthentication 对象。|
|---:|:---|

####返回值

如果身份验证使用身份验证方法,需要一个帐户域。如果返回 TURE 表示成功,否则 返回 FALSE. 

####可用性

 可用在 OS X 10.4 版本以后
 
 <br>**7.**[CFHTTPAuthenticationRequiresOrderedRequests]()
 
 返回一个布尔值,用于请求一次显示身份验证。
 
####演示

>SWIFT
> 
> func CFHTTPAuthenticationRequiresOrderedRequests(_ auth: CFHTTPAuthentication) -> Bool

>OBJECTIVE-C
>
>
>Boolean CFHTTPAuthenticationRequiresOrderedRequests ( CFHTTPAuthenticationRef auth );


####参数

| auth |检查 CFHTTPAuthentication  对象。|
|---:|:----|


####返回值

如果身份验证需要命令请求，返回值为 TURE。否则为 FALSE。

####论述

有些认证方法要求未来的请求必须以有序的方式执行。如果这个函数返回 TRUE ,客户端可以提高他们的身份验证成功的机会通过发出请求一次作为响应从服务器回来。

####可用性

可用在 OS X 10.4 版本和以后


<br>**8.** [CFHTTPAuthenticationRequiresUserNameAndPassword]()

返回一个布尔值,用于显示 CFHTTPAuthentication 对象使用身份验证方法,需要用户名和密码。

####演示

>SWIFT
>
>func CFHTTPAuthenticationRequiresUserNameAndPassword(_ auth: CFHTTPAuthentication) -> Bool


>OBJECTIVE-C
>
>Boolean CFHTTPAuthenticationRequiresUserNameAndPassword ( CFHTTPAuthenticationRef auth );


####参数
| auth | 检查 CFHTTPAuthentication 对象。|
|--:|:---|

####返回值

如果应用的身份验证需要用户名和密码时请求,成功返回TURE,否则 返回FALSE。

####可用性

可用在 OS X 10.4 版本和以后


##CFHTTPAuthentication类型ID


[CFHTTPAuthenticationGetTypeID]()

得到的核心基础类型 CFHTTPAuthentication 不透明类型的标识。

####演示
>SWIFT
>
>func CFHTTPAuthenticationGetTypeID() -> CFTypeID

>OBJECTIVE-C
>
>CFTypeID CFHTTPAuthenticationGetTypeID ( void );


####返回值

核心基础类型为 CFHTTPAuthentication 不透明类型标识符。

####可用性

可用在 OS X 10.4 版本和以后


##数据类型


[CFHTTPAuthenticationRef]()

一个不透明的参考代表 HTTP 身份验证信息。

####演示
>SWIFT
>
>typealias CFHTTPAuthenticationRef = CFHTTPAuthentication

>OBJECTIVE-C
>
>typedef struct __CFHTTPAuthentication *CFHTTPAuthenticationRef;

####可用性

可用在 OS X 10.4 版本和以后


##常量


**1.**[CFHTTP Authentication Scheme Constants]()

指定身份验证方案添加身份验证信息时的 CFHTTP 请求消息对象。

####演示
>SWIFT
>
>let kCFHTTPAuthenticationSchemeBasic: CFString
>
>let kCFHTTPAuthenticationSchemeDigest: CFString
>
>let kCFHTTPAuthenticationSchemeNegotiate: CFString
>
>let kCFHTTPAuthenticationSchemeNTLM: CFString


><br>OBJECTIVE-C
>
>const CFStringRef kCFHTTPAuthenticationSchemeBasic;
>
>const CFStringRef kCFHTTPAuthenticationSchemeDigest;
>
>const CFStringRef kCFHTTPAuthenticationSchemeNegotiate;
>
>const CFStringRef kCFHTTPAuthenticationSchemeNTLM;

####常量

* [kCFHTTPAuthenticationSchemeBasic]()

 指定基本身份验证的用户名和密码。

 可用在 OS X v10.2 和以后

* [kCFHTTPAuthenticationSchemeDigest]()

 保留。

 可用在 OS X v10.2 和以后

* [kCFHTTPAuthenticationSchemeNegotiate]()

 指定身份验证方案进行谈判。

 可用在 OS X v10.5 和以后

* [kCFHTTPAuthenticationSchemeNTLM]()

 指定NTLM认证方案。

 可用在 OS X v10.5 和以后

####论述

身份验证方案常量用于指定当调用身份验证方案时调用[CFHTTPMessageAddAuthentication](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/c/func/CFHTTPMessageAddAuthentication)。

<br>**2.**[CFStreamErrorHTTPAuthentication]()

身份验证错误代码可能试图应用身份验证请求时返回。

####演示
>SWIFT
>
>enum CFStreamErrorHTTPAuthentication : Int32 {
    case TypeUnsupported
    case BadUserName
    case BadPassword
}

>OBJECTIVE-C
>
>enum CFStreamErrorHTTPAuthentication {
   kCFStreamErrorHTTPAuthenticationTypeUnsupported = -1000,
   kCFStreamErrorHTTPAuthenticationBadUserName = -1001,
   kCFStreamErrorHTTPAuthenticationBadPassword = -1002
};
typedef enum CFStreamErrorHTTPAuthentication CFStreamErrorHTTPAuthentication;


####常量

* [kCFStreamErrorHTTPAuthenticationTypeUnsupported]()

 不支持指定的身份验证类型。

 可用在 OS X v10.4 和以后

* [kCFStreamErrorHTTPAuthenticationBadUserName]()

 用户名格式不适合请求。目前,用户名使用kCFStringEncodingISOLatin1 解码。

 可用在 OS X v10.4 和以后

* [kCFStreamErrorHTTPAuthenticationBadPassword]()

 密码格式不适合请求。目前,使用 kCFStringEncodingISOLatin1 解码。

 可用在 OS X v10.4 和以后


####重要声明

>OBJECTIVE-C
>
>@import CFNetwork;

>SWIFT
>
>import CFNetwork


####可用性

可用在 OS X v10.4 和以后



<br>**3.**[CFHTTPMessageApplyCredentialDictionary Keys]()

常量传递到键的字典 [CFHTTPMessageApplyCredentialDictionary](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/c/func/CFHTTPMessageApplyCredentialDictionary)。


####演示
>SWIFT
>
>let kCFHTTPAuthenticationUsername: CFString
>let kCFHTTPAuthenticationPassword: CFString
>let kCFHTTPAuthenticationAccountDomain: CFString

>OBJECTIVE-C
>
>const CFStringRef kCFHTTPAuthenticationUsername;
>
>const CFStringRef kCFHTTPAuthenticationPassword;
>
>const CFStringRef kCFHTTPAuthenticationAccountDomain;

####常量
* [kCFHTTPAuthenticationUsername]()

 用户名用于身份验证。

 可用在 OS X v10.4和以后

* [kCFHTTPAuthenticationPassword]()

 密码用于身份验证。

 可用在 OS X v10.4和以后


* [kCFHTTPAuthenticationAccountDomain]()

 帐户域用于身份验证。

 可用在 OS X v10.4和以后