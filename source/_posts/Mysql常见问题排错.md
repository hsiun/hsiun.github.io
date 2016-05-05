---
title:  Mysql常见问题排查
date: 2016-5-4 11:35
tags: 数据库管理
---

解决UDB主从同步不一致
1、: "开启super权限：update mysql.user set super_priv = 'Y' where user = 'root'; flush privileges;", 
2、: "停止slave: stop slave;", 
3、: "设置跳过slave同步语句数： set global sql_slave_skip_counter = 1;", 
4、: "启动slave： start slave;", 
5、: "确认同步是否正确： show slave status G 确认", 
6、: "若同步还是出错重复第2-5步，若同步正确则结束"


数据库上可以在哪里查询最近执行较慢的sql？

1、可以通过这个命令查看：select * from mysql.slow_log where start_time >= 'xxxx-xx-xx';
2、如何查看MySQL数据库的死锁信息
3、服务器运行一天后，就开始频繁报错：Deadlock found when trying to get to lock; try restarting transaction.
4、使用终端或命令提示符登录到MySQL，输入命令：mysql -h xxxx.xxx.xxx -P 3306 -u username -p password
5、在MySQL客户端下输入命令：show engine innodb status \G;
6、在打印出来的信息中找到“LATEST DETECTED DEADLOCK”一节内容
7、分析其中的内容，我们就可以知道最近导致死锁的事务有哪些.


Mysql 5.6内存问题

mysql5.6版本的，内存少于3G的，内存使用率100%一般是正常情况，可以尝试设置performance_schema=0，重启
原因：
5.6版本本来就比较消耗内存，用户的内存配置又低，可以尝试修改performance_schema=0，并降低缓冲池大小，或建议用户增大内存。
5.6的启动内存大约就需要500M，缓冲区内存900M，当缓冲区填满以后内存就肯定在100%了，并且不会再下降 以后如果是mysql5.6，如果它的总内存在2G以内的，数据量超过900M的，都会引起内存使用超过100%并且不会再下降的情况。

如果是这样，可以：1 减少缓冲池大小 2 禁用performance_schema=0 3 增大内存  

三种方法 2-3G的内存一般会遇到
