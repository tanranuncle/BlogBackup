---
title: SSH（第六步）—— 整合c3p0连接池
tags: [Struts2,Spring,Hibernate,Framework]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*
**注意：**  
点击即可下载：[完整的SSH项目整合Demo](http://download.csdn.net/download/qq_28592887/9994986)

### 一、创建db.properties文件  
<!--more-->
在src路径下创建db.properties配置文件，  
配置数据库连接的四个基本信息。  
如下：  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506699846/Framework%20integration/6/1.png)

### 二、在applicationContext.xml文件中引入c3p0  
读取db.properties文件中的配置，引入c3p0  

      <!-- 读取db.properties文件 -->
      <context:property-placeholder location="classpath:db.properties" />
      <!-- 配置c3p0连接池 -->
      <bean name="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
            <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
            <property name="driverClass" value="${jdbc.driverClass}"></property>
            <property name="user" value="${jdbc.user}"></property>
            <property name="password" value="${jdbc.password}"></property>
      </bean>
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506699846/Framework%20integration/6/2.png)  

### 三、将c3p0注入到SessionFactory中  
注意：  
将c3p0注入到SessionFactory中后，原来配置的四个必选配置就不需要了。  
注意：dataSource大小写不要拼错（拼错会报出applicationContext.xml配置文件加载失败的错误）。  

	<!-- 引入c3p0连接池 -->
	<property name="dataSource" ref="dataSource"></property>
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506699846/Framework%20integration/6/3.png)

### 四、测试  
使用Junit4测试，测试类如下：  

	package cn.mwxu16.test;
	import javax.annotation.Resource;
	import org.hibernate.Session;
	import org.hibernate.SessionFactory;
	import org.hibernate.Transaction;
	import org.hibernate.cfg.Configuration;
	import org.junit.Test;
	import org.junit.runner.RunWith;
	import org.springframework.test.context.ContextConfiguration;
	import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
	import cn.mwxu16.domain.User;
	@RunWith(SpringJUnit4ClassRunner.class)
	@ContextConfiguration("classpath:applicationContext.xml")
	public class TestHibernate {
	      @Resource(name="sessionFactory")
	      //通过Spring容器来注入SessionFactory对象
	      private SessionFactory sf;
	      
	      @Test
	      public void fun1() {
	            
	            //创建session对象
	            Session session = sf.openSession();
	            //开启事务
	            Transaction tx = session.beginTransaction();
	            
	            User u = new User();
	            u.setUser_code("lucy");
	            u.setUser_name("露西");
	            u.setUser_password("123456");
	            
	            session.save(u);
	            
	            //提交事务
	            tx.commit();
	            //关闭资源
	            session.close();
	            
	      }
	}

若成功保存user对象，则说明整合成功。

