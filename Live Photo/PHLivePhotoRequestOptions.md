#PHLivePhotoRequestOptions

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/Photos/Reference/PHLivePhotoRequestOptions_Class/index.html#//apple_ref/occ/instp/PHLivePhotoRequestOptions/progressHandler)

翻译人:王谦 翻译日期:2015.9.29  审核人:每①天都是↗开始 审核日期:2015.10.25


>重要的
>
>这是一个初步的技术开发文档 API 。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实施的软件，应与最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供未来的 API 或技术。


| 继承自         |   遵从协议     |    导入语句     |
|   --------   | :--------  |:----    |
| NSObject PHLivePhotoRequestOptions|NSCopying <br>NSObject    |   @import Photos;      |
|          |       |    可用性 |
|          |      |  可用 在 iOS 9.1 和以后  |


从一个 [PHImageManager]() 对象请求 Live Photo 表现可用的图片的时候,你使用一个`PHLivePhotoRequestOptions`对象指定选项。Live Photo 是一幅照片,包括其捕获片刻之前和之后的运动和声音。



##`指定图像请求选项`

[deliveryMode]() 属性(在新的 iOS 9.1)

所请求的 Live Photo 质量具有优先权。


####演示
>SWIFT
>
>var deliveryMode: PHImageRequestOptionsDeliveryMode

>OBJECTIVE-C
>
>
>@property(nonatomic, assign) PHImageRequestOptionsDeliveryMode deliveryMode

####论述

使用这个属性告诉照片快速提供一张照片(可能牺牲图像质量),提供一个高质量的 Live Photo (可能牺牲速度),或者如果需要的话,可以自动提供。看 [PHImageRequestOptionsDeliveryMode]。

[PHImageRequestOptionsDeliveryMode]:
https://developer.apple.com/library/prerelease/ios/documentation/Photos/Reference/PHImageRequestOptions_Class/index.html#//apple_ref/c/tdef/PHImageRequestOptionsDeliveryMode

####可用性

可用在 iOS 9.1 和以后


##`从 iCloud 获取图像数据`

**1.**[networkAccessAllowed]() 属性(在新的 iOS 9.1)

一个布尔值指定有无照片会从 iCoud 下载请求 Live Photo。


####演示
>SWIFT
>
>var networkAccessAllowed: Bool

>OBJECTIVE-C
>
>
>@property(nonatomic, assign, getter=isNetworkAccessAllowed) BOOL networkAccessAllowed


####论述

如果是 `YES`,所请求的 Live Photo 数据是没有存储在本地设备当中,从 iCloud 下载图片数据。下载进度需待通知,使用 [progressHandler]() 属性提供一个 block 周期性调用图片下载。如果是 `NO`(默认的), Live Photo 的数据是不在本地设备,[PHImageResultIsInCloudKey]() 值在结果处理程序的信息字典表明数据不可用,除非你允许网络访问。



####可用性

可用在 iOS 9.1 和以后


<br>**2.**[progressHandler]() (在新的 iOS 9.1)



####演示
>SWIFT
>
>var progressHandler: PHAssetImageProgressHandler?

>OBJECTIVE-C
>
>
>@property(nonatomic, copy) PHAssetImageProgressHandler progressHandler


####论述

如果请求的 Live Photo 不在本地设备上,你会用 `networkAccessAllowed` 属性启动下载,图片调用你的 block 定期报告进度和允许你取消下载。



####可用性

可用在 iOS 9.1 和以后