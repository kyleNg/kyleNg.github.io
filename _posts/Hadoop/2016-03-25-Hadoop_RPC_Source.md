---
layout: post
category:  Hadoop
title: Hadoop源码学习(一)-RPC
tags: Hadoop，rpc
---

### Hadoop的通讯协议

hadoop的通讯协议主要有两种，一种是Hadoop RPC接口，一种是流式接口，那么这两种接口各自有各自的分工，前者主要是负责一些连接的管理、节点的管理以及一些数据的管理，而后者主要是数据的读写传输。<br>
今天我们主要了解一下RPC协议。

### Hadoop RPC

