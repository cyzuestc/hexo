---
title: log4j学习
date: 2018-04-18 09:37:46
tags:
---
# 使用场景
没有日志记录程序的运行过程,就无法对程序运行过程有一个宏观的反馈. 随着河畔鸡毛信的渐渐成熟, 添加log4j插件也是要摆上日程了.

# 使用概况
使用之前觉得很麻烦,使用之后发觉非常简单. 
1. 添加maven依赖
2. 编辑properties/xml文件
3. 添加到程序中
# 具体方法
```
 //使用jar包:log4j-1.2.17.jar
 static Logger logger = Logger.getLogger(TestLog4j.class);
 BasicConfigurator.configure();//默认的配置
 logger.setLevel(level.Debug);
 
 //或者自己配置
 PropertyConfigurator.configure("e:\\project\\log4j\\src\\log4j.properties");
```
配置文件的格式:
```
log4j.rootLogger=debug, stdout, R
 
log4j.appender.stdout=org.apache.log4j.ConsoleAppender
log4j.appender.stdout.layout=org.apache.log4j.PatternLayout
 
# Pattern to output the caller's file name and line number.
log4j.appender.stdout.layout.ConversionPattern=%5p [%t] (%F:%L) - %m%n
 
log4j.appender.R=org.apache.log4j.RollingFileAppender
log4j.appender.R.File=example.log
log4j.appender.R.MaxFileSize=100KB
log4j.appender.R.MaxBackupIndex=5
 
log4j.appender.R.layout=org.apache.log4j.PatternLayout
log4j.appender.R.layout.ConversionPattern=%p %t %c - %m%n
```
log4j日志输出格式一览：
%c 输出日志信息所属的类的全名
%d 输出日志时间点的日期或时间，默认格式为ISO8601，也可以在其后指定格式，比如：%d{yyy-MM-dd HH:mm:ss }，输出类似：2002-10-18- 22：10：28
%f 输出日志信息所属的类的类名
%l 输出日志事件的发生位置，即输出日志信息的语句处于它所在的类的第几行
%m 输出代码中指定的信息，如log(message)中的message
%n 输出一个回车换行符，Windows平台为“rn”，Unix平台为“n”
%p 输出优先级，即DEBUG，INFO，WARN，ERROR，FATAL。如果是调用debug()输出的，则为DEBUG，依此类推
%r 输出自应用启动到输出该日志信息所耗费的毫秒数
%t 输出产生该日志事件的线程名



