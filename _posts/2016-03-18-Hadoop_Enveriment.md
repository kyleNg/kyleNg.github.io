---
layout: post
category:  Hadoop
title: Hadoop环境搭建
tags: Hadoop
---
## 修改主机名和IP
主机名：/etc/hostname <br>
ip（ubuntu）：/etc/network/interfaces


## 添加用户
sudo用户添加<br>
http://blog.csdn.net/scottxu_5g/article/details/11778257<br>
vim复制一行操作<br>
http://yyq123.blogspot.jp/2009/02/vim_25.html
http://blog.csdn.net/xiongzhengxiang/article/details/7206691


## 配置Hosts,IP映射关系
sudo配置 /etc/hosts

## 配置SSH免密码登陆
http://blog.csdn.net/leexide/article/details/17252369<br>
ssh-keygen -t rsa （四个回车）<br>
touch authorized_keys<br>
scp命令：http://www.cnblogs.com/peida/archive/2013/03/15/2960802.html<br>

## 安装文件
java安装，环境变量配置<br>
hadoop安装和环境变量配置

## 配置环境变量

	export HADOOP_BASE_PATH=/opt
	export JAVA_HOME=/usr/lib/jvm/java-8-oracle/
	export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
	export HADOOP_HOME=$HADOOP_BASE_PATH/hadoop-2.7.3
	export HADOOP_CONF_DIR=$HADOOP_BASE_PATH/hadoop-2.7.3/etc/hadoop
	export PATH=$PATH:$JAVA_HOME/bin:$HADOOP_HOME/bin:$HADOOP_HOME/sbin

## Hadoop主要配置文件
1,hadoop-env.sh<br>
2,core-site.xml<br>
3,hdfs-site.xml   hdfs-default.xml<br>
4,mapred-site.xml (mv mapred-site.xml.template mapred-site.xml)<br>
5,yarn-site.xml



## 验证Hadoop环境是否配置成功
	使用jps命令验证
	27408 NameNode
	28218 Jps
	27643 SecondaryNameNode
	28066 NodeManager
	27803 ResourceManager
	27512 DataNode
http://192.168.1.101:50070 （HDFS管理界面）<br>
http://192.168.1.101:8088 （MR管理界面）

