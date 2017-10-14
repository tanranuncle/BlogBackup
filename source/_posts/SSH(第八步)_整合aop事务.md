---
title: SSH（第八步）—— 整合aop事务
tags: [Struts2,Spring,Hibernate,Framework]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*
**注意：**  
点击即可下载：[完整的SSH项目整合Demo](http://download.csdn.net/download/qq_28592887/9994986)  

整合aop事务有两种方式：  
1.xml配置的方式  
2.注解的方式  
<!--more-->
### 准备工作、配置核心事务管理器  
在applicationContext.xml文件中，配置核心事务管理器  

	<!-- 配置核心事务管理器 -->
	      <bean name="transactionManager" class="org.springframework.orm.hibernate5.HibernateTransactionManager" >
	            <!-- 注入sessionFactory -->
	            <property name="sessionFactory" ref="sessionFactory"></property>
	      </bean>


## 使用xml配置的方式整合aop事务  
### 一、配置通知  

	<!-- 配置通知 -->
	      <tx:advice id="txAdvice" transaction-manager="transactionManager" >
	            <tx:attributes>
	                  <tx:method name="save*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
	                  <tx:method name="persist*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
	                  <tx:method name="update*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
	                  <tx:method name="modify*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
	                  <tx:method name="delete*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
	                  <tx:method name="remove*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="false" />
	                  <tx:method name="get*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="true" />
	                  <tx:method name="find*" isolation="REPEATABLE_READ" propagation="REQUIRED" read-only="true" />
	            </tx:attributes>
	      </tx:advice>
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506700734/Framework%20integration/8/1.png)

### 二、将通知织入目标对象  

	<!-- 配置将通知织入目标对象-->
	      <aop:config>
	            <!-- 配置切点 -->
	            <aop:pointcut expression="execution(* cn.mwxu16.service.impl.*ServiceImpl.*(..))" id="txPc"/>
	            <!-- 配置切面 -->
	            <aop:advisor advice-ref="txAdvice" pointcut-ref="txPc" />
	      </aop:config>
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506700734/Framework%20integration/8/2.png)

xml配置事务结束，可以通过下面的测试方法进行测试。  

## 使用注解的方式整合aop事务  
### 一、在applicationContext.xml配置文件中进行配置  

	<!-- 开启注解事务 -->
	      <tx:annotation-driven transaction-manager="transactionManager" />
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506700734/Framework%20integration/8/3.png)

### 二、在UserServiceImpl类上添加注解  

	package cn.mwxu16.service.impl;
	import org.springframework.transaction.annotation.Isolation;
	import org.springframework.transaction.annotation.Propagation;
	import org.springframework.transaction.annotation.Transactional;
	import cn.mwxu16.dao.UserDao;
	import cn.mwxu16.domain.User;
	import cn.mwxu16.service.UserService;
	@Transactional(isolation=Isolation.REPEATABLE_READ,propagation=Propagation.REQUIRED,readOnly=true)
	public class UserServiceImpl implements UserService {
	      private UserDao ud;
	      @Override
	      public User getUserByCodePassword(User u) {
	            //测试输出
	            System.out.println("UserService");
	            return null;
	      }
	      @Override
	      @Transactional(isolation=Isolation.REPEATABLE_READ,propagation=Propagation.REQUIRED,readOnly=false)
	      public void saveUser(User u) {
	            // TODO Auto-generated method stub
	            ud.save(u);
	      }
	      public void setUd(UserDao ud) {
	            this.ud = ud;
	      }
	}


### 三、测试  
通过Junit4测试  

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
	import cn.mwxu16.dao.UserDao;
	import cn.mwxu16.domain.User;
	import cn.mwxu16.service.UserService;
	@RunWith(SpringJUnit4ClassRunner.class)
	@ContextConfiguration("classpath:applicationContext.xml")
	public class TestHibernate {
	
	      //将UserService对象注入进来
	      @Resource(name="userService")
	      private UserService us;
	      
	      @Test
	      public void fun4() {
	            User u = new User();
	            u.setUser_code("kangkang");
	            u.setUser_name("康康");
	            u.setUser_password("123456");
	      
	            us.saveUser(u);
	            
	      }
	}

### 注意：
若成功将用户保存到数据库中，则说明事务整合成功。  
若事务没有整合成功，将会测试出现异常，  
因为在没有事务的情况下，所有的操作都是只读操作，不能对数据库进行增删改操作。





