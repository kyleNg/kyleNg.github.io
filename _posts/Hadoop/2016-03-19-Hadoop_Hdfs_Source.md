---
layout: post
category:  Hadoop
title: Hadoop学习(二)-Hdfs架构
tags: Hadoop，hdfs
---

### HDFS结构和基本概念
HDFS是其中一种分布式文件管理系统，分布式文件系统是一种允许文件通过网络在多台主机上分享的文件系统，可让多机器上的多用户分享文件和存储空间。  
hadoop中hdfs系统会有一个NameNode节点和多个DataNode节点，NameNode节点存储元数据，DataNode节点则存储实际的数据块。  
client端要访问hdfs上的文件时先要去NameNode上面请求然后在去实际存储的DataNode节点上拿数据。  
假设配置文件中配置了多个副本，那么在上传文件的时候client端会先上传一个在某个Data Node接待你，然后再由DataNode相互之间完成副本的功能。


### HDFS shell
* 1.查看帮助

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
  
* 2.上传文件

		hadoop fs -put <linux上文件> <hdfs上的路径>  
		root@Hadoop-Server:/hadooptest# hadoop fs -put 2016-03-18-Hadoop_Environment.md hdfs://Hadoop-Server:9000/  
在web上也可以相应的看到上传的文件
![](http://img.blog.csdn.net/20170326234644679?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhbzQ2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)<br>


* 3.查看文件内容<br>
如果不加URI就默认是hdfs系统的根目录

		root@Hadoop-Server:/hadooptest# hadoop fs -cat /2016-03-18-Hadoop_Environment.md
  
* 4.查看文件列表

		root@Hadoop-Server:/hadooptest# hadoop fs -ls /
		Found 3 items
		-rw-r--r--   1 root supergroup       6046 2017-03-27 00:32 /2016-03-18-Hadoop_Environment.md
		drwx------   - root supergroup          0 2017-03-24 00:43 /tmp
		drwxr-xr-x   - root supergroup          0 2017-03-24 00:43 /user

* 5.下载文件

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

### 代码实现

### RPC协议
* 1.
