# Xcode

1.如果你的 iOS 项目工程里面用到了 CocoaPods 导入的第三方库，那你就需要用 .xcworkspace 文件打开，而不是 .xcodeproj 文件，不然会出现第三方库载入有问题。


2.在打开 Xcode 项目的时候遇到以下问题：

```
Can't load project.
.xcodeproj cannot be opened because the project file cannot be parsed.
```

这个是因为在合并代码的时候，`.xcodeproj`文件中存在异常代码：

```
<<<<<<< HEAD
				CODE_SIGN_IDENTITY = "iPhone Distribution: Shenzhen Reflying Electronic Co., Ltd";
				"CODE_SIGN_IDENTITY[sdk=iphoneos*]" = "iPhone Distribution: Shenzhen Reflying Electronic Co., Ltd";
=======
				CODE_SIGN_IDENTITY = "iPhone Distribution: chipsguide technology co., ltd. (AXTESR7SA4)";
				"CODE_SIGN_IDENTITY[sdk=iphoneos*]" = "iPhone Distribution: chipsguide technology co., ltd. (AXTESR7SA4)";
>>>>>>> e4a621ebf80c582fe729ee66bc74549e85a0dcdd

```

这种尖括号和等号就是罪魁祸首，找到了解决掉即可。

3.Info.plist 文件修改不生效。

原因：Xcode 中如果有 Test 和 UITest 文件夹的话，里面对应的也有 Info.plist 文件，如果想要修改主项目的 Info.plist 文件，容易修改到错误的位置，让以为修改到位的内容，不生效。
