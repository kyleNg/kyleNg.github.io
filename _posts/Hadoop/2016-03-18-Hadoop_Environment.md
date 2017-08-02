---
layout: post
category:  Hadoop
title: Hadoop-伪分布式环境搭建
tags: Hadoop
---

本博客前期准备工作需要有一台Linux虚拟机，本文中以ubuntu为例。

### 网络配置
**1,主机名：**<br>

	vim /etc/hostname

	Hadoop Server

**2,修改IP:**<br>

	vim /etc/network/interfaces

![](http://img.blog.csdn.net/20170324221332450)

**3,域名映射**<br>

	vim /etc/hosts  集群中的主机域名映射表

![](http://img.blog.csdn.net/20170324214214279)

### 添加用户并赋予sudo权限

[参考另一篇博客](https://kyleng.github.io/linux/Linux_addUser_and_addSudo)

### 配置SSH免密码登陆

[参考另一篇博客](https://kyleng.github.io/linux/Linux_SSHLogin_NoPassword)


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

### Hadoop主要配置文件（这里提供最低配置）
**1,hadoop-env.sh<br>**

	#The java implementation to use.
	export JAVA_HOME=/usr/lib/jvm/java-8-oracle
	这里修改/usr/lib/jvm/java-8-oracle/为自己本地JavaHome

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
	</configuration>


**3,hdfs-site.xml   hdfs-default.xml<br>**

	<configuration>
	    <property>
	        <name>dfs.namenode.secondary.http-address</name>
	        <value>Hadoop-Server:9001</value>
	    </property>
	    <!-- NameNode元数据存放目录，可以配置多个以便HA(高可用) -->
	    <property>
	        <name>dfs.namenode.name.dir</name>
	        <value>file:/hadoop/name</value>
	    </property>
	    <!-- DataNode存放数据信息的目录 -->
	    <property>
	        <name>dfs.namenode.data.dir</name>
	        <value>file:/hadoop/data</value>
	    </property>

		<!-- hsfs的副本数 -->
	    <property>
	        <name>dfs.replication</name>
	        <value>1</value>
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
主要对HDSFS系统的初始化

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

如果成功看见以上两个页面那么一个伪分布式的Hadoop系统就已经安装成功了。