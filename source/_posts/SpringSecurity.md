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

如果不使用Spring，则需要显式将`WebSecurityConfig`传递到超类中以确保获取配置。

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

```java
@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {
	@Override
  protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.inMemoryAuthentication()
      .withUser("user").password("password").roles("USER").and()
      .withUser("admin").password("password").roles("USER", "ADMIN");
  }
}
```

`withUser`方法返回的是`UserDetailsManagerConfigurer.UserDetailsBuilder`对象，该对象提供了进一步配置用户的方法：

| 方法                                          | 说明                         |
| --------------------------------------------- | ---------------------------- |
| accountExpired(boolean)                       | 定义账号是否已经过期。       |
| accountLocked(boolean)                        | 定义账号是否已经锁定。       |
| and()                                         | 用来连接配置。               |
| authorities(GrantedAuthority...)              | 授予某个用户一项或多项权限。 |
| authorities(List<? extends GrantedAuthority>) | 授予某个用户一项或多项权限。 |
| authorities(String...)                        | 授予某个用户一项或多项权限。 |
| credentialsExpired(boolean)                   | 定义凭证是否已经过期。       |
| disabled(boolean)                             | 定义账号是否已经禁用。       |
| password(String)                              | 定义用户的密码。             |
| roles(String...)                              | 授予某个用户一项或多项角色。 |

`roles`方法所给定的值都会自动添加一个`ROLE_`前缀。实际上如下的配置是等价的：

```java
roles("USER")
//等价于：
authorities("ROLE_USER")
```

#### 通过注入`AuthenticationManagerBuilder` 

#### 通过注册 `AuthenticationProvider` Bean

您可以通过将自定义`AuthenticationProvider`注册为bean来定义自定义身份验证。例如，假设`SpringAuthenticationProvider`实现了`AuthenticationProvider`，以下将自定义身份验证：

```java
@Bean
public SpringAuthenticationProvider springAuthenticationProvider() {
  return new SpringAuthenticationProvider();
}
```

> 仅在尚未配置`AuthenticationManagerBuilder`时才使用此方式。

#### 通过注册`UserDetailsService` Bean

您可以通过将自定义`UserDetailsService`注册为bean来定义自定义身份验证。例如，假设`SpitterUserDetailsService`实现`UserDetailsService`，以下将自定义身份验证：

```java
@Bean
public SpitterUserDetailsService spitterUserDetailsService() {
  return new SpitterUserDetailsService();
}
```

> 仅在尚未配置`AuthenticationManagerBuilder`，且尚未定义任何`AuthenticationProvider`Bean时才使用此方式。

SpitterUserDetailsService.java：

```java
public class SpitterUserDetailsService implements UserDetailsService {
  private final SpitterRepository spitterRepository;
  public SpitterUserDetailsService(SpitterRepository spitterRepository) {
    this.spitterRepository = spitterRepository;
  }
  @Override
  public UserDetails loadUserByUsername(String username)
    throws UsernameNotFoundException {
    Spitter spitter = spitterRepository.findByUsername(username);
    if (spitter != null) {
      List<GrantedAuthority> authorities =
        new ArrayList<GrantedAuthority>();
      authorities.add(new SimpleGrantedAuthority("ROLE_SPITTER"));
      return new User(
        spitter.getUsername(),
        spitter.getPassword(),
        authorities);
    }
    throw new UsernameNotFoundException(
      "User '" + username + "' not found.");
  }
}
```

除了将`UserDetailsService`注册为Bean外，也可以采用编码方式来设置`UserDetailsService`：

```java
@Autowired
SpitterRepository spitterRepository;
@Override
protected void configure(AuthenticationManagerBuilder auth)
  	throws Exception {
  auth.userDetailsService(new SpitterUserDetailsService(spitterRepository));
}
```

### 使用基于内存的用户存储

这里采用注册`UserDetailsService` Bean方式：

```java
@Bean
public UserDetailsService userDetailsService() throws Exception {
  // ensure the passwords are encoded properly
  UserBuilder users = User.withDefaultPasswordEncoder();
  InMemoryUserDetailsManager manager = new InMemoryUserDetailsManager();
  manager.createUser(users.username("user").password("password").roles("USER").build());
  manager.createUser(users.username("admin").password("password").roles("USER","ADMIN").build());
  return manager;
}
```

### 使用基于关系数据库的用户存储

这里采用注入`AuthenticationManagerBuilder`方式使用内置的用户存储：

```java
@EnableWebSecurity
public class SecurityConfig {
  @Autowired
  private DataSource dataSource;

  @Autowired
  public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
    auth
      .jdbcAuthentication() //返回JdbcUserDetailsManagerConfigurer<AuthenticationManagerBuilder>
      .dataSource(dataSource)
      .withDefaultSchema()
      .withUser(User.withDefaultPasswordEncoder().username("user").password("password").roles("USER"));
  }
}
```

内置的用户存储要求关系数据库具有指定的表和字段。具体说，Spring Security使用下列SQL语句来查询内置的用户存储：

```sql
-- 获取用户基本信息的SQL语句，用于用户认证：
select username, password, enabled from users where username = ?;
-- 获取用户所授予的权限，用于鉴权：
select username, authority from authorities where username = ?;
-- 查找用户作为组成员所热授予的权限：
select g.id, g.group_name, ga.authority 
	from groups g, group_members gm, group_authorities ga 
	where gm.username = ? and g.id = ga.group_id and g.id = gm.group_id;
```

如果你数据库表和字段与上述不一致，则可以按如下方式配置自己的查询：

```java
@Autowired
public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
  auth
    .jdbcAuthentication()
    .dataSource(dataSource)
    .usersByUsernameQuery("select username, password, true from Spitter where username = ?")
    .authoritiesByUsernameQuery("select username, 'ROLE_USER' from Spitter where username = ?")
    .groupAuthoritiesByUsername(…);
}
```

#### 对密码进行加密

默认密码是以明文的方式保存在数据库，可以使用`JdbcUserDetailsManagerConfigurer`的`passwordEncoder`方法来给密码加密：

```java
@Autowired
public void configureGlobal(AuthenticationManagerBuilder auth) throws Exception {
  auth
    .jdbcAuthentication()
    .dataSource(dataSource)
    .withDefaultSchema()
    .passwordEncoder(new StandardPasswordEncoder("53cr3t"))
    …;
}
```

`passwordEncoder`方法接受`PasswordEncoder`接口的任意实现。Spring Security中提供了三个实现：

- BCryptPasswordEncoder
- NoOpPasswordEncoder
- StandardPasswordEncoder

如果内置的实现无法满足需要时，也可以提供自己的实现。

用户在登录时输入的密码会按照相同的算法进行加密，然后再与数据库中已经加密过的密码进行对比。这个对比是在`PasswordEncoder`接口的`matches`方法中进行的。

### 使用基于LDAP的用户存储

#### 配置本地LDAP服务器

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
  auth.ldapAuthentication()
    .userSearchBase("ou=people")
    .userSearchFilter("(uid={0})")
    .groupSearchBase("ou=groups")
    .groupSearchFilter("member={0}");
}
```

`userSearchBase("ou=people")`表示在名为`people`的组织单元下搜索用户，而不是从根（默认）开始。

默认情况下，Spring Security的LDAP认证假设LDAP服务器监听本机的`33389`端口。

#### 配置远程LDAP服务器

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
  auth.ldapAuthentication()
    .userDnPatterns("uid={0},ou=people")
    .groupSearchBase("ou=groups")
    .contextSource()
    	.url("ldap://foo.com:389/dc=foo,dc=com");
}
```

#### 配置嵌入式的LDAP服务器

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
  auth.ldapAuthentication()
    .userSearchBase("ou=people")
    .userSearchFilter("(uid={0})")
    .groupSearchBase("ou=groups")
    .groupSearchFilter("member={0}")
    .contextSource()
    	.root("dc=foo,dc=com")
    	.ldif("classpath:users.ldif");
}
```

通过`root`方法指定嵌入式服务器的根前缀。

当LDAP服务器启动时，它会加载`ldif`方法指定的LDIF文件（LDAP Data Interchange Format）来加载用户数据。如果缺省`ldif`方法，则默认从整个根类路径下搜索LDIF文件。

users.ldif：

```ldif
dn: ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: groups

dn: ou=people,dc=springframework,dc=org
objectclass: top
objectclass: organizationalUnit
ou: people

dn: uid=admin,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Rod Johnson
sn: Johnson
uid: admin
userPassword: password

dn: uid=user,ou=people,dc=springframework,dc=org
objectclass: top
objectclass: person
objectclass: organizationalPerson
objectclass: inetOrgPerson
cn: Dianne Emu
sn: Emu
uid: user
userPassword: password

dn: cn=user,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: groupOfNames
cn: user
uniqueMember: uid=admin,ou=people,dc=springframework,dc=org
uniqueMember: uid=user,ou=people,dc=springframework,dc=org

dn: cn=admin,ou=groups,dc=springframework,dc=org
objectclass: top
objectclass: groupOfNames
cn: admin
uniqueMember: uid=admin,ou=people,dc=springframework,dc=org
```

#### LDAP的认证策略

基于LDAP进行认证的默认策略是进行绑定操作，直接通过LDAP服务器认证用户。

另一种可选的方式是进行密码比对操作。这涉及将输入的密码发送到LDAP目录上，并要求服务器将这个密码和用户的密码进行比对。因为比对是在LDAP服务器内完成的，实际的密码能保持私密。

如果希望通过密码比对进行认证，可以通过`passwordCompare`方法来实现：

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
  auth.ldapAuthentication()
    .userSearchBase("ou=people")
    .userSearchFilter("(uid={0})")
    .groupSearchBase("ou=groups")
    .groupSearchFilter("member={0}")
    .passwordCompare();
}
```

默认情况下，在登录表单中提供的密码将会与用户的LDAP条目中的`userPassword`属性进行对比。如果密码被保存在不同的属性中，可以通过`passwordAttribute`方法来指定密码属性的名称：

```java
@Override
protected void configure(AuthenticationManagerBuilder auth) throws Exception {
  auth.ldapAuthentication()
    .userSearchBase("ou=people")
    .userSearchFilter("(uid={0})")
    .groupSearchBase("ou=groups")
    .groupSearchFilter("member={0}")
    .passwordCompare()
    .passwordEncoder(new Md5PasswordEncoder())
    .passwordAttribute("passcode");
}
```

虽然实际的密码在服务器端是私密的，但是进行尝试的密码还是需要通过线路传输到LDAP服务器上，这可能会被黑客所拦截。为了避免这一点，我们可以通过调用`passwordEncoder`方法指定加密策略。在本示例中，密码会进行MD5加密。这需要LDAP服务器上密码也使用MD5进行加密。

### 自定义密码加密

您还可以通过将自定义的`PasswordEncoder`实现注册为bean来自定义密码的编码方式。

```java
@Bean
public BCryptPasswordEncoder passwordEncoder() {
  return new BCryptPasswordEncoder();
}
```



# 反应式应用