---
title: 代理ip的获取和使用
tags: [Spider,HttpClient,ProxyIP]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*  

本文只简单介绍一下代理ip的获取和使用，并没有介绍java爬虫。  
有空再单独开一个系列说爬虫吧。
<!--more--> 

## 一、代理ip简介  
为了防止IP被网站封掉而不能继续爬取页面，爬虫程序需要不断的更换IP地址等信息，来伪装成一个真实用户。  

### 代理IP的分类：  
注：关于代理ip的分类信息来源于互联网。  
关于代理IP的话 也分几种 透明代理、匿名代理、混淆代理、高匿代理  

1、透明代理(Transparent Proxy)  
REMOTE\_ADDR = Proxy IP  
HTTP\_VIA = Proxy IP   
HTTP\_X\_FORWARDED_FOR = Your IP  
透明代理虽然可以直接“隐藏”你的IP地址，但是还是可以从HTTP\_X\_FORWARDED_FOR来查到你是谁。  

2、匿名代理(Anonymous Proxy)  
REMOTE\_ADDR = proxy IP  
HTTP\_VIA = proxy IP  
HTTP\_X\_FORWARDED_FOR = proxy IP  
匿名代理比透明代理进步了一点：别人只能知道你用了代理，无法知道你是谁。  

3、混淆代理(Distorting Proxies)  
REMOTE\_ADDR = Proxy IP  
HTTP\_VIA = Proxy IP  
HTTP\_X\_FORWARDED_FOR = Random IP address  
如上，与匿名代理相同，如果使用了混淆代理，别人还是能知道你在用代理，但是会得到一个假的IP地址，伪装的更逼真。   

4、高匿代理(Elite proxy或High Anonymity Proxy)  
REMOTE\_ADDR = Proxy IP  
HTTP\_VIA = not determined  
HTTP\_X\_FORWARDED_FOR = not determined  
可以看出来，高匿代理让别人根本无法发现你是在用代理，所以是最好的选择。  

## 二、获取代理ip  

可以写一个小爬虫来爬取这些免费ip代理网站上的代理IP和端口号。  
注意：可用率真的很低，需要再次筛选后才能使用。  
### 分享几个免费IP代理网站：  
1、西刺代理  
  
	http://www.xicidaili.com/  
2、66免费代理网   
 
	http://www.66ip.cn/  
3、快代理  

	http://www.kuaidaili.com/free/  
4、站大爷（不好用，端口号是图片，不易爬取。不过据说IP的质量还不错）  
  
	http://ip.zdaye.com/  

免费代理ip网站有很多，包括国内的和国外的，但是因为免费，所以可用率低到不行，因此获取之后再筛选是免不了的。  
ip代理可以从各种网站上抓取，放到数据库中。  
当一个ip地址被封时（检测response中的状态码），从数据库中取出一条新的ip使用。  
同时数据库中的ip代理要定时检测是否可用，不可用的ip代理要及时删除。  

### 判断ip代理是否可用：  
使用该ip代理来访问一个网址，比如百度的首页，如果response中的状态码为200.说明可以正常访问，则该ip代理是可用的，否则是不可用的。  

## 三、在java爬虫中使用代理ip
使用代理IP的完整实现代码：  

	package cn.mwxu16.DaiLi_IP;
	import java.io.IOException;
	import org.apache.http.HttpEntity;
	import org.apache.http.HttpHost;
	import org.apache.http.client.config.RequestConfig;
	import org.apache.http.client.methods.CloseableHttpResponse;
	import org.apache.http.client.methods.HttpGet;
	import org.apache.http.impl.client.CloseableHttpClient;
	import org.apache.http.impl.client.HttpClients;
	import org.apache.http.util.EntityUtils;
	/*
	 * Demo1
	 * 使用代理IP
	 */
	public class Demo1 {
	     public static void main(String[] args) throws Exception{
	           CloseableHttpClient httpClient = HttpClients.createDefault();    // 创建一个HTTPClient对象   
	           HttpGet httpget = new HttpGet("http://www.baidu.com";);    // 创建HTTP的get请求实例
	           //==========设置代理IP
	           HttpHost proxy = new HttpHost("58.243.207.87",9858 );     //设置代理IP的地址和端口号
	           RequestConfig config =RequestConfig.custom().setProxy(proxy).build();
	           httpget.setConfig(config);
	           //===================
	           // 设置请求头信息，模拟浏览器，这里使用火狐浏览器的User-Agent
	           httpget.setHeader("User-Agent","Mozilla/5.0 (Windows NT 6.1; Win64; x64; rv:55.0) Gecko/20100101 Firefox/55.0");     
	           CloseableHttpResponse response = null;          // 创建CloseableHttpResponse对象
	           response = httpClient.execute(httpget);                              
	           HttpEntity entity = response.getEntity();  // 获取返回实体（即返回的网页源码）
	           System.out.println(EntityUtils.toString(entity, "utf-8"));      //输出网页源码
	           closeAll(response, httpClient);      //释放资源
	     }
	     // 关闭所有资源
	     public static void closeAll(CloseableHttpResponse response, CloseableHttpClient httpClient) {
	           // 关闭资源
	           try {
	                response.close(); // 关闭response
	           } catch (IOException e) { // IO异常
	                e.printStackTrace();
	           }
	           try {
	                httpClient.close(); // 关闭httpClient
	           } catch (IOException e) { // IO异常
	                e.printStackTrace();
	           }
	     }
	}

