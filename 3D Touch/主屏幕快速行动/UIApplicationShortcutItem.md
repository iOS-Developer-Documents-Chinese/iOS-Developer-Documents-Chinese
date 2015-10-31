#UIApplicationShortcutItem

[原文地址](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutItem_class/index.html#//apple_ref/occ/cl/UIApplicationShortcutItem)

翻译人:王谦 翻译日期:2015.9.26  审核人:ibcker 审核日期:2015.10.25



>重要的
>
>这是一个初步的API开发文档。苹果提供此信息，以帮助您规划将要采用的技术和编程接口。这些信息可能发生变化，根据本文件实施的软件，应与最终的操作系统软件和最终文档进行相应测试。新版本的文档可能提供新的 API 或技术。


| 继承自        |   符合     |    导入语句     |
|   --------   | :--------  |:----    |
| NSObject UIApplicationShortcutItem UIMutableApplicationShortcutItem |NSCopying <br>NSMutableCopying     NSObject      |   @import UIKit;       |
|          |       |    可用性 |
|          |      |  可用 在 iOS 9.0 和以后  |


一个应用程序的快捷方式,仅仅调用一个主屏幕动态快捷操作,指定你的应用程序用户发起的动作。


在一个支持 3D Touch 的设备中,一个用户作通过按压你的应用程序主屏幕上的图标触发的快捷操然后选择一个快捷操作。你的应用程序代理接收程序的快捷操作。


在注册之前它们和你应用程序的对象,你必须在`UIApplicationShortcutItem `初始化的时候指定特性实例化。你的应用程序对象快捷操作注册是不可变的。


##在你的应用程序中注册一组动态快捷操作


在主屏幕上注册一个动态快捷操作,共享你设置[shortcutItems]()的值和包含一组明确的动态主屏幕快捷操作。



##改变你应用程序的动态快捷操作

改变你的应用程序主屏幕上的动态快捷操作,通过设置一个新的[shortcutItems]()属性值来取代你的用用程序对象。作为一个方便使用的注册快捷操作,这个类是一个可变的子类,[UIMutableApplicationShortcutItem]()。阐述一个使用[mutableCopy]() 方法的代码片段,连同可变的快捷操作,改变动态主屏幕快捷操作上的标题:
>
>OBJECTIVE-C
>
>NSArray < UIApplicationShortcutItem * > *existingShortcutItems = [[UIApplication sharedApplication] shortcutItems];<br>
<br>UIApplicationShortcutItem *anExistingShortcutItem = [existingShortcutItems objectAtIndex: anIndex];<br>
<br>NSMutableArray < UIApplicationShortcutItem * > *updatedShortcutItems = [existingShortcutItems mutableCopy];<br>
<br>UIMutableApplicationShortcutItem *aMutableShortcutItem = [anExistingShortcutItem mutableCopy];<br>
<br>[aMutableShortcutItem setLocalizedTitle: @“New Title”];<br>
<br>[updatedShortcutItems replaceObjectAtIndex: anIndex withObject: aMutableShortcutItem];<br>
<br>[[UIApplication sharedApplication] setShortcutItems: updatedShortcutItems];

>SWIFT
>
>let existingShortcutItems = UIApplication.sharedApplication().shortcutItems ?? []
<br>let anExistingShortcutItem = existingShortcutItems[anIndex]<br>
<br>var updatedShortcutItems = existingShortcutItems<br>
<br>let aMutableShortcutItem = anExistingShortcutItem.mutableCopy() as! UIMutableApplicationShortcutItem<br>
<br>aMutableShortcutItem.localizedTitle = “New Title"<br>
<br>updatedShortcutItems[anIndex] = aMutableShortcutItem<br>
<br>UIApplication.sharedApplication().shortcutItems = updatedShortcutItems

##`动态 vs 静态 快捷操作`

虽然是不可变的,一个 `UIApplicationShortcutItem ` 实例是被认为经过动态区分,它来自一个你指定的构建时的静态快捷操作。

* 定义一个主屏幕快捷操作,使用这个类。你创建动态快捷操作代码,在构建时注册你的应用程序对象。

*在你的 Xcode 对象 `Info.plist` 文件 [UIApplicationShortcutItems]()组中定义主屏幕静态快捷操作。
 描述在 [iOS Keys]() [ Information Property List Key Reference]() 章节。在你安装你的应用程序的时候系统注册静态快捷操作。
 
 
 在你按压一个主屏幕应用程序的图片时系统会显示一个快捷操作的数字范围。在这个范围里面设置显示快捷操作的标题,你的静态快捷操作是在第一时间显示,开始在目录的最顶端位置。如果你的静态项没有耗尽可允许的误差数,你可以另外定义个动态快捷操作使用这个类,然后一个或多个你的动态快捷操作就显示出来。
 
 
##应用程序启动和应用程序更新快捷操作注意事项

当你的用户选择一个主屏幕上的的快捷操作,系统启动或者恢复你的应用程序和 UIKit 调用 [application:performActionForShortcutItem:completionHandler:] 方法,在你的应用程序代理中。阅读[UIApplicationDelegate Protocol Reference]明确的方法信息,怎么确保方法在什么时候应该被调用。


[application:performActionForShortcutItem:completionHandler:]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/occ/intfm/UIApplicationDelegate/application:performActionForShortcutItem:completionHandler:


[UIApplicationDelegate Protocol Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationDelegate_Protocol/index.html#//apple_ref/doc/uid/TP40006786


用户安装你的应用程序但还没启动过，按压应用程序图标显示的只有静态快捷操作。在第一次启动之后,你的动态快捷操作(如果你定义在表单的任何地方以适应他们)将被显示。

如果一个用户更新了你的应用程序但是还没有启动过,按压图标显示的是你上一版本的动态快捷操作。优雅的处理这种情况的一种办法是在 userInfo 快捷操作提供应用程序版本信息的方案。


##`创建一个动态屏幕快捷操作`

**1.**[- initWithType:localizedTitle:]()

创建一个不可变的主屏幕快捷操作和一个没有用户版本的图标。

####演示
>SWIFT
>
>convenience init(type type: String,
  localizedTitle localizedTitle: String)
 
>OBJECTIVE-C
>
> -(instancetype)initWithType:(NSString *)type
              localizedTitle:(NSString *)localizedTitle


####参数

| type |要求,在主屏幕上定义应用程序快捷操作的类型。|
|---:|:---|
| localizedTitle | 要求,在主屏幕快捷操作上的用户版本标题。|


####返回值

创建一个不可变的主屏幕快捷操作和一个没有用户版本的图标。

####可用性

可用在 iOS 9.0 和以后版本


<br>**2.**[- initWithType:localizedTitle:localizedSubtitle:icon:userInfo:]() 指定初始化


创建一个静态的主屏幕快捷操作和用户版本标题,图标,和 `userInfo ` 字典。

####演示
>SWIFT
>
>   init(type type: String,
   localizedTitle localizedTitle: String,
localizedSubtitle localizedSubtitle: String?,
             icon icon: UIApplicationShortcutIcon?,
         userInfo userInfo: [NSObject : AnyObject]?)
 
>OBJECTIVE-C
>
> -(instancetype)initWithType:(NSString *)type
              localizedTitle:(NSString *)localizedTitle
           localizedSubtitle:(NSString *)localizedSubtitle
                        icon:(UIApplicationShortcutIcon *)icon
                    userInfo:(NSDictionary *)userInfo
                    
  
####参数

| type |必须,在主屏幕上定义应用程序快捷操作的类型。|
|---:|:---|
| localizedTitle | 必须,在主屏幕快捷操作上的本标题。|
| localizedSubtitle | 可选,在主屏幕快捷操作上的副标题。|
| icon |可选,在主屏幕快捷操作上的图标。|
| userInfo | 定义主屏幕快捷操作应用程序的信息,使用你应用程序落实的行动。`重要的`:如果参数的值没有可编码的表单属性这个方法就会引发异常。更多信息,看[Serializing Property Lists] 在 [Archives and Serializations Programming Guide] 和 看 [NSPropertyListSerialization Class Reference]。 一个常见的,重要的是指定一个应用程序版本的用户字典。如果用户安装或更新你的应用程序但是没有启动更新,按压你的主屏幕图标显示动态快捷操作预装版本,包含应用程序在用户信息里的版本,让你优雅地处理这种情况。|

[Serializing Property Lists]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Archiving/Articles/serializing.html#//apple_ref/doc/uid/20000952


[Archives and Serializations Programming Guide]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Archiving/Archiving.html#//apple_ref/doc/uid/10000047i


[NSPropertyListSerialization Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSPropertyListSerialization_Class/index.html#//apple_ref/doc/uid/TP40003716

####返回值
一个不可变的主屏幕快捷操作项和一个用户版本标题,可选的副标题,可选的图标,和可选的用户信息字典。


####可用性

可用在 iOS 9.0 和以后版本



##检查主屏幕动态快捷操作

**1.**[localizedTitle]() 属性


必须的,主屏幕动态快捷操作用户版本标题。(只读)


` 字典。

####演示
>SWIFT
>
>var localizedTitle: String { get }
 
>OBJECTIVE-C
>
>@property(nonatomic, copy, readonly) NSString *localizedTitle


####演示

每一个主屏幕动态快捷操作必须有一个用户版本标题。

如果标题排成一条线,这是系统显示的单线快捷操作。如果标题在线上太长,你不用指定一个 [localizedSubtitle]() 字符串,系统显示的标题会是两行。

使一个主屏幕动态快捷操作标题置于公共管理下,使用 [NSLocalizedString]()框架函数。描述在[Foundation Functions Reference](),连同一个` Localizable.strings ` 文件在你的 Xcode 工程中。


####可用性

可用在 iOS 9.0 和以后


<br>**2.** [localizedSubtitle]() 属性


可选,主屏幕动态快捷操作的用户版本的子标题。(只读)

####演示
>SWIFT
>
>var localizedSubtitle: String? { get }
 
>OBJECTIVE-C
>
>@property(nonatomic, copy, readonly) NSString *localizedSubtitle


####演示

如果你在快捷操作上指定一个子标题,然后系统显示标题在一个单线上(可能有一个省略字符),无论如何标题不要太长。

一个主屏幕动态快捷操作子标题置于公共管理下,使用 [NSLocalizedString]()框架函数。描述在[Foundation Functions Reference](),连同一个` Localizable.strings ` 文件在你的 Xcode 工程中。
  
####可用性

可用在 iOS 9.0 和以后

####另行参阅

[localizedTitle]()


<br>**3.**[type]() 属性

必须的,确定执行使用应用程序特性字符串快捷操作类型。



####演示
>SWIFT
>
>var type: String { get }
 
>OBJECTIVE-C
>
>@property(nonatomic, copy, readonly) NSString *type


####可用性

可用在 iOS 9.0 和以后


<br>**4.**[icon]() 属性

可选,主屏幕动态快捷操作的图标。(只读)

####演示
>SWIFT
>
>@NSCopying var icon: UIApplicationShortcutIcon? { get }
 
>OBJECTIVE-C
>
>@property(nonatomic, copy, readonly) UIApplicationShortcutIcon *icon


####论述

快捷操作图标是你提供参考的模版(仅仅是透明通道)图片的部分资产目录。更多信息看 [ UIApplicationShortcutIcon Class Reference](https://developer.apple.com/library/prerelease/ios/documentation/UIKit/Reference/UIApplicationShortcutIcon_Class/index.html#//apple_ref/doc/uid/TP40016500)。



####可用性

可用在 iOS 9.0 和以后


<br>**5.**[userInfo]() 属性

可选,你可以提供你的应用程序执行主屏幕快捷操作的用户的应用程序详情信息。(只读)

####演示
>SWIFT
>
>var userInfo: [String : NSSecureCoding]? { get }
 
>OBJECTIVE-C
>
>@property(nonatomic, copy, readonly) NSDictionary <NSString *,id<NSSecureCoding>> *userInfo

####论述

这个属性的字典的键和值必须符合 [NSSecureCoding]() 协议,表单必须可编码属性。如果不能,系统初始化快捷操作时会出现异常。更多表单必须可编码属性数据,看[Serializing Property Lists] 在 [Archives and Serializations Programming Guide] 和 看 [NSPropertyListSerialization Class Reference]。

[Serializing Property Lists]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Archiving/Articles/serializing.html#//apple_ref/doc/uid/20000952


[Archives and Serializations Programming Guide]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Conceptual/Archiving/Archiving.html#//apple_ref/doc/uid/10000047i


[NSPropertyListSerialization Class Reference]:
https://developer.apple.com/library/prerelease/ios/documentation/Cocoa/Reference/Foundation/Classes/NSPropertyListSerialization_Class/index.html#//apple_ref/doc/uid/TP40003716

####可用性

可用在 iOS 9.0 和以后