---
layout: post
category:  Hadoop
title: Hadoop学习(二)-Hdfs
tags: Hadoop，hdfs
---

### 一，HDFS结构和基本概念
HDFS是其中一种**分布式文件管理系统**，分布式文件系统是一种允许文件通过网络在多台主机上分享的文件系统，可让多机器上的多用户分享文件和存储空间。  
hadoop中hdfs系统会有一个NameNode节点和多个DataNode节点，NameNode节点存储元数据，DataNode节点则存储实际的数据块。  
client端要访问hdfs上的文件时先要去NameNode上面请求然后在去实际存储的DataNode节点上拿数据。  
假设配置文件中配置了多个副本，那么在上传文件的时候client端会先上传一个在某个Data Node接待你，然后再由DataNode相互之间完成副本的功能。  



### 二，HDFS的shell(命令行客户端)操作
#### 1.查看帮助  

		root@Hadoop-Server:~# hadoop fs -help
		Usage: hadoop fs [generic options]
			[-appendToFile <localsrc> ... <dst>]
			[-cat [-ignoreCrc] <src> ...]
			[-checksum <src> ...]
			[-chgrp [-R] GROUP PATH...]
			[-chmod [-R] <MODE[,MODE]... | OCTALMODE> PATH...]
			[-chown [-R] [OWNER][:[GROUP]] PATH...]
			[-copyFromLocal [-f] [-p] [-l] <localsrc> ... <dst>]
			[-copyToLocal [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
			[-count [-q] [-h] <path> ...]
			[-cp [-f] [-p | -p[topax]] <src> ... <dst>]
			[-createSnapshot <snapshotDir> [<snapshotName>]]
			[-deleteSnapshot <snapshotDir> <snapshotName>]
			[-df [-h] [<path> ...]]
			[-du [-s] [-h] <path> ...]
			[-expunge]
			[-find <path> ... <expression> ...]
			[-get [-p] [-ignoreCrc] [-crc] <src> ... <localdst>]
			[-getfacl [-R] <path>]
			[-getfattr [-R] {-n name | -d} [-e en] <path>]
			[-getmerge [-nl] <src> <localdst>]
			[-help [cmd ...]]
			[-ls [-d] [-h] [-R] [<path> ...]]
			[-mkdir [-p] <path> ...]
			[-moveFromLocal <localsrc> ... <dst>]
			[-moveToLocal <src> <localdst>]
			[-mv <src> ... <dst>]
			[-put [-f] [-p] [-l] <localsrc> ... <dst>]
			[-renameSnapshot <snapshotDir> <oldName> <newName>]
			[-rm [-f] [-r|-R] [-skipTrash] <src> ...]
			[-rmdir [--ignore-fail-on-non-empty] <dir> ...]
			[-setfacl [-R] [{-b|-k} {-m|-x <acl_spec>} <path>]|[--set <acl_spec> <path>]]
			[-setfattr {-n name [-v value] | -x name} <path>]
			[-setrep [-R] [-w] <rep> <path> ...]
			[-stat [format] <path> ...]
			[-tail [-f] <file>]
			[-test -[defsz] <path>]
			[-text [-ignoreCrc] <src> ...]
			[-touchz <path> ...]
			[-truncate [-w] <length> <path> ...]
			[-usage [cmd ...]]
  
#### 2.上传文件  

		hadoop fs -put <linux上文件> <hdfs上的路径>  
		root@Hadoop-Server:/hadooptest# hadoop fs -put 2016-03-18-Hadoop_Environment.md hdfs://Hadoop-Server:9000/  
在web上也可以相应的看到上传的文件
![](http://img.blog.csdn.net/20170326234644679?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhbzQ2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)<br>


* 3.查看文件内容  
如果不加URI就默认是hdfs系统的根目录

		root@Hadoop-Server:/hadooptest# hadoop fs -cat /2016-03-18-Hadoop_Environment.md
  
#### 4.查看文件列表  

		root@Hadoop-Server:/hadooptest# hadoop fs -ls /
		Found 3 items
		-rw-r--r--   1 root supergroup       6046 2017-03-27 00:32 /2016-03-18-Hadoop_Environment.md
		drwx------   - root supergroup          0 2017-03-24 00:43 /tmp
		drwxr-xr-x   - root supergroup          0 2017-03-24 00:43 /user

#### 5.下载文件  

		root@Hadoop-Server:/hadooptest# ll
		total 16
		drwxr-xr-x  2 root root 4096 Mar 27 00:26 ./
		drwxr-xr-x 24 root root 4096 Mar 27 00:23 ../
		-rw-r--r--  1 root root 6046 Mar 27 00:17 2016-03-18-Hadoop_Environment.md
		root@Hadoop-Server:/hadooptest# rm -f 2016-03-18-Hadoop_Environment.md 
		root@Hadoop-Server:/hadooptest# ll
		total 8
		drwxr-xr-x  2 root root 4096 Mar 27 00:49 ./
		drwxr-xr-x 24 root root 4096 Mar 27 00:23 ../
		root@Hadoop-Server:/hadooptest# hadoop fs -get /2016-03-18-Hadoop_Environment.md
		root@Hadoop-Server:/hadooptest# ll
		total 16
		drwxr-xr-x  2 root root 4096 Mar 27 00:50 ./
		drwxr-xr-x 24 root root 4096 Mar 27 00:23 ../
		-rw-r--r--  1 root root 6046 Mar 27 00:50 2016-03-18-Hadoop_Environment.md
		root@Hadoop-Server:/hadooptest#   
**以上只是hdfs的几个常用命令还有更多的命令可以通过查看帮助去继续了解**  

### 三，HDFS的工作机制
#### 1.概述  
HDFS集群分为两大角色：NameNode、DataNode，NameNode负责管理整个文件系统的元数据，DataNode负责管理用户的文件数据块，文件会按照固定的大小（blocksize）切成若干块后分布式存储在若干台datanode上(可以通过配置文件配置blicksize大小)，每一个文件块可以有多个副本，并存放在**不同**的datanode上,Datanode会定期向Namenode汇报自身所保存的文件block信息,而namenode则会负责保持文件的副本数量，HDFS的内部工作机制对客户端保持透明，客户端请求访问HDFS都是通过向namenode申请来进行。  
#### 2.HDFS写数据流程  
* 1、根namenode通信请求上传文件，namenode检查目标文件是否已存在，父目录是否存在  
* 2、namenode返回是否可以上传  
* 3、client请求第一个 block该传输到哪些datanode服务器上  
* 4、namenode返回3个datanode服务器ABC  
* 5、client请求3台dn中的一台A上传数据（本质上是一个RPC调用，建立pipeline），A收到请求会继续调用B，然后B调用C，将真个pipeline建立完成，逐级返回客户端  
* 6、client开始往A上传第一个block（先从磁盘读取数据放到一个本地内存缓存），以packet为单位，A收到一个packet就会传给B，B传给C；A每传一个packet会放入一个应答队列等待应答  
* 7、当一个block传输完成之后，client再次请求namenode上传第二个block的服务器  
#### 3.HDFS读数据流程  
* 1、跟namenode通信查询元数据，找到文件块所在的datanode服务器
* 2、挑选一台datanode（就近原则，然后随机）服务器，请求建立socket流
* 3、datanode开始发送数据（从磁盘里面读取数据放入流，以packet为单位来做校验）
* 4、客户端以packet为单位接收，现在本地缓存，然后写入目标文件
#### 4.NAMENODE工作机制
* 1、存储机制  
内存中有一份完整的元数据(内存meta data)  
磁盘有一个“准完整”的元数据镜像（fsimage）文件(在namenode的工作目录中)  
用于衔接内存metadata和持久化元数据镜像fsimage之间的操作日志(edits文件)
<br>
* 2、元数据的checkpoint  
每隔一段时间，会由secondary namenode将namenode上积累的所有edits和一个最新的fsimage下载到本地，并加载到内存进行merge（这个过程称为checkpoint）
<br>
* 3、checkpoint操作的触发条件配置参数  

		dfs.namenode.checkpoint.check.period=60  #检查触发条件是否满足的频率，60秒
		dfs.namenode.checkpoint.dir=file://${hadoop.tmp.dir}/dfs/namesecondary
		#以上两个参数做checkpoint操作时，secondary namenode的本地工作目录
		dfs.namenode.checkpoint.edits.dir=${dfs.namenode.checkpoint.dir}
		dfs.namenode.checkpoint.max-retries=3  #最大重试次数
		dfs.namenode.checkpoint.period=3600  #两次checkpoint之间的时间间隔3600秒
		dfs.namenode.checkpoint.txns=1000000 #两次checkpoint之间最大的操作记录
* 4、secondary namenode附带作用  
namenode和secondary namenode的工作目录存储结构完全相同，所以，当namenode故障退出需要重新恢复时，可以从secondary namenode的工作目录中将fsimage拷贝到namenode的工作目录，以恢复namenode的元数据

#### 5.DATANODE的工作机制  

* 1、概述  
存储管理用户的文件块数据  
定期向namenode汇报自身所持有的block信息（通过心跳信息上报）  
当集群中发生某些block副本失效时，集群如何恢复block初始副本数量的问题  

		<property>
		　　　　<name>dfs.blockreport.intervalMsec</name>
		　　　　<value>3600000</value>
		　　　　<description>Determines block reporting interval in milliseconds.</description>
		</property>

* 2、Datanode掉线判断时限参数  
datanode进程死亡或者网络故障造成datanode无法与namenode通信，namenode不会立即把该节点判定为死亡，要经过一段时间，这段时间暂称作超时时长。  
HDFS默认的超时时长为10分钟+30秒。如果定义超时时间为timeout，则超时时长的计算公式为：  

		timeout  = 2 * heartbeat.recheck.interval + 10 * dfs.heartbeat.interval。
而默认的heartbeat.recheck.interval 大小为5分钟，dfs.heartbeat.interval默认为3秒。  
需要注意的是hdfs-site.xml 配置文件中的heartbeat.recheck.interval的单位为毫秒，dfs.heartbeat.interval的单位为秒。  
举个例子，如果heartbeat.recheck.interval设置为5000（毫秒），dfs.heartbeat.interval设置为3（秒，默认），则总的超时时间为40秒。

		<property>
		    <name>heartbeat.recheck.interval</name>
		    <value>2000</value>
		</property>
		<property>
		    <name>dfs.heartbeat.interval</name>
		    <value>1</value>
		</property>

### 四，HDFS的java操作  
#### 1.引入依赖（pom文件）  

	<dependency>
	    <groupId>org.apache.hadoop</groupId>
	    <artifactId>hadoop-common</artifactId>
	    <version>2.7.3</version>
	</dependency>
	<dependency>
	    <groupId>org.apache.hadoop</groupId>
	    <artifactId>hadoop-hdfs</artifactId>
	    <version>2.7.3</version>
	</dependency>
	<dependency>
	    <groupId>org.apache.hadoop</groupId>
	    <artifactId>hadoop-mapreduce-client-common</artifactId>
	    <version>2.7.3</version>
	</dependency>
	<dependency>
	    <groupId>org.apache.hadoop</groupId>
	    <artifactId>hadoop-mapreduce-client-core</artifactId>
	    <version>2.7.3</version>
	</dependency>
	<dependency>
	    <groupId>org.apache.hadoop</groupId>
	    <artifactId>hadoop-mapreduce-client-jobclient</artifactId>
	    <version>2.7.3</version>
	</dependency>

[全部文件参考地址](https://github.com/kyleNg/HadoopStudy/blob/master/HadoopStudy/pom.xml)

**注意**：一定要在Linux环境下开发，不是windows不能开发而是前期准备太麻烦。

#### 2.获取api中的客户端对象
在java中操作hdfs，首先要获得一个客户端实例：

	Configuration conf = new Configuration()
	FileSystem fs = FileSystem.get(conf)

我们的操作目标是HDFS，所以获取到的fs对象应该是*DistributedFileSystem*的实例<br>
get方法是从何处判断具体实例化那种客户端类呢？  
&ensp;&ensp;&ensp;&ensp;——从conf中的一个参数 fs.defaultFS的配置值判断；  
如果我们的代码中没有指定fs.defaultFS，并且工程classpath下也没有给定相应的配置，conf中的默认就来自于hadoop的jar包中的core-default.xml，默认值为： file:///，则获取的将不是一个DistributedFileSystem的实例，而是一个本地文件系统的客户端对象  
[HadoopAPI官方网址](https://hadoop.apache.org/docs/stable/api/)  
**注意**官方API中并没有介绍FileSystem的实例类，还需自行查看hadoop源码  
#### 3.文件的增删改查

[参照](https://github.com/kyleNg/HadoopStudy/tree/master/HadoopStudy/src/main/java/com/kyle/Hdfs)