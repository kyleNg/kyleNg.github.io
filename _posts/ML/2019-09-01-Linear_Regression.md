---
layout: post
category:  Zookeeper 
title: Zookeeper（一）
tags: Zookeeper
---


$$x_1$$



## 一.Zookeeper概念简介
Zookeeper是一个分布式协调服务；就是为用户的分布式应用程序提供协调服务<br>
主要包括主从协调、服务器节点动态上下线、统一配置管理、分布式共享锁、统一名称服务……<br>
通俗的说Zookeeper主要包含两个功能：<br>
1）管理(存储，读取)用户程序提交的数据；<br>
2）为用户程序提供数据节点监听服务；

**注意**：半数机制：集群中半数以上机器存活，集群可用。
zookeeper适合装在奇数台机器上！！！

## 二.安装

本文中以三台机器为例<br>
安装前请检查机器都可以相互ssh免密登陆，确保JDK已经安装正确，安装包已经上传至服务器。<br>
[Zookeeper官网](http://zookeeper.apache.org/ "Zookeeper官网")

### 1，解压安装包 ###
	tar -zxvf zookeeper-3.4.9.tar.gz -C /opt/
	# 把解压后文件夹复制到其他两台机器
	scp -r /opt/zookeeper-3.4.9/ kyle002:/opt/
	scp -r /opt/zookeeper-3.4.9/ kyle003:/opt/

### 2，修改环境变量 ###
	sudo vim /etc/profile

在文件结尾加上下面两句环境变量配置<br>

	export ZOOKEEPER_HOME=/opt/zookeeper-3.4.9
	export PATH=$PATH:\$ZOOKEEPER_HOME/bin

![](http://img.blog.csdn.net/20170611230526583)<br>

重新加载profile文件<br>

	source /etc/profile

### 3，修改配置文件 ###

	cp /opt/zookeeper-3.4.9/conf/zoo_sample.cfg /opt/zookeeper-3.4.9/conf/zoo.cfg
	vim /opt/zookeeper-3.4.9/conf/zoo.cfg

![](http://img.blog.csdn.net/20170611230548240)
配置文件修改如下<br>

	# The number of milliseconds of each tick
	tickTime=2000
	# The number of ticks that the initial 
	# synchronization phase can take
	initLimit=10
	# The number of ticks that can pass between 
	# sending a request and getting an acknowledgement
	syncLimit=5
	# the directory where the snapshot is stored.
	# do not use /tmp for storage, /tmp here is just 
	# example sakes.
	dataDir=/home/hadoop/zookeeper/data
	dataLogDir=/home/hadoop/zookeeper/log
	# the port at which the clients will connect
	clientPort=2181
	# the maximum number of client connections.
	# increase this if you need to handle more clients
	#maxClientCnxns=60
	#
	# Be sure to read the maintenance section of the 
	# administrator guide before turning on autopurge.
	#
	# http://zookeeper.apache.org/doc/current/zookeeperAdmin.html#sc_maintenance
	#
	# The number of snapshots to retain in dataDir
	#autopurge.snapRetainCount=3
	# Purge task interval in hours
	# Set to "0" to disable auto purge feature
	#autopurge.purgeInterval=1
	server.1=kyle001:2888:3888
	server.2=kyle002:2888:3888
	server.3=kyle003:2888:3888

创建配置文件中配置的文件夹<br>

	mkdir 755 -p /home/hadoop/zookeeper/data /home/hadoop/zookeeper/log

data文件夹下新建myid，每台机器的文件内容要跟配置文件一致<br>

	# 集群中每台机器都要配置
	echo 1 > /home/hadoop/zookeeper/data/maid


### 4，测试安装结果 ###
启动（每台机器）<br>

	[hadoop@kyle001 data]$ /opt/zookeeper-3.4.9/bin/zkServer.sh start
	ZooKeeper JMX enabled by default
	Using config: /opt/zookeeper-3.4.9/bin/../conf/zoo.cfg
	Starting zookeeper ... STARTED

	# 想要在一台机器上远程启动需要注意：需要先source下资源<br>
	ssh kyle002 "source /etc/profile;/opt/zookeeper-3.4.9/bin/zkServer.sh start"

全部启动之后查看状态<br>

	[hadoop@kyle003 data]$ /opt/zookeeper-3.4.9/bin/zkServer.sh status
	ZooKeeper JMX enabled by default
	Using config: /opt/zookeeper-3.4.9/bin/../conf/zoo.cfg
	Mode: follower
注意：Mode还有可能是leader<br>
用jps查看

	[hadoop@kyle002 data]$ jps
	1588 QuorumPeerMain
	1647 Jps
QuorumPeerMain就是zookeeper任务

## 三.zookeeper结构和命令
### 1，zookeeper特性 ###
Zookeeper：一个leader，多个follower组成的集群<br>
全局数据一致：每个server保存一份相同的数据副本，client无论连接到哪个server，数据都是一致的<br>
1. 分布式读写，更新请求转发，由leader实施。<br>
2. 来自同一个client的更新请求按其发送顺序依次执行。<br>
3. 数据更新原子性，一次数据更新要么成功，要么失败。<br>
4. 实时性，在一定时间范围内，client能读到最新数据。
### 2，zookeeper数据结构 ###
层次化的目录结构，命名符合常规文件系统规范(见下图)<br>
每个节点在zookeeper中叫做znode,并且其有一个唯一的路径标识<br>
节点Znode可以包含数据和子节点（但是EPHEMERAL类型的节点不能有子节点）<br>
客户端应用可以在节点上设置监视器（后续详细讲解）<br><br>
![](http://img.blog.csdn.net/20170611204938948)
### 3，节点类型 ###
Znode有两种类型：<br>
短暂（ephemeral）（断开连接自己删除）<br>
持久（persistent）（断开连接不删除）<br>
Znode有四种形式的目录节点（默认是persistent ）:<br>
PERSISTENT<br>
PERSISTENT_SEQUENTIAL（持久序列/test0000000019 ）<br>
EPHEMERAL<br>
EPHEMERAL_SEQUENTIAL<br>
创建znode时设置顺序标识，znode名称后会附加一个值，顺序号是一个单调递增的计数器，由父节点维护<br>
在分布式系统中，顺序号可以被用于为所有的事件进行全局排序，这样客户端可以通过顺序号推断事件的顺序<br>
### 4，zookeeper命令行操作 ###

启动命令行客户端：

	[hadoop@kyle001 ~]$ /opt/zookeeper-3.4.9/bin/zkCli.sh 
	Connecting to localhost:2181
	2017-06-12 06:06:11,483 [myid:] - INFO  [main:Environment@100] - Client environment:zookeeper.version=3.4.9-1757313, built on 08/23/2016 06:50 GMT
	2017-06-12 06:06:11,488 [myid:] - INFO  [main:Environment@100] - Client environment:host.name=kyle001
	2017-06-12 06:06:11,489 [myid:] - INFO  [main:Environment@100] - Client environment:java.version=1.8.0_131
	2017-06-12 06:06:11,493 [myid:] - INFO  [main:Environment@100] - Client environment:java.vendor=Oracle Corporation
	2017-06-12 06:06:11,494 [myid:] - INFO  [main:Environment@100] - Client environment:java.home=/soft/jdk1.8.0_131/jre
	2017-06-12 06:06:11,494 [myid:] - INFO  [main:Environment@100] - Client environment:java.class.path=/opt/zookeeper-3.4.9/bin/../build/classes:/opt/zookeeper-3.4.9/bin/../build/lib/*.jar:/opt/zookeeper-3.4.9/bin/../lib/slf4j-log4j12-1.6.1.jar:/opt/zookeeper-3.4.9/bin/../lib/slf4j-api-1.6.1.jar:/opt/zookeeper-3.4.9/bin/../lib/netty-3.10.5.Final.jar:/opt/zookeeper-3.4.9/bin/../lib/log4j-1.2.16.jar:/opt/zookeeper-3.4.9/bin/../lib/jline-0.9.94.jar:/opt/zookeeper-3.4.9/bin/../zookeeper-3.4.9.jar:/opt/zookeeper-3.4.9/bin/../src/java/lib/*.jar:/opt/zookeeper-3.4.9/bin/../conf:
	2017-06-12 06:06:11,494 [myid:] - INFO  [main:Environment@100] - Client environment:java.library.path=/usr/java/packages/lib/amd64:/usr/lib64:/lib64:/lib:/usr/lib
	2017-06-12 06:06:11,494 [myid:] - INFO  [main:Environment@100] - Client environment:java.io.tmpdir=/tmp
	2017-06-12 06:06:11,494 [myid:] - INFO  [main:Environment@100] - Client environment:java.compiler=<NA>
	2017-06-12 06:06:11,494 [myid:] - INFO  [main:Environment@100] - Client environment:os.name=Linux
	2017-06-12 06:06:11,495 [myid:] - INFO  [main:Environment@100] - Client environment:os.arch=amd64
	2017-06-12 06:06:11,495 [myid:] - INFO  [main:Environment@100] - Client environment:os.version=2.6.32-696.el6.x86_64
	2017-06-12 06:06:11,495 [myid:] - INFO  [main:Environment@100] - Client environment:user.name=hadoop
	2017-06-12 06:06:11,495 [myid:] - INFO  [main:Environment@100] - Client environment:user.home=/home/hadoop
	2017-06-12 06:06:11,495 [myid:] - INFO  [main:Environment@100] - Client environment:user.dir=/home/hadoop
	2017-06-12 06:06:11,500 [myid:] - INFO  [main:ZooKeeper@438] - Initiating client connection, connectString=localhost:2181 sessionTimeout=30000 watcher=org.apache.zookeeper.ZooKeeperMain$MyWatcher@446cdf90
	Welcome to ZooKeeper!
	2017-06-12 06:06:11,597 [myid:] - INFO  [main-SendThread(localhost:2181):ClientCnxn$SendThread@1032] - Opening socket connection to server localhost/127.0.0.1:2181. Will not attempt to authenticate using SASL (unknown error)
	JLine support is enabled
	2017-06-12 06:06:11,815 [myid:] - INFO  [main-SendThread(localhost:2181):ClientCnxn$SendThread@876] - Socket connection established to localhost/127.0.0.1:2181, initiating session
	2017-06-12 06:06:11,957 [myid:] - INFO  [main-SendThread(localhost:2181):ClientCnxn$SendThread@1299] - Session establishment complete on server localhost/127.0.0.1:2181, sessionid = 0x15c992d06310000, negotiated timeout = 30000
	
	WATCHER::
	
	WatchedEvent state:SyncConnected type:None path:null
	[zk: localhost:2181(CONNECTED) 0] 

1、使用 ls 命令来查看当前 ZooKeeper 中所包含的内容：

	[zk: localhost:2181(CONNECTED) 0] ls /
	[zookeeper]

2、创建一个新的 znode ，使用 create /zk myData 。这个命令创建了一个新的 znode 节点“ zk ”以及与它关联的字符串：

	[zk: localhost:2181(CONNECTED) 1] create /zk "mydata"
	Created /zk
	[zk: localhost:2181(CONNECTED) 2] 

**注意**：create -e /zk "mydata"就会创建一个短暂的节点（默认创建的是持久节点），在客户端quite的时候一起消失。

3、运行 get 命令来确认 znode 的字符串：

	[zk: localhost:2181(CONNECTED) 2] get /zk
	mydata
	cZxid = 0x400000002
	ctime = Mon Jun 12 06:15:09 CST 2017
	mZxid = 0x400000002
	mtime = Mon Jun 12 06:15:09 CST 2017
	pZxid = 0x400000002
	cversion = 0
	dataVersion = 0
	aclVersion = 0
	ephemeralOwner = 0x0
	dataLength = 6
	numChildren = 0
	[zk: localhost:2181(CONNECTED) 3] 

4、监听某个节点的变化：

**注意**：监听这个节点的客户端会出现提示信息，这种监听只能监听一次变化

	[zk: localhost:2181(CONNECTED) 3] get /zk watch
	mydata
	cZxid = 0x400000002
	ctime = Mon Jun 12 06:15:09 CST 2017
	mZxid = 0x400000002
	mtime = Mon Jun 12 06:15:09 CST 2017
	pZxid = 0x400000002
	cversion = 0
	dataVersion = 0
	aclVersion = 0
	ephemeralOwner = 0x0
	dataLength = 6
	numChildren = 0

5、通过 set 命令来对 zk 所关联的字符串进行设置：

	[zk: localhost:2181(CONNECTED) 1] set /zk "UpdateData"
	cZxid = 0x400000002
	ctime = Mon Jun 12 06:15:09 CST 2017
	mZxid = 0x400000004
	mtime = Mon Jun 12 06:25:48 CST 2017
	pZxid = 0x400000002
	cversion = 0
	dataVersion = 1
	aclVersion = 0
	ephemeralOwner = 0x0
	dataLength = 10
	numChildren = 0
	[zk: localhost:2181(CONNECTED) 2] 

如果在4步设置监听之后再设置节点信息监听事件就会起作用，如下：

	[zk: localhost:2181(CONNECTED) 4] 
	WATCHER::
	WatchedEvent state:SyncConnected type:NodeDataChanged path:/zk

6、删除节点<br>

	[zk: localhost:2181(CONNECTED) 6] delete /zk
	[zk: localhost:2181(CONNECTED) 7] ls /
	[zookeeper]
	[zk: localhost:2181(CONNECTED) 8]
 
7、递归删除节点：rmr<br>

	[zk: localhost:2181(CONNECTED) 11] ls /
	[zookeeper, test]
	[zk: localhost:2181(CONNECTED) 12] ls /test
	[haha]
	[zk: localhost:2181(CONNECTED) 13] rmr /test
	[zk: localhost:2181(CONNECTED) 14] ls /
	[zookeeper]
	[zk: localhost:2181(CONNECTED) 15]