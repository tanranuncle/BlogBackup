---
title: SSH（第七步）—— HibernateTemplate模板操作数据库
tags: [Struts2,Spring,Hibernate,Framework]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*
**注意：**  
点击即可下载：[完整的SSH项目整合Demo](http://download.csdn.net/download/qq_28592887/9994986)

### 一、创建Dao接口及其实现类  
UserDao接口  
<!--more-->
	package cn.mwxu16.dao;
	import cn.mwxu16.domain.User;
	/*
	 * UserDao接口
	 */
	public interface UserDao {
	      //根据登陆名称查询user对象
	            User getByUserCode(String usercode);
	            //保存用户
	            void save(User u);
	}

注：  
1、可以通过HQL来查询，也可以通过Criteria来查询。  
2、需要继承HibernateDaoSupport。  
下面在同一个类中演示两种查询方式（实际操作中只保留一种方式即可）：  
UserDaoImpl实现类一：  
通过HQL来查询  

	package cn.mwxu16.dao.Impl;
	import org.hibernate.HibernateException;
	import org.hibernate.Query;
	import org.hibernate.Session;
	import org.springframework.orm.hibernate5.HibernateCallback;
	import org.springframework.orm.hibernate5.support.HibernateDaoSupport;
	import cn.mwxu16.dao.UserDao;
	import cn.mwxu16.domain.User;
	//HibernateDaoSupport 为dao注入sessionFactory
	public class UserDaoImpl extends HibernateDaoSupport implements UserDao {
	      
	      
	      @Override
	      public User getByUserCode(final String usercode) {
	            //HQL
	            return getHibernateTemplate().execute(new HibernateCallback<User>() {
	                  @Override
	        public User doInHibernate(Session session) throws HibernateException {
	                              String hql = "from User where user_code = ? ";
	                              Query query = session.createQuery(hql);
	                              query.setParameter(0, usercode);
	                              User user = (User) query.uniqueResult();
	                        return user;
	                  }
	            });
	      }
	      @Override
	      public void save(User u) {
	            getHibernateTemplate().save(u);
	      }
	}

UserDaoImpl实现类二：  
通过Criteria来查询   

	package cn.mwxu16.dao.Impl;
	import java.util.List;
	import org.hibernate.HibernateException;
	import org.hibernate.Query;
	import org.hibernate.Session;
	import org.hibernate.criterion.DetachedCriteria;
	import org.hibernate.criterion.Restrictions;
	import org.springframework.orm.hibernate5.HibernateCallback;
	import org.springframework.orm.hibernate5.HibernateTemplate;
	import org.springframework.orm.hibernate5.support.HibernateDaoSupport;
	import cn.mwxu16.dao.UserDao;
	import cn.mwxu16.domain.User;
	//HibernateDaoSupport 为dao注入sessionFactory
	public class UserDaoImpl extends HibernateDaoSupport implements UserDao {
	      
	      
	      @Override
	      public User getByUserCode(final String usercode) {
	            //Criteria
	            DetachedCriteria dc = DetachedCriteria.forClass(User.class);
	            
	            dc.add(Restrictions.eq("user_code", usercode));
	            
	            List<User> list = (List<User>) getHibernateTemplate().findByCriteria(dc);
	                  
	            if(list != null && list.size()>0){
	                  return list.get(0);
	            }else{
	                  return null;
	            }
	      }
	      @Override
	      public void save(User u) {
	            getHibernateTemplate().save(u);
	      }
	}

### 二、将UserDao配置到Spring容器中，并将SessionFactory注入到UserDao中

	<!-- 配置UserDao -->
	      <bean name="userDao" class="cn.mwxu16.dao.Impl.UserDaoImpl" >
	            <!-- 注入sessionFactory -->
	            <property name="sessionFactory" ref="sessionFactory" ></property>
	      </bean>

![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506700242/Framework%20integration/7/1.png)  

### 三、测试  
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
	
	     //将UserDao对象注入进来
	      @Resource(name="userDao")
	      private UserDao ud;
	      @Test
	      public void fun3() {
	            User u = ud.getByUserCode("tom");
	            System.out.println(u);
	      }
	}

若成功在控制台输出user对象的信息，则说明操作成功。
