# P2P标准接口文档-Flash平台

## Concepts and usage 定义和用途

本文档定义了Flash平台上P2P相关的接口标准，以及典型应用流程。

## Defination 定义

P2PNetStream: 基于Flash标准NetStream派生出的P2P NetStream.

## Interfaces 接口列表

#### Constructor 构造方法
Flash SDK采取远程加载的方式调用，加载成功后即可获取P2PNetStream
```actionscript
    var loader:Loader = new Loader();
    loader.contentLoaderInfo.addEventListener(Event.COMPLETE, function(e:Event):void {
        var P2PNetStream:NetStream = e.currentTarget.content.stream;
    });
    loader.load(new URLRequest(sdkUrl);
```
- sdkUrl [String] - SDK远程加载url

---


#### Methods 方法
P2PNetStream继承自flash.net.NetStream，支持NetStream的所有原生方法。

**NetStream.play(videoUrl, config)**

播放函数，调用后及可启动下载及播放
- videoUrl [String] - 直播、点播url，支持rtmp/flv/mp4/HLS;
- config [Object] - 配置信息，用以配置SDK参数。预留使用，不传则使用默认值。

**其他方法**

其他方法参见[flash.net.NetStream说明文档](http://help.adobe.com/zh_CN/FlashPlatform/reference/actionscript/3/flash/net/NetStream.html)

---

#### Events 事件
| 事件 | 事件描述 | 建议处理措施 |
| :------------ | :------------ | :------------ |
| **NetStream.Play.Failed** | P2P播放失败 | 回退至CDN播放 |

---

## Example 用例

```
    public class LiveDemo extends Sprite {
        private var _P2PNetStream:NetStream = null;

        public function LiveDemo() {
            private var _video:Video = new Video(640, 480); 
            addChild(_video); 
            stage.scaleMode = StageScaleMode.NO_SCALE;
            var loadSuccess:Function = function(e:Event):void {
                _P2PNetStream = e.currentTarget.contentsdk.stream;
                _P2PNetStream.addEventListener(NetStatusEvent.NET_STATUS, onPlayStatus);
                _P2PNetStream.play("http://live.qcloud.com/test.flv?sign=f767172ee97231f8bd1d2fbf8c38bbdc&t=59196bda");
                _video.attachNetStream(_P2PNetStream);
            }
            var loader:Loader = new Loader();
            loader.contentLoaderInfo.addEventListener(Event.COMPLETE, loadSuccess); 
            loader.load(new URLRequest("http://img.qcloud.com/open/qcloud/video/sdk/flash/superp2p.swf"));
        }

        private function onPlayStatus(e:NetStatusEvent):void { 
            if(e.info.code == "NetStream.Play.Failed") {
                // p2p播放失败，请在此进行播放回退等处理
            }
        }
    }
```
---

## Compatibility 兼容性
支持 flash player 10.1 及以上版本

---
## See Also 相关资料
- [flash.net.NetStream](http://help.adobe.com/zh_CN/FlashPlatform/reference/actionscript/3/flash/net/NetStream.html)  
- [腾讯云p2p直播服务 —— Web SDK开发指南](https://github.com/Vbytes/flash-demo/blob/master/README.md)
---

## Contributors 贡献者

**Teams** 

![image](https://i.h2.pdim.gs/b2a97149ec43dfc95eb177508af29f6c.png) | 熊猫直播
---|---
![image](https://imgcache.qq.com/open_proj/proj_qcloud_v2/gateway/event/pc/tcc2017/css/img/logo1.png) | 腾讯云


**Individuals**   
-