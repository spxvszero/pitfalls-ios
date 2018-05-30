# 科大讯飞

### 1.编译错误

#### 环境参数：

[参数描述]

#### 问题分析：

```
duplicate symbol _OBJC_METACLASS_$_IFlyVoiceWakeuper in:
    /Users/hao123/Desktop/testPod/test111/test111/iflyMSC.framework/iflyMSC(IFlyVoiceWakeuper.o)
 duplicate symbol _OBJC_CLASS_$_IFlyVoiceWakeuper in:
  /Users/hao123/Desktop/testPod/test111/test111/iflyMSC.framework/iflyMSC(IFlyVoiceWakeuper.o)
    ld: 2 duplicate symbols for architecture armv7
    clang: error: linker command failed with exit code 1 (use -v to see invocation)
```

工程中的配置项 `other linker flag` 中配置了 `-all_load` ,导致了重复的目标文件被链接加载到可执行文件中。    

#### 解决方法：
 
   去掉 `-all_load` 即可。 