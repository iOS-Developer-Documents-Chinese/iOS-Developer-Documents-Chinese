
#使用网络诊断

[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/UsingNetworkDiagnostics/UsingNetworkDiagnostics.html#//apple_ref/doc/uid/TP30001132-CH7-SW1)
翻译人:王谦  日期:2015.9.10

在许多基于 network-based 的应用程序中，可能发生基于 network-based 和应用程序无关的错误。然而,大多数用户可能不知道为什么一个应用程序发生错误。CFNetDiagnostics API 允许您用一种快速而简单的方法来帮助用户解决网络问题。


如果您的应用程序使用一个 CFStream 对象，然后创建一个网络诊断(`CFNetDiagnosticRef`)通过调用函数
[CFNetDiagnosticCreateWithStreams](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFNetDiagnosticsRef/index.html#//apple_ref/doc/c_ref/CFNetDiagnosticCreateWithStreams). [CFNetDiagnosticCreateWithStreams](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFNetDiagnosticsRef/index.html#//apple_ref/doc/c_ref/CFNetDiagnosticCreateWithStreams)来分配。
读取流,写流作为参数。如果您的应用程序只使用read stream或write stream,未使用的参数应设置为NULL。

您还可以创建一个网络诊断，如果没有流的存在那么直接从URL引用。要做到这一点,叫[CFNetDiagnosticCreateWithURL](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFNetDiagnosticsRef/index.html#//apple_ref/doc/c_ref/CFNetDiagnosticDiagnoseProblemInteractively)函数并将其传递给一个分配器,CFURLRef和URL。它将返回一个网络诊断用来做为参考。


诊断问题通过网络诊断助手,调用CFNetDiagnosticDiagnoseProblemInteractively函数和通过网络诊断做为参照。清单6显示了如何使用CFNetDiagnostics流上实现run loop。


**清单6-1使用cfnetdiagnostics API流时发生错误**


	case kCFStreamEventErrorOccurred:
	CFNetDiagnosticRef diagRef =
	CFNetDiagnosticCreateWithStreams(NULL, stream, NULL);
	(void)CFNetDiagnosticDiagnoseProblemInteractively(diagRef);
	CFStreamError error = CFReadStreamGetError(stream);
	reportError(error);
	CFReadStreamClose(stream);
	CFRelease(stream);
	break;


CFNetworkDiagnostics还使您能够检索问题,而不是使用网络的状态诊断助手。这是通过调用[CFNetDiagnosticCopyNetworkStatusPassively](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFNetDiagnosticsRef/index.html#//apple_ref/doc/c_ref/CFNetDiagnosticCopyNetworkStatusPassively)完成的,它返回一个常数值`kCFNetDiagnosticConnectionUp`或`kCFNetDiagnosticConnectionIndeterminate`等。