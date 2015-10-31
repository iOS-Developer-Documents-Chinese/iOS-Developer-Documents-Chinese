#3D Touch APIs

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/UserExperience/Conceptual/Adopting3DTouchOniPhone/3DTouchAPIs.html#//apple_ref/doc/uid/TP40016543-CH4-SW1)

翻译人:王谦 翻译日期:2015.9.23 审核人：kyson.cn 审核日期:2015.10.28

iOS 有三种情况会用到3D Touch:

* 主屏幕API
在主屏幕上按压程序icon，可以看到应用程序弹出了类似快捷方式的图标,这个图标中的条目可以加速用户与应用程序的交互。

* 应用程序内部
在应用程序内部的3d touch 可以让你的app更加容易使用。ios9 中提供了 peek 快速行动 API press-enabled 替换应用程序的touch-and-hold 行动 api。

* 针对WebView的预览等功能的api
允许您启用 system-mediated 预览的 HTML 链接到一个新的view。

* UITouch force properties
允许你添加自定义的3d touch 到你的app

不管你采用哪些 APIs,你都必须app 运行时 检查 3D Touch  的可用性。


##检查 3D Touch  的可用性



在程序运行时如果要检查设备是否支持 3D Touch,可以通过调用UITraitCollection类的 [forceTouchCapability](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/occ/instp/UITraitCollection/forceTouchCapability) 方法。UITraitCollection 是iOS8引入的新类，一开始是为了表征size class,在iOS9 中又增加了一些新的方法来支持3d touch的配置。可以通过实现[UITraitEnvironment](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitEnvironment_Ref/index.html#//apple_ref/doc/uid/TP40014306)协议来获取UITraitCollection实例。UITraitEnvironment还提供了监听用户是否在应用程序运行时关闭了 3D Touch,你只需要在[traitcollectiondidchange](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitEnvironment_Ref/index.html#//apple_ref/occ/intfm/UITraitEnvironment/traitCollectionDidChange:) 做相应处理即可。

##主屏幕API


按压应用程序的icon后出来的类似快捷方式菜单，它可以快速的你可以写在app配置文件里，或者通过函数动态设置。

* **静态设置** 在Info.plist文件中添加数组 [UIApplicationShortcutItems](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/index.html#//apple_ref/occ/cl/UIApplicationShortcutItem)，这样用户在安装应用之后第一次启动就可以使用。


* **动态设置** iOS9中在[UIApplication](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/cl/UIApplication)中添加了新的属性 [shortcutItems](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/instp/UIApplication/shortcutItems)来实现3D Touch。 [shortcutItems](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplication_Class/index.html#//apple_ref/occ/instp/UIApplication/shortcutItems)是包含[UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/index.html#//apple_ref/occ/cl/UIMutableApplicationShortcutItem)的数组，通过它你可以里面的[UIApplicationShortcutIcon](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/index.html#//apple_ref/occ/cl/UIApplicationShortcutIcon)等属性定义快捷方式的图标等详细信息。需要注意的是[UIApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/index.html#//apple_ref/occ/cl/UIMutableApplicationShortcutItem)是不可变的，如果你想更改其属性，可以使用[UIMutableApplicationShortcutItem](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/index.html#//apple_ref/occ/cl/UIMutableApplicationShortcutItem)。


需要注意的是，主屏幕上的快捷方式个数不能超过4个。如果你设置的个数超过4个,那么系统首先显示快速静态api,并从上到下显示。如果你没有设置静态API,你可以设置动态API,也可以实现同样的效果。


主屏幕静态和动态快速行动这两种api最多可以显示两行，每一行可以由文本和图标构成。文本可以添加一些诸如对齐，截断等其他属性。

主屏幕快速动作特性支持 Voice Over。


更多有关实现主屏幕API,可以参考下列资料:

* [UIApplicationShortcutItem Class Reference]

* [UIMutableApplicationShortcutItem Class Reference]

* [UIApplicationShortcutIcon Class Reference]

* [UIApplicationShortcutItems] in [Information Property List Key Reference]

* [ApplicationShortcuts: Using UIApplicationShortcutItem]-(示例代码)



[UIApplicationShortcutItem Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/index.html#//apple_ref/doc/uid/TP40016501


[UIMutableApplicationShortcutItem Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/index.html#//apple_ref/doc/uid/TP40016502


[UIApplicationShortcutIcon Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/index.html#//apple_ref/doc/uid/TP40016500



[UIApplicationShortcutItems]:
https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/iPhoneOSKeys.html#//apple_ref/doc/uid/TP40009252-SW36

[Information Property List Key Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/General/Reference/InfoPlistKeyReference/Introduction/Introduction.html#//apple_ref/doc/uid/TP40009247


[ApplicationShortcuts: Using UIApplicationShortcutItem]:
https://developer.apple.com/library/prerelease/ios/samplecode/ApplicationShortcuts/Introduction/Intro.html#//apple_ref/doc/uid/TP40016545



##`UIKit Peek and Pop`

所谓peek和pop就是在应用运行中添加的按压手势。通过配置配置UIViewController的 peek 和pop 三属性,可以为当前的控制器提供一个弹出框,这个弹出框的作用就是下一个页面的内容或导航。


为了实现peek 和 pop ,iOS 9 提供的SDK包括:

* 为当前Controller添加了注册和取消注册3D Touch 的新方法

* 为新的 Controller添加3D Touch


您可以配置一个用于预览控制的peek ,达到深度链接到您的应用程序的目的。你也可以通过向上刷 peek 获得 peek 的操作。


支持peek快速行动, iOS 9 SDK 包括:

* 新的 [UIPreviewAction] 和 [UIPreviewActionGroup]类

* 新的 [UIPreviewActionItem] 协议


[UIPreviewAction]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/index.html#//apple_ref/occ/cl/UIPreviewAction


[UIPreviewActionGroup]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/index.html#//apple_ref/occ/cl/UIPreviewActionGroup

[UIPreviewActionItem]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/index.html#//apple_ref/occ/intf/UIPreviewActionItem



有关实现 peek 和 pop 和实现 peek 快速行动,阅读下列材料:

* 描述 [registerForPreviewingWithDelegate:sourceView:] 和 [unregisterForPreviewingWithContext:] 方法在 [UIViewController Class Reference]

[registerForPreviewingWithDelegate:sourceView:]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/registerForPreviewingWithDelegate:sourceView:

[unregisterForPreviewingWithContext:]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/occ/instm/UIViewController/unregisterForPreviewingWithContext:


[UIViewController Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewController_Class/index.html#//apple_ref/doc/uid/TP40006926


* [UIViewControllerPreviewing Protocol Reference],是3D Touch-enabled 视图控制器的上下文对象的协议

[UIViewControllerPreviewing Protocol Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerPreviewing_Protocol/index.html#//apple_ref/doc/uid/TP40016568


* [UIViewControllerPreviewingDelegate Protocol Reference],这个协议中的两个方法是对peek和pop的处理，peek用于响应用户按压，pop用于响应弹出的控制器

[UIViewControllerPreviewingDelegate Protocol Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIViewControllerPreviewingDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40016569


* [UIPreviewAction Class Reference],描述了 peek 方法

[UIPreviewAction Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewAction_Class/index.html#//apple_ref/doc/uid/TP40016565


* [UIPreviewActionGroup Class Reference],用于描述submenu-like peek 快速分组行动

[UIPreviewActionGroup Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionGroup_Class/index.html#//apple_ref/doc/uid/TP40016566


* [UIPreviewActionItem Protocol Reference],它描述了接口采用 peek 方法和组织

[UIPreviewActionItem Protocol Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIPreviewActionItem_Protocol/index.html#//apple_ref/doc/uid/TP40016567


* [ViewControllerPreviews: Using the UIViewController previewing APIs]-(代码示例)


[ViewControllerPreviews: Using the UIViewController previewing APIs]:
https://developer.apple.com/library/prerelease/ios/samplecode/ViewControllerPreviews/Introduction/Intro.html#//apple_ref/doc/uid/TP40016546


##`Web View Peek 和 Pop`

在 web views想要启用3dtouch，你需要先检测peek和pop的`allowsLinkPreview` 属性。在 iOS 9,推荐使用新的在 [WKWebView] (在 WebKit framework )类中的这个属性或者 在老 [UIWebView] 类(在 UIKit framework)。


[WKWebView]:
https://developer.apple.com/library/prerelease/ios/documentation/WebKit/Reference/WKWebView_Ref/index.html#//apple_ref/occ/cl/WKWebView

[UIWebView]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIWebView_Class/index.html#//apple_ref/occ/cl/UIWebView


 [SFSafariViewController] 是safari浏览器使用的框架，这个框架默认实现了peek和pop

[SFSafariViewController]:
https://developer.apple.com/library/prerelease/ios/documentation/SafariServices/Reference/SFSafariViewController_Ref/index.html#//apple_ref/occ/cl/SFSafariViewController


##`Force 属性 UITouch 对象`

[UITouch]() 类有两个新的属性来实现3 d Touch: [force]() 和 [maximumPossibleForce]() 。第一次在iOS设备上,这些属性让你应用程序接收发现和应对 UIEvent 对象的压力触摸。


在iPhone上,触摸力量不同，可以得到3dtouch浮点值也是不一样的，可以根据这个特点来获取按压力量。


有关提供一个自定义实现 3D Touch 使用 force 值,阅读下列资料:

* 描述 [force] 和 [maximumPossibleForce]() 属性在 [UITouch Class Reference]。

* [TouchCanvas: Using UITouch efficiently and effectively]-(代码示范)

[maximumPossibleForce]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/index.html#//apple_ref/occ/instp/UITouch/maximumPossibleForce


 [force]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/index.html#//apple_ref/occ/instp/UITouch/force

[UITouch Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/index.html#//apple_ref/doc/uid/TP40006785

[TouchCanvas: Using UITouch efficiently and effectively]:
https://developer.apple.com/library/prerelease/ios/samplecode/TouchCanvas/Introduction/Intro.html#//apple_ref/doc/uid/TP40016561
