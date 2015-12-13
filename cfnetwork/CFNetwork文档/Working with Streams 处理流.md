


#处理流

[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/CFStreamTasks/CFStreamTasks.html#//apple_ref/doc/uid/TP30001132-CH6-SW1)
 翻译人:王谦  翻译时间:2015.9.9


本章讨论了如何创建、打开、读写流检查错误。它还描述了如何阅读从一个读取流,如何写一个 write streams ,如何防止读或写流时阻塞,以及如何通过代理服务器引导流。



##使用读取流

核心基础流可用于读写文件或使用网络 sockets 。除了创建这些流的过程中,他们的行为类似。


###创建一个读取流

首先创建一个读取流。清单2创建一个读取一个文件流

**清单2创建一个从文件中读取流**

>CFReadStreamRef myReadStream = CFReadStreamCreateWithFile(kCFAllocatorDefault, fileURL);


在这个清单中,`kCFAllocatorDefault`参数指定当前默认系统分配器被用来分配内存流和fileURL参数指定的文件的名称,这读流被创建,如文件:`file:///Users/joeuser/Downloads/MyApp.sit`。


类似地,您可以创建一条流基于网络服务通过调用[CFStreamCreatePairWithSocketToCFHost](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFSocketStreamRef/index.html#//apple_ref/c/func/CFStreamCreatePairWithSocketToCFHost)(描述[Using a Run Loop to Prevent Blocking]())或[CFStreamCreatePairWithSocketToNetService](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFSocketStreamRef/index.html#//apple_ref/c/func/CFStreamCreatePairWithSocketToNetService)(描述 [NSNetServices and CFNetServices Programming Guide](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/NSNetServiceProgGuide/Introduction.html#//apple_ref/doc/uid/TP40002736)。)


现在,您已经创建了流,你可以打开它。打开一个流，使流保留系统资源,如打开文件所需的文件描述符。清单2是一个示例打开读流。

清单2 - 2打开阅读流

	if (!CFReadStreamOpen(myReadStream)) {
	CFStreamError myErr = CFReadStreamGetError(myReadStream);
	   // An error has occurred.
	      if (myErr.domain == kCFStreamErrorDomainPOSIX) {
	      // Interpret myErr.error as a UNIX errno.
	      } else if (myErr.domain ==   kCFStreamErrorDomainMacOSStatus) {
       	 // Interpret myErr.error as a MacOS error code.
	OSStatus macError = (OSStatus)myErr.error;
	     // Check other error domains.
	   }
	}

`CFReadStreamOpen`函数返回`TRUE`指示成功和错误如果打开失败任何理由。如果`CFReadStreamOpen`返回`FALSE`,示例调用`CFReadStreamGetError`函数,它返回一个结构类型的`CFStreamError`组成的两个值:一个域代码和错误代码。域代码指示应如何解释错误代码。例如,如果`kCFStreamErrorDomainPOSIX`域代码,错误代码是一个UNIX `errno`值。其他错误域`kCFStreamErrorDomainMacOSStatus`,这表明错误代码是一个`OSStatus` `MacErrors.h`中定义的值。`kCFStreamErrorDomainHTTP`,这表明的错误代码是`CFStreamErrorHTTP`定义的枚举值。




打开一个流可以是一个漫长的过程,所以`CFReadStreamOpen`和`CFWriteStreamOpen`函数返回`TRUE`指示避免阻塞,打开流的过程已经开始。检查打开的状态,调用函数`CFReadStreamGetStatus` `CFWriteStreamGetStatus`,它返回`kCFStreamStatusOpening`如果开放仍在进行中,`kCFStreamStatusOpen`如果开放完成,或`kCFStreamStatusErrorOccurred`如果开放已经完成但失败了。在大多数情况下,不管是否开放完成因为`CFStream`读写的函数将阻塞,直到流是开放的。


##读取一个流

读一个读取流, CFReadStreamRead 调用这个函数,类似于 UNIX 阅读()系统调用。把缓冲区和缓冲区长度参数。都返回读取的字节数,如果最后的流或文件,或是－1，则发生错误。两个 block ,直到至少可以读一个字节,而继续阅读,只要他们没有阻碍。阅读清单2 - 3是一个示例流。


清单2 - 3阅读一个流(阻塞)

	CFIndex numBytesRead;
	do {
		UInt8 buf[myReadBufferSize]; // define myReadBufferSize as desired
		numBytesRead = CFReadStreamRead(myReadStream, buf, sizeof(buf));
		if( numBytesRead > 0 ) {
		handleBytes(buf, numBytesRead);
		} else if( numBytesRead < 0 ) {
				CFStreamError error = CFReadStreamGetError(myReadStream);
				reportError(error);
		}
	} while( numBytesRead > 0 );


###Tearing Down a Read Stream

当所有的数据被读取时,你应该调用`CFReadStreamClose`函数关闭流,从而与之关联的释放系统资源。然后释放流参考[CFRelease](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFTypeRef/index.html#//apple_ref/doc/c_ref/CFRelease)通过调用函数。您可能还想无效的参考设置为`NULL`。清单2 - 4给出了一个例子。

**清单2 - 4释放 read stream**

	CFReadStreamClose(myReadStream);
	CFRelease(myReadStream);
	myReadStream = NULL;



##使用Write Streams


使用 Write Streams 类似于处理 Read Stream 。一个主要的区别在于功能 CFWriteStreamWrite 并不能保证接受你传递给它的所有字节。相反,`CFWriteStreamWrite`返回的字节数,它接受。您会注意到清单2 - 5所示的示例代码,如果写的字节数和字节总数不一样，那么缓冲调整以适应这一点


**清单2 - 5创建、打开、写作,并释放 write stream**


	CFWriteStreamRef myWriteStream =
	CFWriteStreamCreateWithFile(kCFAllocatorDefault, fileURL);
	if (!CFWriteStreamOpen(myWriteStream)) {
	CFStreamError myErr = CFWriteStreamGetError(myWriteStream);
	// An error has occurred.
	if (myErr.domain == kCFStreamErrorDomainPOSIX) {
	// Interpret myErr.error as a UNIX errno.
	} else if (myErr.domain == kCFStreamErrorDomainMacOSStatus) {
	// Interpret myErr.error as a MacOS error code.
	OSStatus macError = (OSStatus)myErr.error;
	// Check other error domains.
	}
	}
	UInt8 buf[] = “Hello, world”;
	CFIndex bufLen = (CFIndex)strlen(buf);

	while (!done) {
	CFIndex bytesWritten = CFWriteStreamWrite(myWriteStream, buf, (CFIndex)bufLen);
	if (bytesWritten < 0) {
	CFStreamError error = CFWriteStreamGetError(myWriteStream);
	reportError(error);
	} else if (bytesWritten == 0) {
	if (CFWriteStreamGetStatus(myWriteStream) == kCFStreamStatusAtEnd) {
	done = TRUE;
	}
	} else if (bytesWritten != bufLen) {
	// Determine how much has been written and adjust the buffer
	bufLen = bufLen - bytesWritten;
	memmove(buf, buf + bytesWritten, bufLen);
	
	// Figure out what went wrong with the write stream
	CFStreamError error = CFWriteStreamGetError(myWriteStream);
	reportError(error);
	
	}
	}
	CFWriteStreamClose(myWriteStream);
	CFRelease(myWriteStream);
	myWriteStream = NULL;


##处理流的时候防止阻塞

使用流交流时,总是有一个机会,特别是在套接字流,数据传输可能需要很长时间。如果你正在实施流同步整个应用程序将不得不等待数据传输。因此,强烈建议您的代码使用替代方法来防止阻塞。

有两种方法可以防止阻塞 当reading或writing CFStream对象的时候:

* 使用一个 run loop ——注册接收stream-related事件和进度上的流 run loop。当stream-related事件发生时,你的回调函数（由注册呼叫指定）被调用。

* 轮询,读取流,发现如果有字节从流读取前阅读。写流,发现流是否可以写入没有阻塞的流。

这些方法中的每一个都是在以下部分中描述。



###使用一个Run Loop,防止阻塞

使用流的首选方法是 run loop 。你的主程序线程上运行 run loop 。它等待事件发生,然后调用任何函数与一个给定的事件相关联。

在网络传输的情况下,你的回调函数执行的 run loop 当你注册的事件发生。这允许您不需要调查你的套接字流,这将减缓线程。

了解更多关于 run loop，看[Threading Programming Guide](https://developer.apple.com/library/prerelease/mac/documentation/Cocoa/Conceptual/Multithreading/Introduction/Introduction.html#//apple_ref/doc/uid/10000057i).

这个例子首先创建一个套接字读取流:

	CFStreamCreatePairWithSocketToCFHost(kCFAllocatorDefault, host, port,
	&myReadStream, NULL);


CFHost对象引用,主机,指定的远程主机读取流为与端口参数指定主机使用的端口号。`CFStreamCreatePairWithSocketToCFHost`函数返回新的阅读myReadStream流参考。最后一个参数,NULL,表明,调用者不希望创建一个写流。如果你想创建一个写流,最后一个参数,例如,&myWriteStream。


打开套接字读取流之前,创建一个上下文,将使用当你注册时以接收stream-related事件:

	CFStreamClientContext myContext = {0, myPtr, myRetain, myRelease, myCopyDesc};

第一个参数是0到指定版本号。信息参数,数据是一个指针,`myPtr`你想要传递给回调函数。`myPtr`通常是一个指向结构的指针已经定义,包含流有关的信息。保留参数是一个指向函数的指针保留信息参数。`myRetain`所以如果你将它设置为你的函数,在上面的代码中,`CFStream`将调用`myRetain(myPtr)`保留信息的指针。`myRelease`,同样,释放参数是一个函数指针释放信息参数。当流与上下文没有关联,`CFStream`称之为`myRelease(myPtr)`。最后,`copyDescription`是一个函数的参数提供的描述流。举个例子,如果你是叫`CFCopyDesc(myReadStream)`与上面所示的流上下文,`CFStream`称之为`myCopyDesc(myPtr)`。


客户端上下文还允许您设置的选项保留,释放和`copyDescription`参数为`NULL`。如果你保留和释放参数设置为`NULL`,那么系统将期待你指针所指向的内存信息或者直到流本身被摧毁。如果你`copyDescription`参数设置为`NULL`,那么系统将提供,如果委托有要求,基本的描述什么是指针指向的内存的信息。


与委托上下文设置,调用函数`CFReadStreamSetClient`登记接收stream-related事件。`CFReadStreamSetClient`要求您指定回调函数和你想接收的事件。下面的例子在清单2 - 6指定回调函数想收到`kCFStreamEventHasBytesAvailable`, `kCFStreamEventErrorOccurred`,`kCFStreamEventEndEncountered`事件。然后安排流run loop [CFReadStreamScheduleWithRunLoop](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/doc/c_ref/CFReadStreamScheduleWithRunLoop)函数。参见清单2 - 6对如何做到这一点的一个例子。





**清单2 - 6安排一个流 run loop**

	CFOptionFlags registeredEvents = kCFStreamEventHasBytesAvailable |
	kCFStreamEventErrorOccurred | kCFStreamEventEndEncountered;
	if (CFReadStreamSetClient(myReadStream, registeredEvents, myCallBack, &myContext))
	{
	CFReadStreamScheduleWithRunLoop(myReadStream, CFRunLoopGetCurrent(),
	kCFRunLoopCommonModes);
	}


安排流run loop,你准备打开流如清单2所示。


**清单2 - 7开放非阻塞读流**

	if (!CFReadStreamOpen(myReadStream)) {
	CFStreamError myErr = CFReadStreamGetError(myReadStream);
	if (myErr.error != 0) {
	// An error has occurred.
	if (myErr.domain == kCFStreamErrorDomainPOSIX) {
	// Interpret myErr.error as a UNIX errno.
	strerror(myErr.error);
	} else if (myErr.domain == kCFStreamErrorDomainMacOSStatus) {
	OSStatus macError = (OSStatus)myErr.error;
	}
	// Check other domains.
	} else
	// start the run loop
	CFRunLoopRun();
	}



现在,等待你的回调函数被执行。在你的回调函数,检查事件代码和采取适当的行动。参见清单2 - 8



**清单2 - 8网络事件回调函数**

	void myCallBack (CFReadStreamRef stream, CFStreamEventType event, void *myPtr) {
	switch(event) {
	case kCFStreamEventHasBytesAvailable:
	// It is safe to call CFReadStreamRead; it won’t block because bytes
	// are available.
	UInt8 buf[BUFSIZE];
	CFIndex bytesRead = CFReadStreamRead(stream, buf, BUFSIZE);
	if (bytesRead > 0) {
	handleBytes(buf, bytesRead);
	}
	// It is safe to ignore a value of bytesRead that is less than or
	// equal to zero because these cases will generate other events.
	break;
	case kCFStreamEventErrorOccurred:
	CFStreamError error = CFReadStreamGetError(stream);
	reportError(error);
	CFReadStreamUnscheduleFromRunLoop(stream, CFRunLoopGetCurrent(),
	kCFRunLoopCommonModes);
	CFReadStreamClose(stream);
	CFRelease(stream);
	break;
	case kCFStreamEventEndEncountered:
	reportCompletion();
	CFReadStreamUnscheduleFromRunLoop(stream, CFRunLoopGetCurrent(),
	kCFRunLoopCommonModes);
	CFReadStreamClose(stream);
	CFRelease(stream);
	break;
	}
	}


回调函数接收`kCFStreamEventHasBytesAvailable`事件代码时,它调用`CFReadStreamRead`读取数据

回调函数接收`kCFStreamEventErrorOccurred`事件代码时,它调用`CFReadStreamGetError`的错误和自己的误差函数(`reportError`)来处理错误。

回调函数接收`kCFStreamEventEndEncountered`事件代码时,它调用的函数(`reportCompletion`)处理的数据,然后调用`CFReadStreamUnscheduleFromRunLoop`函数删除指定的流循环运行。然后`CFReadStreamClose`函数运行关闭流和`CFRelease`释放流参考


查询网络流

一般来说,查询网络流是不明智的。然而,在某些罕见的情况下,它可能是有用的。调查一个流,你先看看流准备阅读或写作,然后执行一个读或写操作流。

当写入写流时,您可以确定流是通过调用[CFWriteStreamCanAcceptBytes](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/c/func/CFWriteStreamCanAcceptBytes)准备接受数据。如果它返回TRUE,那么你可以确信随后调用[CFWriteStreamWrite](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/c/func/CFWriteStreamCanAcceptBytes)没有阻塞函数将立即发送数据。


同样,读取流,在调用[CFReadStreamRead](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamRead)之前,[CFReadStreamHasBytesAvailable](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamHasBytesAvailable)调用的函数。

清单2 - 9读流是一个查询的例子。

	while (!done) {
	if (CFReadStreamHasBytesAvailable(myReadStream)) {
	UInt8 buf[BUFSIZE];
	CFIndex bytesRead = CFReadStreamRead(myReadStream, buf, BUFSIZE);
	if (bytesRead < 0) {
	CFStreamError error = CFReadStreamGetError(myReadStream);
	reportError(error);
	} else if (bytesRead == 0) {
	if (CFReadStreamGetStatus(myReadStream) == kCFStreamStatusAtEnd) {
	done = TRUE;
	}
	} else {
	handleBytes(buf, bytesRead);
	}
	} else {
	// ...do something else while you wait...
	}
	}

**清单2 - 10是一个写流的查询例子。**

	UInt8 buf[] = “Hello, world”;
	UInt32 bufLen = strlen(buf);
	
	while (!done) {
	if (CFWriteStreamCanAcceptBytes(myWriteStream)) {
	int bytesWritten = CFWriteStreamWrite(myWriteStream, buf, strlen(buf));
	if (bytesWritten < 0) {
	CFStreamError error = CFWriteStreamGetError(myWriteStream);
	reportError(error);
	} else if (bytesWritten == 0) {
	if (CFWriteStreamGetStatus(myWriteStream) == kCFStreamStatusAtEnd)
	{
	done = TRUE;
	}
	} else if (bytesWritten != strlen(buf)) {
	// Determine how much has been written and adjust the buffer
	bufLen = bufLen - bytesWritten;
	memmove(buf, buf + bytesWritten, bufLen);
	
	// Figure out what went wrong with the write stream
	CFStreamError error = CFWriteStreamGetError(myWriteStream);
	reportError(error);
	}
	} else {
	// ...do something else while you wait...
	}
	}


在防火墙


有两种方法可以应用防火墙设置流。对于大多数流,您可以使用[SCDynamicStoreCopyProxies](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Reference/SCDynamicStoreCopySpecific/index.html#//apple_ref/doc/c_ref/SCDynamicStoreCopyProxies)检索代理设置函数,然后将结果应用于流通过设置`kCFStreamHTTPProxy(或kCFStreamFTPProxy)`特性。[SCDynamicStoreCopyProxies](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Reference/SCDynamicStoreCopySpecific/index.html#//apple_ref/doc/c_ref/SCDynamicStoreCopyProxies)函数是系统配置框架的一部分,因此您需要包括`< SystemConfiguration / SystemConfiguration。h >`在您的项目中使用的功能。当你完成它时就发布代理字典引用。这个过程看起来如清单2 - 11所示。


**通过代理服务器清单2 - 11引导流**

	CFDictionaryRef proxyDict = SCDynamicStoreCopyProxies(NULL);
	CFReadStreamSetProperty(readStream, kCFStreamPropertyHTTPProxy, proxyDict);


但是,如果你经常需要使用代理设置多个流,变得更加复杂。在这种情况下检索用户的机器的防火墙设置需要五个步骤:

**1.**创建一个持久句柄动态存储会话,SCDynamicStoreRef。

**2.**把处理动态存储会话到run loop代理更改的通知。

**3.**使用[SCDynamicStoreCopyProxies](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Reference/SCDynamicStoreCopySpecific/index.html#//apple_ref/doc/c_ref/SCDynamicStoreCopyProxies)检索最新的代理设置。

**4.**更新你的那一份代理时，告知变化。

**5.**当你通过`SCDynamicStoreRef`清理。


创建动态存储会话的处理,使用函数[SCDynamicStoreCreate](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Reference/SCDynamicStore/index.html#//apple_ref/doc/c_ref/SCDynamicStoreCreate)和通过分配器,一个名字来描述您的过程,一个回调函数和动态存储上下文,`SCDynamicStoreContext`。这是应用程序运行时初始化。代码将类似于清单2

**清单2创建一个动态存储会话句柄**

	SCDynamicStoreContext context = {0, self, NULL, NULL, NULL};
	systemDynamicStore = SCDynamicStoreCreate(NULL,
	CFSTR("SampleApp"),
	proxyHasChanged,
	&context);


创建引用动态存储之后,您需要将其添加到运行循环。首先,采取动态存储引用和设置监测对代理的任何更改。这是[SCDynamicStoreKeyCreateProxies](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Reference/SCDynamicStoreKey/index.html#//apple_ref/doc/c_ref/SCDynamicStoreKeyCreateProxies)和[SCDynamicStoreSetNotificationKeys](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Reference/SCDynamicStore/index.html#//apple_ref/doc/c_ref/SCDynamicStoreSetNotificationKeys)功能完成。然后,您可以将动态存储引用添加到运行与功能[SCDynamicStoreCreateRunLoopSource](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Reference/SCDynamicStore/index.html#//apple_ref/doc/c_ref/SCDynamicStoreCreateRunLoopSource)和[CFRunLoopAddSource](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFRunLoopRef/index.html#//apple_ref/doc/c_ref/CFRunLoopAddSource)循环。您的代码应该类似于清单2 - 13所示。


**清单2 - 13添加一个动态存储引用run loop**

	// Set up the store to monitor any changes to the proxies
	CFStringRef proxiesKey = SCDynamicStoreKeyCreateProxies(NULL);
	CFArrayRef keyArray = CFArrayCreate(NULL,
	(const void **)(&proxiesKey),
	1,
	&kCFTypeArrayCallBacks);
	SCDynamicStoreSetNotificationKeys(systemDynamicStore, keyArray, NULL);
	CFRelease(keyArray);
	CFRelease(proxiesKey);
	
	// Add the dynamic store to the run loop
	CFRunLoopSourceRef storeRLSource =
	SCDynamicStoreCreateRunLoopSource(NULL, systemDynamicStore, 0);
	CFRunLoopAddSource(CFRunLoopGetCurrent(), storeRLSource, kCFRunLoopCommonModes);
	CFRelease(storeRLSource);


一旦动态存储引用添加到run loop,用它来预加载代理通过调用SCDynamicStoreCopyProxies词典目前的代理设置。参见清单2 - 14对如何做到这一点的

**清单2 - 14加载代理的字典**

	gProxyDict = SCDynamicStoreCopyProxies(systemDynamicStore);
	

由于动态存储引用添加到run loop,每一次改变你的回调函数将会运行代理。释放当前代理字典用新的代理设置并重新加载它。一个示例回调函数2-15。

**Listing 2-15  代理回调函数**

		void proxyHasChanged() {
		CFRelease(gProxyDict);
		gProxyDict = SCDynamicStoreCopyProxies(systemDynamicStore);
		}


因为所有的代理信息是最新的,应用代理。在创建你的读或写流,设置kCFStreamPropertyHTTPProxy CFReadStreamSetProperty或CFWriteStreamSetProperty代理通过调用函数。如果你列出流读取流叫做readStream,你的函数调用将如清单18所示。


**清单16代理信息添加到一个流**

	CFReadStreamSetProperty(readStream, kCFStreamPropertyHTTPProxy, gProxyDict);



当你使用代理设置都完成,确保释放字典和动态存储引用,从run loop删除动态存储引用。请参见清单2-17。


**清单2-17清理代理信息**

	if (gProxyDict) {
	CFRelease(gProxyDict);
	}
	
	// Invalidate the dynamic store's run loop source
	// to get the store out of the run loop
	CFRunLoopSourceRef rls = SCDynamicStoreCreateRunLoopSource(NULL, systemDynamicStore, 0);
	CFRunLoopSourceInvalidate(rls);
	CFRelease(rls);
	CFRelease(systemDynamicStore);




