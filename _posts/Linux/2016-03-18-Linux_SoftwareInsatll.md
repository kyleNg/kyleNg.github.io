---
layout: post
category:  Linux 
title: Linux常用软件安装
tags: Linux
---
## 一. Linux系统软件安装方式

**1、二进制发布包**

软件已经针对具体平台编译打包发布，只要解压，修改配置即可

**2、RPM发布包**

软件已经按照redhat的包管理工具规范RPM进行打包发布，需要获取到相应的软件RPM发布包，然后用RPM命令进行安装

**3、Yum在线安装**

软件已经以RPM规范打包，但发布在了网络上的一些服务器上，可用yum在线安装服务器上存在的rpm软件，并且会自动解决软件安装过程中的库依赖问题
（注：类似于maven）

**4、源码编译安装**

软件以源码工程的形式发布，需要获取到源码工程后用相应开发工具进行编译打包部署

## 二. JAVA软件安装——JDK安装

**1、上传二进制JDK**

各种软件都可以完成此操作例如sftp、WinSCP.....

**2、解压jdk压缩包**

	[hadoop@centos ~]$ tar -zxvf jdk-8u201-linux-x64.tar.gz -C /usr/local/

**3、修改环境变量PATH**

	vi /etc/profile

在文件最后加两行：

	export JAVA_HOME=/usr/local/jdk1.8.0_201
	export PATH=$PATH:$JAVA_HOME/bin

**4、让环境变量生效**

	source /etc/profile