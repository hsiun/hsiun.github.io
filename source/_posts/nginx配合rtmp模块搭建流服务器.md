---
title: nginx配合rtmp模块搭建流服务器
date: 2016-6-3 11:35
tags: 运维相关
---

本篇博客通过介绍使用nginx的rtmp模块来使nginx服务支持rtmp协议，可以完成推流和播放的功能。博客的内容完全出自参考1，参考2比较完整还介绍了除了介绍rtmp还介绍了hls。这篇博客主要介绍rtmp相关的内容。

### 准备 ###
首先你需要一台安装了linux系统的主机，无论是云主机啊什么的都可以，甚至是树莓派也没问题。window应该也是可以的，但不是这里使用的环境，如果是想在window下测试，可以再Google下看看。另外这里使用的linux发行版是ubuntu，实际使用的是ubuntu14.04。

### 安装nginx和rtmp模块 ###
安装必要的库和工具。

```
$ sudo apt-get install build-essential libpcre3 libpcre3-dev libssl-dev
```

由于要安装rtmp模块，这里nginx的安装使用源码安装的方式。下面先下载nginx和rtmp模块源码。

```
$ wget http://nginx.org/download/nginx-1.9.15.tar.gz
$ wget https://github.com/arut/nginx-rtmp-module/archive/master.zip
```

解压nginx源码和rtmp模块源码，准备编译安装nginx和rtmp模块。

```
$ tar -zxvf nginx-1.9.15.tar.gz
$ unzip master.zip
```

进入nginx解压后的目录，指定rtmp模块的路径。编译安装nginx。

```
$ cd nginx-1.9.15
$ ./configure --with-http_ssl_module --add-module=../nginx-rtmp-module-master
$ make
$ sudo make install
```

现在nginx已经编译安装好了，启动nginx。

```
$ sudo /usr/local/nginx/sbin/nginx
```
访问下nginx服务器看看，将浏览器地址指向http://< your server ip >/这时候应该可以nginx的欢迎页面。

#### 配置nginx使用rtmp模块 ###
使用编辑器打开/usr/local/nginx/conf/nginx.conf文件并在最后加上，下面配置内容。

```
rtmp {
        server {
                listen 1935;
                chunk_size 4096;

                application live {
                        live on;
                        record off;
                }
        }
}
```
简单说明下这里的意思，这里就是配置了一个rtmp服务器，监听1935端口。接入点的名字叫做live。如果需要使用其他平台来进行拉流加速（播放加速，可以理解为给直播播放过程加个cdn，起到提高并发，加快速度的目的）的时候这个可能用的到。rtmp模块的完整配置说明参考：[这里](https://github.com/arut/nginx-rtmp-module/wiki/Directives)。

最后重启下nginx，让nginx加载新的配置文件。

```
$ sudo /usr/local/nginx/sbin/nginx -s stop
$ sudo /usr/local/nginx/sbin/nginx
```

最后的最后，测试。测试使用的软件还是obs。obs的使用可以参考前面的博客。大概的配置内容如下。

Streaming Service: Custom
Server: rtmp://< your server ip >/live
Play Path/Stream Key: test

这里的流id，test其实是可以随便填的。配置到这里我们自己的rtmp服务器就搭建完成了，这时候，如果需要的话，可以在配置一个拉流加速，配置的过程可以参考前面的博客，之前有一篇介绍推流加速的配置的，过程是类似的。

参考：
[
How to set up your own private RTMP server using nginx](https://obsproject.com/forum/resources/how-to-set-up-your-own-private-rtmp-server-using-nginx.50/)
[nginx搭建支持http和rtmp协议的流媒体服务器之一 ](http://blog.chinaunix.net/uid-26000296-id-4335063.html)