---
layout: post
category: 
- java
title: Java多线程&并发-Queue
tags: java，Thread
---
## Java中并发Queue种类

在并发编程中我们有时候需要使用线程安全的队列。如果我们要实现一个线程安全的队列有两种实现方式一种是使用**阻塞算法**，另一种是使用**非阻塞算法**。<br>
**阻塞算法:** BlockingQueue接口实现类，
**非阻塞算法:** ConcurrentLinkedQueue类

## ConcurrentLinkedQueue

ConcurrentLinkedQueue是一个基于链接节点的无界线程安全队列，它采用先进先出的规则对节点进行排序，当我们添加一个元素的时候，它会添加到队列的尾部，当我们获取一个元素时，它会返回队列头部的元素。因为是无锁方式实现，所以适合在高并发场景，性能要优于BlockingQueue接口实现的子类对象

### 1,使用CAS算法来入队

	public boolean offer(E e) {
	        if (e == null) throw new NullPointerException();
	        //入队前，创建一个入队节点
	        Node</e><e> n = new Node</e><e>(e);
	        retry:
	        //死循环，入队不成功反复入队。
	        for (;;) {
	            //创建一个指向tail节点的引用
	            Node</e><e> t = tail;
	            //p用来表示队列的尾节点，默认情况下等于tail节点。
	            Node</e><e> p = t;
	            for (int hops = 0; ; hops++) {
	            //获得p节点的下一个节点。
	                Node</e><e> next = succ(p);
	     			//next节点不为空，说明p不是尾节点，需要更新p后在将它指向next节点
	                if (next != null) {
	                   //循环了两次及其以上，并且当前节点还是不等于尾节点
	                    if (hops > HOPS && t != tail)
	                        continue retry;
	                    p = next;
	                }
	                //如果p是尾节点，则设置p节点的next节点为入队节点。
	                else if (p.casNext(null, n)) {
	                  //如果tail节点有大于等于1个next节点，则将入队节点设置成tair节点，更新失败了也没关系，因为失败了表示有其他线程成功更新了tair节点。
						if (hops >= HOPS)
	                        casTail(t, n); // 更新tail节点，允许失败
	                    return true;
	                }
	               // p有next节点,表示p的next节点是尾节点，则重新设置p节点
	                else {
	                    p = succ(p);
	                }
	            }
	        }
	    }

## BlockingQueue接口

BlockingQueue 是个接口，需要使用它的实现之一来使用 BlockingQueue。java.util.concurrent 具有以下 BlockingQueue 接口的实现(Java 6)：<br>

* ArrayBlockingQueue
* DelayQueue
* LinkedBlockingQueue
* PriorityBlockingQueue
* SynchronousQueue

BlockingQueue 通常用于一个线程生产对象，而另外一个线程消费这些对象的场景。<br>
![](https://timgsa.baidu.com/timg?image&quality=80&size=b9999_10000&sec=1503846043589&di=49c2987da1f56b640003a38eec71028c&imgtype=0&src=http%3A%2F%2Fstatic.oschina.net%2Fuploads%2Fspace%2F2014%2F0106%2F211417_F2gn_1421353.png "图片来自网络")

**BlockingQueue 接口中的方法**<br>
BlockingQueue 具有 4 组不同的方法用于插入、移除以及对队列中的元素进行检查。如果请求的操作不能得到立即执行的话，每个方法的表现也不同。<br>

![](http://img.blog.csdn.net/20170827202620381 "图片来自网络")

四组不同的行为方式解释：<br>

1. **抛异常：**如果试图的操作无法立即执行，抛一个异常。
2. **特定值：**如果试图的操作无法立即执行，返回一个特定的值(常常是 true / false)。
3. **阻塞：**如果试图的操作无法立即执行，该方法调用将会发生阻塞，直到能够执行。
4. **超时：**如果试图的操作无法立即执行，该方法调用将会发生阻塞，直到能够执行，但等待时间不会超过给定值。返回一个特定值以告知该操作是否成功(典型的是 true / false)。

**注意：**

1. 无法向一个 BlockingQueue 中插入 null。如果你试图插入 null，BlockingQueue 将会抛出一个 NullPointerException。
2. 可以访问到 BlockingQueue 中的所有元素，而不仅仅是开始和结束的元素。

### 1,数组阻塞队列 ArrayBlockingQueue

ArrayBlockingQueue 是一个**有界**的**阻塞**队列，其内部实现是将对象放到一个数组里。有界也就意味着，它不能够存储无限多数量的元素。它有一个同一时间能够存储元素数量的上限。你可以在对其初始化的时候设定这个上限，但之后就无法对这个上限进行修改了(注：因为它是基于数组实现的，也就具有数组的特性：一旦初始化，大小就无法修改)。<br>
ArrayBlockingQueue 内部以 FIFO(先进先出)的顺序对元素进行存储。队列中的头元素在所有元素之中是放入时间最久的那个，而尾元素则是最短的那个。<br>
ArrayBlockingQueue在生产者放入数据和消费者获取数据，都是共用同一个锁对象，由此也意味着两者无法真正并行运行。

### 2,链阻塞队列 LinkedBlockingQueue
LinkedBlockingQueue 内部以一个链式结构(链接节点)对其元素进行存储。如果需要的话，这一链式结构可以选择一个上限。如果没有定义上限，将使用 Integer.MAX_VALUE 作为上限。<br>
LinkedBlockingQueue 内部以 FIFO(先进先出)的顺序对元素进行存储。队列中的头元素在所有元素之中是放入时间最久的那个，而尾元素则是最短的那个。<br>
通过putLock和takeLock两个锁进行同步，两个锁分别实例化notFull和notEmpty两个Condtion，用来协调多线程的存取动作。其中某些方法(如remove,toArray,toString,clear等)的同步需要同时获得这两个锁，并且总是先putLock.lock紧接着takeLock.lock(在同一方法fullyLock中)，这样的顺序是为了避免可能出现的死锁情况。

### 3,延迟队列 DelayQueue
Delayed 元素的一个**无界阻塞**队列，只有在延迟期满时才能从中提取元素。该队列的头部 是延迟期满后保存时间最长的 Delayed 元素。如果延迟都还没有期满，则队列没有头部，并且 poll 将返回 null。当一个元素的getDelay(TimeUnit.NANOSECONDS) 方法返回一个小于或等于零的值时，则出现期满。java.util.concurrent.Delayed 接口定义：

		public interface Delayed extends Comparable<Delayed< {  
		  
		 public long getDelay(TimeUnit timeUnit);  
		  
		} 

DelayQueue 将会在每个元素的 getDelay() 方法返回的值的时间段之后才释放掉该元素。如果返回的是 0 或者负值，延迟将被认为过期，该元素将会在 DelayQueue 的下一次 take  被调用的时候被释放掉。
传递给 getDelay 方法的 getDelay 实例是一个枚举类型，它表明了将要延迟的时间段。TimeUnit 枚举将会取以下值：

	DAYS  
	HOURS  
	MINUTES  
	SECONDS  
	MILLISECONDS  
	MICROSECONDS  
	NANOSECONDS 

Delayed 接口也继承了 java.lang.Comparable 接口，这也就意味着 Delayed 对象之间可以进行对比。这个可能在对 DelayQueue 队列中的元素进行排序时有用，因此它们可以根据过期时间进行有序释放。

### 4,具有优先级的阻塞队列 PriorityBlockingQueue

PriorityBlockingQueue 是一个无界的并发队列。它使用了和类 java.util.PriorityQueue 一样的排序规则。你无法向这个队列中插入 null 值。<br>
所有插入到 PriorityBlockingQueue 的元素必须实现 java.lang.Comparable 接口。因此该队列中元素的排序就取决于你自己的 Comparable 实现。
注意 PriorityBlockingQueue 对于具有相等优先级(compare() == 0)的元素并不强制任何特定行为。<br>
如果你从一个 PriorityBlockingQueue 获得一个 Iterator 的话，该 Iterator 并不能保证它对元素的遍历是以优先级为序的。

### 5,同步队列 SynchronousQueue
SynchronousQueue 是一个特殊的队列，它的内部同时只能够容纳单个元素。如果该队列已有一元素的话，试图向队列中插入一个新元素的线程将会阻塞，直到另一个线程将该元素从队列中抽走。同样，如果该队列为空，试图向队列中抽取一个元素的线程将会阻塞，直到另一个线程向队列中插入了一条新的元素。<br>
