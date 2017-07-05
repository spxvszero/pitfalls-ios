# 设备

## iPad

### 1. 图标无法显示

**可能的原因和解决方法：**  

+ 工程运行期间会自动生成两个适配 iPad 的应用图标的文件。

删除即可:

```
icon file(iOS5)
CFBundeIcons~ipad

```
+ 应用的图标尺寸不对。

核对放入的 icon 的尺寸是否跟对应的规定相同。

+ 没有添加图标在对应的位置上。

核对下所有尺寸，是否有放错位置。

## iPod Touch

### 1. 摇一摇无震动效果   

**原因：**   
iPod Touch 不支持震动。

震动的代码：

```
#import <AudioToolbox/AudioToolbox.h>

AudioServicesPlaySystemSound(kSystemSoundID_Vibrate);   
```
iPhone：每个调用都会生成一个简短的 1~2 秒的震动。   
iPod Touch： 该调用不执行任何操作，但也不会发生错误！ 

待验证内容：

只有各个版本的iPhone具备振动提醒功能。很遗憾，没有公开的API用于检测设备是否支持振动功能。不过，AudioToolbox.framework有两个方法用来选择性地振动不同版本的iPhone：

```
#import <AudioToolbox/AudioToolbox.h>

AudioServicesPlayAlertSound(kSystemSoundID_Vibrate);
AudioServicesPlaySystemSound(kSystemSoundID_Vibrate);
```
第一个方法会振动iPhone，而在iPod touch/iPad上则会发出“哔哔”的响声。第二个方法只会振动iPhone。在不支持振动的设备上，它什么都不做。如果你正在开发的一款游戏通过振动设备来提示危险，或是开发一款迷宫游戏，当玩家撞到墙时发出振动，那么应该用第二个方法。第一个方法用来提醒用户，包括振动和发出哔哔声，而第二个方法只能用来发出振动。

参考：[http://www.cnblogs.com/wfwenchao/p/4000131.html](http://www.cnblogs.com/wfwenchao/p/4000131.html)

**解决方法：**调整产品在 iPod Touch 上的使用策略。  

### 2. 测试包无法安装

**可能的原因和解决方法：**  

+ 配置文件中有一个属性 `Build Active Architecture Only` 在导出测试包之前设置成了 Yes。

如果你设置为 `yes`，这样 `debug` 速度上会更快，它会编译你需要的那一个。设置为 `no` 时会编译所有的架构。而编译的架构是向下兼容的。所以，有时候你会看到你设置的是 `yes`，有的手机也可以安装上去。但架构不同的还是无法安装。所以，一般这个属性是 `debug` 为 `yes`，`release` 为 `no`。一般默认就是这样的配置，尽量不要修改这个属性。