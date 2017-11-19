---
layout: post
category: Spring
title: Spring学习-Basic
tags: Spring
---

## Spring概述

Spring 是一个开源框架.<br>
主要特点：<br>
- 轻量级：Spring 是非侵入性的基于 Spring 开发的应用中的对象可以不依赖于 Spring 的 API<br>
- 依赖注入(DI --- dependency injection、IOC)<br>
- 面向切面编程(AOP --- aspect oriented programming)<br>
- 容器: Spring 是一个容器, 因为它包含并且管理应用对象的生命周期<br>
- 框架: Spring 实现了使用简单的组件配置组合成一个复杂的应用. 在 Spring 中可以使用 XML 和 Java 注解组合这些对象<br>
- 一站式：在 IOC 和 AOP 的基础上可以整合各种企业应用的开源框架和优秀的第三方类库 （实际上 Spring 自身也提供了展现层的 SpringMVC 和 持久层的 Spring JDBC）<br>
<br>
各个模块组成如下图：·<br>
<br>
![](http://www.zhanbus.com/uploads/allimg/141214/1-14121403253I23.png "图片来自网络")

## Spring中的IOC&DI

**控制反转IOC(Inversion of Control)**——Spring通过一种称作控制反转（IoC）的技术促进了松耦合。当应用了IoC，一个对象依赖的其它对象会通过被动的方式传递进来，而不是这个对象自己创建或者查找依赖对象。你可以认为IoC与JNDI相反——不是对象从容器中查找依赖，而是容器在对象初始化时不等对象请求就主动将依赖传递给它。<br>
**DI(Dependency Injection)** — IOC 的另一种表述方式：即组件以一些预先定义好的方式(例如: setter 方法)接受来自如容器的资源注入. 相对于 IOC 而言，这种表述更直接

#### 1.Bean的配置形式
* **基于 XML 文件的方式**

		<bean id="helloWord" class="com.kyle.Spring_1.HelloWorld">
			<property name="name" value="Spring"></property>
		</bean>

在 IOC 容器中必须是唯一的

* **基于注解的方式**

#### 2.Bean 的配置方式
1. 通过全类名（反射）
2. 通过工厂方法（静态工厂方法 & 实例工厂方法）
3. FactoryBean

#### 3.IOC 容器 

在 **Spring IOC 容器**读取 Bean 配置创建 Bean 实例之前, 必须对它进行实例化. 只有在容器实例化后, 才可以从 IOC 容器里获取 Bean 实例并使用

1. **BeanFactory** : IOC 容器的基本实现

	


2. **ApplicationContext** : 提供了更多的高级特性. 是 BeanFactory 的子接口
	
	**重要接口**：<br>
	-- ConfigurableApplicationContext : 扩展于 ApplicationContext，新增加两个主要方法：refresh() 和 close()， 让 ApplicationContext 具有启动、刷新和关闭上下文的能力


	**主要实现类**：<br>
	-- ClassPathXmlApplicationContext：从 类路径下加载配置文件<br>
	-- FileSystemXmlApplicationContext: 从文件系统中加载配置文件

	在初始化上下文时就实例化所有单例的 Bean

	WebApplicationContext 是专门为 WEB 应用而准备的，它允许从相对于 WEB 根目录的路径中完成初始化工作

3. IOC 容器中 Bean 的生命周期
4. 从 IOC 容器中获取 Bean

	调用 ApplicationContext 的 getBean() 方法


#### 4.依赖注入(DI)
1. **属性注入**(实际应用中最常见的方式)

	(1) 属性注入即通过 setter 方法注入Bean 的属性值或依赖的对象<br>
	(2) 属性注入使用 <property> 元素, 使用 name 属性指定<br>

2. **构造器注入**

	(1) 通过构造方法注入Bean 的属性值或依赖的对象，它保证了 Bean 实例在实例化后就可以使用。<br>
	(2) 构造器注入在 <constructor-arg> 元素里声明属性, <constructor-arg> 中没有 name 属性<br>
	(3) 按索引匹配入参<br>
	(4) 按类型匹配入参<br>

3. **工厂方法注入**（很少使用，不推荐）
4. **注入属性值细节**

	(1) 字面值<br>
	- 可用字符串表示的值，可以通过 <value> 元素标签或 value 属性进行注入。
	- 基本数据类型及其封装类、String 等类型都可以采取字面值注入的方式
	- 若字面值中包含特殊字符，可以使用 <![CDATA[]]> 把字面值包裹起来。

	(2) 引用其他Bean,使 Bean 能够相互访问
	- 在 Bean 的配置文件中, 可以通过 <ref> 元素或 ref  属性为 Bean 的属性或构造器参数指定对 Bean 的引用
	- 也可以在属性或构造器里包含 Bean 的声明, 这样的 Bean 称为**内部 Bean**
	- 当 Bean 实例仅仅给一个特定的属性使用时, 可以将其声明为内部 Bean. 内部 Bean 声明直接包含在 <property> 或 <constructor-arg> 元素里, 不需要设置任何 id 或 name 属性,内部 Bean 不能使用在任何其他地方.
	
	(3) null 值和级联属性
	- 可以使用专用的 <null/> 元素标签为 Bean 的字符串或其它对象类型的属性注入 null 值
	- 和 Struts、Hiberante 等框架一样，Spring 支持级联属性的配置。
	(4) 集合属性
	- 在 Spring中可以通过一组内置的 xml 标签(例如: <list>, <set> 或 <map>) 来配置集合属性.
	- 配置 java.util.List 类型的属性, 需要指定 <list>  标签, 在标签里包含一些元素. 这些标签可以通过 <value> 指定简单的常量值, 通过 <ref> 指定对其他 Bean 的引用. 通过<bean> 指定内置 Bean 定义. 通过 <null/> 指定空元素. 甚至可以内嵌其他集合.
	- 数组的定义和 List 一样, 都使用 <list>
	- 配置 java.util.Set 需要使用 <set> 标签, 定义元素的方法与 List 一样.
	- Java.util.Map 通过 <map> 标签定义, <map> 标签里可以使用多个 <entry> 作为子标签. 每个条目包含一个键和一个值.必须在 <key> 标签里定义键,因为键和值的类型没有限制, 所以可以自由地为它们指定 <value>, <ref>, <bean> 或 <null> 元素.可以将 Map 的键和值作为 <entry> 的属性定义: 简单常量使用 key 和 value 来定义; Bean 引用通过 key-ref 和 value-ref 属性定义使用 <props> 定义 java.util.Properties, 该标签使用多个 <prop> 作为子标签. 每个 <prop> 标签必须定义 key 属性.
	(5) utility scheme 定义集合
	- 使用基本的集合标签定义集合时, 不能将集合作为独立的 Bean 定义, 导致其他 Bean 无法引用该集合, 所以无法在不同 Bean 之间共享集合.可以使用 util schema 里的集合标签定义独立的集合 Bean. 需要注意的是, 必须在 <beans> 根元素里添加 util schema 定义
	(6) p 命名空间
	- 为了简化 XML 文件的配置，越来越多的 XML 文件采用属性而非子元素配置信息。Spring 从 2.5 版本开始引入了一个新的 p 命名空间，可以通过 <bean> 元素属性的方式配置 Bean 的属性。

5. **自动装配**
	(1) XML 配置里的 Bean 自动装配
	- Spring IOC 容器可以自动装配 Bean. 需要做的仅仅是在 <bean> 的 autowire 属性里指定自动装配的模式
	- byType(根据类型自动装配): 若 IOC 容器中有多个与目标 Bean 类型一致的 Bean. 在这种情况下, Spring 将无法判定哪个 Bean 最合适该属性, 所以不能执行自动装配.
	- byName(根据名称自动装配): 必须将目标 Bean 的名称和属性名设置的完全相同.
	- constructor(通过构造器自动装配): 当 Bean 中存在多个构造器时, 此种自动装配方式将会很复杂. **不推荐使用**
	(2) XML 配置里的 Bean 自动装配的缺点
	- 在 Bean 配置文件里设置 autowire 属性进行自动装配将会装配 Bean 的所有属性. 然而, 若只希望装配个别属性时, autowire 属性就不够灵活了. 
	- autowire 属性要么根据类型自动装配, 要么根据名称自动装配, 不能两者兼而有之.
	- 一般情况下，在实际的项目中很少使用自动装配功能，因为和自动装配功能所带来的好处比起来，明确清晰的配置文档更有说服力一些


#### 5.Bean 之间的关系
1. 继承
	(1) Spring 允许继承 bean 的配置, 被继承的 bean 称为父 bean. 继承这个父 Bean 的 Bean 称为子 Bean子 Bean 从父 Bean 中继承配置, 包括 Bean 的属性配置,子 Bean 也可以覆盖从父 Bean 继承过来的配置,父 Bean 可以作为配置模板, 也可以作为 Bean 实例. 若只想把父 Bean 作为模板, 可以设置 <bean> 的abstract 属性为 true, 这样 Spring 将不会实例化这个 Bean,并不是 <bean> 元素里的所有属性都会被继承. 比如: autowire, abstract 等.,也可以忽略父 Bean 的 class 属性, 让子 Bean 指定自己的类, 而共享相同的属性配置. 但此时 abstract 必须设为 true
	

2. 依赖
	(1) Spring 允许用户通过 depends-on 属性设定 Bean 前置依赖的Bean，前置依赖的 Bean 会在本 Bean 实例化之前创建好,如果前置依赖于多个 Bean，则可以通过逗号，空格或的方式配置 Bean 的名称

#### 6.Bean 的作用域
1. singleton
2. prototype
3. WEB 环境作用域

#### 7.使用外部属性文件

#### 8.spEL

#### 9.Spring 4.x 新特性：泛型依赖注入















## Spring中的AOP
面向切面——Spring提供了面向切面编程的丰富支持，允许通过分离应用的业务逻辑与系统级服务（例如审计（auditing）和事务（）管理）进行内聚性的开发。应用对象只实现它们应该做的——完成业务逻辑——仅此而已。它们并不负责（甚至是意识）其它的系统级关注点，例如日志或事务支持。<br>