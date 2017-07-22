---
layout: post
category:  Hadoop
title: Hadoop学习(三)-MapReduce
tags: Hadoop，MapReduce
---

## MapReduce基本概念

MapReduce是一种分布式计算模型（编程框架），由Google提出，主要用于搜索领域，解决海量数据的计算问题.MR由两个阶段组成：Map和Reduce，用户只需要实现map()和reduce()两个函数，即可实现分布式计算，这两个函数的形参是key、value对，表示函数的输入信息。<br>
### 1，基本结构 ###

**1.1 MRAppMaster(mapreduce application master)**负责整个程序的过程调度及状态协调<br>
a)一个mr程序启动的时候，最先启动的是MRAppMaster，MRAppMaster启动后根据本次job的描述信息，计算出需要的maptask实例数量，然后向集群申请机器启动相应数量的maptask进程<br>
b)MRAppMaster监控到所有maptask进程任务完成之后，会根据客户指定的参数启动相应数量的reducetask进程，并告知reducetask进程要处理的数据范围（数据分区）<br>
**1.2 MapTask**：负责map阶段的整个数据处理流程<br>
maptask进程启动之后，根据给定的数据切片范围进行数据处理，主体流程为：<br>
a)利用客户指定的inputformat来获取RecordReader读取数据，形成输入KV对<br>
b)将输入KV对传递给客户定义的map()方法，做逻辑运算，并将map()方法输出的KV对收集到缓存<br>
c)将缓存中的KV对按照K分区排序后不断溢写到磁盘文件<br>
**1.3 ReduceTask**：负责reduce阶段的整个数据处理流程<br>
Reducetask进程启动之后，根据MRAppMaster告知的待处理数据所在位置，从若干台maptask运行所在机器上获取到若干个maptask输出结果文件，并在本地进行重新归并排序，然后按照相同key的KV为一个组，调用客户定义的reduce()方法进行逻辑运算，并收集运算输出的结果KV，然后调用客户指定的outputformat将结果数据输出到外部存储<br>
### 2，并行度决定机制 ###
既然是分布式计算系统那么我们的maptask的并行和reducetask的并行就是必须的，那么是什么样的并行机制在影响着map reduce计算框架呢？是否并行的实例越多越好呢？我们接下来进一步讨论。<br>
**2.1 mapTask并行度的决定机制**<br>
一个job的map阶段并行度由客户端在提交job时决定而客户端对map阶段并行度的规划的基本逻辑为：
将待处理数据执行逻辑切片（即按照一个特定切片大小，将待处理数据划分成逻辑上的多个split），然后每一个split分配一个mapTask并行实例处理<br>
那什么又是切片呢？