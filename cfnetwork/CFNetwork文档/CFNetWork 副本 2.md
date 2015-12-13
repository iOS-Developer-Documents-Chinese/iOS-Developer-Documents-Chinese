#介绍CFNetwork编程指南

[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/Introduction/Introduction.html#//apple_ref/doc/uid/TP30001132-CH1-DontLinkElementID_30)
翻译人:王谦 日期:2015.9.9



>重要:这是一个初步的文档的 API 或技术发展。苹果是提供这些信息帮助你计划采用的技术和编程接口介绍 Apple-branded 产品使用。这些信息变更,软件实现根据本文档应与最后的操作系统软件测试和最终的文档。新版本的文档可以提供未来的 API 或技术。


CFNetwork 是核心服务框架,框架提供了一个抽象库网络协议。这些抽象简化执行各种各样的网络任务,如:

* 使用BSD套接字

* 创建使用SSL和TLS加密连接

* 解决DNS主机

* 使用HTTP、HTTP和HTTPS服务器进行身份验证

* 使用FTP服务器

* 发布、解决和浏览Bonjour服务(在 [NSNetServices and CFNetServices Programming Guide](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/NSNetServiceProgGuide/Introduction.html#//apple_ref/doc/uid/TP40002736) 描述)

这本书是针对开发人员想要在他们的应用程序中使用的网络协议。为了完全理解这本书,读者应该有一个好的理解的网络编程概念,如 BSD 套接字流和 HTTP 协议。此外,读者应该熟悉 OS X 编程概念包括 run loops。关于 OS X 的更多信息请阅读 [Mac Technology Overview](https://developer.apple.com/library/prerelease/mac/documentation/MacOSX/Conceptual/OSX_Technology_Overview/About/About.html#//apple_ref/doc/uid/TP40001067)。


##本文的组织


这本书包含以下章节:

* [CFNetwork Concepts](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/Concepts/Concepts.html#//apple_ref/doc/uid/TP30001132-CH4-SW10)描述每个CFNetwork api,以及他们如何相互作用。
* [Working with Streams](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/CFStreamTasks/CFStreamTasks.html#//apple_ref/doc/uid/TP30001132-CH6-SW1) 描述如何使用 CFStream API 发送和接收网络数据。
* [Communicating with HTTP Servers](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/CFHTTPTasks/CFHTTPTasks.html#//apple_ref/doc/uid/TP30001132-CH5-SW2)描述如何发送和接收 HTTP 消息。
* [Communicating with Authenticating HTTP Servers](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/CFHTTPAuthenticationTasks/CFHTTPAuthenticationTasks.html#//apple_ref/doc/uid/TP30001132-CH8-SW1) 描述如何沟通与安全的 HTTP 服务器。
* [Working with FTP Servers](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/CFFTPTasks/CFFTPTasks.html#//apple_ref/doc/uid/TP30001132-CH9-SW1)描述如何从一个 FTP 服务器,上传和下载文件,如何下载目录列表。
* [Using Network Diagnostics](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/UsingNetworkDiagnostics/UsingNetworkDiagnostics.html#//apple_ref/doc/uid/TP30001132-CH7-SW1)描述如何为应用程序添加网络诊断。


##另请参阅

关于网络api的更多信息在OS X上,写着:

* 开始使用网络

请参考下面的参考文档CFNetwork:

* [CFFTPStream Reference](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFFTPStreamRef/index.html#//apple_ref/doc/uid/TP40003359) 是CFFTPStream API参考文档。
* [CFHTTPMessage Reference](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/uid/TP40003336)  是 CFHTTPMessage API 参考文档。
* [CFHTTPStream Reference ](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFHTTPStreamRef/index.html#//apple_ref/doc/uid/TP40003363)是 CFHTTPStream API 参考文档。
* [CFHTTPAuthentication Reference](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFHTTPAuthenticationRef/index.html#//apple_ref/doc/uid/TP40003335) 是CFHTTPAuthentication API 参考文档。
* [CFHost Reference](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFHostRef/index.html#//apple_ref/doc/uid/TP40003333)是CFHost API 参考文档。
* [CFNetServices Reference](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFNetServiceRef/index.html#//apple_ref/doc/uid/TP40003365) 是 CFNetServices API 参考文档。
* [CFNetDiagnostics Reference](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFNetDiagnosticsRef/index.html#//apple_ref/doc/uid/TP40003364)是 CFNetDiagnostics API 参考文档。

除了苹果提供的文档,下面是 socket-level 编程的参考书:

* UNIX网络编程,卷1(史蒂文斯,芬纳和Rudoff)










