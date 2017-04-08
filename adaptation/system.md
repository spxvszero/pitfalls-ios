# 系统

## 1.https 问题
   
### 1.1 修改 CocoaPods 导入的库导致无法正常请求 Https

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

解决方法：

A.在 CocoaPods 里直接修改    
现象：修改了之后无论如何无法请求成功，打了断点，当代码运行到断点所打之处，好像运行到该处了，但其实没有，修改了的代码也不起作用，很容易产生错觉，如果不仔细看 Xcode 上的调用栈很容易误认为就是调用到了该处，和没改之前一个模样。无论写了什么，无任何效用。

B.将该库移除 CocoaPods 再导入工程，再修改之后运行
现象：代码正常运行了，可正常加载。

### 1.2 https 证书签发者无效

由于后台提供的 crt 证书文件安装后，会出现两种奇怪的现象，一部分人的电脑显示证书有效，
有部分人显示此证书签发者无效。所以推测是跟自己电脑钥匙串或者其他的配置有关，具体由哪个配置
引起的，仍未找到。有头绪的童鞋，欢迎一起来讨论研究下。

解决办法：

   1.网上找到一个跟这个有些类似的博客，就是将该证书中的默认值“使用系统默认”，改成“始终信任”。
     完成后，原来红色的提示信息变成了“此证书已标记为受此账号信任”。这样就解决“此证书的签发者无效”的问题。博客地址：[http://jingyan.baidu.com/article/49711c617a540cfa441b7c86.html](http://jingyan.baidu.com/article/49711c617a540cfa441b7c86.html)
(备注: 由于实测发现，不用证书也能访问成功，所以暂时还没想到怎么测试此方法的可行性。)

### 1.3 iOS 10 打开相册闪退 bug   

报错提示信息：

```   
2016-10-11 10:33:53.178931 BluetoothColorLampMeshLamp[745:235978] [access] This app has crashed because it attempted to access privacy-sensitive data without a usage description.  The app's Info.plist must contain an NSPhotoLibraryUsageDescription key with a string value explaining to the user how the app uses this data.
```

解决办法：   
在 plist 中添加字段：    

```  
Privacy - Camera Usage Description         
cameraDesciption      

Privacy - Photo Library Usage Description      
photoLibraryDesciption      
```
