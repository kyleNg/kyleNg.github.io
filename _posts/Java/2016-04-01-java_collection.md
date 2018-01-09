---
layout: post
category: 
- java
title: Java集合
tags: java
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
	      +--java.util.LinkedHashSet[C]
	   +--java.util.SortedSet [I]
	      +--java.util.TreeSet [C]

	java.util.Map [I]
	+--java.util.SortedMap [I]
	   +--java.util.TreeMap [C]
	+--java.util.Hashtable [C]
	+--java.util.HashMap [C]
	   +--java.util.LinkedHashMap [C]

	[I]：接口	[C]：类

接下来逐一学习上面的集合类

### 一、Collection接口
Collection是最基本的集合接口，一个Collection代表一组Object的集合，这些Object被称作Collection的元素。<br>
Collection是一个接口，用以提供规范定义，不能被实例化使用。但是接口里面有几个类是所有实现它的集合都有的方法。<br>

![](http://img.blog.csdn.net/20170416210825924?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhbzQ2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

**迭代器**(interator)<br>
不论Collection的实际类型如何，它都支持一个iterator()的方法，该方法返回一个迭代对象，使用该迭代对象即可逐一访问Collection中每一个元素。典型的用法如下：

	Iterator it = collection.iterator(); // 获得一个迭代子 
	while(it.hasNext()) { 
		// 注意：在迭代器里面操作集合会有异常
		//obj.add("abc"); //java.util.ConcurrentModificationException
	　　Object obj = it.next(); // 得到下一个元素 
	}

### 二、List接口
List继承自Collection接口。List是**有序**并且**可以重复**的Collection，使用此接口能够精确的控制每个元素插入的位置。用户能够使用索引（元素在List中的位置，类似于数组下标）来访问List中的元素。<br>
实现List接口的常用类有LinkedList，ArrayList，Vector和Stack。<br>
List的列表迭代器：<br>

	//Returns a list iterator over the elements in this list (in proper sequence).
	ListIterator<E>	listIterator()
	starting at the specified position in the list
	ListIterator<E>	listIterator(int index)
	
	//列表迭代器也是Interator的一个实现，这个实现支持在迭代的时候对列表进行操作
	//具体方法实现参照下图javaAPI
	for (ListIterator it = list.listIterator();it.hasNext();) {
	       Object object = it.next();
	}

**Interface ListIterator**

![](http://img.blog.csdn.net/20170416222800338?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvY2hhbzQ2NjY=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)
<br>

**List接口的子类**

**1.ArrayList**

ArrayList就是一个可变长的数组结构(动态数组)。初始化数组长度为10<br>
ArrayList允许所有元素，包括null。<br>
ArrayList是非同步的（unsynchronized）。<br>
ArrayList擅长随机访问。

**2.LinkedList**

LinkedList实现了List接口，是一个双向链表，允许null元素。此外LinkedList提供额外的get，remove，insert方法在 LinkedList的首部或尾部。这些操作使LinkedList可被用作堆栈（stack），队列（queue）或双向队列（deque）。<br>
**注意**LinkedList没有同步方法。如果多个线程同时访问一个List，则必须自己实现访问同步。一种解决方法是在创建List时构造一个同步的List：

	List list = Collections.synchronizedList(new LinkedList(...));

**3.Vector**

Vector非常类似ArrayList，但是Vector是同步的。因为Vector是同步的，当一个Iterator被创建而且正在被使用，另一个线程改变了Vector的状态（例如，添加或删除了一些元素），这时调用Iterator的方法时将抛出ConcurrentModificationException，因此**必须**捕获该异常。

**4.Stack**

Stack 类表示后进先出（LIFO）的对象堆栈。它通过五个操作对类 Vector 进行了扩展 ，允许将向量视为堆栈。它提供了通常的 push 和 pop 操作，以及取堆栈顶点的 peek 方法、测试堆栈是否为空的 empty 方法、在堆栈中查找项并确定到堆栈顶距离的 search 方法。

### 三、Set接口

一个不**包含重复元素**的 collection。更确切地讲，set 不包含满足 e1.equals(e2) 的元素对 e1 和 e2，并且最多包含一个 null 元素。正如其名称所暗示的，此接口模仿了数学上的 set 抽象。<br>
因为Set的这个制约，在使用Set集合的时候，应该注意：  
1，为Set集合里的元素的实现类实现一个有效的equals(Object)方法。  
2，对Set的构造函数，传入的Collection参数不能包含重复的元素。


**Set接口的子类**

**1.HashSet类**

此类实现 Set 接口，由哈希表（实际上是一个 HashMap 实例，也就是HashMap的key组成了HashSet）支持。它**不保证 set 的迭代顺序**（区别于List接口的子类）；特别是它不保证该顺序恒久不变。此类允许使用 null 元素。  
**注意**，此实现不是同步的。若想构造一个同步的Hashset：  

    Set s = Collections.synchronizedSet(new HashSet(...));


**2.TreeSet类**

TreeSet是SortedSet接口的实现类，TreeSet可以确保集合元素处于排序状态。使用元素的自然顺序对元素进行排序，或者根据创建 set 时提供的 Comparator 进行排序，具体取决于使用的构造方法。 

**注意**，如果要正确实现 Set 接口，则 set 维护的顺序（无论是否提供了显式比较器）必须与 equals 一致。（关于与 equals 一致 的精确定义，请参阅 Comparable 或 Comparator。）这是因为 Set 接口是按照 equals 操作定义的，但 TreeSet 实例使用它的 compareTo（或 compare）方法对所有元素进行比较，因此从 set 的观点来看，此方法认为相等的两个元素就是相等的。即使 set 的顺序与 equals 不一致，其行为也是 定义良好的；它只是违背了 Set 接口的常规协定。

**3.EnumSet类**

是枚举的专用Set。所有的元素都是枚举类型。

**2.LinkedHashSet类**

### 四、Map接口

Map与List、Set接口不同，它是由一系列键值对组成的集合，提供了key到Value的映射。同时它也没有继承Collection。在Map中它保证了key与value之间的一一对应关系。也就是说一个key对应一个value，所以它不能存在相同的key值，当然value值可以相同。实现map的有：HashMap、TreeMap、HashTable、Properties、EnumMap。

**1.HashMap类**
以哈希表数据结构实现，查找对象时通过哈希函数计算其位置，它是为快速查询而设计的，其内部定义了一个hash表数组（Entry[] table），元素会通过哈希转换函数将元素的哈希地址转换成数组中存放的索引，如果有冲突，则使用散列链表的形式将所有相同哈希地址的元素串起来，可能通过查看HashMap.Entry的源码它是一个单链表结构。

**2.Hashtable类**
也是以哈希表数据结构实现的，解决冲突时与HashMap也一样也是采用了散列链表的形式，不过性能比HashMap要低

**3.TreeMap类**
TreeMap就是一个红黑树数据结构，每个key-value对即作为红黑树的一个节点。TreeMap存储key-value对(节点)时，需要根据key对节点进行排序。TreeMap可以保证所有的
key-value对处于有序状态。同样，TreeMap也有两种排序方式: 自然排序、定制排序

### 五、Queue

队列，它主要分为两大类，一类是阻塞式队列，队列满了以后再插入元素则会抛出异常，主要包括ArrayBlockQueue、PriorityBlockingQueue、LinkedBlockingQueue。另一种队列则是双端队列，支持在头、尾两端插入和移除元素，主要包括：ArrayDeque、LinkedBlockingDeque、LinkedList。

### 六、对集合操作的工具类

总结
如果涉及到堆栈，队列等操作，应该考虑用List，对于需要快速插入，删除元素，应该使用LinkedList，如果需要快速随机访问元素，应该使用ArrayList。
如果程序在单线程环境中，或者访问仅仅在一个线程中进行，考虑非同步的类，其效率较高，如果多个线程可能同时操作一个类，应该使用同步的类。
在除需要排序时使用TreeSet,TreeMap外,都应使用HashSet,HashMap,因为他们 的效率更高。
要特别注意对哈希表的操作，作为key的对象要正确复写equals和hashCode方法。

容器类仅能持有对象引用（指向对象的指针），而不是将对象信息copy一份至数列某位置。一旦将对象置入容器内，便损失了该对象的型别信息。
尽量返回接口而非实际的类型，如返回List而非ArrayList，这样如果以后需要将ArrayList换成LinkedList时，客户端代码不用改变。这就是针对抽象编程。
 
注意：
1、Collection没有get()方法来取得某个元素。只能通过iterator()遍历元素。<br>
2、Set和Collection拥有一模一样的接口。<br>
3、List，可以通过get()方法来一次取出一个元素。使用数字来选择一堆对象中的一个，get(0)...(add/get)<br>
4、一般使用ArrayList。用LinkedList构造堆栈stack、队列queue。<br>
5、Map用 put(k,v) / get(k)，还可以使用containsKey() / containsValue()来检查其中是否含有某个key / value。
HashMap会利用对象的hashCode来快速找到key。