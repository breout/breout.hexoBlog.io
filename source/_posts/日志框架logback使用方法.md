---
title: 日志框架logback使用方法
date: 2022-08-26 10:38:53
author: jason.xiang
tags:
- logback
---

# 一、logback的介绍

Logback是由log4j创始人设计的又一个开源日志组件。logback当前分成三个模块：logback-core，logback- classic和logback-access。logback-core是其它两个模块的基础模块。logback-classic是log4j的一个改良版本。此外logback-classic完整实现SLF4J API使你可以很方便地更换成其它日志系统如log4j或JDK14 Logging。logback-access访问模块与Servlet容器集成提供通过Http来访问日志的功能。 Logback是要与SLF4J结合起来用两个组件的官方网站如下：

logback的官方网站： [http://logback.qos.ch](http://logback.qos.ch/download.html)

SLF4J的官方网站：[http://www.slf4j.org](http://www.slf4j.org/download.html)

**本文章用到的组件如下：**

logback-access-1.0.0.jar（这个可以不用）

logback-classic-1.0.0.jar

logback-core-1.0.0.jar

slf4j-api-1.6.0.jar



```xml
<!-- logback+slf4j -->  
<dependency>  
    <groupId>org.slf4j</groupId>  
    <artifactId>slf4j-api</artifactId>  
    <version>1.6.0</version>  
    <type>jar</type>  
    <scope>compile</scope>  
</dependency>  
<dependency>  
    <groupId>ch.qos.logback</groupId>  
    <artifactId>logback-core</artifactId>  
    <version>0.9.28</version>  
    <type>jar</type>  
</dependency>  
<dependency>  
    <groupId>ch.qos.logback</groupId>  
    <artifactId>logback-classic</artifactId>  
    <version>0.9.28</version>  
    <type>jar</type>  
</dependency>  
```

 

# 二、Logback的配置介绍

**1、Logger、appender及layout**

Logger作为日志的记录器，把它关联到应用的对应的context上后，主要用于存放日志对象，也可以定义日志类型、级别。

Appender主要用于指定日志输出的目的地，目的地可以是控制台、文件、远程套接字服务器、 MySQL、PostreSQL、 Oracle和其他数据库、 JMS和远程UNIX Syslog守护进程等。 

Layout负责把事件转换成字符串，格式化的日志信息的输出。

**2、logger context**

各个logger都被关联到一个LoggerContext，LoggerContext负责制造logger，也负责以树结构排列各 logger。其他所有logger也通过org.slf4j.LoggerFactory 类的静态方法getLogger取得。 getLogger方法以logger名称为参数。用同一名字调用LoggerFactory.getLogger 方法所得到的永远都是同一个logger对象的引用。

**3、有效级别及级别的继承**

Logger 可以被分配级别。级别包括：TRACE、DEBUG、INFO、WARN 和 ERROR，定义于ch.qos.logback.classic.Level类。如果 logger没有被分配级别，那么它将从有被分配级别的最近的祖先那里继承级别。root logger默认级别是DEBUG。

**4、打印方法与基本的选择规则**

打印方法决定记录请求的级别。例如，如果L是一个logger实例，那么，语句L.info("..")是一条级别为 INFO 的记录语句。记录请求的级别在高于或等于其logger的有效级别时被称为被启用，否则，称为被禁用。记录请求级别为 p，其logger的有效级别为q，只有则当p>=q时，该请求才会被执行。

***\*该规则是\**l****ogback的核心。级别排序为： TRACE < DEBUG < INFO < WARN < ERROR。** 

# 三、Logback的默认配置

如果配置文件logback-test.xml和logback.xml都不存在，那么logback默认地会调用BasicConfigurator ，创建一个最小化配置。最小化配置由一个关联到根logger的ConsoleAppender组成。输出用模式为%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n的PatternLayoutEncoder进行格式化。root logger默认级别是DEBUG。

**1、Logback的配置文件**

Logback配置文件的语法非常灵活。正因为灵活，所以无法用DTD或XML schema进行定义。尽管如此，可以这样描述配置文件的基本结构：以<configuration>开头，后面有零个或多个<appender>元素，有零个或多个<logger>元素，有最多一个<root>元素。

**2、Logback默认配置的步骤**

(1). 尝试在classpath下查找文件logback-test.xml；

(2). 如果文件不存在，则查找文件logback.xml；

(3). 如果两个文件都不存在，logback用Bas icConfigurator自动对自己进行配置，这会导致记录输出到控制台。

**3、Logback.xml文件**    

HTML

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--定义日志文件的存储地址 勿在 LogBack 的配置中使用相对路径-->  
    <property name="LOG_HOME" value="c:/log" />  
    <!-- 控制台输出 -->   
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
       <!-- 日志输出编码 -->  
       <Encoding>UTF-8</Encoding>   
        <layout class="ch.qos.logback.classic.PatternLayout">   
             <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符--> 
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n   
            </pattern>   
        </layout>   
    </appender>   
    <!-- 按照每天生成日志文件 -->   
    <appender name="FILE"  class="ch.qos.logback.core.rolling.RollingFileAppender">   
        <Encoding>UTF-8</Encoding>   
        <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
            <!--日志文件输出的文件名-->
            <FileNamePattern>${LOG_HOME}/myApp.log.%d{yyyy-MM-dd}.log</FileNamePattern>   
            <MaxHistory>30</MaxHistory>
        </rollingPolicy>   
        <layout class="ch.qos.logback.classic.PatternLayout">  
            <!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符--> 
            <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50} - %msg%n   
            </pattern>   
       </layout> 
        <!--日志文件最大的大小-->
       <triggeringPolicy class="ch.qos.logback.core.rolling.SizeBasedTriggeringPolicy">
         <MaxFileSize>10MB</MaxFileSize>
       </triggeringPolicy>
    </appender> 
   <!-- show parameters for hibernate sql 专为 Hibernate 定制 -->  
    <logger name="org.hibernate.type.descriptor.sql.BasicBinder"  level="TRACE" />  
    <logger name="org.hibernate.type.descriptor.sql.BasicExtractor"  level="DEBUG" />  
    <logger name="org.hibernate.SQL" level="DEBUG" />  
    <logger name="org.hibernate.engine.QueryParameters" level="DEBUG" />  
    <logger name="org.hibernate.engine.query.HQLQueryPlan" level="DEBUG" />  
    
    <!-- 日志输出级别 -->
    <root level="INFO">   
        <appender-ref ref="STDOUT" />   
        <appender-ref ref="FILE" />   
    </root> 
     
     <!--日志异步到数据库 -->  
    <appender name="DB" class="ch.qos.logback.classic.db.DBAppender">
        <!--日志异步到数据库 --> 
        <connectionSource class="ch.qos.logback.core.db.DriverManagerConnectionSource">
           <!--连接池 --> 
           <dataSource class="com.mchange.v2.c3p0.ComboPooledDataSource">
              <driverClass>com.mysql.jdbc.Driver</driverClass>
              <url>jdbc:mysql://127.0.0.1:3306/databaseName</url>
              <user>root</user>
              <password>root</password>
            </dataSource>
        </connectionSource>
  </appender> -->
</configuration>
```

# 四、在程序用引用Logback

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class BlogAction{
     //定义一个全局的记录器，通过LoggerFactory获取
     private static final Logger logger = LoggerFactory.getLogger(BlogAction.class); 
     /**
     * @param args
     */
    public static void main(String[] args) {
        logger.info("logback 成功了");
        logger.error("logback 成功了");
    }
}
```
