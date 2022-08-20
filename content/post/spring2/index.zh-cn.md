---
title: Spring学习之路（二）
description: 几个模板引擎及模板缓存
date: 2022-08-15
slug: spring
image: 1.jpg
categories:
    - Spring
    - 学习笔记
tags:
    - 框架
---
## 模板引擎
书中总结了以下模板：

|          模板           |        Spring Boot starter依赖         |
|:---------------------:|:------------------------------------:|
| JavaServer Pages(JSP) |          无(由Tomcat或Jetty提供)          |
|      FreeMarker       |    spring-boot-starter-freemarker    |
|   Groovy Templates    | spring-boot-starter-groovy-templates |
|       Mustache        |     spring-boot-starter-mustache     |
|       Thymeleaf       |    spring-boot-starter-thymeleaf     |  

其中，JSP不需要依赖的原因:
* 是因为Servlet容器本身（默认是Tomcat）会实现JSP，因此不需要额外的依赖。  

使用JSP问题： 
* Java Servlet容器包括嵌入式的Tomcat和Jetty容器，通常会在“/WEB-INF”目录下寻找JSP。如果将应用构建成一个可执行的JAR文件，就无法满足这种需求；**只有在将应用构建成WAR文件并部署到Servlet容器中时**，才能选择JSP方案。

## 模板缓存
* 默认情况下，模板只有在**第⼀次使用的时候解析⼀次**，解析的结果会被后续的请求所使⽤。开发期缓存存在时，想要看到变更效果，必须重新启动应用，非常不方便 
* 禁用模板缓存：将相关缓存属性设成false:在application.properties中添加：
`spring.thymeleaf.cache=false`

|        模板        |           启用缓存的属性            |
|:----------------:|:----------------------------:|
|    FreeMarker    |   spring.freemarker.cache    |
| Groovy Templates | spring.groovy.template.cache |
|     Mustache     |    spring.mustache.cache     |
|    Thymeleaf     |    spring.thymeleaf.cache    |  

## Thymeleaf模板
* Thymeleaf 模板只是 HTML 与一些额外的元素属性，指导模板在渲染请求数据。 

  &emsp;_例如，如果有一个请求属性，它的键是 “message”，你希望它被Thymeleaf渲染成一个`HTML<p>`标签，你可以在你的Thymeleaf模板中写以下内容：_  
    `<p th:text="${message}">placeholder message</p>`

* Thymeleaf 提供了一个内置的field.hasErrors方法，该方法根据给定字段是否存在任何错误返回一个布尔值。  

  &emsp;例如要在一个信用卡号字段上显示验证错误：  
  `<label for="ccNumber">Credit Card #: </label>  
  <input type="text" th:field="*{ccNumber}"/>  
  <span class="validationError"  
  th:if="${#fields.hasErrors('ccNumber')}"  
  th:errors="*{ccNumber}">CC Num Error</span>`  

> 如有遗漏或错误，欢迎补充纠正