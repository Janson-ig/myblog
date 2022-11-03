---
title: Spring学习之路（三）
description: Spring Security(一)：密码编译器 & Securing web requests
date: 2022-11-02
slug: spring3
image: 1.jpg
categories:
    - Spring
    - 学习笔记
tags:
    - 框架
---
* Spring实战第五版中使用的WebSecurityConfigurerAdapter已被弃用 （还没学过就被弃用了...）  
* 第五章开始，使用最新的Spring实战第六版进行学习
## Spring Security的密码编译器

|          密码编译器          |                加密方法                 |
|:-----------------------:|:-----------------------------------:|
|  BCryptPasswordEncoder  |          应用 bcrypt 加强哈希加密           |
|   NoOpPasswordEncoder   |     不应用任何编码<br/>（对测试有用，不适合生产使用）     |
|  Pbkdf2PasswordEncoder  |            应用 PBKDF2 加密             |
|  SCryptPasswordEncoder  |           应用 scrypt 哈希加密            |
| StandardPasswordEncoder | 应用 SHA-256 哈希加密<br/>（被认为不够安全，已经被弃用） |  

&emsp;数据库中密码被加密；  
&emsp;用户在login输入的密码使用相同的算法进行编码，在数据库使用PasswordEncoder的matches()方法进行比较。

## UserDetailsService实现：
Spring Security提供了几个开箱即用的UserDetailsService实现：
1. 基于内存的用户存储
2. 基于JDBC的用户存储 
3. 由LDAP支持的用户存储
```
/*
*loadUserByUsername()方法接受用户名并使用它查找UserDetails对象。
*如果找不到给定用户名的用户，则抛出UsernameNotFoundException异常。
*/
public interface UserDetailsService {
 
    UserDetails loadUserByUsername(String username) throws UsernameNotFoundException;
 
}
```


## Securing web requests
&emsp;authorizeRequests()返回(ExpressionUrlAuthorizationConfigurer.ExpressionInterceptUrlRegistry)对象，允许指定URL路径和模式，以及路径对应的安全要求：  
&emsp;书中项目的代码实现如下：
```
/*
 *保证对于/design 和 /orders路径的请求只对认证的用户有效；
 *其他路径对于所有的用户有效；
 */
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    return http
	    .authorizeRequests()
	    .antMatchers("/design", "/orders").hasRole("USER")
	    .antMatchers("/", "/**").permitAll()
	 
	    .and()
	    .build();
}
```
&emsp;Spring web requests的配置方法，以及如何保护路径如下：

|             方法             |                          作用                          |
|:--------------------------:|:----------------------------------------------------:|
|       access(String)       | 如果给定的Spring Expression Language(SpEL)表达式求值为true，允许访问 |
|        anonymous()         |                       允许匿名用户访问                       |
|      authenticated()       |                     允许通过验证的用户访问                      |
|         denyAll()          |                      无条件拒绝所有访问                       |  
|    fullyAuthenticated()    |                 允许通过完全身份验证的用户访问（不推荐）                 |
| hasAnyAuthority(String...) |    如果用户具有任何一个给定的权限，则允许访问（可代替hasAnyRole(String)方法）    |
|    hasAnyRole(String…)     |       如果用户具有任何给定的角色，则允许访问<br/> （自动加 ROLE_ 的前缀）       |
|    hasAuthority(String)    |                  如果用户具有指定的权限，则允许访问                   |
|      hasRole(String)       |                  如果用户具有指定的角色，则允许访问                   |
|    hasIpAddress(String)    |                 如果用户具有指定的IP地址，则允许访问                  |
|           not()            |                    否定任何其他认证方法的效果                     |
|        permitAll()         |                       无条件允许访问                        |
|        rememberMe()        |             允许通过 remember-me 进行身份验证的用户访问             |	
* 这其中值得注意的事，hasRole()方法可以被hasAuthority()代替：hasRole()方法自动补足 **ROLE_** 的前缀。  
* 设计上来看，一个是权限，一个是角色，角色是权限的集合体。

## Spring Security对于SpEL表达式的扩展

|               Security expression               |                                       SpEL的计算结果                                       |
|:-----------------------------------------------:|:-------------------------------------------------------------------------------------:|
|                 authentication                  |                                        用户的认证对象                                        |
|                     denyAll                     |                                        恒为false                                        |
|      hasAnyAuthority(String...authorities)      |                                如果用户被授予所有权限中的某一个，则为true                                |
|           hasAnyRole(String...roles)            |                               如果用户有给定的roles中的某一个，则为true                               |
|        hasAnyAuthority(String authority)        |                                  如果用户被授予指定的权限，则为true                                  |  
| hasPermission(Object target, Object permission) |                              如果用户能以给定的权限访问指定的目标对象，则为true                              |
|              hasRole(String role)               |                                  如果用户有给定的role，则为true                                  |
|         hasIpAddress(String ipAddress)          |                                   如果请求来自指定ip，则为true                                   |
|                  isAnnoymous()                  |                                    如果用户是匿名的，则为true                                    |
|                isAuthenticated()                |                                    如果用户已认证，则为true                                     |
|             isFullyAuthenticated()              |                      如果用户经过完全身份验证(没有使用remember-me进行身份验证)，则为true                       |
|                 isRememberMe()                  |                             如果用户使用remeber-me进行身份验证，则为true                             |
|                   permitAll()                   |                                        恒为true                                         |
|                    principal                    |                                        用户的主要对象                                        |
* remember-me（记住我）认证，就是常见的记住我：下次登录时，使用cookie导致自动登录发生。
```
/*
*所以上面一节中的方法，可以用access()方法+security表达式的方式来实现；
*/
@Bean
public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
    return http
        .authorizeRequests()
        .antMatchers("/design", "/orders").access("hasRole('USER')")
        .antMatchers("/", "/**").access("permitAll()")
	 
	    .and()
	    .build();
}
```

> 如有遗漏或错误，欢迎补充纠正