---
layout: post
category:  Hive
title: Hive基础
tags: Hive
---

## 什么是Hive

Hive是基于Hadoop的一个数据仓库工具，可以将结构化的数据文件映射为一张数据库表，并提供类SQL查询功能。<br>
主要用途：用来做离线数据分析，比直接用mapreduce开发效率更高


**Hive架构图**

![](https://blog-1255865654.cos.ap-beijing.myqcloud.com/hive/hive%E6%9E%B6%E6%9E%84.png?q-sign-algorithm=sha1&q-ak=AKID4bWwGk54XYUVv0ZxtN0bA7ALqzhamYiH&q-sign-time=1557156354;1557158154&q-key-time=1557156354;1557158154&q-header-list=&q-url-param-list=&q-signature=40a237e8619373b406c027ca7a2021e3cbd3c6c2&x-cos-security-token=1eb486a05624ed847163662615766ab28d569baf10001)

## Hive基本组成及功能

1. 用户接口主要由三个：**CLI、JDBC/ODBC和WebGUI**。其中，CLI为shell命令行；JDBC/ODBC是Hive的JAVA实现，与传统数据库JDBC类似；WebGUI是通过浏览器访问Hive。

2. 元数据存储：Hive 将元数据存储在数据库中。Hive 中的元数据包括表的名字，表的列和分区及其属性，表的属性（是否为外部表等），表的数据所在目录等。

3. 解释器、编译器、优化器完成 HQL 查询语句从词法分析、语法分析、编译、优化以及查询计划的生成。生成的查询计划存储在 HDFS 中，并在随后有 MapReduce 调用执行。

## Hive的数据存储

1. Hive中所有的数据都存储在 HDFS 中，没有专门的数据存储格式（可支持Text，SequenceFile，ParquetFile，RCFILE等）<br>
	SequenceFile是hadoop中的一种文件格式：文件内容是以序列化的kv对象来组织的

2. 只需要在创建表的时候告诉 Hive 数据中的列分隔符和行分隔符，Hive 就可以解析数据

3. Hive 中包含以下数据模型：DB、Table，External Table，Partition，Bucket。<br>
	**db：**在hdfs中表现为${hive.metastore.warehouse.dir}目录下一个文件夹<br>
	**table：**在hdfs中表现所属db目录下一个文件夹<br>
	**external table：**与table类似，不过其数据存放位置可以在任意指定路径与table类似，不过其数据存放位置可以在任意指定路径<br>
	**partition：**在hdfs中表现为table目录下的子目录
	**bucket：**在hdfs中表现为同一个表目录下根据hash散列之后的多个文件

## Hive的安装部署

**1. 解压Hive包**

		tar zxvf apache-hive-1.2.2-bin.tar.gz

**2. 安装MariaDB(Mysql) 提供元数据存储服务**

若已经有一个Mysql的服务器则可以忽略此步骤<br>
**详细参见Linux软件安装**
	
	yum -y install mariadb-server
	service mariadb start
	systemctl enable mariadb

配置用户授权及权限

	MariaDB [(none)]> grant all on *.* to 'root'@'%' identified by '123456';
	Query OK, 0 rows affected (0.00 sec)
	MariaDB [(none)]> grant all on *.* to 'root'@'localhost' identified by '123456';
	Query OK, 0 rows affected (0.00 sec)
	MariaDB [(none)]> grant all on *.* to 'root'@'master' identified by '123456';
	Query OK, 0 rows affected (0.00 sec)
	MariaDB [(none)]> flush privileges;
	Query OK, 0 rows affected (0.00 sec)

**3. 修改配置文件 配置元数据相关信息**

	cd apache-hive-1.2.2-bin/conf
	#创建配置文件
	vim hive-site.xml

文件内容更如下：

	<configuration>
		<property>
			<name>javax.jdo.option.ConnectionURL</name>
			<value>jdbc:mysql://master:3306/hive?createDatabaseIfNotExist=true</value>
			<description>JDBC connect string for a JDBC metastore</description>
		</property>
		<property>
			<name>javax.jdo.option.ConnectionDriverName</name>
			<value>com.mysql.jdbc.Driver</value>
			<description>Driver class name for a JDBC metastore</description>
		</property>
		<property>
			<name>javax.jdo.option.ConnectionUserName</name>
			<value>root</value>
			<description>username to use against metastore database</description>
		</property>
		<property>
			<name>javax.jdo.option.ConnectionPassword</name>
			<value>123456</value>
			<description>password to use against metastore database</description>
		</property>
	</configuration>


**4. 增加环境变量**

	vim /etc/profile
	# 文件追加下面两句
	HIVE_HOME=/usr/local/src/apache-hive-1.2.2-bin
	export PATH=$HIVE_HOME/bin:$PATH
	# 重新加载环境变量
	source /etc/profile

**5. 添加JDBC连接驱动**

提前准备好JDBC连接文件

	cp mysql-connector-java-5.1.44-bin.jar apachehive-1.2.2-bin/lib

**6. 启动Hive服务**

	hive

## Hive启动方式

**1. 使用的本地metastore，直接通过hive命令启动**

使用以下命令开启

	$HIVE_HOME/bin/hive

hive命令默认是启动client服务，相当于以下命令

	$HIVE_HOME/bin/hive --service cli

**2. 使用远程的metastore**

服务器1如1中配置，使用本地metastore

服务器1启动metastore服务

	$HIVE_HOME/bin/hive --service metastore

上边会启动端口号默认9083的metastore服务，也可以通过-p指定端口号

	$HIVE_HOME/bin/hive --service metastore -p 9083

服务器2如下配置，使用远程metastore

	<configuration>
	    <property>
	       <name>hive.metastore.uris</name>
	       <value>thrift://master:9083</value><!-- 此处是服务器1的ip -->
	    </property>
	</configuration>

服务器2直接启动cli即可

	$HIVE_HOME/bin/hive


**3. 启动hiveserver2，使其他服务可以通过thrift接入hive**

hive-site.xml中可以加上是否需要验证的配置，此处设为NONE，暂时不需要验证
	<property>
	    <name>hive.server2.authentication</name>
	    <value>NONE</value>
	</property>

hadoop的core-site.xml文件中配置hadoop代理用户

	<property>
	    <name>hadoop.proxyuser.root.hosts</name>
	    <value>*</value>
	</property>
	<property>
	    <name>hadoop.proxyuser.root.groups</name>
	    <value>*</value>
	</property>

此处这样设置的意思是，root用户提交的任务可以在任意机器上以任意组的所有用户的身份执行。 
如果不设置，后续连接时会报下面错误。

	org.apache.hadoop.ipc.RemoteException(org.apache.hadoop.security.authorize.AuthorizationException):
	User: root is not allowed to impersonate anonymous

hadoop在安全校验时不允许root用户使用匿名身份访问hadoop。 
但其实我的hadoop并没有设置Authorization，为什么必须设置root的代理用户，这里暂时还是个疑问。 
设置完后，需要重启hadoop

启动hiveserver2

	$HIVE_HOME/bin/hive --service hiveserver2

beeline工具测试使用jdbc方式连接

	$HIVE_HOME/bin/beeline -u jdbc:hive2://localhost:10000 -n root

或者

	hive/bin/beeline  回车，进入beeline的命令界面
	输入命令连接hiveserver2
	beeline> !connect jdbc:hive2//mini1:10000

hiveserver2端口号默认是10000 
使用beeline通过jdbc连接上之后就可以像client一样操作

hiveserver2会同时启动一个webui，端口号默认为10002，可以通过http://master:10002/访问

界面中可以看到Session/Query/Software等信息。(此网页只可查看，不可以操作hive数据仓库)

**4. 启动hiveWebInterface，通过网页访问hive**

hive提供网页GUI来访问Hive数据仓库 可以通过以下命令启动hwi，默认端口号9999

	$HIVE_HOME/bin/hive --service hwi

**5. 使用HCatalog访问hive**

待更新

## Hive基本操作

[官方文档](https://cwiki.apache.org/confluence/display/Hive/Home#Home-UserDocumentation)

**1. DDL操作**

’[]’ 表示可选，’|’ 表示二选一 <br>
[详细参见官网](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-CreateTable)

	CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name    -- (Note: TEMPORARY available in Hive 0.14.0 and later)
	  [(col_name data_type [COMMENT col_comment], ... [constraint_specification])]
	  [COMMENT table_comment]
	  [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
	  [CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS]
	  [SKEWED BY (col_name, col_name, ...)                  -- (Note: Available in Hive 0.10.0 and later)]
	     ON ((col_value, col_value, ...), (col_value, col_value, ...), ...)
	     [STORED AS DIRECTORIES]
	  [
	   [ROW FORMAT row_format] 
	   [STORED AS file_format]
	     | STORED BY 'storage.handler.class.name' [WITH SERDEPROPERTIES (...)]  -- (Note: Available in Hive 0.6.0 and later)
	  ]
	  [LOCATION hdfs_path]
	  [TBLPROPERTIES (property_name=property_value, ...)]   -- (Note: Available in Hive 0.6.0 and later)
	
	[AS select_statement];   -- (Note: Available in Hive 0.5.0 and later; not supported for external tables)
	
	CREATE [TEMPORARY] [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name
	  LIKE existing_table_or_view_name
	  [LOCATION hdfs_path];
	 
	data_type
	  : primitive_type
	  | array_type
	  | map_type
	  | struct_type
	  | union_type  -- (Note: Available in Hive 0.7.0 and later)
	 
	primitive_type
	  : TINYINT
	  | SMALLINT
	  | INT
	  | BIGINT
	  | BOOLEAN
	  | FLOAT
	  | DOUBLE
	  | DOUBLE PRECISION -- (Note: Available in Hive 2.2.0 and later)
	  | STRING
	  | BINARY      -- (Note: Available in Hive 0.8.0 and later)
	  | TIMESTAMP   -- (Note: Available in Hive 0.8.0 and later)
	  | DECIMAL     -- (Note: Available in Hive 0.11.0 and later)
	  | DECIMAL(precision, scale)  -- (Note: Available in Hive 0.13.0 and later)
	  | DATE        -- (Note: Available in Hive 0.12.0 and later)
	  | VARCHAR     -- (Note: Available in Hive 0.12.0 and later)
	  | CHAR        -- (Note: Available in Hive 0.13.0 and later)
	 
	array_type
	  : ARRAY < data_type >
	 
	map_type
	  : MAP < primitive_type, data_type >
	 
	struct_type
	  : STRUCT < col_name : data_type [COMMENT col_comment], ...>
	 
	union_type
	   : UNIONTYPE < data_type, data_type, ... >  -- (Note: Available in Hive 0.7.0 and later)
	 
	row_format
	  : DELIMITED [FIELDS TERMINATED BY char [ESCAPED BY char]] [COLLECTION ITEMS TERMINATED BY char]
	        [MAP KEYS TERMINATED BY char] [LINES TERMINATED BY char]
	        [NULL DEFINED AS char]   -- (Note: Available in Hive 0.13 and later)
	  | SERDE serde_name [WITH SERDEPROPERTIES (property_name=property_value, property_name=property_value, ...)]
	 
	file_format:
	  : SEQUENCEFILE
	  | TEXTFILE    -- (Default, depending on hive.default.fileformat configuration)
	  | RCFILE      -- (Note: Available in Hive 0.6.0 and later)
	  | ORC         -- (Note: Available in Hive 0.11.0 and later)
	  | PARQUET     -- (Note: Available in Hive 0.13.0 and later)
	  | AVRO        -- (Note: Available in Hive 0.14.0 and later)
	  | INPUTFORMAT input_format_classname OUTPUTFORMAT output_format_classname
	 
	constraint_specification:
	  : [, PRIMARY KEY (col_name, ...) DISABLE NOVALIDATE ]
	    [, CONSTRAINT constraint_name FOREIGN KEY (col_name, ...) REFERENCES table_name(col_name, ...) DISABLE NOVALIDATE

**说明：**

1. CREATE TABLE 创建一个指定名字的表。如果相同名字的表已经存在，则抛出异常；用户可以用 IF NOT EXISTS 选项来忽略这个异常。<br>

2. EXTERNAL关键字可以让用户创建一个外部表，在建表的同时指定一个指向实际数据的路径（LOCATION），Hive 创建内部表时，会将数据移动到数据仓库指向的路径；若创建外部表，仅记录数据所在的路径，不对数据的位置做任何改变。在删除表的时候，内部表的元数据和数据会被一起删除，而外部表只删除元数据，不删除数据。

3. LIKE 允许用户复制现有的表结构，但是不复制数据。<br>

		CREATE TABLE new_table LIKE old_table;

4. ROW FORMAT DELIMITED <br>
   [FIELDS TERMINATED BY char] <br>
   [COLLECTION ITEMS TERMINATED BY char]<br> 
   [MAP KEYS TERMINATED BY char] <br>

	Hive将HDFS上的文件映射成表结构，通过分隔符来区分列（比如’,’ ‘;’ or ‘^’ 等），row format就是用于指定序列化和反序列化的规则。

	逗号用于分割列，即FIELDS TERMINATED BY char，分割为如下列 ID、name、hobby（该字段是数组形式，通过 ‘-’ 进行分割，即COLLECTION ITEMS TERMINATED BY ‘-’）、address（该字段是键值对形式map，通过 ‘:’ 分割键值，即 MAP KEYS TERMINATED BY ‘:’）；而FIELDS TERMINATED BY char用于区分不同条的数据，默认是换行符；

5. STORED AS 
SEQUENCEFILE|TEXTFILE|RCFILE
如果文件数据是纯文本，可以使用 STORED AS TEXTFILE。如果数据需要压缩，使用 STORED AS SEQUENCEFILE。

6. CLUSTERED BY
对于每一个表（table）或者分区， Hive可以进一步组织成桶，也就是说桶是更为细粒度的数据范围划分。Hive也是 针对某一列进行桶的组织。Hive采用对列值哈希，然后除以桶的个数求余的方式决定该条记录存放在哪个桶当中。 
把表（或者分区）组织成桶（Bucket）有两个理由：

	（1）获得更高的查询处理效率。桶为表加上了额外的结构，Hive 在处理有些查询时能利用这个结构。具体而言，连接两个在（包含连接列的）相同列上划分了桶的表，可以使用 Map 端连接 （Map-side join）高效的实现。比如JOIN操作。对于JOIN操作两个表有一个相同的列，如果对这两个表都进行了桶操作。那么将保存相同列值的桶进行JOIN操作就可以，可以大大较少JOIN的数据量。

	（2）使取样（sampling）更高效。在处理大规模数据集时，在开发和修改查询的阶段，如果能在数据集的一小部分数据上试运行查询，会带来很多方便。


**2. 修改表**

[详细参见官网](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL#LanguageManualDDL-AlterTable/Partition/Column)



	#修改表明
	ALTER TABLE table_name RENAME TO new_table_name;
	#修改表的属性
	ALTER TABLE table_name SET TBLPROPERTIES table_properties;
	table_properties:
	  : (property_name = property_value, property_name = property_value, ... )
	#修改说明
	ALTER TABLE table_name SET TBLPROPERTIES ('comment' = new_comment);


**3. DML操作**

[详细参见官网](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DML)

	LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename [PARTITION (partcol1=val1, partcol2=val2 ...)]
	 
	LOAD DATA [LOCAL] INPATH 'filepath' [OVERWRITE] INTO TABLE tablename [PARTITION (partcol1=val1, partcol2=val2 ...)] [INPUTFORMAT 'inputformat' SERDE 'serde'] (3.0 or later)


**4. 显示命令**

	show tables;
	show databases;
	show partitions table_name;
	show functions;
	desc extended table_name;
	desc formatted table_name;

	#！ + Linux命令
	！ls
	#dfs + hadoop命令
	dfs -ls /
