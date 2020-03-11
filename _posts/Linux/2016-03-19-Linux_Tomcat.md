---
layout: post
category:  Linux 
title: LinuxTomcat安装与部署
tags: Linux
---
## 一. 安装Tomcat

**1、安装配置jdk**

tomcat运行需要jdk支持，所以前提要安装且配置好jdk

**2、安装tomcat**

下载[tomcat](http://tomcat.apache.org/)安装包（本文以9.0.31为例，版本不同配置有所不同请参照官方文档）
将安装包上传到Linux服务器中/usr/local/文件夹下

	1.解压tomcat压缩包

		cd /usr/local/
		tar -zxvf apache-tomcat-9.0.31.tar.gz

	2.配置环境变量

		vim /etc/profile

		# 在文件最后加上以下配置
		# tomcat evn
		CATALINA_HOME=/usr/local/src/apache-tomcat-9.0.31
		export CATALINA_HOME

		# 保存退出后执行下面的命令,使其生效
		source /etc/profile

	3.配置tomcat的catalina.sh文件

		# 进入tomcat的bin目录
		cd $CATALINA_HOME/bin
		vim catalina.sh

		# 找到 # OS specific support，然后在这行下面添加以下配置
		# OS specific support.  $var _must_ be set to either true or false.
		CATALINA_HOME=/usr/local/src/apache-tomcat-9.0.31
		JAVA_HOME=/usr/local/src/jdk1.8.0_201
		# 保存退出

	4.安装tomcat服务

		cd $CATALINA_HOME/bin
		cp catalina.sh /etc/init.d/tomcat

	5.测试tomcat启动和停用

		# 启动
		service tomcat start
		# 停用
		service tomcat stop

		# 浏览器访问tomcat主页如果没有报错的话，证明已经配置成功了

## 二. 配置Tomcat开机自启

设置tomcat开机自启通常有两种方式，第一种修改/etc/rc.d/rc.local文件并赋予执行权限，第二种就是添加启动脚本添加tomcat服务。
这里我们主要针对第二种方式做说明，其他类似系统设置方法也可以参照例如Ngix。

**1、添加自启脚本**

上面安装tomcat的时候已经修改过catalina.sh文件并且添加到了/etc/init.d/tomcat文件中。
此步骤是在这基础上修改的。

	修改启动脚本
		vim /etc/init.d/tomcat
	在文件头部#!/bin/sh下方添加以下代码
		# chkconfig: 2345 10 90
		# description: Tomcat Service

上面这两行注释一定要加，第一行# chkconfig是服务的配置第一个数字是服务的运行级别；

	等级0表示：表示关机
	等级1表示：单用户模式
	等级2表示：无网络连接的多用户命令行模式
	等级3表示：有网络连接的多用户命令行模式
	等级4表示：不可用
	等级5表示：带图形界面的多用户模式
	等级6表示：重新启动

第二个参数是启动的优先级，整数值0-99；<br>
第三个参数是停止的优先级，整数值0-99；<br>
第二行# description是对服务的描述。

**2、给脚本添加执行权限**

	chmod +x /etc/init.d/tomcat

**3、添加开机自启服务**

	chkconfig --add tomcat

[chkconfig命令的详细解释参照这个博客](https://www.cnblogs.com/machangwei-8/p/10388557.html)

	# 检查一下服务添加结果
	chkconfig --list | grep tomcat
	# 查看服务状态
	systemctl status tomcat

## 三. Tomcat Manager配置访问主机及添加用户

**1. 配置可访问主机**

在Tomcat安装目录下，进入conf/Catalina/localhost
新建manager.xml, 将下将内容复制粘贴到其中：

	<Context privileged="true" antiResourceLocking="false"
	         docBase="${catalina.home}/webapps/manager">
	  <Valve className="org.apache.catalina.valves.RemoteAddrValve"
	         allow="10\.0\.0\.11" />
	</Context>

其中allow后面的IP地址就是允许访问manager的主机的IP地址；\必须保留















