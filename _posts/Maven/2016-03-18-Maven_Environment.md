---
layout: post
category:  Maven
title: Maven环境搭建
tags: Maven
---
## 一.下载安装maven
1,[下载地址](https://maven.apache.org/download.cgi)
本文下载的是maven3.3.9 版本.<br>

2,解压<br>
将maven解压到一个不含有中文和空格的目录中。<br>

![这里写图片描述](http://img.blog.csdn.net/20170415203427084)

3,电脑上需安装java环境，安装JDK1.7 + 版本(将JAVA_HOME/bin 配置环境变量path)<br>
<br>
4,配置MAVEN_HOME，环境变量中添加MAVEN_HOME环境变量，变量值就是你的maven安装 的路径(bin目录之前一级目录)<br>

![这里写图片描述](http://img.blog.csdn.net/20170415204149009)

5,将 %MAVEN_HOME%/bin 加入环境变量 path<br>
![这里写图片描述](http://img.blog.csdn.net/20170415204155022)

6.测试<br>

	mvn -version

![这里写图片描述](http://img.blog.csdn.net/20170415210933310)

** 到这里我们的maven已经正常安装并且能运行起来，下面继续探究maven的用法吧 **

## 二.Maven仓库的配置

1,仓库的分类<br>
![这里写图片描述](http://img.blog.csdn.net/20170415212435095)

2,本地仓库的配置

这个配置文件：maven安装路径下\conf\settings.xml文件中

![这里写图片描述](http://img.blog.csdn.net/20170415213142939)

## 三.Maven项目工程目录约定

使用maven创建的工程我们称它为maven工程，maven工程具有一定的目录规范，如下：<br>

	src/main/java —— 存放项目的.java文件
	src/main/resources —— 存放项目资源文件，如spring, hibernate配置文件
	src/test/java —— 存放所有单元测试.java文件，如JUnit测试类
	src/test/resources —— 测试资源文件
	target —— 项目输出位置，编译后的class文件会输出到此目录
	pom.xml——maven项目核心配置文件

	Project
	  |-src
	  |   |-main
	  |   |  |-java          —— 存放项目的.java文件
	  |   |  |-resources     —— 存放项目资源文件，如spring, hibernate配置文件
	         |-webapp        —— webapp目录是web工程的主目录
	            |-WEB-INF
	              |-web.xml
	  |   |-test
	  |      |-java          ——存放所有测试.java文件，如JUnit测试类
	  |      |-resources     —— 测试资源文件
	  |-target               —— 目标文件输出位置例如.class、.jar、.war文件
	  |-pom.xml              ——maven项目核心配置文件

## 四.Maven常用命令

**compile**

compile是maven工程的编译命令，作用是将src/main/java下的文件编译为class文件输出到target目录下。<br>

**test**

test是maven工程的测试命令，会执行src/test/java下的单元测试类。
cmd执行mvn test执行src/test/java下单元测试类。

**clean**

clean是maven工程的清理命令，执行 clean会删除target目录的内容。

**package**

package是maven工程的打包命令，对于java工程执行package打成jar包，对于web工程打成war包。

**install**

install是maven工程的安装命令，执行install将maven打成jar包或war包发布到本地仓库。

## 五.生命周期

**三套生命周期**

maven对项目构建过程分为三套相互独立的生命周期，请注意这里说的是“三套”，而且“相互独立”，这三套生命周期分别是：

	Clean Lifecycle 在进行真正的构建之前进行一些清理工作。 
	Default Lifecycle 构建的核心部分，编译，测试，打包，部署等等。
	Site Lifecycle 生成项目报告，站点，发布站点。

每个生命周期都有很多阶段，每个阶段对应一个执行命令。<br>
	
**1.如下是clean生命周期的阶段**<br>
pre-clean 执行一些需要在clean之前完成的工作 <br>
clean 移除所有上一次构建生成的文件 <br>
post-clean 执行一些需要在clean之后立刻完成的工作<br>

**2.如下是default周期的内容**<br>
validate<br>
generate-sources<br>
process-sources<br>
generate-resources<br>
process-resources     复制并处理资源文件，至目标目录，准备打包。<br>
compile     编译项目的源代码。<br>
process-classes<br>
generate-test-sources <br>
process-test-sources<br>
generate-test-resources<br>
process-test-resources     复制并处理资源文件，至目标测试目录。<br>
test-compile     编译测试源代码。<br>
process-test-classes<br>
test     使用合适的单元测试框架运行测试。这些测试代码不会被打包或部署。<br>
prepare-package<br>
package     接受编译好的代码，打包成可发布的格式，如 JAR 。<br>
pre-integration-test<br>
integration-test<br>
post-integration-test<br>
verify<br>
install     将包安装至本地仓库，以让其它项目依赖。<br>
deploy     将最终的包复制到远程的仓库，以让其它开发人员与项目共享。<br>

**3.如下是site生命周期的阶段**<br>
pre-site 执行一些需要在生成站点文档之前完成的工作 <br>
site 生成项目的站点文档 <br>
post-site 执行一些需要在生成站点文档之后完成的工作，并且为部署做准备<br>
site-deploy 将生成的站点文档部署到特定的服务器上<br>
