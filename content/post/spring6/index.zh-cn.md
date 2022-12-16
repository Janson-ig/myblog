---
title: Spring学习之路（六）
description: 配置属性(一)：创建自己的配置属性&profile搭建特定环境的配置
date: 2022-12-16
slug: spring6
image: 1.jpg
categories:
    - Spring
    - 学习笔记
tags:
    - 框架
---
&emsp;

## 创建自己的配置属性
&emsp;这章讲的有点啰嗦，主要就讲了一个注解：
* ```@ConfigurationProperties```：   
Spring Boot提供的支持配置属性的注入注释。放到Spring bean上后，它会为该bean中那些能够根据Spring环境注入值的属性赋值。  
&emsp;```@ConfigurationProperties```实际上通常会放到⼀种特定类型的bean中，这种bean的⽬的就是持有配置数据。这样的话，特定的配置细节就能从控制器和其他应⽤程序类中抽离出来，多个bean也能更容易地共享⼀些通⽤的配置。

* 注：Pageable是Spring Data根据页号和每页数量选取结果的子集的一种方法。
## Spring profile搭建特定环境的配置
&emsp;profile是⼀种条件化的配置，在运⾏时，根据哪些profile处于激活状态，可以使⽤或忽略不同的bean、配置类和配置属性
### 定义特定profile
&emsp;定义特定profile相关的属性的⼀种⽅式就是创建另外⼀个YAML或属性⽂件，其中只包含⽤于⽣产环境的属性。  
&emsp;⽂件的名称要遵守如下的约定：```application-{profile名}.yml```或 ```application-{profile名}.properties```。

1. 方法1  
新建名为application-prod.yml的文件，包含以下属性：
 ```yaml
 spring:
   datasource:
     url: jdbc:mysql://localhost/tacocloud
     username: tacouser
     password: tacopassword
 logging:
   level:
     tacos: WARN
```

2. 方法2  
将特定的profile和非特定profile的属性都放到application.yml中，二者之间使用三个中线【---】进行分隔，并且使用spring.profiles属性来命名profile。application.yml如下所示：  
```yaml
logging:
  level:
    tacos: DEBUG
---
spring:
  profiles: prod

  datasource:
   	url: jdbc:mysql://localhost/tacocloud
   	username: tacouser
   	password: tacopassword
logging:
  level:
    tacos: WARN
```

&emsp;该文件通过中划线(---)分成两部分。第二部分制定了spring.profiles值，代表**后面的属性适用于prod profile**。  
&emsp;第一部分没有指定**spring.profiles**，所以是**所有profiles通用**的，如果当前激活的profiles没有设置这些属性，它们会作为默认值。  

* 解释：  
&emsp;在上述例子，不管程序运行的时候哪个profile处于激活状态，根据**默认profile**，包的日志级别都会设为DEBUG。  
&emsp;但是如果名为prod的profile激活，```logging.level.tacos```属性会被重写为**WARN**。同理，如果prod profile处于激活状态，数据源相关属性会被设置为使用外部的MySQL数据库。

### 激活profile（三个方法）
1. 需要将profile名称的列表赋值给```spring.profiles.active```属性。例如，在```application.yml```文件中：
```yaml
spring:
  profiles:
    active:
    - prod
```
&emsp;如果这么激活，这个profile就会成为**默认profile**。体验不到使用profile将生产环境和开发环境属性分开的好处。
2. ```% export SPRING_PROFILES_ACTIVE=prod```
3. 以JAR文件形式运行，可以以**命令行参数**的形式设置要激活的profile：  
```% java -jar taco-cloud.jar --spring.profiles.active=prod```

#### 设置多个profile(两个方法)
1. YAML中：
```yaml
spring:
  profiles:
    active:
    - prod
    - audit
    - ha
```
2. ```% export SPRING_PROFILES_ACTIVE=prod,audit,ha```

#### 如果使用Cloud Foundry   
&emsp;Spring应用部署在Cloud Foundry，会自动激活名为**cloud**的profile，只需将生产环境相关的配置放到cloud profile下。  

## 使用profile条件化地创建bean
```@Profile```注解可以将某些bean设置为仅适用于给定的profile，例如：  
```java
@Bean
@Profile("dev")
public CommandLineRunner dataLoader(IngredientRepository repo,
    UserRepository userRepo, PasswordEncoder encoder) {
    ...
}
```
**用法**：
1. ```@Profile("dev")```:在dev profile激活时创建该bean  
2. ```@Profile({"dev", "qa"})```：dev**或**qa profile激活的时候创建该bean  
3. ```@Profile("!prod")```：prod profile不激活就创建该bean  
4. 与```@Configuration```注解一起使用：  
将 ```CommandLineRunner```抽取到一个名为```DevelopmentConfig```的配置类中：
```java
@Profile({"!prod", "!qa"})
@Configuration
public class DevelopmentConfig {
    @Bean
    public CommandLineRunner dataLoader(IngredientRepository repo,
        UserRepository userRepo, PasswordEncoder encoder) {
        ...
    }
}
```
只有prod和qa**均**没有激活的情况下才创建该bean。

> 本章完。  
> 如有遗漏或错误，欢迎补充纠正