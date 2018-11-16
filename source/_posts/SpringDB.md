---
title: SpringDB
date: 2018-11-16 10:34:40
tags: []
---

# JDBC

## 配置数据源

### 使用JNDI数据源

通过JNDI获取数据源的好处在于数据源完全可以在应用程序之外进行管理。另外，在应用服务器中管理的数据源通常以池的方式组织，具备更好的性能，并且还支持系统管理员对其进行热切换。

```java
@Bean
public JndiObjectFactoryBean dataSource() {
  JndiObjectFactoryBean jndiObjectFB = new JndiObjectFactoryBean();
  jndiObjectFB.setJndiName("jdbc/SpittrDS");
  jndiObjectFB.setResourceRef(true);
  jndiObjectFB.setProxyInterface(javax.sql.DataSource.class);
  return jndiObjectFB;
}
```

或者：

```xml
<jee:jndi-lookup id="dataSource"
                 jndi-name="/jdbc/SpitterDS"
                 resource-ref="true" />
```

> 如果应用程序运行在Java应用服务器中，你需要将`resource-ref`属性设置为`true`，这样给定的`jndi-name`将会自动添加`java:comp/env/`前缀。

### 使用数据源连接池

使用数据源连接池也是配置数据源的不错选择。

Spring支持多种开源的连接池实现：

- BoneCP
- Apache Commons DBCP
- c3p0

#### DBCP

```java
@Bean
public BasicDataSource dataSource() {
  BasicDataSource ds = new BasicDataSource();
  ds.setDriverClassName("org.h2.Driver");
  ds.setUrl("jdbc:h2:tcp://localhost/~/spitter");
  ds.setUsername("sa");
  ds.setPassword("");
  ds.setInitialSize(5);
  ds.setMaxActive(10);
  return ds;
}
```

或者：

```xml
<bean id="dataSource" 
      class="org.apache.commons.dbcp.BasicDataSource"
      p:driverClassName="org.h2.Driver"
      p:url="jdbc:h2:tcp://localhost/~/spitter"
      p:username="sa"
      p:password=""
      p:initialSize="5"
      p:maxActive="10" />
```

`BasicDataSource`的池配置属性：

| 池配置属性                 | 说明                                                         |
| -------------------------- | ------------------------------------------------------------ |
| initialSize                | 池启动时创建的初始连接数量。                                 |
| maxActive                  | 同一时间可从池中分配的最多连接数。如果设置为0，则表示无限制。 |
| maxIdle                    | 池里不会被释放的最多空闲连接数。如果设置为0，则表示无限制。  |
| maxOpenPreparedStatements  | 在同一时间能够从语句池中分配的预处理语句的最大数量。如果设置为0，则表示无限制。 |
| maxWait                    | 在抛出异常之前，池等待连接回收的最大时间（当没有可用连接时）。如果设置为-1，则表示无限等待。 |
| minEvictableIdleTimeMillis | 连接在池中保持空闲而不被回收的最大时间。                     |
| minIdle                    | 在不创建新连接的情况下，池中保持空闲的最小连接数。           |
| poolPreparedStatements     | 是否对预处理语句进行池管理。（布尔值）                       |

### 使用JDBC驱动的数据源

在Spring中，通过JDBC驱动定义数据源是最简单的配置方式。Spring提供了三个这样的数据源类：

- DriverManagerDataSource：在每个连接请求时都会返回一个新建的连接。
- SimpleDriverDataSource：类似`DriverManagerDataSource`。
- SingleConnectionDataSource：在每个连接请求时都会返回同一个连接。

```java
@Bean
public DataSource dataSource() {
  DriverManagerDataSource ds = new DriverManagerDataSource();
  ds.setDriverClassName("org.h2.Driver");
  ds.setUrl("jdbc:h2:tcp://localhost/~/spitter");
  ds.setUsername("sa");
  ds.setPassword("");
  return ds;
}
```

或者：

```xml
<bean id="dataSource"
      class="org.springframework.jdbc.datasource.DriverManagerDataSource"
      p:driverClassName="org.h2.Driver"
      p:url="jdbc:h2:tcp://localhost/~/spitter"
      p:username="sa"
      p:password="" />
```

使用JDBC驱动的数据源都没有提供连接池功能，通常适合小应用或开发环境。

### 使用嵌入式数据源

```java
@Bean
public DataSource dataSource() {
  return new EmbeddedDatabaseBuilder()
    .setType(EmbeddedDatabaseType.H2)
    .addScript("classpath:schema.sql")
    .addScript("classpath:test-data.sql")
    .build();
}
```

或者：

```xml
<?xml version="1.0" encoding="UTF-8"?> 
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc"
       xmlns:c="http://www.springframework.org/schema/c"
       xsi:schemaLocation="http://www.springframework.org/schema/jdbc
                           http://www.springframework.org/schema/jdbc/spring-jdbc-3.1.xsd
                           http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd">
  ...
  <jdbc:embeddeddatabase id="dataSource" type="H2"> 
    <jdbc:script location="com/habuma/spitter/db/jdbc/schema.sql"/> 
    <jdbc:script location="com/habuma/spitter/db/jdbc/test-data.sql"/> 
  </jdbc:embedded-database>
  ...
</beans>
```

嵌入式的数据源适合于开发或测试环境。