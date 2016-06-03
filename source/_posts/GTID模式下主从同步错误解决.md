---
title: GTID模式下主从同步错误解决
date: 2016-5-31 02:35
tags: 数据库管理
---

### GTID简介 ###
#### 什么是GTID ####
GTID(Global Transaction ID)是对于一个已提交事务的编号，并且是一个全局唯一的编号。
GTID实际上是由UUID+TID组成的。其中UUID是一个MySQL实例的唯一标识。TID代表了该实例上已经提交的事务数量，并且随着事务提交单调递增。下面是一个GTID的具体形式

3E11FA47-71CA-11E1-9E33-C80AA9429562:23
更详细的介绍可以参见：官方文档

### GTID的作用 ###
那么GTID功能的目的是什么呢？具体归纳主要有以下两点：

- 根据GTID可以知道事务最初是在哪个实例上提交的
- GTID的存在方便了Replication的Failover

关于GTID这个的更多说明可以参考网上搜来的这篇文章：http://www.cnblogs.com/cenalulu/p/4309009.html

### 主从同步错误的一般处理办法 ###
一般来说遇到主从同步错误都会先执行下 `mysql> show slave status\G` 检查下从库上两个IO线程的情况，在判断接下来改如何处理。一般的处理步骤在从库设置sql_slave_skip_counter。
1、 停止slave: stop slave;",
2、 设置跳过slave同步语句数： set global sql_slave_skip_counter = 1;",
3、 启动slave： start slave;",
4、 确认同步是否正确： show slave status G 确认",


但是当从库开启了GTID时，执行set global sql_slave_skip_counter = 1;时会报错。报错如下：
```
ERROR 1858 (HY000): sql_slave_skip_counter can not be set when the server is run
ning with @@GLOBAL.GTID_MODE = ON. Instead, for each transaction that you want t
o skip, generate an empty transaction with the same GTID as the transaction
```

这个意思是说开启了GTID_MODE不能跳过。
在我们执行show slave status\G;时，有下面两行是关于这个GTID的需要注意。
```
Retrieved_Gtid_Set: a99ac96f-18de-11e6-b507-525400da7a6f:172-1620
Executed_Gtid_Set: 3e5bec07-1ced-11e6-8f7c-525400bf7a4d:1-7,
a99ac96f-18de-11e6-b507-525400da7a6f:1-1581
```

这次主从同步在执行a99ac96f-18de-11e6-b507-525400da7a6f:1582这个GTID的事务时候失败。
这个事务向数据库里面插了一条记录，而这条记录在从库上已经被插入过了。插入的GTID是：3e5bec07-1ced-11e6-8f7c-525400bf7a4d:3。从库已经从主库拿到172-1620了，但是实际executed的只有到1581，那说明执行卡在1582了



解决方法是在从库上跳过a99ac96f-18de-11e6-b507-525400da7a6f:1582这个GTID的执行。可以在从库执行如下命令：

```
mysql> STOP SLAVE;

Query OK, 0 rows affected (0.00 sec)
mysql> SET SESSION GTID_NEXT = ’a99ac96f-18de-11e6-b507-525400da7a6f:1582′;
Query OK, 0 rows affected (0.00 sec)
mysql> BEGIN; COMMIT;
Query OK, 0 rows affected (0.00 sec)
Query OK, 0 rows affected (0.00 sec)

 

mysql> SET SESSION GTID_NEXT = AUTOMATIC;

Query OK, 0 rows affected (0.00 sec)

mysql> START SLAVE;
```

然后他就会跳过这个事务继续往下执行了