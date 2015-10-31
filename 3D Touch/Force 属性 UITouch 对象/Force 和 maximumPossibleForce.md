#3D Touch APIs

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITouch_Class/index.html#//apple_ref/occ/instp/UITouch/force)

翻译人:王谦 翻译日期:2015.9.23 审核人:ibcker 审核日期:2015.10.25


**1.**[force]()属性

触摸的力度,其中值为1.0时表示平均接触的力度(系统的预定值,不是用户特有的)。(只读)


####演示
>SWIFT
>
>var force: CGFloat { get }

>OBJECTIVE-C
>
>@property(nonatomic, readonly) CGFloat force


####论述

支持 3D Touch 的设备上可用此属性。在 runtime 检查设备是否支持 3D Touch,读[forceTouchCapability] 属性的值在任何对象的特性集合到你的应用环境与特征。更多的信息,看[UITraitEnvironment Protocol Reference]和 [UIForceTouchCapability]() 列举在 [UITraitCollection Class Reference]。
 
 
 [forceTouchCapability]:
 https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/occ/instp/UITraitCollection/forceTouchCapability


[UITraitEnvironment Protocol Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitEnvironment_Ref/index.html#//apple_ref/doc/uid/TP40014306


[UITraitCollection Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UITraitCollection_ClassReference/index.html#//apple_ref/doc/uid/TP40014202

####可用性

可用在 iOS 9.0 和以后

####另行参阅

[maximumPossibleForce]()

<br>
**2.**[maximumPossibleForce]()

最大合适的按压力度。(只读)

####演示
>SWIFT
>
>var maximumPossibleForce: CGFloat { get }

>OBJECTIVE-C
>
>@property(nonatomic, readonly) CGFloat maximumPossibleForce

####论述

这个属性的值是足够高,为力值属性提供一个较宽的动态范围。


支持 3D Touch 的设备上可用此属性。在 runtime 检查设备是否支持3D Touch,读[forceTouchCapability] 属性的值在任何对象的特性集合到你的应用环境与特征。更多的信息,看[UITraitEnvironment Protocol Reference]和 [UIForceTouchCapability]() 列举在 [UITraitCollection Class Reference]。
 
 
####可用性

可用在 iOS 9.0 和以后

####另行参阅

[force]()