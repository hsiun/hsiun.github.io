---
title: Docker安装
date: 2016-04-24 17:11:00
tags: 系统运维
---

记录在我的云主机上安装Docker的过程，安装过程是通过软件包管理器来安装的，所有安装过程比较简单，过程参考了Docker官方文档和其他一些内容。

### 前提条件 ###
由于Docker对操作系统的一些底层功能有依赖，所有并不是所有的Linux发行版都可以用来安装Docker的，我这里选择的是Ubuntu14.04 64bit，因为Ubuntu本身使用用户量比较大，而且也是我自己常用的系统。具体要求在列表中给出：

 1. 必须是64位系统，不论是AMD还是X86_64，总之一定要是64位的；
 2. 内核版本最起码是3.10，小版本最低的3.10都行；


### 更新APT源 ###
为了安装最新的docker版本，这里并没有使用ubuntu官方的源来安装docker，而是使用了其他的源，添加过程如下所示：

```shell
$ sudo apt-get update
$ sudo apt-get install apt-transport-https ca-certificates
$ sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
$ vim /etc/apt/sources.list.d/docker.list #添加下面的内容，因为我是14.04的

deb https://apt.dockerproject.org/repo ubuntu-trusty main
```
其他版本的添加的源如下：
在Ubuntu Precise 12.04 (LTS)中，添加下面的源

deb https://apt.dockerproject.org/repo ubuntu-precise main
在Ubuntu Trusty 14.04 (LTS)中，添加下面的源

deb https://apt.dockerproject.org/repo ubuntu-trusty main
Ubuntu Wily 15.10

deb https://apt.dockerproject.org/repo ubuntu-wily main
Ubuntu Xenial 16.04 (LTS)

deb https://apt.dockerproject.org/repo ubuntu-xenial main

保存并退出之后更新本地源缓存，我记得apt好像是有缓存：）。

```shell
$ sudo apt-get update
```
### 安装 ###

在进行完上面的准备工作之后就可安装了，安装过程非常简单就是一条apt命令，安装之后对docker做一个简单检查。还有ufw的问题，ufw是Ubuntu下的防火墙，类似Centos下iptables，在我的主机上，这个防火墙是关闭的，所有可以不用担心它。

```shell
$ sudo apt-get install docker-engine
```

安装之后启动服务

```
$ sudo service docker start
```

通过运行一个Docker容器，检查Docker是否正常安装

```
$ sudo docker run hello-world
```

查看Docker信息

```
$ sudo docker info
```

上面就是我安装过程的一个记录，安装好Docker之后可以尽情的折腾了，遇到问题解决不了的话，直接重装系统吧：）。然后又可以愉快的玩耍了。有问题可以参考官方文档，链接在下面，官方文档说的特别详细，考虑了很多方面，我这里针对我本地需要和本地环境做了一些简化。哈哈，小偷了一下懒。不过也没什么影响，总的来说Docker安装过程还是非常简单的。

参考：[Installation on Ubuntu](https://docs.docker.com/engine/installation/linux/ubuntulinux/)
