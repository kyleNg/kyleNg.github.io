---
layout: post
category: 
- java
- collection
title: Java集合
tags: java,collection
---
## 前言
Java集合是java提供的工具包，包含了常用的数据结构：集合、链表、队列、栈、数组、映射等。Java集合工具包位置是java.util.*<br>
Java集合主要可以划分为4个部分：List(列表)、Set(集合)、Map(映射,键值对)、工具类(Iterator迭代器、Enumeration枚举类、Arrays和Collections)。<br><br>
Java集合框架的基本接口/类层次结构:<br>

	java.util.Collection [I]
	+--java.util.List [I]
	   +--java.util.ArrayList [C]
	   +--java.util.LinkedList [C]
	   +--java.util.Vector [C]
	      +--java.util.Stack [C]
	+--java.util.Set [I]
	   +--java.util.HashSet [C]
	   +--java.util.SortedSet [I]
	   +--java.util.TreeSet [C]

	java.util.Map [I]
	+--java.util.SortedMap [I]
	   +--java.util.TreeMap [C]
	+--java.util.Hashtable [C]
	+--java.util.HashMap [C]
	+--java.util.LinkedHashMap [C]
	+--java.util.WeakHashMap [C]

	[I]：接口	[C]：类

接下来逐一学习上面的集合类

### 一，Collection接口
Collection是最基本的集合接口，一个Collection代表一组Object的集合，这些Object被称作Collection的元素。<br>
Collection是一个接口，用以提供规范定义，不能被实例化使用。但是接口里面有几个类是所有实现它的集合都有的方法。<br>

![](http://img.blog.csdn.net/20170416210825924?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhbzQ2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

不论Collection的实际类型如何，它都支持一个iterator()的方法，该方法返回一个迭代对象，使用该迭代对象即可逐一访问Collection中每一个元素。典型的用法如下：

	Iterator it = collection.iterator(); // 获得一个迭代子 
	while(it.hasNext()) { 
		// 注意：在迭代器里面操作集合会有异常
		//obj.add("abc"); //java.util.ConcurrentModificationException
	　　Object obj = it.next(); // 得到下一个元素 
	}

### 二，List接口
List继承自Collection接口。List是有序并且可以重复的Collection，使用此接口能够精确的控制每个元素插入的位置。用户能够使用索引（元素在List中的位置，类似于数组下标）来访问List中的元素。<br>
实现List接口的常用类有LinkedList，ArrayList，Vector和Stack。<br>
List的列表迭代器：<br>

	//Returns a list iterator over the elements in this list (in proper sequence).
	ListIterator<E>	listIterator()
	//Returns a list iterator over the elements in this list (in proper sequence), starting at the specified position in the list
	ListIterator<E>	listIterator(int index)
	
	//列表迭代器也是Interator的一个实现，这个实现支持在迭代的时候对列表进行操作
	//具体方法实现参照下图javaAPI
	for (ListIterator it = list.listIterator();it.hasNext();) {
           Object object = it.next();
    }


**Interface ListIterator<E>**

![](http://img.blog.csdn.net/20170416222800338?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhbzQ2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
<br>

**List接口的子类**

**1.ArrayList**

JavaAPI解释：<br>
Resizable-array implementation of the List interface. Implements all optional list operations, and permits all elements, including null. In addition to implementing the List interface, this class provides methods to manipulate the size of the array that is used internally to store the list. (This class is roughly equivalent to Vector, except that it is unsynchronized.)<br>

简单说ArrayList就是一个可变长的数组结构。<br>
ArrayList实现了List接口的所有方法。（好像说了很多又好像是什么都没说）<br>
ArrayList允许所有元素，包括null。ArrayList没有同步。<br>
ArrayList是非同步的（unsynchronized）。

**2.LinkedList**

**3.Vector**
Vector非常类似ArrayList，但是Vector是同步的。因为Vector是同步的，当一个Iterator被创建而且正在被使用，另一个线程改变了Vector的状态（例如，添加或删除了一些元素），这时调用Iterator的方法时将抛出ConcurrentModificationException，因此**必须**捕获该异常。

