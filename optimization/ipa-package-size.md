# 安装包大小

![](http://www.zoomfeng.com/images/2016/10/12/4.png)

****************************
实际开发中，使得 .ipa 安装包文件增大的一大元凶就是工程中导入积累了越来越多无用的图片，使得导出来的安装包大小剧增。    

解决办法：    </br>          
  在准备打包时，先用工具检测下工程中是否存在未使用的图片资源。 
      
工具使用教程和下载地址：    
[http://blog.lessfun.com/blog/2015/09/02/find-unused-resources-in-xcode-project/](http://blog.lessfun.com/blog/2015/09/02/find-unused-resources-in-xcode-project/)   

写了个简易的demo测试了下，总结优缺点如下：      
####优点：   

* 1.安装方便，下载后直接打开即可，无需多余步骤。   
* 2.使用方便，直接点击```browse```打开工程所在根目录，然后点击search即可获取项目中未使用的图片。   
* 3.匹配过滤更精确，例如：icon_tag_x.png 是不应该被当做未使用的资源的，只是以一种比较隐晦的方式间接引用了，所以不应该出现在结果列表中。    
* 4.删除方便，在获取到未使用图片列表后，可直接在工具上选中要删除图片即可，无需回到工程中逐个删除。   
* 5.查找速度开，一般几秒之内即可完成。

####缺点：   

* 1.若代码中使用了该图片，但是该代码被注释掉，或者没有被调用，工具无法检测出来。   
  例如：   
  
  ```
  // UIImage *image = [UIImage imageNamed:@"77.png"];
  
   /*
        UIImage *image = [UIImage imageNamed:[NSString stringWithFormat:@"aa_%d.png",1]];
    */
     
   ```
####总结：   
  经过简单demo测试后，发现这个工具还是非常好用的，方便快捷，强烈推荐使用。
  
  


****************************
使用8-bit的PNG图片    
　　比32-bit的图片能减少4倍的压缩率。由于8-bit的图片支持最多256种不同的颜色，所以8-bit的图片一般只应该用于一小部分的颜色图片。例如灰度图片最好使用8-bit。
针对32-bit的图片尽量使用高压缩的比率    
　　利用Adobe Photoshop的Save For Web可以减小JPEG和PNG的图片大小。在Xcode中，默认情况下，会自动的使用pngcrush来压缩.png图片。
****************************
对应用程序做一个完整性检查    
　　利用 Inspecting Your App 中介绍的流程，对.app bundle做一个全面的检查，以了解那些是真正需要用到的。在程序中，经常会包含一些额外的文件，例如readme之类的，这些从来都不会被用到。

****************************
编译选项    
　　将build setting中的Optimization Level设置为Fastest, Smallest [-Os]; 将build setting 中的Strip Debug Symbols During Copy设置为YES(COPY_PHASE_STRIP = YES)，这样可以减小编译出二进制文件的尺寸。这里提到的这些设置在Xcode工程中对于Release的配置是默认的。

　　警告：这些设置会让你的程序很难debug。在一般开发环境build中不建议这样设置，

****************************
iOS App Store相关因素    
　　作为提交到App Store中app里的可执行文件是被加过密的。加密的副作用是可执行文件的压缩效果没有之前的好了，因为加密会隐藏一些细节问题。因此，从App Store下载下来的.ipa文件大小要比从本地build出来的.ipa文件大。    
　　注意：将长文本内容和表数据等从代码中移除，并添加到外部文件中，这样可以减小最终安装包下载的大小——因为这些文件的压缩效果更好。    
　　如果你选择Organizer window中的某个archived，然后点击Estimate Size，Xcode可以对最终分发的程序尺寸做出一个评估。这里并不考虑Mac App Store上面的和企业级部署的iOS程序。

****************************
减少图片数量：减少图片生成，只采用 @3x。

减少不必要的系统和架构的适配。

****************************
资源优化：

去除无用资源：  
相同的资源但名字不同的；  
未使用的图片；    
一倍图资源；      
代码里面一些无用的文件，例如RENAME等。  

资源压缩：     
图片无损/有损压缩；[官网]（https://imageoptim.com/howto.html）  

音频压缩；         

使用ASSETS.XCASSETS来管理图片；         

h5页面远程化；  

动态下载资源；（字体、图片、配置数据等非必须资源） 

****************************

编译选项优化（Build Settings编译选项）： 

Optimization Level设置为Fastest,Smallest;(默认release是此选项)   
（Optimization Level 应该是编译器的优化程度。比较早期的时候，硬件资源是比较缺乏的。为了提高性能，开发编译器的大师们，都会对编译器(从c到汇编的编译过程)加上一定的优化策略。优化后的代码效率比较高，但是可读性比较差，且编译时间更长。优化是指编译器一级的措施，与机器指令比较接近，所以很可能会导致硬件不兼容，进而产生了目前遇到的软件装不上的问题。 他是编译器的行为，与代码理论上不相关。）    

把Enable bitcode 修改为YES;   
(bit code是什么：说的是bitcode是被编译程序的一种中间形式的代码。包含bitcode配置的程序将会在App store上被编译和链接。bitcode允许苹果在后期重新优化程序的二进制文件，而不需要重新提交一个新的版本到App store上。另外的解释：当提交程序到App store上时，Xcode会将程序编译为一个中间表现形式(bitcode)。然后App store会再将这个botcode编译为可执行的64位或32位程序。总之就是和瘦身有关。)    

Strip Debug Symbols During Copy设置为YES;    
新创建版本会默认YES。旧版本未知。作用还需研究下。    

Strip Linked Product设置成YES；   
作用是去掉调试的信息。（目前还要确认会否影响到错误日志的收集）   

Symbols hidden by default 选项设置为YES。   
作用:release时去除不必要的调试符号。      
参考：[symbolicate工具环境设置](http://blog.csdn.net/dnj630/article/details/7321101)   

Make Strings Read-Only设为YES。    
字面意思就是变为只读属性。详细作用需要调研。   

附录： 
[配置参数的理解](http://blog.csdn.net/iitvip/article/details/9118499)

****************************

文件优化：

去掉文件未使用的第三方库和框架；   

去掉未使用的类。

去掉未使用的代码。       

****************************

代码优化：   

重构代码，提高代码的重用性。    

如果要用第三方库里面一个属性或者方法。建议自己重写。    

****************************

将工程 BuildSetting 的 Levels 选项中的 Generate Debug Symbols 这个设置为 NO:   
当 Generate Debug Symbols 设置为 YES 时，编译产生的 .o 文件会大一些，当然最终生成的可执行文件也大一些。
当 Generate Debug Symbols 设置为 NO 的时候，在 Xcode 中设置的断点不会中断，同样生成的ipa安装包也会小一些。

****************************
适当舍弃架构 armv7:   
因为 armv7 用于支持 4s 和 3gs，4s是2011年11月正式上线，虽然还有小部分人在使用，如果是是追求包体大小的完全可以舍弃了。

****************************
删除无用的图片音频和视频文件:   
ipa 包的体积增大很大程度上取决于资源文件的大小。包括 Images.xcassets 中无用的图片， bundle 中的音频、视频、图片 和字体文件等。

****************************
代码及代码文件的优化:   
通过 AppCode 打开对应的工程文件 选择 Code -> inspect Code 分析代码，去掉无用的引用及代码。查找内部使用到的第三方库，一方面可以进行删减代码，用不到的类，可以直接删除，还有把第三方库中的图片资源删除掉

****************************
Optimization Level 等编译项优化:   
Build Settings->Optimization Level有几个编译优化选项，release版应该选择 Fastest, Smalllest ，这个选项会开启那些不增加代码大小的全部优化，并让可执行文件尽可能小。
Strip Linked Product / Deployment Postprocessing / Symbols Hidden by Default在release版本应该设为yes，可以去除不必要的调试符号。Symbols Hidden by Default会把所有符号都定义成”private extern”。。

****************************
如何查看 ipa 包中的大文件?   
  1. 找到自己打包后的 ipa ，然后右键，打开方式选择归档实用工具，就会解压出来一个名为 Payload 文件夹。
  2. 在Payload文件夹中找到当前ipa的app文件（基本就是和这个ipa名字一样的文件，app后缀系统默认隐藏），右键显示包内容。
  3. 进入到文件夹内，按照大小进行排序，你会发现所有的资源。

****************************
查找 iOS 工程无用图片资源工具:   
[LSUnusedResources](https://github.com/tinymind/LSUnusedResources)
  1. 点击 Browse，选择一个文件夹。
  2. 点击 Search 开始搜索。
  3. 等待片刻即可看到结果，可直接对搜索结果进行操作。

****************************
注意:   
针对减小 ipa 包体积的操作，我们必须考虑相关影响，以确保做出正确的决定。如果不做权衡的话，我们无法知道需要对程序做出什么样的改变。

****************************
删除无用和重复资源
检查工程里是否有重复和不需要的音频、图片等资源，有的话请及时删除
****************************
制作自己的库文件（.a 或者.Framework）时，只需保证真机运行的即可，不需制作出支持模拟器的库文件

****************************
音频的压缩
参考WWDC中的Audio Development for Games，里面介绍了如何有效的处理音频。常规来说，我们要使用AAC或MP3来压缩音频，并且可以尝试降低一下音频的比特率。有时候44.1khz的采样是没有必要的，稍微低一点的比特率也不会降低音频的质量。

****************************
iOS App Store相关因素
作为提交到App Store中app里的可执行文件是被加过密的。加密的副作用是可执行文件的压缩效果没有之前的好了，因为加密会隐藏一些细节问题。因此，从App Store下载下来的.ipa文件大小要比从本地build出来的.ipa文件大。
注意：将长文本内容和表数据等从代码中移除，并添加到外部文件中，这样可以减小最终安装包下载的大小——因为这些文件的压缩效果更好。
如果你选择Organizer window中的某个archived，然后点击Estimate Size，Xcode可以对最终分发的程序尺寸做出一个评估。这里并不考虑Mac App Store上面的和企业级部署的iOS程序。

****************************
（1）Strip Link Product设成YES，可执行文件减少 
（2）Make Strings Read-Only设为YES后可执行文件减少 
（3）去掉异常支持，Enable C++ Exceptions和Enable Objective-C Exceptions设为NO，并且Other C Flags添加-fno-exceptions，可执行文件减少了27M，其中__gcc_except_tab段减少了17.3M，__text减少了9.7M，效果特别明显。可以对某些文件单独支持异常，编译选项加上-fexceptions即可。但有个问题，假如ABC三个文件，AC文件支持了异常，B不支持，如果C抛了异常，在模拟器下A还是能捕获异常不至于Crash，但真机下捕获不了（有知道原因可以在下面留言：）。去掉异常后，Appstore后续几个版本Crash率没有明显上升。个人认为关键路径支持异常处理就好，像启动时NSCoder读取setting配置文件得要支持捕获异常，等等

****************************
将build setting中的Optimization Level设置为Fastest, Smallest [-Os]; 将build setting 中的Strip Debug Symbols During Copy设置为YES(COPY_PHASE_STRIP = YES)，这样可以减小编译出二进制文件的尺寸。这里提到的这些设置在Xcode工程中对于Release的配置是默认的。
警告：这些设置会让你的程序很难debug。在一般开发环境build中不建议这样设置，




