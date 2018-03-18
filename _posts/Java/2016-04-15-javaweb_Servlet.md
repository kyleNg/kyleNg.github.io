---
layout: post
category:  java
title: JavaWeb--Servlet
tags: servlet
---
### **Servlet是什么** ###
----------
> Servlet（Server Applet）是用Java编写的服务器端程序。其主要功能在于交互式地浏览和修改数据，生成动态Web内容。狭义的Servlet是指Java语言实现的一个接口，广义的Servlet是指任何实现了这个Servlet接口的类，一般情况下，人们将Servlet理解为后者。

以上摘自维基百科，通俗的说，Servlet是一个java接口，适用于B/S架构中的请求处理。

### 初识Servlet ###
----------
既然Servlet是一个接口，那按照常规做法就先创建一个类去实现这个接口，是不是就可以处理浏览器发过来的请求呢？答案肯定不是，那么怎样才能处理呢？这时就可以用一个Web应用服务器Tomcat,WebLogic,WebSphere,Jetty等负责处理客户请求,把请求传送给Servlet,并将Servlet的响应传送回给客户。

1. **创建一个web工程**

	[创建方式请参考网友博客](http://blog.csdn.net/u014079773/article/details/51397850)<br>
	完成之后目录结构大概是这个样子

	![](https://blog-1255865654.cos.ap-beijing.myqcloud.com/sevlet/servlet01.PNG)

2. **创建一个Servlet接口的实现类**

		package com.kyle.study01;
	
		import java.io.IOException;
		import javax.servlet.Servlet;
		import javax.servlet.ServletConfig;
		import javax.servlet.ServletException;
		import javax.servlet.ServletRequest;
		import javax.servlet.ServletResponse;
		
		public class HelloServlet implements Servlet {
		
			@Override
			public void destroy() {
				System.out.println("destroy");
		
			}
		
			@Override
			public ServletConfig getServletConfig() {
				System.out.println("getServletConfig");
				return null;
			}
		
			@Override
			public String getServletInfo() {
				System.out.println("getServletInfo");
				return null;
			}
		
			@Override
			public void init(ServletConfig arg0) throws ServletException {
				System.out.println("init");
			}
		
			@Override
			public void service(ServletRequest arg0, ServletResponse arg1) throws ServletException, IOException {
				System.out.println("service");
			}
		
			public HelloServlet() {
				System.out.println("HelloServlet constructor");
			}
		}

	为测试方便在每个重写方法中加入一句打印方法名，并且加入构造函数。

3. **在web.xml中配置这个Servlet**

		<!-- 配置和映射 Servlet -->
		<servlet>
			<servlet-name>helloservlet</servlet-name>
			<!-- 类的全路径名 -->
			<servlet-class>com.kyle.study01.HelloServlet</servlet-class>
		</servlet>
		<servlet-mapping>
			<servlet-name>helloservlet</servlet-name>
			<!-- / 代表项目根目录 -->
			<url-pattern>/hello</url-pattern>
		</servlet-mapping>

4. **在Tomcat中发布此项目**

	1. 在浏览器中输入http://localhost:8080/ServletStudy/hello

		控制台输出：

			HelloServlet constructor
			init
			service

	2. 多次刷新浏览器
 
		控制态输出：

			service
			service

	3. 右键Tomcat停止服务器

		控制态输出：

			destroy

	如下图：

	![](https://blog-1255865654.cos.ap-beijing.myqcloud.com/sevlet/Servlet03.PNG)


### Servlet的生命周期 ###
----------

结合上面的例子已经对Servlet有了一个初步的了解，下面来详细说一下Servlet的各个生命周期方法。

* Servlet 通过调用 init () 方法进行初始化。
* Servlet 调用 service() 方法来处理客户端的请求。
* Servlet 通过调用 destroy() 方法终止（结束）。
* 最后，Servlet 是由 JVM 的垃圾回收器进行垃圾回收的。

![](https://blog-1255865654.cos.ap-beijing.myqcloud.com/sevlet/Servlet-LifeCycle.jpg)

1. **init(ServletConfig arg0) 方法**

	init 方法被设计成只调用一次。它在第一次创建 Servlet 时被调用，在后续每次用户请求时不再调用。因此，它是用于一次性初始化，就像 Applet 的 init 方法一样。<br>
	Servlet 创建于用户第一次调用对应于该 Servlet 的 URL 时，但是您也可以指定 Servlet 在服务器第一次启动时被加载。

2. **service(ServletRequest arg0, ServletResponse arg1) 方法**

	service() 方法是执行实际任务的主要方法。Servlet 容器（即 Web 服务器）调用 service() 方法来处理来自客户端（浏览器）的请求，并把格式化的响应写回给客户端。<br>
	每次服务器接收到一个 Servlet 请求时，服务器会产生一个新的线程并调用服务。service() 方法检查 HTTP 请求类型（GET、POST、PUT、DELETE 等），并在适当的时候调用 doGet、doPost、doPut，doDelete 等方法。

3. **destroy() 方法**

	destroy() 方法只会被调用一次，在 Servlet 生命周期结束时被调用。destroy() 方法可以让您的 Servlet 关闭数据库连接、停止后台线程、把 Cookie 列表或点击计数器写入到磁盘，并执行其他类似的清理活动。<br>
	在调用 destroy() 方法之后，servlet 对象被标记为垃圾回收。

4. **构造器方法**

	从上面例子可以看出多次刷浏览器网址构造方法只被调用了一次，由此可见在之后的两次调用service方法时候都是同一个Servlet对象,因此可以推断Servlet是单例的。

### Servlet的单实例多线程 ###
----------
从上面可以得知一个servlet是在第一次被访问时加载到内存并实例化的。同样的业务请求共享一个servlet实例。不同的业务请求一般对应不同的servlet。<br>
