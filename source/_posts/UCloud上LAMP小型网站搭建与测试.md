---
title: UCloud上LAMP小型网站搭建与测试
date: 2016-03-30 17:57:23
tags: 系统运维
---

### 目录 ###
 1. 介绍
 2. LAMP环境搭建
 3. 打开UCloud防火墙
 4. WordPress安装
 5. 应用测试
 
### 介绍 ###
本篇博客旨在通过介绍搭建一个WordPress博客的过程介绍在UCloud的云主机（UHOST）上搭建单机Web服务的过程。WordPress作为一个著名的CMS系统，有着广泛的应用。其作为博客也是非常常见的用法。所以这里使用WordPress作为示例软件来说明。在UHost上安装LAMP环境和在其他的linux下安装过程类似，但是要**注意UCloud平台提供的防火墙，记得开放相应端口**。



### LAMP环境搭建 ###

在UCloud上安装Linux主机，这里安装的是Centos6.5 x64系统。

 1. 创建主机，根据您需要选择相应的机房，在相应的机房下创建主机。一开始只是测试用的话，可以选择按需付费，在决定使用后在改成月付或者年付，主机和网络的配置选默认配置。如下图所示：
 
 ![这里写图片描述](http://7xr7kh.com1.z0.glb.clouddn.com/UCloud%E4%B8%8ALAMP%E5%B0%8F%E5%9E%8B%E7%BD%91%E7%AB%99%E6%90%AD%E5%BB%BA%E4%B8%8E%E6%B5%8B%E8%AF%951.png)
 
 
 2. 是xshell登录管理云主机，xshell是一个ssh远程管理工具，针对个人用户是免费的。ssh工具还有其他的如，putty和secureCRT等，大家可以根据自己的需求和爱好选用。使用xshell的话，首先创建一个会话过程如下所示，在后面进行连接是，会提示保存证书，然后输入linux系统的账号和密码就可以连接到主机，然后通过ssh来管理主机了。
 ![这里写图片描述](http://7xr7kh.com1.z0.glb.clouddn.com/UCloud%E4%B8%8ALAMP%E5%B0%8F%E5%9E%8B%E7%BD%91%E7%AB%99%E6%90%AD%E5%BB%BA%E4%B8%8E%E6%B5%8B%E8%AF%952.png)
 
 
 3. 下面介绍安装AMP的过程，安装方法是通过在shell中执行下面的命令实现的。
 
安装AMP
``` shell
# ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# yum install -y nginx mysql-server php php-gd php-xml php-mysql php-bcmath php-mbstring php-mcrypt
```
 修改PHP的时区
``` shell
# sed -i 's#;date.timezone =#date.timezone = Asia/Shanghai#' /etc/php.ini
```
配置Apache

``` shell
# rm -rf /etc/httpd/conf.d/README
# rm -rf /etc/httpd/conf.d/welcome.conf
# vim /etc/httpd/conf.d/vhost.conf
添加如下内容：
<VirtualHost *:80>
ServerName www.domain.com
DocumentRoot /var/www/html/
    ErrorLog /var/log/httpd/error.log
    CustomLog /var/log/httpd/access.log common
</VirtualHost>
```
启动Apache

``` shell
# service httpd restart
# chkconfig httpd on
# echo “<?php phpinfo();” > /var/www/html/phpinfo.php
```
打开浏览器，输入下面URL(记得解析DNS，或者修改hosts文件)
http://www.domain.com/phpinfo.php

启动MySQL
MySQL默认有生成几个配置文件，可以根据自己的硬件情况把文件复制到/etc/my.cnf

``` shell
# ls /usr/share/mysql/*.cnf
# service mysqld start
# chkconfig mysqld on
```

### 打开UCloud防火墙 ###
UCloud平台提供了一个防火墙，在控制台打开，初次使用的很容易忽略这个问题，导致业务无法再外网访问。基本的方法是创建一个新的防火墙，然后绑定到主机上。防火墙字网络UNET下，找到外网防火墙。点创建防火墙按钮，创建一个新的防火墙。
![这里写图片描述](http://7xr7kh.com1.z0.glb.clouddn.com/UCloud%E4%B8%8ALAMP%E5%B0%8F%E5%9E%8B%E7%BD%91%E7%AB%99%E6%90%AD%E5%BB%BA%E4%B8%8E%E6%B5%8B%E8%AF%953.png)

创建防火墙规则
![这里写图片描述](http://7xr7kh.com1.z0.glb.clouddn.com/UCloud%E4%B8%8ALAMP%E5%B0%8F%E5%9E%8B%E7%BD%91%E7%AB%99%E6%90%AD%E5%BB%BA%E4%B8%8E%E6%B5%8B%E8%AF%954.png)

在主机页面将新建的防火墙绑定到主机。
![这里写图片描述](http://7xr7kh.com1.z0.glb.clouddn.com/UCloud%E4%B8%8ALAMP%E5%B0%8F%E5%9E%8B%E7%BD%91%E7%AB%99%E6%90%AD%E5%BB%BA%E4%B8%8E%E6%B5%8B%E8%AF%955.png)


### WordPress安装（[参考自WordPress官方文档](https://codex.wordpress.org/zh-cn:%E5%AE%89%E8%A3%85_WordPress)） ###

1、下载并解压缩WordPress程序安装包。
 

```shell
# wget http://wordpress.org/latest.tar.gz
# tar -xzvf latest.tar.gz
```

2、在你的网页服务器上为WordPress创建一个数据库, 并且也创建一个MySQL 拥有所有权限可以进入和修改的用户.

```
$ mysql -u adminusername -p
Enter password:
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 5340 to server version: 3.23.54
 
Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
 
mysql> CREATE DATABASE databasename;
Query OK, 1 row affected (0.00 sec)
 
mysql> GRANT ALL PRIVILEGES ON databasename.* TO "wordpressusername"@"hostname"
    -> IDENTIFIED BY "password";
Query OK, 0 rows affected (0.00 sec)
  
mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.01 sec)

mysql> EXIT
Bye
$ 
```

3、 重命名 wp-config-sample.php 文件为 wp-config.php.用你最喜欢的 文本编辑器 打开 wp-config.php ，填上你的数据库信息。
 

```
# mv wp-config-sample.php wp-config.php
# vim wp-config.php
```

4、 把WordPress文件夹放在你服务器上想要放的地方:如果你想把通过顶级域名来访问你的WordPress博客 (例如 http://example.com/),移动或上传所有解压后的WordPress文件夹里面的文件(但不包括WordPress文件夹本身) 到你服务器的根目录下.

5、 在你喜欢的浏览器中访问wp-admin/install.php 以便启动安装程序.如果你在根目录下安装WordPress,，你应该访问: http://example.com/wp-admin/install.php

### Web性能测试 ###
PS：网站性能压力测试是性能调优过程中必不可少的一环。只有让服务器处在高压情况下才能真正体现出各种设置所暴露的问题。Apache中有个自带的，名为ab的程序，可以对Apache或其它类型的服务器进行网站访问压力测试。

ApacheBench命令原理：

ab命令会创建很多的并发访问线程，模拟多个访问者同时对某一URL地址进行访问。它的测试目标是基于URL的，因此，既可以用来测试Apache的负载压力，也可以测试nginx、lighthttp、tomcat、IIS等其它Web服务器的压力。

ab命令对发出负载的计算机要求很低，既不会占用很高CPU，也不会占用很多内存，但却会给目标服务器造成巨大的负载，其原理类似CC攻击。自己测试使用也须注意，否则一次上太多的负载，可能造成目标服务器因资源耗完，严重时甚至导致死机。

ApacheBench用法详解：

在Linux系统，一般安装好Apache后可以直接执行；

```
# ab -n 4000 -c 1000 http://www.ha97.com/
```

如果是Win系统下，打开cmd命令行窗口，cd到apache安装目录的bin目录下；

-n后面的4000代表总共发出4000个请求；-c后面的1000表示采用1000个并发（模拟1000个人同时访问），后面的网址表示测试的目标URL。

参考：[Web性能压力测试工具之ApacheBench（ab）详解](http://www.ha97.com/4617.html)