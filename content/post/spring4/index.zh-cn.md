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

## 方法级安全控制
### 1. ```@PreAuthorize```注解：  
1. #### 作用：  
   &emsp;```@PreAuthorize```注解根据SpEL表达式进行判断，如果表达式值为```false```，则不会调用方法：    
   &emsp;```@PreAuthorize```如果阻止了调用，SpringSecurity抛出**AccessDeniedException**未检查的异常，不需要进行捕捉。除非想对其进行自定义处理。如果不进行捕获:   
      * &emsp;异常会被SpringSecurity的过滤器捕捉，要么显示**HTTP403**错误；
      * &emsp;要么如果用户没登录的话，被重定向到登录界面；
2. #### 用法：
```java
    @PreAuthorize("hasRole('ADMIN')")
    public void deleteAllOrders(){
        orderRepository.deleteAll();
    }
```

3. #### **注意：**  
&emsp;~~在以前的用法中，使用```@EnableGlobalMethodSecurity```注解，并扩展```WebSecurityConfigurerAdapter```类并且覆盖配置```HttpSecurity```和```WebSecurity```的方法；~~  
&emsp;**现在，```WebSecurityConfigurerAdapter```在SpringSecurity5.7.1以上或SpringBoot2.7.0以上会出现已被弃用的警告!****
&emsp;在新的用法中，我们得分别声明类型为```SecurityFilterChain```和```WebSecurityCustomizer```的bean，例如：

```java
    @Bean
    public SecurityFilterChain filterChain(HttpSecurityhttp) throws Exception{
    
    }
    @Bean
    public WebSecurityCustomizer webSecurityCustomizer(){
    
    }
```

### 2. ```@PostAuthorize```注解：  
1. #### 作用：                 
&emsp;```@PostAuthorize```注解的工作方式几乎与```@PreAuthorize```注解相同，只是它的表达式在**调用目标方法并返回之前**不会被计算。表达式可以根据方法的返回值来决定是否允许调用方法。  
&emsp;抛出异常与```@PreAuthorize```注解相同，为**AccessDeniedException**未检查的异常。  

2. #### 用法：
    ```java
    @PostAuthorize("hasRole('ADMIN') || " +
                "returnObject.user.username == authentication.name")
    public TacoOrder getOrder(long id) {
        ...
    }
    ```

## Spring Security判断经过身份验证用户的方法：  
### 1. 
* 在控制器方法中添加**java.security.Principal**对象：  
    ```java
    @PostMapping
    public String processOrder(@Valid TacoOrder order, Errors errors,
        SessionStatus sessionStatus, Principal principal) {
        ...
        User user = userRepository.findByUsername(principal.getName());	    
        order.setUser(user);
            ...
    }
    ```
### 2.
* 在控制器方法中添加**org.springframework.security.core.Authentication**对象  
    ```java
    /**
    * 调用getPrincipal()方法得到主要对象（例子为User）
    * getPrincipal()方法返回java.util.Object，所以将其转换为User
    */
    @PostMapping
    public String processOrder(@Valid TacoOrder order, Errors errors,
        SessionStatus sessionStatus, Authentication authentication) {
        ...
        User user = (User) authentication.getPrincipal();
        order.setUser(user);
        ...
    }
    ```
### 3. 
* 注入一个带```@AuthenticationPrincipal```注解的方法参数。（该注解来自Spring Security的**org.springframework.security.core.annotation**包）  
    ```java
    @PostMapping
    public String processOrder(@Valid TacoOrder order, Errors errors, 
        SessionStatus sessionStatus,  @AuthenticationPrincipal User user) {
                  
        if (errors.hasErrors()) {
            return "orderForm";
        }
                  
        order.setUser(user);
                  
        orderRepo.save(order);
        sessionStatus.setComplete();
                  
        return "redirect:/";
    }
    ```
* 优点：
  * 不需要对对象进行映射
  * 它将特定于安全性的代码限制为注释本身
### 4. 第四种方法
* 还有另一种方法可以识别经过身份验证的用户是谁，虽然有点混乱，因为它使用了大量特定于安全的代码。可以从安全上下文中获取一个身份验证对象，然后像这样请求它的主体:
    ```java
    Authentication authentication =
        SecurityContextHolder.getContext().getAuthentication();
    User user = (User) authentication.getPrincipal();
    ```
* 缺点：
  * 有大量安全相关的代码
* 优点：
  * 可以在应用程序中的任何地方使用，而不仅仅是在控制器的处理程序方法中。（这使得它适合在较低级别的代码中使用）


> 如有遗漏或错误，欢迎补充纠正