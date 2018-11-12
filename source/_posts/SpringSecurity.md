---
title: SpringSecurity
date: 2018-11-12 09:46:19
tags: [5.1.1]
---

# 简介

Spring Security是为基于Spring的应用程序提供声明式安全保护的安全框架。

Spring Security最初被称为Acegi Security。

# 架构

| 模块             | 说明                                                         |
| ---------------- | ------------------------------------------------------------ |
| Core（必需模块） | 提供Spring Security基本库。                                  |
| Config           | 如果您使用Spring Security XML命名空间进行配置或Spring Security的Java配置支持，则需要它。 |
| Web              | 提供了基于Filter的Web安全性支持。如果您需要Spring Security Web身份验证服务和基于URL的访问控制，则需要它。 |
| Remoting         | 提供了对Spring Remoting的支持，用于开发远程客户端。          |
| LDAP             | 支持基于LDAP进行认证。                                       |
| OAuth 2.0 Core   | 包含为OAuth 2.0授权框架和OpenID Connect Core 1.0提供支持的核心类和接口。 |
| OAuth 2.0 Client | 是Spring Security对OAuth 2.0授权框架和OpenID Connect Core 1.0的客户端支持。 |
| OAuth 2.0 JOSE   | 包含Spring Security对JOSE（Javascript Object Signing and Encryption）框架的支持。 |
| ACL              | 支持通过访问控制列表（Access Control List）为领域对象提供安全性。 |
| CAS              | Spring Security的CAS客户端集成。                             |
| OpenID           | 支持使用OpenID进行集中式认证。                               |
| Test             | 支持对Spring Security进行测试。                              |

# 安装

Spring Security应用程序通常都会需要Core和Config（依赖于Core）模块，其他Spring Security模块则可以根据应用场景来选择使用。

pom.xml

```xml
<dependencyManagement>
  <dependencies>
    <!-- ... other dependency elements ... -->
    <dependency>
      <groupId>org.springframework.security</groupId>
      <artifactId>spring-security-bom</artifactId>
      <version>5.1.1.RELEASE</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
<dependencies>
  <!-- ... other dependency elements ... -->
  <dependency>
    <groupId>org.springframework.security</groupId>
    <artifactId>spring-security-config</artifactId>
  </dependency>
</dependencies>
```

# Servlet应用

## 配置`DelegatingFilterProxy`过滤器

`DelegatingFilterProxy`是一个特殊的Servlet过滤器，它本身所做的工作并不多。只是将工作委托给一系列具体提供各种安全性功能的`javax.servlet.Filter`实现类，这些实现类作为Bean注册在Spring应用的上下文中。

![DelegatingFilterProxy](SpringSecurity/DelegatingFilterProxy.png)

### 基于Java配置（不使用Spring）

```java
public class SecurityWebApplicationInitializer
    extends AbstractSecurityWebApplicationInitializer {
  public SecurityWebApplicationInitializer() {
    super(WebSecurityConfig.class);
  }
}
```

如果不使用Spring或Spring MVC，则需要显式将`WebSecurityConfig`传递到超类中以确保获取配置。

`SecurityWebApplicationInitializer`做了以下事件：

- 自动为应用程序中的每个URL注册`springSecurityFilterChain`过滤器；
- 添加一个加载`WebSecurityConfig`的`ContextLoaderListener`。

### 基于Java配置（已使用Spring）

```java
public class SecurityWebApplicationInitializer
  	extends AbstractSecurityWebApplicationInitializer {

}
```

这时，`SecurityWebApplicationInitializer`只为应用程序中的每个URL注册`springSecurityFilterChain`过滤器。

接下来，我们要确保在现有的`ApplicationInitializer`中加载`WebSecurityConfig`。例如，如果我们使用Spring MVC，它将被添加到`getRootConfigClasses`方法中：

```java
public class MvcWebApplicationInitializer 
  	extends AbstractAnnotationConfigDispatcherServletInitializer {
  @Override
  protected Class<?>[] getRootConfigClasses() {
    return new Class[] { WebSecurityConfig.class };
  }

  // ... other overrides ...
}
```

### 基于XML的配置

## 启用Web安全

首先，在配置类上标注`@EnableWebSecurity`，以启用Web安全功能。

> `@EnableWebMvcSecurity`已经废弃。

```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig {
	…
}
```

## 配置用户储存

用户储存也就是用户名、密码、具有的角色等用户信息存储的地方，在进行认证决策时会对其进行检索。

Spring Security内置了多种常见的用户存储场景，如内存、关系型数据库以及LDAP。

此外，也允许我们编写并插入自定义的用户存储实现。

### 配置方式

#### 通过重写`WebSecurityConfigurerAdapter`类

如果要使用内置的用户存储功能，可以重写`WebSecurityConfigurerAdapter`类的`configure(AuthenticationManagerBuilder)`方法。

#### 通过注入`AuthenticationManagerBuilder` 

#### 通过注册 `AuthenticationProvider` Bean

#### 通过注册`UserDetailsService` Bean

### 使用基于内存的用户存储



# 反应式应用