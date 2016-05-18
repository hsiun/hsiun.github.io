---
title:  Mysql慢查询分析与优化
date: 2016-5-18 18:57
tags: 数据库管理
---

### 概述（慢查询定义与危害） ###
慢查询——顾名思义，耗时很长、响应很慢的查询

危害——
导致整个数据库响应变慢（过多消耗cpu或者io）
短连接场景下还可能引起耗尽连接数


### 慢查询日志相关参数（开启慢查询） ###
以下参数均是是在Mysql的配置文件/etc/my.cnf中配置的，配置在[mysqld]下。

参数名            |   	含义
---------------|------------------
slow_query_log	| 是否开启慢查询
long_query_time	| 查询消耗超过多少时间认为是慢查询，单位秒
log_output	|   日志的输出形式(FILE或者TABLE)
slow-query-log-file |	慢查询的保存文件
log-queries-not-using-indexes	      |   记录没有使用索引的查询

示例参数配置

```
# 慢查询相关
slow_query_log = 1
long_query_time = 1

# 日志的输出形式
log_output = FILE
slow-query-log-file = /var/log/mysql/mysql-slow.log

```

修改参数之后记得重启mysqld服务，可以在mysql命令行验证修改是否生效。命令如下：

```
mysql> show global variables like 'slow%' ;
mysql> show global variables like 'long%' ;
mysql> show global variables like 'log%' ;
```

经过上面的配置mysql的慢日志就打开了，然后mysql就会记录各条查询相关信息了，下面是一个人为构造的演示例子。

### 演示例子（自造慢查询：） ###
下面是用来新建测试用的表和查询语句的sql命令。
```
mysql> use test;
mysql> create table b (id int not null primary key, name varchar(100) not null);
mysql> select sleep(1);
mysql> select * from b where name = 'a';
mysql> select * from b where name = 'b';
mysql> select * from b where name = 'c';
```

我们可以直接看下mysql-slow.log中的内容，cat或vim都行，只是看一眼：）

```
# cat /var/log/mysql/mysql-slow.log
```
下面是文件中个参数意义的介绍：

Querytime: sql的请求执行时间
Locktime: sql 等锁的时间
Rows_examined: sql处理过程中扫描的行数；通常，该值越大，对cpu/io消耗也就越大

### 慢查询分析 ###
首先是慢查询分析工具——mysqldumpslow，示例用法如下：

```
# mysqldumpslow -t 10 -s t /path/to/slow.log
```
参数说明：
1. -t：返回前面多少条的数据，即top几；
2. -s：排序方式。c , t , l , r 分别表示记录次数、时间、查询时间的多少、返回的记录数排序；ac , at , al , ar 表示相应的倒叙；

TABLE类型慢日志处理
直接做工具处理相对比较麻烦，但我们可以变相处理。
处理思路：
1. 将mysql.slow_log表的数据转成日志文件形式
2. 然后再用mysqldumpslow进行分析

这里提供一个工具(python脚本，需要先安装MySQL-python依赖库)，可以将mysql.slow_log表导出成日志格式
https://gist.github.com/sennajox/fdff82cc02b161d0141d

例如，要列出2016-5-1至2016-5-18期间的慢查询

```
# python slow_log_dump.py 10.6.29.129 3306 root 123456 '2016-5-1' '2016-5-18'
```
### 优化分析 ###
记录slowlog仅仅是第一步，更重要的是找到慢查询的根源。
而这里，我们可以利用explain命令，对慢查询sql执行explain得到对应的执行计划进行分析，从而确定如何优化。

```
mysql> explain select * from b where name = 'a';
```
各个参数说明
关键列名	     |          含义
------------------| -------------------
type          |	使用了什么查询方式，由好到差，依次为const > eq_ref > ref > range > index > ALL注意: index表示遍历索引；而ref表示使用非唯一的索引
key	             |   最终使用到的索引
ref	    |      使用了索引的那一列，可能为列名或者const
rows  |   	扫描的行数
extra	|  附加信息，比如”Using Where”、“Using Index”、“Using filesort”、“Using temporary”

![explain示例1](http://7xr7kh.com1.z0.glb.clouddn.com/mysql%E6%85%A2%E6%9F%A5%E8%AF%A2%E5%88%86%E6%9E%901.png)


![explain示例2](http://7xr7kh.com1.z0.glb.clouddn.com/mysql%E6%85%A2%E6%9F%A5%E8%AF%A2%E5%88%86%E6%9E%902.png)

### 优化要点 ###

1.严禁在MySQL执行复杂的计算，以免过多消耗CPU，把复杂的事情交给程序处理

2.尽可能少用关联查询，如果不得不使用，最好不超过2个表，以免笛卡尔积过大，一方面增加读取磁盘几率，一方面有可能生成文件临时表，极大影响IO

3.适当的使用索引，且仅对离散程度比较高的列使用，让索引的效果可以最大化；索引是牺牲了写性能来换取读性能，因此也不能过多

4.控制单表的数据量，多作分库分表

