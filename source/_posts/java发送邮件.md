---
title: 在java中发送邮件
tags: [Util,mail,java]
---
## *[From : blog.mwxu16.cn](http://blog.mwxu16.cn)*  
在很多场景中，都会用到发送邮件。  
比如用户注册时验证邮箱、找回密码、发送通知等等。  
本文讲的是java中如何发送邮件。  
<!--more-->   
### 一、导入相关jar包
这里使用的是[mail.jar](https://github.com/tanranuncle/MyBlogImages/blob/master/Util/mail/mail.jar)（点击即可进入下载页面）  
### 二、准备账号
这里使用的是QQ邮箱账号来发送邮件。  
首先需要到QQ邮箱的账户设置界面，生成授权码。下面需要使用QQ邮箱账号+授权码来验证邮箱。  
![](https://raw.githubusercontent.com/tanranuncle/MyBlogImages/master/Util/mail/mail.png)  
### 三、准备MailUtils工具类  

	package test;
	
	import java.io.UnsupportedEncodingException;
	import java.security.GeneralSecurityException;
	import java.util.Properties;
	
	import javax.mail.Authenticator;
	import javax.mail.Message;
	import javax.mail.MessagingException;
	import javax.mail.PasswordAuthentication;
	import javax.mail.Session;
	import javax.mail.Transport;
	import javax.mail.internet.AddressException;
	import javax.mail.internet.InternetAddress;
	import javax.mail.internet.MimeMessage;
	import javax.mail.internet.MimeMessage.RecipientType;
	
	import com.sun.mail.util.MailSSLSocketFactory;
	
	public class MailUtils {
	
		public static void sendMail(String email, String emailMsg)
				throws AddressException, MessagingException, GeneralSecurityException, UnsupportedEncodingException {
			// 1.创建一个程序与邮件服务器会话对象 Session
	
			Properties props = new Properties();
			props.setProperty("mail.transport.protocol", "SMTP");
			props.setProperty("mail.host", "smtp.qq.com");				//注释1
			props.setProperty("mail.smtp.auth", "true");// 指定验证为true
	
			//QQ邮箱的SSL加密。					//注释2
			MailSSLSocketFactory sf = new MailSSLSocketFactory();
			sf.setTrustAllHosts(true);
			props.put("mail.smtp.ssl.enable", "true");
			props.put("mail.smtp.ssl.socketFactory", sf);
			
			
			// 创建验证器
			Authenticator auth = new Authenticator() {
				public PasswordAuthentication getPasswordAuthentication() {
					return new PasswordAuthentication("此处为QQ邮箱对应的QQ号", "此处为生成的授权码");		//注释3
				}
			};
	
			Session session = Session.getInstance(props, auth);
	
			// 2.创建一个Message，它相当于是邮件内容
			Message message = new MimeMessage(session);
			message.setFrom(new InternetAddress("此处为发送者的邮箱","我是别名")); // 设置发送者（和别名）		//注释4
			message.setRecipient(RecipientType.TO, new InternetAddress(email)); // 设置发送方式与接收者
			message.setSubject("用户激活");	// 设置邮件主题
			
			// 设置邮件内容
			message.setContent(emailMsg, "text/html;charset=utf-8");
			
			// 3.创建 Transport用于将邮件发送
			Transport.send(message);
		}
	}

### 四、测试
测试类如下：  

	package test;
	
	import java.io.UnsupportedEncodingException;
	import java.security.GeneralSecurityException;
	import javax.mail.MessagingException;
	
	public class TestMail {
	
		public static void main(String[] args){
	
				try {
					MailUtils.sendMail("haha@heihei.com", "test mail...");			//注释5
				} catch (MessagingException | GeneralSecurityException | UnsupportedEncodingException e) {
					System.out.println("邮件发送失败！");
				}
		
		}
	}
运行测试类，如果上面步骤没有出错，应当能够成功发送邮件。
### 五、解说注释
**注释1：  **
这里使用的是QQ邮箱，因此设置的邮件服务器是**smtp.qq.com**，若使用126邮箱，应当设置为**smtp.126.com**。  

**注释2：  **
经测试，若使用QQ邮箱，则需要添加这部分SSL加密的代码。若使用其他邮箱，好像是不需要的。  
QQ邮箱若不添加这部分代码会导致发送失败。  

**注释3： ** 
这里的两个参数分别为邮箱账号和授权码。  
邮箱账号：取@符号前面的部分。例如邮箱为12345@qq.com，则此处的参数应该为12345。  
授权码：此处并非是邮箱的原本登录密码，而是生成的授权码。QQ邮箱生成授权码的方式参照上图。其他邮箱请自行搜索生成授权码的方式。  

**注释4：**  
这里的两个参数分别是发送者的邮箱，以及发送者的别名。其中别名是可选参数。  

邮箱：这里是完整的邮箱账号，如12345@qq.com。需要注意的是，由于QQ邮箱可以为邮箱绑定多个别名（以及域名邮箱），因此可以填写任意一个别名邮箱即可。  
例如，QQ邮箱为12345@qq.com，绑定的别名为abcde@qq.com，那么这里使用abcde@qq.com作为参数也是可以的。（这一点在使用域名邮箱是很重要）  

别名：这个别名是发送者的别名，注意和上面邮箱的别名区分开。  
以abcde@qq.com为例。若没有别名，收件方显示的发送者名称为abcde。若设置别名为张三，则收件方显示的发送者名称为张三。通常设置别名会使得体验更友好。  

**注释5：**  
这里的两个参数分别为收件人邮箱和邮件内容（都是字符串形式的）。