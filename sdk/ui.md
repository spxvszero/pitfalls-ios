# UI

### 1.`NavigationController` 手势导致整个程序页面不响应

#### 环境参数：

[参数描述]

#### 问题分析：

使用 `UINavigationController` 时，系统自带的附加了一个从屏幕左边缘开始滑动可以实现 pop 的手势。但是，如果自定义了 navigationItem 的 leftBarButtonItem，那么这个手势就会失效。

#### 解决方法：



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


### 2.tableview 中用xib创建cell时，第一个cell位置有时候会与顶部有一段距离 

#### 环境参数：

[参数描述]

#### 问题分析：

[分析描述]

#### 解决方法：

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




