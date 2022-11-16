---
title: Spring学习之路（四）
description: Spring Security(二)：介绍其对OAuth2.0的支持，CSRF防护，方法级安全控制和判断用户方法。
date: 2022-11-16
slug: spring4
image: 1.jpg
categories:
    - Spring
    - 学习笔记
tags:
    - 框架
---
&emsp;本文是Spring Security系列最后一篇总结。

## 对OAuth2.0的支持
**OAuth2.0是什么？**  
&emsp;举个例子，就是网站中常见的第三方登录，登录某个网站支持使用Facebook，Google账号登录。微信网页授权也是基于OAuth2.0。同时1.0版本也已经被2.0完全取代了。  
* Spring Security支持Fackbook,Google,GitHub,Okta第三方登录
* 书中没有详细的配置教学：“ 通过OAuth2/OpenID连接客户端，你需要设置以下属性:”
```	
spring:
    security:
        oauth2:
            client:
                registration:
		        <oauth2 or openid provider name>:
		        clientId: <client id>
		        clientSecret: <client secret>
                scope: <comma-separated list of requested scopes>
```
&emsp;如果要启用OAuth2.0登录，需要在***Spring Security配置类***中添加```.and().oauth2Login()```方法。  
&emsp;同时提供传统用户名-密码登录方式和第三方登录：```.and().oauth2Login().loginPage("/login")```
&emsp;启用logout登出功能：
```java
    /**
 * 启用logout登出:点击logout时，session被清空，同时登出
 * logoutSuccessUrl()方法：登出后，重定向到指定路径页面
 */
    .and()
        .logout()
            .logoutSuccessUrl("/")

```
## 阻止跨站请求伪造 (CSRF:Cross-site request forgery)：
* Spring Security含有**内置的CSRF保护**，而且是**默认启用**的，所以只需确保应用程序提交的任何表单都包含一个名为```_csrf```的字段，该字段包含CSRF令牌。
* Spring Security通过将CSRF令牌放在名为```_csrf```的请求属性中来简化这一点。因此，在一个**Thymeleaf模板**中，可以用下面的代码在一个隐藏字段中呈现CSRF令牌:  
  &emsp;```<input type="hidden" name="_csrf" th:value="${_csrf.token}"/>```
* 如果使用Spring MVC表单标签库（Spring MVC’s JSP tag library）或spring security安全方言，内置CSRF令牌的隐藏字段会**自动显示**。
  * 在静态模板Thymeleaf 中，只需要确保<form>元素作为Thymeleaf的前缀，例如：
    * ```<form method="POST" th:action="@{/login}" id="loginForm">```，```th:action``` 在Thymeleaf会为开发者渲染隐藏字段。  

作者极其不推荐，但是还是教了这个歪门邪道：  
&emsp;禁用内置CSRF支持：
*  ```.and().csrf().disable()```


> 如有遗漏或错误，欢迎补充纠正