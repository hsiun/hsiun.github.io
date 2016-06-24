---
title:  阿里云CDN架构
date: 2016-6-13 15:16
tags: 产品说明
---

最近在学习阿里云的文档，看到阿里的CDN架构图，觉得不错，留着看看。

LVS做四层均衡负载
DR模式
双LVS做Active-Active互备
负载均衡算法采用wrr
Tengine做七层负载均衡：
阿里基于Nginx开发的高性能HTTP服务器，已经开源，详细请了解：http://tengine.taobao.org
主动健康检查
SPDY v3支持
Swift做HTTP缓存
高性能Cache
磁盘（SSD/SATA）

![基础架构图](http://7xr7kh.com1.z0.glb.clouddn.com/aliyun-architecture-single.png)

![部署架构图](http://7xr7kh.com1.z0.glb.clouddn.com/aliyun-architecture-001.png)