---
layout: post
category:  Mysql 
title: MySQL的Replication.主从同步(Master-slave)
tags: Mysql
---
## **Mysql同步的基本概念** ##
----------
**前言**

MySQL的Replication是一种多个MySQL的数据库做主从同步的方案，这对我们实现数据库的冗灾、备份、恢复、负载均衡等都是有极大帮助的。一来可以实现简单的读写分离场景，二来也可以纯粹的给数据库备份。<br>

**MySQL官方提供的MySQL Replication教程：**

[Mysql官网Replication教程地址](https://dev.mysql.com/doc/refman/5.6/en/replication.html)

**下图是MySQL官方给出了使用Replication的场景：**

![](http://dl.iteye.com/upload/attachment/0076/1660/4a9328c6-a24d-34d4-9fd3-fd39e39fcec5.jpg)

**Mysql Replication原理：**

Mysql 的 Replication 是一个异步的复制过程，从一个MySQL节点（称之为Master）复制到另一个MySQL节点（称之Slave）。在 Master 与 Slave 之间的实现整个复制过程主要由三个线程来完成，其中两个线程（SQL 线程和 I/O 线程）在 Slave 端，另外一个线程（I/O 线程）在 Master 端。

要实现 MySQL 的 Replication ，首先必须打开 Master 端的 Binary Log，因为整个复制过程实际上就是 Slave 从 Master 端获取该日志然后再在自己身上完全顺序的执行日志中所记录的各种操作。

**原理总结与注意点：**

* 每个从仅可以设置一个主。
* 主在执行sql之后，记录二进制log文件（bin-log）。
* 从连接主，并从主获取binlog，存于本地relay-log，并从上次记住的位置起执行sql，一旦遇到错误则停止同步。
* 主从间的数据库不是实时同步，就算网络连接正常，也存在瞬间，主从数据不一致。
* 如果主从的网络断开，从会在网络正常后，批量同步。
* 如果对从进行修改数据，那么很可能从在执行主的bin-log时出现错误而停止同步，这个是很危险的操作。所以一般情况下，非常小心的修改从上的数据。
* 一个衍生的配置是双主，互为主从配置，只要双方的修改不冲突，可以工作良好。

## **Mysql同步主从配置** ##
----------

**1.实验环境:**

* 主服务器（Master）：192.168.1.10
* 从服务器（Slave）：192.168.1.11
* 两台机器都装有mysql。（注意：最好两个机器MySQL版本一致，或者从服务器的的版本比主服务器版本高，否则可能出现高版本的log文件拿到低版本的Mysql服务器上没办法执行。）

**2.主服务器配置（Master配置）**

* **创建mysql 的slave用户**

		grant replication slave on *.* to slave@192.168.1.11 identified by 'slave';

	这里的IP地址是从服务器的IP地址。<br>
	replication slave 为mysql同步的必须权限，此处不要授权all权限.

* **修改master的mysql配置文件 my.ini(Windows) 或 my.cnf(Linux)**


		#设置 server id(server-id=1中的1可以任定义，只要是唯一的就行。)
		server-id=1 
	
		# 打开二进制日志 ，最好放在不同的硬盘上，减小 IO 消耗
		log-bin= mysql-binlog
	
		# 设置二进制日志保存日期
		expire_logs_day=10
	
		# 设置 每个 binlog 文件的大小
		max_binlog_size=500M
	
		# 设置需要备份的数据库
		binlog-do-db=back

		# 设置忽略备份的数据库
		binlog_ignore_db=mysql

	
* **重启Mysql**
	
	Windows：

		net stop mysql;
		net start mysql;

	Linux：

		service mysql restart

* **获取相关的db信息, 供slave链接db时使用**

		mysql> show master status;
		+------------------+----------+--------------+------------------+-------------------+
		| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
		+------------------+----------+--------------+------------------+-------------------+
		| mysql_bin.000008 |      452 | back         |                  |                   |
		+------------------+----------+--------------+------------------+-------------------+
		1 row in set (0.00 sec)


**3.从服务器配置（Slave配置）**

* **修改Slave的mysql配置文件 my.ini(Windows) 或 my.cnf(Linux)**

		# 设置 server id(server-id=2中的2可以任定义，只要是唯一的就行。)
		server-id=2 
		
		# 指定slave要复制哪个库
		replicate-do-db=back

* **启动从服务器上的复制线程**

		start slave;

* **检查主从同步**

		mysql> show slave status\G;
		*************************** 1. row ***************************
		               Slave_IO_State: Waiting for master to send event
		                  Master_Host: 192.168.1.10
		                  Master_User: slave
		                  Master_Port: 3306
		                Connect_Retry: 60
		              Master_Log_File: mysql_bin.000008
		          Read_Master_Log_Pos: 452
		               Relay_Log_File: WangPC-relay-bin.000004
		                Relay_Log_Pos: 320
		        Relay_Master_Log_File: mysql_bin.000008
		             Slave_IO_Running: Yes
		            Slave_SQL_Running: Yes
		              Replicate_Do_DB: back
		          Replicate_Ignore_DB:
		           Replicate_Do_Table:
		       Replicate_Ignore_Table:
		      Replicate_Wild_Do_Table:
		  Replicate_Wild_Ignore_Table:
		                   Last_Errno: 0
		                   Last_Error:
		                 Skip_Counter: 0
		          Exec_Master_Log_Pos: 452
		              Relay_Log_Space: 528
		              Until_Condition: None
		               Until_Log_File:
		                Until_Log_Pos: 0
		           Master_SSL_Allowed: No
		           Master_SSL_CA_File:
		           Master_SSL_CA_Path:
		              Master_SSL_Cert:
		            Master_SSL_Cipher:
		               Master_SSL_Key:
		        Seconds_Behind_Master: 0
		Master_SSL_Verify_Server_Cert: No
		                Last_IO_Errno: 0
		                Last_IO_Error:
		               Last_SQL_Errno: 0
		               Last_SQL_Error:
		  Replicate_Ignore_Server_Ids:
		             Master_Server_Id: 1
		                  Master_UUID: 3613c664-b7f7-11e7-9c73-005056c00001
		             Master_Info_File: C:\ProgramData\MySQL\MySQL Server 5.7\Data\master.info
		                    SQL_Delay: 0
		          SQL_Remaining_Delay: NULL
		      Slave_SQL_Running_State: Slave has read all relay log; waiting for more updates
		           Master_Retry_Count: 86400
		                  Master_Bind:
		      Last_IO_Error_Timestamp:
		     Last_SQL_Error_Timestamp:
		               Master_SSL_Crl:
		           Master_SSL_Crlpath:
		           Retrieved_Gtid_Set:
		            Executed_Gtid_Set:
		                Auto_Position: 0
		         Replicate_Rewrite_DB:
		                 Channel_Name:
		           Master_TLS_Version:
		1 row in set (0.00 sec)


	如果您看到Slave_IO_Running和Slave_SQL_Running均为Yes，则主从复制连接正常。


---------------------------------------------------------------

