---
title: UCloud上LNMP小型网站搭建
date: 2016-04-01 02:22:24
tags: 系统运维
---

### 目录 ###
 1. 介绍
 2. LNMP环境搭建
 3. 打开UCloud防火墙
 4. Discuz!安装
 5. 应用测试

### 介绍 ###
这篇博客是之前[UCloud上LAMP小型网站搭建与测试](http://zone.gaospot.com/2016/03/30/UCloud%E4%B8%8ALAMP%E5%B0%8F%E5%9E%8B%E7%BD%91%E7%AB%99%E6%90%AD%E5%BB%BA%E4%B8%8E%E6%B5%8B%E8%AF%95/)的姐妹篇，重点在于通过介绍在UCloud云主机上安装Discuz!来介绍LNMP环境的搭建，这里Discuz!可以换成任何用户自己的应用，过程都是类似的。

### LNMP环境搭建 ###
关于主机的创建，登录和管理可以参考之前的那篇博客，这里直接就介绍在主机上安装NMP环境，安装步骤如下：

1、Uhost环境
CentOS-6.5 x86_64


2、安装NMP

```bash
# ln -sf /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# yum install -y nginx mysql-server php php-gd php-xml php-fpm php-mysql php-bcmath php-mbstring php-mcrypt
```


3、配置Nginx

```bash
# vim /etc/nginx/nginx.conf
```

修改如下内容：

```
user nginx;
worker_processes  1;
error_log /var/log/nginx/error.log;
pid /var/run/nginx.pid;

events {
    use epoll;
    worker_connections 1024;
}

http {
    include /etc/nginx/mime.types;
    default_type application/octet-stream;
    sendfile on;
    tcp_nopush on;
    keepalive_timeout 65;
    gzip on;
    include /etc/nginx/conf.d/*.conf;
}
```

添加虚拟主机

```bash
# vim/etc/nginx/conf.d/virtual.conf
```

```
server {
    listen       80;
    server_name  www.domain.com;
    root /var/www/html;
    index index.html index.htm index.php;
    access_log  /var/log/nginx/domain-acess.log;

    location ~ .*\.php?$ {
        include fastcgi.conf;
        fastcgi_pass 127.0.0.1:9000;
        fastcgi_index index.php;
        access_log off;
    }

    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|js|css)$ {
        expires 15d;
    }

}
```

4、配置PHP

```bash
# sed -i 's#;date.timezone =#date.timezone = Asia/Shanghai#' /etc/php.ini
# sed -n 's#user = apache#user = nginx#p' /etc/php-fpm.d/www.conf
# sed -n 's#group = apache#group = nginx#p' /etc/php-fpm.d/www.conf
```

5、启动服务

```bash
# service nginx start
# chkconfig nginx on
# service php-fpm start
# chkconfig nginx on
# echo “<?php phpinfo();” > /var/www/html/phpinfo.php
```

打开浏览器，输入下面URL(记得解析DNS，或者修改hosts文件)
http://www.domain.com/phpinfo.php


6、启动MySQL
MySQL默认有生成几个配置文件，可以根据自己的硬件情况把文件复制到/etc/my.cnf

```bash
# ls /usr/share/mysql/*.cnf
# service mysqld start
# chkconfig mysqld on
```

### 打开UCloud防火墙 ###
UCloud平台防火墙的打开可以参考之前那篇博客[UCloud上LAMP小型网站搭建与测试](http://zone.gaospot.com/2016/03/30/UCloud%E4%B8%8ALAMP%E5%B0%8F%E5%9E%8B%E7%BD%91%E7%AB%99%E6%90%AD%E5%BB%BA%E4%B8%8E%E6%B5%8B%E8%AF%95/)，上面有详细的说明。平台上面默认的防火墙是不允许修改规则的，所以要添加新的规则，需要新建防火墙。

### Discuz!安装 ###
1、下载Discuz，并解压。将upload 这个目录下面的所有文件拷贝到网站根目录，或者是其他二级目录也行。

```bash
# wget http://download.comsenz.com/DiscuzX/3.1/Discuz_X3.1_SC_UTF8.zip

```

2、文件成功解压到网站根目录后，通过在浏览器中运行安装向导来安装Discuz系统。然后一步步按提示向下安装。

3、安装好后就可以在浏览器访问您的论坛了！

参考：[Discuz!X3.1 全新安装图文教程 ](http://www.discuz.net/thread-3456887-1-1.html)

### 应用测试 ###
测试不是必要的过程，但为了更好的掌握应用的负载能力还是建议进行测试的，以对应用状况有比较好的了解。测试工具和测试方法网上有很多，可以自行Google，也可以参考上面一篇博客介绍的方法。最后说一点是没有完全正确的测试，测试只能模拟高并发是的情况，但与真实访问时候还是有差距的。


