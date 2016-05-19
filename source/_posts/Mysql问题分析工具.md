---
title: Mysql问题分析工具
date: 2016-5-19 16:55
tags: 数据库管理
---

### 常用工具 ###
top、iostat查看CPU/IO情况
慢查询日志+explain
获取数据库的各种运行状态，如”show  engine innodb status”， 查询information_schema库里的INNODB_TRX、PROCESSLIST等状态表
tcpdump
strace

```
# top -d0.5 -c
```

以0.5秒频率刷新，查看mysql的CPU使用情况，主要是看下cpu占用率。

```
# iostat -txm 1
```

观察磁盘使用情况，util使用率越高，表示磁盘越繁忙

### 慢查询 ###
慢查询日志slowlog，使用mysqldumpslow可以统计分析出最消耗时间的TopN。
得到具体语句后，使用explain进行分析，进而得到优化方法。

### 数据库状态 ###
查看所有连接的运行情况

```
mysql> show full processlist; 
```

或者

```
mysql> select * from information_schema.processlist;
```

查看innodb的实时状态，关注cache命中率、读写次数、有无死锁等

```
mysql> show  engine innodb status \G  
```

查看事务运行情况

```
mysql> select * from information_schema.innodb_trx;
```

### 网络及系统分析 ###
tcpdump —— 抓取相关网络包
例如：

```
# tcpdump host 10.4.12.8 and port 3306 -i eth0 -w 10.4.12.8.pcap
```

strace —— 查看进程的系统调用
例如：

```
# strace -p 12345    # 12345为目标进程pid
```

