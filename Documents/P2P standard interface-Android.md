# P2P标准接口文档-iOS平台

## Concepts and usage 定义和用途

本文档定义了Android平台上P2P相关的接口标准，以及典型应用流程。

## Defination 定义

P2PModule: P2P模块，P2P所有功能在此模块内实现.

## Interfaces 接口列表

#### Constructor 构造方法
**P2PModule.create(Contect context, String appId, String appKey, String appSecretKey)**

此接口用以完成P2P模块的创建,创建后即产生LiveController和VodController。为实现鉴权，需传入应用context以及appId，appKey，appSecretKey。

- context [Context] - Android应用上下文
- appId [String] - 客户的应用Id
- appKey [String] - 客户的应用key
- appSecret [String] - 客户的应用secret


---


#### Methods 方法

**P2PModule.getVersion()**

获取SDK版本号。

- return [String] - SDK版本信息

**P2PModule.setEventHandler(Handler errorHandler)**

设置事件处理函数，以处理P2PModule抛出的内部事件。事件标准参见“事件”一节。

- errorHandler [Handler] 事件处理函数

**LiveController.getInstance()**

获取单例的直播下载模块。

- return LiveController - 直播下载模块

**LiveController.load(String liveUrl, OnLoadedListener listener)**

启动一次直播下载。

- liveUrl [String] 直播url
- listener [OnLoadedListener] 回调函数，加载成功后会返回一个本地代理uri,播放器直接播放该uri即可

**LiveController.unload()**

关闭一次直播下载，与load成对出现。

**VodController.getInstance()**

获取单例的点播下载模块。

- return VodController - 点播下载模块

**VodController.load(String url, OnLoadedListener listener)**

启动一次点播下载。

- url [String] 点播url
- listener [OnLoadedListener] 回调函数，加载成功后会返回一个本地代理uri,播放器直接播放该uri即可

**VodController.unload()**

关闭一次点播下载，与load成对出现。

**VodController.pause()**

暂停P2P模块下载，播放暂停时调用。

**VodController.resume()**

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

```java
// Simple LiveVideoActivity
protected void onCreate(Bundle savedInstanceState) {
    try {
        LiveController.getInstance().load(liveUrl, new OnLoadedListener() {
            @Override
            public void onLoaded(Uri uri) {
                mVideoPath = uri.toString();
                mVideoView.setVideoURI(uri);
                mVideoView.start();
            }

        });
    } catch (Exception e) {
        e.printStackTrace();
    }
}

protected void onStop() {
    mVideoView.stop();
    LiveController.getInstance().unload();
    // do something other...
}

```

---

## Compatibility 兼容性

支持 Android 2.3 及以上版本

---

## See Also 相关资料
 
- [腾讯云X-P2P —— Android SDK开发指南](https://github.com/Vbytes/libp2pimpl-android/blob/master/README.md)

---

## Contributors 贡献者

**Teams** 

![image](https://imgcache.qq.com/open_proj/proj_qcloud_v2/gateway/event/pc/tcc2017/css/img/logo1.png) | 腾讯云
---|---
![image](https://i.h2.pdim.gs/b2a97149ec43dfc95eb177508af29f6c.png) | 熊猫直播

**Individuals**   
-