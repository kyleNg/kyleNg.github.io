---
layout: post
category:  Hadoop
title: Hadoop学习(三)-MapReduce
tags: Hadoop，MapReduce
---

### MapReduce结构和基本概念

MapReduce是一种分布式计算模型，由Google提出，主要用于搜索领域，解决海量数据的计算问题.MR由两个阶段组成：Map和Reduce，用户只需要实现map()和reduce()两个函数，即可实现分布式计算，这两个函数的形参是key、value对，表示函数的输入信息。