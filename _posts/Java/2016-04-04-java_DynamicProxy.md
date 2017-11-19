---
layout: post
category:  java
title: 动态代理
tags: java，动态代理
---
## 动态代理概述

动态代理是指在运行时动态生成代理类。不需要像静态代理那个去手动写一个个的代理类。生成动态代理类有很多方式：Java动态代理，CGLIB，Javassist，ASM库等。这里主要说一下Java动态代理和CGLIB的实现。

### 一 JDK动态代理----通过接口

在java的动态代理机制中，有两个重要的类或接口，一个是 InvocationHandler(Interface)、另一个则是 Proxy(Class)，这一个类和接口是实现动态代理所必须用到的。

* **InvocationHandler:**

	每一个动态代理类都必须要实现InvocationHandler这个接口，并且每个代理类的实例都关联到了一个handler，当通过代理对象调用一个方法的时候，这个方法的调用就会被转发为由InvocationHandler这个接口的 invoke 方法来进行调用。

		/**
		 * 
		 * proxy:  指代所代理的那个真实对象
		 * method: 指代的是所要调用真实对象的某个方法的Method对象
		 * args:　　指代的是调用真实对象某个方法时接受的参数
		 *
		 */
		Object invoke(Object proxy, Method method, Object[] args) throws Throwable

* **Proxy:**
	
	这个代类有很多方法常用的方法就是newProxyInstance

		/**
		 * 
		 * loader:  一个ClassLoader对象，定义了由哪个ClassLoader对象来对生成的代理对象进行加载
		 * interfaces:  一个Interface对象的数组，表示的是我将要给我需要代理的对象提供一组什么接口，
		 * 	如果提供了一组接口给它，那么这个代理对象就宣称实现了该接口(多态)，这样我就能调用这组接口中的方法了
		 * h:  一个InvocationHandler对象，表示的是当我这个动态代理对象在调用方法的时候，会关联到哪一个InvocationHandler对象上
		 *
		 */
		public static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces,  InvocationHandler h)  throws IllegalArgumentException

**一个例子：**

1. 定义一个Subject类型的接口:

		package com.kyle.DynamicProxy;
		
		public interface Subject {
			
			void hello(String str);
		}

2. 定义了一个类来实现这个接口，这个类就是真实对象，RealSubject类:

		package com.kyle.DynamicProxy;
		
		public class RealSubject implements Subject {
		
			@Override
			public void hello(String str) {
				System.out.println("hello: " + str);
			}
		
		}

3. 定义一个动态代理类了，每一个动态代理类都必须要实现 InvocationHandler 这个接口，因此这个动态代理类也不例外:

		package com.kyle.DynamicProxy;
		
		import java.lang.reflect.InvocationHandler;
		import java.lang.reflect.Method;
		
		public class DynamicProxy implements InvocationHandler {
		
			// 要代理的真实对象
			private Object subject;
		
			// 构造方法，要代理的真实对象赋初值
			public DynamicProxy(Object subject) {
				this.subject = subject;
			}
		
			@Override
			public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
				// 在代理真实对象前我们可以添加一些自己的操作
				System.out.println("before Hello");
		
				System.out.println("Method:" + method);
		
				// 当代理对象调用真实对象的方法时，其会自动的跳转到代理对象关联的handler对象的invoke方法来进行调用
				method.invoke(subject, args);
		
				// 在代理真实对象后我们也可以添加一些自己的操作
				System.out.println("after Hello");
		
				return null;
			}
		}

4. 最后，定义Client类：

		import java.lang.reflect.InvocationHandler;
		import java.lang.reflect.Proxy;
		
		public class Client {
		
		public static void main(String[] args) {
			
			// 要代理的真实对象
			Subject realSubject = new RealSubject();
			
			// 要代理哪个真实对象，就将该对象传进去，最后是通过该真实对象来调用其方法的
			InvocationHandler handler = new DynamicProxy(realSubject);
		
			/*
			 * 通过Proxy的newProxyInstance方法来创建我们的代理对象，三个参数 第一个参数
			 * handler.getClass().getClassLoader()
			 * ，这里使用handler这个类的ClassLoader对象来加载代理对象
			 * 第二个参数realSubject.getClass().getInterfaces()，这里为代理对象提供的接口是真实对象所实行的接口
			 * ，表示我要代理的是该真实对象，这样我就能调用这组接口中的方法了 第三个参数handler， 我们这里将这个代理对象关联到了上方的
			 * InvocationHandler 这个对象上
			 */
			Subject subject = (Subject) Proxy.newProxyInstance(handler.getClass().getClassLoader(),
					realSubject.getClass().getInterfaces(), handler);
		
			System.out.println(subject.getClass().getName());
			subject.hello("world");
			}
		
		}


控制台的输出：<br>

	com.sun.proxy.$Proxy0
	before Hello
	Method:public abstract void com.kyle.DynamicProxy.Subject.hello(java.lang.String)
	hello: world  realSubject
	after Hello

**注：**通过 Proxy.newProxyInstance 创建的代理对象是在jvm运行时动态生成的一个对象，它并不是我们的InvocationHandler类型，也不是我们定义的那组接口的类型，而是在运行是动态生成的一个对象，并且命名方式都是这样的形式，以$开头，proxy为中，最后一个数字表示对象的标号。

**对于JDK动态代理的疑问**
为什么java动态代理为什么一定要代理接口而不能代理类？
在动态代理经过编译之后的实现类似于以下结构：

	public class $Proxy1 extends Proxy implements 传入的接口{
	    
	}

到这里应该很容易理解为什么这里需要代理接口而不是一个类，因为这个动态代理已经继承了Proxy这个类.

**延迟加载代理举例：**

* 接口类：

		package com.kyle.LazyLoad;
		
		public interface IDBExcute {
			
			String query();
		
			int update();
		}

* 实现类：

		package com.kyle.LazyLoad;
		
		public class DBExcute implements IDBExcute {
		
			String queryContent = "Something from DB.";
		
			@Override
			public String query() {
				System.out.println("query is running...");
				// 模拟查询操作的返回结果
				return queryContent;
			}
		
			public DBExcute() {
				// 真实对象被创建时候打印信息
				System.out.println("Real object is created...");
			}
		
			@Override
			public int update() {
				System.out.println("update is running...");
				// 模拟更新操作影响的件数
				return 1;
			}
		}

* 代理类：

		package com.kyle.LazyLoad;
		
		import java.lang.reflect.InvocationHandler;
		import java.lang.reflect.Method;
		
		public class DBExcuteHandler implements InvocationHandler {
		
			// 定义主题接口
			IDBExcute dbQuery = null;
		
			@Override
			public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
				// 如果第一次调用，生成真实主题,创建真正要代理的类
				if (dbQuery == null) {
					dbQuery = new DBExcute();
				}
		
				// 返回真实主题完成实际的操作
				return method.invoke(dbQuery, args);
			}
		}

* 测试类：

		package com.kyle.LazyLoad;
		
		import java.lang.reflect.Proxy;
		
		public class Main {
		
			public static void main(String[] args) {
				System.out.println("开始获取代理对象");
				// 获取代理对象的时候真正的对象并没有被创建
				IDBExcute excute = (IDBExcute) Proxy.newProxyInstance(ClassLoader.getSystemClassLoader(),
						new Class[] { IDBExcute.class }, new DBExcuteHandler());
				System.out.println("开始调用实际方法");
				// 当第一次调用实际对象方法的时候真正的对象才被创建
				String result = excute.query();
				System.out.println("查询方法结果： " + result);
				int count = excute.update();
				System.out.println("更新结果： " + count);
		
			}
		}

控制台的输出：<br>

	开始获取代理对象
	开始调用实际方法
	Real object is created...
	query is running...
	查询方法结果： Something from DB.
	update is running...
	更新结果： 1

### 二 CGLIB动态代理----通过类继承
未完待续。。。