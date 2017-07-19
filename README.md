## P2P标准

maintainer：腾讯云

members：腾讯云，网宿，金山云



### 1.Introduction

腾讯云P2P方案支持全平台、多场景，具有良好开放性和兼容性，旨在帮助客户节约大量带宽成本。应用场景为：https://github.com/CDN-Union/H265/raw/master/Document/images/scenarios.png （类似图片）

此方案由腾讯云专业P2P团队研发，具备自主知识产权，在延迟、分享率、卡播率、系统资源占用率等多个参数进行持续优化，打造世界一流的P2P服务体系。方案遵循良好的软件工程原则，为融合P2P架构持续发展打下良好基础：

1. 系统兼容性：与现有CDN兼容且解耦和，支持FLASH、HTML5、IOS、ANDROID主流平台与系统；

2. 系统开放性：支持源站在第三方，后端也可对接多家第三方CDN；

3. 系统扩展性：兼容当前主流RTMP、UDP、HTTP-FLV直播协议，向后兼容MPEG-DASH直播协议，支持客户平滑迁移HTML5方案，未来有支持H265和微信小程序的计划；

   ​

### 2.模块标准

P2P方案流程要比CDN流程复杂很多，整体架构如下所示：

https://p.vbyte.cn/file/data/zwbaaphnf5tfyf3ea522/PHID-FILE-ovj4svhlg4gwpe46csdm/%E6%9E%B6%E6%9E%84%E5%9B%BE.png （需要显示出来）

P2P服务中链条分为切片源站服务，P2P服务集群（conf配置服务，tracker服务，穿透服务，上报统计服务），客户端SDK。

##### 2.1 切片源站服务

源端接入是整个服务的基础，接入支持rtmp/HTTP-FLV/UDP协议的标准推流或者拉流切片，拉流切片由客户端SDK触发，源为负载均衡集群，异地多备份。切片服务同样为负载均衡异地多备份服务集群，支持回源访问和存储（方便时移会看等服务），回源为标准HTTP服务，支持Range请求，支持对接多家CDN；

##### 2.2 P2P服务集群

P2P服务集群为负载均衡异地多备份服务器集群，包含HTTP服务器和UDP服务器，主要用于配合客户端SDK实现P2P逻辑和P2P调度功能。

​	2.2.1 conf配置服务：弹性配置的p2p参数服务，用于配置客户的是否开启p2p功能，可以操作到直播房间/点播资产粒度的配置，具备防盗链验证功能，标准HTTP服务，需支持HTML5跨域访问；（HTTP 1.1？）

​	2.2.2 tracker服务：负载均衡HTTP服务，用来获取线上p2p peer，SDK与tracker服务连接时间间隔为16s；

​	2.2.3 穿透服务：负载均衡UDP服务，用于p2p peer间直连，支持peer NAT类型获取。

 	2.2.4 上报统计服务：上报统计服务用于CDN数据校对和主动式分析，为标准HTTP标准服务。

### 3.SDK接口标准

方案针对FLASH、HTML5、IOS、ANDROID分别提供actionscript3、js、object-c和java接口标准。

##### 	3.1 FLASH平台

​	FLASH的SDK为swf，该swf可在网络上自由获取。SDK扩展自Adobe actionscript3的Netstream，支持符合netsream全部原生方法。

​	启动时，加载直播SDK（livesdk）或者点播SDK（vodsdk）。

​	var loader:Loader = new Loader();

​	loader.contentLoaderInfo.addEventListener(Event.COMPLETE, loadSuccess);

​	loader.load(new URLRequest("http://split.vbyte.cn/sdk/livesdk.swf")); (或者 vodsdk.swf)

​	var p2pNetStream:Netream = liveP2PIntance.stream;

​	p2pNetStream.addEventListener(NetStatusEvent.NET_STATUS, onPlayStatus);

​	video.attachNetStream(p2pNetStream);

​	p2pNetStream.play(channelID);(或者URL)

##### 	3.2 ANDROID和IOS平台

​	调起接口为Load（），关闭接口为Unload（）；

##### 	3.3 HTML5平台

​	调起接口为，关闭接口为（）；









### 