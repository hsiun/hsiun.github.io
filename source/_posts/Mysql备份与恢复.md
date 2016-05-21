---
title:  Mysql备份与恢复
date: 2016-5-21 11:35
tags: 数据库管理
---

### 概述 ###
这里主要介绍通过mysqldump来导出数据和通过binlog导入数据的Mysql导入导出问题。

### 备份 ###
常用工具：mysqldump,  xtrabackup

mysqldump: 原生数据导出工具，以sql的形式导出保存
xtrabackup: percona团队提供的备份工具，基于文件系统的备份

#### 备份全库——最简单版本 ####

```
mysqldump -h10.6.29.129 -uroot -p --all-databases > 10.6.29.129.sql
```
mysqldump是直接屏幕输出，所以重定向后即可得到对应的备份文件。
1. 这个备份命令很简单，但存在问题：
2. 会锁全库，影响业务
3. 有可能缺失常规表以外的内容，如存储过程
4. 未记录binlog同步点，不能用于主从同步以及利用binlog增量恢复
对于数据集较大的表，可能会吃掉server端大量内存

#### 备份全库——进阶版 ####

```
mysqldump -h10.6.29.129 -uroot -p --all-databases --master-data=2 --routines --events --quick --single-transaction > 10.6.29.129.sql
```

- --master-data=2 表示需要记录导出数据当时主库的binlog位置
- --routines 表示导出存储过程（可视实际库表决定是否使用）
- --events 表示导出事件（可根据实际库表决定是否使用）
- --quick 表示让服务端不将结果集一次发送，而是分批发送，可减轻压力。另外，加上--master-data后默认是锁库的，可确保数据一致性，即导出数据和binlog位置的一致。
- --single-transaction 取消锁库并利用Innodb事务特性确保数据一致，但对MyISAM引擎不能确保一致性(即备份期间的写入，仍有可能被导出到备份文件中)

#### 备份部分database ####

```
mysqldump -h10.6.29.129 -uroot -p --master-data=2 --routines –events --quick --single-transaction --databases db1 db2 db3 > 10.6.29.129.sql
```

(将需要导出的库名，依次填写在databases参数后)

#### 备份指定database的部分表 ####

```
mysqldump -h10.6.29.129 -uroot –p --master-data=2 --routines –events --quick --single-transaction mydb table1 table2 > 10.6.29.129.sql
```

(先写明指定的database，然后紧跟需要备份的表名)

### 数据恢复 ###
直接利用mysqlclient以及冷备文件进行导入

```
mysql -h10.6.29.129 -uroot -p < 10.6.29.129.sql
```
（简单易行，但要确保导入的库表，不会同时有业务在写入）

#### 借助binlog恢复数据库 ####
在冷备数据的基础上，借助binlog可以恢复到冷备时间点至最新binlog之间的任意时刻。
![利用binlog恢复到任意时间点](http://7xr7kh.com1.z0.glb.clouddn.com/binlog%E6%81%A2%E5%A4%8D.png)

首先由于binlog二进制文件，要使用binlog必须先对binlog进行解析，解析的工具就是mysqlbinlog。
使用mysqlbinlog工具，对binlog文件进行解析
简单用法——

```
mysqlbinlog mysql.000004 > binlog.000004
```
使用mysqlbinlog工具，对binlog文件进行解析
进阶用法——

```
mysqlbinlog mysql.000004 --start-position=549212174 --stop-datetime='2014-9-10 10:00' > binlog.000004.sql
```
--start-position 表示从binlog的那个位置开始解析，而这个起始点可以通过备份时的master-data参数得到
--stop-datetime 表示只解析到哪个时间点的语句为止，可用于回档到某个指定时间点

--start-datetime 从那个时间点开始，但通常还是尽量使用start-position，更为准确
--verbose, -v 使用方式：-vv, -vvv，常用于ROW模式的详细输出

恢复的具体操作方法，就是先通过冷备份将数据库还原到备份点，然后在通过binlog生成从冷备点恢复的sql。分别执行下两个sql文件就可以了。
