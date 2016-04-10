---
title: Win2008搭建Web和FTP服务
date: 2016-04-11 02:32:55
tags: 系统运维
---

### 介绍
本篇博客介绍的是在UCloud的云主机上通过IIS搭建Web和FTP服务器的过程，这里的Web服务器只支持最简单的html页面，如果需要其他支持，请自行安装所需软件。

###创建和连接主机
**1、创建win2008的主机**
在UCloud平台上创建win2008主机的过程和创建其他系统主机过程一样，但是要注意的是，默认的镜像不是Windows系统的，所以在安装过程中需要注意选择下Windows系统的镜像。这里使用的是Win2008 R2 64bit的一个镜像系统。
![Win2008搭建Web和FTP服务](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A1.png)

**2、使用mstsc连接管理主机**
UCloud平台提供了Web连接主机的方式，这种连接方式并不推荐用来管理主机，而比较适合在应急的情况下登录，检查主机状况，由于控制登录与主机网络状态是无关的，这种登录方式在主机网络异常的情况下，可以非常方便的通过其登录主机检查主机的网络设置。在正常情况下我们还是使用mstsc（远程桌面连接）来管理主机。首先打开远程桌面软件，通过按下win key + r，然后输入mstsc就可以了。
![Win2008搭建Web和FTP服务](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A11.png)

打开远程连接界面后，下面有个小三角形，点击打开更大选项，输入相关参数。输入示例如下：
![Win2008搭建Web和FTP服务](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A12.png)

输入都正确以后，会弹出一个关于正式的对话框，点是忽略这个证书问题就可以了，然后就能顺利的连接上主机。

###创建Web和ftp服务器
1、服务器登录上去之后会自动打开服务器管理界面。第一件事先添加一个角色。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A13.png)

2、点击下一步，勾选Web服务器（IIS）这一项，然后点击下一步。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A14.png)

3、由于这里还要安装ftp服务，把ftp相关的勾选上。在点击下一步就会开始安装了，安装过程很快，大概一两分钟的样子。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A15.png)

4、添加完角色之后就可以添加站点了，右键点击箭头标出的地方，然后选择新建站点就可以了。其实这个时候就可以直接访问了，可以访问到默认站点。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A16.png)

5、站点的设置。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A17.png)

6、本地访问测试默认站点。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A18.png)

7、下面的步骤介绍的是建立ftp服务器的过程，首先新建一个ftp站点。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A19.png)

8、ftp站点的基本设置，站点名和站点（物理）路径。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A110.png)

9、ssl设置，由于我没有证书，没办法使用https模式。所以ssl这个选无。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A111.png)

10、访问和授权设置。这里不限制用户访问，给所有访问这个ftp站点的用户读取站点文件的权限。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A112.png)

11、在外网测试ftp服务器，这里假设防火墙端口已经打开。在UCloud平台上，Windows内部的防火墙默认是关闭了的，但是平台提供的防火墙是开着的，而且默认防火墙21端口是没有打开的。如果不清楚防火墙打开过程可以参考下面的说明。
![创建Web和ftp服务器](http://7xr7kh.com1.z0.glb.clouddn.com/Win2008%E6%90%AD%E5%BB%BAWeb%E5%92%8CFtp%E6%9C%8D%E5%8A%A113.png)


###打开外网防火墙
打开外网防火墙的说明在之前博客中有介绍，这里就不在赘述，过程都是一样的，具体参考前面的博客[UCloud上LAMP小型网站搭建与测试](http://zone.gaospot.com/2016/03/30/UCloud%E4%B8%8ALAMP%E5%B0%8F%E5%9E%8B%E7%BD%91%E7%AB%99%E6%90%AD%E5%BB%BA%E4%B8%8E%E6%B5%8B%E8%AF%95/)。



*参考：[Windows server 2008 r2搭建FTP服务器](http://ce.sysu.edu.cn/hope/Item/91192.aspx)*




