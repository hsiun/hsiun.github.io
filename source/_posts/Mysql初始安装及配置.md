---
title:  Mysql初始安装与配置
date: 2016-05-17 22:10
tags: 数据库管理
---



### 概述 ###
这里是同事分享的mysql课程的笔记，中间加入了一些自己网上找来的东西，按照自己的想法弄了这篇博客。

### 环境 ###
系统环境：centos6.5 ``` cat /etc/centos-relese```
数据库版本：mysql5.5(通过remi源安装）

### 安装 ###
CentOS6.5(试验环境）
```
yum install mysql-server --enablerepo=remi
```
Ubuntu（默认版本为5.5)
```
apt-get install mysql-server
```
源码安装
官方下载链接
http://dev.mysql.com/downloads/mysql/5.5.html#downloads
```
./configure  #进入到下载的源文件中，首先要解压缩
make && make install
```

### 修改配置文件 ###
默认的配置文件位置在/etc/my.cnf，默认配置文件过于简单在实际使用中先对其进行一定的修改。
参数名                 | 含义
--------            | ---
bind-address               |	 监听ip
port	                            |  监听端口
character_set_server	| 默认字符集(建议utf8)
max_connect_errors	| 最大允许错误次数(并非指密码错误，而是访问协议不正确之类的，如果某个IP错误次数达到该阈值，则被屏蔽)
max_connections	       | 最大连接数(能同时访问server的连接个数，如果达到该阈值，则不能再连接到server)
datadir	          | 数据保存目录
log-error		| 	错误日志保存路径
skip_name_resolve         |	跳过域名解析(可加快client连接速度)
back_log            |	创建TCP连接时，允许的最大同时未完成的连接个数(主要适用于短连接场景)
log-bin	             | Binlog的保存路径
binlog-format	      |Binlog写入格式，共有ROW/STATEMENT/MIXED三种可选。一般使用MIXED。
expire_logs_days	    |Binlog保留时间(单位:天)


这里采用的配置文件（在mysqld下添加如下内容）
```
bind-address = 10.19.96.184
port = 3306
character_set_server = utf8
max_connect_errors = 1000000
max_connections = 500
datadir = /data/mysql
log-error = /var/log/mysql/mysql-error.log
skip_name_resolve
back_log = 2000
log-bin = /data/mysql
binlog-format = MIXED
expire_logs_days = 3

```
如果datadir/log-error/log-bin等对应目录需要确保已创建，并且权限为mysql所有。例如，
```
mkdir -p /data/mysql && chown -R mysql.mysql /data/mysql
mkdir -p /var/log/mysql && chown -R mysql.mysql /var/log/mysql
```
配置完成，启动mysql: 
```
/etc/init.d/mysqld start
或者
service mysqld start
```

### 权限设置 ###
执行select * from mysql.user; 可以查看到当前的所有用户。

结果集中的几个关键字段：
Host——对应的允许访问域(%表示全域，192.168.%.%表示对应账号对该段均可访问，192.168.8.100表示对应账号仅这台机器可访问)
User——用户名
Password——密码加密串
xxx_priv——各种权限


用户权限管理
``` bash
mysql -u root -p # 1、以管理员身份登录mysql

use mysql # 2、选择mysql数据库

create user 'testuser'@'localhost' identified by 'testpassword' #3、创建用户并设定密码

flush privileges #4、使操作生效

create database testdb #5、为用户创建数据库

grant all privileges on testdb.* to test@localhost identified  by '1234' #6、为用户赋予操作数据库testdb的所有权限

flush privileges #7、使操作生效

mysql -u test -p #8、用新用户登录
```

如果当前账号拥有所有权限，可用最简单的新增用户并授权：

``` grant all on *.* to `root`@`%` identified by '123456' with grant option; ```

其中with grant option表示新增的该账号是否有grant权限，即是否可以通过其创建新账号。
非常不建议给用户开放全部权限，最好给新用户仅开放所需要的相关权限


### 导入测试用数据库 ###
```bash
wget https://launchpadlibrarian.net/24493586/employees_db-full-1.0.6.tar.bz2

tar -xjf employees_db-full-1.0.6.tar.bz2 

cd employees_db

mysql -u root -p < employees.sql

```

### 练习 ### 
设置权限, 要求如下：
1. root账号仅能从本地以及特定机器访问
2. 创建一个test账号，允许所有机器访问


```mysql> grant all on *.* to `root`@`localhost` identified by '123456' with grant option; ```


```mysql> grant all on *.* to `test`@`%` identified by '123456' with grant option; ```
