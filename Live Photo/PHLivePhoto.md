#PHLivePhoto

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/Photos/Reference/PHLivePhoto_Class/index.html#//apple_ref/doc/uid/TP40016581)

翻译人:王谦 翻译日期:2015.9.29  审核人:每①天都是↗开始 审核日期:2015.10.25


>重要的
>
>这是一个基础的技术开发文档和 API 。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实现的软件，应改用最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供测试版的 API 或技术。


| 继承自        |   遵从协议     |    导入语句     |
|   --------   | :--------  |:----    |
| NSObject PHLivePhoto |NSCopying <br>NSObject  NSSecureCoding    |   @import Photos;       |
|          |       |    可用性 |
|          |      |  可用 在 iOS 9.1 和以后  |

 
  Live Photo 是一张照片,通过一个支持相机应用的设备来捕获,它包括了拍照前后的声音和动作。一个 PHLivePhoto 对象代表了组合了动作声音和图像数据的这样一张照片。使用这个类从用户的库中来引用 Live Photos(获取 [PHAsset]() 和 [PHImageManager]() 类),加载可显示的 Live Photo 对象 (如通过社交网络照片共享),和分配Live Photos [PHLivePhotoView]() 显示对象。

 
 `PHLivePhoto` 类服务的角色 Live Photos 作为静态图像用户界面图像类服务。一个`UIImage` 对象表示没有数据图像文件加载,而是一个现成的图像可以显示在 view-similarly,`PHLivePhoto` 对象代表了一种 Live Photo 使用 [PHLivePhotoView]() 来显示运动和声音对象,不进入照片库或构成照片的数据资源。(工作与生活照片元素的照片库,使用 [PHAsset]() 类。与数据文件构成生活照片,使用[PHAssetResource]() 类。)
 
 
##`检查 Live Photo `


[size]() 属性(在新的 iOS 9.1)

用像素表示的照片的大小(只读)

####论述
>SWIFT
>
>var size: CGSize { get }

>OBJECTIVE-C
>
>@property(readonly, nonatomic) CGSize size

####可用性

可用在 iOS 9.1 和以后


##从数据文件加载 Live Photo


**1.**[+requestLivePhotoWithResourceFileURLs:placeholderImage:targetSize:contentMode:resultHandler:]() (在新的iOS 9.1)


从指定的源文件中异步加载一个  Live Photo。

####论述
>SWIFT
>
	class func requestLivePhotoWithResourceFileURLs(_ fileURLs: [NSURL],
                               placeholderImage image: UIImage?,
                                     targetSize targetSize: CGSize,
                                    contentMode contentMode: PHImageContentMode,
                                  resultHandler resultHandler: (PHLivePhoto?,
                                      [NSObject : AnyObject]) -> Void) -> PHLivePhotoRequestID

>OBJECTIVE-C
>
	+ (PHLivePhotoRequestID)requestLivePhotoWithResourceFileURLs:(NSArray<NSURL *> *)fileURLs
                                            placeholderImage:(UIImage *)image
                                                  targetSize:(CGSize)targetSize
                                                 contentMode:(PHImageContentMode)contentMode
                                               resultHandler:(void (^)(PHLivePhoto *livePhoto,
                                                                       NSDictionary *info))resultHandler
                                 
                                       
####参数                                                                                                           
| fileURLs | Live Photo 的URL构成的数组,可以通过使用[PHAssetResource]() 类获得|
|---:|:---|  
| image |在还没有完全加载和验证完的照片|
| targetSize |Live Photo 返回的尺寸大小。通过 [CGSizeZero]() 获取所请求的 Live Photo 的原始大小。|
| contentMode |一个选择如何适应图像的纵横比要求的尺寸。详情,请参阅`PHImageContentMode`。|
| resultHandler | 一个 block 被称为图像加载完成时,提供所请求的 Live Photo 或请求的状态信息。<br><br> block 接受以下参数:<br><br> result:Live Photo 的请求对象。<br><br> info:字典提供的信息请求的状态。见图片结果信息 Keys 可能的 keys 和 values。|


####返回值

一个数值标识符的请求。如果你需要在完成之前取消请求,通过标识符 [cancelLivePhotoRequestWithRequestID:]() 方法。

####论述

使用这个方法加载 Live Photo 显示对象来自来自之前图片库导出的数据文件。举一个例子,一个社交网络应用程序能使用 [PHAssetResource]() 类恢复文件数据,在一个用户库构成一个 Live Photo 和上传它们到一个服务器。然后在另一个用户的设备上,这个应用程序下载那些文件数据和使用这个方法再创建一个 Live Photo 对象使用 [PHLivePhotoView]() 类显示。

>注意
>
>从用户照片库表现一个  Live Photo 资源来代替获得的一个 `PHLivePhoto` 对象。使用 [PHAsset]() 类定位资源和[PHImageManager]() 类获取资源配置 的 Live Photo 显示的数据。

>代替从照片库中导入 Live Photo,使用 [PHAssetCreationRequest]() 类。


这个方法是异步的。图片加载,验证,和准备数据在一个后台线程,然后调用你的 resultHandler block 准备好显示 Live Photo 对象。像 PHImageManager 类中类似的方法,不止一次可以调用结果处理程序 block,提供一个低质量的 Live Photo 对象(包括静态图像的图像参数),随后提供完整的运动和声音内容的 Live Photo。如果[PHLivePhotoInfoIsDegradedKey]() 值结果处理程序信息的字典是肯定的,照片会再次调用结果处理程序。

这种方法只能加载一个 `PHLivePhoto` 对象从同一组文件导出之前捕获的资源 Live Photo。当你使用这个方法,照片确认文件和元数据加载,可以作为 Live Photo。如果图片不能从指定的文件加载 Live Photo,结果参数结果处理程序块为`nil`,和信息字典包含一个[NSError]() 对象描述错误。


####可用性

可用在 iOS 9.1和以后

<br>**2.**[+ cancelLivePhotoRequestWithRequestID:]() (在新的 iOS 9.1)

取消一个异步请求。

####演示
>SWIFT
> 
>class func cancelLivePhotoRequestWithRequestID(_ requestID: PHLivePhotoRequestID)

>OBJECTIVE-C
> 
> +(void)cancelLivePhotoRequestWithRequestID:(PHLivePhotoRequestID)requestID     

####参数
| requestID |取消数值标识符。|
|---:|:---|                                             


####论述

从资源文件异步加载一个  Live Photo 的时候你可以使用 [requestLivePhotoWithResourceFileURLs:placeholderImage:targetSize:contentMode:resultHandler:]() 方法,这个方法返回一个数值标识符请求。在完成之前取消请求,提供标识符的时候调用`cancelLivePhotoRequestWithRequestID:`方法。

####可用性

可用在 iOS 9.1和以后


##`数据类型`

[PHLivePhotoRequestID]()(在新的 iOS 9.1)

一个数值标识符异步 Live Photo 加载请求。

####演示
>SWIFT
> 
>typealias PHLivePhotoRequestID = Int32

>OBJECTIVE-C
> 
> typedef int32_t PHLivePhotoRequestID;


####论述

如果你需要在完成之前取消一个请求通过这个标识符 [cancelLivePhotoRequestWithRequestID:]() 方法。


####重要声明
>OBJECTIVE-C
>
>@import Photos;

>SWIFT
>
>import Photos

####可用性

可用在 iOS 9.1和以后

##`常量`

**1.**[Image Request Identifiers]()

返回一个异步请求[ PHLivePhotoRequestID]() 标识符特殊值。

####演示
>OBJECTIVE-C
>
>static const PHLivePhotoRequestID PHLivePhotoRequestIDInvalid = 0;


####常量

* `PHLivePhotoRequestIDInvalid `

无法取消一个异步 Live Photo 加载。

<br>**2.** [Result Handler Info Dictionary Keys]()

试图描述一个 Live Photo 信息,用于信息字典结果处理程序提供的[requestLivePhotoWithResourceFileURLs:placeholderImage:targetSize:contentMode:resultHandler:]() 方法。


####演示
>SWIFT
>
>let PHLivePhotoInfoErrorKey: String
<br>let PHLivePhotoInfoIsDegradedKey: String
<br>let PHLivePhotoInfoCancelledKey: String
>

>OBJECTIVE-C
>
>extern NSString * const PHLivePhotoInfoErrorKey;
<br>extern NSString * const PHLivePhotoInfoIsDegradedKey;
<br>extern NSString * const PHLivePhotoInfoCancelledKey;


####常量

* `PHLivePhotoInfoErrorKey `

 试图加载请求 Live Photo 时发生一个错误。
 
 [requestLivePhotoWithResourceFileURLs:placeholderImage:targetSize:contentMode:resultHandler:]() 方法验证文件及其元数据加载,可以作为 Live Photo。如果照片不能从指定的文件加载 Live Photo ,结果参数结果处理程序块 nil,和这个关键信息字典包含一个 [NSError]() 描述错误的对象。
 
 可用在 iOS 9.1和以后
 
 
 
* `PHLivePhotoInfoIsDegradedKey `

 一个布尔( [NSNumber]() )值显示结果来表示是否 Live Photo 是一个低质量的替代请求的 Live Photo。


 如果是 YES,结果参数的 resultHandler block 包含一个低质量的Live Photo ,照片将再次调用结果处理程序 Live Photo 给你提供完整的运动和声音内容的 Live Photo。如果没有,照片已经提供了所有可能的数据,不会再给你调用的结果处理程序。
 
  可用在 iOS 9.1和以后
  
  
* `PHLivePhotoInfoCancelledKey `

 
 一个布尔([NSNumber]())值指示是否 Live Photo 加载请求被取消了。


 如果你调用 [cancelLivePhotoRequestWithRequestID:]() 方法取消请求,照片调用您的结果处理程序块，这是一个值的值。


 可用在 iOS 9.1和以后