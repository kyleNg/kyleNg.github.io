---
layout: post
category:  Hadoop
title: Hadoop-MapReduce的Combiner
tags: Hadoop，MapReduce，Combiner
---

### Combiner基本概念

* Combiner是MR程序中Mapper和Reducer之外的一种组件<br>
* Combiner组件的父类就是Reducer<br>
* Combiner和reducer的区别在于运行的位置：<br>
—— Combiner是在每一个maptask所在的节点运行<br>
—— Reducer是接收全局所有Mapper的输出结果
* combiner的意义就是对每一个maptask的输出进行局部汇总，以减小网络传输量.
* combiner能够应用的前提是不能影响最终的业务逻辑.而且,combiner的输出kv应该跟reducer的输入kv类型要对应起来

### 具体实现步骤：

1、自定义一个combiner继承Reducer，重写reduce方法<br>
2、在job中设置：  job.setCombinerClass(CustomCombiner.class)

### Combiner的执行时机

Conbiner函数的执行时机可能会在map的merge操作完成之前，也可能在merge之后执行，这个时机由配置参数 `min.num.spill.for.combine` (该值默认为3)，也就是说在map端产生的spill文件最少有 `min.num.spill.for.combine` 的时候，Conbiner函数会在merge操作合并最终的本机结果文件之前执行，否则在merge之后执行。通过这种方式，就可以在spill文件很多并且需要做conbine的时候，减少写入本地磁盘的数据量，同样也减少了对磁盘的读写频率，可以起到优化作业的目的.

**总结：**

Combiner发生在Map端，对数据进行局部聚合处理，数据量变小了，传送到reduce端的数据量变小了，传输时间变短，作业的整体时间变短。