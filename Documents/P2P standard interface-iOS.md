
# P2P标准接口文档-iOS平台

## Concepts and usage 定义和用途

本文档定义了iOS平台上P2P相关的接口标准，以及典型应用流程。

## Defination 定义

P2PModule: P2P模块，P2P所有功能在此模块内实现.

## Interfaces 接口列表

#### Constructor 构造方法
**P2PModule.init:(NSString *)appId appKey:(NSString *)appKey appSecretKey:(NSString *)appSecretKey**

此接口用以完成P2P模块的创建。为实现鉴权，需传入appId，appKey，appSecretKey。

- appId [String] - 客户的应用Id
- appKey [String] - 客户的应用key
- appSecret [String] - 客户的应用secret

**P2PModule.release**

析构方法，释放P2P模块。

---

#### Methods 方法

**P2PModule.getVersion**

获取SDK版本号。

- return [NSString] - SDK版本信息

**P2PModule.setDelegate: (id<p2peventdelegate>) aDelegate**

设置事件处理函数，以处理P2PModule抛出的内部事件。事件标准参见“事件”一节。

- aDelegate [id<p2peventdelegate>] 事件处理协议

**P2PModule.load: (NSString *)liveUrl listener:(void(^)(NSURL*))listener**

启动一次直播下载。

- liveUrl [String] 直播url
- listener [listener] 回调函数，加载成功后会返回一个本地代理uri,播放器直接播放该uri即可

**LiveController.unload**

关闭一次直播下载，与load成对出现。

**VodController.load: String url, listener:(void(^)(NSURL*))listener**

启动一次点播下载。

- url [String] 点播url
- listener [listener] 回调函数，加载成功后会返回一个本地代理uri,播放器直接播放该uri即可

**VodController.unload**

关闭一次点播下载，与load成对出现。

**VodController.pause**

暂停P2P模块下载，播放暂停时调用。

**VodController.resume**

恢复P2P模块下载，播放暂停后恢复播放时调用。

---

#### Events 事件
| 事件 | 事件描述 | 建议处理措施 |
| :------------ | :------------ | :------------ |
| P2PModule.Error.AUTH_FAILED | P2P模块认证失败 | 检查是否传参错误 |
| P2PModule.Error.CHANNEL_INVALID | 直播频道不存在 | 回退至CDN播放 |
| P2PModule.Error.PLAY_FAILED | P2P播放失败 | 回退至CDN播放 |

---

## Example 用例

```Objective-c
    // Example: 应用的入口AppDelegate.m
    #import <P2PModule.h>

    - (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
        ...
        [P2PModule init:appID appKey:key appSecretKey:secret];
        ...
    }

    - (void)applicationWillTerminate:(UIApplication *)application {
        ...
        // 释放P2P模块
        [VPModule release];
        ...
    }

```

```Objective-c
    // Example: LiveVideoController.m

    #import <P2PModule.h>
    #import <P2PLiveController.h>

    - (void)viewDidLoad {
        [super viewDidLoad];
    
        [P2PLiveController load:[liveUrl absoluteString] listener:^(NSURL *url){
            if (url != nil) {
                // start to play proxy url
                [self.player prepareToPlay:[url absoluteString]];
            }
        }];
    }

    - (void)viewDidDisappear:(BOOL)animated {
        [super viewDidDisappear:animated];
        [self.player shutdown];
    
        [P2PLiveController unload];
    }
```


---

## Compatibility 兼容性


---

## See Also 相关资料
 
- [腾讯云X-P2P —— iOS SDK开发指南](https://github.com/Vbytes/ios-sample/blob/master/README.md)

---

## Contributors 贡献者

**Teams** 

![image](https://imgcache.qq.com/open_proj/proj_qcloud_v2/gateway/event/pc/tcc2017/css/img/logo1.png) | 腾讯云
---|---
![image](https://i.h2.pdim.gs/b2a97149ec43dfc95eb177508af29f6c.png) | 熊猫直播

**Individuals**   
-