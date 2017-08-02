---
layout: post
category:  Hadoop
title: Hadoop-MapReduce的shuffle
tags: Hadoop，MapReduce，shuffle
---

### shuffle基本概念

* mapreduce中，map阶段处理的数据如何传递给reduce阶段，是mapreduce框架中最关键的一个流程，这个流程就叫shuffle<br>
* shuffle: 洗牌、发牌——（核心机制：数据分区，排序，缓存）<br>
* maptask输出的处理结果数据，分发给reducetask，并在分发的过程中，*对数据按key进行了分区和排序*<br>

### 主要流程：

Shuffle缓存流程：<br>
![图片摘自网络](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1501698720835&di=78b76ddd1f6ecf4ebfe359e6ac47da7a&imgtype=0&src=http%3A%2F%2Fwww.myexception.cn%2Fimg%2F2013%2F07%2F08%2F12142841.png)

shuffle是MR处理流程中的一个过程，它的每一个处理步骤是分散在各个map task和reduce task节点上完成的，整体来看，分为3个操作：  

1. 分区partition
2. Sort根据key排序
3. Combiner进行局部value的合并

### 详细流程

1. maptask收集我们的map()方法输出的kv对，放到内存缓冲区中.
2. 当缓冲区达到一定阈值时，就从内存缓冲区不断溢出到本地磁盘文件，可能会溢出多个文件.
3. 多个溢出文件会被合并成大的溢出文件.
4. 在溢出过程中，及合并的过程中，都要调用partitoner进行分组和针对key进行排序.
5. reducetask根据自己的分区号，去各个maptask机器上取相应的结果分区数据.
6. reducetask会取到同一个分区的来自不同maptask的结果文件，reducetask会将这些文件再进行合并（归并排序）.
7. 合并成大文件后，shuffle的过程也就结束了，后面进入reducetask的逻辑运算过程（从文件中取出一个一个的键值对group，调用用户自定义的reduce()方法）.


Shuffle中的缓冲区大小会影响到mapreduce程序的执行效率，原则上说，缓冲区越大，磁盘io的次数越少，执行速度就越快 
缓冲区的大小可以通过参数调整,  参数： `io.sort.mb`  默认100M