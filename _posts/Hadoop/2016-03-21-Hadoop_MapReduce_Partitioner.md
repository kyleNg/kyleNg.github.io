---
layout: post
category:  Hadoop
title: Hadoop学习(五)-MapReduce的Partitioner
tags: Hadoop，MapReduce，Partitioner
---

## Partitioner基本概念

分区(Partitioner)的作用是将所有的数据输出到同一个out文件中，分区可以将数据输出到不同的文件中。因为Reduce任务最终的输出就是整个job的输出，因此分区是针对于Reduce任务的数量来说的。

**分区的作用：**<br>
在mapreduce运算之后可能会有这样的需求：有一类数据都具有统一特征，那么在reduce输出结果到文件中的时候是不是把这些具有统一特征的数据放到一个结果输出文件比较好呢？这时候就可以把这些区分数据的特征逻辑写到partitioner中实现数据分类保存。

## Partitioner实现方式

### 1 提交Job需要设置分区的代码 ###

	//设置reducer任务数，默认为1
	job.setNumReduceTasks(2);
	//设置分区类
	job.setPartitionerClass(HashPartitioner.class);

**注意：**reduce任务数的设置需要跟分区类中的逻辑相互配合，两者相互关联。但是reduce的任务数量与分区数量没有必然关系。

### 2 源码默认实现 ###

	public class HashPartitioner<K, V> extends Partitioner<K, V> {
	 
	  /**reduce任务输出的value
	     *reduce任务输出的value
	     *numReduceTasks 分区的数量，也就是reduce任务的数量
	    */
	  public int getPartition(K key, V value,int numReduceTasks) {
	    return (key.hashCode() & Integer.MAX_VALUE) % numReduceTasks;
	  }
	}

**分析：**
(key.hashCode() & Integer.MAX_VALUE) % numReduceTasks;
<br>
HashPartitioner是根据reduce任务输出的key值进行hash分区，使用key.hashCode()取得hash值。我们知道在java中，每个hashcode的值都基本上是唯一的，但是其值是int型，也就是说取值范围是-231到231-1之间。而整数最大值二进制表示为0x7fffffff，当进行了与操作之后，只有最高位会改变为0，其他位的数值都是不变的，这样就可以把负数的hashcode值转化为正数。
<br>
另外我们知道一个正数对另外一个正数n取模，那么结果范围肯定是0...n-1。
<br>
假设我们现在设置的numReduceTasks为2，那么经过取模后，结果就是0,1。也就是说我们输出的分区，一个编号是0，一个编号是1。那么hadoop就会自动帮我们创建2个输出文件，一个输出文件编号为0，一个文件输出编号为1.然后针对key去hashcode取模，将结果输出到相应的文件中。

**总结：**<br>
对于HashPartitioner，其是根据key的hashcode，对numReduceTasks取模，因此我们有多少个numReduceTasks，就会有多少个输出分区。即reduce任务的数量等于分区的数量。

### 2 自己实现 ###

如果不想用默认的哈希区分的partitioner可以自己写一个partitioner类继承Partitioner类并实现getPartition方法（分区逻辑）。

	public class MyPartitioner extends Partitioner<Key, Value> {
		@Override
		public int getPartition(Key key, Value value, int numPartitions) {
			//TODO 实现自己的分区逻辑代码
			return 0;
		}
	}

**注意：**在job的设置中partitioner的类要改成自己写的类

	job.setPartitionerClass(MyPartitioner.class);

**总结：**<br>
在自己设置partitioner的时候可以尽可能按照自己的业务逻辑将一类特征向量相同的数据出力到同一个文件中，同时也要注意自己定义的分区数不能比设置的reduce数量还要多的话就会有Illegal partition异常，如果reduce数量设置的比分区数量多，那么没有被分到数据的reduce处理文件就是空文件，不会报错。