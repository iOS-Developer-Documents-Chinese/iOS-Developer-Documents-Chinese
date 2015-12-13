
使用NSURLConnection

NSURLConnection提供最灵活的方法检索URL的内容。这个类提供了一个简单的接口用于创建和取消连接,并支持委派方法的集合,提供反馈和控制连接的许多方面。这些类可分为五类:URL加载、缓存管理、身份验证和凭证,cookie存储和协议的支持。


创建一个连接

NSURLConnection类支持三种方式检索URL的内容:同步,异步使用完成block,并使用一个自定义的委托对象异步。

检索URL的内容同步:只在一个后台线程运行的代码,你可以叫sendSynchronousRequest:returningResponse:错误:执行HTTP请求。这个调用返回时,请求完成或发生错误。更多细节,请参阅检索数据同步。

检索URL使用完成处理block的内容:如果你不需要监控的状态请求,而仅仅是需要执行一些操作,当数据被完全接受,你可以叫sendAsynchronousRequest:队列:completionHandler:,通过block处理结果。更多细节,请参见使用完成检索数据处理程序块。

检索URL使用委托对象的内容:创建一个委托类实现至少以下委派方法:连接:didReceiveResponse:连接:didReceiveData:连接:didFailWithError:connectionDidFinishLoading:。支持委派方法定义在NSURLConnectionDelegate,NSURLConnectionDownloadDelegate,NSURLConnectionDataDelegate协议。

清单2-1中的示例发起一个连接URL。这段代码首先创建了一个URL NSURLRequest实例,指定缓存访问策略和连接的超时间隔。然后创建一个NSURLConnection实例,指定请求和代表。如果NSURLConnection不能创建一个连接请求,initWithRequest:代表:返回零。NSMutableData片段还创建了一个实例的存储的数据逐步提供给委托方。


Listing 2-1  Creating a connection using NSURLConnection
// Create the request.
NSURLRequest *theRequest=[NSURLRequest requestWithURL:[NSURL URLWithString:@"http://www.apple.com/"]
cachePolicy:NSURLRequestUseProtocolCachePolicy
timeoutInterval:60.0];

// Create the NSMutableData to hold the received data.
// receivedData is an instance variable declared elsewhere.
receivedData = [NSMutableData dataWithCapacity: 0];

// create the connection with the request
// and start loading the data
NSURLConnection *theConnection=[[NSURLConnection alloc] initWithRequest:theRequest delegate:self];
if (!theConnection) {
// Release the receivedData object.
receivedData = nil;

// Inform the user that the connection failed.
}


立即调用开始接到initWithRequest:代表:消息。可以取消之前的任何时间代表接收connectionDidFinishLoading:或连接:didFailWithError:消息发送连接取消的消息。

当服务器提供了足够的数据来创建一个NSURLResponse对象,代表接收到连接:didReceiveResponse:消息。NSURLResponse对象提供的委托方法可以检查并确定预期的内容长度数据,MIME类型,显示文件名和其他元数据服务器提供的。


你应该准备你的代理接受连接:didReceiveResponse:消息多次单个连接;这可能发生多部分的MIME编码的响应。每次代表接收到连接:didReceiveResponse:消息,应该重置任何进展迹象和丢弃所有以前接收的数据(在多部分反应除外)。清单2中的示例实现简单重置接收的数据的长度为0每次调用它。

Listing 2-2  Example connection:didReceiveResponse: implementation
- (void)connection:(NSURLConnection *)connection didReceiveResponse:(NSURLResponse *)response
{
// This method is called when the server has determined that it
// has enough information to create the NSURLResponse object.

// It can be called multiple times, for example in the case of a
// redirect, so each time we reset the data.

// receivedData is an instance variable declared elsewhere.
[receivedData setLength:0];
}

代理定期发送连接:didReceiveData:消息作为数据接收。委托实现负责存储新收到的数据。在清单2 - 3中的示例实现,新数据附加到NSMutableData对象创建如清单2所示。

Listing 2-3  Example connection:didReceiveData: implementation
- (void)connection:(NSURLConnection *)connection didReceiveData:(NSData *)data
{
// Append the new data to receivedData.
// receivedData is an instance variable declared elsewhere.
[receivedData appendData:data];
}


您还可以使用连接:didReceiveData:方法提供一个连接的进步给用户。要做到这一点,您必须首先获得预期的内容长度通过调用expectedContentLength方法在连接URL响应对象:didReceiveResponse:委托的方法。如果服务器不提供长度信息,NSURLResponseUnknownLength expectedContentLength回报。


如果一个错误发生在转让、委托接收连接:didFailWithError:消息。NSError对象作为参数传递给指定的细节错误。它还提供的URL请求失败的用户信息使用关键NSURLErrorFailingURLStringErrorKey字典。


代理接收连接后:didFailWithError:消息,它没有收到进一步的委托为指定的连接信息


清单2 - 4中的示例释放连接,以及任何接收的数据,记录错误。

Listing 2-4  Example connection:didFailWithError: implementation
- (void)connection:(NSURLConnection *)connection
didFailWithError:(NSError *)error
{
// Release the connection and the data object
// by setting the properties (declared elsewhere)
// to nil.  Note that a real-world app usually
// requires the delegate to manage more than one
// connection at a time, so these lines would
// typically be replaced by code to iterate through
// whatever data structures you are using.
theConnection = nil;
receivedData = nil;

// inform the user
NSLog(@"Connection failed! Error - %@ %@",
[error localizedDescription],
[[error userInfo] objectForKey:NSURLErrorFailingURLStringErrorKey]);
}

最后,如果连接成功的检索请求,代理接收connectionDidFinishLoading:消息。代理没有收到进一步的消息连接,和应用程序可以释放NSURLConnection对象。


清单2 - 5中的示例实现日志接收的数据的长度和释放连接对象和接收的数据。



Listing 2-5  Example connectionDidFinishLoading: implementation
- (void)connectionDidFinishLoading:(NSURLConnection *)connection
{
// do something with the data
// receivedData is declared as a property elsewhere
NSLog(@"Succeeded! Received %d bytes of data",[receivedData length]);

// Release the connection and the data object
// by setting the properties (declared elsewhere)
// to nil.  Note that a real-world app usually
// requires the delegate to manage more than one
// connection at a time, so these lines would
// typically be replaced by code to iterate through
// whatever data structures you are using.
theConnection = nil;
receivedData = nil;
}


这个示例使用NSURLConnection代理的简单实现。额外委派方法提供定制服务器的处理重定向的能力,授权请求和响应缓存。




做一个POST请求

你可以做一个HTTP或HTTPS POST请求几乎以相同的方式使其他URL请求(身份验证示例中描述)。主要的区别在于,你必须首先配置NSMutableURLRequest对象提供initWithRequest:代表:方法。

您还需要构建数据。你可以选择三种方式之一:

上传短,内存中的数据,你应该对于现有的数据,像Continuing Without Credentials中描述的那样。

从磁盘数据上传文件,调用setHTTPBodyStream:方法告诉从NSInputStream NSMutableURLRequest阅读和使用结果数据作为主体内容。

对于大量的构造数据,调用CFStreamCreateBoundPair创建一条小数据流,然后调用setHTTPBodyStream:方法告诉NSMutableURLRequest使用其中一个流作为其主体内容的源。通过编写其他流,您可以发送数据块。

这取决于你如何在服务器端处理事情,你也可以对于你发送的数据。 (详情请见 Continuing Without Credentials.)

如果你上传数据到一个兼容的服务器,URL加载系统还支持100(继续)HTTP状态代码,它允许一个上传继续上次的事件或其他 身份验证错误失败的时候。使支持上传延续,设置期望:头100-continue 继续请求对象。

清单6显示了如何为一个POST请求配置一个NSMutableURLRequest对象。

Listing 2-6  Configuring an NSMutableRequest object for a POST request
// In body data for the 'application/x-www-form-urlencoded' content type,
// form fields are separated by an ampersand. Note the absence of a
// leading ampersand.
NSString *bodyData = @"name=Jane+Doe&address=123+Main+St";

NSMutableURLRequest *postRequest = [NSMutableURLRequest requestWithURL:[NSURL URLWithString:@"https://www.apple.com"]];

// Set the request's content type to application/x-www-form-urlencoded
[postRequest setValue:@"application/x-www-form-urlencoded" forHTTPHeaderField:@"Content-Type"];

// Designate the request a POST request and specify its body data
[postRequest setHTTPMethod:@"POST"];
[postRequest setHTTPBody:[NSData dataWithBytes:[bodyData UTF8String] length:strlen([bodyData UTF8String])]];

// Initialize the NSURLConnection and proceed as described in
// Retrieving the Contents of a URL



为请求指定一个不同的内容类型,使用setValue:forHTTPHeaderField:方法。如果你做什么,确保你的传输流内容类型正确格式化。


获得一个进度预估一个POST请求,实现连接:didSendBodyData:totalBytesWritten:totalBytesExpectedToWrite:方法连接的代理。请注意,这不是一个上载过程的精确测量,因为可能会连接失败或者连接可能会遇到一个身份验证的要求。


使用Completion Handler Block

NSURLConnection类提供了支持检索资源的内容以异步的方式代表一个NSURLRequest对象和调用返回一块当结果或当一个错误或超时。要做到这一点,调用类方法sendAsynchronousRequest:队列:completionHandler:提供请求对象,完成Completion Handler Block和block应该运行一个NSOperation队列。当请求完成或发生错误时,URL加载系统调用,结果数据块或错误信息。


如果请求成功,请求的内容传递给回调Completion Handler Block 作为NSData对象和一个NSURLResponse对象的请求。如果NSURLConnection无法检索URL,NSError对象是作为第三个参数传递。

注意:这个方法有两个重要的限制:

最小的支持请求,要求提供身份验证。如果请求需要身份验证的连接,有效身份证件必须已经在NSURLCredentialStorage对象是可用的或必须提供所请求的URL的一部分。如果无法获得凭据或无法验证,URL加载系统会发送NSURLProtocol子类处理连接continueWithoutCredentialForAuthenticationChallenge:消息。

没有修改的方式响应缓存或接受服务器的默认行为重定向。当一个连接请求遇到服务器重定向,定向总是受到肯定的。同样地,数据存储在缓存的响应根据默认提供的协议实现的支持

NSURLSession类提供了类似的功能,没有这些限制。有关更多信息,看Using NSURLSession.


检索数据同步

NSURLConnection类提供了支持检索资源的内容由一个NSURLRequest对象以同步的方式使用类方法sendSynchronousRequest:returningResponse:错误:。使用这种方法是不可取的,因为它有严重的局限性:

除非你是写一个命令行工具,您必须添加额外的代码来确保请求不会运行在应用程序的主线程。

抽象的支持请求,要求提供身份验证。

没有修改的方式响应缓存或接受服务器的默认行为重定向。

重要:如果你检索数据同步,您必须确保代码的问题永远不能运行在应用程序的主线程。网络运营可以任意长时间才能完成。如果你试图在主线程上执行这些网络同步操作,操作将应用程序的执行,直到数据块已经完全收到,发生错误,或请求超时。这将导致糟糕的用户体验,可以导致iOS终止应用程序。

如果请求成功,返回请求的内容作为一个NSData对象和一个NSURLResponse对象请求返回的引用。如果NSURLConnection无法检索URL,此方法返回零和任何可用NSError实例引用适当的参数。

如果请求需要身份验证的连接,有效身份证件必须已经在NSURLCredentialStorage对象是可用的或必须提供所请求的URL的一部分。如果无法获得凭据或无法验证,URL加载系统会发送NSURLProtocol子类处理连接continueWithoutCredentialForAuthenticationChallenge:消息

当尝试遇到服务器同步连接重定向,定向总是肯定的。同样地,数据存储在缓存的响应根据默认提供的协议实现支持。

