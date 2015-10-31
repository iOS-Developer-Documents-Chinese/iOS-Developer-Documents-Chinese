#PHLivePhotoViewDelegate

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/PhotosUI/Reference/PHLivePhotoViewDelegate_Protocol/index.html#//apple_ref/occ/intfm/PHLivePhotoViewDelegate/livePhotoView:didEndPlaybackWithStyle:)

翻译人:王谦 翻译日期:2015.9.29   审核人:每①天都是↗开始 审核日期:2015.10.25


>重要的
>
>这是一个基础的技术开发文档和 API 。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实现的软件，应改用最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供测试版的 API 或技术。



| 继承自        |   符合     |    导入语句     |
|   --------   | :--------  |:----    |
| 不适用| 不适用  |   @import Photos;       |
|          |       |    可用性 |
|          |      |  可用 在 iOS 9.1 和以后  |


`PHLivePhotoViewDelegate` 协议 [PHLivePhotoView]() 实例描述了发送的消息的响应回放事件,当开启的运动和声音内容相关的 Live Photo 。接收到消息,执行这个方法实现协议的一个控制器对象,并将该对象分配给 Live Photo 视图的代理属性。


##应对 Live Photo 回放事件

**1.**[-livePhotoView:willBeginPlaybackWithStyle:]()

通知代理 Live Photo 回放即将开始。

####演示
>SWIFT
>
>optional func livePhotoView(_ livePhotoView: PHLivePhotoView,
 willBeginPlaybackWithStyle playbackStyle: PHLivePhotoViewPlaybackStyle)
 
>OBJECTIVE-C
>
>
>-(void)livePhotoView:(PHLivePhotoView *)livePhotoView
willBeginPlaybackWithStyle:(PHLivePhotoViewPlaybackStyle)playbackStyle

####参数
| livePhotoView |Live Photo 的内容视图开始播放。|
|---:|:---|
| playbackStyle |是回放风格,指示内容是否能完全播放或短在预览。|


####可用性

可用在 iOS 9.1 和以后


<br>**2.**[- livePhotoView:didEndPlaybackWithStyle:]()

通知代理结束 Live Photo 的回放。

####演示
>SWIFT
>
>optional func livePhotoView(_ livePhotoView: PHLivePhotoView,
    didEndPlaybackWithStyle playbackStyle: PHLivePhotoViewPlaybackStyle)
 
>OBJECTIVE-C
>
>
>-(void)livePhotoView:(PHLivePhotoView *)livePhotoView
didEndPlaybackWithStyle:(PHLivePhotoViewPlaybackStyle)playbackStyle


####参数
| livePhotoView |结束  Live Photo 的内容回放。|
|--:|:---|
| playbackStyle |是回放风格,指示内容是否能完全播放或短在预览。|


####可用性

可用在 iOS 9.1 和以后