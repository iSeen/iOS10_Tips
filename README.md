<p align="center">
	<a target='_blank' href='https://github.com/iSeen'>
<img src='http://d.hiphotos.baidu.com/baike/w%3D268%3Bg%3D0/sign=8e71514fbafb43161a1f7d7c189f211e/7e3e6709c93d70cf88db9b89f0dcd100baa12b14.jpg' width='100'/>
</a>
  <br>
</p>



# iOS10 & Xcode8 开发适配

## 目录
###### iOS10:  

[Notification](#Notification)  
ps: 推送获取device token问题. 

[UI控件](#UI控件):   
UIStatusBar方法过期,  
UICollectionView 性能优化,   
UIColor 新增方法,  
UITextField添加textContentType枚举,   
字体随手机系统字体而改变,  
UIScrollView新增UIRefreshControl,  
Xib awakeFromNib的警告,  
UIWebView的代理方法

[隐私权限设置](#隐私权限设置)  
[自定义相机出现了问题，cameraViewTransform不能用](#自定义相机)
[App跳转设置](#App跳转设置)   

[系统判断方法失效](http://mp.weixin.qq.com/s?__biz=MzA3NzM0NzkxMQ==&mid=2655358043&idx=1&sn=a1433f4bed139ea18dccf625fde9fe3c&chksm=84e24173b395c8657776918340eb76b694b770bde044b64e6fd5c10301936a118fb8c33aadca&scene=1&srcid=0924CTuZrrHF6tXUBLnOMExq#rd) 

###### Xcode 8:
[证书管理](#证书管理)  
[Xib文件](#Xib文件)  
[不支持7.0](#不支持7.0)

[ATS](#ATS)  
[控制台杂乱Log屏蔽](#Log屏蔽)  
[编译过慢](#编译过慢)  


---
## <a id="Notification"></a>Notification
主要适配:接收通知数据的回调方法.  

iOS 10 中将通知相关的 API 都统一了,在此基础上很多用户定义的通知,并且可以捕捉到各个通知状态的回调.以前通知的概念是:大家想接受的提前做好准备,然后一下全两分发,没收到也不管了,也不关心发送者,现在的用户通知做成了类似于网络请求,先发一个request得到response的流程,还封装了error,可以在各个状态的方法中做一些额外的操作,并且能获得一些字段,比如发送者之类的.这个功能的头文件是:#import

主要有以下文件:

	#import <UserNotifications/NSString+UserNotifications.h>
	#import <UserNotifications/UNError.h>
	#import <UserNotifications/UNNotification.h>
	#import <UserNotifications/UNNotificationAction.h>
	#import <UserNotifications/UNNotificationAttachment.h>
	#import <UserNotifications/UNNotificationCategory.h>
	#import <UserNotifications/UNNotificationContent.h>
	#import <UserNotifications/UNNotificationRequest.h>
	#import <UserNotifications/UNNotificationResponse.h>
	#import <UserNotifications/UNNotificationSettings.h>
	#import <UserNotifications/UNNotificationSound.h>
	#import <UserNotifications/UNNotificationTrigger.h>
	#import <UserNotifications/UNUserNotificationCenter.h>
	#import <UserNotifications/UNNotificationServiceExtension.h>

##### iOS 9 以前的通知:

1.在调用方法时，有些方法让人很难区分，容易写错方法，这让开发者有时候很苦恼。

2.应用在运行时和非运行时捕获通知的路径还不一致。

3.应用在前台时，是无法直接显示远程通知，还需要进一步处理。

4.已经发出的通知是不能更新的，内容发出时是不能改变的，并且只有简单文本展示方式，扩展性根本不是很好。


##### iOS 10 开始的通知:

1.所有相关通知被统一到了UserNotifications.framework框架中。

2.增加了撤销、更新、中途还可以修改通知的内容。

3.通知不在是简单的文本了，可以加入视频、图片，自定义通知的展示等等。

4.iOS 10相对之前的通知来说更加好用易于管理，并且进行了大规模优化，对于开发者来说是一件好事。

5.iOS 10开始对于权限问题进行了优化，申请权限就比较简单了(本地与远程通知集成在一个方法中)。

##### 文章解析
[活久见的重构 - iOS 10 UserNotifications 框架解析](https://onevcat.com/2016/08/notification)  
[iOS10全新推送功能的实现](http://blog.csdn.net/u012847940/article/details/51801078)  


##### 推送获取device token问题
推送中在获取device token时，会报如下错误.  
![](http://upload-images.jianshu.io/upload_images/1682338-cadbd7870170bbaa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

解决方法: 打开设置
![](http://upload-images.jianshu.io/upload_images/1682338-1d5ac88da5b4d9aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
会自动生成.entitlements文件, 打开如下显示  
![](http://upload-images.jianshu.io/upload_images/1682338-ce1098fa4399b53c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)  
字段对应两种状态：development和production，然后再运行，即可成功获取device token了。






---
## <a id="UI控件"></a>UI控件
#### UIStatusBar方法过期
在iOS10中,如果还使用以前设置UIStatusBar类型或者控制隐藏还是显示的方法,会报警告,方法过期,如下图:  
![](http://mmbiz.qpic.cn/mmbiz_png/8RTSPr4mlykibVrolAzic1tVSb66uiafQoHLh8T6uGoZ5TCBWoDljQf4SFSOEibfc3nIia4lyic6HcjOgYHD7kN5HibTg/640?wx_fmt=png&wxfrom=5&wx_lazy=1)

上面方法到 iOS 10 不能使用了,要想修改UIStatusBar的样式或者状态使用下图中所示的属性或方法:
	@property(nonatomic, readonly) UIStatusBarStyle preferredStatusBarStyle NS_AVAILABLE_IOS(7_0) __TVOS_PROHIBITED; // Defaults to UIStatusBarStyleDefault
	@property(nonatomic, readonly) BOOL prefersStatusBarHidden NS_AVAILABLE_IOS(7_0) __TVOS_PROHIBITED; // Defaults to NO
	- (UIStatusBarStyle)preferredStatusBarStyle NS_AVAILABLE_IOS(7_0) __TVOS_PROHIBITED; // Defaults to UIStatusBarStyleDefault
	- (BOOL)prefersStatusBarHidden NS_AVAILABLE_IOS(7_0) __TVOS_PROHIBITED; // Defaults to NO
	// Override to return the type of animation that should be used for status bar changes for this view controller. This currently only affects changes to prefersStatusBarHidden.
	- (UIStatusBarAnimation)preferredStatusBarUpdateAnimation NS_AVAILABLE_IOS(7_0) __TVOS_PROHIBITED; // Defaults to UIStatusBarAnimationFade



		//iOS 10 可查看官方API
		- (UIStatusBarStyle)preferredStatusBarStyle {
		    return UIStatusBarStyleDefault;
		}

#### UICollectionView 性能优化
[WWDC2016 Session笔记 - iOS 10 UICollectionView新特性](http://www.jianshu.com/p/e97780a24224)

在iOS 10 之前,UICollectionView上面如果有大量cell,当用户活动很快的时候,整个UICollectionView的卡顿会很明显,为什么会造成这样的问题,这里涉及到了iOS 系统的重用机制,当cell准备加载进屏幕的时候,整个cell都已经加载完成,等待在屏幕外面了,也就是整整一行cell都已经加载完毕,这就是造成卡顿的主要原因,专业术语叫做:掉帧.

要想让用户感觉不到卡顿,我们的app必须帧率达到60帧/秒,也就是说每帧16毫秒要刷新一次.

iOS 10 之前UICollectionViewCell的生命周期是这样的:

    1.用户滑动屏幕,屏幕外有一个cell准备加载进来,把cell从reusr队列拿出来,然后调用prepareForReuse方法,在这个方法里面,可以重置cell的状态,加载新的数据;
    2.继续滑动,就会调用cellForItemAtIndexPath方法,在这个方法里面给cell赋值模型,然后返回给系统;
    3.当cell马上进去屏幕的时候,就会调用willDisplayCell方法,在这个方法里面我们还可以修改cell,为进入屏幕做最后的准备工作;
    4.执行完willDisplayCell方法后,cell就进去屏幕了.当cell完全离开屏幕以后,会调用didEndDisplayingCell方法.

iOS 10 UICollectionViewCell的生命周期是这样的:

    1.用户滑动屏幕,屏幕外有一个cell准备加载进来,把cell从reusr队列拿出来,然后调用prepareForReuse方法,在这里当cell还没有进去屏幕的时候,就已经提前调用这个方法了,对比之前的区别是之前是cell的上边缘马上进去屏幕的时候就会调用该方法,而iOS 10 提前到cell还在屏幕外面的时候就调用;
    2.在cellForItemAtIndexPath中创建cell，填充数据，刷新状态等操作,相比于之前也提前了;
    3.用户继续滑动的话,当cell马上就需要显示的时候我们再调用willDisplayCell方法,原则就是:何时需要显示,何时再去调用willDisplayCell方法;
    4.当cell完全离开屏幕以后,会调用didEndDisplayingCell方法,跟之前一样,cell会进入重用队列.
    在iOS 10 之前,cell只能从重用队列里面取出,再走一遍生命周期,并调用cellForItemAtIndexPath创建或者生成一个cell.
    在iOS 10 中,系统会cell保存一段时间,也就是说当用户把cell滑出屏幕以后,如果又滑动回来,cell不用再走一遍生命周期了,只需要调用willDisplayCell方法就可以重新出现在屏幕中了.
    iOS 10 中,系统是一个一个加载cell的,二以前是一行一行加载的,这样就可以提升很多性能;
    iOS 10 新增加的Pre-Fetching预加载
    这个是为了降低UICollectionViewCell在加载的时候所花费的时间,在 iOS 10 中,除了数据源协议和代理协议外,新增加了一个UICollectionViewDataSourcePrefetching协议,这个协议里面定义了两个方法:

- (void)collectionView:(UICollectionView *)collectionView prefetchItemsAtIndexPaths:(NSArray<NSIndexPath *> *)indexPaths NS_AVAILABLE_IOS(10_0);

- (void)collectionView:(UICollectionView *)collectionView cancelPrefetchingForItemsAtIndexPaths:(NSArray<NSIndexPath *> *)indexPaths  NS_AVAILABLE_IOS(10_0);

在ColletionView prefetchItemsAt indexPaths这个方法是异步预加载数据的,当中的indexPaths数组是有序的,就是item接收数据的顺序;

CollectionView cancelPrefetcingForItemsAt indexPaths这个方法是可选的,可以用来处理在滑动中取消或者降低提前加载数据的优先级.

注意:这个协议并不能代替之前读取数据的方法,仅仅是辅助加载数据.

Pre-Fetching预加载对UITableViewCell同样适用.


#### UIColor 新增方法
iOS 10 苹果官方建议我们使用sRGB，因为它性能更好，色彩更丰富。

	UIColor类中新增了两个Api如下:
	+ (UIColor *)colorWithDisplayP3Red:(CGFloat)displayP3Red green:(CGFloat)green blue:(CGFloat)blue alpha:(CGFloat)alpha NS_AVAILABLE_IOS(10_0);
	
	- (UIColor *)initWithDisplayP3Red:(CGFloat)displayP3Red green:(CGFloat)green blue:(CGFloat)blue alpha:(CGFloat)alpha NS_AVAILABLE_IOS(10_0);

#### UITextField添加textContentType枚举

在iOS 10 中,UITextField新增了textContentType字段,是UITextContentType类型,它是一个枚举,作用是可以指定输入框的类型,以便系统可以分析出用户的语义.是电话类型就建议一些电话,是地址类型就建议一些地址.可以在#import文件中,查看textContentType字段.

	// The textContentType property is to provide the keyboard with extra information about the semantic intent of the text document.
	
	@property(nonatomic,copy) UITextContentType textContentType NS_AVAILABLE_IOS(10_0); // default is nil

在iOS 10 UITextField添加了textContentType枚举，指示文本输入区域所期望的语义意义。

使用此属性可以给键盘和系统信息，关于用户输入的内容的预期的语义意义。例如，您可以指定一个文本字段，用户填写收到一封电子邮件确认uitextcontenttypeemailaddress。当您提供有关您期望用户在文本输入区域中输入的内容的信息时，系统可以在某些情况下自动选择适当的键盘，并提高键盘修正和主动与其他文本输入机会的整合。


#### 字体随手机系统字体而改变, 还会导致文字显示不全问题

字体变大，原有frame需要适配.  
当我们手机系统字体改变了之后，那我们App的label也会跟着一起变化，这需要我们写很多代码来进一步处理才能实现，但是iOS 10 提供了这样的属性adjustsFontForContentSizeCategory来设置。因为没有真机，具体实际操作还没去实现，如果理解错误帮忙指正。

  UILabel *myLabel = [UILabel new];
   /*
    UIFont 的preferredFontForTextStyle: 意思是指定一个样式，并让字体大小符合用户设定的字体大小。
   */
    myLabel.font =[UIFont preferredFontForTextStyle: UIFontTextStyleHeadline];

     //是否更新字体的变化
    myLabel.adjustsFontForContentSizeCategory = YES;


Q：xcode7编译的包，动态计算一段文字的高度时，计算出来的高度在iOS10设备上不足以容纳这段文字
A：因为iOS10更换了系统字体，所以会导致文字的大小跟iOS9及之前的系统有点区别，如果继续使用xcode7编译的话，建议在文字高度上多加5。
或者直接使用xcode8编译的包也可以，文字的高度计算结果是正确的。
至于类似YYText这样的库，底层使用CoreText写的，xcode7、xcode8上都会存在这个问题，在最后的计算出来的高度上直接加5就ok了。



#### UIScrollView新增UIRefreshControl
	
	@property (nonatomic, strong, nullable) UIRefreshControl *refreshControl NS_AVAILABLE_IOS(10_0) __TVOS_PROHIBITED;
	
在iOS 10 中, UIRefreshControl可以直接在UICollectionView和UITableView中使用,并且脱离了UITableViewController.现在RefreshControl是UIScrollView的一个属性.
使用方法:

	UIRefreshControl *refreshControl = [[UIRefreshControl alloc] init];
	[refreshControl addTarget:self action:@selector(loadData) forControlEvents:UIControlEventValueChanged];
	collectionView.refreshControl = refreshControl;


#### Xib awakeFromNib的警告
在Xcode 8会有如下警告：
![](http://upload-images.jianshu.io/upload_images/2353624-7e457c7877d6bcf7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
若想去掉警告，应该明确加上[super awakeFromNib];


#### UIWebView的代理方法
**注意要删除NSError前面的 nullable，否则报错。

	- (void)webView:(UIWebView *)webView didFailLoadWithError:(nullable NSError *)error
	{}


---
## <a id="隐私权限设置"></a>隐私权限设置
隐私权限更加严格，如果不设置就会直接崩溃. 解决办法都是在info.plist文件添加对应的Key-Value就可以.   
注意，添加的时候，末尾不要有空格.
![](http://upload-images.jianshu.io/upload_images/2353624-dd84d7146f413f7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如何设置: [兼容iOS 10：配置获取隐私数据权限声明](http://www.jianshu.com/p/616240463a7a)


---
## <a id="自定义相机"></a>自定义相机出现了问题，cameraViewTransform不能用

	很多人反映自定义相机出现了问题，cameraViewTransform不能用了，其实网上关于这个的资料不是很多，在这里提供参考办法如下：
	
	通过监听AVCaptureSessionDidStartRunningNotification来解决
	
	//#import <AVFoundation/AVFoundation.h>
	//监听
	[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(cameraNotification:) name:AVCaptureSessionDidStartRunningNotification object:nil];
	//监听方法
	- (void)cameraNotification:(NSNotification *)notification {
	dispatch_async(dispatch_get_main_queue(), ^{
	    // 这里实现
	    imagePickerController.cameraViewTransform = CGAffineTransformMakeTranslation(50,  50);
	});
	}
	
	使用AVFoundation框架
	看来UIImagePickerController视乎不在适用于iOS 10了。所以说可以选择AVFoundation来解决这个问题。


---
## <a id="App跳转设置"></a>App跳转设置
	NSURL *url = [NSURL URLWithString:@"prefs:root=bundleIn identifier"];  
	以上方法之前在iOS7-9正常跳转, iOS10要改为以下方法:  
	NSURL *url = [NSURL URLWithString:UIApplicationOpenSettingsURLString];
		
	if ([[UIApplication sharedApplication] canOpenURL:url]) {
	  [[UIApplication sharedApplication] openURL:url];
	}

UIApplicationOpenSettingsURLString为iOS8的:  
若应用无需适配iOS7, 则可以直接使用UIApplicationOpenSettingsURLString;  
若应用需要适配iOS7, 则判断系统版本, 7使用"prefs:root=...", >=8使用UIApplicationOpenSettingsURLString.




Xcode 8:

---
## <a id="证书管理"></a>证书管理
自动管理证书: 建议大家勾选这个Automatically manage signing
添加苹果账号, 添加设备.
![](http://static.zybuluo.com/coderXu/1567ki636zl9z3t7rhs7zsmw/QQ20160913-8.png)


---
## <a id="Xib文件"></a>Xib文件

使用Xcode8打开xib文件后，会出现下图的提示, 选择Choose Device即.
![](http://static.zybuluo.com/coderXu/g61l7p3rpipveka1f9yzta6i/QQ20160913-9.png)  

若布局/frame杂乱，可更新一下frame.
![](http://static.zybuluo.com/coderXu/yulrjsd3qs4hhabt16iyikzn/QQ20160913-11.png)

注意：如果按上面的步骤操作后，在用Xcode7打开Xib会报一下错误.
![](http://static.zybuluo.com/coderXu/u6tw3mdjonjspmh0h9uham98/QQ20160913-12.png)


解决办法：

	<capability name="documents saved in the Xcode 8 format" minToolsVersion="8.0"/>

需要删除Xib里面上面这句话, 以及把< document >中的toolsVersion和< plugIn >中的version改成你正常的xib文件中的值，不过不建议这么做，在Xcode8出来后，希望大家都快速上手，全员更新。


---
## <a id="插件不能用"></a>插件不能用
因苹果解决xcode ghost，把插件屏蔽了。  
解决方法: 打开终端，命令运行： sudo /usr/libexec/xpccachectl  
然后必须重启电脑后生效.

注意：Xcode8内置了开启注释的功能，位置在这里.
![](http://upload-images.jianshu.io/upload_images/707724-9ace6550ccedaa6c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


---
## <a id="不支持7.0"></a>不支持7.0
手动输入7.0即可, Xcode8编译的ipa包，装在iOS7设备上可以正常工作.
![](http://upload-images.jianshu.io/upload_images/1682338-0fe018dcb0434d93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



---
## <a id="ATS"></a>ATS
从 2017 年 1 月 1 日起，所有的新提交 app 默认是不允许使用 NSAllowsArbitraryLoads 来绕过 ATS 限制的，也就是说，我们最好保证 app 的所有网络请求都是 HTTPS 加密的，否则可能会在应用审核时遇到麻烦。  
[关于iOS10 中ATS的问题](https://onevcat.com/2016/06/ios-10-ats/)  


---
## <a id="Log屏蔽"></a>控制台杂乱Log屏蔽
![](http://mmbiz.qpic.cn/mmbiz_png/k0UVxv3BTLLbiadjH7vtiaIjgkNBKhxo9lg3TW5Hm2Ttfs7VorUJQTeIZBxxwK48U9cAIVK0opk1KrlmtcjRfSCQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1)
解决办法是设置OS_ACTIVITY_MODE : disable  
[stackoverflow问答](http://stackoverflow.com/questions/37800790/hide-xcode-8-logs)

![](http://mmbiz.qpic.cn/mmbiz_png/k0UVxv3BTLLbiadjH7vtiaIjgkNBKhxo9liaE4XkSKApWrXaVUZ2ayIR5sXdHvCyiatAicMQDvmbB3MIkgRptFCCAGg/640?wx_fmt=png&wxfrom=5&wx_lazy=1)  


---
## <a id="编译过慢"></a>编译过慢
[提高Xcode编译速度](http://nszzy.me/2016/03/20/reduce-xcode-build-times/)




摘自部分文章:  

[iOS 10 的适配问题](http://mp.weixin.qq.com/s?__biz=MzA3NzM0NzkxMQ==&mid=2655358043&idx=1&sn=a1433f4bed139ea18dccf625fde9fe3c&chksm=84e24173b395c8657776918340eb76b694b770bde044b64e6fd5c10301936a118fb8c33aadca&scene=1&srcid=0924CTuZrrHF6tXUBLnOMExq#rd)

[iOS10适配总结](http://www.jianshu.com/p/c2bb07786fd1)  

[iOS开发 适配iOS10以及Xcode8](http://www.jianshu.com/p/9756992a35ca)




