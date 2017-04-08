# Media

#### 2016-06-21
[Lewanny](https://github.com/Lewanny)

####问题描述: 
项目中在获取本地iPods音乐时, 获取不全, 例如本机音乐100首, 只能获取到10多首.

####问题分析：
如下,这句代码 `[query setGroupingType: MPMediaGroupingPodcastTitle];`使用了MPMeidaQuery的setGroupingType方法将MPMeidaQuery的属性设置为了播客标题.<br>
所以在获取的时候导致没有博客信息的曲目获取不到.


```objective-c 
    MPMediaQuery *query = [[MPMediaQuery alloc] init];
    [query setGroupingType: MPMediaGroupingPodcastTitle];
    NSArray *albums = [query collections];
    for (MPMediaItemCollection *album in albums) {
        MPMediaItem *representativeItem = [album representativeItem];
        NSString *artistName =
        [representativeItem valueForProperty: MPMediaItemPropertyArtist];
        NSString *albumName =
        [representativeItem valueForProperty: MPMediaItemPropertyAlbumTitle];
        NSLog (@"%@ by %@", albumName, artistName);
     
        NSArray *songs = [album items];
     }
```
####解决方案:
使用如下方法获取, 不需要设置MPMeidaQuery的属性, 其默认为MPMediaGroupingTitle, 即按照Title获取即可.

```objective-c 
    MPMediaQuery *myPlaylistsQuery = [MPMediaQuery songsQuery];
    NSArray *playlists = [myPlaylistsQuery collections];
    for (MPMediaPlaylist *playlist in playlists) {
    
        NSArray *array = [playlist items];
        for (MPMediaItem *song in array) {
            NSString *songTitle = [song valueForProperty: MPMediaItemPropertyTitle];
        }
    }
```
***




#### 2016-05-10（四）

1.iOS 添加变声传音时的出现的Bug和解决方案 

情景：集成变声传音功能时, 若当前在播放音乐, 会出现录音时音乐从听筒播放的问题.

原因：当程序中出现同时使用硬件情景时, 如播放音乐时, 进行变声传音的录制和播放, 应考虑到多种调用触发情景, 结合需求进行合理的逻辑判断和处理.

解决方法：当播放音乐状态下开始实时传音的录制和播放, 对当前正在播放音乐进行处理, 然后在进行实时传音的相关操作, 完成后再对音乐状态进行恢复.

***
>>>>>>> 7508da625785c407598524f0ce79fd87f87dfc4e
>>>>>>> 
>>>>>>> 
>>>>>>> 
>>>>>>> 


1、iOS工程图片导出的问题

情景：当有新项目需要在原本原有的项目进行二次开发的时候，设计师往往会找我们要我们之前工程的图片，而这时我们往往会想到对工程目录当中的图片或者Imagee.xcassets 里面的图片进行复制过来再传给他（她）。可最终的结果是，设计师后来给的图片和之前使用的图片出现了不同程度的突差别，导致在开发过程中不尽人意。

解析：如果全部图片放在了工程目录下，复制的时候，直接复制过去，问题不是很大；可如果图片是放在了Imagee.xcassets文件夹下，而你直接把这个Imagee.xcassets里面的文件全部复制给了设计师，那么设计师的工作量估计不小。Xcode 下图片只要放入了Imagee.xcassets，Xcode自动回为图片包上了一个.imageset的文件夹，并生成了对应的content.json文件，设计师拿到的时候，要把图片一张张抽出来，工作量可想而知。这种方发绝不是一种好的做法。

最佳做法是：先给我们之前的工程打一个包（测试包或者，App Store 包）都行，用归档使用工具进行打开，这时候你会发现，我们的包变成了Payload的文件夹，打开这个Payload文件夹会看到一个.app文件，接着显示包内容你会看到工程所有工程当中使用的图片的在这里了纯粹的图片，没有使用的图片不在，（不行你自己实验一下），只需要把这里边的图片全部拷出来，发给设计师，大功告成。

原理：Xcode其实在打包的时候，会对应用当中的资源进行检测，没有用到的资源不会放到包里面，所以，在这里找到的图片一定涵盖了工程中使用的所有图片。在这一点上，感觉Xcode还是挺强大的。




#### 2016-03-31(四)

1.iOS 闪屏图片对应用分辨率的影响  
情景1：  
很多时候我们会发现（尤其是使用6/6S或是6/6sPlus的时候），同尺寸安卓设备和iOS设备，iOS设备的分辨率明显却差了很多，有些模糊的感觉，给人很low的感觉，甚至有时候会疑惑  
情景2：
有时候当我们使用设备（6/6S，6/6sPlus）进行开发新项目或是调试的时候，会发现这样的一种现象：我们所要写的控件，图片或是字体，明显的比设计师给的效果图或是切图有很大的突兀，有时候甚至觉得设计师弄错了，很多时候，我们放大或是缩小我们的字体控件等等以使得UI效果和设计师提供的原型一致。  
情景3：
有时候版本发App Store或是打测试包在不同的设备上运行的时候，我们会发现有些尺寸的屏幕闪屏没有，譬如4S，6/6s等等，表现为在设备运行的时候一片漆黑一闪而过。  
以上的问题，其实都是由于各种iOS设备屏幕的闪屏没有添加完毕（可能缺少某一种），app在运行的时候会根据闪屏去唯一确定分辨率，如果6/6s plus 的闪屏缺少，系统会去选择同iphone 5 屏幕尺寸一致的图片作为更大尺寸设备屏幕的闪屏，系统默认的分辨率就是iphone 5手机分辨率，所以我们在6/6s上看到的效果就会差很多，当我们重新添加上这些缺少的图片以后，6/6s plus的分辨率就恢复正常了。


    

