## H5 HLS开发指南

--------

## 概述

本项目适用于浏览器上的HLS P2P, 支持live和vod,支持以下播放器

  - [Video.js 5][10] 以插件的形式提供
  - 使用原生的 html5 `<video>` 标签

本项目由[hls.js][3]开发而来,如需进一步了解,请[点击这里][3]

## 阅读对象
该文档面向考虑使用vbyte云视频服务进行web开发的开发人员，用户需具备一定的web开发基础和经验。

## 前置条件

### 客户端

使用本项目播放HLS需要浏览器支持以下特性:

  - Media Source Extensions(MSE) [Section `Streaming` in html5test]
  - MSE 支持 video/mp4

如需支持P2P,还需浏览器同时支持:

  - WebSocket [Section `Communication` in html5test]
      - Basic socket comunication
      - ArrayBuffer and Blob support
  - WebRTC 1.0 [Section `Peer To Peer` in html5test]
  - Data channel [Section `Peer To Peer` in html5test]
  
点击[这里][11]或者[html5test.com][5]检测

### 服务端

- 须支持CORS

    一些参考资料
  
    > 同源政策(Same-origin policy): [维基百科][7] 
    
    > 跨域资源共享(CORS): [MDN][6] / [html5rocks][8] / [W3C标准][9]

- 对于.m3u8请求:

    - Content-Type须响应准确的MIME类型(使用videojs要求此条件)

    - 须支持以下HTTP Method

        - GET

- 对于.ts请求:

    - 须响应正确的Content-Length

    - 须支持以下HTTP Method

        - GET
        - HEAD
        - OPTION

    -  对于GET请求须支持以下Header

        - Range
        

## 浏览器兼容性

### 桌面浏览器

|浏览器 | 播放  |
|:--------|:-----|
|chrome | 34+ |
|Firefox  | 42+  |
|IE for windows8.1  | 11+  |
|Edge | all Edge for Windows 10  |
|opera for Desktop  |  |
|Safari for Mac(beta)| 8+|

### 移动端浏览器

|浏览器 | 播放  |
|:--------|:-----|
|chrome for Android  | 34+  |
|Firefox for Android  | 41+  |


## 开发前准备

  - 在使用之前，参考[资源管理][4]在[开发者中心][1]上注册帐号，创建应用.
  - 请在[vbyte开发者中心][1]建立一个测试直播频道，获取频道ID备用(按业务需求可选).

## 集成Video.js

### 1) 获取SDK
在页面中引入资源

```html
<script src="http://stream.ventureinc.net/hls/dist/adapter.js"></script>
<script src="http://stream.ventureinc.net/hls/dist/videojs-hlsp2p.js"></script>
```

### 2) 参数设置

初始化Hls.js

```html
<!doctype html>
<html>
<head>
    <!-- videojs -->
    <link href="http://cdn.bootcss.com/video.js/5.15.1/alt/video-js-cdn.min.css" rel="stylesheet">
    <script src="http://cdn.bootcss.com/video.js/5.15.1/video.min.js"></script>

    <script src="http://stream.ventureinc.net/hls/dist/adapter.js"></script>
    <script src="http://stream.ventureinc.net/hls/dist/videojs-hlsp2p.js"></script>
</head>

<body>
    <video id=example-video width=600 height=300 class="video-js vjs-default-skin" controls>
        <source src="http://split.vbyte.cn/hls/test.m3u8" type="application/x-mpegURL">
    </video>
    <script>
        var options = {
            html5: {
                autoplay: true,
                hlsjsConfig: {
                    package: 'com.zhibocc.web',
                    appId: '566ebf42f530e612681641b6',
                    appKey: 'jKMn17OGwjdeZThM',
                    isLive: true
            }
        };
        var player = videojs('example-video', options);
        player.qualityPickerPlugin(); // 清晰度选择插件
    </script>
</script>
</body>
</html>
```

hlsjsConfig中的参数:

|                    参数   |                     说明   |
|:-------------------------|:-------------------------|
|package                    |包名,请确保包名在我们的白名单中|
|appId                      |应用ID,从devcenter中获取     |
|appKey                     |从deventer中获取            |
|isLive                     |直播为true, 点播为false,请务必填写正确*|
|cid(可选,请参看下方频道源的选择) |                           |
|                           |                                 |

#### 频道源的选择

>  `source` 标签中的type须为 `application/x-mpegURL` 或者 `application/vnd.apple.mpegurl`

  - A. 如果使用url播放

    * 可以设置 `source` 标签的 `src` 属性为要播放的url
  
            <source src="http://your/path/to/demo.m3u8" type="application/x-mpegURL">
    
  - B. 如果不使用url播放并且您事先在开发者中心创建了频道, 那么可以使用频道id播放, 下列方式二选一即可
  
    * a. 可以设置 `source` 标签中的 `cid` 属性
  
            <source cid="xxxxxxx" type="application/x-mpegURL">
    
    * b. 可以在 `hlsjsConfig` 中设置 `cid`
  
            var options = {
                html5: {
                    autoplay: true,
                    hlsjsConfig: {
                        cid: "xxxxxxxxxx"
                    }
               }
            };
            

### 3) 集成Demo

```html
<!doctype html>
<html>
<head>
    <meta charset="utf-8">
    <title>Video.js hls.js p2p Integration</title>

    <!-- video.js -->
    <link href="http://cdn.bootcss.com/video.js/5.15.1/alt/video-js-cdn.min.css" rel="stylesheet">
    <script src="http://cdn.bootcss.com/video.js/5.15.1/video.min.js"></script>

    <script src="http://stream.ventureinc.net/hls/dist/adapter.js"></script>
    <script src="http://stream.ventureinc.net/hls/dist/debug/videojs5-hlsjs-p2p-source-handler.js"></script>

</head>

<body>

    <video id="example-video" width="600" height="300" class="video-js vjs-default-skin" controls>
        <source cid='574fa52fb7eecaae58867492', type="application/x-mpegURL"/>
    </video>

    <script>
        var options = {
            html5: {
                autoplay: true,
                hlsjsConfig: {
                    debug: false,
                    package: 'com.zhibocc.web',
                    appId: '566ebf42f530e612681641b6',
                    appKey: 'jKMn17OGwjdeZThM',
                    isLive: true,
                    //cid: '574fa52fb7eecaae58867492'
                }
            }
        };
        var player = videojs('example-video', options);
      
        player.on(player.tech_, HLS.Events.ERROR, function(evt, data) {
          console.log('some event was triggered!', evt, data);
        });
        
        player.qualityPickerPlugin();
    </script>

</body>

</html>

```

## 集成H5 <video\>标签

如果您没有使用第三方播放器,而是直接操作 `<video>` 标签,则可以使用如下的集成方式


### 1) 获取SDK

在页面中引入资源

    <script src="http://aliyun.vbyte.cn/hls/lasted/hlsjs-p2p-integration.js"></script>
    
### 2) 集成

```javascript
var loader = vbyteLoader(videoId);
var loadeResult = loader(url|cid, options, errorCallbacks);
```

#### 参数详解

- vbyteLoader: 全局变量,用来绑定 `<video>` 标签, 并返回loader,一般调用一次即可
    - _videoId_ : `<video>` 标签的 `id`
- loader: 用来播放url或cid, 可以调用多次
    - _url|cid_ : __String__ url是m3u8的链接. 如果您在我们的devcenter添加了频道,则可以填写频道id
    - _options_ : __Object__ 用来初始化sdk,详细解释请参看下方
    - _errorCallbacks_ : __Object__ 当sdk出错的时候您需要进行的处理,如不需要可省略, 详细解释请参看下方
    - _loaderResult_ : __Object__ 播放视频url后返回的结果,详细解释请参看下方

#### options

参数 | 说明
-----|-----
package                    |包名,请确保包名在我们的白名单中, 请向我们的客服咨询如何填写
appId                      |应用ID,从devcenter中获取, 请向我们的客服咨询如何填写
appKey                     |从deventer中获取, 请向我们的客服咨询如何填写
isLive                     |表示当前播放的是直播还是点播. 如省略此项则默认为 `true` 表示直播. __如果播放的是点播,请务必填写 `false`__

#### errorCallbacks

```javascript
  // 错误处理回调函数, 如不需要可省略, 也可按需实现
  var errorCallbacks = {};
  errorCallbacks[Hls.ErrorTypes.NETWORK_ERROR] = function () {
    // 网络相关错误导致播放停止的时候可在此进行处理
    console.log('network error handler');
  };
  errorCallbacks[Hls.ErrorTypes.OTHER_ERROR] = function () {
    // 除网络相关错误和视频编解码错误外, 其他原因导致的播放停止的时候可在此进行处理
    console.log('other error handler');
  };
```

#### loaderResult

```
loaderResult = {
  hls: sdk的实例引用, type: Object
  canPlay: 是否使用sdk播放视频, type: boolean
}
```

### 3) 集成Demo

```html
<!doctype html>
<html>
<head>

  <style>
    .videoCentered {
      width: 720px;
      margin-left: auto;
      margin-right: auto;
      display: block
    }

    .innerControls {
      display: block;
      float: left;
      width: 99%;
      margin: 3px;
      padding-left: 3px;
      font-size: 8pt
    }
  </style>

  <script src="http://apps.bdimg.com/libs/jquery/2.1.3/jquery.min.js"></script>

  <script src="http://stream.ventureinc.net/hls/dist/hlsjs-p2p-integration.js"></script>

</head>

<body>
<p> 输入频道id或者url </p>
<input id="streamURL" class="innerControls" type=text value=""/>
<video id="video" controls autoplay class="videoCentered"></video>
<ul>
  <li>
    <b>累积p2p比例(每分钟更新)</b>: <b id="rate"></b>
  </li>
  <li>
    <b>peer数</b>: <b id="peers"></b>
  </li>
</ul>


<script>

  var url = 'http://split.vbyte.cn/hls/test.m3u8';
  $('#streamURL').val(url);

  var
    loaderResult,                 // sdk加载url的结果, 可判断是否能播放
    hls,                          // sdk的实例引用
    canPlay,                      // canPlay===true表示使用sdk播放hls
    loader = vbyteLoader('video');// 参数是video标签的id, 返回的loader用于播放url和注册错误回调

  // 错误处理回调函数, 如不需要可忽略, 也可按需实现
  var errorCallbacks = {};
  errorCallbacks[Hls.ErrorTypes.NETWORK_ERROR] = function () {
    // 网络相关错误导致播放停止的时候可在此进行处理
    console.log('network error handler');
  };
  errorCallbacks[Hls.ErrorTypes.OTHER_ERROR] = function () {
    // 除网络相关错误和视频编解码错误外, 其他原因导致的播放停止的时候可在此进行处理
    console.log('other error handler');
  };

  var options = {
    appId: '566ebf42f530e612681641b6',
    appKey: 'jKMn17OGwjdeZThM',
    package: 'com.zhibocc.web',

    isLive: true
  };

  loaderResult = loader($('#streamURL').val(), options, errorCallbacks);
  hls = loaderResult.hls;
  canPlay = loaderResult.canPlay;


  $('#streamURL').change(function () {
    var options = {
      appId: '566ebf42f530e612681641b6',
      appKey: 'jKMn17OGwjdeZThM',
      package: 'com.zhibocc.web',
      confId: '574fa52fb7eecaae58867492', 

      isLive: true
  };
    loaderResult = loader($('#streamURL').val(), options, errorCallbacks);
    hls = loaderResult.hls;
    canPlay = loaderResult.canPlay;
  });

  // 以下为demo页面统计参考
  // start
  var rate = document.getElementById('rate');
  var peersNum = document.getElementById('peers');

  setInterval(function () {
    if (hls && hls.totalBytes) {
      var
        p2p = hls.totalBytes.p2p,
        cdn = hls.totalBytes.cdn + 0.0001;
      rate.innerText = (p2p * 100 / (p2p + cdn)).toFixed(2) + '%';
      peersNum.innerText = hls.peersNum;
    }
  }, 1000);

  // end

</script>
</body>

</html>
```


## 技术支持及BUG反馈

对SDK集成及上线使用中如有任何问题或意见建议，您可通过联系客服的方式进行反馈，我们的工程师会同您联系。

对SDK有新的定制化需求，请与客服进行沟通。

  [1]: http://devcenter.vbyte.cn
  [2]: http://www.vbyte.cn
  [3]: https://github.com/dailymotion/hls.js
  [4]: /manage/base/
  [5]: https://html5test.com
  [6]: https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Access_control_CORS
  [7]: https://en.wikipedia.org/wiki/Same-origin_policy
  [8]: https://www.html5rocks.com/en/tutorials/cors/
  [9]: https://www.w3.org/TR/cors/
  [10]: http://videojs.com/
  [11]: http://stream.ventureinc.net/hls/dist/test.html
