---
title:  Mysql主从配置
date: 2016-05-17 23:36
tags: 数据库管理
---


### 常见mysql主从架构 ###
1.Master-Slave
2.级联
3.双Master互为主备

### 主从同步原理 ###
下面的图说明了主从库之间是如何进行数据同步的，概括来讲就是主库通过把他的binlog推送给从库，从库通过binlog构建数据库来实现的，下面附上一张原理图：
![主从同步原理](http://7xr7kh.com1.z0.glb.clouddn.com/%E4%B8%BB%E4%BB%8E%E5%90%8C%E6%AD%A5%E5%8E%9F%E7%90%86.PNG)

所以要求主库必须要打开binlog
集群的所有MySQL必须配置server-id参数，并确保唯一

### Master-Slave 配置 ###
准备要做的就是配置server-id参数，这个参数是在mysql的配置文件中配置的，在这里是/etc/my.cnf。这里假设Master的server-id设置为1，Slave的server-id设置为2。

```
# vim /etc/my.cnf
```

首先，主库要创建好专门用于同步的账号，例如：
```
mysql> grant replication slave, replication client on *.* to  replication@'%' identified by '123456';
```

然后，利用mysqldump从主库导出数据，例如：

```
# mysqldump -h10.6.29.129 -uroot -p --all-databases --master-data=2 --routines --events --quick> 10.6.29.129.sql
```
--master-data=2 表示需要记录导出数据当时主库的binlog位置
--routines 表示导出存储过程（可视实际库表决定是否使用）
--events 表示导出事件（可根据实际库表决定是否使用）
--quick 表示让服务端不将结果集一次发送，而是分批发送，可减轻压力
另外，加上--master-data后默认是锁库的，可确保数据一致性，即导出数据和binlog位置的一致。
对于Innodb引擎，可增加--single-transaction取消锁库并利用Innodb事务特性确保数据一致

将备份sql从主库传到从库来，这里也可以直接在主库所在主机上操作，如果不在主库所在主机操作的话，可以使用scp传下。将导出的数据灌入到从库，例如：

```
# mysql -uroot < 10.6.29.129.sql 
```
从导出的数据文件的开头位置找到同步点

```
# vim 10.6.29.129.sql
```
找到类似下面的一段内容

```
-- CHANGE MASTER TO MASTER_LOG_FILE='mysql.000003', MASTER_LOG_POS=600；
```
依据上面的语句构造sql语句，使用有SUPER权限的账号登陆到从库，设置同步信息，例如下：

```
mysql> CHANGE MASTER TO MASTER_LOG_FILE='mysql.000003', MASTER_LOG_POS=600, master_host='10.6.29.129', master_port=3306, master_user='replication', master_password='123456';
```
` mysql> show slave status \G `  检查同步信息是否正确，这里主要是检查Master相关的一些配置，如Master_Host等等，主要是检查我们上面执行命令是否生效。如果配置和我们执行的命令一致，则说明修改生效。

执行`mysql> start slave; ` 启动同步，并检查同步状态。我在这里的时候遇到了一个错误，报错内容如下：

```
ERROR 1200 (HY000): The server is not configured as slave; fix in config file or with CHANGE MASTER TO
```
网上搜了下，google到了一篇大神的文章，文章上将出现这个报错的原因一般是server-id的设置有问题，文章总结了解决这个问题的三个步骤：
第一，` show slave status \G` 检查下从库的状态，对应我们上面的，看看change master to的修改是否生效了。

第二，` SHOW VARIABLES LIKE 'server_id'` server_id是否是0或者1，1是默认值，作为从库无效的，改成其他的吧。我在执行这一步得时候就发现我的server_id是1。。。后来想想修改配置之后我们重启mysqld可能是这个导致的，我赶紧重启了下，好了。

第三，主从都看下server_id的值，看看是否是一样的，是一样的也会导致问题，作者建议将server_id的值改为ip地址的最后几位，这样便于记忆而且不会重复。

经过排查解决了上面的问题之后在`start slave;` 没有报错了。最后在` show slave status \G` 。这里主要是检查Slave_IO_Running和Slave_SQL_Running的情况，Slave_IO_Running/Slave_SQL_Running均为Yes，表示同步成功。

### 级联架构 ###
每个作为Slave的MySQLDB只需要根据其对应主库设置同步信息即可，步骤与上一小节相同。

但需要注意的是，默认情况下，从库执行relay-log中的SQL语句不会记录到BINLOG。
所以，处于中间层的MySQL配置项需要额外开启log_slave_updates=1。

级联架构的示意图如下：
![级联架构图](http://7xr7kh.com1.z0.glb.clouddn.com/%E7%BA%A7%E8%81%94%E5%8E%9F%E7%90%86.PNG)

### Master-Master ###
首先，按普通的主从配置步骤将MasterB配置成MasterA的从库；然后，确保MasterB没有写入，通过show master status命令在MasterB上得到其同步点，再将MasterA配置成MasterB的从库。

通常，为了简化逻辑，其中一个Master会设置为只读，正常只通过另外一个Master进行读写。
若要两边都写，为了避免自增id冲突，一般会设置奇偶错开，即一台的自增ID均为奇数，另一台均为偶数。这个要在业务中通过设置表属性设置的。




