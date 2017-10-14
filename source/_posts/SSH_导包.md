---
title: SSH（第零步）—— 导包
tags: [Struts2,Spring,Hibernate,Framework]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*
**注意：**  
点击即可下载：[完整的SSH项目整合Demo](http://download.csdn.net/download/qq_28592887/9994986)  

SSH三大框架整合，分别导入三大框架的jar包。  
### 一、导入Hibernate框架所需jar包  
包括框架所需的jar包和数据库驱动包。 
<!--more--> 
**1、数据库驱动包（以MySQL为例）**   
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355073/Framework%20integration/0/1.png)  

**2、框架所需的jar包**  
要导入一个jpa的jar包（是java的持久化规范）  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355085/Framework%20integration/0/2.png)  

Hibernate框架下的lib下的required文件夹中的所有jar包（必需jar包）必须导入。
其他jar包则根据需要导入。  

![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355073/Framework%20integration/0/3.png)  

### 二、导入Struts2框架所需jar包
将Struts2框架下的示例项目中的jar包导入。  
注意，其中有一个名为
javassist-xxxxxxxx.jar的jar包，Hibernate中也导入了这个jar包，因此jar包重复，所以只留下版本较新的一个即可。
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355093/Framework%20integration/0/4.png)  

另外需要导入一个插件包（该包在Struts框架下的lib目录下），用于整合Struts和Spring：  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355093/Framework%20integration/0/5.png)  

注意：
若导入这个包，则项目启动时，Struts2框架会自动寻找Spring容器。因此，若不想整个Struts2框架和Spring框架时，不要导入这个包，否则项目启动时会报错。

### 三、导入Spring框架所需jar包
1、基本包
分别是：
core、beans、context、expression、logging、log4j  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355093/Framework%20integration/0/6.png)  

2、整合web的web包  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355093/Framework%20integration/0/7.png)   
 
3、整合aop的：4个  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355093/Framework%20integration/0/8.png)  
  
4、整合JDBC事务：4个包（其中数据库驱动因为Hibernate导包时已经导过了，因此这里不需要）
其中orm包是用来整合Hibernate框架的。  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355093/Framework%20integration/0/9.png)  

5、整合Junit4测试：test包  
![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355093/Framework%20integration/0/10.png)  

### 四、导入jstl标签库

![](http://res.cloudinary.com/dyb1o2amn/image/upload/v1506355093/Framework%20integration/0/11.png)  


