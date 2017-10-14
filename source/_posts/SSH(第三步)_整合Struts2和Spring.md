---
title: SSH（第三步）—— 整合Struts2和Spring
tags: [Struts2,Spring,Hibernate,Framework]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*
**注意：**  
点击即可下载：[完整的SSH项目整合Demo](http://download.csdn.net/download/qq_28592887/9994986)

原理：  
Spring框架与Struts2框架的整合  
就是将Struts2框架中的Action对象交给Spring容器负责创建。
<!--more-->
### 一、导包（在第0步导包环节已经导入过了）  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506698010/Framework%20integration/3/1.png)  
该jar包用于整Struts2框架和Spring框架。

### 二、配置常量
	<constant name="struts.objectFactory" value="spring"></constant>
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506698010/Framework%20integration/3/2.png)  

### 三、准备相关类
1、创建service层的类

	创建UserService接口
	package cn.mwxu16.service;
	import cn.mwxu16.domain.User;
	public interface UserService {
	      // 登陆方法
	      User getUserByCodePassword(User u);
	      // 注册用户
	      void saveUser(User u);
	}  
创建UserService实现类

	package cn.mwxu16.service.impl;
	import cn.mwxu16.domain.User;
	import cn.mwxu16.service.UserService;
	public class UserServiceImpl implements UserService {
	      @Override
	      public User getUserByCodePassword(User u) {
	            //测试输出
	            System.out.println("UserService");
	            return null;
	      }
	      @Override
	      public void saveUser(User u) {
	            // TODO Auto-generated method stub
	      }
	}

2、在Action类中引用UserService  

	package cn.mwxu16.web.action;
	import com.opensymphony.xwork2.ActionSupport;
	import cn.mwxu16.service.UserService;
	public class UserAction extends ActionSupport{
	      
	      //UserService对象由Spring容器注入
	      private UserService userService ;
	      
	      public String hello(){
	            System.out.println("我是Spring注入的"+userService);
	            return SUCCESS;
	      }
	      
	      public void setUserService(UserService userService) {
	            this.userService = userService;
	      }
	}


下面有两种方案进行整合：
### 一、仍然有Struts负责创建Action对象，由Spring注入依赖（不推荐使用）

1、在struts.xml配置文件中，仍然配置action的完整类名。  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506698010/Framework%20integration/3/3.png)  

2、在applicationContext.xml配置文件中配置UserService  
注意：
这里的action配置中，不需要手动对属性注入，Spring会自动注入依赖，Struts的action对象的作用范围必须是多例，需要手动配置作用范围scope。
这里的UserService配置中，bean的name必须与action中的属性名完全一致，否则Spring无法自动注入。  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506698010/Framework%20integration/3/4.png)

3、测试
运行项目，访问UserAction类的hello方法  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506698010/Framework%20integration/3/5.png)  

若没有报错，并在控制台输出了
如下内容，说明整合成功  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506698010/Framework%20integration/3/6.png)

### 二、由Spring容器负责action的完整生命周期（推荐使用）

1、在struts.xml配置文件中，class属性不配置action的完整类名，而是配置action类在applicationContext.xml文件中配置的bean的name。  
	
	<!-- 通过动态方法调用，配置action -->
	            <action name="userAction_*" class="userAction" method="{1}">
	                  <result name="success">/success.jsp</result>
	            </action>
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506698010/Framework%20integration/3/7.png)

2、在applicationContext.xml配置文件中配置UserService  
注意：
这里的action配置中，需要手动注入属性，需要注意作用范围必须是多例的。  
	
	<!-- 配置action -->
	<!-- 注意action对象的作用范围一定是多例的，默认的范围是单例，因此必须手动声明为多例 -->
	<bean name="userAction" class="cn.mwxu16.web.action.UserAction" scope="prototype" >
	      <property name="userService" ref="userService"></property>
	</bean>
	<!-- 配置UserService -->
	<bean name= "userService" class="cn.mwxu16.service.impl.UserServiceImpl"></bean>
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506698010/Framework%20integration/3/8.png)

3、测试
运行项目，访问UserAction类的hello方法  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506698010/Framework%20integration/3/9.png)

若没有报错，并在控制台输出了
如下内容，说明整合成功  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506698010/Framework%20integration/3/10.png)

### 总结：
第一种方案：  
在struts.xml文件中，acton的class属性，需要配置action的完整类名。
然后在applicationContext.xml配置文件中，不需要手动注入依赖，Spring会自动注入。

第二种方案：  
在struts.xml文件中，action的class属性，不配置action的完整类名，而是配置action类在applicationContext.xml配置文件中，bean的name。

### 区别：  
第一种方案，Spring只是帮助进行属性注入，而action对象并不是由Spring来创建的，因此Spring的有些功能无法应用到action对象上（例如aop）。

第二种方案，由Spring创建action对象，因此Spring的所有功能都可以应用到action类上。











