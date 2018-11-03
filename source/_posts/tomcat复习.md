---
title: tomcat复习
date: 2018-04-03 10:15:38
tags:
---
# Tomcat是什么?
Tomcat 是由 Apache 开发的一个 Servlet 容器，实现了对 Servlet 和 JSP 的支持，并提供了作为Web服务器的一些特有功能，如Tomcat管理和控制平台、安全域管理和Tomcat阀等。

由于 Tomcat 本身也内含了一个 HTTP 服务器，它也可以被视作一个单独的 Web 服务器。但是，不能将 Tomcat 和 Apache HTTP 服务器混淆，Apache HTTP 服务器是一个用 C 语言实现的 HTTP Web 服务器；这两个 HTTP web server 不是捆绑在一起的。Tomcat 包含了一个配置管理工具，也可以通过编辑XML格式的配置文件来进行配置。

# tomcat重要目录
- /bin -Tomcat脚本存放目录. .sh文件用于Unix文件
- /conf - Tomcat配置文件目录
- /logs - Tomcat默认日志目录
- /webapps - webapp 运行目录

# web工程发布目录结构
```
|-- webapp                         # 站点根目录
    |-- META-INF                   # META-INF 目录
    |   `-- MANIFEST.MF            # 配置清单文件
    |-- WEB-INF                    # WEB-INF 目录
    |   |-- classes                # class文件目录
    |   |   |-- *.class            # 程序需要的 class 文件
    |   |   `-- *.xml              # 程序需要的 xml 文件
    |   |-- lib                    # 库文件夹
    |   |   `-- *.jar              # 程序需要的 jar 包
    |   `-- web.xml                # Web应用程序的部署描述文件
    |-- <userdir>                  # 自定义的目录
    |-- <userfiles>                # 自定义的资源文件
```
```
webapp：工程发布文件夹。其实每个 war 包都可以视为 webapp 的压缩包。

META-INF：META-INF 目录用于存放工程自身相关的一些信息，元文件信息，通常由开发工具，环境自动生成。

WEB-INF：Java web应用的安全目录。所谓安全就是客户端无法访问，只有服务端可以访问的目录。

/WEB-INF/classes：存放程序所需要的所有 Java class 文件。

/WEB-INF/lib：存放程序所需要的所有 jar 文件。

/WEB-INF/web.xml：web 应用的部署配置文件。它是工程中最重要的配置文件，它描述了 servlet 和组成应用的其它组件，以及应用初始化参数、安全管理约束等
```
# Server
Server元表示整个Catelina servlet容器
因此,它必须是conf/server.xml配置文件的根元素,它的属性代表了整个servlet容器的特性
## 属性表

属性 | 描述 | 备注
---|---|---
classname|这个类必须实现org.apache.catalina.Server接口|默认org.apache.catalina.core.StandardServer
address|服务器等待关机命令的TCP/IP地址. 如果没有指定地址,则使用localhost| 
port|服务器等待关机命令的TCP/IP端口号.设置-1以禁止关闭端口
shutdown|必须通过TCP / IP连接接收到指定端口号的命令字符串，以关闭Tomcat。

# Service
Service元素表示一个或多个连接器组件的组合，这些组件共享一个用于处理传入请求的引擎组件。Server 中可以有多个 Service。
```
<?xml version="1.0" encoding="UTF-8"?>
<Server port="8080" shutdown="SHUTDOWN">
  <Service name="xxx">
  ...
  </Service>
</Server>
```

#Executor
executor表示可以在Tomcat的组件之间共享的线程池
```
<Service name="xxx">
  <Executor name="tomcatThreadPool" namePrefix="catalina-exec-" maxThreads="300" minSpareThreads="25"/>
</Service>
```
# Connector
Connector代表连接组件.Tomcat 支持三种协议：HTTP/1.1、HTTP/2.0、AJP。
 
属性 | 描述 | 备注 
---|---|---
asyncTimeout| 	Servlet3.0规范中的异步请求超时| 	默认30s
port |	请求连接的TCP Port |	设置为0,则会随机选取一个未占用的端口号
protocol |	协议. 一般情况下设置为 HTTP/1.1,这种情况下连接模型会在NIO和APR/native中自动根据配置选择 	
URIEncoding |	对URI的编码方式. |	如果设置系统变量org.apache.catalina.STRICT_SERVLET_COMPLIANCE为true,使用 ISO-8859-1编码;如果未设置此系统变量且未设置此属性, 使用UTF-8编码
useBodyEncodingForURI |	是否采用指定的contentType而不是URIEncoding来编码URI中的请求参数

# Context
Context元素表示一个Web应用程序, 它在特定的虚拟主机中运行,每个web应用程序都基于Web应用程序存档（WAR）文件，或者包含相应的解包内容的相应目录，如Servlet规范中所述。

属性 | 描述 | 备注 
---|---|---
altDDName |	web.xml部署描述符路径 |	默认 /WEB-INF/web.xml
docBase |	Context的Root路径 |	和Host的appBase相结合, 可确定web应用的实际目录
failCtxIfServletStartFails |	同Host中的failCtxIfServletStartFails, 只对当前Context有效 |	默认为false
logEffectiveWebXml |	是否日志打印web.xml内容(web.xml由默认的web.xml和应用中的web.xml组成) |	默认为false
path |	web应用的context path |	如果为根路径,则配置为空字符串(""), 不能不配置
privileged 	| 是否使用Tomcat提供的manager servlet 	| 
reloadable 	| /WEB-INF/classes/ 和/WEB-INF/lib/ 目录中class文件发生变化是否自动重新加载 |	默认为false
swallowOutput |	true情况下, System.out和System.err输出将被定向到web应用日志中 |	默认为false

# Engine

Engine元素表示与特定的Catalina服务相关联的整个请求处理机器。它接收并处理来自一个或多个连接器的所有请求，并将完成的响应返回给连接器，以便最终传输回客户端。
# Host
Host元素表示一个虚拟主机，它是一个服务器的网络名称（如“www.mycompany.com”）与运行Tomcat的特定服务器的关联。

# 启动

1. 部署方式
```
将打包好的 war 包放在 Tomcat 安装目录下的 webapps 目录下，然后在 bin 目录下执行 startup.bat 或 startup.sh ，Tomcat 会自动解压 webapps 目录下的 war 包。

成功后，可以访问 http://localhost:8080/xxx （xxx 是 war 包文件名）。
```
2. 嵌入式
```
在 pom.xml 中添加依赖

<dependency>
  <groupId>org.apache.tomcat.embed</groupId>
  <artifactId>tomcat-embed-core</artifactId>
  <version>8.5.24</version>
</dependency>

添加 SimpleEmbedTomcatServer.java 文件
```

