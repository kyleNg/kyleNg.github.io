---
layout: post
category:  Linux 
title: Linux 使用ssh-keygen设置SSH无密码登录
tags: Linux
---

## 1.生成ssh免登陆密钥
执行下面命令：

	[root@Server ~]# ssh-keygen -t rsa 

输入后，会提示创建.ssh/id_rsa、id_rsa.pub的文件，其中第一个为密钥，第二个为公钥。过程中会要求输入密码，为了ssh访问过程无须密码，可以直接回车 。

## 2.进入到我的home目录查看钥匙
执行下面命令：

	[root@Server ~]# ls -l .ssh  
	-rw------- 1 root root 1679 Mar  4 18:28 id_rsa  
	-rw-r--r-- 1 root root  393 Mar  4 18:28 id_rsa.pub  
可以发现 ssh目录下的两枚钥匙

## 3.将公钥复制到被管理机器下的任意目录下
将公钥发给要面密码登陆的目标机

	[root@Server .ssh]# scp id_rsa.pub root@Server_target:/root

到Server_target下的root文件夹下执行下面命令

	[root@Server_target ~]# cat id_dsa.pub >> ~/.ssh/authorized_keys

## 4.设置文件和目录权限： 
设置authorized_keys权限

	chmod 600 authorized_keys

设置.ssh目录权限

	chmod 700 -R .ssh

## 5.验证使用SSH免密码登陆
在Server机用ssh免密码登陆：

	[root@Server ~]# ssh root@Server_target

注意：用主机名的方式必须在/etc/hosts中配置ip解析，否则可以将Server_target改成ip
