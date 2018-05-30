# [YTKNetwork](https://github.com/yuantiku/YTKNetwork)

### 1.音频文件上传报错

#### 环境参数：

[参数描述]

#### 问题分析：

在使用 `YTKNetwork` 进行上传音频文件的时候，仿照上传图片的例子，将上传的 `type` 类型改为 `audio/m4a` 发现无法上传成功，后台状态码 `state code == 500`, 报了和没有适配 `AFNeworking` 的 `contentType` 类似的问题，一样的错误。

#### 解决方法：

解决方法如下：    
在配置 `YTKNetworkAgent` 的时候，统一配置，例：
		
	- (void)converContentTypeConfig{
    	YTKNetworkAgent *agent = [YTKNetworkAgent sharedAgent];
   		NSSet *acceptableContentTypes = [NSSet setWithObjects:@"application/json", @"text/json", @"text/javascript", @"text/plain", @"text/html", @"text/css",@"audio/m4a", nil];
    	NSString *keypath = 	@"jsonResponseSerializer.acceptableContentTypes";
    	[agent setValue:acceptableContentTypes forKeyPath:keypath];
	}
	
将需要添加的 `contentType 添加到集合里面，比如音频的类型为 `@"audio/m4a"`，问题即可解决。    

### 2.图片上传报错

#### 环境参数：

[参数描述]

#### 问题分析：

`No known instance method for selector 'appendPartWithFileData: name: fileName: mineType:'`

在使用 `CocoaPods` 引入最新 `YTKNetwork` 进行网络请求时，在构造 `constructingBodyBlock` 的 `formData appendPartWithFileData: name: fileName: mineType:` 时，报了一个未能发现 `appendPartWithFileData` 方法的错误提示，整个工程无法在继续进行。
提示如下：

		No known instance method for selector 'appendPartWithFileData: name: fileName: mineType:'

#### 解决方法：

在 `YTKNetwork issues` 里找到了如下解决办法，需要再加入头文件
     
     #import "AFURLRequestSerialization.h"
可正常编译。
