
1.最近公司一个程序中包含了对和服务器通信的加密和解密方法，里面引入到了zlib，但是编译的时候老是提醒以下错误：  
```
Undefined symbols for architecture armv7:
  "_compress", referenced from:
      +[ZipStr Compress:length:] in ZipStr.o
  "_inflate", referenced from:
      _ggzread in ZipStr.o
  "_inflateReset", referenced from:
      _ggzread in ZipStr.o
  "_inflateInit2_", referenced from:
      _Init in ZipStr.o
  "_crc32", referenced from:
      _ggzread in ZipStr.o
      _Init in ZipStr.o
  "_inflateEnd", referenced from:
      _destroy in ZipStr.o
ld: symbol(s) not found for architecture armv7
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

结果发现是没有导入对应的`libz.tbd`文件，导入即可。另外，现在Xcode 7中选择 `.tbd`文件替换之前的`.dylib`文件，相关参考资料：  
1.[Why Xcode 7 shows *.tbd instead of *.dylib?](http://stackoverflow.com/questions/31450690/why-xcode-7-shows-tbd-instead-of-dylib)  
2.[Xcode 7 : 浅析 .tbd 与 .dylib](http://www.meniny.cn/2015/09/22/00-00-01-iOS_Xcode_7_tbd/)  
3.[关于Xcode7中的tbd文件](http://www.cocoachina.com/ios/20160506/16141.html)



2.公司蓝牙封装库 Demo 编译的时候出现以下编译错误：

```
  "_OBJC_CLASS_$_LXBluetoothDeviceFMSenderManagerController", referenced from:

      objc-class-ref in mainViewController.o

ld: symbol(s) not found for architecture arm64

clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

以上编译错误，是有一个一个.m文件没有被引入，可能是之前注销了引用，然后在`Build Phases`的`Compile Sources`中添加相关的.m文件即可。

3.当APP处于后台的时候，如果没有一下参数的配置，蓝牙断开没有回调：

```
Required background modes

1.App communicates using CoreBluetooth
2.App shares data using CoreBluetooth
```

另外，还需要注意在`Capablities`里面的`Background Modes`里面选择：
```
1.Uses Bluetooth LE accessories
2.Acts as a Bluetooth LE accessory
```

4.同事购买了一个日版的iPhone5，有一个有趣的特性：  
手机静音的情况下，拍照依然有声音！

5.今天删除了本地的全部Xcode中使用的`.mobileprovisioning`文件，然后也删除了用于iOS的全部开发证书。然后重新创建了一个`.cer`证书文件，准备双击安装的时候，提示以下内容：  
**不能修改“System Roots”钥匙串。**  
*若要更改根证书是否会被信任，请在“钥匙串访问”中打开它，然后修改它的信任设置。新根证书应被添加到当前用户的登录钥匙串，如果它将被这台机器的所有用户共享，则应被添加到系统钥匙串。*  
解决方法：我们点击确定之后，直接在当前窗口：`钥匙串 -> 登录`，将`.cer`文件直接拖拽进来即可！


6.iOS应用的图标黑边的问题，有一种原因：就是设计师将图做了圆角处理，iOS系统本事会自动做圆角处理，由于角度不对应，就会出现酱的问题。

7.上传到App Store的应用信息中的图标，不能存在Alpha通道或者透明度。


8.编译出来的.a库，运行发现以下错误：

```
Undefined symbols for architecture arm64:
  "_OBJC_CLASS_$_LXGroupLampModel", referenced from:
      objc-class-ref in libBluetoothLibrary.a(LXBluetoothDeviceManager.o)
ld: symbol(s) not found for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

一般这种错误是因为真正编译的时候，相对应的.m文件没有加入到编译中，我们可以去`TARGETS` -> `Build Phases` -> `Compile Sources`中查看是否有加入这个.m文件。

9.有一个地方需要注意的就是：以下地方`"代理方法名"`处，Xcode是不会做错误校验处理的，最多`warning`，所以一定要注意，如果某个回调方法没有触发，很有可能是这里的问题。

```
respondsToSelector:@selector("代理方法名")
```

10.在打Ad Hoc测试包的时候，发现老是提示没有对应的`.mobileprovision`文件，但是其实已经下载到`\MobileDevice\Provisioning Profiles`文件夹下面，我删除掉重新安装即可解决这个问题，但是又会提示和开发组相关什么权限问题，我在选择`Team`的时候选择了`local`的配置，解决了问题！

11.Xcode 在导出`.ipa`包的时候，在拉取登陆账户信息的时候，显示以下信息：

```
The request timed out.
```

然后这个导出`.ipa`包的过程就终止了。如果一直纠结于此的话，你可能一辈子都打不出来包。去到`Xcode -> Preferences -> Accounts`，我们发现其中有部分登陆的账号，右侧有一个闪电的图标，并且在右侧的详细信息中显示`The request timed out.`，我们重新登陆之后，就解决了这个问题。出现这种情况可能是当前网络问题，或者账号密码有更改。

12.当编译的时候，出现无法识别类的类型，我们可以在当前引用处，使用 `@class` 进行申明。

13.在真机调试的时候，运行到手机上的时候，发现应用运行卡死，并且提示错误。

```
process launch failed: failed to get the task for process 2316
```

原因：把发布证书当做调试证书来使用，就会出现这种情况，注意配置调试证书即可。

14.今天OBD项目中出现了一个看起来比较麻烦的问题，就是我自己使用蓝牙库加上Demo测试，然后OBD的开发人员在项目中始终无法获取到相关的代理回调，最终找到问题，并且给出了解决方案。有以下两点值得总结！  
任何问题，只要你单步调试，可以解决目前90%以上的bug！  
关于代理或者回调，我们经常会犯错：  
```
1.传入的代理为空。  
2.代理设置的时机不对，一般问题是设置的不够及时，已经回调了，还没有来得及设置代理。  
3.代理被多次设置，代理被转移。
```
说明：由于多个页面需要利用代理来更新UI，所以我们可以采用代理集合，通过for循环来分发出去。

15.有时候，你会发现通过Xcode写一个枚举，却发现不能引用，提示不存在，我们先将整个代码编译一下，然后就可以引用了。

16.以下代码编译运行错误。

```
        case LXBluetoothDeviceCustomEQModeRock:
            Byte byte[] = {13,7,255,3,9,mode,14};
            NSData *checkData = [[NSData alloc] initWithBytes:byte length:byte[1]];
            [self sendCustomCommand:self.global_cmdKey param1:0 param2:0 others:checkData];
            break;
```

但是以下代码编译就没有问题：:cry: :cry: :cry:

```
        case LXBluetoothDeviceCustomEQModeUser:
        {

            Byte byte[] = {13,7,255,3,9,mode,14};
            NSData *checkData = [[NSData alloc] initWithBytes:byte length:byte[1]];
            [self sendCustomCommand:self.global_cmdKey param1:0 param2:0 others:checkData];
        }
```



17.重构代码，进行重命名[`Edit -> Refactor -> Rename`]的时候，发现有的时候，会出现部分的内容不能同步修改，目前还不知道是什么原因。没有修改过来的地方，有的通过重新编译之后可以定位没有修改的地方，但是有些代理回调方法就不提示了。

18.使用Xcode在当前工程的Group中新建文件的时候，发现生成的文件名称是红色，背景是灰色的。我选取了另外一种方法，先将文件创建出来，然后再Add File，就可以了！

19.Xcode运行项目出现一下错误：

```
duplicate symbol _OBJC_CLASS_$_***Manager in:
    /Users/ifeegoo/Library/Developer/Xcode/DerivedData/Bluetooth-diybgwkcpztvmhffxofltfwpjjpv/Build/Intermediates/Bluetooth.build/Debug-iphoneos/Bluetooth.build/Objects-normal/arm64/***Manager-A1C9B786AB61733D.o
    /Users/ifeegoo/Library/Developer/Xcode/DerivedData/Bluetooth-diybgwkcpztvmhffxofltfwpjjpv/Build/Intermediates/Bluetooth.build/Debug-iphoneos/Bluetooth.build/Objects-normal/arm64/***Manager-916E166E553EC92B.o
duplicate symbol _OBJC_METACLASS_$_***Manager in:
    /Users/ifeegoo/Library/Developer/Xcode/DerivedData/Bluetooth-diybgwkcpztvmhffxofltfwpjjpv/Build/Intermediates/Bluetooth.build/Debug-iphoneos/Bluetooth.build/Objects-normal/arm64/***Manager-A1C9B786AB61733D.o
    /Users/ifeegoo/Library/Developer/Xcode/DerivedData/Bluetooth-diybgwkcpztvmhffxofltfwpjjpv/Build/Intermediates/Bluetooth.build/Debug-iphoneos/Bluetooth.build/Objects-normal/arm64/***Manager-916E166E553EC92B.o
ld: 2 duplicate symbols for architecture arm64
clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

关键字：`duplicate symbols for architecture` ，我这个项目中包含Project还有Library，存在重复文件引入的可能，我们去到 `Targets -> Project - Build Phases -> Compile Sources ` 找到重复文件，移除之后，重新编译，即可！

20.有时候在Xcode的Team设置中，总是提醒需要Fix Issues，提醒的是没有对应的mobileprovision文件，但是实际上又是在当前的文件目录下。我们可以找到那个对应的mobileprovision文件，删除之后，重新安装就没有问题了。

21.今天早上自己使用的iPhone 5S突然开不了机，按电源键，频幕一直是黑的。后来通过同时按下电源键和Home键，出现了白背景和黑苹果的画面，然后再按电源键，依然无法打开。最后尝试了几次同时按下电源键和Home键，然后再按电源键，就开启了手机。

22.处理解决了一个项目编译错误，通过在：  

`Build Settings -> Linking -> Other Linker Flags -> -all_load`  

解决了问题，但是对于这个`-all_load`这个参数配置还是没有太理解。

23.今天在整理证书的过程中，发现导出.p12文件的时候，明明输入了密码，但是导出.p12文件之后，发现密码错误。应该是没有输入进去。后来就导出了一个不需要密码的.p12文件。这样问题也不大。

24.今天早上自己使用的iPhone 5S突然开不了机，按电源键，频幕一直是黑的。后来通过同时按下电源键和Home键，出现了白背景和黑苹果的画面，然后再按电源键，依然无法打开。最后尝试了几次同时按下电源键和Home键，然后再按电源键，就开启了手机。



25.下面的代码最里层的括号如果不加上的话，编译器会报错.

```
case 8:
                {
                    if ((byteData[3] == 2) &&(byteData[3] == 4))
                    {
                        if ([self.lxBluetoothDeviceOBDManagerDelegate respondsToSelector:@selector(bluetoothDeviceOBDCarIgnitionWithStatusCode:)])
                        {
                        
                        }
                    }
                }
                    break;
```


26.通过91助手安装Ad Hoc版本的测试包的时候，不能通过直接将附件中的.ipa包拖入安装，这样就会出现问题。显示的是安装包解析错误，错误码：-400556023
