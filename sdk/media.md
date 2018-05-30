# Media

### 1.项目中在获取本地iPods音乐时, 获取不全

#### 环境参数：

```
*
```

#### 问题分析：

项目中在获取本地iPods音乐时, 获取不全, 例如本机音乐100首, 只能获取到10多首.

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


#### 解决方法：

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

### 2.集成变声传音功能时, 若当前在播放音乐, 会出现录音时音乐从听筒播放的问题.

#### 环境参数：

[参数描述]

#### 问题分析：

当程序中出现同时使用硬件情景时, 如播放音乐时, 进行变声传音的录制和播放, 应考虑到多种调用触发情景, 结合需求进行合理的逻辑判断和处理.

#### 解决方法：

当播放音乐状态下开始实时传音的录制和播放, 对当前正在播放音乐进行处理, 然后在进行实时传音的相关操作, 完成后再对音乐状态进行恢复.

### 2.工程图片导出的问题

#### 环境参数：

[参数描述]

#### 问题分析：

当有新项目需要在原本原有的项目进行二次开发的时候，设计师往往会找我们要我们之前工程的图片，而这时我们往往会想到对工程目录当中的图片或者Imagee.xcassets 里面的图片进行复制过来再传给他（她）。可最终的结果是，设计师后来给的图片和之前使用的图片出现了不同程度的突差别，导致在开发过程中不尽人意。

解析：如果全部图片放在了工程目录下，复制的时候，直接复制过去，问题不是很大；可如果图片是放在了Imagee.xcassets文件夹下，而你直接把这个Imagee.xcassets里面的文件全部复制给了设计师，那么设计师的工作量估计不小。Xcode 下图片只要放入了Imagee.xcassets，Xcode自动回为图片包上了一个.imageset的文件夹，并生成了对应的content.json文件，设计师拿到的时候，要把图片一张张抽出来，工作量可想而知。这种方发绝不是一种好的做法。

#### 解决方法：

最佳做法是：先给我们之前的工程打一个包（测试包或者，App Store 包）都行，用归档使用工具进行打开，这时候你会发现，我们的包变成了Payload的文件夹，打开这个Payload文件夹会看到一个.app文件，接着显示包内容你会看到工程所有工程当中使用的图片的在这里了纯粹的图片，没有使用的图片不在，（不行你自己实验一下），只需要把这里边的图片全部拷出来，发给设计师，大功告成。

原理：Xcode其实在打包的时候，会对应用当中的资源进行检测，没有用到的资源不会放到包里面，所以，在这里找到的图片一定涵盖了工程中使用的所有图片。在这一点上，感觉Xcode还是挺强大的。



    

