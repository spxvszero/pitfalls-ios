# pitfalls-ios



#### 2017-02-09
##### 较长字段 URL 编码的坑
有时候做网页分享时，需要生成一个 URL字符串，该URL 自由短短的几个字段，但每个字段的数据都很长，比如每个字段都是一个 JSON 结构，而该请求不方便用 POST 请求，如果直接传输该字符串，在移动客户端，可能没法打开（PC端往往可以），此时在 iOS 端又不直接提供 URL Encoding 的方法。

例：比如该 URL 要拼接 data=[]的字段 ;中括号内部是一个 JSON 数据字符串，最后分享出去的时候出现了加载失败的提示。

解决方式：对中括号内部的数据（包括中括号）进行 URL Encoding，为方便起见，对NSString 对象新建一个 URL Encoding 的类目，并实现该 URL Encoding 的方法。


    -(NSString *)urlEncodeUsingEncoding:(NSStringEncoding)encoding 
    {
    	return (NSString*)CFBridgingRelease(CFURLCreateStringByAddingPercentEscapes
    	(NUL,
    	(CFStringRef)self,
    	NULL,
    	(CFStringRef)@"!*'\"();:@&=+$,/?%#[]%",
    	CFStringConvertNSStringEncodingToEncoding(encoding)));
    }

encoding 往往使用 UTF-8 。

#### 2017-02-04
HTTPS 适配中的坑总结    
##### 1、 修改 CocoaPods 导入的库导致无法正常请求 Https 的情形
原则上，不会去修改 CocoaPods 导入的库，此乃大忌。    

如果到了万不得已的情形，一定要修改，可先把该库从 CocoaPodas 中移除，再重新拖入工程，并将该库引用的其他库以 CocoaPods 的形式引入,然后再对该库进行修改，这样可避免修改之后的代码看起来好像运行了，但其实没有运行的问题。

例：在使用 DTCoreText 图文混排库加载 Https 图片的时候使，DTLazyImageView 使用的是 NSURLSession ( iOS 9 or later) 或 NSURLConnection 进行图片加载，遗憾的是，DTCoreText 并未处理是否信任服务器的验证。导致自己无法不对 DTCoreText 库进行修改。    

在控制台上打印 error 信息如下：

    Error Domain=NSURLErrorDomain Code=-1202 "此服务器的证书无效。您可能正在连接到一个伪装成“banks.snaillove.com”的服务器，	这会威胁到您的机密信息的安全。" UserInfo={NSURLErrorFailingURLPeerTrustErrorKey=<SecTrustRef: 0x1741197d0>, 	NSLocalizedRecoverySuggestion=您仍要连接此服务器吗？, _kCFStreamErrorDomainKey=3, _kCFStreamErrorCodeKey=-9807, 	NSErrorPeerCertificateChainKey=(
    "<cert(0x10422b200) s: ***.com i: StartCom Class 1 DV Server CA>",
    "<cert(0x104228000) s: StartCom Class 1 DV Server CA i: StartCom Certification Authority>"
	), NSUnderlyingError=0x174445d60 {Error Domain=kCFErrorDomainCFNetwork Code=-1202 "(null)" 	UserInfo={_kCFStreamPropertySSLClientCertificateState=0, kCFStreamPropertySSLPeerTrust=<SecTrustRef: 	0x1741197d0>, _kCFNetworkCFStreamSSLErrorOriginalValue=-9807, _kCFStreamErrorDomainKey=3, 	_kCFStreamErrorCodeKey=-9807, kCFStreamPropertySSLPeerCertificates=(
    "<cert(0x10422b200) s: ***.com i: StartCom Class 1 DV Server CA>",
    "<cert(0x104228000) s: StartCom Class 1 DV Server CA i: StartCom Certification Authority>"
	)}}, NSLocalizedDescription=此服务器的证书无效。您可能正在连接到一个伪装成“banks.snaillove.com”的服务器，这会威胁到您的机密	信息的安全。, NSErrorFailingURLKey=https://***, 	NSErrorFailingURLStringKey= https://***, 	NSErrorClientCertificateStateKey=0}


#####1.1 在 CocoaPods 里直接修改    
现象：修改了之后无论如何无法请求成功，打了断点，当代码运行到断点所打之处，好像运行到该处了，但其实没有，修改了的代码也不起作用，很容易产生错觉，如果不仔细看 Xcode 上的调用栈很容易误认为就是调用到了该处，和没改之前一个模样。无论写了什么，无任何效用。

#####1.2 将该库移除 CocoaPods 再导入工程，再修改之后运行
现象：代码正常运行了，可正常加载。


#### 2017-01-09    
    
极光推送常遇见的坑总结    

1、badge 字段的坑    

推送数目的坑，App Icon右上角数目由后台推送的badge 字段的数值就是推送的数目，一般来讲如果不需要显示该数值的时候，只需要后台配置该值设置为0 即可，很多的应用都是让该值自动增长。极光推送该值得默认值目前貌似为2，所有不管后台推了多少条通知 App 端看到的数值一直都是2。    

解决方案，设置该值为  ‘+1’ 它就会自增长，其余的交由APP 端来做，这也是后台很容易遗漏之处之一。    


2、APNsProduction 设置的坑    

很多时候，当应用发布时，突然发现推送功能失效了，查了很久的资料也没差出个所以然，在调试阶段貌似好好的（这里的情况包括，比如先发了一个ad_hoc 测试包，发现无法推送，再用Xcode 往手机装了一下，推送OK）。    

但当传到App  Store 之后发现  都推不了了，在确保各种证书都没有问题的情况下，很有可能是后台忘了配置APNsProduction 的值，该值默认为false，当发布至App Store 之后该值应设置为true 这样App Store 上的版本就可以正常收到通知消息了。    


#### 2017-01-09
关于iOS https 证书签发者无效的问题

####一、问题：

  由于后台提供的 crt 证书文件安装后，会出现两种奇怪的现象，一部分人的电脑显示证书有效，
有部分人显示此证书签发者无效。所以推测是跟自己电脑钥匙串或者其他的配置有关，具体由哪个配置
引起的，仍未找到。有头绪的童鞋，欢迎一起来讨论研究下。


####二、解决办法：

   1.网上找到一个跟这个有些类似的博客，就是将该证书中的默认值“使用系统默认”，改成“始终信任”。
     完成后，原来红色的提示信息变成了“此证书已标记为受此账号信任”。这样就解决“此证书的签发者无效”的问题。
       
    博客地址：[http://jingyan.baidu.com/article/49711c617a540cfa441b7c86.html](http://jingyan.baidu.com/article/49711c617a540cfa441b7c86.html)
       (PS: 由于实测发现，不用证书也能访问成功，所以暂时还没想到怎么测试此方法的可行性。)


####三、另外，附上目前主流的两种支持https的方式：

在Xcode7.0之后,苹果废弃了NSURLConnection方法,数据请求使用NSURLSession,作为网络请求类第三方库使用量最大的AFN也及时的更新的新的版本——AFN 3.0版本。
新的版本的里废弃了基于NSURLConnection封装的AFHTTPRequestOperationManager，转而使用基于NSURLSession封装的AFHTTPSessionManager了。

1）支持https（校验证书）:



      // 1.初始化单例类
       AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
      // 注意写法的变化
          manager.securityPolicy=  [AFSecurityPolicy policyWithPinningMode:AFSSLPinningModeCertificate];

        // 2.设置证书模式
        NSString * cerPath = [[NSBundle mainBundle] pathForResource:@"xxx" ofType:@"cer"];
        NSData * cerData = [NSData dataWithContentsOfFile:cerPath];
        manager.securityPolicy = [AFSecurityPolicy policyWithPinningMode:AFSSLPinningModeCertificate withPinnedCertificates:[[NSSet alloc] initWithObjects:cerData, nil]];
        // 客户端是否信任非法证书
        mgr.securityPolicy.allowInvalidCertificates = YES;
        // 是否在证书域字段中验证域名
        [mgr.securityPolicy setValidatesDomainName:NO];
    
2）支持https（不校验证书）:

        // 1.初始化单例类
         AFHTTPSessionManager *manager = [AFHTTPSessionManager manager];
        // 2.设置非校验证书模式
        manager.securityPolicy = [AFSecurityPolicy policyWithPinningMode:AFSSLPinningModeNone];
        manager.securityPolicy.allowInvalidCertificates = YES;
        [manager.securityPolicy setValidatesDomainName:NO];

####四、测试结果：
          经程序测试发现，无论是使用方式一中的证书校验模式，还是方式二中的非证书校验模式，都能请求到数据。

####五、总结：
        如果项目不涉及支付等敏感数据，可以直接使用不校验证书的方式，仅配置https的基本配置即可。
>>>>>>> masterRemote

#### 2017-01-04   
替换最新版本科大讯飞出现的问题：    
错误提示：   

    duplicate symbol _OBJC_METACLASS_$_IFlyVoiceWakeuper in:
        /Users/hao123/Desktop/testPod/test111/test111/iflyMSC.framework/iflyMSC(IFlyVoiceWakeuper.o)
    duplicate symbol _OBJC_CLASS_$_IFlyVoiceWakeuper in:
        /Users/hao123/Desktop/testPod/test111/test111/iflyMSC.framework/iflyMSC(IFlyVoiceWakeuper.o)
    ld: 2 duplicate symbols for architecture armv7
    clang: error: linker command failed with exit code 1 (use -v to see invocation)


原因：    
   工程中的配置项 other linker flag 中配置了 -all_load ,导致了重复的目标文件被链接加载到可执行文件中。    
   
解决办法：      
   去掉 -all_load 即可。 
     
   
#### 2016-12-12

项目在繁体下有些图片无法显示的问题

这个问题涉及到国际化问题，先了解下我是国际化处理的方式。   
首先创建一个language.plist文件。里面包含了你需要国际化的语言。    
例如：   
key zh-HK value  zh-Hant-HK。    
key zh-TW value  zh-Hant-TW。    
key en    value  en    

获取对应的语言下的值    
define kLanguageDictionary [NSDictionary dictionaryWithContentsOfFile:[[NSBundle mainBundle] pathForResource:@"language" ofType:@"plist"]]

```objective-c 
if (!kLanguageDictionary[key]) {
        path = [[NSBundle mainBundle] pathForResource:@"en" ofType:@"lproj"];
}else
{
        path = [[NSBundle mainBundle] pathForResource:kLanguageDictionary[key] ofType:@"lproj"];
}

NSBundle * languageBundle = [NSBundle bundleWithPath:path];
//根据对应的Localizable.string来获取对应的语言显示。没有的话显示英文。
s = [languageBundle localizedStringForKey:translation_key value:@"" table:nil];
```

图片国际化利用图片的名字在对应的Localizable来声明图片。例如：   
zh-HK下   
"btn_control_active" = "btn_control_active_zh_hant";   
"btn_control_inactive" = "btn_control_inactive_zh_hant";   
en下   
"btn_control_active" = "btn_control_active_en";   
"btn_control_inactive" = "btn_control_inactive_en";   

在这种情况，如果图片名字没有写对，这个位置就不会显示对应的图片。


#### 2016-12-01
###iOS plist配置文件中存在未使用权限字段被拒问题
* 第一次被拒：
被拒原文：
```
Hardware required


We began the review of your app but are not able to continue because we need the associated hardware to fully assess your app features.

At your earliest convenience, please send the necessary hardware/accessory to the address below.

NOTE: Please include your app name and app ID in the shipment; failure to provide this information can delay the review process.

Additionally, it may take several business days for us to receive the hardware once it has been delivered to Apple.

Once you've shipped the hardware, please reply to this message with the shipping carrier and tracking information. We will resume the review of your app upon receiving the hardware.

IMPORTANT: for non-US Developers

To avoid delays with US Customs, please provide the following information with your shipment (required for all radio-frequency devices imported in the US):

Please use FCC form 740 for details on how to provide this information.

Please make sure you also provide any required demo account information, including passwords, in the App Review Information section for your app in iTunes Connect.

To provide demo account information:

```
大致意思是需要寄硬件设备到美国配合审核。
由于之前审核都是不需要寄设备审核，经沟通后发现info.plist 中存在对后台音乐播放的声明，然后去掉了权限字段：
```
<key>NSAppleMusicUsageDescription</key>
<string>此 App 需要您的同意才能获取本地音乐</string>
```

* 第二次被拒：  
被拒原文：
```

Upon further review, we still found that your app declares support for audio in the UIBackgroundModes key in your Info.plist but did not include features that require persistent audio.

Next Steps

The audio key is intended for use by apps that provide audible content to the user while in the background, such as music player or streaming audio apps. Please revise your app to provide audible content to the user while the app is in the background or remove the "audio" setting from the UIBackgroundModes key.

Additional Information

If you have difficulty reproducing a reported issue, please try testing the workflow described in Technical Q&A QA1764: How to reproduce bugs reported against App Store submissions.

If you have code-level questions after utilizing the above resources, you may wish to consult with Apple Developer Technical Support. When the DTS engineer follows up with you, please be ready to provide:
- complete details of your rejection issue(s)
- screenshots
- steps to reproduce the issue(s)
- symbolicated crash logs - if your issue results in a crash log

```

大致意思是当前APP仍支持后台音乐。   
经查找发现，```Required background modes``` 中确实存在```App plays audio or streams audio/video using AirPlay``` 字段 删除后即可。   
####总结：
  在提交app store版本时，应确认使用的功能，一定要加上对应的字段。此外，应确保配置文件plist中没有多余的权限字段，即没有使用到的功能，应及时删除对应字段，否则app会被拒。
  
  
  
#### 2016-10-17


iOS 10 却少对隐私权限的描述被 app store 被拒
app store 上传app 时因为没有对使用功能的权限进行描述而被拒解决方法总结
拒绝时而收到的邮件内容

您好：
 
感谢您就上传 App 构建版本的问题联系 Apple Developer Program Support。

从 iOS 10 开始，访问任何应用中受保护的数据类都需要使用目的字符串（purpose string），包括所有第三方库在您的应用里使用这些受保护的数据类的用途。如果您收到有关您无法识别的数据类缺少目的字符串（purpose string）的提示，请咨询您的第三方库供应商，了解其对该受保护数据类的使用。
有关更多详细信息，请参阅以下资源：
App Programming Guide for iOS 中的 ”Supporting User Privacy“
https://developer.apple.com/library/ios/documentation/iPhone/Conceptual/iPhoneOSProgrammingGuide/ExpectedAppBehaviors/ExpectedAppBehaviors.html#//apple_ref/doc/uid/TP40007072-CH3-SW6
Information Property List Key Reference 中的 “Cocoa Keys”
https://developer.apple.com/library/prerelease/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html
WWDC 2016: Engineering Privacy for Your Users (usage description keys discussed at 29:14)
https://developer.apple.com/videos/play/wwdc2016/709/?time=1754
希望以上信息对您有帮助。如果您需要再次联系我们，欢迎致电或通过电邮的方式跟我们联系，并提供案例编号：100045823076。我们的办公时间是北京时间周一至周五，09:00 至 17:00，电话号码是 4006-701-855。
我们很乐意给您提供帮助，感谢您参与我们的开发者计划。 
Queenie
Apple Inc. 

在工程的info.plist文件中需要添加获取用户隐私权限的key ，并且要对key 进行描述，因为没有对权限的key 进行描述，所以才会被拒，在plist文件中代码如下
<key>NSAppleMusicUsageDescription</key>
<string>此 App 需要您的同意才能获取本地音乐</string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string>App需要您的同意,才能访问蓝牙</string>


这样就不会被拒了

#### 2016-10-11
iOS 10 打开相册闪退bug   

报错提示信息：   
2016-10-11 10:33:53.178931 BluetoothColorLampMeshLamp[745:235978] [access] This app has crashed because it attempted to access privacy-sensitive data without a usage description.  The app's Info.plist must contain an NSPhotoLibraryUsageDescription key with a string value explaining to the user how the app uses this data.

解决办法：   
在plist中添加字段：      
Privacy - Camera Usage Description         
cameraDesciption      

Privacy - Photo Library Usage Description      
photoLibraryDesciption      

#### 2016-10-09
炬力返回有表情的设备名字时，返回设备名字为空的问题。

具体分析：

蓝牙搜索返回接口：
/*!
 *  @method foundPeripheral:advertisementData:
 *
 *  @param peripheral 蓝牙设备对象
 *  @param advertisementData 包含蓝牙设备广播信息或者蓝牙搜索回应数据的字典容器。
 *
 *  @abstract 搜索蓝牙时返回的蓝牙设备信息。
 *
 *  @discussion 请及时保存<i>peripheral</i>引用，系统随时可能将其释放。
 *
 *  @seealso scanStart
 */
-(void)foundPeripheral:(CBPeripheral *)peripheral advertisementData:(NSDictionary *)advertisementData;
当返回有表情的设备时：
peripheral返回的name不为空，有设备名字并且带有表情符号。
advertisementData返回的设备名字为空，其他数据正常。
所以，当用设备名字时最好用peripheral的name字段。不要用advertisementData来获取设备名。

#### 2016-09-30
CocoaLumberjack 更新最新版本v3.0.0编译报错问题：          

报错信息 DDFileLogger.h:429:40: Token is not a valid binary operator in a preprocessor subexpression   

FOUNDATION_SWIFT_SDK_EPOCH_AT_LEAST      

出错原因：      
     最新CocoaLumberjack v3.0.0版本最低支持Xcode版本为 Xcode 8 或以上版本。   
     
解决办法：   
   要么升级Xcode版本，要么恢复回 CocoaLumberjack v2.4.0版本   


#### 2016-08-16
AppStore被驳回问题：   

问题描述：   
发件人 Apple
Design Preamble

Your app includes an update button or alerts the user to update the app. To avoid user confusion, app version updates must utilize the iOS built-in update mechanism. 

Specifically, your app has a tappable version number section.

We've attached screenshot(s) for your reference.

Next Steps

Please remove the update feature from your app. To distribute a new version of your app, upload the new app binary version into the same iTunes Connect record you created for the app's previous version. Updated versions keep the same Apple ID, iTunes Connect ID (SKU), and bundle ID as the original version, and are available free to customers who purchased a previous version. 

Resources

To create new versions of your app, please see Replacing Your App with a New Version in the iTunes Connect Developer Guide.   

被拒原因：        
   界面上的版本号显示位置可以点击，容易误导用户以为可以点击升级。   
 
解决版本：   
   将该位置设置为不可点击状态。    

#### 2016-08-12
点击应用图标出现卡顿的问题：
 据分析可能是因为应用长时间在后台，手机系统会回收长时间不被使用的资源，
当再次点击图标启动的时候，系统会分配资源给应用，当一个应用初始化占用资源太多时，就有可能出现卡顿显现
一个应用启动的过程：start->(加载framework，动态静态链接库，启动图片，Info.plist，pch等)->main函数->UIApplicationMain函数
 下面给出优化的方案：
（1） 程序应用需要用到的一些资源如，plist      文件，mp3文件，字体ttf等文件不要随意放到项目的其它文件中，请放到项目中专门用于存放资源的文件夹Supporting Files
（2）pch文件配置时候不要写入太多预编译的头文件，因为这会影响性能，所以Xcode6以上的版本才开始默认不使用pch 文件
（3）图片资源请尽量放到Images.xcassets，因为Images.xcassets会压缩图片资源，而放到放到mainBudnle里面，图片没有被压缩
（4）layer的渲染不宜使用的过多
 （5） 在 appDeleage.m 文件的- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
方法里开启了另一条线程 来初始化某些第三方组件和初始化其他资源：例如
-(BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{

 // 等待此方法完成一秒后开启另一条线程初始化其他资源
 dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1 * NSEC_PER_SEC)), dispatch_get_global_queue(0, 0), ^{
// 需要初始化的资源，
1、友盟插件
2、喜马拉雅sdk
3、日志打印等等 组件
 });
[self.window makeKeyAndVisible];
return YES;
}

#### 2016-08-11
点击应用图标出现卡顿的问题：
经过测试，didFinishLaunchingWithOptions的方法和卡顿是有直接关系的。
测试方法，去掉了didFinishLaunchIngWithOptions的一些处理。就没有重现这个卡顿现象（应该说卡顿时间特别短）。卡顿出现一般是长时间不打开不用这个应用。（大概30分钟以上，出现几率比较大）
目前项目修改方式，是在didFinishLaunchingWithOptions里添加一个子线程，对不涉及ui的操作放到子线程里处理。
处理方式为：

    dispatch_after(dispatch_time(DISPATCH_TIME_NOW, (int64_t)(1 * NSEC_PER_SEC)), dispatch_get_global_queue(0, 0), ^{
    //内容处理（不能是更新ui，因为更新ui要在主线程）
    }

#### 2016-08-10

在ipod上安装不了测试包，在其他手机上可以正常安装的测试包的问题：
在配置文件中有个属性Build Active Architecture Only。
他代表了你要安装的架构。如果你设置为yes，这样debug速度上会更快，它会编译你需要的那一个。设置为no时会编译所有的架构。
而编译的架构是向下兼容的。所以，有时候你会看到你设置的是yes，有的手机也可以安装上去。但架构不同的还是无法安装。
所以，一般这个属性是debug为yes，release为no。一般默认就是这样的配置，尽量不要修改这个属性。

#### 2016-07-15

1、外部链接打不开的问题：    
情景：当在运用中使用［［UIApplication sharedApplication］openURL：［NSURL urlWithString：urlString］］去打开一个外部链接时，往往都能正常的打开，凡事都有例外，也有怎么都打不开的情形。    
i、当urlString存在空格的时候，比如第一个字符为空格，那么，打开外部链接的命令将不生效。    
ii、当urlString 存在中文字符时也打不开。    

成因分析：    
构建URL的时候（不管是＋号方法还是减号方法构建的，只要第一个字符为空格，可能苹果为了提高效率考虑，直接返回URL空对象，所以打开外部链接不生效）。    

解决方案  
1、去空格    
2、统一UTF－8 编码(防止有中文字符的情况)    
代码：    

    NSString *urlString = [NSString stringWithString:string];
    
    NSString *URL = [urlString stringByReplacingOccurrencesOfString:@" " withString:@""];
    
    NSURL *url = [NSURL URLWithString:[URL stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding]];
    
    return url;



#### 2016-07-06
1、UIWebView 获取当前页面URL的坑
问题描述：    
iOS 系统中，苹果提供的UIWebView控件功能中，能直接调用的方法寥寥无几，有一些方法甚至不管怎么调，他的值都不会变，比如pageCount,和pagelength等等属性，如果想在原生控件的基础上获取WebView中的某些信息，比如URL、页数等等，会相当的头疼，（安卓的却是可以直接获取）。    

解决方法：    
由于webView可以和Javascript直接进行交互，无疑给我们在解决WebView问题上开了一片天，弥补了WebView本身的缺陷，无论想获取什么，只要把Js代码写上，再用WebView加载即可，前提是知道怎么写，比如点击返回的时候，获取当前Html URL，可这么写：    
    
        NSString * currentUrl = [_webView stringByEvaluatingJavaScriptFromString: @"window.location.href"];
这样当前页面的URL就获取到了，page页数往往在字符串的最后的位置，有了这一工具，我们想获取什么，只要把对应的JS 代码加载即可，不得不说，在处理iOS中WebView问题上，Javascript真的很神奇。

#### 2016-06-21
[Lewanny](https://github.com/Lewanny)

####问题描述: 
项目中在获取本地iPods音乐时, 获取不全, 例如本机音乐100首, 只能获取到10多首.

####问题分析：
如下,这句代码 `[query setGroupingType: MPMediaGroupingPodcastTitle];`使用了MPMeidaQuery的setGroupingType方法将MPMeidaQuery的属性设置为了播客标题.<br>
所以在获取的时候导致没有博客信息的曲目获取不到.


```objective-c 
    MPMediaQuery *query = [[MPMediaQuery alloc] init];
    [query setGroupingType: MPMediaGroupingPodcastTitle];
    NSArray *albums = [query collections];
    for (MPMediaItemCollection *album in albums) {
        MPMediaItem *representativeItem = [album representativeItem];
        NSString *artistName =
        [representativeItem valueForProperty: MPMediaItemPropertyArtist];
        NSString *albumName =
        [representativeItem valueForProperty: MPMediaItemPropertyAlbumTitle];
        NSLog (@"%@ by %@", albumName, artistName);
     
        NSArray *songs = [album items];
     }
```
####解决方案:
使用如下方法获取, 不需要设置MPMeidaQuery的属性, 其默认为MPMediaGroupingTitle, 即按照Title获取即可.

```objective-c 
    MPMediaQuery *myPlaylistsQuery = [MPMediaQuery songsQuery];
    NSArray *playlists = [myPlaylistsQuery collections];
    for (MPMediaPlaylist *playlist in playlists) {
    
        NSArray *array = [playlist items];
        for (MPMediaItem *song in array) {
            NSString *songTitle = [song valueForProperty: MPMediaItemPropertyTitle];
        }
    }
```
***



#### 2016-06-20
[Lewanny](https://github.com/Lewanny)

#####问题描述: 
当我们从网络Clone或者Download下来的Demo是使用CocoaPods来管理第三方库时, 这时候直接编译是不通过的, 这是
我们就要使用Terminal进到该工程文件的根目录下, 然后执行:pod update 命令来更新本地第三方类库。
但使用pod uodate 或者pod install时有事无法完成更新, 报错如下:<br>   
[!] The dependency `SDWebImage` is not used in any concrete target.<br>
[!] The dependency `Masonry` is not used in any concrete target.
.... 

#####问题分析：
podfile升级之后到最新版本，pod里的内容必须明确指出所用第三方库的target，否则会出现The dependency `` is not used in any concrete target这样的错误。

#####解决方案:
编辑Podfile文件内容指定其Target, 其中“MyApp”为你的工程名, use_frameworks, 个别需要用到，比如reactiveCocoa: 

```objective-c 
platform :ios, '8.0'

def pods
  pod 'SDWebImage'
  pod 'Masonry'
end

target 'MyApp' do
  pods
end
```
***


#### 2016-06-02
1.tableview 中用xib创建cell时，第一个cell位置有时候会与顶部有一段距离   

去掉该顶部距离方法：   

创建 tableHeaderView ，通过改变tableHeaderView的大小来改变与顶部的距离。    

<p code>
UIView *tableHeaderView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 1, 0.1)];    // 设置高度为0.1
tableView.tableHeaderView = tableHeaderView;
</p>

注意：此处若直接设置headview 为空，则无任何作用    
<p>
tableView.tableHeaderView = nil;
</p>
=======

#### 2016-05-12



1、iOS工程图片导出的问题

情景：当有新项目需要在原本原有的项目进行二次开发的时候，设计师往往会找我们要我们之前工程的图片，而这时我们往往会想到对工程目录当中的图片或者Imagee.xcassets 里面的图片进行复制过来再传给他（她）。可最终的结果是，设计师后来给的图片和之前使用的图片出现了不同程度的突差别，导致在开发过程中不尽人意。

解析：如果全部图片放在了工程目录下，复制的时候，直接复制过去，问题不是很大；可如果图片是放在了Imagee.xcassets文件夹下，而你直接把这个Imagee.xcassets里面的文件全部复制给了设计师，那么设计师的工作量估计不小。Xcode 下图片只要放入了Imagee.xcassets，Xcode自动回为图片包上了一个.imageset的文件夹，并生成了对应的content.json文件，设计师拿到的时候，要把图片一张张抽出来，工作量可想而知。这种方发绝不是一种好的做法。

最佳做法是：先给我们之前的工程打一个包（测试包或者，App Store 包）都行，用归档使用工具进行打开，这时候你会发现，我们的包变成了Payload的文件夹，打开这个Payload文件夹会看到一个.app文件，接着显示包内容你会看到工程所有工程当中使用的图片的在这里了纯粹的图片，没有使用的图片不在，（不行你自己实验一下），只需要把这里边的图片全部拷出来，发给设计师，大功告成。

原理：Xcode其实在打包的时候，会对应用当中的资源进行检测，没有用到的资源不会放到包里面，所以，在这里找到的图片一定涵盖了工程中使用的所有图片。在这一点上，感觉Xcode还是挺强大的。


#### 2016-05-10（四）

1.iOS 添加变声传音时的出现的Bug和解决方案 

情景：集成变声传音功能时, 若当前在播放音乐, 会出现录音时音乐从听筒播放的问题.

原因：当程序中出现同时使用硬件情景时, 如播放音乐时, 进行变声传音的录制和播放, 应考虑到多种调用触发情景, 结合需求进行合理的逻辑判断和处理.

解决方法：当播放音乐状态下开始实时传音的录制和播放, 对当前正在播放音乐进行处理, 然后在进行实时传音的相关操作, 完成后再对音乐状态进行恢复.

***
>>>>>>> 7508da625785c407598524f0ce79fd87f87dfc4e

#### 2016-04-15 (五)    

1、iOS 中分页视图控制器的管理手动调用viewController 中的viewWillAppear，viewDidAppear,等懒加载方法的调用。    

描述：在一些比较复杂的页面里，有时候我们不希望在一个页面里面写很多的控件，而希望把一部分的控件放在一个视图控制器管理起来，这样我们就只需要将这几个视图控制器协调好、组织好，至于每个视图控制器里面写什么外层不用理会。很多时候我们希望在ScrollerView中放几个ViewController的View，这样方便我们切换ScrollerView的View的时候，重新把回调设置给其他的视图控制器，如果操作不当View对应的ViewController的viewWillAppear，viewDidAppear,等等懒加载方法根本不会调用，经过不断的研究测试，总结到了以下几点，当要调用者些个懒加载的方法时，我们要将视图控制器的View从父视图中先移除，当需要一个新的视图控制器需要显示的时候，很多时候我们需要注意：
1、首先应把当前显示的视图控制器的View  从父视图中移除.
2、将要显示的视图控制器，调用将要移到父视图控制器的方法。
3、将新的视图控制器的View  添加到父视图控制器上面。
4、将要显示的视图控制器，调用已经移到父视图控制器的方法。
5、将当前将要显示的视图控制器设置为当前视图控制器。
     ……
代码表示如下：

```
    [_currentVc.view removeFromSuperview];
    [self addChildViewController:newController];
    //这里可是适当设置一些viewController的View的属性    
    [newController willMoveToParentViewController:self];
    [self.scrollerView addSubview:newController.view];
    [newController didMoveToParentViewController:self];
    self.currentVc = newController;
```

经过了这些步骤，我们就可以通过ScrollerView翻页来切换视图控制了。    


#### 2016-04-07（四）


1.iOS 数据库升级导致程序崩溃 

情景：应用程序更新到最新版本，运行出现程序崩溃

原因：为了使应用程序文件命名规范化，工程里的.h 和.m等文件类前缀改为LX，当应用重新启动后，程序读取残留下来的，未更改类前缀名字的数据，
与更改名字后的类有冲突，导致程序崩溃。

解决方法：由于该数据只是用作缓存，不包含有用户的信息，因此在新版本程序中，在读取数据之前，可以使用NSFileManager把旧的数据库文件删掉，重新创建新的数据库文件，即可解决崩溃问题
#### 2016-03-31(四)

1.iOS 闪屏图片对应用分辨率的影响  
情景1：  
很多时候我们会发现（尤其是使用6/6S或是6/6sPlus的时候），同尺寸安卓设备和iOS设备，iOS设备的分辨率明显却差了很多，有些模糊的感觉，给人很low的感觉，甚至有时候会疑惑  
情景2：
有时候当我们使用设备（6/6S，6/6sPlus）进行开发新项目或是调试的时候，会发现这样的一种现象：我们所要写的控件，图片或是字体，明显的比设计师给的效果图或是切图有很大的突兀，有时候甚至觉得设计师弄错了，很多时候，我们放大或是缩小我们的字体控件等等以使得UI效果和设计师提供的原型一致。  
情景3：
有时候版本发App Store或是打测试包在不同的设备上运行的时候，我们会发现有些尺寸的屏幕闪屏没有，譬如4S，6/6s等等，表现为在设备运行的时候一片漆黑一闪而过。  
以上的问题，其实都是由于各种iOS设备屏幕的闪屏没有添加完毕（可能缺少某一种），app在运行的时候会根据闪屏去唯一确定分辨率，如果6/6s plus 的闪屏缺少，系统会去选择同iphone 5 屏幕尺寸一致的图片作为更大尺寸设备屏幕的闪屏，系统默认的分辨率就是iphone 5手机分辨率，所以我们在6/6s上看到的效果就会差很多，当我们重新添加上这些缺少的图片以后，6/6s plus的分辨率就恢复正常了。

#### 2016-05-5(四)

2. ipod 摇一摇，无震动效果   

原因：   不支持震动的平台（ipod touch）   
震动的代码：AudioServicesPlaySystemSound(kSystemSoundID_Vibrate);   
iphone 效果：每个调用都会生成一个简短的1~2秒的震动。   
ipod 效果： 该调用不执行任何操作，但也不会发生错误！   
    
