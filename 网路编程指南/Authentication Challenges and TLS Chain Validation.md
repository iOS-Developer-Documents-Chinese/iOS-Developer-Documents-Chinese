身份验证的证明和 TLS 链验证
===

[原文地址](https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Conceptual/URLLoadingSystem/Articles/AuthenticationChallenges.html#//apple_ref/doc/uid/TP40009507-SW1)

翻译人:王谦 翻译日期:2015.10.11

一个`NSURLRequest` 对象常常遭遇一个身份验证的证明,或从服务器请求凭证连接。NSURLSession,NSURLConnection,当一个请求遇到一个身份验证的证明的时候 `NSURLDownload `类通知他们的代理。

>重要的:URL 加载系统类不调用他们的代理处理请求证明除非服务器响应包含一个`WWW-Authenticate`头。


##决定怎么去响应一个身份验证的证明

如果一个 `NSURLRequest` 对象需要身份认证,这个方法提供了 你的应用变化取决于请求由一个 NSURLSession 对象来证明,一个 `NSURLConnection`对象,或者 `NSURLDownload` 对象:

* 如果请求是和一个 `NSURLSession` 对象关联的,全部的身份认证请求是通过代理,不管身份验证类型。

* 如果请求是`NSURLConnection` 或 `NSURLDownload` 对象,对象代理接收一个[connection:canAuthenticateAgainstProtectionSpace:](或`download:canAuthenticateAgainstProtectionSpace:`)消息。这是允许代理对服务特性进行分析,包含它的协议和身份验证方法,在尝试验证它之前。如果你的代理是不准备验证服务器的防护空间,你可以返回 NO,和系统试图从用户的钥匙链中进行身份认证信息。

* 如果代理`NSURLConnection` 或 `NSURLDownload`对象没有实现[connection:canAuthenticateAgainstProtectionSpace:](或`download:canAuthenticateAgainstProtectionSpace:`)方法和使用客户端身份认证防护空间或服务器信赖身份认证,如果你返回 NO 系统运转。如果你返回 YES 系统表现其他全部的身份认证类型。

[connection:canAuthenticateAgainstProtectionSpace:]:
https://developer.apple.com/library/prerelease/watchos/documentation/Foundation/Reference/NSURLConnectionDelegate_Protocol/index.html#//apple_ref/occ/intfm/NSURLConnectionDelegate/connection:canAuthenticateAgainstProtectionSpace:


下一个,如果你代理同意处理身份认证和没有可用的有效认证信息,任何一个部分请求 URL 或在共享 [NSURLCredentialStorage],代理接收到以下消息:

 [URLSession:didReceiveChallenge:completionHandler:]

 [URLSession:task:didReceiveChallenge:completionHandler:]

 [connection:didReceiveAuthenticationChallenge:]

`download:didReceiveAuthenticationChallenge:`

[NSURLCredentialStorage]:
https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSURLCredentialStorage_Class/index.html#//apple_ref/occ/cl/NSURLCredentialStorage

[URLSession:didReceiveChallenge:completionHandler:]:
https://developer.apple.com/library/prerelease/watchos/documentation/Foundation/Reference/NSURLSessionDelegate_protocol/index.html#//apple_ref/occ/intfm/NSURLSessionDelegate/URLSession:didReceiveChallenge:completionHandler:

[URLSession:task:didReceiveChallenge:completionHandler:]:
https://developer.apple.com/library/prerelease/watchos/documentation/Foundation/Reference/NSURLSessionTaskDelegate_protocol/index.html#//apple_ref/occ/intfm/NSURLSessionTaskDelegate/URLSession:task:didReceiveChallenge:completionHandler:

[connection:didReceiveAuthenticationChallenge:]:
https://developer.apple.com/library/prerelease/watchos/documentation/Foundation/Reference/NSURLConnectionDelegate_Protocol/index.html#//apple_ref/occ/intfm/NSURLConnectionDelegate/connection:didReceiveAuthenticationChallenge:

在继续连接命令,代理有三个选择:

* 提供身份认证证书
* 尝试继续没有凭证。
* 取消身份认证证明。

帮助纠正正确的行动,通过 [NSURLAuthenticationChallenge] 实例方法
触发包含关于身份认证证明的信息,有多少次的证明 ,任何先前尝试凭证,`NSURLProtectionSpace` 需要认证信息,和发送证明。

 [NSURLAuthenticationChallenge]:
 https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSURLAuthenticationChallenge_Class/index.html#//apple_ref/occ/cl/NSURLAuthenticationChallenge
 
 
 如果身份认证证明有尝试认证之前都失败了(举个例子,在服务器上如果用户改变他或他的密码),你可以试图通过调用在身份认证证明`proposedCredential` 来获得证书。代理可以使用这些认证信息填充一个会话显示给用户。
 
 调用在身份认证证明的 `previousFailureCount`返回之前身份认证的全部编号。包含那些来自不同身份认证的协议。代理可以提供这些信息给用户,是否提供以前的失败的凭证,或试图限制的最大数量身份认证。
 
##响应一个身份认证证明

以下是三种方式你可以响应的`connection:didReceiveAuthenticationChallenge:`代理方法。

###提供的凭证

试图认证,通过服务器从预期的应用程序应该创建一个`NSURLCredential` 对象和身份认证信息。你可以确定服务器身份认证方法通过调用[authenticationMethod]在防护空间提供的身份认证证明。一些身份认证方法通过 `NSURLCredential` 支持的是:

* HTTP 基础身份认证(`NSURLAuthenticationMethodHTTPBasic`)要求一个用户名和密码。提示用户输入必要的信息,并创建一个`NSURLCredential`对象[credentialWithUser:password:persistence:]。

[credentialWithUser:password:persistence:]:
https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSURLCredential_Class/index.html#//apple_ref/occ/clm/NSURLCredential/credentialWithUser:password:persistence:

* HTTP 身份认证摘要(`NSURLAuthenticationMethodHTTPDigest`),像基础的身份认证,需要一个用户名和一个密码。(自动产生的摘要。)提示用户输入必要的信息,并创建一个`NSURLCredential`对象和[credentialWithUser:password:persistence:]。

[credentialWithUser:password:persistence:]:
https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSURLCredential_Class/index.html#//apple_ref/occ/clm/NSURLCredential/credentialWithUser:password:persistence:


* 客户端身份认证凭证(`NSURLAuthenticationMethodClientCertificate`)需要系统标识和所有证书需要在服务器上进行身份验证。创建一个`NSURLCredential` 对象和[credentialWithIdentity:certificates:persistence:]。

[credentialWithIdentity:certificates:persistence:]:
https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSURLCredential_Class/index.html#//apple_ref/occ/clm/NSURLCredential/credentialWithIdentity:certificates:persistence:


* 服务器身份认证信任(`NSURLAuthenticationMethodServerTrust`)需要一个信赖的防护空间提供的身份验证的证明。创建一个`NSURLCredential`对象和[credentialForTrust:]。

[credentialForTrust:]:
https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Classes/NSURLCredential_Class/index.html#//apple_ref/occ/clm/NSURLCredential/credentialForTrust:


你在创建 `NSURLCredential` 之后:

* `NSURLSession` 通过对戏身份认证证明发送使用 提供完成的处理 block。

* `NSURLConnection`和`NSURLDownload`,通过对象身份认证证明发送[useCredential:forAuthenticationChallenge:]。

[useCredential:forAuthenticationChallenge:]:
https://developer.apple.com/library/prerelease/watchos/documentation/Cocoa/Reference/Foundation/Protocols/NSURLAuthenticationChallengeSender_Protocol/index.html#//apple_ref/occ/intfm/NSURLAuthenticationChallengeSender/useCredential:forAuthenticationChallenge:


##一个身份认证的例子

清单 6-1 中所示的实现响应身份认证通过创建一个`NSURLCredential`实例所提供的用户名和密码应用程序的首选项。如果身份验证失败之前,取消认证挑战并通知用户。

清单 6-1 举一个例子使用 `connection:didReceiveAuthenticationChallenge:`代理方法\

	-(void)connection:(NSURLConnection *)connection
        didReceiveAuthenticationChallenge:(NSURLAuthenticationChallenge *)challenge
	{
    if ([challenge previousFailureCount] == 0) {
        NSURLCredential *newCredential;
        newCredential = [NSURLCredential credentialWithUser:[self preferencesName]
                                                 password:[self preferencesPassword]
                                              persistence:NSURLCredentialPersistenceNone];
        [[challenge sender] useCredential:newCredential
               forAuthenticationChallenge:challenge];
    } else {
        [[challenge sender] cancelAuthenticationChallenge:challenge];
        // inform the user that the user name and password
        // in the preferences are incorrect
        [self showPreferencesCredentialsAreIncorrectPanel:self];
    }
	}
	
如果代理没有执行`connection:didReceiveAuthenticationChallenge:`需要请求身份认证,有效身份证件必须已经在 URL 中必须提供凭证存储或请求的 URL 的一部分。如果证书不可用或如果他们无法进行身份验证,一个`continueWithoutCredentialForAuthenticationChallenge:`消息是通过潜在的方式执行。


##执行自定义 TLS 链验证

在 NSURL 家庭的 API,TLS 链验证是通过你的应用程序代理方法处理,但是提实例供用户(或你的应用程序)身份认证的认证信息到服务器,你的应用程序检查服务器提供 TLS 握手期间的凭证,然后告诉 URL 加载系统,它是否应该接受或拒绝这些凭证。

如果你需要用非标准的方式来进行链验证（例如接受一个特定的自签名证书的测试),你的应用程序必须做以下的:

* `NSURLSession`,执行[URLSession:didReceiveChallenge:completionHandler:]或[URLSession:task:didReceiveChallenge:completionHandler:]任何一个代理方法。如果你执行两者,session-level 方法负责处理验证。

* `NSURLConnection`和`NSURLDownload`,执行[connection:canAuthenticateAgainstProtectionSpace:]或`download:canAuthenticateAgainstProtectionSpace:`方法和返回 YES 如果防护空间有一个身份认证类型`NSURLAuthenticationMethodServerTrust`。

然而,执行[connection:didReceiveAuthenticationChallenge:]或`download:didReceiveAuthenticationChallenge:`方法处理身份认证。

在你的身份认证之内处理代理方法, 你应该检查看如果证明防护空间有一个身份认证类型`NSURLAuthenticationMethodServerTrust`,如果这样,获得`serverTrust` 来自防护空间的信息。

额外的细节和代码片段(基于`NSURLConnection`),阅读[Overriding TLS Chain Validation Correctly]。

[Overriding TLS Chain Validation Correctly]:
https://developer.apple.com/library/prerelease/watchos/documentation/NetworkingInternet/Conceptual/NetworkingTopics/Articles/OverridingSSLChainValidationCorrectly.html#//apple_ref/doc/uid/TP40012544