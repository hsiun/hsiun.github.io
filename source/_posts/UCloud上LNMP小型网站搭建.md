---
title: UCloud上LNMP小型网站搭建
date: 2016-04-01 02:22:24
tags: 系统运维
---

#### 更新 ####
* [2016/6/8 添加虚拟主机的说明](#1) 
* [2016/6/8 添加禁止ip访问的说明](#2) 
* [2016/6/8 添加缓存策略的配置说明(#3) 


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

<span id='vs'>添加虚拟主机</span>
这里添加了一个名为www.domain.com的服务器，如果有需要的话，可以按照自己的需要添加更多server。

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


<h3 id="1">虚拟主机</h3>
上面的这个nginx其实是配置了虚拟主机的，虚拟主机是用来实现在一台主机上实现多个站点的方式，在使用主机的时候，这里是直接将域名解析到ip了，当这个ip对应的主机上有多个站点的时候要如何区分这些站点呢，这就是通过虚拟主机实现的。虚拟主机可以配置在不同的端口，使用不同的域名等等方式来配置。上面有对应 [虚拟主机的说明](#vs)。

<h3 id="2">禁止ip访问</h3>
禁止ip访问的目的在在于防止恶意解析，一般来说将域名解析到网站分成两步。第一步，将域名解析到网站所在的主机，第二步，在web服务器中将域名与相应的网站绑定。但是，如果通过主机IP能直接访问某网站，那么把域名解析到这个IP也将能访问到该网站，而无需在主机上绑定，也就是说任何人将任何域名解析到这个IP就能访问到这个网站。可能您并不介意通过别人的域名访问到您的网站，但有些情况对方可能是恶意这样做的，那我们就可以通过限制直接通过ip访问来防止恶意解析。最简单配置如下，在server中添加下面一行就可以。
```
listen 80 default; 
```
后面的default参数表示这个是默认虚拟主机。
下面是一些有用的技巧。
比如别人通过ip或者未知域名访问你的网站的时候，你希望禁止显示任何有效内容，可以给他返回500。
```
server {  
      listen 80 default;  
      return 500;  
   } 
```

也可以把这些流量收集起来，导入到自己的网站，只要做以下跳转设置就可以：
```
server {  
       listen 80 default;  
       rewrite ^(.*) http://www.123.cn permanent;  
   } 
```

按照如上设置后，确实不能通过IP访问服务器了，但是在应该用中出现当server_name后跟多个域名时，其中一个域名怎么都无法访问，设置如下：
```
server  {  
        listen 80;  
        server_name www.123.cn example.com    
   }
```
没更改之前，通过server_name 中的www.123.cn 123.cn均可访问服务器，加入Nginx 禁止IP访问的设置后，通过123.cn无法访问服务器了，www.123.cn可以访问，用 Nginx -t 检测配置文件会提示warning：
```
   [warn]: conflicting server name “467.cn” on 0.0.0.0:80, 
      ignored  
   the configuration file /usr/local/Nginx/conf/
      Nginx.conf syntax is ok  
   configuration file /usr/local/Nginx/conf/Nginx.
      conf test is successful
```
最后通过在listen 80 default;后再加server_name _;解决，形式如下：
```
  #禁止IP访问  
   server  {  
       listen 80 default;  
       server_name _;  
       server_name www.123.cn example.com 
       return 500;  
   } 
```
这样，通过123.cn就能访问服务器了。

参考：[Nginx服务器如何禁止通过IP地址访问网站 ](http://blog.sohu.com/s/MzcwNDkxMzM/301722120.html)

<h3 id=3>缓存控制</h3>
缓存控制主要是在使用了cdn的时候会用到，通过配置源站webserver的header来控制cdn节点的缓存机制。nginx的缓存配置主要是通过add_header和expires这两个模块来控制的。

1） add_header

语法：add_header name value 
默认值：none 
使用字段：http, server, location

 
2） expires

语法：expires [time|epoch|max|off] 
默认值：expires off 
使用字段：http, server, location 


这个指令控制是否在应答中标记一个过期时间，如果是，如何标记。
off 将禁止修改头部中的 Expires和Cache-Control字段。
Time控制“Cache-Control”的值，负数表示no-cache
epoch 将Expires头设置为1 January, 1970 00:00:01 GMT。
max 将Expires头设置为31 December 2037 23:59:59 GMT，将Cache-Control最大化到10 年。
 

例如设置php的文件类型过期时间设置为1个小时：
1） add_header

语法：add_header name value 
默认值：none 
使用字段：http, server, location
 

2） expires

语法：expires [time|epoch|max|off] 
默认值：expires off 
使用字段：http, server, location 

 

这个指令控制是否在应答中标记一个过期时间，如果是，如何标记。
off 将禁止修改头部中的 Expires和Cache-Control字段。
Time控制“Cache-Control”的值，负数表示no-cache
epoch 将Expires头设置为1 January, 1970 00:00:01 GMT。
max 将Expires头设置为31 December 2037 23:59:59 GMT，将Cache-Control最大化到10 年。

 

例如设置php的文件类型过期时间设置为1个小时，在nginx的配置文件中加入下面一行：
```
expires 1h;
```

不缓存
```
expries -1h;
```

另外可以通过add_header设置相对应的缓存策略，对于动态的php文件设置为不缓存：

 
```
location ~ .*\.php$ {
    if ($request_uri !~ ^/dynamicimg/) {
        add_header              Cache-Control "no-cache";
        add_header              Pragma no-cache;
    }
  }
```
