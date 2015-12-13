#Bonjour 常见问题

[原文地址](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/NetServices/Articles/faq.html#//apple_ref/doc/uid/TP40008762-SW1) 

 翻译人:王谦 翻译日期:2015.9.10
 
 
 这个附录描述关于 Bonjour 频繁被问的问题。
 
##1.什么是 Bonjour ?
 
 Bonjour,也被称为零配置网络,使计算机自动发现 IP 网络设备和服务。Bonjour 使用行业标准的 IP 协议允许设备自动发现对方而不需要输入 IP 地址或配置 DNS 服务器。具体地说, Bonjour 使没有 DHCP 服务器自动分配 IP 地址,没有名字 DNS 服务器地址转换,没有目录服务器的发现服务。Bonjour 是一个开放的协议,苹果已经提交到 IETF 持续standards-creation 过程的一部分。为了了解更多,检查[Bonjour Protocol Specifications]详细的技术构成链接和广域 Bonjour 。
 
 [Bonjour Protocol Specifications]:
 http://developer.apple.com/networking/bonjour/specs.html
##2.什么是 mDNSResponder ?

mDNSResponder 是一个 Bonjour 系统服务,实现多路广播 DNS 服务发现服务在本地网络的发现,和单播 DNS 服务发现的发现服务在世界任何地方。mDNSResponder 是 OS X 和 iOS 和可以下载作为Bonjour Windows 的一部分。应用程序像 iTunes 一样, iPhoto ,消息和 Safari 使用 mDNSResponder 实现零配置网络音乐分享、照片分享,聊天和文件共享,发现远程用户接口硬件设备如打印机和网络摄像头。mDNSResponder 还用于发现 Bonjour 打印机和 USB 打印机和打印极端和表达基站连接到机场。mDNSResponder 是开源的,和硬件设备制造商鼓励 [mDNSResponder source code]直接嵌入到他们的产品从零配置网络中获益。

[mDNSResponder source code]:
http://developer.apple.com/opensource


##3. Bonjour 在多个子网之间工作吗?

是的。DNS 服务发现的第一个版本( DNS-SD ) OS X 集中在单键网络多路广播 DNS(mdn) 因为这是环境最差的 IP 软件。你好,使用动态域名更新( RFC 2316 )和单播 DNS 查询,使广域服务发现。

##4.当我从网络断开设备,仍可见吗?

是的,一段时间。最终,DNS 记录达到生存时间间隔和消失。作为应用程序开发人员,如果你使用 Bonjour 连接到主机,连接失败,您可以问 Bonjour 确认备案。这个过程进一步在[NSNetServices and CFNetServices Programming Guide]中描述。

[NSNetServices and CFNetServices Programming Guide]:
https://developer.apple.com/library/ios/documentation/Networking/Conceptual/NSNetServiceProgGuide/Introduction.html#//apple_ref/doc/uid/TP40002736


##5.我必须做些什么来支持 iOS Bonjour 在蓝牙吗?

在iOS 5之后,应用程序必须明确地选择做发现服务蓝牙,必须使用低级别的 DNS 服务发现 C API 解决服务。更多信息, 看[Bonjour over Bluetooth on iOS 5 and Later]。

[Bonjour over Bluetooth on iOS 5 and Later]:
https://developer.apple.com/library/ios/qa/qa1753/_index.html#//apple_ref/doc/uid/DTS40011315


##6.我应该离开服务浏览器运行多长时间?

浏览器消耗资源,所以你不应该让他们运行,如果你不希望使用数据。然而,保持服务浏览器运行时连接到服务通常是一个好主意。如果连接失败,存在运行浏览器更积极地鼓励 Bonjour 重新验证潜在失效服务条目,使服务更精确的列表。

作为一个规则,如果你不显示任何包含列表的用户界面元素,如果你不积极地连接到任何服务,你应该阻止浏览器。然而,这只是一个一般的建议;在所有情况下,你应该做任何导致最好的用户体验。


##7. Bonjour,是否支持“SOAP”通过 HTTP RPC ?

是的。Bonjour 发现服务定义了一个新的协议( DNS-SD ),然而,这地方没有限制服务发现的类型。因此你会发现 SOAP 服务一样轻松地你会发现朋友和 iTunes 音乐库的消息。换句话说,Bonjour 支持 HTTP 上的 SOAP 以及其他应用协议层上的 TCP / IP 或 UDP / IP。

##8. Bonjour 有任何类型的订阅或通知机制?

是的。原因是很多人似乎不知道 Bonjour 也通知可能是因为它只是发现协议的一种内在属性。与一个设计良好的发现协议,同样的协议,用来发现一些信息还用于发现信息的变化。发现静态信息和发现的变量信息,发现当变量信息的变化都是不同的点在同一光谱。的一个示例应用程序使用 Bonjour “通知”,查看消息。当你改变你的状态从“可用”到“开”或输入状态信息,通知所有其他消息客户端在本地网络上的变化。

这一技术进一步描述在[NSNetServices and CFNetServices Programming Guide]。


##9.我应该通过 “name” 参数的注册服务吗?

默认情况下,你应该选择一个人类可读的名字,唯一地描述服务。iTunes ,例如,选择一个默认的音乐共享名称结合计算机用户的第一个和最后一个名字,如“艾萨克·牛顿的音乐”。对于大多数硬件设备,默认的服务名称应该是完整的产品的型号。例如,就像“苹果 MacBook Pro ”。记住,这只是默认名字的盒子,应该允许和用户定制服务名称来区分多个设备或服务网络。

OS X 应用程序开发人员注册服务,也许是有意义的一个实例,对给定的计算机服务(而不是每一个实例的应用程序可以运行在多个帐户)。在这种情况下,而不是在您的应用程序现在自己的用户界面为用户输入的名称的广告服务,更方便的使用系统提供的默认名称注册称为“计算机名”分享首选项面板。如果你传递一个空字符串(" ")服务名称注册时,系统会自动使用“计算机名”。传递一个空字符串也会自动添加一个数字处理名称冲突的名称。

然而,有服务的多个实例可以位于同一台计算机上。例如,一个有三个打印机的打印服务器应该通知每个打印机作为一个一流的实体。每个打印机应该通知使用描述性名称,有效标识打印机本身。这很重要,因为打印机叫做“营销透明度打印机”在未来可能会搬到一个不同的打印服务器,但是用户不应该注意那些细节。他们还应该看到相同的服务网络上的广告在相同的名称,即使现在是在一个不同的打印服务器。

##10.我应该通过什么“ type ”参数当注册一个服务?

你必须通过一个字符串的形式`“_applicationprotocol._transportprotocol”`。目前`“_transportprotocol”`必须`“_tcp”`或`“_udp”`。你的`“applicationprotocol”`必须小于15个字符,应该在 IANA注册所以他们可以添加你的注册 [protocol names and port numbers]。请参阅[QA1312]为 OS X 所使用的服务类型的列表。

[QA1312]:
http://developer.apple.com/qa/qa2001/qa1312.html

[protocol names and port numbers]:
http://www.iana.org/assignments/port-numbers


##11.我应该通过在“domain”参数的注册服务吗?

如果你传递一个空字符串(" "),然后使用链接您的服务注册在user-chosen多播和单播DNS域,如果适用的话。

如果你在`“local”`,那么你的服务注册只使用链接多播,而不是在任何user-chosen 单播 DNS 域。

除了`“local”`领域,你只需要通过一个特定的字符串如果你有特别的理由想要注册你的服务在一个特定的远程域。

##12.应该发生在网络上的两台设备都使用同一个服务的名字吗?

在罕见的情况下发生名称冲突,你的设备应该添加一个数字的名称,例如:

"Apple Mac mini (2)"

应用程序和设备调用 Bonjour api DNSServiceRegister 和 CFNetServiceRegisterWithOptions 将自动获得名称冲突发生时这个名字改变人们的行为。设备有一个屏幕,可以用户输入,而不是添加一个数字,你可以选择决定提示用户输入惟一名称。


###13.TXT记录用于什么?

三种记录的具体性质,以及它是如何使用是依赖于服务类型的。每个服务类型将定义零个或多个名称/值对用于存储元数据对每个服务。这些名称/值对应该格式化第六节中描述[DNS-Based Service Discovery]。请参阅 [QA1306] 信息应该如何格式化TXT记录时使用OS X和iOS api。也,[DNS-SD Web Site]当前定义为公共服务类型名称/值对。

[QA1306]:
http://developer.apple.com/qa/qa2001/qa1306.html

[DNS-Based Service Discovery]:
http://files.dns-sd.org/draft-cheshire-dnsext-dns-sd.txt

[DNS-SD Web Site]:
http://www.dns-sd.org/ServiceTypes.html


##14.之后在我的应用程序中用户浏览网络并选择他们想要使用的服务实例,我应该保存 IP 地址在我的应用程序的首选项文件,对吗?

错了。这是一个常见的错误。DHCP (和链接地址)是不安全的假定服务实例明天将有相同的IP地址。可以改变地址。服务名称是预期的稳定服务实例标识符。保存实例名称(名称、类型和域)在应用程序的首选项文件,然后解决它在每次用户访问服务的需求。还请注意,您不应该存储主机名和端口号,因为你不应该假定服务实例明天一定会运行在相同的端口号。而不是存储主机名、存储服务实例的名称(名称、类型和域),然后当你解决服务实例名称的时候使用你肯定会得到最新的 IP 地址和端口号。

##15.我的硬件设备有一个内置的 web 服务器用于配置。我应该注册使用 Bonjour 吗?

是的。你应该注册每一个服务运行在你的设备,例如,HTTP、FTP、SSH、Telnet。在OS X上,Safari浏览器可以发现web服务器与Bonjour广告,和Internet Explorer在Windows可以发现当 [Bonjour for Windows] 安装 web 服务器。同样,在 OS X 终端应用程序可以发现FTP,SSH 和 Telnet 服务器。

[Bonjour for Windows]:
https://developer.apple.com/downloads/index.action?q=Bonjour%20SDK%20for%20Windows