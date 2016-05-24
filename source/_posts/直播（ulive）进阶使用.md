---
title:  云直播（ULive）进阶使用
date: 2016-5-24 19:06
tags: 产品说明
---

之前有一篇博客介绍[ulive初次使用](http://zone.gaospot.com/2016/05/09/%E4%BA%91%E7%9B%B4%E6%92%AD%EF%BC%88ULive%EF%BC%89%E5%88%9D%E4%BD%93%E9%AA%8C/)的，文中通过介绍使用ucloud提供的测试推拉流域名来进行直播平台的使用。这篇博客则是完整的介绍从推流域名的创建到播放的完整过程。其中推拉流域名都使用我们自己配置的域名。

### 拉流配置 ###
首先到ULive条目下，点击创建加速按钮，选择直播推流加速。然后可以按照界面中示例的进行配置。
![创建推流](http://7xr7kh.com1.z0.glb.clouddn.com/%E6%8E%A8%E6%B5%81%E9%85%8D%E7%BD%AE.png)
下面对其中各项做个说明：

-  推流域名：指流上传的域名，这个主要是配置在需要上传端的工具中的，如使用sdk推流则在sdk中配置，如使用obs则在obs中配置。
-  接入点：这个可以随便填的。相当于是区别用户流的一个点。
-  播放域名：这两个域名是用来播放直播的，可以都勾选上。rtmp协议主要用在pc web等等环境中，而hls主要用在移动端，如手机web中。

创建加速的申请提交之后大概半个小时到一个小时之间应该就能配好。在配置中时，也会提供一个推流域名的cname记录给您配置下这个cname记录，在配置好以后会提供播放的cname记录。配置好的加速大概是这个样子的。
![直播配置好的样子](http://7xr7kh.com1.z0.glb.clouddn.com/%E7%9B%B4%E6%92%AD%E8%AF%A6%E6%83%85.png)

直播加申请好以后，获得了上面的cname记录之后就可以进行cname的配置。cname在域名提供商哪里进行配置，下面是我的配置示例。
![直播cname配置](http://7xr7kh.com1.z0.glb.clouddn.com/cname%E9%85%8D%E7%BD%AE.png)

进过上面的一系列配置，使用直播过程中在ucloud平台这边的配置也就完成了，下面可以通过配置的推流域名和拉流域名来进行相应的视频上传下播放操作了。


### 视频推流和播放 ###
下面安装推流和播放的顺序来说明在进行相应操作的时候应该使用的工具和具体的场合。
#### 推流 ####
推流主要是移动端，移动端主要是是sdk来自行开发相应的软件实现直播上传的工作。pc端这里只说有一个obs。
pc端：这里主要介绍obs，其他情况不做说明。
Android app：https://docs.ucloud.cn/upd-docs/ulive/ULive_Android_SDK.html
ISO app：https://docs.ucloud.cn/upd-docs/ulive/ULive_IOS_SDK.html

#### 播放 ####
首先如果不愿安装任何软件的话，可以使用web端的rtmp拉流测试工具http://www.cutv.com/demo/live_test.swf。或者安装了vlc的话，可以直接使用vlc来播放。
pc web：https://docs.ucloud.cn/upd-docs/uvideo/web_sdk.html
pc app：任何支持rtmp协议的软件都可以。
iso app：https://docs.ucloud.cn/upd-docs/ulive/ULive_Player_IOS_SDK.html
Android app：https://docs.ucloud.cn/upd-docs/ulive/ULive_Player_Android_SDK.html
iso and Android web：使用html5的video标签来实现，具体参见下面的代码。

```html
<!DOCTYPE HTML>
<html>
<body>

<video src="http://hls.gaospot.com/lol/1/playlist.m3u8" controls="controls" width="100%" height="100%">
your browser does not support the video tag
</video>

</body>
</html>
```

