---
title: Docker镜像创建
date: 2016-05-11 08:29:00
tags: 系统运维
---

### 1、创建Docker镜像的一些说明 ###
这里通过创建一个Shadowsocks的docker镜像来说明创建一个docker镜像的过程，Docker镜像创建是Docker一个非常基础重要的功能，这里介绍的这个例子当然也可以通过一个shell脚本来做，通过shell的问题还是那个老生常谈的话题，环境不好控制，遇到不一样的环境可能出错，docker在创建镜像的时候就自行创建了环境，因此避免了这个问题。


### 2、创建Dockerfile，构建shadowsocks环境 ###

Dockerfile文件
```
# Version: 0.0.1

# Pull base image
FROM centos:6

MAINTAINER Gao Xiong "growdane@gmail.com"

# Install openssl-devel
RUN yum -y install openssl-devel

# Install make and another devople tools
RUN yum -y install gcc gcc-c++ automake autoconf libtool make

# Install git 
RUN yum -y install git

# Pull shadowsocks and install
RUN cd /root && git clone https://github.com/madeye/shadowsocks-libev.git
RUN cd /root/shadowsocks-libev && ./configure && make && make install

# Expose ports
EXPOSE 8388

# Define default command
ENTRYPOINT cd /root/shadowsocks-libev && ss-server -p 8388 -k fuckgfw10086
```

### 3、构建镜像 ###
Dockerfile写好了，就可以在宿主的命令行中构建这个镜像了，构建命令如下：
```
# docker build -t growdane/shadowsocks .
# docker run -d -p 8388:8388 growdane/shadowsocks
```
执行完上面的命令就可以直接在需要shadowsock的地方连接他了，密码就是ENTRYPOINT中，-k参数设置的。

### 4、镜像上传 ###
镜像上传有多种办法，第一种是通过docker直接在命令行将生成好的镜像上传到 docker.io这种方法会稍微慢一点，还有种方法是将dockerfile上传到github，然后通过自动构建来在docker.io上生成镜像。两种方法的shell命令如下：
```
# docker login
# docker push growdane/shadowsocks
```

第二种方法
这种方法不好用命令来说明，直接在用文字来说吧，现在github上创建一个新得Repository，然后将Dockerfile上传到github上就可以了。上面的这个Dockerfile我已经上传到https://github.com/hsiun/dockerfile-shadowsocks。

### 5、镜像拉取 ###
现在镜像已经创建成功了，如果需要的话可以通过下面的命令来拉取
```
# docker pull growdane/shadowsocks
```

