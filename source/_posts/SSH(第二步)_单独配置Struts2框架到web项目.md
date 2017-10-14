---
title: SSH（第二步）—— 单独配置Struts2框架到web项目
tags: [Struts2,Spring,Hibernate,Framework]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*
**注意：**  
点击即可下载：[完整的SSH项目整合Demo](http://download.csdn.net/download/qq_28592887/9994986)

### 一、创建action
示例：
<!--more-->
	package cn.mwxu16.web.action;
	import com.opensymphony.xwork2.ActionSupport;
	public class UserAction extends ActionSupport{
	      
	      public String hello(){
	            
	            return SUCCESS;
	      } 
	}

### 二、创建配置文件
1、在项目的src路径下，创建一个名为struts.xml的配置文件。  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506521154/Framework%20integration/2/1.png)
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506521154/Framework%20integration/2/2.png)  

2、并导入约束。  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506521154/Framework%20integration/2/3.png)

3、将UserAction配置到Struts配置文件中

	<!-- 配置package -->
	      <package name="ssh" namespace="/" extends="struts-default">
	      
	            <!-- 通过动态方法调用，配置action -->
	            <action name="userAction_*" class="cn.mwxu16.web.action.UserAction" method="{1}">
	                  <result name="success">/success.jsp</result>
	            </action>
	      
	      </package>
如下图所示：  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506521154/Framework%20integration/2/4.png)

### 三、配置Struts2的核心过滤器
在web.xml文件中配置。  
过滤器的名字可以随意，上下保持一致即可。  
全类名需要搜索，具体查找方法参照Struts的笔记。  
虚拟路径为/*，即希望Struts2框架来过滤所有的请求。  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506521154/Framework%20integration/2/5.png)

### 四、创建success.jsp（用于测试）
页面随便输出一句话即可。

通过访问配置的Action中的方法，测试是否能转到指定的页面。
测试成功即说明Struts2框架单独配置完成。



