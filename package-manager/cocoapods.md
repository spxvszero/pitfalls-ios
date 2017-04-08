# CocoaPods

## 1.编译不通过

当我们从网络Clone或者Download下来的Demo是使用CocoaPods来管理第三方库时, 这时候直接编译是不通过的, 这是
我们就要使用Terminal进到该工程文件的根目录下, 然后执行:pod update 命令来更新本地第三方类库。
但使用pod uodate 或者pod install时有事无法完成更新, 报错如下:<br>   
[!] The dependency `SDWebImage` is not used in any concrete target.<br>
[!] The dependency `Masonry` is not used in any concrete target.
.... 

#####问题分析：
podfile升级之后到最新版本，pod里的内容必须明确指出所用第三方库的target，否则会出现The dependency `` is not used in any concrete target这样的错误。

#####解决方案:
编辑Podfile文件内容指定其Target, 其中“MyApp”为你的工程名, use_frameworks, 个别需要用到，比如reactiveCocoa: 

```objective-c 
platform :ios, '8.0'

def pods
  pod 'SDWebImage'
  pod 'Masonry'
end

target 'MyApp' do
  pods
end
```
***
