---
layout: post
category: 
- java
title: Java多线程&并发
tags: java，Thread
---
## Java实现多线程概述

Java 5以前实现多线程有两种实现方法：一种是**继承Thread类**；另一种是**实现Runnable接口**。两种方式都要通过重写run()方法来定义线程的行为。推荐使用后者，因为Java中的继承是单继承，一个类有一个父类，如果继承了Thread类就无法再继承其他类了，显然使用Runnable接口更为灵活。

Java 5以后创建线程还有第三种方式：**实现Callable接口**，该接口中的call方法可以在线程执行结束时产生一个返回值

### 1,继承Thread类

**步骤：**

1. 定义一个类继承 Thread 类。
2. 覆盖 Thread 类中的run方法（用于封装线程要运行的代码）。
3. 直接创建Thread的子类对象创建线程的子类对象创建线程。
4. 调用 start() 方法开启线程并调用的任务，并执行run方法。

		public class ThreadTest01 {
			public static void main(String[] args) {
				//创建线程
				ThreadExample t1 = new ThreadExample("线程1");
				ThreadExample t2 = new ThreadExample("线程2");
				// 启动线程
				t1.start();
				t2.start();
			}
		}
		class ThreadExample extends Thread{
			public ThreadExample(String name) {
				// 调用父类构造方法为给线程名赋值
				super(name);
			}
			@Override
			public void run() {
				for (int i = 0; i < 10; i++) {
					// Thread.currentThread().getName() 获取当前线程名称
					System.out.println(Thread.currentThread().getName() + "-----" + i);
				}
			}
		}

### 2,实现Runnable接口

**步骤：**

1. 定义类实现Runnable接口。
2. 覆盖接口中的run方法（用于封装线程要运行的代码）。
3. 通过Thread类创建线程对象。
4. 将实现了Runnable接口的子类对象作为实际参数传递给Thread类中的构造函数。让线程对象明确要运行的run方法所属的对象。
5. 调用Thread对象的start方法。开启线程，并运行Runnable接口子类中的run方法。

		public class ThreadTest02 {
			public static void main(String[] args) {
				new Thread(new Runnable() {
					@Override
					public void run() {
						for (int i = 0; i < 10; i++) {
							// Thread.currentThread().getName() 获取当前线程名称
							System.out.println(Thread.currentThread().getName() + "-----" + i);
						}
					}
				}, "线程1").start();
				
				new Thread(new Runnable() {
					@Override
					public void run() {
						for (int i = 0; i < 10; i++) {
							// Thread.currentThread().getName() 获取当前线程名称
							System.out.println(Thread.currentThread().getName() + "-----" + i);
						}
					}
				}, "线程2").start();
			}
		}

### 3,实现Runnable接口与继承Thread类比较

1. 可以避免由于Java的单继承特性而带来的局限
2. 增强程序的健壮性，代码能够被多个程序共享，代码与数据是独立的
3. 适合多个相同程序代码的线程区处理同一资源的情况

对于第三点我们来举个例子说明可能会更直观：案例模拟了火车站买票场景，三个窗口一起买十张车票。

**用继承Thread类实现**

	class MyThread extends Thread{  
	    private int ticket = 5;  
	    public void run(){  
	        for (int i=0;i<10;i++)  
	        {  
	            if(ticket > 0){  
	                System.out.println("ticket = " + ticket--);  
	            }  
	        }  
	    }  
	}  
	public class ThreadDemo{  
	    public static void main(String[] args){  
	        new MyThread().start();  
	        new MyThread().start();  
	        new MyThread().start();  
	    }  
	} 

**运行结果：**<br>
ticket = 5<br>
ticket = 5<br>
ticket = 5<br>
ticket = 4<br>
ticket = 4<br>
ticket = 3<br>
ticket = 4<br>
ticket = 2<br>
ticket = 3<br>
ticket = 1<br>
ticket = 3<br>
ticket = 2<br>
ticket = 2<br>
ticket = 1<br>
ticket = 1<br>

每个线程单独卖了5张票，即独立的完成了买票的任务，但实际应用中，比如火车站售票，需要多个线程去共同完成任务，在本例中，即多个线程共同买5张票。

**实现Runnable借口实现**

	class MyThread implements Runnable{  
	    private int ticket = 5;  
	    public void run(){  
	        for (int i=0;i<10;i++)  
	        {  
	            if(ticket > 0){  
	                System.out.println("ticket = " + ticket--);  
	            }  
	        }  
	    }  
	}  
	  
	public class RunnableDemo{  
	    public static void main(String[] args){  
	        MyThread my = new MyThread();  
	        new Thread(my).start();  
	        new Thread(my).start();  
	        new Thread(my).start();  
	    }  
	} 

**运行结果：**<br>
ticket = 5<br>
ticket = 4<br>
ticket = 1<br>
ticket = 3<br>
ticket = 2<br>

ticket输出的顺序并不是54321，这是因为线程执行的时机难以预测。<br>
虽然第二种方式看上去满足了我们的需求但是由于3个Thread对象共同执行一个Runnable对象中的代码，因此可能会造成线程的不安全当我们在MyThresd类的run方法中让线程睡眠一定时间后就会出现0号票或者-1号票或者同号票被卖出的情况。这个问题在下面同步将会继续讨论。


### 4,一个线程的生命周期
http://www.jianshu.com/p/40d4c7aebd66

![](http://upload-images.jianshu.io/upload_images/1689841-2d62f26c104e2ad0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "图片来自网络")

* **新建状态:**<br>
使用 new 关键字和 Thread 类或其子类建立一个线程对象后，该线程对象就处于新建状态。它保持这个状态直到程序 start() 这个线程。
* **就绪状态:**<br>
当线程对象调用了start()方法之后，该线程就进入就绪状态。就绪状态的线程处于就绪队列中，要等待JVM里线程调度器的调度。
* **运行状态:**<br>
如果就绪状态的线程获取 CPU 资源，就可以执行 run()，此时线程便处于运行状态。处于运行状态的线程最为复杂，它可以变为阻塞状态、就绪状态和死亡状态。
* **阻塞状态:**<br>
如果一个线程执行了sleep（睡眠）、suspend（挂起）等方法，失去所占用资源之后，该线程就从运行状态进入阻塞状态。在睡眠时间已到或获得设备资源后可以重新进入就绪状态。可以分为三种：
等待阻塞：运行状态中的线程执行 wait() 方法，使线程进入到等待阻塞状态。
同步阻塞：线程在获取 synchronized 同步锁失败(因为同步锁被其他线程占用)。
其他阻塞：通过调用线程的 sleep() 或 join() 发出了 I/O 请求时，线程就会进入到阻塞状态。当sleep() 状态超时，join() 等待线程终止或超时，或者 I/O 处理完毕，线程重新转入就绪状态。
* **死亡状态:**<br>
一个运行状态的线程完成任务或者其他终止条件发生时，该线程就切换到终止状态。

## 同步&锁

### 1,同步

为了了解同步，那么就要先了解以下几个概念：<br>

**多线程安全问题：**<br>
发现一个线程在执行多条语句时，并运算同一个数据时，在执行过程中，其他线程参与进来，并操作了这个数据。导致到了错误数据的产生，比如上面卖票的例子未解决的问题我们继续讨论<br>
现将代码做如下修改

	class MyThread implements Runnable{  
	    private int ticket = 5;  
	    public void run(){  
	        for (int i=0;i<10;i++)  
	        {  
	            if(ticket > 0){  
	            	// 让线程睡眠三秒
	            	try {
						Thread.sleep(3000);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
	                System.out.println("ticket = " + ticket--);  
	            } 
	            
	        }  
	    }  
	}  
	  
	public class RunnableDemo{  
	    public static void main(String[] args){  
	        MyThread my = new MyThread();  
	        new Thread(my).start();  
	        new Thread(my).start();  
	        new Thread(my).start();  
	    }  
	} 

**运行结果：**<br>
ticket = 5<br>
ticket = 4<br>
ticket = 3<br>
ticket = 2<br>
ticket = 1<br>
ticket = 1<br>
ticket = 0<br>

这种情况的出现是由于，一个线程在判断ticket>0后，还没有来得及减1，另一个线程拿到同样数值的ticket>0，这时两个线程同时做减1操作就会得到卖出去去两张1号票的情况，0号票被卖出也同理。<br>
为了解决这个问题就引入了同步的概念:<br>
在并发编程中，多线程同时并发访问的资源叫做临界资源，当多个线程同时访问对象并要求操作相同资源时，分割了原子操作就有可能出现数据的不一致或数据不完整的情况，为避免这种情况的发生，我们会采取同步机制，以确保在某一时刻，方法内只允许有一个线程。<br>

synchronized关键字，可以修饰一个方法或者一个代码块。synchronized 方法和 synchronized 块<br>
同步代码块和同步函数的区别？<br>

+ 同步代码块使用的锁可以是任意对象
+ 同步函数使用的锁是this，静态同步函数的锁是该类的字节码文件对象

		class MyThread implements Runnable{  
		    private int ticket = 5;
			// 1，synchronized 方法
		    public synchronized void run(){  
		        for (int i=0;i<10;i++)  
		        {  
		            if(ticket > 0){  
		            	// 让线程睡眠三秒
		            	try {
							Thread.sleep(3000);
						} catch (InterruptedException e) {
							e.printStackTrace();
						}
		                System.out.println("ticket = " + ticket--);
						// 2，synchronized 块
						// synchronized (this) {
						//		System.out.println("ticket = " + ticket--);
						//}
		            }
		        }  
		    }  
		}  
		  
		public class RunnableDemo{  
		    public static void main(String[] args){  
		        MyThread my = new MyThread();  
		        new Thread(my).start();  
		        new Thread(my).start();  
		        new Thread(my).start();  
		    }  
		} 


**说明：**<br>

1. 如果同一个方法内同时有两个或更多线程，则每个线程有自己的局部变量拷贝

2. 类的每个实例都有自己的对象级别锁。当一个线程访问实例对象中的synchronized同步代码块或同步方法时，该线程便获取了该实例的对象级别锁，其他线程这时如果要访问synchronized同步代码块或同步方法，便需要阻塞等待，直到前面的线程从同步代码块或方法中退出，释放掉了该对象级别锁

3. 访问同一个类的不同实例对象中的同步代码块，不存在阻塞等待获取对象锁的问题，因为它们获取的是各自实例的对象级别锁，相互之间没有影响

4. 在被static修饰的静态方法如果加上synchronized就是类级别锁，它用于控制对static成员变量以及static方法的并发访问。具体用法与对象级别锁相似，取得的锁很特别，是**当前调用这个方法的对象所属的类**（Class，而不再是由这个Class产生的某个具体对象了）

5. 使用synchronized（obj）同步语句块，可以获取指定对象上的对象级别锁。obj为对象的引用，如果获取了obj对象上的对象级别锁，在并发访问obj对象时时，便会在其synchronized代码处阻塞等待，直到获取到该obj对象的对象级别锁。当obj为this时，便是获取当前对象的对象级别锁

案例：此案例对第三点和第四点做一个示例说明<br>

	public class MultiThread {
		private int num = 0;
		/** static */
		// 当给此方法加上static修饰的时候就变成类级别的锁就会实现同步，上面4，5两点说明在此示例有很好的展示
		public synchronized void printNum(String tag){
			try {
				if(tag.equals("a")){
					num = 100;
					System.out.println("tag a, set num over!");
					Thread.sleep(1000);
				} else {
					num = 200;
					System.out.println("tag b, set num over!");
				}
				System.out.println("tag " + tag + ", num = " + num);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
		//注意观察run方法输出顺序
		public static void main(String[] args) {
			//俩个不同的对象
			final MultiThread m1 = new MultiThread();
			final MultiThread m2 = new MultiThread();
			Thread t1 = new Thread(new Runnable() {
				@Override
				public void run() {
					m1.printNum("a");
				}
			});
			Thread t2 = new Thread(new Runnable() {
				@Override 
				public void run() {
					m2.printNum("b");
				}
			});		
			t1.start();
			t2.start();
	}

**运行结果：**<br>
tag a, set num over!<br>
tag b, set num over!<br>
tag b, num = 200<br>
tag a, num = 100<br>

从运行结果中我们可以看出因为是不同的对象去调用printNum方法，所以synchronized关键字并没有实现同步，两个线程还是并发同时的。如果加上static修饰同步方法，那么锁就会变成当前调用这个方法所属的类，从而实现同步。<br>

案例：多线程访问单例模式<br>
单例模式一般分为两种饿汉模式和懒汉模式，这里只对懒汉模式（延迟加载）做讨论

		class Single{
			private static Single s = null;
			private Single(){}
			public static Single getInstance(){
				if(s == null)
					s = new Single();
				return s;
			}
		}

这是普通的延迟加载单例模式，当有多线程访问的时候因为对`s = new Single();`共性数据进行多条语句的操作，所以容易出现线程安全问题。也就是说在一个线程判断s为null之后还没开始new之前可能会有另一个进程也判断s为null进如条件，这样就会产生两个对象，导致程序不是单例的。<br>
解决办法：在new对象时候实现同步

		class Single{
			private static Single s = null;
			private Single(){}
			public static Single getInstance(){
				// 为了效率问题不需要每次进入方法都走同步方法，
				// 同步方法虽然能实现线程安全，但是带来的相对降低性能，因为判断锁需要消耗资源
				if(s == null){
					// 因为是static修饰所以锁应该用该对象的类
					synchronized(Single.class){
						if(s == null)
							s = new Single();
					}
				}
				return s;
			}
		}

### 2,死锁

通过上面同步的讲解相信我们对锁的概念已经有了初步的认识。<br>
但是当线程需要同时持有多个锁时，有可能产生**死锁**<br>

案例：同步的嵌套死锁

		class Test implements Runnable{
			private boolean flag;
			Test(boolean flag){
				this.flag = flag;
			}
			public void run(){
				if(flag){
					while(true)
						// 先获取locka锁
						synchronized(MyLock.locka)
						{
							System.out.println(Thread.currentThread().getName()+"..if   locka....");
							// 再获取lockb锁
							synchronized(MyLock.lockb){
								System.out.println(Thread.currentThread().getName()+"..if   lockb....");
							}
						}
				}
				else{
					while(true)
						// 先获取lockb锁
						synchronized(MyLock.lockb){
							System.out.println(Thread.currentThread().getName()+"..else  lockb....");
							// 再获取locka锁
							synchronized(MyLock.locka){
								System.out.println(Thread.currentThread().getName()+"..else   locka....");
							}
						}
				}
			}
		}
		
		class MyLock{
			// 为了创建两个锁
			public static final Object locka = new Object();
			public static final Object lockb = new Object();
		}
		class DeadLockTest{
			public static void main(String[] args){
				Test a = new Test(true);
				Test b = new Test(false);
				Thread t1 = new Thread(a);
				Thread t2 = new Thread(b);
				t1.start();
				t2.start();
			}
		}

**运行结果：**<br>
Thread-0..if   locka....<br>
Thread-1..else  lockb....<br>

从结果中可以看出，由于两个线程都在试图获取对方的锁，但对方都没有释放自己的锁，因而便产生了死锁。

大部分代码并不容易产生死锁，死锁可能在代码中隐藏相当长的时间，等待不常见的条件地发生，但即使是很小的概率，一旦发生，便可能造成毁灭性的破坏。避免死锁是一件困难的事，遵循以下原则有助于规避死锁：<br>
> 1. 只在必要的最短时间内持有锁，考虑使用同步语句块代替整个同步方法；
> 2. 尽量编写不在同一时刻需要持有多个锁的代码，如果不可避免，则确保线程持有第二个锁的时间尽量短暂；
> 3. 创建和使用一个大锁来代替若干小锁，并把这个锁用于互斥，而不是用作单个对象的对象级别锁；

## 线程之间通信

Object是所有类的超类，它有5个方法组成了等待/通知机制的核心：notify（）、notifyAll（）、wait（）、wait（long）和wait（long，int）。在Java中，所有的类都从Object继承而来，因此，所有的类都拥有这些共有方法可供使用。而且，由于他们都被声明为final，因此在子类中不能覆写任何一个方法。<br>

1、wait（）

```
public final void wait()  throws InterruptedException,IllegalMonitorStateException
```

该方法用来将当前线程置入休眠状态，直到接到通知或被中断为止。在调用wait（）之前，线程必须要获得该对象的对象级别锁，即只能在同步方法或同步块中调用wait（）方法。进入wait（）方法后，当前线程释放锁。在从wait（）返回前，线程与其他线程竞争重新获得锁。如果调用wait（）时，没有持有适当的锁，则抛出IllegalMonitorStateException，它是RuntimeException的一个子类，因此，不需要try-catch结构。

2、notify（）

```
public final native void notify() throws IllegalMonitorStateException
```

该方法也要在同步方法或同步块中调用，即在调用前，线程也必须要获得该对象的对象级别锁，的如果调用notify（）时没有持有适当的锁，也会抛出IllegalMonitorStateException。

该方法用来通知那些可能等待该对象的对象锁的其他线程。如果有多个线程等待，则线程规划器任意挑选出其中一个wait（）状态的线程来发出通知，并使它等待获取该对象的对象锁（notify后，当前线程不会马上释放该对象锁，wait所在的线程并不能马上获取该对象锁，要等到程序退出synchronized代码块后，当前线程才会释放锁，wait所在的线程也才可以获取该对象锁），但不惊动其他同样在等待被该对象notify的线程们。当第一个获得了该对象锁的wait线程运行完毕以后，它会释放掉该对象锁，此时如果该对象没有再次使用notify语句，则即便该对象已经空闲，其他wait状态等待的线程由于没有得到该对象的通知，会继续阻塞在wait状态，直到这个对象发出一个notify或notifyAll。这里需要注意：它们等待的是被notify或notifyAll，而不是锁。这与下面的notifyAll（）方法执行后的情况不同。 

3、notifyAll（）

```
public final native void notifyAll() throws IllegalMonitorStateException
```

该方法与notify（）方法的工作方式相同，重要的一点差异是：

notifyAll使所有原来在该对象上wait的线程统统退出wait的状态（即全部被唤醒，不再等待notify或notifyAll，但由于此时还没有获取到该对象锁，因此还不能继续往下执行），变成等待获取该对象上的锁，一旦该对象锁被释放（notifyAll线程退出调用了notifyAll的synchronized代码块的时候），他们就会去竞争。如果其中一个线程获得了该对象锁，它就会继续往下执行，在它退出synchronized代码块，释放锁后，其他的已经被唤醒的线程将会继续竞争获取该锁，一直进行下去，直到所有被唤醒的线程都执行完毕。

4、wait（long）和wait（long,int）

显然，这两个方法是设置等待超时时间的，后者在超值时间上加上ns，精度也难以达到，因此，该方法很少使用。对于前者，如果在等待线程接到通知或被中断之前，已经超过了指定的毫秒数，则它通过竞争重新获得锁，并从wait（long）返回。另外，需要知道，如果设置了超时时间，当wait（）返回时，我们不能确定它是因为接到了通知还是因为超时而返回的，因为wait（）方法不会返回任何相关的信息。但一般可以通过设置标志位来判断，在notify之前改变标志位的值，在wait（）方法后读取该标志位的值来判断，当然为了保证notify不被遗漏，我们还需要另外一个标志位来循环判断是否调用wait（）方法。

案例：等待唤醒机制

	//资源
	class Resource
	{
		String name;
		String sex;
		// 用于标记这个资源是否需要再生产，如果flag为true就说明已经生产过
		boolean flag = false;
	}
	
	//输入
	class Input implements Runnable{
		Resource r ;
		Input(Resource r){
			this.r = r;
		}
		public void run(){
			int x = 0;
			while(true){
				// 输入输出同时操作资源对象需要加上锁，锁需要一样。
				synchronized(r){
					// 如果flag为true说明已经生产过直接wait进入休眠状态，并且释放锁。
					if(r.flag)
						try{r.wait();}catch(InterruptedException e){}
					// 如果flag是false就给资源赋值
					if(x==0){
						r.name = "小明";
						r.sex = "男";
					}else{
						r.name = "小丽";
						r.sex = "女";
					}
					// 赋值之后将flag设置成true
					r.flag = true;
					// 唤醒等待的输出线程获取锁
					r.notify();
				}
				x = (x+1)%2;
			}
		}
	}
	//输出
	class Output implements Runnable{
		Resource r;
		Output(Resource r){
			this.r = r;
		}
		public void run(){
			while(true){
				synchronized(r){
					// 如果flag是false则说明输入还没完成，进入等待状态
					if(!r.flag)
						try{r.wait();}catch(InterruptedException e){}
					// 如果flag是true则直接输出并把flag设置成false
					System.out.println(r.name+"....."+r.sex);
					r.flag = false;
					// 唤醒等待锁的输入线程
					r.notify();
				}
			}
		}
	}
	
	class  ResourceDemo2{
		public static void main(String[] args){
			//创建资源。
			Resource r = new Resource();
			//创建任务。
			Input in = new Input(r);
			Output out = new Output(r);
			//创建线程，执行路径。
			Thread t1 = new Thread(in);
			Thread t2 = new Thread(out);
			//开启线程
			t1.start();
			t2.start();
		}
	}

案例：多生产者，多消费者问题

	class Resource{
		private String name;
		private int count = 1;
		private boolean flag = false;
		public synchronized void set(String name){
			while(flag)
				try{this.wait();}catch(InterruptedException e){}
			this.name = name + count;//资源1  资源2  资源3
			count++;//2 3 4
			System.out.println(Thread.currentThread().getName()+"...生产者..."+this.name);//生产资源1 生产资源2 生产资源3
			flag = true;
			// notify:只能唤醒一个线程，如果本方唤醒了本方，没有意义。而且while判断标记+notify会导致死锁。
			// notifyAll解决了本方线程一定会唤醒对方线程的问题。
			notifyAll();
		}

		public synchronized void out(){
			while(!flag)
				try{this.wait();}catch(InterruptedException e){}
			System.out.println(Thread.currentThread().getName()+"...消费者........"+this.name);//消费资源1
			flag = false;
			notifyAll();
		}
	}
	
	class Producer implements Runnable{
		private Resource r;
		Producer(Resource r){
			this.r = r;
		}
		public void run(){
			while(true){
				r.set("资源");
			}
		}
	}
	
	class Consumer implements Runnable
	{
		private Resource r;
		Consumer(Resource r){
			this.r = r;
		}
		public void run(){
			while(true){
				r.out();
			}
		}
	}
	
	class  ProducerConsumerDemo{
		public static void main(String[] args) {
			Resource r = new Resource();
			Producer pro = new Producer(r);
			Consumer con = new Consumer(r);

			Thread t0 = new Thread(pro);
			Thread t1 = new Thread(pro);
			Thread t2 = new Thread(con);
			Thread t3 = new Thread(con);
			t0.start();
			t1.start();
			t2.start();
			t3.start();
		}
	}