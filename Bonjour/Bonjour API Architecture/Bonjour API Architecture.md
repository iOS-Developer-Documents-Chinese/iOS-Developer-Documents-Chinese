#Bonjour API 结构

[原文地址](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/NetServices/Articles/programming.html#//apple_ref/doc/uid/TP40002459-SW1) 

 翻译人:王谦 翻译日期:2015.10.29
 
 OS X 和 iOS 提供应用程序的几个接口(API)层为 Bonjour 的应用程序服务;在 Foundation framework 中的 NSNetService 和 NSNetServiceBrowser 类。CFNetServices,在 Core Service  CFNetwork framework 中的一部分; DNS Service Discovery Java (OS X only);和建立在 BSD sockets 的低级 DNS Service Discovery API。发表提供设置所有三个 API 的条件, discovery,和网络解析服务。插图 3-1 举例说明 API 层的组织结构。正如您能看到的,组播 DNS 响应(或者其他 DNS server)处于最低水平,所以你的软件没有直接与 DNS 进行交互。
 

插图 3-1 Bonjour 网络服务的 API 层

![rendarch_04apilayers_2x.png](./rendarch_04apilayers_2x.png)


##NSNetService and NSNetServiceBrowser

`NSNetService` 和 `NSNetServiceBrowser`类,在 Cocoa Foundation framework 部分,提供面向对象的抽象服务发现和发布。`NSNetService` 对象代表  Bonjour services 的实例,通过一个客户端任何一个发布或服务发现,`NSNetServiceBrowser` 代表一个浏览器特定类型的服务。大部分 Cocoa 程序应该发现这些类,满足它们的需要。如果你需要更复杂的控制,你可以从 Cocoa 应用程序中 使用 DNS Service Discovery API。