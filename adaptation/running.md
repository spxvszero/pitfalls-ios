# 运行

## 1 卡顿

### 1.1 点击应用图标卡顿







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
