---
title: SSH（第一步）—— 单独配置Spring框架到web项目
tags: [Struts2,Spring,Hibernate,Framework]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*
**注意：**  
点击即可下载：[完整的SSH项目整合Demo](http://download.csdn.net/download/qq_28592887/9994986)

### 一、创建配置文件并导入约束
共应导入四个约束
beans、Context、aop、tx。
<!--more--> 
1、在src路径下新建applicationContext.xml配置文件。
并在配置文件中添加beans标签。  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506520196/Framework%20integration/1/1.png)  

2、切换到Design视图，按照原来的方式添加约束  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506520196/Framework%20integration/1/2.png)

### 二、配置监听器
在web.xml配置文件中，配置Spring的监听器，使得Spring容器随项目的启动而启动。

	<!-- 让spring随web启动而创建的监听器 -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<!-- 配置spring配置文件位置参数 -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>classpath:applicationContext.xml</param-value>
	</context-param>

![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506520196/Framework%20integration/1/3.png)

### 三、测试
直接发布项目到服务器，并启动项目。
若服务器启动过程中没有报错，说明Spring的配置是正确的。

到现在为止，项目中只有Spring框架。而且没有任何的业务。














