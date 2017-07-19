Vbyte P2P IOS SDK
===

[![CI Status](http://img.shields.io/travis/huster-zhangpeng/VbyteP2P.svg?style=flat)](https://travis-ci.org/huster-zhangpeng/VbyteP2P)
[![Version](https://img.shields.io/cocoapods/v/VbyteP2P.svg?style=flat)](http://cocoapods.org/pods/VbyteP2P)
[![License](https://img.shields.io/cocoapods/l/VbyteP2P.svg?style=flat)](http://cocoapods.org/pods/VbyteP2P)
[![Platform](https://img.shields.io/cocoapods/p/VbyteP2P.svg?style=flat)](http://cocoapods.org/pods/VbyteP2P)

vbyte云视频解决方案，可帮助用户直接使用经过大规模验证的直播流媒体分发服务，通过vbyte成熟的P2P技术大幅节省带宽，提供更优质的用户体验。开发者可通过SDK中简洁的接口快速同自有应用集成，实现iOS设备上的视频P2P加速功能。

### 功能

- 直播、点播基本功能+P2P
- 直播时移支持
- 直播HLS P2P原生支持
- 防盗播支持

### 依赖安装

IOS SDK托管于第三方平台[CocoaPods][6]上，依赖部署是非常简单的。凭借这设计良好的接口，在使用上也非常方便。

- 由于依赖库较大，需要[`git-lfs`][8]来管理，所以首先安装[`git-lfs`][8]，以在git clone时将依赖库能下载下来

```bash
# 参考：https://git-lfs.github.com/
# 如果你使用的是Homebrew，可以如下安装 
$ brew install git-lfs
# 系统默认使用MacPorts安装 
$ port install git-lfs

# 安装完别忘记先在系统内初始化，只需要执行一次，以后每个项目都可以使用
$ git lfs install
```

- 将以下依赖加进项目的Podfile中

```ruby
pod 'VbyteP2P', '~> 1.0'
```

- 在项目根目录下，执行`pod install`

```bash
cd /path/to/your/application
pod install
```
- 打开自动生成的xcworkspace，在代码中引入`#import <VbyteP2P/VPModule.h>`即可使用

### 开始使用

- 首先参考[资源管理][3]在[开发者中心][1]上注册帐号，创建应用，创建应用时要写对Bundle id。然后得到相应的app id,app key与app secret key
- 第二步，P2P模块拥有命名空间VP，应用生命周期管理：在应用启动之初，启动VPModule；在应用结束时销毁VPModule
```Objective-c
// Example: 程序的入口AppDelegate.m

#import "AppDelegate.h"
#import <VbyteP2P/VPModule.h>

@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    // do something other...

    // 初始化P2P
    NSString *appID = @"577cddf55aa77e385435dcff";
    NSString *key = @"ZiMAWNyAdKhjATiK";
    NSString *secret = @"NxSMiy6VUqRel1Cf5OLoCJSjZDQFgaC4";
    [VPModule init:appID appKey:key appSecretKey:secret];
    
    return YES;
}

// something other function...

- (void)applicationWillTerminate:(UIApplication *)application
{
    // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.

    // 释放P2P模块
    [VPModule release];
}

@end
```

#### 使用直播

直播大家都很熟悉，观众一进来都是直接看到最新的直播内容，本是没有暂停、随机播放（回看）功能。但是应时移回看需求的增长，我们的SDK也提供了时移回看的方式，详细见[API文档][2]。

- 启动一个直播频道的过程如下，其中第2个参数是写死的，必须为UHD；未来多码率支持可能会有HD等更多参数选择:
```Objective-c
// Example: LiveVideoController.m

#import <VbyteP2P/VPModule.h>
#import <VbyteP2P/VPLiveController.h>

- (void)viewDidLoad
{
    [super viewDidLoad];
    
    // do something others as init player and view

    [VPLiveController load:[channel absoluteString] resolution:@"UHD" listener:^(NSURL *url){
        if (url != nil) {
            self.url = url
            [self.player prepareToPlay:[url absoluteString]];
        }
    }];
}
```

- 退出当前直播播放频道，只需要调用unload即可
```Objective-c
// Example: LiveVideoController.m

#import <VbyteP2P/VPModule.h>
#import <VbyteP2P/VPLiveController.h>

- (void)viewDidDisappear:(BOOL)animated {
    [super viewDidDisappear:animated];
    [self.player shutdown];
    [self removeMovieNotificationObservers];
    
    // 直播停止的地方
    [VPLiveController unload];
}
```

#### 使用点播

点播与直播最大的不同是点播视频是固定的，包括文件大小固定、内容固定、视频时长固定，有暂停、恢复、随机播放等操作。

- 启动一个点播频道的过程如下:
```Objective-c
// Example: VodVideoController.m

#import <VbyteP2P/VPModule.h>
#import <VbyteP2P/VPVodController.h>

@interface MyUrlGenerator : NSObject<VPUrlGenerator>
@end

@implementation MyUrlGenerator

- (VPSecurityUrl*) createSecurityUrl: (NSString *) sourceId
{
    // TODO: 在此根据点播资源sourceId实现防盗链url的生成
}

@end

@implementation VodVideoController

- (void)viewDidLoad
{
    [super viewDidLoad];
    
    // do something others as init player and view

    // 设置防盗链url生成器
    [VPVodcontroller setUrlGenerator: [[MyUrlGenerator alloc] init]];
    // 开始加载
    [VPVodController load:[url absoluteString] resolution:@"UHD" startTime:0 listener:^(NSURL *url){
        if (url != nil) {
            self.url = url
            [self.player prepareToPlay:[url absoluteString]];
        }
    }];
}

@end
```
- 暂停和恢复当前点播节目:
```Objective-c
// 在播放器暂停按钮按下时，应调用
[VPVodController pause];
// 在播放器播放按钮按下时，恢复播放
[VPVodController resume];
```
- 点播视频支持在不重启播放器的情况下随机位置播放，这在观众滑动进度条时发生，此时播放器会从进度条位置重新加载，而P2P模块能自动感知这样的seek行为，您不用为此做什么。
- 退出当前点播播放频道，只需要调用unload即可
```Objective-c
// Example: VodVideoController.m

#import <VbyteP2P/VPModule.h>
#import <VbyteP2P/VPVodController.h>

- (void)viewDidDisappear:(BOOL)animated {
    [super viewDidDisappear:animated];
    [self.player shutdown];
    [self removeMovieNotificationObservers];
    
    // 直播停止的地方
    [VPVodController unload];
}
```

#### 高级功能

更多高级功能诸如开启debug开关、事件监听、直播时移等请参见[IOS版API][2]文档，然后就可以尽情地使用P2P SDK带来的便利功能吧！

### 扩展链接

* **[Github][4]**: SDK的开源代码仓库
* **[IOS Sample][7]**: IOS的一个项目示例
* **[API Doc][2]**: 更加详细的API文档，其中包含如直播时移的高级功能使用方法

### 技术支持

感谢阅读本篇文档，希望能帮您尽快上手IOS SDK的用法，再次欢迎您使用月光石P2P加速SDK！

*温馨提示*：如果你需要任何帮助，或有任何疑问，请[联系我们](mailto:contact@exatech.cn)。

[1]: http://devcenter.vbyte.cn
[2]: /api/ios/
[3]: /manage/base/
[4]: https://github.com/Vbytes/ios-framework
[5]: https://developer.apple.com/library/content/documentation/MacOSX/Conceptual/BPFrameworks/Tasks/IncludingFrameworks.html
[6]: https://cocoapods.org/pods/VbyteP2P
[7]: https://github.com/Vbytes/ios-sample
[8]: https://git-lfs.github.com/
