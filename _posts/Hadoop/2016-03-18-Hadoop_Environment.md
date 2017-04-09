---
layout: post
category:  Hadoop
title: Hadoop学习(一)-伪分布式环境搭建
tags: Hadoop
---

本博客前期准备工作需要有一台Linux虚拟机，本文中以ubuntu为例。

### 网络配置
**1,主机名：**<br>

	vim /etc/hostname

![](http://img.blog.csdn.net/20170324220817007)

**2,修改IP:**<br>

	vim /etc/network/interfaces

![](http://img.blog.csdn.net/20170324221332450)

**3,域名映射**<br>

	vim /etc/hosts  集群中的主机域名映射表

![](http://img.blog.csdn.net/20170324214214279)

### 添加用户

sudo用户添加<br>
http://blog.csdn.net/scottxu_5g/article/details/11778257<br>
vim复制一行操作<br>
http://yyq123.blogspot.jp/2009/02/vim_25.html
http://blog.csdn.net/xiongzhengxiang/article/details/7206691

### 配置SSH免密码登陆
http://blog.csdn.net/leexide/article/details/17252369<br>
ssh-keygen -t rsa （四个回车）<br>
touch authorized_keys<br>
scp命令：http://www.cnblogs.com/peida/archive/2013/03/15/2960802.html<br>

### 安装文件
java安装，环境变量配置<br>
hadoop安装和环境变量配置<br>

### Hadoop主要目录介绍
1,hadoop主目录下<br>

	bin
	etc  --> hadoop的各种配置文件
	include
	lib
	libexec
	LICENSE.txt
	logs
	NOTICE.txt
	README.txt
	sbin  --> hadoop启动停止等运行脚本
	share
	  --doc  -->hadoop的文档
	  --hadoop -->jar包存放位置
	      --common
	      --hdfs
	      --httpfs
	      --kms
	      --mapreduce
	      --tools
	      --yarn

### 配置环境变量

	export HADOOP_BASE_PATH=/opt
	export JAVA_HOME=/usr/lib/jvm/java-8-oracle/
	export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
	export HADOOP_HOME=$HADOOP_BASE_PATH/hadoop-2.7.3
	export HADOOP_CONF_DIR=$HADOOP_BASE_PATH/hadoop-2.7.3/etc/hadoop
	export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

### Hadoop主要配置文件
**1,hadoop-env.sh<br>**

	#The java implementation to use.
	export JAVA_HOME=${JAVA_HOME}

**2,core-site.xml<br>**

	<configuration>

		<!-- hadoop公共目录 -->
		<property>
		    <name>hadoop.tmp.dir</name>
		    <value>file:/hadoop</value>
		    <description>A base for other temporary directories.</description>
		</property>

		<!-- 文件系统 -->
		<property>
		    <name>fs.defaultFS</name>
		    <value>hdfs://Hadoop-Server:9000</value>
		</property>

		<!-- file system properties -->
		<property>
		    <name>fs.default.name</name>
		    <value>hdfs://Hadoop-Server:9000</value>
		</property>
		<property>
		    <name>io.file.buffer.size</name>
		    <value>131072</value>
		</property>
		<property>
		    <name>hadoop.proxyuser.hduser.hosts</name>
		    <value>*</value>
		</property>
		<property>
		    <name>hadoop.proxyuser.hduser.groups</name>
		    <value>*</value>
		</property>
	</configuration>


**3,hdfs-site.xml   hdfs-default.xml<br>**

	<configuration>
	    <property>
	        <name>dfs.namenode.secondary.http-address</name>
	        <value>Hadoop-Server:9001</value>
	    </property>
	    <property>
	        <name>dfs.namenode.name.dir</name>
	        <value>file:/hadoop/name</value>
	    </property>
	    <property>
	        <name>dfs.namenode.data.dir</name>
	        <value>file:/hadoop/data</value>
	    </property>

		<!-- hsfs的副本数 -->
	    <property>
	        <name>dfs.replication</name>
	        <value>1</value>
	    </property>
	    <property>
	        <name>dfs.webhdfs.enabled</name>
	        <value>true</value>
	    </property>
	    <property>
	        <name>dfs.permissions.enabled</name>
	        <value>false</value>
	    </property>
	</configuration>

**4,mapred-site.xml (mv mapred-site.xml.template mapred-site.xml)<br>**

	<configuration>
	    <property>
	        <name>mapreduce.framework.name</name>
	        <value>yarn</value>
	    </property>
	    <property>
	        <name>mapreduce.jobhistory.address</name>
	        <value>Hadoop-Server:10020</value>
	    </property>
	    <property>
	        <name>mapreduce.jobhistory.webapp.address</name>
	        <value>Hadoop-Server:19888</value>
	    </property>
	</configuration>

**5,yarn-site.xml**

	<configuration>
		<!-- Site specific YARN configuration properties -->
	    <property>
	        <name>yarn.nodemanager.aux-services</name>
	        <value>mapreduce_shuffle</value>
	    </property>
	    <property>
	        <name>yarn.nodemanager.aux-services.mapreduce.shuffle.class</name>
	        <value>org.apache.hadoop.mapred.ShuffleHandler</value>
	    </property>
	    <property>
	        <name>yarn.resourcemanager.address</name>
	        <value>Hadoop-Server:8032</value>
	    </property>
	    <property>
	        <name>yarn.resourcemanager.scheduler.address</name>
	        <value>Hadoop-Server:8030</value>
	    </property>
	    <property>
	        <name>yarn.resourcemanager.resource-tracker.address</name>
	        <value>Hadoop-Server:8031</value>
	    </property>
	    <property>
	        <name>yarn.resourcemanager.admin.address</name>
	        <value>Hadoop-Server:8033</value>
	    </property>
	    <property>
	        <name>yarn.resourcemanager.webapp.address</name>
	        <value>Hadoop-Server:8088</value>
	    </property>
	</configuration>

### 启动hdfs和yarn

**1. 格式化hdfs<br>**

	hdfs namenode -format
**2. hadoop执行脚本一览**

![](http://img.blog.csdn.net/20170324222008016)<br>

**3. 启动hdfs<br>**

	start-dfs.sh
![](http://img.blog.csdn.net/20170324222752017)<br>

**4. 启动yarn<br>**

	start-yarn.sh
![](http://img.blog.csdn.net/20170324222817674)<br>

**5. 使用jps命令验证<br>**

![](http://img.blog.csdn.net/20170324222912786)<br>

http://192.168.0.234:50070 (HDFS管理界面)<br>

![](http://img.blog.csdn.net/20170324223023223)<br>

http://192.168.0.234:8088 (MR管理界面)<br>

![](http://img.blog.csdn.net/20170324223050302)<br>

### 用hdfs和mapreduce运行word count例子