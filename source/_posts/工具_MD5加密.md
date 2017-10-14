---
title: MD5加密的使用
tags: [Util,Encryption,MD5]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*  
**注意：**  
这里讲的是MD5加密算法的使用，而不是讲解MD5算法。
<!--more--> 
## MD5Utils工具类：  
这里调用的是java提供的信息加密工具，选择MD5加密。  
只需要将待加密的字符串，传递给md5方法，则该方法会返回加密后的定长字符串。  

	package cn.mwxu16.MD5;
	import java.math.BigInteger;
	import java.security.MessageDigest;
	import java.security.NoSuchAlgorithmException;
	import org.junit.Test;
	public class MD5Utils {
	     /**
	      * 使用md5的算法进行加密
	      */
	     public static String md5(String plainText) {
	           byte[] secretBytes = null;
	           try {
	                secretBytes = MessageDigest.getInstance("md5").digest(
	                           plainText.getBytes());
	           } catch (NoSuchAlgorithmException e) {
	                throw new RuntimeException("没有md5这个算法！");
	           }
	           String md5code = new BigInteger(1, secretBytes).toString(16);// 16进制数字
	           // 如果生成数字未满32位，需要前面补0
	           for (int i = 0; i < 32 - md5code.length(); i++) {
	                md5code = "0" + md5code;
	           }
	           return md5code;
	     }
	     @Test
	     public void test() {
	           System.out.println(md5("123"));
	     }
	}

执行test方法，即可输出加密后的字符串。  
如上例中，加密“123”，输出结果为：  

	202cb962ac59075b964b07152d234b70

## MD5加密的特点：  
是一种丢失精度的、不可逆的加密算法，可以将任意长的字符串，以字节为单位，加密成一个定长的字符串。  

**应用场景：**  
1、文件传输校验。  
只要文件传输前后，文件的内容有任何变化，则传输前后对文件加密得到的字符串都会不同。  

2、密码加密存储  
对数据库中用户的密码加密存储，以提高安全性。（防止数据库泄露时密码暴露）
