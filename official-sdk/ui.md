# ui



##### NavigationController 手势导致整个程序页面不响应问题
使用 UINavigationController ，系统自带的附加了一个从屏幕左边缘开始滑动可以实现 pop 的手势。    
但是，如果自定义了 navigationItem 的 leftBarButtonItem，那么这个手势就会失效。解决方法有很多种  	  

1、重新设置手势的 delegate

self.navigationController.interactivePopGestureRecognizer.delegate = (id<UIGestureRecognizerDelegate>)self;	

2、自定义手势事件

[self.navigationController.interactivePopGestureRecognizer addTarget:self action:@selector(handleGesture:)];

我使用了第一种方案，继承 UINavigationController 写了一个子类，直接设置 delegate 到 self 。并且让 gesture 始终可以 begin	

	-(BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer	
	{		
		return YES;		
	}		

但是出现很多问题，比如说在 rootViewController 的时候这个手势也可以响应，导致整个程序页面不响应；

如果在 push 的同时我触发这个手势，那么会导致 navigationBar 错乱，甚至 crash；

push 了多层后，快速的触发两次手势，也会错乱。

尝试了种种方案后我的解决方案是加个判断，代码如下，这次运行良好了。

@interface NavRootViewController : UINavigationController<UIGestureRecognizerDelegate,UINavigationControllerDelegate>	
 
 
@property(nonatomic,weak) UIViewController* currentShowVC;

 
@end

　　

@implementation NavRootViewController    

 
-(id)initWithRootViewController:(UIViewController *)rootViewController

{

	NavRootViewController* nvc = [super initWithRootViewController:rootViewController];

	self.interactivePopGestureRecognizer.delegate = self;

	nvc.delegate = self;

	return nvc;

}   
 

-(void)navigationController:(UINavigationController *)navigationController willShowViewController:(UIViewController *)viewController
animated:(BOOL)animated

{

 
}
 

-(void)navigationController:(UINavigationController *)navigationController didShowViewController:(UIViewController *)viewController animated:(BOOL)animated

{

	if (navigationController.viewControllers.count == 1)
	
	   self.currentShowVC = Nil;
	   
	else
	
	  self.currentShowVC = viewController;
	  
}
 
 

-(BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer

{

	if (gestureRecognizer == self.interactivePopGestureRecognizer) {
	
	return (self.currentShowVC == self.topViewController);
	
	}
	
	return YES;
	
}



#### 2016-06-02
1.tableview 中用xib创建cell时，第一个cell位置有时候会与顶部有一段距离   

去掉该顶部距离方法：   

创建 tableHeaderView ，通过改变tableHeaderView的大小来改变与顶部的距离。    

<p code>
UIView *tableHeaderView = [[UIView alloc] initWithFrame:CGRectMake(0, 0, 1, 0.1)];    // 设置高度为0.1
tableView.tableHeaderView = tableHeaderView;
</p>

注意：此处若直接设置headview 为空，则无任何作用    
<p>
tableView.tableHeaderView = nil;
</p>
=======

#### 2016-05-12

#### 2016-04-15 (五)    

1、iOS 中分页视图控制器的管理手动调用viewController 中的viewWillAppear，viewDidAppear,等懒加载方法的调用。    

描述：在一些比较复杂的页面里，有时候我们不希望在一个页面里面写很多的控件，而希望把一部分的控件放在一个视图控制器管理起来，这样我们就只需要将这几个视图控制器协调好、组织好，至于每个视图控制器里面写什么外层不用理会。很多时候我们希望在ScrollerView中放几个ViewController的View，这样方便我们切换ScrollerView的View的时候，重新把回调设置给其他的视图控制器，如果操作不当View对应的ViewController的viewWillAppear，viewDidAppear,等等懒加载方法根本不会调用，经过不断的研究测试，总结到了以下几点，当要调用者些个懒加载的方法时，我们要将视图控制器的View从父视图中先移除，当需要一个新的视图控制器需要显示的时候，很多时候我们需要注意：
1、首先应把当前显示的视图控制器的View  从父视图中移除.
2、将要显示的视图控制器，调用将要移到父视图控制器的方法。
3、将新的视图控制器的View  添加到父视图控制器上面。
4、将要显示的视图控制器，调用已经移到父视图控制器的方法。
5、将当前将要显示的视图控制器设置为当前视图控制器。
     ……
代码表示如下：

```
    [_currentVc.view removeFromSuperview];
    [self addChildViewController:newController];
    //这里可是适当设置一些viewController的View的属性    
    [newController willMoveToParentViewController:self];
    [self.scrollerView addSubview:newController.view];
    [newController didMoveToParentViewController:self];
    self.currentVc = newController;
```

经过了这些步骤，我们就可以通过ScrollerView翻页来切换视图控制了。    