# Xcode

### 1.第三方库载入问题

#### 环境参数：

```
macOS High Sierra 10.13
Xcode 9.2
```

#### 问题分析：

如果你的 iOS 项目工程里面用到了 `CocoaPods` 导入的第三方库，那你就需要用 `.xcworkspace` 文件打开，而不是 `.xcodeproj` 文件，不然会出现第三方库载入有问题。

#### 解决方法：

打开 .xcworkspace 文件

### 2.Can't load project..xcodeproj cannot be opened because the project file cannot be parsed.

#### 环境参数：

```
macOS High Sierra 10.13
Xcode 9.2
```

#### 问题分析：

其中一种原因是`.xcodeproj`文件中存在异常代码：

```
<<<<<<< HEAD
				CODE_SIGN_IDENTITY = "iPhone Distribution: Shenzhen Reflying Electronic Co., Ltd";
				"CODE_SIGN_IDENTITY[sdk=iphoneos*]" = "iPhone Distribution: Shenzhen Reflying Electronic Co., Ltd";
=======
				CODE_SIGN_IDENTITY = "iPhone Distribution: chipsguide technology co., ltd. (AXTESR7SA4)";
				"CODE_SIGN_IDENTITY[sdk=iphoneos*]" = "iPhone Distribution: chipsguide technology co., ltd. (AXTESR7SA4)";
>>>>>>> e4a621ebf80c582fe729ee66bc74549e85a0dcdd

```

以上问题是合并代码导致的，这种尖括号和等号就是罪魁祸首，找到了解决掉即可。

#### 解决方法：

[方法描述]

### 3.Info.plist 文件修改不生效。

#### 环境参数：

```
macOS High Sierra 10.13
Xcode 9.2
```

#### 问题分析：

Xcode 中如果有 Test 和 UITest 文件夹的话，里面对应的也有 Info.plist 文件，如果想要修改主项目的 Info.plist 文件，容易修改到错误的位置，让以为修改到位的内容，不生效。

#### 解决方法：

修改正确位置的 Info.plist 文件。