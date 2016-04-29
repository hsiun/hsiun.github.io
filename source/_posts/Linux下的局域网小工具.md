---
title: Linux下的局域网小工具
date: 2014-10-06 15:36
tags: 计算机网络
---

1、Sniffer工作原理
在本地网卡设置成“混杂”模式时，网卡会接收所有在网络中的所有的帧，这就构成了监听。如果局域网中的某一台主机被设置成了这种监听模式，它就成了一个Sniffer。根据Sniffer的原理，如果一个帧没有被发送到指定的网卡上，那么就无法监听到该帧。所以Sniffer所能监听的信息仅限于同一个物理网络内传输的数据。

2、Sniffit的安装
在Debian可以使用软件源安装。命令如下：
```shell
sudo aptitude install sniffit
```
当然也可以下载源文件进行编译安装。
```shell
sudo tar -zxvf sniffit.version.tar.gz
cd sniffti.version
./configure && make
./sniffit
```
3、TcpDump
安装过程类似于上面安装sniffit的过程。
不添加任何参数直接运行TcpDump，系统将监视网络中所有流过的数据包，这是TcpDump最简单的应用。

tcpdump/libpcap的包过滤规则也支持更多通用分组表达式，在这些表达式中，包中的任意字节范围都可以使用关系或二进制操作符进行检查。对于字节范围表达，你可以使用以下格式：
```
proto [ expr : size ]  
```

“proto”可以是熟知的协议之一（如ip，arp，tcp，udp，icmp，ipv6），“expr”表示与指定的协议头开头相关的字节偏移量。有我们熟知的直接偏移量如tcpflags，也有取值常量如tcp-syn，tcp-ack或者tcp-fin。“size”是可选的，表示从字节偏移量开始检查的字节数量。

只捕获TCP SYN包：
```
# tcpdump -i <interface> "tcp[tcpflags] & (tcp-syn) != 0"  
```




4、EtherApe
安装过程也类似于上面可以采用软件源安装和源码安装两种方式。
EtherApe是一个图形化的网络嗅探器。主要是用在验证主机与主机之间的连接，EtherApe能够图形化的显示网络目前所处的状态，且使用不同颜色的线来表示位于不同主机之间的连接，二通过连线的粗细可以判断主机数据量的大小。


5、netstat
netstat是系统只带的工具，可以用于显示于IP，TCP，UDP和ICMP协议相关的统计数据，一般用于检验本地2计算机各端口的网络连接情况。
