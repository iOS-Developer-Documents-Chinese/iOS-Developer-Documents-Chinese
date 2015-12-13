


#与HTTP服务器通信

[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/CFHTTPTasks/CFHTTPTasks.html#//apple_ref/doc/uid/TP30001132-CH5-SW2)
翻译人:王谦 日期:2015.9.9

本章解释了如何创建、发送和接收HTTP请求和响应。


##创建一个CFHTTP请求


组成一个 HTTP 请求消息的远程服务器执行的方法,对象操作( URL ),消息头和消息体。方法通常是下列之一: GET, HEAD, PUT, POST, DELETE, TRACE, CONNECT or OPTIONS。创建一个 HTTP 请求与 CFHTTP 需要四个步骤:


1.使用`CFHTTPMessageCreateRequest`函数生成一个CFHTTP消息对象。

2.使用函数`CFHTTPMessageSetBody`设置消息的主体。

3.设置消息的标题使用`CFHTTPMessageSetHeaderFieldValue`函数。

4.`CFHTTPMessageCopySerializedMessage`通过调用函数序列化消息。

示例代码看起来就像清单3中的代码。

**Listing 3-1  创建一个 HTTP 请求**

	CFStringRef bodyString = CFSTR(""); // Usually used for POST data
	CFDataRef bodyData = CFStringCreateExternalRepresentation(kCFAllocatorDefault,
	bodyString, kCFStringEncodingUTF8, 0);
	
	CFStringRef headerFieldName = CFSTR("X-My-Favorite-Field");
	CFStringRef headerFieldValue = CFSTR("Dreams");
	
	CFStringRef url = CFSTR("http://www.apple.com");
	CFURLRef myURL = CFURLCreateWithString(kCFAllocatorDefault, url, NULL);
	
	CFStringRef requestMethod = CFSTR("GET");
	CFHTTPMessageRef myRequest =
	CFHTTPMessageCreateRequest(kCFAllocatorDefault, requestMethod, myURL,
	kCFHTTPVersion1_1);
	
	CFDataRef bodyDataExt = CFStringCreateExternalRepresentation(kCFAllocatorDefault, bodyData, kCFStringEncodingUTF8, 0);
	CFHTTPMessageSetBody(myRequest, bodyDataExt);
	CFHTTPMessageSetHeaderFieldValue(myRequest, headerFieldName, headerFieldValue);
	CFDataRef mySerializedRequest = CFHTTPMessageCopySerializedMessage(myRequest);

这个示例代码, url 是通过调用[CFURLCreateWithString](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFURLRef/index.html#//apple_ref/doc/c_ref/CFURLCreateWithString)首先转换成一个 CFURL 对象。然后用四个参数:[CFHTTPMessageCreateRequest](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageCreateRequest)叫做`kCFAllocatorDefault`指定默认的系统内存分配器是用于创建消息引用,`requestMethod`指定方法,如 POST 方法,`myURL`指定 URL ,如`http://www.apple.com`,`kCFHTTPVersion1_1`指定消息的HTTP版本是1.1。


返回的消息对象引用(`myRequest`)[CFHTTPMessageCreateRequest](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageCreateRequest)然后送到[CFHTTPMessageSetBody](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageSetBody)随着消息的主体(`bodyData`)。然后调用[CFHTTPMessageSetHeaderFieldValue](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageSetHeaderFieldValue)使用相同的消息对象引用的名字一起头(`headerField`)和价值(`value`)。头参数是CFString对象的`Content-Length`,和值参数是 CFString 对象如 1260 。最后,通过调用[CFHTTPMessageCopySerializedMessage](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageCopySerializedMessage)消息序列化,应该通过写流发送到接收者,在这个例子中`http://www.apple.com`。



>注意:请求主体通常省略。主要将使用请求主体是一个 POST 请求包含 POST 数据。它也可以用于其他 WebDAV 等有关 HTTP 请求类型的扩展。有关更多信息,请参见RFC 2616



当消息不再需要时,释放消息序列化对象和消息。参见清单3的示例代码

Listing 3-2  释放一个 HTTP 请求


	CFRelease(myRequest);
	CFRelease(myURL);
	CFRelease(url);
	CFRelease(mySerializedRequest);
	myRequest = NULL;
	mySerializedRequest = NULL;


##创建一个CFHTTP响应



用创建一个HTTP响应完全相同的步骤创建一个HTTP请求。唯一的区别是,并不是调用[CFHTTPMessageCreateRequest](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageCreateRequest),你调用函数[CFHTTPMessageCreateResponse](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageCreateResponse)使用相同的参数。




##反序列化传入HTTP请求


反序列化传入的 HTTP 请求,创建一个空的消息使用[CFHTTPMessageCreateEmpty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageCreateEmpty)函数,传递准确的`isRequest`参数来指定要创建一个空的请求消息。然后使用函数[CFHTTPMessageAppendBytes](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageAppendBytes)将传入消息添加到空的请求消息里。[CFHTTPMessageAppendBytes](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageAppendBytes)反序列化消息并移除任何可能包含的控制信息。



继续这样做,直到函数[CFHTTPMessageIsHeaderComplete](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageIsHeaderComplete)返回TRUE。如果你不检查[CFHTTPMessageIsHeaderComplete](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageIsHeaderComplete)返回 TRUE ,消息可能是不完整的和不可靠的。一个示例使用清单3 - 3中可以看到这两个函数。

**Listing 3-3  序列化一个消息**

	CFHTTPMessageRef myMessage = CFHTTPMessageCreateEmpty(kCFAllocatorDefault, TRUE);
	if (!CFHTTPMessageAppendBytes(myMessage, &data, numBytes)) {
	//Handle parsing error
	}




在这个示例中,数据是附加的数据和 `numBytes`数据的长度。你可能想调用[CFHTTPMessageIsHeaderComplete](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageIsHeaderComplete)附加消息的验证头就完成了。


	
	if (CFHTTPMessageIsHeaderComplete(myMessage)) {
	// Perform processing.
	}



反序列化的消息,您现在可以从以下函数提取信息:

* `CFHTTPMessageCopyBody`消息正文的一个副本

* `CFHTTPMessageCopyHeaderFieldValue`获得特定的header字段值的副本
* `CFHTTPMessageCopyAllHeaderFields`拿到一份所有消息的头字段
* `CFHTTPMessageCopyRequestURL`的消息副本的URL
* `CFHTTPMessageCopyRequestMethod`得到消息的请求方法的副本



当你不再需要信息,就释放，正确的处理它。





##反序列化传入HTTP响应

就像创建一个HTTP请求非常类似于创建一个HTTP响应,反序列化传入HTTP请求也非常类似于反序列化传入的HTTP响应。唯一重要的区别是,当调用[CFHTTPMessageCreateEmpty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageCreateEmpty),你必须通过 isRequest 参数指定要创建的消息是一个响应消息

##使用读取流序列化并发送HTTP请求


您可以使用一个CFReadStream对象序列化和发送CFHTTP请求。当你使用CFReadStream CFHTTP请求对象发送,打开流导致消息序列化和发送在一个步骤。使用CFReadStream对象发送CFHTTP请求让我们更加容易的请求的响应,因为响应是可用属性的流。



###序列化并发送一个HTTP请求

使用 CFReadStream 对象序列化并发送一个 HTTP 请求,首先创建一个 CFHTTP 请求和设置消息正文和标题中描述[Creating a CFHTTP Request]()。然后创建一个 CFReadStream 对象通过调用函数 [CFReadStreamCreateForHTTPRequest](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFHTTPStreamRef/index.html#//apple_ref/doc/c_ref/CFReadStreamCreateForHTTPRequest) 和通过请求您刚刚创建的。最后,用 [CFReadStreamOpen](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/doc/c_ref/CFReadStreamOpen) 打开读取流。


当 [CFReadStreamCreateForHTTPRequest](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFHTTPStreamRef/index.html#//apple_ref/doc/c_ref/CFReadStreamCreateForHTTPRequest) 被调用时,它通过 CFHTTP 复制请求对象。因此,如果有必要,你可以调用 [CFReadStreamCreateForHTTPRequest](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFMessageRef/index.html#//apple_ref/doc/c_ref/CFHTTPMessageIsHeaderComplete) 后立即释放 CFHTTP 请求对象。



因为 read stream 与服务器打开一个 socket 连接指定的 myUrl 参数 CFHTTP 请求创建时,一段时间前必须允许通过流被认为是开放的。打开 read stream 也让请求序列化和发送。



**Listing 3-4  读取流序列化一个HTTP请求**
	
	CFStringRef url = CFSTR("http://www.apple.com");
	CFURLRef myURL = CFURLCreateWithString(kCFAllocatorDefault, url, NULL);
	CFStringRef requestMethod = CFSTR("GET");
	
	CFHTTPMessageRef myRequest = CFHTTPMessageCreateRequest(kCFAllocatorDefault,
	requestMethod, myUrl, kCFHTTPVersion1_1);
	CFHTTPMessageSetBody(myRequest, bodyData);
	CFHTTPMessageSetHeaderFieldValue(myRequest, headerField, value);
	
	CFReadStreamRef myReadStream = CFReadStreamCreateForHTTPRequest(kCFAllocatorDefault, myRequest);
	
	CFReadStreamOpen(myReadStream);



###检查响应

调度的请求 run loop 之后,你最终会得到一个头完成回调。在这一点上,你可以叫[CFReadStreamCopyProperty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamCopyProperty)阅读得到的消息响应流


	CFHTTPMessageRef myResponse = (CFHTTPMessageRef)CFReadStreamCopyProperty(myReadStream, kCFStreamPropertyHTTPResponseHeader);

你可以得到完整的状态行CFHTTPMessageCopyResponseStatusLine响应消息通过调用函数:

CFStringRef myStatusLine = CFHTTPMessageCopyResponseStatusLine(myResponse);

或者只是响应消息的状态代码`CFHTTPMessageGetResponseStatusCode`通过调用函数

	UInt32 myErrCode = CFHTTPMessageGetResponseStatusCode(myResponse);


>注意:如果您正在使用这个类同步(没有调用run loop),你必须开始阅读消息通过至少一个调用[CFReadStreamRead](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamRead)调用`CFReadStreamCopyProperty`之前。`CFReadStreamRead`调用直到数据块(或连接失败)是可用的。不要在你的主应用程序线程上做这个。



###处理身份验证错误

如果返回的状态码函数`CFHTTPMessageGetResponseStatusCode`是 401 (远程服务器需要身份验证信息)或 407 (代理服务器需要身份验证),您需要将身份验证信息附加到请求和发送一遍。请阅读 [Communicating with Authenticating HTTP Servers](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/CFHTTPAuthenticationTasks/CFHTTPAuthenticationTasks.html#//apple_ref/doc/uid/TP30001132-CH8-SW1) 如何处理身份验证。




###处理重定向错误

当`CFReadStreamCreateForHTTPRequest`创建一个读流,自动重定向流默认情况下是禁用的。如果统一资源定位符,或者URL,该URL发送请求重定向到另一个,发送请求将导致一个错误的状态码300到307不等。如果你收到一个重定向错误,你需要关闭流,创建流再次启用自动重定向,并打开流。参见清单3 - 5

Listing 3-5  重定向一个 HTTP stream

	CFReadStreamClose(myReadStream);
	CFReadStreamRef myReadStream =
	CFReadStreamCreateForHTTPRequest(kCFAllocatorDefault, myRequest);
	if (CFReadStreamSetProperty(myReadStream, kCFStreamPropertyHTTPShouldAutoredirect, kCFBooleanTrue) == false) {
	// something went wrong, exit
	}
	CFReadStreamOpen(myReadStream);



你当你创建一个读取流可能想要启用自动重定向。

###取消一个未决请求


一旦请求已经发送,它是不可能阻止远程服务器代理。然而,如果你不再关心响应数据,您可以关闭流。

>重要:不要从任何线程关闭流，另一个线程正在等待该流的内容。如果你需要能够终止请求时,您应该使用非阻塞I / O [Preventing Blocking When Working with Streams] (https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/CFStreamTasks/CFStreamTasks.html#//apple_ref/doc/uid/TP30000230-61973)。一定要 run loop 关闭之前删除流。
























