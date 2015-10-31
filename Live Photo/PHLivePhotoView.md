#PHLivePhotoView

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/PhotosUI/Reference/PHLivePhotoView_Class/index.html#//apple_ref/c/tdef/PHLivePhotoViewPlaybackStyle)

翻译人:王谦 翻译日期:2015.9.23 审核人:每①天都是↗开始 审核日期:2015.10.25


>重要的
>
>这是一个基础的技术开发文档和 API 。苹果提供此信息，用来帮助您介绍将要计划采用的技术和编程接口。这些信息可能发生变化，根据本文件实现的软件，应改用最终的操作系统软件和最终文档进行相应测试。新版本的文档可以提供测试版的 API 或技术。



| 继承自        |   遵从协议     |    导入语句     |
|   --------   | :--------  |:----    |
| NSObject UIResponder <br>UIView <br>PHLivePhotoView |NSCopying <br>NSObject<br>UIAppearance<br>UIAppearanceContainer<br>UICoordinateSpace<br>UIDynamicItem<br>UIFocusEnvironment<br>UITraitEnvironment      |   @import PhotosUI;       |
|          |       |    可用性 |
|          |      |  可用 在 iOS 9.1 和以后  |


一个 `PHLivePhotoView `对象是显示描述一个 Live Photo 的一个视图,还包括捕获之前和之后片刻的运动和声音。之后获得[PHLivePhoto]() 对象(从照片库使用[UIImagePickerController]() 或 [PHAsset]() 和[PHImageManager]() 类,或者通过从照片库中导出的有用资源创建一个),使用一个  Live Photo 视图显示照片和控制播放的动作和声音内容。

默认情况下,一个 Live Photo 视图使用它自己的手势识别允许用户在照片应用程序中看到一个具有相同交互作用和视觉效果的实时照片的运动和声音内容。举个例子 - 定制一个手势识别,将其安装在一个你的应用程序的视图层次中适当的处理不同的事件 - 使用 [playbackGestureRecognizer]() 属性。


典型,应用程序不需要直接控制住照片回放。在某些情况下,然而,它可能是有用的简单动画视图向用户显示一幅 Live Photo。要做到这一点,使用 [startPlaybackWithStyle:]() 方法和[PHLivePhotoViewPlaybackStyleHint]() 选项。



##`选择一个 Live Photo 来显示`

[livePhoto]() 属性( 在新的 iOS 9.1 )

在视图上显示一个  Live Photo 。

####演示

>SWIFT
>
>var livePhoto: PHLivePhoto?

>OBJECTIVE-C
>
>@property(readwrite, nonatomic, strong) PHLivePhoto *livePhoto

####可用性

可用在 iOS 9.1 和以后



##`管理播放`

**1.**[playbackGestureRecognizer]() 属性( 在新的 iOS 9.1 )

一种控制 Live Photo 视图回放的手势识别。

####演示

>SWIFT
>
>var playbackGestureRecognizer: UIGestureRecognizer { get }

>OBJECTIVE-C
>
>@property(readonly, nonatomic, strong) UIGestureRecognizer *playbackGestureRecognizer


####论述

Live Photo 视图自动创建和安装这个手势识别器。使用定制的手势识别行为特性。举一个例子,你可以使用代理方法影响它如何与其他的手势识别,或安装在不同的视图，以确保在您的应用程序的视图层次中正确的事件处理。


####可用性

可用在 iOS 9.1 和以后


<br>**2.**[muted]()属性( 在新的 iOS 9.1 )

一个布尔值,确定视图中音频内容的 Live Photo。


####演示

>SWIFT
>
>var muted: Bool

>OBJECTIVE-C
>
>@property(readwrite, nonatomic, assign, getter=isMuted) BOOL muted


####论述

默认值是没有,表示视图中播放音频内容以及它的 Live Photo 的内容。

####可用性

可用在 iOS 9.1 和以后

##`应对事件回放`

[delegate]() 属性( 在新的 iOS 9.1 )

一个对象在 Live Photo 回放开始或结束的时候通知。


####演示

>SWIFT
>
>weak var delegate: PHLivePhotoViewDelegate?

>OBJECTIVE-C
>
>@property(readwrite, nonatomic, weak) id< PHLivePhotoViewDelegate > delegate


####可用性

可用在 iOS 9.1 和以后


##`手动播放活照片内容`

**1.**[- startPlaybackWithStyle:]() ( 在新的 iOS 9.1 )

开始一个在一个视图上 Live Photo 的回放。


####演示

>SWIFT
>
>func startPlaybackWithStyle(_ playbackStyle: PHLivePhotoViewPlaybackStyle)

>OBJECTIVE-C
>
>-(void)startPlaybackWithStyle:(PHLivePhotoViewPlaybackStyle)playbackStyle

####参数
| playbackStyle |选择多少 Live Photo 的运动和声音内容。看[PHLivePhotoViewPlaybackStyle]()。|
|---:|:---|


####论述

使用 playbackStyle 参数选择是否回放一个短暂的的运动和声音内容生活照片。

通常,应用程序不需要直接控制播放,因为现场照片视图提供了交互回放控制。使用这种方法只有在非交互式回放是适合的 - 例子,短暂的动画内容表明一个视图包含一个 Live Photo ,而不是一个静态图像。 

####可用性

可用在 iOS 9.1 和以后


**2.**[- stopPlayback]() ( 在新的 iOS 9.1 )

结束播放 Live Photo 内容的视图。

####演示

>SWIFT
>
>func stopPlayback()

>OBJECTIVE-C
>
>-(void)stopPlayback


####可用性

可用在 iOS 9.1 和以后


##`访问用户界面图标的 Live Photos`

[+ livePhotoBadgeImageWithOptions:]() ( 在新的 iOS 9.1 )

返回一个图标图片指定 Live Photo 语义的选择。


####演示

>SWIFT
>
>class func livePhotoBadgeImageWithOptions(_ badgeOptions: PHLivePhotoBadgeOptions) -> UIImage

>OBJECTIVE-C
>
>+(UIImage *)livePhotoBadgeImageWithOptions:(PHLivePhotoBadgeOptions)badgeOptions


####参数
| badgeOptions |一组选项,用于在应用程序的用户界面中使用一个Live Photo 指示的语义上下文。看[PHLivePhotoBadgeOptions]()。|
|---:|:---|

####返回值

图片的图标为指定的选项。


####论述

使用这种方法获得的图标图片用于界面元素补充 Live Photos。例如,您可以使用此方法在自定义可用浏览用户界面中所获得的图标来表示可用的是 Live Photos,而不是正常的静态照片,或显示当用户选择了分享Live Photos 有或没有它的运动和声音内容。




默认情况下，此方法返回一个单色图像适合用作模板的图像可以显示在一个特定的背景色彩。（使用 [UIImage]() 类创建模板的图像。）当你计划覆盖图标动画效果 Live Photos 的内容,增添[phlivephotobadgeoptionsovercontent]() 选项来获得图像（不适用于模板使用）,提供额外的背景之下。


####可用性

可用在 iOS 9.1 和以后


##`常量`

**1.**[PHLivePhotoViewPlaybackStyle]()

选项中有多少的动作和声音内容 Live Photo ,用于[startPlaybackWithStyle:]() 方法和消息视图的 [delegate]() 对象。

####演示

>SWIFT
>
>enum PHLivePhotoViewPlaybackStyle : Int {
    <br>case Undefined
    <br>case Full
    <br>case Hint
<br> }

>OBJECTIVE-C
>
>typedef enum: NSInteger {
   <br>PHLivePhotoViewPlaybackStyleUndefined = 0,
   <br>PHLivePhotoViewPlaybackStyleFull,
   <br>PHLivePhotoViewPlaybackStyleHint,
<br>} PHLivePhotoViewPlaybackStyle;


####常量

* `PHLivePhotoViewPlaybackStyleUndefined `

 这个值是无效的。

 可用在 iOS 9.1 和以后
 
* `PHLivePhotoViewPlaybackStyleFull `

 回放整个动作和声音内容,包括在开始和结束的过渡效应。


 这种风格的照片与在照片应用时看到的效果相匹配。

 可用在 iOS 9.1 和以后
 
 
* `PHLivePhotoViewPlaybackStyleHint `

 回放只有短暂的运动内容的生活照片,没有声音。

 这种风格匹配效应出现在各种 iOS 界面元素时提示用户可用的包含生活照片内容。

 可用在 iOS 9.1 和以后
 
 
####重要声明

####演示

>SWIFT
>
>@import PhotosUI;

>OBJECTIVE-C
>
>import PhotosUI


####可用性

可用在 iOS 9.1 和以后


<br>**2.**[PHLivePhotoBadgeOptions]()


选择的语义使用和显示象征可用 Live Photo 的风格,[livePhotoBadgeImageWithOptions:]() 所使用的方法。


####演示

>SWIFT
>
>struct PHLivePhotoBadgeOptions : OptionSetType {<br>
    <br>init(rawValue rawValue: UInt)
    <br>static var OverContent: PHLivePhotoBadgeOptions { get }
    <br>static var LiveOff: PHLivePhotoBadgeOptions { get }<br>
<br>}

>OBJECTIVE-C
>
>typedef enum : NSUInteger {
   <br>PHLivePhotoBadgeOptionsOverContent = 1 << 0,
   <br>PHLivePhotoBadgeOptionsLiveOff = 1 << 1,
<br>} PHLivePhotoBadgeOptions;

####常量

* `PHLivePhotoBadgeOptionsLiveOff `

 返回一个图标识别可用的额外的 Live Photo 内容是禁用的。

 例如,当实现一个选择器接口在一个社交网络分享照片,你可以使用这个图标指示当用户没有选择分享额外的运动和声音内容的现场照片。

 可用在 iOS 9.1 和以后
 

* `PHLivePhotoBadgeOptionsOverContent `

 返回在可变的图标使用在一个不同的背景上寻找以动画形式的 Live Photo 视图。

 默认情况下，该 [livephotobadgeimagewithoptions:]() 返回一个单色图像适合用作模板的图像,然后你可以适当的显示色彩与具体背景。如果背景内容是被占用,动画,或未知,添加这个选项而不是获得一个图标不适合用作模板图像）,为更好的可读性提供了额外的对比度。
 
  可用在 iOS 9.1 和以后
  
  
####重要声明

>OBJECTIVE-C
>
>@import PhotosUI;

>SWIFT
>
>import PhotosUI


####可用性

可用在 iOS 9.1 和以后