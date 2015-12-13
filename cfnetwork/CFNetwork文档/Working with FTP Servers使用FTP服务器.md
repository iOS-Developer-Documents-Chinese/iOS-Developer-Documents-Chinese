
#使用FTP服务器


[原文地址](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Conceptual/CFNetwork/CFFTPTasks/CFFTPTasks.html#//apple_ref/doc/uid/TP30001132-CH9-SW2)
翻译人:王谦  日期:2015.9.10



本章解释了如何使用 CFFTP API 的一些基本特性。FTP 管理事务是异步执行的,而管理文件传输同步实现。




##下载一个文件


使用 CFFTP 非常类似于使用 CFHTTP ,因为他们都是基于 CFStream 。与任何其他 API ,使用异步 CFStream ,下载一个文件, CFFTP 要求您创建一个读取流文件,和一个回调函数,读取流。读取流接收数据时,回调函数将运行,您需要适当地下载字节。这个过程通常应该执行使用两个函数:一个设置流和一个回调函数。


###设置FTP流


首先创建一个 read stream 使用[CFReadStreamCreateWithFTPURL](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFFTPStreamRef/index.html#//apple_ref/doc/c_ref/CFReadStreamCreateWithFTPURL)函数和传递文件的 URL 字符串是在远程服务器上下载。URL 字符串的一个例子可能是`ftp://ftp.example.com/file.txt`。注意,字符串包含服务器名称、路径和文件。下一步，为该文件将被下载的本地位置创建一个 write stream 。这是使用[CFWriteStreamCreateWithFile](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/doc/c_ref/CFWriteStreamCreateWithFile)函数完成的,通过将下载的文件的路径。


因为 write stream 和 read stream 需要保持同步,这是一个好主意，来创建一个结构,包含所有的公共信息,如代理字典,文件大小,写的字节数,剩余的字节数和缓冲区。这种结构可能看起来像,如清单5 - 1所示。


**清单5 - 1流结构**

	typedef struct MyStreamInfo {
	
	CFWriteStreamRef  writeStream;
	CFReadStreamRef   readStream;
	CFDictionaryRef   proxyDict;
	SInt64            fileSize;
	UInt32            totalBytesWritten;
	UInt32            leftOverByteCount;
	UInt8             buffer[kMyBufferSize];
	
	} MyStreamInfo;


初始化结构与您刚才创建的读,写流。然后您可以定义流客户端上下文的信息字段(`CFStreamClientContext`)指向结构。这在以后有用。


打开你的write stream 与[CFWriteStreamOpen](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/c/func/CFWriteStreamOpen)函数,这样你就可以开始写入本地文件。以确保正常打开的流,调用函数[CFWriteStreamGetStatus](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/doc/c_ref/CFWriteStreamGetStatus)并检查是否返回`kCFStreamStatusOpen`或`kCFStreamStatusOpening`。


在写数据流时，将一个回调函数与读流关联起来。[CFReadStreamSetClient](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamSetClient)调用的函数,通过读取流,网络事件应该得到你的回调函数,回调函数的名称和`CFStreamClientContext`对象。早些时候通过信息字段设置客户端流的上下文,每当运行时，您的结构将会发送到你的回调函数里。



一些FTP服务器可能需要一个用户名,有些人可能还需要一个密码。如果你访问的服务器需要身份验证的用户名,调用[CFReadStreamSetProperty](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/c/func/CFReadStreamSetProperty)函数,通过读取流,`kCFStreamPropertyFTPUserName`特性,指`CFString`对象包含用户名。此外,如果您需要设置一个密码,设置`kCFStreamPropertyFTPPassword`属性。


也可以使用一些网络配置 FTP 代理。你获取代理信息以不同的方式取决于您的代码运行在 OS X 或 iOS 。


* 在OS X中,可以检索代理设置在字典通过调用
[SCDynamicStoreCopyProxies](https://developer.apple.com/library/prerelease/mac/documentation/Networking/Reference/SCDynamicStoreCopySpecific/index.html#//apple_ref/c/func/SCDynamicStoreCopyProxies)函数并传递NULL。

*  在iOS,您可以通过调用[CFNetworkCopyProxiesForURL](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFProxySupport/index.html#//apple_ref/c/func/CFNetworkCopyProxiesForURL)检索代理设置。


动态存储这些函数返回一个引用。您可以使用这个值来设置`kCFStreamPropertyFTPProxy`属性的读取流。这个设置代理服务器,指定的端口,并返回一个布尔值,该值指示被动模式是否为 FTP 执行流。


在提到的属性之外,有许多可用的其他属性FTP流。完整的列表如下。


* `kCFStreamPropertyFTPUserName` —使用用户名登录(可设置和检索;不设置匿名FTP连接)


* `kCFStreamPropertyFTPPassword` —密码用来登录(可设置和检索;不设置匿名FTP连接)


* `kCFStreamPropertyFTPUsePassiveMode` —是否使用被动模式(可设置和检索)

* `kCFStreamPropertyFTPResourceSize` —正在下载的项目的预期大小,如果可用(可收回,只能FTP读取流)


* `kCFStreamPropertyFTPFetchResourceInfo` —是否要求资源信息,比如尺寸,需要开始前下载(可设置和检索);设置这个属性可能会影响性能


* `kCFStreamPropertyFTPFileTransferOffset` —文件偏移量,开始转移(可设置和检索)


* `kCFStreamPropertyFTPAttemptPersistentConnection` —是否尝试重用连接(可设置和检索)

* `kCFStreamPropertyFTPProxy` —CFDictionary类型包含代理的键-值对的字典(可设置和检索)


* `kCFStreamPropertyFTPProxyHost` —FTP代理主机名称(可设置和检索)


* `kCFStreamPropertyFTPProxyPort` —端口号的FTP代理主机(可设置和检索)




正确的属性分配给读流,打开流使用[CFReadStreamOpen](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/doc/c_ref/CFReadStreamOpen)函数。假设这并不返回一个错误,那么所有的流已正确设置。



##实现回调函数


你的回调函数将接收三个参数:读取流,事件的类型,和你`MyStreamInfo`  结构。事件的类型决定了必须采取什么行动。


最常见的事件是`kCFStreamEventHasBytesAvailable`,发送时从服务器读取流已收到的字节。首先,检查有多少字节，读取通过调用[CFReadStreamRead](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/doc/c_ref/CFReadStreamRead)函数。确保返回值不小于零(错误),或等于零(下载已完成)。如果返回值是完成的,那么你就可以开始读流中的数据通过 write stream 写入磁盘。




调用[CFWriteStreamWrite](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/doc/c_ref/CFWriteStreamWrite)函数编写的数据流。有时[CFWriteStreamWrite](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/doc/c_ref/CFWriteStreamWrite)可以不写所有的读数据流返回。出于这个原因,设置一个循环运行,只要还有写数据。这个循环的代码在清单5 - 2,其中的信息是`MyStreamInfo`结构[Setting up the Streams]()。这个方法的写作写流使用阻塞流。你可以取得更好的性能通过写流事件驱动,但是代码比较复杂。

**清单5 - 2从读数据流中写入数据**

	bytesRead = CFReadStreamRead(info->readStream, info->buffer, kMyBufferSize);
	
	//...make sure bytesRead > 0 ...
	
	bytesWritten = 0;
	while (bytesWritten < bytesRead) {
	CFIndex result;
	
	result = CFWriteStreamWrite(info->writeStream, info->buffer + bytesWritten, bytesRead - bytesWritten);
	if (result <= 0) {
	fprintf(stderr, "CFWriteStreamWrite returned %ld\n", result);
	goto exit;
	}
	bytesWritten += result;
	}
	info->totalBytesWritten += bytesWritten;


重复整个过程,只要有可用的字节 read stream 。


其他两件事你需要注意`kCFStreamEventErrorOccurred`和`kCFStreamEventEndEncountered`。如果出现错误,使用[CFReadStreamGetError](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFReadStreamRef/index.html#//apple_ref/doc/c_ref/CFReadStreamGetError)检索错误,然后退出。如果在文件的末尾发生,那么你的下载已经完成,你可以退出。


确保一切完成后删除所有你的流和不使用其他流程的流。首先,关闭write stream 和客户端设置为 NULL 。然后从 run loop 中取消流并释放它。当你完成时，从 run loop 中删除流。



##上传一个文件


上传一个文件类似于下载一个文件。下载一个文件,你需要一个ReadStream和一个write stream。然而,当上传一个文件,读取流将用于本地文件并且写流将用于远程文件。按照说明 [Setting up the Streams](),无论它指的是读取流,都要适应写流的代码，反之亦然。


在回调函数,而不是寻找 `kCFStreamEventHasBytesAvailable` 事件,现在寻找事件 `kCFStreamEventCanAcceptBytes` 。首先,从文件读取字节使用流并将数据读入 `MyStreamInfo` 缓冲区。然后,运行[CFWriteStreamWrite](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/doc/c_ref/CFWriteStreamWrite)函数将字节从缓冲区中写入流。[CFWriteStreamWrite](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/doc/c_ref/CFWriteStreamWrite)返回的字节数,一直写到流。如果向流写入的字节数少于从文件读取的数量,计算出剩余的字节并将它们存储到缓冲区。在接下来的写周期,如果有剩余的字节,写他们写流而不是加载新数据读取流。重复整个过程,只要写流可以接受字节([CFWriteStreamCanAcceptBytes](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFWriteStreamRef/index.html#//apple_ref/doc/c_ref/CFWriteStreamCanAcceptBytes))。看到这个循环代码在清单5 - 3。




**清单5 - 3写数据到write stream**


	do {
	// Check for leftover data
	if (info->leftOverByteCount > 0) {
	bytesRead = info->leftOverByteCount;
	} else {
	// Make sure there is no error reading from the file
	bytesRead = CFReadStreamRead(info->readStream, info->buffer,
	kMyBufferSize);
	if (bytesRead < 0) {
	fprintf(stderr, "CFReadStreamRead returned %ld\n", bytesRead);
	goto exit;
	}
	totalBytesRead += bytesRead;
	}
	
	// Write the data to the write stream
	bytesWritten = CFWriteStreamWrite(info->writeStream, info->buffer, bytesRead);
	if (bytesWritten > 0) {
	
	info->totalBytesWritten += bytesWritten;
	
	// Store leftover data until kCFStreamEventCanAcceptBytes event occurs again
	if (bytesWritten < bytesRead) {
	info->leftOverByteCount = bytesRead - bytesWritten;
	memmove(info->buffer, info->buffer + bytesWritten,
	info->leftOverByteCount);
	} else {
	info->leftOverByteCount = 0;
	}
	} else {
	if (bytesWritten < 0)
	fprintf(stderr, "CFWriteStreamWrite returned %ld\n", bytesWritten);
	break;
	}
	
	} while (CFWriteStreamCanAcceptBytes(info->writeStream));



使用 `kCFStreamEventErrorOccurred` 和`kCFStreamEventEndEncountered` 事件时下载一个文件。



##创建一个远程目录


在远程服务器上创建一个目录,设置一个写流,如果你是要上传一个文件。然而,提供一个目录路径,而不是一个文件,CFURL对象传递 [CFWriteStreamCreateWithFTPURL](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFFTPStreamRef/index.html#//apple_ref/doc/c_ref/CFWriteStreamCreateWithFTPURL) 函数。用正斜杠结束路径。例如,一个适当的目录路径将是 `ftp://ftp.example.com/newDirectory/` ,而不是 `ftp://ftp.example.com/newDirectory/newFile.txt` 。当回调函数执行的循环运行,它发送事件 `kCFStreamEventEndEncountered` ,这意味着创建了目录(或 `kCFStreamEventErrorOccurred` 如果事情错了)


只有一个级别的目录可以创建 [CFWriteStreamCreateWithFTPURL](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFFTPStreamRef/index.html#//apple_ref/doc/c_ref/CFWriteStreamCreateWithFTPURL)每个调用。同样,只有如果你有正确的创建一个目录服务器上的权限。


##下载目录清单

通过 FTP 下载目录清单下载或上传一个文件略有不同。这是由于传入的数据必须被解析。首先,建立一个阅读流目录清单。这样做应该是下载一个文件:创建一个流,注册一个回调函数,调度的流 run loop (如果有必要,设置用户名、密码和代理信息),最后打开流。在接下来的例子中你不需要读和写流检索目录清单时,由于输入数据将屏幕而不是一个文件。


在回调函数中,看 `kCFStreamEventHasBytesAvailable` 事件。加载数据的读取流之前,确保没有剩余的数据流从上一次回调函数运行。负载的抵消 `leftOverByteCount` `MyStreamInfo` 结构领域。然后,从流读取数据,考虑到抵消你计算。读取的字节缓冲区大小和数量也应该计算。这都是完成了清单5 - 4所示。


**清单5 - 4加载数据目录清单**


	// If previous call had unloaded data
	int offset = info->leftOverByteCount;
	
	// Load data from the read stream, accounting for the offset
	bytesRead = CFReadStreamRead(info->readStream, info->buffer + offset,
	kMyBufferSize - offset);
	if (bytesRead < 0) {
	fprintf(stderr, "CFReadStreamRead returned %ld\n", bytesRead);
	break;
	} else if (bytesRead == 0) {
	break;
	}
	bufSize = bytesRead + offset;
	totalBytesRead += bufSize;

数据被读取到缓冲区后,设置一个循环来解析数据。解析的数据不一定是整个目录清单;它可以(可能)的清单。创建循环使用函数 [CFFTPCreateParsedResourceListing](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFFTPStreamRef/index.html#//apple_ref/doc/c_ref/CFFTPCreateParsedResourceListing) 解析数据,应通过缓冲区的数据,缓冲区的大小,参考字典。它返回的字节数解析。只要这个值大于零,继续循环。[CFFTPCreateParsedResourceListing](https://developer.apple.com/library/prerelease/mac/documentation/CoreFoundation/Reference/CFFTPStreamRef/index.html#//apple_ref/doc/c_ref/CFFTPCreateParsedResourceListing) 创建包含所有的字典目录清单信息;更多信息看 [Setting up the Streams]().

有可能 `CFFTPCreateParsedResourceListing` 返回一个积极的价值,而不是创建一个解析词典。例如,如果结束的清单包含的信息不能被解析, `CFFTPCreateParsedResourceListing` 将返回一个积极的价值告诉调用者,数据已经被消耗。然而, `CFFTPCreateParsedResourceListing` 不会创建一个解析词典,因为它无法理解数据。


如果创建一个解析词典,重新计算读取的字节数和缓冲区的大小,如清单5 5所示。

**清单5 5加载目录清单和解析它**


	do
	{
	bufRemaining = info->buffer + totalBytesConsumed;
	
	bytesConsumed = CFFTPCreateParsedResourceListing(NULL, bufRemaining,
	bufSize, &parsedDict);
	if (bytesConsumed > 0) {
	
	// Make sure CFFTPCreateParsedResourceListing was able to properly
	// parse the incoming data
	if (parsedDict != NULL) {
	// ...Print out data from parsedDict...
	CFRelease(parsedDict);
	}
	
	totalBytesConsumed += bytesConsumed;
	bufSize -= bytesConsumed;
	info->leftOverByteCount = bufSize;
	
	} else if (bytesConsumed == 0) {
	
	// This is just in case. It should never happen due to the large buffer size
	info->leftOverByteCount = bufSize;
	totalBytesRead -= info->leftOverByteCount;
	memmove(info->buffer, bufRemaining, info->leftOverByteCount);
	
	} else if (bytesConsumed == -1) {
	fprintf(stderr, "CFFTPCreateParsedResourceListing parse failure\n");
	// ...Break loop and cleanup...
	}
	
	} while (bytesConsumed > 0);


当流没有更多可用的字节,从 run loop 中清理所有的数据流和删除它们。










