# [YTKNetwork](https://github.com/AFNetworking/AFNetworking)

### 1.支持 https

#### 环境参数：

[参数描述]

#### 问题分析：

在 Xcode7.0 之后,苹果废弃了 NSURLConnection 方法,数据请求使用 NSURLSession，作为网络请求类第三方库使用量最大的 AFN 也及时的更新的新的版本—— AFN 3.0版本。
新的版本的里废弃了基于 NSURLConnection 封装的 AFHTTPRequestOperationManager，转而使用基于 NSURLSession 封装的 AFHTTPSessionManager 了。


#### 解决方法：

支持 https（校验证书）:



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

经程序测试发现，无论是使用方式一中的证书校验模式，还是方式二中的非证书校验模式，都能请求到数据。如果项目不涉及支付等敏感数据，可以直接使用不校验证书的方式，仅配置 https 的基本配置即可。


