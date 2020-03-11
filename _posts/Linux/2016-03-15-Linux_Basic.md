---
layout: post
category:  Linux 
title: Linux(CentOS) 基础学习
tags: Linux
---
## 一.基本日常操作命令

1. **查看当前所在的工作目录的全路径 pwd**

		[root@localhost ~]# pwd
		/root

	**Linux常用工作路径介绍**

	/：根目录，一般根目录下只存放目录，在 linux 下有且只有一个根目录，所有的东西都是从这里开始 当在终端里输入/home ，其实是在告诉电脑，先从 / （根目录）开始，再进入到 home 目录

	/bin、/usr/bin：可执行二进制文件的目录，如常用的命令 ls、tar、mv、cat 等

	/boot：放置 linux 系统启动时用到的一些文件，如 linux 的内核文件： /boot/vmlinuz ，系统引导管理 器： /boot/grubcat /etc/issue

	/dev：存放linux系统下的设备文件，访问该目录下某个文件，相当于访问某个设备，常用的是挂载光驱 mount /dev/cdrom /mnt

	/etc：系统配置文件存放的目录，不建议在此目录下存放可执行文件，重要的配置文件有

		/etc/inittab
		/etc/fstab
		/etc/init.d
		/etc/X11
		/etc/sysconfig
		/etc/xinetd.d
	/home：系统默认的用户家目录，新增用户账号时，用户的家目录都存放在此目录下

	~ 表示当前用户的家目录
	~edu 表示用户 edu 的家目录
	/lib、/usr/lib、/usr/local/lib：系统使用的函数库的目录，程序在执行过程中，需要调用一些额外的参数时需要 函数库的协助

	/lost+fount：系统异常产生错误时，会将一些遗失的片段放置于此目录下

	/mnt: /media：光盘默认挂载点，通常光盘挂载于 /mnt/cdrom 下，也不一定，可以选择任意位置进行挂载

	/opt：给主机额外安装软件所摆放的目录
	
	/proc：此目录的数据都在内存中，如系统核心，外部设备，网络状态，由于数据都存放于内存中，所以不占 用磁盘空间，比较重要的文件有： -- /proc/cpuinfo -- /proc/interrupts -- /proc/dma -- /proc/ioports -- /proc/net/* 等

	/root：系统管理员root的家目录

	/sbin、/usr/sbin、/usr/local/sbin：放置系统管理员使用的可执行命令，如 fdisk、shutdown、mount 等。与

	/bin 不同的是，这几个目录是给系统管理员 root 使用的命令，一般用户只能"查看"而不能设置和使用

	/tmp：一般用户或正在执行的程序临时存放文件的目录，任何人都可以访问，重要数据不可放置在此目录下

	/srv：服务启动之后需要访问的数据目录，如 www 服务需要访问的网页数据存放在 /srv/www 内

	/usr：应用程序存放目录

		/usr/bin：存放应用程序
		/usr/share：存放共享数据
		/usr/lib：存放不能直接运行的，却是许多程序运行所必需的一些函数库文件
		/usr/local：存放软件升级包
		/usr/share/doc：系统说明文件存放目录
		/usr/share/man：程序说明文件存放目录

	/var：放置系统执行过程中经常变化的文件

		/var/log：随时更改的日志文件
		/var/spool/mail：邮件存放的目录
		/var/run：程序或服务启动后，其 PID 存放在该目录下 

2. **查看当前系统的时间 date**

		[root@localhost ~]# date +%Y-%m-%d
		2019-01-18
		
		[root@localhost ~]# date +%Y-%m-%d  --date="-1 day"  #加减也可以 month | year
		2019-01-17

		设置时间
		date -s "2016-05-23 01:01"      ## 修改时间
		修改时间后，需要写入硬件bios才能在重启之后依然生效
		hwclock -w

3. **查看有谁在线（哪些人登陆到了服务器）**

	who  查看当前在线

		[root@localhost ~]# who
		root     pts/0        2019-01-18 07:20 (192.168.229.1)

	last 查看最近的登陆历史记录

		[root@localhost ~]# last -3
		root     pts/0        192.168.229.1    Fri Jan 18 07:20   still logged in   
		reboot   system boot  2.6.32-696.el6.x Fri Jan 18 07:20 - 07:27  (00:06)    
		root     pts/0        192.168.229.1    Thu Jan 17 08:20 - down   (01:42)    
		
		wtmp begins Wed Dec 19 08:22:35 2018

4. **关机/重启**

	关机（必须用root用户）

	shutdown -h now  # 立刻关机<br>
	shutdown -h +10  #  10分钟以后关机<br>
	shutdown -h 12:00:00  #12点整的时候关机<br>
	halt   #  等于立刻关机

	重启

	shutdown -r now
	reboot   # 等于立刻重启

5. **清屏**

	clear    ## 或者用快捷键  ctrl + l

6. **退出当前进程**

	ctrl+c   有些程序也可以用q键退出

7. **退出当前进程**

	ctrl+z   ## 进程会挂起到后台<br>
	bg jobid  ## 让进程在后台继续执行<br>
	fg jobid   ## 让进程回到前台

8. **echo 相当于java中System.out.println(userName)**

		[root@localhost ~]# a="helloword"
		[root@localhost ~]# echo $a
		helloword
		[root@localhost ~]# 


## 二.目录操作

1. **查看目录信息**

	ls /     ## 查看根目录下的子节点（文件夹和文件）信息<br>
	ls  -al  ##  -a是显示隐藏文件   -l是以更详细的列表形式显示<br>
	ls -l  有一个别名： ll    可以直接使用ll  <是两个L>
	ll -ha  ## -h文件大小单位显示
	ll -R 查看文件夹结构

2. **切换工作目录**

	cd  /home/hadoop    ## 切换到用户主目录<br>
	cd ~     ## 切换到用户主目录<br>
	cd  什么路径都不带，则回到用户的主目录<br>
	cd -     ##  回退到上次所在的目录

3. **创建文件夹**

	mkdir aaa     ## 这是相对路径的写法<br>
	mkdir  /data    ## 这是绝对路径的写法 <br>
	mkdir -p  aaa/bbb/ccc   ## 级联创建目录

4. **删除文件夹**

	rmdir  aaa   ## 可以删除空目录<br>
	rm  -r  aaa   ## 可以把aaa整个文件夹及其中的所有子节点全部删除<br>
	rm  -rf  aaa   ## 强制删除aaa

5. **修改文件夹名称**

	mv  aaa  angelababy<br>
	mv本质上是移动<br>
	mv  install.log  aaa/  将当前目录下的install.log 移动到aaa文件夹中去<br>

	rename 可以用来批量更改文件名

## 三.文件操作

1. **创建文件**
2. **文本编辑器**
3. **拷贝/删除/移动**

	
	
4. **查看文件内容**

	cat    somefile    一次性将文件内容全部输出（控制台）

	分页查看文件的命令：

	more   somefile     可以翻页查看, 下翻一页(空格)    上翻一页（b）   退出（q）<br>
	less   somefile     可以翻页查看,下翻一页(空格)     上翻一页（b），上翻一行(↑)  下翻一行（↓）  可以搜索关键字（/keyword）<br>
	跳到文件末尾： G<br>
	跳到文件首行： gg<br>
	退出less ：  q<br>
	
	
	tail -10  install.log  查看文件尾部的10行<br>
	tail +10  install.log  查看文件 10-->末行<br>
	tail -f install.log    小f跟踪文件的唯一inode号，就算文件改名后，还是跟踪原来这个inode表示的文件<br>
	tail -F install.log    大F按照文件名来跟踪<br>
	
	head  -10  install.log   查看文件头部的10行

5. **打包压缩**

## 四.查找命令


## 五.基本的用户管理

1. **添加一个用户**

		1、useradd spark
		2、passwd spark     根据提示设置密码；

		添加一个tom用户，设置它属于users组，并添加注释信息
		分步完成：useradd tom
		usermod -g users tom
		usermod -c "hr tom" tom

		一步完成：useradd -g users -c "hr tom" tom
		
		设置tom用户的密码
		passwd tom

2. **删除一个用户**

		userdel -r spark     加一个-r就表示把用户及用户的主目录都删除

3. **修改用户**

		修改tom用户的登陆名为tomcat
		usermod -l tomcat tom
		
		将tomcat添加到sys和root组中
		usermod -G sys,root tomcat
		查看tomcat的组信息
		groups tomcat

4. **用户组操作**

		添加一个叫america的组
		groupadd america
		
		将jerry添加到america组中
		usermod -g america jerry
		
		将tomcat用户从root组和sys组删除
		gpasswd -d tomcat root
		gpasswd -d tomcat sys
		
		将america组名修改为am
		groupmod -n am america

5. **为用户配置sudo权限**

		用root编辑 vi /etc/sudoers
		在文件的如下位置，为hadoop添加一行即可
		root    ALL=(ALL)       ALL     
		hadoop  ALL=(ALL)       ALL
		
		然后，hadoop用户就可以用sudo来执行系统级别的指令
		[hadoop@shizhan ~]$ sudo useradd test

## 六.系统管理操作

1. **挂载外部存储设备**

	可以挂载光盘、硬盘、磁带、光盘镜像文件等<br>
	1/ 挂载光驱<br>

		mkdir   /mnt/cdrom      创建一个目录，用来挂载
		mount -t iso9660 -o ro /dev/cdrom /mnt/cdrom/     将设备/dev/cdrom挂载到挂载点 ： /mnt/cdrom中
	
	2/ 挂载光盘镜像文件（.iso文件）

		mount -t iso9660 -o loop  /home/hadoop/Centos-6.7.DVD.iso /mnt/centos

	注：挂载的资源在重启后即失效，需要重新挂载。要想自动挂载，可以将挂载信息设置到/etc/fstab配置文件中，如下：

		/dev/cdrom              /mnt/cdrom              iso9660 defaults        0 0
		/root/CentOS-6.7-x86_64-bin-DVD1.iso    /mnt/centos     iso9660 defaults,ro,loop        0 0

	3/ 卸载 umount<br>

		umount /mnt/cdrom
	
	4/ 存储空间查看<br>

		df -h

2. **统计文件或文件夹的大小**

		du -sh  /mnt/cdrom/packages   ## 统计指定路径下的所有子目录和文件的大小
		df -h    查看磁盘的剩余空间

3. **系统服务管理**

	service --status-all    # 查看系统所有的后台服务进程<br>
	service sshd status     # 查看指定的后台服务进程的状态<br>
	service sshd stop <br>
	service sshd start<br>
	service sshd restart<br>

	配置后台服务进程的开机自启<br>
	chkconfig httpd on     # 让httpd服务开机自启<br>
	chkconfig httpd off    # 让httpd服务开机不要自启<br>

		[root@localhost ~]# chkconfig httpd off
		[root@localhost ~]# chkconfig --list | grep httpd
		httpd           0:off   1:off   2:off   3:off   4:off   5:off   6:off
		[root@localhost ~]# chkconfig --level 35 httpd on
		[root@localhost ~]# chkconfig --list | grep httpd
		httpd           0:off   1:off   2:off   3:on    4:off   5:on    6:off
4. **系统启动级别管理**

	通常将默认启动级别设置为：3

		[hadoop@localhost ~]$ vi  /etc/inittab
		
		# inittab is only used by upstart for the default runlevel.
		#
		# ADDING OTHER CONFIGURATION HERE WILL HAVE NO EFFECT ON YOUR SYSTEM.
		#
		# System initialization is started by /etc/init/rcS.conf
		#
		# Individual runlevels are started by /etc/init/rc.conf
		#
		# Ctrl-Alt-Delete is handled by /etc/init/control-alt-delete.conf
		#
		# Terminal gettys are handled by /etc/init/tty.conf and /etc/init/serial.conf,
		# with configuration in /etc/sysconfig/init.
		#
		# For information on how to write upstart event handlers, or how
		# upstart works, see init(5), init(8), and initctl(8).
		#
		# Default runlevel. The runlevels used are:
		#   0 - halt (Do NOT set initdefault to this)
		#   1 - Single user mode
		#   2 - Multiuser, without NFS (The same as 3, if you do not have networking)
		#   3 - Full multiuser mode
		#   4 - unused
		#   5 - X11
		#   6 - reboot (Do NOT set initdefault to this)
		#
		id:3:initdefault:

5. **进程管理**

		top
		free
		ps -ef | grep ssh
		kill -9 2358     ## 将指定进程号的进程杀死

	注意：grep搜索关键词的时候会把自己也搜索出来，对比以下两种写法 

		[root@localhost ~]# ps -ef | grep sixunhuan
		root       2857   2465 30 02:41 pts/0    00:00:07 sh sixunhuan.sh
		root       2874   2858  0 02:42 pts/1    00:00:00 grep sixunhuan
		[root@localhost ~]# ps -ef | grep sixunhuan | grep -v grep
		root       2857   2465 34 02:41 pts/0    00:00:25 sh sixunhuan.sh
		[root@localhost ~]# kill -9 2857

## 七.SSH免密登陆配置

	参见其他博客

## 八.网络管理

1. **主机名配置**

	1/ 查看主机名<br>
	hostname<br>
	2/ 修改主机名(重启后无效)<br>
	hostname hadoop<br>
	3/ 修改主机名(重启后永久生效)<br>
	vi /ect/sysconfig/network<br>

2. I**P地址配置**

	修改IP地址<br>
	1/ 方式一：setup<br>
	用root输入setup命令，进入交互式修改界面<br>
	
	2/ 方式二：修改配置文件(重启后永久生效)<br>
	
		[hadoop@localhost ~]$ vi /etc/sysconfig/network-scripts/ifcfg-eth0 
		DEVICE=eth0  #描述网卡对应的设备别名
		HWADDR=00:0C:29:34:95:C5  #对应的网卡物理地址
		TYPE=Ethernet
		UUID=0c2a753e-7d2c-4512-9a17-12f91dc22bfa
		ONBOOT=yes  #系统启动时是否设置此网络接口，设置为yes时，系统启动时激活此设备
		NM_CONTROLLED=yes
		BOOTPROTO=static  #设置网卡获得ip地址的方式，选项可以为为static，dhcp或bootp
		IPADDR=192.168.229.100  #只有网卡设置成static时，才需要此字段
		NETMASK=255.255.255.0  #网卡对应的网络掩码
		# 下面两项可以设置全局也可以在每块往卡里单独设置
		GATEWAY=192.168.229.2
		DNS1=192.168.229.2

	单独设置网关
	
		route add default gw 192.168.1.1 dev eth0
	
	这样就把网关修改为192.168.1.1了，这种修改只是临时的，当你重新启动系统或网卡之后，还是会变回原来的网关。
	
		vi  /etc/sysconfig/network

		NETWORKING=yes
		HOSTNAME=centos
	
	NETWORKING=yes #表示系统是否使用网络，一般设置为yes。如果设为no，则不能使用网络。<br>
	HOSTNAME=centos #设置本机的主机名，这里设置的主机名要和/etc/hosts中设置的主机名对应<br>
	GATEWAY=192.168.1.1 #设置本机连接的网关的IP地址。
	

	单独设置DNS

		vi /etc/resolv.conf
		nameserver 8.8.8.8 #google域名服务器

	3/ 方式三：ifconfig命令(重启后无效)<br>
	ifconfig eth0 192.168.12.22

3. **域名映射**

	/etc/hosts文件 用于在通过主机名进行访问时做ip地址解析之用<br>
	所以，你想访问一个什么样的主机名，就需要把这个主机名和它对应的ip地址配置在/etc/hosts文件中

		[hadoop@localhost ~]$ vi /etc/hosts
		127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
		::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
		192.168.229.110   test01
		192.168.229.120   test02
		192.168.229.130   test03

4. **网络服务管理**

	1) 后台服务管理<br>
	service network status    查看指定服务的状态<br>
	service network stop     停止指定服务<br>
	service network start     启动指定服务<br>
	service network restart   重启指定服务<br>
	service --status-all       查看系统中所有的后台服务<br>
	
	2) 设置后台服务的自启配置<br>
	chkconfig   查看所有服务器自启配置<br>
	chkconfig iptables off   关掉指定服务的自动启动<br>
	chkconfig iptables on   开启指定服务的自动启动<br>

5. **系统中网络进程的端口监听情况**

	netstat -nltp

6. **防火墙配置**

	防火墙根据配置文件/etc/sysconfig/iptables来控制本机的“出、入”网络访问行为
其对行为的配置策略有四个策略表

	查看防火墙状态

		service iptables status
	关闭防火墙

		service iptables stop
	启动防火墙

		service iptables start
	禁止防火墙自启

		chkconfig iptables off


	**扩展了解**

	1、列出iptables规则

		iptables -L -n
	列出iptables规则并显示规则编号

		iptables -L -n --line-numbers
	
	2、列出iptables nat表规则（默认是filter表）

		iptables -L -n -t nat
	
	3、清除默认规则（注意默认是filter表，如果对nat表操作要加-t nat）

		#清除所有规则
		iptables -F 
	
		#重启iptables发现规则依然存在，因为没有保存
		service iptables restart
	
		#保存配置
		service iptables save
	
	4、禁止ssh登陆（若果服务器在机房，一定要小心）

		iptables -A INPUT -p tcp --dport 22 -j DROP
	
		#删除规则
		iptables -D INPUT -p tcp --dport 22 -j DROP
	
		#加入一条INPUT规则开放80端口
		iptables -I INPUT -p tcp --dport 80 -j ACCEPT

## 九.高级文本处理命令

1. **cut命令**

	cut命令可以从一个文本文件或者文本流中提取文本列。

	**cut语法**

		[hadoop@kyle-01 ~]$ cut -d'分隔字符' -f fields     ## 用于有特定分隔字符
		[hadoop@kyle-01 ~]$ cut -c 字符区间            ## 用于排列整齐的信息
		选项与参数：
		-d：后面接分隔字符。与 -f 一起使用；
		-f：依据 -d 的分隔字符将一段信息分割成为数段，用 -f 取出第几段的意思；
		-c：以字符 (characters) 的单位取出固定字符区间；
	eg：

		[hadoop@kyle-01 ~]$ echo $PATH
		/usr/local/bin:/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/hadoop/bin
		[hadoop@kyle-01 ~]$ echo $PATH | cut -d':' -f2
		/bin
		[hadoop@kyle-01 ~]$ echo $PATH | cut -d':' -f5
		/usr/sbin
		[hadoop@kyle-01 ~]$ echo $PATH | cut -d':' -f2,5
		/bin:/usr/sbin
		[hadoop@kyle-01 ~]$ echo $PATH | cut -d':' -f2-5
		/bin:/usr/bin:/usr/local/sbin:/usr/sbin
		[hadoop@kyle-01 ~]$ echo $PATH | cut -d':' -f2-
		/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/sbin:/home/hadoop/bin

2.  **sed命令**

	sed是一个很好的文件处理工具，本身是一个管道命令，主要是以行为单位进行处理，可以将数据行进行替换、删除、新增、选取等特定工作

	1) 删除：d命令<br>

		[hadoop@kyle-01 ~]$ sed '2d' example   	 	-----删除example文件的第二行。
		[hadoop@kyle-01 ~]$ sed '2,$d' example  	 	-----删除example文件的第二行到末尾所有行。
		[hadoop@kyle-01 ~]$ sed '$d' example  	 	-----删除example文件的最后一行。
		[hadoop@kyle-01 ~]$ sed '/test/'d example		-----删除example文件所有包含test的行。

	2) 替换：s命令
	
		[hadoop@kyle-01 ~]$ sed 's/test/mytest/g' example				
		##  在整行范围内把test替换为mytest。如果没有g标记，则只有每行第一个匹配的test被替换成mytest。
		
		[hadoop@kyle-01 ~]$ sed -n 's/^test/mytest/p' example			
		##  (-n)选项和p标志一起使用表示只打印那些发生替换的行。也就是说，如果某一行开头的test被替换成mytest，就打印它。
		
		[hadoop@kyle-01 ~]$ sed 's/^192.168.0.1/&localhost/' example		
		##  &符号表示追加一个串到找到的串后。所有以192.168.0.1开头的行都会被替换成它自已加 localhost，变成192.168.0.1localhost。
		
		[hadoop@kyle-01 ~]$ sed -n 's/\(love\)able/\1rs/p' example
		##  love被标记为1，所有loveable会被替换成lovers，而且替换的行会被打印出来。
		
		$ sed 's#10#100#g' example
		##  不论什么字符，紧跟着s命令的都被认为是新的分隔符，所以，“#”在这里是分隔符，代替了默认的“/”分隔符。表示把所有10替换成100。
		选定行的范围：逗号
		
		[hadoop@kyle-01 ~]$ sed -n '/test/,/check/p' example
		## 所有在模板test和check所确定的范围内的行都被打印。
		
		[hadoop@kyle-01 ~]$ sed -n '5,/^test/p' example
		## 打印从第五行开始到第一个包含以test开始的行之间的所有行。
		
		[hadoop@kyle-01 ~]$ sed '/test/,/check/s/$/sed test/' example
		## 对于模板test和west之间的行，每行的末尾用字符串sed test替换。
		多点编辑：e命令
		
		[hadoop@kyle-01 ~]$ sed -e '1,5d' -e 's/test/check/' example
		##  (-e)选项允许在同一行里执行多条命令。如例子所示，第一条命令删除1至5行，第二条命令用check替换test。命令的执行顺序对结果有影响。如果两个命令都是替换命令，那么第一个替换命令将影响第二个替换命令的结果。
		
		[hadoop@kyle-01 ~]$ sed --expression='s/test/check/' --expression='/love/d' example
		## 一个比-e更好的命令是--expression。它能给sed表达式赋值。

	3) 从文件读入：r命令

		[hadoop@kyle-01 ~]$ sed '/test/r file' example
		-----file里的内容被读进来，显示在与test匹配的行下面，如果匹配多行，则file的内容将显示在所有匹配行的下面。

	4) 写入文件：w命令

		[hadoop@kyle-01 ~]$ sed -n '/test/w file' example
		-----在example中所有包含test的行都被写入file里。

	5) 追加命令：a命令

		[hadoop@kyle-01 ~]$ sed '/^test/a\\--->this is a example' example    
		##  '--->this is a example'被追加到以test开头的行后面，sed要求命令a后面有一个反斜杠。

	6) 插入：i命令

		[hadoop@kyle-01 ~]$ sed '/test/i\\some thing new -------------------------' example
		如果test被匹配，则把反斜杠后面的文本插入到匹配行的前面。

	7) 下一个：n命令

		[hadoop@kyle-01 ~]$ sed '/test/{ n; s/aa/bb/; }' example
		-----如果test被匹配，则移动到匹配行的下一行，替换这一行的aa，变为bb，并打印该行，然后继续。

	8/ 退出：q命令
	
		[hadoop@kyle-01 ~]$ sed '10q' example
		-----打印完第10行后，退出sed。

2. **awk命令**

	awk是一个强大的文本分析工具，相对于grep的查找，sed的编辑，awk在其对数据分析并生成报告时，显得尤为强大。简单来说awk就是把文件逐行的读入，以空格为默认分隔符将每行切片，切开的部分再进行各种分析处理。

	假设last -n 5的输出如下

		[root@www ~]# last -n 5        ## 仅取出前五行
		root     pts/1   192.168.1.100  Tue Feb 10 11:21   still logged in
		root     pts/1   192.168.1.100  Tue Feb 10 00:46 - 02:28  (01:41)
		root     pts/1   192.168.1.100  Mon Feb  9 11:41 - 18:30  (06:48)
		dmtsai   pts/1   192.168.1.100  Mon Feb  9 11:41 - 11:41  (00:00)
		root     tty1                   Fri Sep  5 14:09 - 14:10  (00:01)

	如果只是显示最近登录的5个帐号

		[root@www ~]# last -n 5 | awk  '{print $1}'
		root
		root
		root
		dmtsai
		root

	awk工作流程是这样的：读入有'\n'换行符分割的一条记录，然后将记录按指定的域分隔符划分域，填充域，$0则表示所有域,$1表示第一个域,$n表示第n个域。默认域分隔符是"空白键" 或 "[tab]键",所以$1表示登录用户，$3表示登录用户ip,以此类推。


	如果只是显示/etc/passwd的账户

		[root@www ~]# cat /etc/passwd |awk  -F ':'  '{print $1}'  
		root
		daemon
		bin
		sys

	这种是awk+action的示例，每行都会执行action{print $1}。<br>
	-F指定域分隔符为':'

 
	如果只是显示/etc/passwd的账户和账户对应的shell,而账户与shell之间以tab键分割

		[root@www ~]# cat /etc/passwd |awk  -F ':'  '{print $1"\t"$7}'
		root    /bin/bash
		daemon  /bin/sh
		bin     /bin/sh
		sys     /bin/sh
 

	如果只是显示/etc/passwd的账户和账户对应的shell,而账户与shell之间以逗号分割,而且在所有行添加列名name,shell,在最后一行添加"blue,/bin/nosh"。

		[root@www ~]# cat /etc/passwd |awk  -F ':'  'BEGIN {print "name,shell"}  {print $1","$7} END {print "blue,/bin/nosh"}'
		name,shell
		root,/bin/bash
		daemon,/bin/sh
		bin,/bin/sh
		sys,/bin/sh
		....
		blue,/bin/nosh
 
	awk工作流程是这样的：先执行BEGING，然后读取文件，读入有/n换行符分割的一条记录，然后将记录按指定的域分隔符划分域，填充域，$0则表示所有域,$1表示第一个域,$n表示第n个域,随后开始执行模式所对应的动作action。接着开始读入第二条记录······直到所有的记录都读完，最后执行END操作。


	搜索/etc/passwd有root关键字的所有行

		[root@www ~]# awk  -F:  '/root/'  /etc/passwd
		root:x:0:0:root:/root:/bin/bash

	这种是pattern的使用示例，匹配了pattern(这里是root)的行才会执行action(没有指定action，默认输出每行的内容)。

	搜索支持正则，例如找root开头的: awk -F:  '/^root/'  /etc/passwd<br>
	搜索/etc/passwd有root关键字的所有行，并显示对应的shell

		[root@www ~]# awk  -F':'  '/root/{print $7}'  /etc/passwd             
		/bin/bash

 	这里指定了action{print $7}


	统计/etc/passwd:文件名，每行的行号，每行的列数，对应的完整行内容:

		[root@www ~]# awk  -F ':'  '{print "filename:" FILENAME ",linenumber:" NR ",columns:" NF ",linecontent:"$0}' /etc/passwd
		filename:/etc/passwd,linenumber:1,columns:7,linecontent:root:x:0:0:root:/root:/bin/bash
		filename:/etc/passwd,linenumber:2,columns:7,linecontent:daemon:x:1:1:daemon:/usr/sbin:/bin/sh
		filename:/etc/passwd,linenumber:3,columns:7,linecontent:bin:x:2:2:bin:/bin:/bin/sh
		filename:/etc/passwd,linenumber:4,columns:7,linecontent:sys:x:3:3:sys:/dev:/bin/sh
 
	使用printf替代print,可以让代码更加简洁，易读

 		[root@www ~]# awk  -F ':'  '{printf("filename:%s,linenumber:%s,columns:%s,linecontent:%s\n",FILENAME,NR,NF,$0)}' /etc/passwd