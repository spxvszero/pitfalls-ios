# 支付宝

## 1. 集成 SDK 报错

### 1.1 `openssl/asn.h's file not found`

在集成支付宝支付 SDK 的时候，官方给出的 demo 运行跑下来没任何问题，但是当把所有需要的文件导入到工程中的时候，却报了这么一个无法忍受的错误：  
![](img/openssl_tip.png)  
属于典型的编译性问题，`openssl` 包为支付宝 `SDK` 提供。

解决方法：  
1、进入工程 `TARGETS -> Build Setting -> Search Paths -> Header Search Paths`;    
2、增加路径    

		"$(SRCROOT)/工程名/../ openssl 包的上层文件夹名"
		
3、不允许递归 
	
		non-recursive 改为 recursive
		
**切记**  
1、双引号不能省略( 但有时候不加双引号 )    
2、增加的路径一定要明确指定到 openssl 包的上层文件夹

例子：
工程目录：

![](img/foderlist.png)
修改项：
![](img/folder_eidt.png)

### 1.2 `rsa_private read error : private key is NULL`

在集成支付宝进行支付的时候，控制台经常打印出现这样的日志：
		
	rsa_private read error : private key is NULL
问题和它所描述的一样，私钥读取错误。    
解决方法如下：    
+ 仔细检查私钥是否有无，或者是否拿公钥当私钥来使，如有替换过来即可；    
+ 替换支付宝本身 SDK `RSADataSigner.m` 类中的部分代码；  
在RSADataSigner.m文件中 搜索代码

	[result appendString:@"-----BEGIN PRIVATE KEY-----\n"];
	将其改成
	[result appendString:@"-----BEGIN RSA PRIVATE KEY-----\n"];
	再在RSADataSigner.m文件中 搜索代码
	[result appendString:@"\n-----END PRIVATE KEY-----"];
	将其改成
	[result appendString:@"\n-----END RSA PRIVATE KEY-----"];
