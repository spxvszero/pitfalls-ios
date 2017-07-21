
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
