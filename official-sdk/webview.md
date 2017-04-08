# WebView

##



#

#### 2016-07-06
1、UIWebView 获取当前页面URL的坑
问题描述：    
iOS 系统中，苹果提供的UIWebView控件功能中，能直接调用的方法寥寥无几，有一些方法甚至不管怎么调，他的值都不会变，比如pageCount,和pagelength等等属性，如果想在原生控件的基础上获取WebView中的某些信息，比如URL、页数等等，会相当的头疼，（安卓的却是可以直接获取）。    

解决方法：    
由于webView可以和Javascript直接进行交互，无疑给我们在解决WebView问题上开了一片天，弥补了WebView本身的缺陷，无论想获取什么，只要把Js代码写上，再用WebView加载即可，前提是知道怎么写，比如点击返回的时候，获取当前Html URL，可这么写：    
    
        NSString * currentUrl = [_webView stringByEvaluatingJavaScriptFromString: @"window.location.href"];
这样当前页面的URL就获取到了，page页数往往在字符串的最后的位置，有了这一工具，我们想获取什么，只要把对应的JS 代码加载即可，不得不说，在处理iOS中WebView问题上，Javascript真的很神奇。