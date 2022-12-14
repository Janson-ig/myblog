---
title: Spring学习之路（五）
description: 配置属性(一)：自动配置
date: 2022-12-14
slug: spring5
image: 1.jpg
categories:
    - Spring
    - 学习笔记
tags:
    - 框架
---
&emsp;第六章内容第五版与第六版几乎相同。本章介绍了自动配置bean；创建自己的配置属性；使用profile进行配置。

# 自动配置：

### 细粒度的自动配置
**两种不同但相关的配置**  
1. Bean wiring（装配bean）：声明在Spring应⽤上下⽂中创建哪些应⽤组件以及它们应该如何相互注入。  
2. Property injection（依赖注入）声明在Spring应⽤上下⽂中创建哪些应⽤组件以及它们应该如何相互注入。

&emsp;在Spring的XML和Java配置中，这两种类型的配置通常在同一个地方**显式声明**。  
&emsp;在Java配置中，带@bean注解的方法很可能既实例化一个bean，又给它的属性赋值。举例来说，@Bean方法为H2数据库声明一个数据源：
```	
@Bean
public DataSource dataSource() {
    return new EmbeddedDatabaseBuilder()
        .setType(H2)
        .addScript("taco_schema.sql")
        .addScripts("user_data.sql", "ingredient_data.sql")
        .build();
}
```
```addScript()``` 和 ```addScripts()```方法通过设置String属性来确定数据源——**这是不使用Spring Boot的配置方法。**  
&emsp;如果在运行时类路径中能够找到H2依赖，那么**Spring Boot会自动在Spring应用上下文中创建对应的DataSource bean**。这个bean会运行名为```schema.sql```和```data.sql```的脚本。

### Spring的环境抽象
**Spring的环境抽象**  
&emsp;Spring环境会拉取多个属性源：
* JVM系统属性
* 操作系统环境变量
* 命令行参数
* 应用属性配置文件

&emsp;将这些属性聚合到一个源中，通过这个源可以注入到spring的bean中。如下图所示：
![img.png](img.png)
&emsp;Spring boot自动配置的bean都可以通过spring环境提取的属性进行配置.  
例如，希望底层Servlet容器使用另一个端口监听，有以下四个不同的方法：
1. “src/main/resources/application.properties”将server.port设置成一个不同的端口：server.port = 9090
2. “src/main/resources/application.yml”中: server : port : 9090
3. 命令行参数启动时指定端口：$ java -jar tacocloud-0.0.5-SNAPSHOT.jar --server.port=9090
4. 操作系统环境变量进行一次配置：$ export SERVER_PORT=9090（Spring能自动挑选环境变量名，将其解析）  

### 配置数据源
**显示配置自己的datasource**:  
在```application.yml```配置：
```
spring:
    datasource:
        url: jdbc:mysql://localhost/tacocloud
        username: tacodb
        password: tacopassword
```
&emsp;**尽管我们需要将对应的JDBC驱动添加到构建⽂件中，但是我们不需要指定JDBC驱动类。Spring Boot会根据数据库URL的结构推算出来。**  
#### 设置JDBC驱动类
&emsp;然⽽，我们依然可以通过```spring.datasource.driver-class-name```属性来进⾏设置JDBC驱动类：  
```
spring:
    datasource:
        url: jdbc:mysql://localhost/tacocloud
        username: tacodb
        password: tacopassword
        driver-class-name: com.mysql.jdbc.Driver
```
&emsp;Spring boot自动化配置DataSource bean的时候，会使用该连接。如果类路径存在```HikariCP```的连接池，会使用该连接池。否则在类路径查找并使用以下连接池：
* Tomcat JDBC Connection Pool
* Apache Commons DBCP 2  

注：旧版默认连接池是Tomcat的JDBC连接池，新版默认连接池已经变成HikariCP，因为它够快，代码量少，稳定。

### 配置嵌入式服务器
&emsp;如果在配置servlet容器端口时，将端口设为0，即：  
```
server:
    Port : 0
```
会发生什么？  
&emsp;虽然显式设为0，但是服务器不会在端口0上启动。而是任选一个可用的端口。这在运行自动化集成测试的时候，非常有用：因为可以保证并发运行的测试不会与硬编码的端口号冲突。

#### 配置使其处理HTTPS请求
&emsp;底层服务器配置不仅局限于一个端口，底层容器最常见的一项设置是让它处理HTTPS请求。  
&emsp;为了实现，需要使用JDK的keytool命令行工具生成keystore：  
```$ keytool -keystore mykeys.jks -genkey -alias tomcat -keyalg RSA```  

&emsp;接下来，为了在嵌入式服务器中启用HTTPS，需要设置属性：
1. 可以在命令行中设置，但是不方便
2. 可以通过```application.properties```或```application.yml```文件来声明配置：

```
server:
	port: 8443
	ssl:
		key-store: file:/ / /path/to/mykeys.jks
		key-store-password: letmein
		key-password: letmein
```
 
&emsp;解释：
1. 端口设为**8443**（开发阶段HTTPS服务器的常用选择）
2. ```server.ssl.key-store```设置为所创建的```keystore```路径
3. ```server.ssl.key-store-password```和```server.ssl.key-password```属性设置为创建```keystore```所设置的密码  

属性准备就绪后，应用会监听8443端口的HTTPS请求。

### 配置日志
&emsp;默认情况下，Spring Boot通过Logback配置日志，日志以INFO级别写入到控制台  
&emsp;为了完全控制日志的配置，可以在类路径的根目录下```src/main/resources```创建```logback.xml```文件。  
简单样例：  
```
<configuration>
    <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
		    <pattern>
				%d{HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %msg%n
			</pattern>
		</encoder>
	</appender>
	<logger name="root" level="INFO"/>
	<root level="INFO">
		<appender-ref ref="STDOUT" />
	</root>
</configuration>
```
&emsp;这个```Logback```和没有```logback.xml```文件时的默认行为是几乎完全相同的。但是编辑```logback.xml```文件，可以完全控制应用的日志文件。

&emsp;日志配置中最常见变更是**修改日志级别**和**指定日志写入到某个文件**，Spring Boot的配置属性功能如何实现？  
* 设置日志级别：  
在```application.yml```中添加条目，例如：  
```
logging:
    level:
        root: WARN
        org.springframework.security: DEBUG
```
以上配置将```root logging```设置为**WARN**级别，spring security的日志级别设为**DEBUG**。  

* 将日志条目写入指定文件：
如要将日志条目写入到"```/var/logs/```"中的TacoCloud文件中```logging.path```和```logging.file```文件可以如下配置：  
```
logging:
    file:
        path: /var/logs/
        file: TacoCloud.log
    level:
        root: WARN
        org:
            springframework:
                security: DEBUG
```

&emsp;如果应用具有指定目录的写入权限，日志条目会被写入到指定目录。  
&emsp;默认情况下，日志文件达到**10MB**就会轮换。

### 使用特定的属性值
&emsp;设置属性不局限于将值设置为硬编码的String或数值，可以从其他的**配置属性派生值**：  
&emsp;假设我们想要设置⼀个名为```greeting.welcome```的属性，它的值来源于名为```spring.application.name```的另⼀个属性：
&emsp;在设置```greeting.welcome```的时候，可以使用```${}```占位符标记：  
```
greeting:
welcome: ${spring.application.name}
```
&emsp;甚至可以将```${}```占位符嵌入到其他文本中：  
```
greeting:
welcome: You are using ${spring.application.name}.
```
&emsp;在配置Spring⾃⼰的组件时，使⽤配置属性可以很容易地将值注⼊这些组件属性中，并且可以细粒度地调整⾃动配置功能。  
&emsp;配置属性并不专属于Spring创建的bean。

> 如有遗漏或错误，欢迎补充纠正