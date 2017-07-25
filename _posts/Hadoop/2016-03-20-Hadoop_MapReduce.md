---
layout: post
category:  Hadoop
title: Hadoop学习(三)-MapReduce
tags: Hadoop，MapReduce
---

## MapReduce基本概念

MapReduce是一种分布式计算模型（编程框架），由Google提出，主要用于搜索领域，解决海量数据的计算问题.MR由两个阶段组成：Map和Reduce，用户只需要实现map()和reduce()两个函数组件，即可实现分布式计算，这两个函数的形参是key、value对，表示函数的输入信息。<br>
### 1 基本结构 ###
**1.1 MRAppMaster(mapreduce application master)**负责整个程序的过程调度及状态协调<br>
a)一个mr程序启动的时候，最先启动的是MRAppMaster（由yarn框架启动），MRAppMaster启动后根据本次job的描述信息（客户端提交的job信息），计算出需要的maptask实例数量，然后向集群申请机器启动相应数量的maptask进程<br>
b)MRAppMaster监控到所有maptask进程任务完成之后，会根据客户指定的参数启动相应数量的reducetask进程，并告知reducetask进程要处理的数据范围（数据分区）<br>
**1.2 MapTask**：负责map阶段的整个数据处理流程<br>
maptask进程启动之后，根据给定的数据切片范围进行数据处理，主体流程为：<br>
a)利用客户指定的inputformat来获取RecordReader读取数据，形成输入KV对<br>
b)将输入KV对传递给客户定义的map()方法，做逻辑运算，并将map()方法输出的KV对收集到缓存<br>
c)将缓存中的KV对按照K分区排序后不断溢写到磁盘文件<br>
**1.3 ReduceTask**：负责reduce阶段的整个数据处理流程<br>
Reducetask进程启动之后，根据MRAppMaster告知的待处理数据所在位置，从若干台maptask运行所在机器上获取到若干个maptask输出结果文件，并在本地进行重新归并排序，然后按照相同key的KV为一个组，调用客户定义的reduce()方法进行逻辑运算，并收集运算输出的结果KV，然后调用客户指定的outputformat将结果数据输出到外部存储<br>
### 2 并行度决定机制 ###
既然是分布式计算系统那么我们的maptask的并行和reducetask的并行就是必须的，那么是什么样的并行机制在影响着map reduce计算框架呢？是否并行的实例越多越好呢？我们接下来进一步讨论。<br>

**2.1 mapTask并行度的决定机制**<br>
一个job的map阶段并行度由客户端在提交job时决定而客户端对map阶段并行度的规划的基本逻辑为：
将待处理数据执行逻辑切片（即按照一个特定切片大小，将待处理数据划分成逻辑上的多个split），然后每一个split分配一个mapTask并行实例处理<br>

**2.2 切片机制**<br>
①、切片定义在InputFormat类中的getSplit()方法,该方法在客户端进行处理<br>
②、FileInputFormat中默认的切片机制：<br>

	a)简单地按照文件的内容长度进行切片
	b)切片大小，默认等于block大小
	c)切片时不考虑数据集整体，而是逐个针对每一个文件单独切片
**2.3 FileInputFormat中切片的大小的参数配置**<br>
minsize：默认值：1<br>

	配置参数： mapreduce.input.fileinputformat.split.minsize

maxsize：默认值：Long.MAXValue<br>

    配置参数：mapreduce.input.fileinputformat.split.maxsize

blocksize：默认情况下，切片大小=blocksize<br>
maxsize（切片最大值）：
参数如果调得比blocksize小，则会让切片变小，而且就等于配置的这个参数的值<br>
minsize （切片最小值）：
参数调的比blockSize大，则可以让切片变得比blocksize还大

**2.4 ReduceTask并行度的决定机制**<br>
reducetask的并行度同样影响整个job的执行并发度和执行效率，但与maptask的并发数由切片数决定不同，Reducetask数量的决定是可以直接手动设置：<br>

	//默认值是1，手动设置为4
	job.setNumReduceTasks(4);

***注意***： reducetask数量并不是任意设置，还要考虑业务逻辑需求，有些情况下，需要计算全局汇总结果，就只能有1个reducetask

## MAPREDUCE 示例编写及编程规范

（1）用户编写的程序分成三个部分：Mapper，Reducer，Driver(提交运行mr程序的客户端)<br>
（2）Mapper的输入数据是KV对的形式（KV的类型可自定义）<br>
（3）Mapper的输出数据是KV对的形式（KV的类型可自定义）<br>
（4）Mapper中的业务逻辑写在map()方法中<br>
（5）map()方法（maptask进程）对每一个<K,V>调用一次<br>
（6）Reducer的输入数据类型对应Mapper的输出数据类型，也是KV<br>
（7）Reducer的业务逻辑写在reduce()方法中<br>
（8）Reducetask进程对每一组相同k的<k,v>组调用一次reduce()方法<br>
（9）用户自定义的Mapper和Reducer都要继承各自的父类<br>
（10）整个程序需要一个Drvier来进行提交，提交的是一个描述了各种必要信息的job对象<br>

### 1 wordcount示例编写 ###
需求：在一堆给定的文本文件中统计输出每一个单词出现的总次数<br>
**1.1 定义一个mapper类**

	/首先要定义四个泛型的类型
	//keyin:  LongWritable    valuein: Text
	//keyout: Text            valueout:IntWritable
	
	public class WordCountMapper extends Mapper<LongWritable, Text, Text, IntWritable>{
		//map方法的生命周期：  框架每传一行数据就被调用一次
		//key :  这一行的起始点在文件中的偏移量
		//value: 这一行的内容
		@Override
		protected void map(LongWritable key, Text value, Context context) throws IOException, InterruptedException {
			//拿到一行数据转换为string
			String line = value.toString();
			//将这一行切分出各个单词
			String[] words = line.split(" ");
			//遍历数组，输出<单词，1>
			for(String word:words){
				context.write(new Text(word), new IntWritable(1));
			}
		}
	}

**1.2 定义一个reducer类**

	//生命周期：框架每传递进来一个kv 组，reduce方法被调用一次
		@Override
		protected void reduce(Text key, Iterable<IntWritable> values, Context context) throws IOException, InterruptedException {
			//定义一个计数器
			int count = 0;
			//遍历这一组kv的所有v，累加到count中
			for(IntWritable value:values){
				count += value.get();
			}
			context.write(key, new IntWritable(count));
		}
	}

**1.3 定义一个主类，用来描述job并提交job**

	public class WordCountRunner {
		//把业务逻辑相关的信息（哪个是mapper，哪个是reducer，要处理的数据在哪里，输出的结果放哪里……）描述成一个job对象
		//把这个描述好的job提交给集群去运行
		public static void main(String[] args) throws Exception {
			Configuration conf = new Configuration();
			Job wcjob = Job.getInstance(conf);
			//指定我这个job所在的jar包
	//		wcjob.setJar("/home/hadoop/wordcount.jar");
			wcjob.setJarByClass(WordCountRunner.class);
			
			wcjob.setMapperClass(WordCountMapper.class);
			wcjob.setReducerClass(WordCountReducer.class);
			//设置我们的业务逻辑Mapper类的输出key和value的数据类型
			wcjob.setMapOutputKeyClass(Text.class);
			wcjob.setMapOutputValueClass(IntWritable.class);
			//设置我们的业务逻辑Reducer类的输出key和value的数据类型
			wcjob.setOutputKeyClass(Text.class);
			wcjob.setOutputValueClass(IntWritable.class);
			
			//指定要处理的数据所在的位置
			FileInputFormat.setInputPaths(wcjob, "hdfs://hdp-server01:9000/wordcount/data/big.txt");
			//指定处理完成之后的结果所保存的位置
			FileOutputFormat.setOutputPath(wcjob, new Path("hdfs://hdp-server01:9000/wordcount/output/"));
			
			//向yarn集群提交这个job
			boolean res = wcjob.waitForCompletion(true);
			System.exit(res?0:1);
		}


### 2 MAPREDUCE程序运行模式 ###

**2.1 本地运行模式**

（1）mapreduce程序是被提交给LocalJobRunner在本地以单进程的形式运行<br>
（2）而处理的数据及输出结果可以在本地文件系统，也可以在hdfs上<br>
（3）怎样实现本地运行？写一个程序，不要带集群的配置文件（本质是你的mr程序的conf中是否有mapreduce.framework.name=local以及yarn.resourcemanager.hostname参数）<br>
（4）本地模式非常便于进行业务逻辑的debug，只要在eclipse中打断点即可<br>

**2.2 集群运行模式**

（1）将mapreduce程序提交给yarn集群resourcemanager，分发到很多的节点上并发执行<br>
（2）处理的数据和输出结果应该位于hdfs文件系统<br>
（3）提交集群的实现步骤：<br>
————A、将程序打成JAR包，然后在集群的任意一个节点上用hadoop命令启动<br>

	$ hadoop jar wordcount.jar cn.itcast.bigdata.mrsimple.WordCountDriver inputpath outputpath
————B、直接在linux的eclipse中运行main方法
（项目中要带参数：mapreduce.framework.name=yarn以及yarn的两个基本配置,可在配置文件中直接配置或者在设置job时候配置）