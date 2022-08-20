---
title: Spring学习之路（一）
description: Spring项目结构，@SpringBootApplication组合注解，Spring的重点项目汇总
date: 2022-08-13
slug: spring
image: 1.jpg
categories:
    - Spring
    - 学习笔记
tags:
    - 框架
---
## 前言
&emsp;学习路程是按照[Spring实战（第五版）](https://zh.jp1lib.org/book/18238678/2d772e "Spring实战（第五版）")进行，本系列只针对笔者自己进行内容总结，用于加深学习以及日后回顾使用。如感兴趣，推荐阅读参考图书进行学习。

&emsp;书中有较多地方描述比较不清楚，实战时，存在很多问题。[spring-in-action-5-samples](https://github.com/habuma/spring-in-action-5-samples "spring-in-action-5-samples")对书中每一章项目进行了汇总，对照此仓库学习更容易理解。

## Spring项目结构 
* mvnw和mvnw.cmd：这是Maven包装器（wrapper）脚本，借助脚本，即使没有安装Maven也可以构建项目
* pom.xml： Maven构建规范，用于引入依赖等
* Application.java：SpringBoot主类
* application.properties：初始为空，用来指定配置属性
* static：初始为空，用于存放静态内容（图片，样式表，JavaScript等）
* templates：初始为空，用于存放渲染内容到浏览器的模板文件
* ApplicationTests.java：简单的测试类，确保Spring应用上下文成功加载 

## @SpringBootApplication组合注解
@SpringBootApplication是一个组合注解，它组合了3个其他的注释：
* @SpringBootConfiguration：将该类声明为配置类。尽管这个类⽬前还没有太多的配置，但是后续我们可以按需添加基于Java的Spring框架配置。这个注解实际上是@Configuration注解的特殊形式。
* @EnableAutoConfiguration：启⽤Spring Boot的⾃动配置。我们随后会介绍⾃动配置的更多功能。就现在来说，我们只需要知道这个注解会告诉Spring Boot⾃动配置它认为我们会⽤到的组件。
* @ComponentScan：启⽤组件扫描。这样我们能够通过像@Component、 @Controller、@Service这样的注解声明其他类，Spring会⾃动发现它们并将它们注册为Spring应⽤上下⽂中的组件

## Spring的一些重点项目：
* Spring MVC——Spring的Web框架：可以边写控制器处理Web请求；创建REST API，生成非HTML的输出
* Spring Boot：starter依赖和自动配置；Actuator能洞察应用运行时的内部工作状况；灵活的环境属性规范；在核心框架的测试辅助功能之上提供了对测试的额外支持
* Spring Data：将应用程序数据repository定义为java接口，定义驱动存储和检索数据的方法时使用一种命名约定即可；能够处理不同类型的数据库，包括关系型数据库（JPA）、文档数据库（Mongo）、图数据库（Neo4j）等
* Spring Security：Spring的安全框架，解决了身份验证、授权和API安全性等安全性需求
* Spring Integration：解决实时集成问题，数据在可用时马上就会得到处理
* Spring Batch：批处理集成问题——数据收集一段时间，直到触发触发器，才进行批处理
* Spring Cloud：使用Spring开发云原生应用程序的项目

> 如有遗漏或错误，欢迎补充纠正