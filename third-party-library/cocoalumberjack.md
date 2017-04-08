# [CocoaLumberjack](https://github.com/CocoaLumberjack/CocoaLumberjack)


## 1.v3.0.0编译报错问题

CocoaLumberjack 更新最新版本v3.0.0编译报错问题：          

报错信息 DDFileLogger.h:429:40: Token is not a valid binary operator in a preprocessor subexpression   

FOUNDATION_SWIFT_SDK_EPOCH_AT_LEAST      

出错原因：      
     最新CocoaLumberjack v3.0.0版本最低支持Xcode版本为 Xcode 8 或以上版本。   
     
解决办法：   
   要么升级Xcode版本，要么恢复回 CocoaLumberjack v2.4.0版本   