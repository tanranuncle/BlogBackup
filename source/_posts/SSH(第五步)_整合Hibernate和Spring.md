---
title: SSH（第五步）—— 整合Hibernate和Spring
tags: [Struts2,Spring,Hibernate,Framework]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*
**注意：**  
点击即可下载：[完整的SSH项目整合Demo](http://download.csdn.net/download/qq_28592887/9994986)

整合原理：  
Spring框架与Hibernate框架的整合  
就是将Hibernate中的sessionFactory交给Spring来负责维护。  
由Spring负责session维护和aop事务。  
<!--more-->
### 一、将SessionFactory配置到Spring容器中
由于SessionFactory的创建需要配置信息，因此必须提供配置信息才行。
这里有两种方案来提供配置信息。

方案一：仍然使用外部的hibernate.cfg.xml文件中的配置信息  

	<!-- 将SessionFactory配置到Spring容器中 -->
	<!-- 注意：class属性的值，必须与当前使用的hibernate的版本对应，这里使用的是hibernate5
	      可以Ctrl+Shift+T,搜索LocalSessionFactoryBean,然后选择对应的版本，再复制完整类名
	 -->
	<bean name="sessionFactory" class="org.springframework.orm.hibernate5.LocalSessionFactoryBean" >
	<!-- 方案一     仍然使用外部的hibernate.cfg.xml中的配置信息  -->
	            <!-- name属性必须是configLocation，value属性是classpath:hibernate.cfg.xml（即类路径下面的Hibernate配置文件） -->
	      <property name="configLocation" value="classpath:hibernate.cfg.xml"></property>
	</bean>
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506699373/Framework%20integration/5/1.png)

方案二：在applicationContext.xml中配置数据库相关的信息。  

	<bean name="sessionFactory"
	            class="org.springframework.orm.hibernate5.LocalSessionFactoryBean">
	            <!-- 方案二 使用Properties配置相关信息 -->
	            <!--配置hibernate基本信息 -->
	            <property name="hibernateProperties">
	                  <props>
	                        <!-- 四个必选配置 -->
	                        <prop key="hibernate.connection.driver_class">com.mysql.jdbc.Driver</prop>
	                        <prop key="hibernate.connection.url">jdbc:mysql:///crm_32?characterEncoding=utf-8</prop>
	                        <prop key="hibernate.connection.username">root</prop>
	                        <prop key="hibernate.connection.password">root</prop>
	                        <!-- 四个可选配置 -->
	                        <prop key="hibernate.dialect">org.hibernate.dialect.MySQLDialect</prop>
	                        <prop key="hibernate.show_sql">true</prop>
	                        <prop key="hibernate.format_sql">true</prop>
	                        <prop key="hibernate.hbm2ddl.auto">update</prop>
	                  </props>
	            </property>
	            <!-- 引入ORM元数据 ,指定ORM元数据所在的路径，Spring会自动读取包中的所有配置 -->
	            <property name="mappingDirectoryLocations" value="classpath:cn/mwxu16/domain"></property>
	      </bean>
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506699373/Framework%20integration/5/2.png)

### 二、测试  
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
