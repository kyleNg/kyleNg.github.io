---
layout: post
category:  Linux 
title: Linux crontab （定时任务）
tags: Linux
---

## 1.命令功能

通过crontab 命令，我们可以在固定的间隔时间执行指定的系统指令或 shell script脚本。时间间隔的单位可以是分钟、小时、日、月、周及以上的任意组合。这个命令非常适合周期性的日志分析或数据备份等工作。

## 2.安装crontab

	yum install crontabs

	服务操作说明：
	service crond start  	## 启动服务
	service crond stop  	## 关闭服务
	service crond restart	## 重启服务
	service crond reload  	## 重新载入配置

	## 查看crontab服务状态：
	service crond status
	
	## 手动启动crontab服务：
	service crond start
	
	## 查看crontab服务是否已设置为开机启动，执行命令：
	chkconfig --list
	
	## 加入开机自动启动：
	chkconfig  --level 35 crond on


## 3.命令格式

	crontab [-u user] file
	crontab [-u user] [ -e | -l | -r ]
	
	参数说明：
	-u user：用来设定某个用户的crontab服务，例如，“-u ixdba”表示设定ixdba用户的crontab服务，此参数一般有root用户来运行。
	file：file是命令文件的名字,表示将file做为crontab的任务列表文件并载入crontab。
	-e：编辑某个用户的crontab文件内容。如果不指定用户，则表示编辑当前用户的crontab文件。
	-l：显示某个用户的crontab文件内容，如果不指定用户，则表示显示当前用户的crontab文件内容。
	-r：删除定时任务配置，从/var/spool/cron目录中删除某个用户的crontab文件，如果不指定用户，则默认删除当前用户的crontab文件。
	-i：在删除用户的crontab文件时给确认提示。
	
	命令示例：
	crontab file [-u user]	## 用指定的文件替代目前的crontab。 
	
	必掌握：
	crontab -l [-u user]		## 列出用户目前的crontab. 
	crontab -e [-u user]		## 编辑用户目前的crontab.



将公钥发给要免密码登陆的目标机

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
