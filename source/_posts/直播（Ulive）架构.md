---
title:  直播（ULive）架构
date: 2016-5-25 21:29
tags: 产品说明
---

前面有两篇博客介绍了云直播的使用，这篇博客介绍ulive的架构是如何做的，这里主要是通过整理公开资料写的这篇博客。主要内容都引用自UCloud流媒体研发总监曾凯源分享内容。这篇文章在参考中给出了。

文章首先介绍的是直播的应用场景，根据不同的场景来提出不同的需求，再跟进需求来开发产品我觉得这是一个很重要的思想。直播的架构经历了多次的演变，从简单到复杂，从不可靠到可靠的一个变化过程。这里主要就介绍下最后的架构。最后的架构由oc，区域三通机房和合作cdn共同解决。他们的引入无法为了解决两个问题，降低主播推流的延时和用户播放的延时。同时oc的引入降低了BGP机房的压力。同时区域三通点的引入，为BGP机房做容灾，对于同一区域如都在华东的推流和分发，直接走区域三通机房，BGP机房和三通机房部署多个，故障是只要调整路由即可。下面给出一张图做为说明：
![直播架构](http://7xr7kh.com1.z0.glb.clouddn.com/%E7%9B%B4%E6%92%AD%E6%9E%B6%E6%9E%84)
参考：
[直播云平台架构如何构建？【 有料】](https://mp.weixin.qq.com/s?__biz=MzAxOTAzMDEwMA==&mid=2652499688&idx=1&sn=d8ce74eed7ca7e2e9de0e66a6aef0494&scene=2&srcid=0505OgZbZXhyuaRxdGSNJVon&from=timeline&isappinstalled=0&pass_ticket=YoVPy4HOG/PZEe5kIFWtZK10BedpyT9k7etx3R03J7s=#rd)
[关于直播，所有的技术细节都在这里了（一）](http://mp.weixin.qq.com/s?__biz=MjM5NDE0MjI4MA==&mid=2656298621&idx=1&sn=5088b182777331106786b55d7f3f657d&scene=21#wechat_redirect)
[关于直播，所有的技术细节都在这里了（二）](http://mp.weixin.qq.com/s?__biz=MjM5NDE0MjI4MA==&mid=2656298657&idx=1&sn=d3e649f5809421b09a97b863809825a1&scene=21#wechat_redirect)
[关于直播，所有的技术细节都在这里了（三）](https://mp.weixin.qq.com/s?__biz=MjM5NDE0MjI4MA==&mid=2656298664&idx=1&sn=e5844d067c1a04673bcdeb007ad6b05c&scene=1&srcid=0516uvDRnn3CUBHxCGjPPsOK&pass_ticket=YoVPy4HOG/PZEe5kIFWtZK10BedpyT9k7etx3R03J7s=#rd)