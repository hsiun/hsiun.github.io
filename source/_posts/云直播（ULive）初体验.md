---
title:  云直播（ULive）初体验
date: 2016-5-9 17:45
tags: 产品说明
---

这篇博客旨在通过一个简单是使用例子来初步说明ULive这个产品的使用方法，产品文档的说明主要是针对产品的，而这篇博客则主要是从用户业务的角度来做说明。

### 概述 ###
这里通过obs（一个开源的直播软件）来录视频（产生直播流），当然也可以通过sdk来开发推流客户端。直播流推流到UCloud提供专门用于测试的推流地址，播放是通过支持rtmp协议串流播放的vlc（开源播放器）来做的。有其他更多需求比如说弹幕啊什么的其实是通过在播放客户端中做的，这个可以自行实现。下面通过图示来说明这个过程（图略丑，见谅：）
![推流和拉流过程说明](http://7xr7kh.com1.z0.glb.clouddn.com/%E6%A6%82%E8%BF%B0.png)

### 准备和前提条件 ###
推流和播放软件：
obs和vlc

用于测试的推流和播放地址：
推流地址：rtmp://publish3.cdn.ucloud.com.cn/ucloud/流id
rtmp播放地址：rtmp://vlive3.rtmp.cdn.ucloud.com.cn/ucloud/流id
hls播放地址：http://vlive3.hls.cdn.ucloud.com.cn/ucloud/流id/playlist.m3u8

其中，流id是您可以任意指定的（仅限于英文字母、数字、下划线），比如aaa、xyz等。因为测试地址是公共的，为了不与其他客户的测试冲突，建议您使用带有自定义前缀（如公司英文名）的流id。

### 视频录制和推流 ###
这里使用的是obs来录制视频并推流，obs的具体使用不做太多的说明，只说明直播服务器相关地方的说明。首先是点设定，弹出设定对话框，然后选择广播设定，设定FMS URL，和播放路径这两项。FMS URL填的测试推流地址：rtmp://publish3.cdn.ucloud.com.cn/ucloud/，播放路径就是Ulive所说的流id，我这里写的是gaospot。具体说明见下面的文档。
![视频录制和推流](http://7xr7kh.com1.z0.glb.clouddn.com/%E6%8E%A8%E6%B5%81.png)

### 播放和拉流 ###
这里使用vlc来播放我们的直播，媒体-》打开网络串流。就到如下所示的对话框，这个和上面推流地址是相关的，播放地址就是推流地址加流id，我这里具体来说就是rtmp://vlive3.rtmp.cdn.ucloud.com.cn/ucloud/gaospot
![播放和拉流](http://7xr7kh.com1.z0.glb.clouddn.com/%E6%8B%89%E6%B5%81.png)

了解了上面的姿势之后在看官方文档（https://docs.ucloud.cn/upd-docs/ulive/index.html）的说明就能完全掌握这个产品的使用了。