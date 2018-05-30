# 数据

### 1.较长字段 URL 编码问题

#### 环境参数：

[参数描述]

#### 问题分析：


有时候做网页分享时，需要生成一个 URL 字符串，该 URL 自由短短的几个字段，但每个字段的数据都很长，比如每个字段都是一个 JSON 结构，而该请求不方便用 POST 请求，如果直接传输该字符串，在移动客户端，可能没法打开（PC端往往可以），此时在 iOS 端又不直接提供 URL Encoding 的方法。

例：比如该 URL 要拼接 data=[]的字段 ;中括号内部是一个 JSON 数据字符串，最后分享出去的时候出现了加载失败的提示。

#### 解决方法：

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

Encoding 往往使用 UTF-8 。

### 2.外部链接打不开

#### 环境参数：

```
macOS High Sierra 10.13
Xcode 9.2
```

#### 问题分析：

当在运用中使用［［UIApplication sharedApplication］openURL：［NSURL urlWithString：urlString］］去打开一个外部链接时，往往都能正常的打开，凡事都有例外，也有怎么都打不开的情形。    
当urlString存在空格的时候，比如第一个字符为空格，那么，打开外部链接的命令将不生效。当urlString 存在中文字符时也打不开。    
 
构建URL的时候（不管是＋号方法还是减号方法构建的，只要第一个字符为空格，可能苹果为了提高效率考虑，直接返回URL空对象，所以打开外部链接不生效）。    

#### 解决方法：

1、去空格    
2、统一UTF－8 编码(防止有中文字符的情况)    
代码：    

    NSString *urlString = [NSString stringWithString:string];
    
    NSString *URL = [urlString stringByReplacingOccurrencesOfString:@" " withString:@""];
    
    NSURL *url = [NSURL URLWithString:[URL stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding]];
    
    return url;

