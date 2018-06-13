# App Store 审核


### 1.Potential Loss of Keychain Access - The previous version of software has an application-identifier value of [‘xxxxxxxxxxxxx’] and the new version of software being submitted has an application-identifier of [‘xxxxxxxxxxxx’]. This will result in a loss of keychain access.

#### 环境参数：

[参数描述]

#### 问题分析：

提交到 App Store 选择了自动匹配证书打包上传选项，一般情况下选择自动匹配证书是没有问题，但是由于该应用之前是从一个账号转移到另一个账号，选择自动匹配证书有可能会在老的账号上自动创建 App 信息，导致上传时会发出以上警告。

#### 解决方法：

登录老账号，把该 App 信息从老账号中删掉。

### 2.Missing Info.plist key - This app attempts to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSPhotoLibraryUsageDescription key with a string value explaining to the user how the app uses this data.


#### 环境参数：

```
iTunes Connect @ App Store
```

#### 问题分析：

提交到 iTunes connect 后台不成功提醒：
***
We have discovered one or more issues with your recent delivery for "XXXX". To process your delivery, the following issues must be corrected:

Missing Info.plist key - This app attempts to access privacy-sensitive data without a usage description. The app's Info.plist must contain an NSPhotoLibraryUsageDescription key with a string value explaining to the user how the app uses this data.

Once these issues have been corrected, you can then redeliver the corrected binary.

Regards,

The App Store team
***

使用了相册的使用权限声明，缺少相册的使用权限的相关描述，会被拒绝提交。

#### 解决方法：

添加相册的使用权限和相关描述，问题可得到解决。

备注：

另外一个隐私权限，Privacy - Photo Library Addtions Usage Description 和相册使用权限Privacy - Photo Library Usage Description 长得很样，在输入 Photo 词语的时候往往出来的是第一个，容易填错，填错导致的结果和没填导致的结果是一样的。

如果程序中还使用了其他的权限，只是该功能被隐藏了，不易发现，这时候也需要确保把其他隐藏的使用权限也申明。

### 3.Guideline 5.1.1 - Legal - Privacy - Data Collection and Storage - We noticed that your app requests the user’s consent to access their Apple Music, music and video activity, media library but does not clarify the use of this feature in the permission modal alert.Please see attached screenshots for details.￼


#### 环境参数：

```
App Store Review
```

#### 问题分析：

应用需要访问用户的媒体库资料的权限。虽然已经做了权限申请的提示框，但却没有给用户明确的说明，这个权限会用在什么地方。

#### 解决方法：

把原来的「此 App 需要访问你的媒体资料库」

改成：

「此 App 需要访问您的媒体资料库，用以播放音乐」

### 4.Guideline 5.1.2 - Legal - Privacy - Data Use and Sharing - Your app accesses user data from the device but does not have the required precautions in place. - To collect personal data with your app, you must make it clear to the user that their personal data will be uploaded to your server and you must obtain the user's consent before the data is uploaded. You must also have a Privacy Policy URL and ensure that the URL you provide directs users to your privacy policy.


#### 环境参数：

```
App Store Review
```

#### 问题分析：

应用需要访问用户的通讯录的权限。虽然已经做出提示框，给了详细的权限使用说明，但苹果要求增加用户隐私协议界面，这样保证用户的私有信息不会上传到服务器或者被用作商用。

#### 解决方法：

放置一个 HTML 文本在服务器上或者本地利用 UIWebView 或者 WKWebView 加载。
在使用到通讯录权限时弹出协议，同时在软件内部帮助或者更多界面里增加可点击访问的连接入口。

### 5.We began the review of your app but still are not able to continue because we need access to a video that demonstrates your app in use on an iOS device. - Specifically, please provide a demo video to demonstrate the interactive features associating with the hardware. - To provide a link to a demo video: - Log in to iTunes Connect - Click on “My Apps” - Select your app - Scroll down to “App Review Information” - Provide demo video access details in the “Notes” section - Click Save - Click Submit for Review - Once this information is available, we can continue with the review of your app.


#### 环境参数：

```
App Store Review
```

#### 问题分析：

由于我们的应用使用到实体硬件设备，所以需要苹果需要我们拍个视频演示如何使用我们的应用。

#### 解决方法：

上传一个视频到开放的视频网站，把链接放在 App 提交审核的页面备注里。
PS.视频播放最好不要有广告，不然苹果会提示「are not able to access the provided demo」,并需要你再次提交审核。

### 6.This type of app has been identified as one that may violate one or more of the following App Store Review Guidelines. Specifically, these types of apps often: - 1.1.6 - Include false information, features, or misleading metadata. - 2.3.0 - Undergo significant concept changes after approval - 2.3.1 - Have hidden or undocumented features, including hidden "switches" that redirect to a gambling or lottery website - 3.1.1 - Use payment mechanisms other than in-app purchase to unlock features or functionality in the app - 4.3.0 - Are a duplicate of another app or are conspicuously similar to another app - 5.2.1 - Were not submitted by the legal entity that owns and is responsible for offering any services provided by the app - 5.3.4 - Do not have the necessary licensing and permissions for all the locations where the app is used - Before we can continue with our review, please confirm that this app does not violate any of the above guidelines. You may reply to this message in Resolution Center or the App Review Information section in App Store Connect to verify this app’s compliance.  - Given the tendency for apps of this type to violate the aforementioned guidelines, this review will take additional time. If at any time we discover that this app is in violation of these guidelines, the app will be rejected and removed from the App Store, and it may result in the termination of your Apple Developer Program account. - Should you choose to resubmit this app without confirming this app’s compliance, the next submission of this app will still require a longer review time. Additionally, this app will not be eligible for an expedited review until we have received your confirmation.



#### 环境参数：

```
App Store Review
```

#### 问题分析：

一般机审会出现此类的打回重审问题。在他提到的几项问题中排除原因，如果没有就可以不用做任何处理再次提交审核，如果存在某项问题，则手动处理，再次排除。我们的问题在于账号底下存在功能类似，但 UI 不同的面向不同客户的软件，所以苹果需要确认不是另一个软件的拷贝版本。

#### 解决方法：

在 App 提交审核的页面备注里，我们做了如下解释：该应用主要用于控制特定的智能设备，是为其他客户量身定制的，并没有存在上述问题。

