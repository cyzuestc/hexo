---
title: javaMail学习记录
date: 2018-04-16 09:27:38
tags:
---
# 前言
这段时间需要实时给自己发送消息,思来想去最方便快捷的还是邮件. 所以搜了很多教程, 填了很多坑. 在搞完这些之际,特回头来总结一下.
其中, 关键的步骤为:
```
 Properties prop = new Properties;
 Session session = javax.mail.Session.getInstance(prop);
 Transport ts = session.getTransport();
 Message msg = createSimpleMail(session);
 ts.sendMessage(msg,msg.getAllRecipients);
```

# 设置邮件properties 和session
```
public void sendMail(){
	//new 一个properties
	Properties prop = new Properties();
	prop.put("mail.host","smtp.qq.com");
	prop.put("mail.transport.protocol","smtp");
	prop.put("mail.smtp.auth",true);
	//此行缺失会导致qq邮箱无法发送,但163可以发送
	prop.put("mail.smtp.starttls.enable","true");
	Security.addProvider(new com.sun.net.ssl.internal.ssl.Provider);
	
	//获取session
	Session session = javax.mail.Session.getInstance(prop);
	session.setDebug(true);
	
	//获取transport
	Transport ts = session.getTransport();
	ts.connect(user,pass);
	
	//获取Message
	Message msg = createSimpleMail(session);
	
	//发送
	ts.sendMessage(msg,msg.getAllRecipients);
	
	
	
}

```



# MimeMessage :构建邮件体
```
public static MimeMessage createSimpleMail(Session session) throw AddressException,MessagingException{
	MimeMessage mm = new MimeMessage(session);
	//设置发件人
	mm.setFrom(new InternetAddress("from@qq.com"));
	//设置收件人
	mm.setRecipient(Message.RecipientType.TO,new InternetAddress("to@qq.com"));
	//设置抄送人
	mm.setRecipient(Message.RecipientType.CC, new InternetAddress("用户名@163.com"));
	//设置题目和内容
	mm.setSubject(mail_title);
    mm.setContent(mail_content, "text/html;charset=gbk");
	
	return mm;
```