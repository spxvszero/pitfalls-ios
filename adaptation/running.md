# 运行

### 1.点击应用图标卡顿

#### 环境参数：

```
*
```

#### 问题分析：

`didFinishLaunchingWithOptions ` 方法中有较多的操作。

分析：  
一个应用启动的过程：`start` -> (加载 `framework`，动态静态链接库，启动图片，`Info.plist`，`pch` 等)-> `main` 函数-> `UIApplicationMain` 函数。  
优化的方案：  

- 程序应用需要用到的一些资源如，`plist` 文件，`mp3` 文件，字体 `ttf` 等文件不要随意放到项目的其它文件中，请放到项目中专门用于存放资源的文件夹 `Supporting Files`。  
- `pch` 文件配置时候不要写入太多预编译的头文件，因为这会影响性能，所以 `Xcode6` 以上的版本才开始默认不使用 `pch` 文件。
- 图片资源请尽量放到 `Images.xcassets`，因为 `Images.xcassets` 会压缩图片资源，而放到放到 `mainBudnle` 里面，图片没有被压缩。
- `layer` 的渲染不宜使用的过多。
- 在 `appDeleage.m` 文件的 `- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions` 方法里开启了另一条线程来初始化某些第三方组件和初始化其他资源，例如：

```
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
```

#### 解决方法：

减少 `didFinishLaunchingWithOptions ` 方法中有较多的操作，开启异步线程来处理。

