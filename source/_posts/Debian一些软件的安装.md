---
title: Debian一些软件的安装
date: 2014-04-14 16:42
tags: 个人总结
---

以下是自己的Debian系统在使用时安装一些软件和进行一些配置的过程，以此做个记录，以备将来查用。
系统信息是：Debian 7.2

1、sudo的配置，Debian默认sudo是不能使用的，但是已经习惯了使用sudo怎么办呢，可以通过相关的配置文件来启用这个命令。如下所示：
编辑/etc/sudoers文件，将username    ALL=(ALL) ALL加入其中，其中username时要使用sudo命令的用户的用户名。

2、配置Debian的软件源，以获取更新。
通过下面的shell命令，备份原来的软件源，并打开配置文件。
```
sudo cp /etc/apt/sources.list /etc/apt/sources.list_bak #备份一下软件源  
sudo vi /etc/apt/sources.list  
```

将下面的内容复制到sources.list文件中
```
# 网易163更新服务器：  
deb http://mirrors.163.com/debian/ squeeze main non-free contrib  
deb http://mirrors.163.com/debian/ squeeze-proposed-updates main non-free contrib  
deb-src http://mirrors.163.com/debian/ squeeze main non-free contrib  
deb-src http://mirrors.163.com/debian/ squeeze-proposed-updates main non-free contrib  
```

3、安装软件开发工具包，里面包含了常用的用于软件开发的工具，如gcc，make等。
```
sudo apt-get install build-essential   
sudo apt-get install gdb   
sudo apt-get install g++  
```

4、安装一个截屏软件scrot,使用scrot -s命令可以截取一个区域的图片。
```
sudo apt-get install scrot  
```


5、在使用Debian的同时还装了一个Windwos是很正常的事情，如果遇到了问题要重装Debian到没什么问题，但是如果是重装Windows问题就出来了，一般重装后，启动是有问题的，具体说就是启动不了。这是可以使用下面的方法。
使用一张支持Ext4文件系统的Ubuntu live CD(9.04+)，从光盘启动进入体验模式。
```
sudo apt-get install grub  
```

假设Ubuntu在(hd0,2)，其实输入root之后，可以Tab出详细信息。
```
root (hd0,2)  
setup (hd0)  
quit  
```

重启机器
