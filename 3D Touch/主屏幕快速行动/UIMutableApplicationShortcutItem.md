#UIMutableApplicationShortcutItem

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIMutableApplicationShortcutItem_class/index.html#//apple_ref/occ/cl/UIMutableApplicationShortcutItem)

翻译人:王谦 翻译日期:2015.9.26  审核人:ibcker 审核日期:2015.10.25



>重要的
>
>这是一个初步的API开发文档。苹果提供此信息，以帮助您规划将要采用的技术和编程接口。这些信息可能发生变化，根据本文件实施的软件，应与最终的操作系统软件和最终文档进行相应测试。新版本的文档可能提供新的 API 或技术。


| 继承自        |   符合     |    导入语句     |
|   --------   | :--------  |:----    |
| NSObject UIApplicationShortcutItem UIMutableApplicationShortcutItem |NSCopying <br>NSMutableCopying     NSObject      |   @import UIKit;       |
|          |       |    可用性 |
|          |      |  可用 在 iOS 9.0 和以后  |


一个可变应用程序的一项快捷方式。又称,冗长的，易变的主屏幕动态快捷操作,在你的应用程序指定一个用户发起的可配置的行动。这个类是一个[UIApplicationShortcutItem]()的子类,帮助你的注册工作,因此是不可变的,快捷操作。

在你的应用程序中怎样使用对象的信息。请看[UIApplicationShortcutItem Class Reference]。

[UIApplicationShortcutItem Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/index.html#//apple_ref/doc/uid/TP40016501


##检查一个可变的主屏幕,动态快捷操作

**1.**[localizedTitle]() 属性

这是必须的。用户看到的主屏幕上的动态快捷操作标题。

####演示
>SWIFT
>
>var localizedTitle: String

>OBJECTIVE-C
>
>@property(nonatomic, copy) NSString *localizedTitle

####论述

每个主屏幕上可变的快捷操作必须是用户可见的。

如果标题排成一条线,这是系统显示的单线快捷操作。如果标题在线上太长,你不用指定一个 [localizedSubtitle]() 字符串,系统显示的标题会是两行。

使一个主屏幕动态快捷操作标题置于公共管理下,使用 [NSLocalizedString]()框架函数。描述在[Foundation Functions Reference](),连同一个` Localizable.strings ` 文件在你的 Xcode 工程中。


####可用性
可用在 iOS 9.0 和以后

####另行参阅

[localizedSubtitle]()