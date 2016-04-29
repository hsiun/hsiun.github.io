---
title:  Centos6.5安装mysql5.6
date: 2015-11-19 06:41
tags: 数据库管理
---
centos6.5源默认的MySQL好像是5.1。如果需要安装其他版本的mysql可以通过下载mysql相应版本的rpm包来实现。这篇博客记录的就是使用yum在Centos6.5版本的系统上安装MySQL5.6。安装包是”架构相关的”因此应用需要32位（i386）或者64位（x86_64）的操作系统。

操作时具体的环境
操作系统：Centos6.5
架构：X86_64

依照下面的步骤可以安装Mysql5.6
第一步：登入到服务器中，并且下载yum的rpm包。（URL：http://dev.mysql.com/downloads/repo/）
```
yum install wget  
wget http://repo.mysql.com/mysql-community-release-el6-5.noarch.rpm  
```


第二步：安装下载的rpm包
```
rpm -ivh mysql-community-release-el6-5.noarch.rpm  
```


第三步：在命令行中安装mysql5.6，使用yum的方式安装，依赖的软件包会一并安装。
```
yum install mysql-server  
```

第四步：安装完mysql服务器之后使用命令把服务启动起来
```
/etc/init.d/mysqld start  
或者

service mysqld start  
```

第五步：因为mysql服务器默认安装时没有密码的，必须从新设置mysql的密码
可以使用下面的命令来完成一系列的安全设置
```
mysql_secure_installation  
也可以使用下面的方法

mysql -u root  

mysql> use mysql;  
mysql> update user set password=PASSWORD("GIVE-NEW-ROOT-PASSWORD") where User='root';  
mysql> flush privileges;  
mysql> quit  
```


自此，mysql便安装完成了。过程还是非常简单的，做个记录供以后回顾。下面是mysql的一些基础知识。


首先一点要说的是数据库和数据库实例的区别，他们两者之间的区别让我想到了程序和进程之间的区别，其实他们之间的区别是一样的。数据库就是指静态的在文件系统上存在的文件，而数据库实例指的则是运行在内存中的数据库后台线程和数据的内存区。这就像程序一般我们是讲静态的程序文件，而进程则是讲程序在内存中运行的状态。关于数据库和实例的定义这里借用《MySQL技术内幕》这本书中的相关说明：

数据库：物理操作系统文件或其他形式文件类型的集合。在Mysql数据库中，数据库文件可以是frm，MYD，MYI，ibd结尾的文件。当使用NDB引擎时，数据库的文件可能不是操作系统上的文件，而是存放于内存之中的文件，但是定义仍然不变。 
实例：Mysql数据库由后台线程以及一个共享内存区组成。共享内存可以被运行的后台线程所共享。需要牢记的是，数据库才是真正操作数据库文件的。

Mysql进程：mysql被设计为一个但进程多线程架构的数据库。在linux系统中，创建进程所需的花销要远大于创建线程。进程的创建需要复制整个进程的所有数据，而新建线程的数据如文件描述符等是共享的。mysql在linux系统只会有一个进程，可以使用ps命令确认。

ps -ef | grep mysqld
1
1
配置文件：当Mysql启动时，会去配置文件，然后根据配置文件中的参数来启动数据库，Mysql有系列的配置文件，他会根据一定的顺序依次读取。后面读取的配置后覆盖前面相同的配置。可以使用下面的命令确认。

mysql --help | grep my.cnf
1
1
数据文件：在配置文件中有数据所在位置的参数，具体参数名叫做datadir的。在linux中默认是/usr/local/mysql/data。这个参数是可以修改的，默认是一个链接。可以使用下面命令验证，在mysql命令行中使用bash命令，可以通过在命令前加system关键字的方式实现。

SHOW VARIABLES LIKE 'datadir'\G;
1
1
其他http://dev.mysql.com/doc/