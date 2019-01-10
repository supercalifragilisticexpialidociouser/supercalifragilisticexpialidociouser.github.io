---
title: SpringSecurity
date: 2018-11-12 09:46:19
tags: [5.1.1]
---

# 简介

Spring Security是为基于Spring的应用程序提供声明式安全保护的安全框架。

Spring Security最初被称为Acegi Security。

<!--more-->

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

## 保护请求

默认情况下，Spring Security会拦截所有请求，并要求所有请求都要进行认证。如果要对每个请求进行细粒度安全控制，则需要重写`WebSecurityConfigurerAdapter`类的`configure(HttpSecurity)`方法。

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
  http
    .authorizeRequests()                                                         
      .antMatchers("/resources/**", "/signup", "/about").permitAll() //任何人均可访问
      .antMatchers("/admin/**").hasRole("ADMIN") //具有ROLE_ADMIN角色的用户允许访问
      .antMatchers("/db/**").access("hasRole('ADMIN') and hasRole('DBA')") 
      .anyRequest().authenticated() //已经登录的用户允许访问
    	.and()
    // ...
    	.and()
    .formLogin();
}
```

### 路径选择

用于设定拦截路径的方法主要有：`antMatchers`、`regexMatchers`和`anyRequest`。

`antMatchers`方法中设定的路径支持Ant风格的通配符。

`regexMatchers`方法则能够接受正则表达式来定义请求路径：

```java
.regexMatchers("/admin/.*")
```

`anyRequest`方法表示任何请求。

### 授权请求

可以通过如下方法来定义如何保护指定路径的请求：

| 方法                       | 说明                                                         |
| -------------------------- | ------------------------------------------------------------ |
| access(String)             | 如果给定的SpEL表达式计算结果为`true`，就允许访问。           |
| anonymous()                | 允许匿名用户访问。                                           |
| authenticated()            | 允许认证过的用户访问。                                       |
| denyAll()                  | 无条件拒绝所有访问。                                         |
| fullyAuthenticated()       | 如果用户是完整认证的话（不是通过Remember-me功能认证的），就允许访问。 |
| hasAnyAuthority(String...) | 如果用户具备给定权限中的某一个，就允许访问。                 |
| hasAnyRole(String...)      | 如果用户具备给定角色中的某一个，就允许访问。                 |
| hasAuthority(String)       | 如果用户具备给定权限，就允许访问。                           |
| hasIpAddress(String)       | 如果请求来自给定IP地址，就允许访问。                         |
| hasRole(String)            | 如果用户具备给定角色，就允许访问。                           |
| not()                      | 对其他访问方法的结果求反。                                   |
| permitAll()                | 无条件允许访问。                                             |
| rememberMe()               | 如果用户通过Remember-me功能认证，就允许访问。                |

我们可以将任意数量的`antMatchers`、`regexMatchers`和`anyRequest`连接起来，以满足Web应用安全规则的需要。排在越前面的安全规则，优先级越高。因此，要将最为具体的请求路径放在最前面，而最不具体的路径放在最后面。

对每一路径只能应用一次上面的方法。也就是说，如果已使用`hasRole`方法限制某个特定的角色，就不能在相同的路径上同时通过`hasIpAddress`限制特定的IP地址。如果希望在同一路径上同时应用多个条件，则需要借助`access`方法，它支持使用SpEL表达式来声明访问限制。

Spring Security通过一些安全相关的表达式扩展了SpEL：

| 安全表达式             | 计算结果                                                  |
| ---------------------- | --------------------------------------------------------- |
| authentication         | 用户的认证对象                                            |
| denyAll                | 结果始终为`false`                                         |
| hasAnyRole(角色列表)   | 如果用户被授予了列表中任意一个指定角色，则结果为`true`    |
| hasRole(role)          | 如果用户被授予了指定角色，则结果为`true`                  |
| hasIpAddress(IP地址)   | 如果请求来自指定IP，则结果为`true`                        |
| isAnonymous()          | 如果当前用户为匿名用户，则结果为`true`                    |
| isAuthenticated()      | 如果当前用户已进行了认证，则结果为`true`                  |
| isFullyAuthenticated() | 如果当前用户进行了完整认证，则结果为`true`                |
| isRememberMe()         | 如果当前用户是通过Remember-me功能自动认证，则结果为`true` |
| permitAll              | 结果始终为`true`                                          |
| principal              | 用户的`principal`对象                                     |

### 请求通道

`HttpSecurity`对象的`requiresChannel`方法可以为各个路径设置请求的通道（HTTP或HTTPS）。

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
  http
    .authorizeRequests()                                                         
      .antMatchers("/resources/**", "/signup", "/about").permitAll()
      .antMatchers("/admin/**").hasRole("ADMIN")
      .antMatchers("/db/**").access("hasRole('ADMIN') and hasRole('DBA')") 
      .anyRequest().authenticated()
    	.and()
    .requiresChannel()
      .antMatchers("/").requiresInecure()
      .anyRequest().requiresSecure()
    // ...
    	.and()
    .formLogin();
}
```

`requiresInecure`方法表示对“/”的请求，将自动重定向到不安全的HTTP通道上。

`requiresSecure`方法表示对其他任何请求，将自动重定向到安全的HTTPS通道上。

### 防止跨站请求伪造

CSRF（Cross Site Request Forgery, 跨站域请求伪造）是一种网络的攻击方式，它可以在受害者毫不知情的情况下以受害者名义伪造请求发送给受攻击站点，从而在并未授权的情况下执行在权限保护之下的操作。比如说，受害者 Bob 在银行有一笔存款，通过对银行的网站发送请求 http://bank.example/withdraw?account=bob&amount=1000000&for=bob2 可以使 Bob 把 1000000 的存款转到 bob2 的账号下。通常情况下，该请求发送到网站后，服务器会先验证该请求是否来自一个合法的 session，并且该 session 的用户 Bob 已经成功登陆。黑客 Mallory 自己在该银行也有账户，他知道上文中的 URL 可以把钱进行转帐操作。Mallory 可以自己发送一个请求给银行：http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory。但是这个请求来自 Mallory 而非 Bob，他不能通过安全认证，因此该请求不会起作用。这时，Mallory 想到使用 CSRF 的攻击方式，他先自己做一个网站，在网站中放入如下代码： src=”http://bank.example/withdraw?account=bob&amount=1000000&for=Mallory ”，并且通过广告等诱使 Bob 来访问他的网站。当 Bob 访问该网站时，上述 url 就会从 Bob 的浏览器发向银行，而这个请求会附带 Bob 浏览器中的 cookie 一起发向银行服务器。大多数情况下，该请求会失败，因为他要求 Bob 的认证信息。但是，如果 Bob 当时恰巧刚访问他的银行后不久，他的浏览器与银行网站之间的 session 尚未过期，浏览器的 cookie 之中含有 Bob 的认证信息。这时，悲剧发生了，这个 url 请求就会得到响应，钱将从 Bob 的账号转移到 Mallory 的账号，而 Bob 当时毫不知情。等以后 Bob 发现账户钱少了，即使他去银行查询日志，他也只能发现确实有一个来自于他本人的合法请求转移了资金，没有任何被攻击的痕迹。而 Mallory 则可以拿到钱后逍遥法外。

从Spring Security 3.2开始，默认就会启用CSRF防护。Spring Security通过一个同步token的方式来实现CSRF防护，它将会拦截状态变化的请求（例如，非GET、HEAD、OPTIONS和TRACE的请求）并检查CSRF token。如果请求中不包含CSRF token的话，或者token不能与服务器端的token相匹配，请求将会失败，并抛出`CsrfException`异常。这意味着在你的应用中，所有的表单必须在一个`_csrf`域中提交token，而且这个token必须要与服务器端计算并存储的token一致。

例如，使用JSP作为视图模板时，需要在表单中添加如下的隐藏域：

```jsp
<input type="hidden" name="${_csrf.parameterName}" value="${_csrf.token}" />
```

好消息是，如果使用的是Thymeleaf视图技术，则只要在`<form>`标签的`action`属性添加了Thymeleaf命名空间前缀，那么就会自动生成一个`_csrf`隐藏域：

```html
<form method="POST" th:action="@{/spittles}"
```

更好的功能是，如果使用Spring的表单绑定标签的话，`<sf:form>`标签会自动为我们添加隐藏的CSRF token标签。

如果要禁用CSRF防护，只需要：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
  http.csrf().disable();
}
```

## 认证用户

### 基于表单的认证

默认情况下（即没有重写`configure(HttpSecurity)`方法），当访问应用的`/login`链接或者导航到需要认证的页面时，Spring Security会为我们提供一个简单的登录页面。默认认的`configure(HttpSecurity)`方法如下：

```java
protected void configure(HttpSecurity http) throws Exception {
  http
    .authorizeRequests()
    	.anyRequest().authenticated()
    	.and()
    .formLogin() //基于表单的登录
    	.and()
    .httpBasic(); //HTTP Basic方式的认证
}
```

但是，一旦重写了`configure(HttpSecurity)`方法，就需要自己显式调用`formLogin`方法来获得这个登录页面。

#### 自定义登录页

自定义登录页可以参考默认提供的登录页来写，主要注意如下几点：

1. 表单要提交到`/login`；
2. 用户名输入域名必须是`username`，密码输入域名必须是`password`；
3. 如果没有禁用CSRF，还要保证包含值为CSRF token的`_csrf`输入域。

例如：（Thymeleaf模板）

```html
<html xmlns="http://www.w3.org/1999/xhtml"
      xmlns:th="http://www.thymeleaf.org">
  <head>
    <title>Spitter</title>
    <link rel="stylesheet"
          type="text/css"
          th:href="@{/resources/style.css}"></link>
  </head>
  <body onload='document.f.username.focus();'>
    <div id="header" th:include="page :: header"></div>
    <div id="content">
      <form name='f' th:action='@{/login}' method='POST'>
        <table>
          <tr><td>User:</td><td>
            <input type='text' name='username' value='' /></td></tr>
          <tr><td>Password:</td>
            <td><input type='password' name='password'/></td></tr>
          <tr><td colspan='2'>
            <input name="submit" type="submit" value="Login"/></td></tr>
        </table>
      </form>
    </div>
    <div id="footer" th:include="page :: copy"></div>
  </body>
</html>
```

### 启用HTTP Basic认证

当应用程序的使用者是另外一个应用程序时（例如REST客户端访问RESTful API），使用表单来提示登录的方式就不太适合。

HTTP Basic认证会直接通过HTTP请求本身，对要访问应用程序的用户进行认证。

在访问需要HTTP Basic认证的资源时，如果没有在请求中包含用户名和密码等认证信息时，则会响应一个HTTP 401（如果是在浏览器中访问，则是向用户弹出一个简单的模态对话框，要求用户输入用户名和密码）。

默认情况，Spring Security已经启用了HTTP Basic认证。但如果重写了`configure(HttpSecurity)`方法，就需要自己显式调用`httpBasic`方法来启用HTTP Basic认证。另外，还可以通过调用`realmName`方法指定安全域：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
  http
    .formLogin()
    	.loginPage("/login")
    	.and()
    .httpBasic()
    	.realmName("Spittr")
    	.and()
    ...
}
```

### 启用Remember-me功能

Remember-me功能使得用户登录过一次，就会被记住，当再次回到应用时就不需要登录了。

要启用Remember-me功能只需要象如下代码一样调用`rememberMe`方法：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
  http
    .formLogin()
    	.loginPage("/login")
    	.and()
    .rememberMe()
    	.tokenValiditySeconds(2419200)
    	.key("spittrKey")
    ...
}
```

默认情况下，这个功能是通过在cookie中存储一个token来完成的。这个token最多两周内有效，本例中我们设定这个token最多四周内有效（2419200秒）。

存储在cookie中的token包含用户名、密码、过期时间和一个私钥——在写入cookie前都进行了MD5哈希。默认情况下，私钥名为`SpringSecured`，本例通过`key`方法将其设置为`spittrKey`。

启用Remember-me功能后，还需要在登录请求中包含一个名为`remember-me`的参数。在登录表单中，增加一个简单复选框就可以完成这件事情：

```html
<input id="remember_me" name="remember-me" type="checkbox"/>
<label for="remember_me" class="inline">Remember me</label>
```

### 退出

能出功能是通过Servlet容器中的Filter实现的（即`LogoutFilter`），它会拦截对`/logout`的请求。因此，为应用添加退出功能只需要添加如下的链接即可：

```html
<a th:href="@{/logout}">Logout</a>
```

当用户点击上述链接时，会发起对`/logout`的请求，这个请求会被Spring Security的`LogoutFilter`所处理，用户会退出应用，所有的Remember-me token都会被清除掉。

在退出完成之后，用户浏览器将会重定向到`/login?logout`，从而允许用户进行再次登录。如果希望被重定向到其他页面，如应用的首页，则可以在`configure`方法中做如下配置：

```java
@Override
protected void configure(HttpSecurity http) throws Exception {
  http
    .formLogin()
      .loginPage("/login")
      .and()
    .logout()
    	.logoutSuccessUrl("/")
    ...
}
```

`logoutSuccessUrl("/")`表明在退出成功之后，浏览器需要重定向到`/`。

`LogoutFilter`默认拦截`/logout`路径，可以通过`logoutUrl`方法来自定义拦截路径：

```java
.logout()
  .logoutSuccessUrl("/")
  .logoutUrl("/signout")
```

## 保护视图

参见《Spring实战 · 9.5保护视图》（第四版）。

# 反应式应用

# 保护方法

在前面，我们看到了Spring Security如何保护应用的Web层。接下来，我们要看一下如何保护场景后面的方法。

Spring Security提供了三种不同的安全标注：

- Spring Security自带的`@Secured`标注；
- JSR-250的`@RolesAllowed`标注；
- 表达式驱动的标注，包括`@PreAuthorize`、`@PostAuthorize`、`@PreFilter`和`@PostFilter`。

## 基于`@Secured`的方法安全性

### 启用基于`@Secured`的方法安全性

```java
@Configuration
@EnableGlobalMethodSecurity(securedEnabled=true)
public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {
  @Override
  protected void configure(AuthenticationManagerBuilder auth) throws Exception {
    auth.inMemoryAuthentication()
        .withUser("user").password("password").roles("USER");
  }
  …
}
```

注意：这里是设置`@EnableGlobalMethodSecurity`的`securedEnabled`为`true`。

### 使用`@Secured`

```java
@Secured({"ROLE_SPITTER", "ROLE_ADMIN"})
public void addSpittle(Spittle spittle) {
	// ...
}
```

`@Secured`标注会使用一个`String`数组作为参数，每个`String`值是一个权限，调用这个方法至少需要具备其中的一个权限。

如果方法被没有认证的用户或没有所需要权限的用户调用，保护这个方法的切面将抛出一个Spring Security异常（可能是`AuthenticationException`或`AccessDeniedException`的子类）。它们是非检查型异常，但这些异常最终必须要被捕获和处理。如果被保护的方法是在Web请求中调用的，这个异常会被Spring Security的过滤器自动处理。

## 基于`@RolesAllowed`的方法安全性

`@RolesAllowed`标注和`@Secured`标注在各个方面基本上都是一致的，唯一显著的区别在于`@RolesAllowed`标注是JSR-250定义的Java标准注解。

### 启用基于`@RolesAllowed`的方法安全性

```java
@Configuration
@EnableGlobalMethodSecurity(jsr250Enabled=true)
public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {
  …
}
```

这里将`jsr250Enabled`设置为`true`，不仅可以使用`@RolesAllowed`，也可以同时使用`@Secured`标注。

### 使用`@RolesAllowed`

```java
@RolesAllowed("ROLE_SPITTER")
public void addSpittle(Spittle spittle) {
	// ...
}
```

## 基于表达式驱动的方法安全性

有时候，安全性约束不仅仅涉及用户是否有权限。Spring Security 3.0引入几个新标注，它们可以使用SpEL来进行安全约束。

| 标注           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| @PreAuthorize  | 在方法调用之前，基于表达式的计算结果来限制对方法的访问。     |
| @PostAuthorize | 允许方法调用，但是如果表达式计算结果为false，将抛出一个安全性异常。 |
| @PostFilter    | 允许方法调用，但必须按照表达式来过滤方法的结果。             |
| @PreFilter     | 允许方法调用，但必须在进入方法之前过滤输入值。               |

### 启用基于表达式的方法安全性

```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled=true)
public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {
  …
}
```

### 表述方法访问规则

#### 在方法调用前应用安全规则

`@PreAuthorize`的SpEL会在方法调用之前计算，如果表达式的结果不为`true`，将会阻止方法执行。

例如：Spittr应用程序的一般用户只能写140个字以内的Spittle，而付费用户不限制字数。

```java
@PreAuthorize("(hasRole('ROLE_SPITTER') and #spittle.text.length() <= 140) or hasRole('ROLE_PREMIUM')")
public void addSpittle(Spittle spittle) {
	// ...
}
```

#### 在方法调用之后应用安全规则

`@PostAuthorize`的SpEL直到方法被调用并得到返回值之后才会计算，然后决定是否抛出安全性的异常。

例如：我们想对`getSpittleById()`方法进行保护，确保返回的Spittle对象属于当前的认证用户。我们只有得到Spittle对象之后，才能判断它是否属于当前用户。因此，`getSpittleById()`方法必须要先执行，在得到Spittle之后，如果它不属于当前用户，将会抛出安全性异常。

```java
@PostAuthorize("returnObject.spitter.username == principal.username")
public Spittle getSpittleById(long id) {
	// ...
}
```

为了便利地访问受保护方法的返回对象，Spring Security在SpEL中提供了名为`returnObject`的变量。

`principal`变量是当前认证用户的主要信息。

### 过滤方法的输入和输出

#### 事后对方法的返回值进行过滤

`@PostFilter`也使用一个SpEL作为参数。但是，这个表达式不是用来限制方法的访问的，`@PostFilter`会使用这个表达式计算该方法所返回集合的每个成员，将计算结果为`false`的成员移除掉。

假设有一个方法`getOffensiveSpittles()`，它返回标记为具有攻击性的Spittle列表。我们要求管理员调用这个方法时不对返回结果进行限制，而普通用户调用这个方法时只能看到它自己的Spittle。

```java
@PreAuthorize("hasAnyRole({'ROLE_SPITTER', 'ROLE_ADMIN'})")
@PostFilter( "hasRole('ROLE_ADMIN') || filterObject.spitter.username == principal.name")
public List<Spittle> getOffensiveSpittles() {
	...
}
```

`filterObject`变量是这个方法所返回`List`中的某个元素（这里是`Spittle`）。

#### 事先对方法的参数进行过滤

假设我们有一个批量删除`Spittle`的方法：

```java
public void deleteSpittles(List<Spittle> spittles) { ... }
```

现在我们希望在它上面应用一些安全规则，比如`Spittle`只能由其所有者或管理员删除。

```java
@PreAuthorize("hasAnyRole({'ROLE_SPITTER', 'ROLE_ADMIN'})")
@PreFilter( "hasRole('ROLE_ADMIN') || targetObject.spitter.username == principal.name")
public void deleteSpittles(List<Spittle> spittles) { ... }
```

`@PreFilter`能够保证传递给`deleteSpittles`方法的`Spittle`列表中，只包含当前用户有权限删除的`Spittle`。

`targetObject`变量是要方法参数中进行计算的当前列表元素（这里是Spittle）。

#### 定义许可评估器

当安全规则变得复杂时，SpEL可能会不断膨胀，变得很笨重。而且，SpEL还难以测试。

Spring Security允许我们将复杂的安全规则定义在一个许可评估器中。许可评估器是一个实现`PermissionEvaluator`接口的类，我们要重写它的两个`hasPermission`方法。

```java
public class SpittlePermissionEvaluator implements PermissionEvaluator {
  private static final GrantedAuthority ADMIN_AUTHORITY =
    new GrantedAuthorityImpl("ROLE_ADMIN");
  public boolean hasPermission(Authentication authentication,
                               Object target, Object permission) {
    if (target instanceof Spittle) {
      Spittle spittle = (Spittle) target;
      String username = spittle.getSpitter().getUsername();
      if ("delete".equals(permission)) {
        return isAdmin(authentication) ||
          username.equals(authentication.getName());
      }
    }
    throw new UnsupportedOperationException(
      "hasPermission not supported for object <" + target
      + "> and permission <" + permission + ">");
  }
  public boolean hasPermission(Authentication authentication,
                               Serializable targetId, String targetType, Object permission) {
    throw new UnsupportedOperationException();
  }
  private boolean isAdmin(Authentication authentication) {
    return authentication.getAuthorities().contains(ADMIN_AUTHORITY);
  }
}
```

然后，将该许可评估器注册到Spring Security中，以便在使用`@PreFilter`的SpEL时，支持`hasPermission()`操作。

```java
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled=true)
public class MethodSecurityConfig extends GlobalMethodSecurityConfiguration {
  @Override
  protected MethodSecurityExpressionHandler createExpressionHandler() {
    DefaultMethodSecurityExpressionHandler expressionHandler =
      new DefaultMethodSecurityExpressionHandler();
    expressionHandler.setPermissionEvaluator(new SpittlePermissionEvaluator());
    return expressionHandler;
  }
}
```

> 默认情况下，Spring Security自动配置的`DefaultMethodSecurityExpressionHandler`使用的是`DenyAllPermissionEvaluator`许可评估器，它的`hasPermission`方法总是返回`false`，拒绝所有的方法访问。

注册了许可评估器后，我们就可以在SpEL中使用`hasPermission`来保护方法，它会调用许可评估器的相应`hasPermission`方法来决定用户是否有权限调用方法：

```java
@PreAuthorize("hasAnyRole({'ROLE_SPITTER', 'ROLE_ADMIN'})")
@PreFilter("hasPermission(targetObject, 'delete')")
public void deleteSpittles(List<Spittle> spittles) { ... }
```

