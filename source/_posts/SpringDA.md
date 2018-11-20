---
title: SpringDA
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

### 使用profile选择数据源

参见《SpringCore》的“Spring Profile”。

## 使用JDBC模板

Spring将数据访问的样板代码抽象到模板类中。Spring为JDBC提供了三个模板类：

- `JdbcTemplate`
- `NamedParameterJdbcTemplate`：是对`JdbcTemplate`的包装，以提供命名参数而不是传统的JDBC `?` 占位符。
- `SimpleJdbcTemplate`（已废弃）

### 注册JDBC模板

```java
@Bean
public JdbcTemplate jdbcTemplate(DataSource dataSource) {
	return new JdbcTemplate(dataSource);
}
```

在内部，`JdbcTemplate`将会捕获所有可能抛出的`SQLException`，并将通用的`SQLException`转换为独立于持久化方案的Spring数据访问异常，然后将其重新抛出。并且Spring的数据访问异常都继承自`DataAccessException`异常，都是运行时异常。因而，没有必要捕获Spring所抛出的数据访问异常。

### 插入数据

```java
@Repository
public class JdbcSpitterRepository implements SpitterRepository {
  private static final String INSERT_SPITTER = "insert into Spitter (username, password, fullname, email, updateByEmail) values (?, ?, ?, ?, ?)";
  
  private JdbcOperations jdbcOperations;
  
  @Inject
  public JdbcSpitterRepository(JdbcOperations jdbcOperations) {
  	this.jdbcOperations = jdbcOperations;
  }
  
  public void addSpitter(Spitter spitter) {
    jdbcOperations.update(
      INSERT_SPITTER,
      spitter.getUsername(),
      spitter.getPassword(),
      spitter.getFullName(),
      spitter.getEmail(),
      spitter.isUpdateByEmail());
  }
  ...
}
```

注意：`JdbcTemplate`实现了`JdbcOperations`接口。

标注`@Repository`使得该类被注册成为一个数据访问Bean。

### 读取数据

```java
@Repository
public class JdbcSpitterRepository implements SpitterRepository {
  private static final String SELECT_SPITTER_BY_ID = "select id, username, password, fullname, email, updateByEmail from Spitter where id=?";

  private JdbcOperations jdbcOperations;
  
  @Inject
  public JdbcSpitterRepository(JdbcOperations jdbcOperations) {
  	this.jdbcOperations = jdbcOperations;
  }
  
  public Spitter findOne(long id) {
    return jdbcOperations.queryForObject(
      SELECT_SPITTER_BY_ID, 
      new SpitterRowMapper(), 
      id);
  }
  
  private static final class SpitterRowMapper implements RowMapper<Spitter> {
    public Spitter mapRow(ResultSet rs, int rowNum) throws SQLException {
      return new Spitter(
        rs.getLong("id"),
        rs.getString("username"),
        rs.getString("password"),
        rs.getString("fullName"),
        rs.getString("email"),
        rs.getBoolean("updateByEmail"));
    }
  }
  ...
}
```

### 使用Lambda表达式

```java
public Spitter findOne(long id) {
  return jdbcOperations.queryForObject (
    SELECT_SPITTER_BY_ID,
    (rs, rowNum) -> {
      return new Spitter(
        rs.getLong("id"),
        rs.getString("username"),
        rs.getString("password"),
        rs.getString("fullName"),
        rs.getString("email"),
        rs.getBoolean("updateByEmail"));
    },
    id);
}
```

另外，我们还可以使用Java 8的方法引用，在单独的方法中定义映射逻辑：

```java
public Spitter findOne(long id) {
  return jdbcOperations.queryForObject(
    SELECT_SPITTER_BY_ID, 
    this::mapSpitter, 
    id);
}

private Spitter mapSpitter(ResultSet rs, int row) throws SQLException {
  return new Spitter(
    rs.getLong("id"),
    rs.getString("username"),
    rs.getString("password"),
    rs.getString("fullName"),
    rs.getString("email"),
    rs.getBoolean("updateByEmail"));
}
```

### 使用命名参数

注册`NamedParameterJdbcTemplate`  Bean：

```java
@Bean
public NamedParameterJdbcTemplate jdbcTemplate(DataSource dataSource) {
	return new NamedParameterJdbcTemplate(dataSource);
}
```

Repository：

```java
private static final String INSERT_SPITTER = "insert into Spitter (username, password, fullname, email, updateByEmail) values (:username, :password, :fullname, :email, :updateByEmail)";
public void addSpitter(Spitter spitter) {
  Map<String, Object> paramMap = new HashMap<String, Object>();
  paramMap.put("username", spitter.getUsername());
  paramMap.put("password", spitter.getPassword());
  paramMap.put("fullname", spitter.getFullName());
  paramMap.put("email", spitter.getEmail());
  paramMap.put("updateByEmail", spitter.isUpdateByEmail());
  jdbcOperations.update(INSERT_SPITTER, paramMap);
}
```

## 使用`SimpleJdbcInsert` 与`SimpleJdbcCall` 

## 使用RDBMS 对象

# ORM

## Hibernate

### 注册Session工厂Bean

Hibernate主要是通过`org.hibernate.Session`接口提供数据访问功能。

获取Hibernate Session对象的标准方式是借助于Hibernate的`SessionFactory`接口的实现类，它主要负责Hibernate Session的打开、关闭及管理。

Spring提供了`org.springframework.orm.hibernate5.LocalSessionFactoryBean`来获取Hibernate SessionFactory。

```java
@Bean
public LocalSessionFactoryBean sessionFactory(DataSource dataSource) {
  LocalSessionFactoryBean sfb = new LocalSessionFactoryBean();
  sfb.setDataSource(dataSource);
  sfb.setPackagesToScan(new String[] { "com.habuma.spittr.domain" });
  Properties props = new Properties();
  props.setProperty("dialect", "org.hibernate.dialect.H2Dialect");
  sfb.setHibernateProperties(props);
  return sfb;
}
```

### 创建Repository

```java
@Repository
public class HibernateSpitterRepository implements SpitterRepository {
  private SessionFactory sessionFactory;

  @Inject
  public HibernateSpitterRepository(SessionFactory sessionFactory) {
    this.sessionFactory = sessionFactory;
  }

  private Session currentSession() {
    return sessionFactory.getCurrentSession();
  }

  public long count() {
    return findAll().size();
  }

  public Spitter save(Spitter spitter) {
    Serializable id = currentSession().save(spitter);
    return new Spitter((Long) id, 
                       spitter.getUsername(), 
                       spitter.getPassword(), 
                       spitter.getFullName(), 
                       spitter.getEmail(), 
                       spitter.isUpdateByEmail());
  }

  public Spitter findOne(long id) {
    return (Spitter) currentSession().get(Spitter.class, id); 
  }

  public Spitter findByUsername(String username) {		
    return (Spitter) currentSession() 
      .createCriteria(Spitter.class) 
      .add(Restrictions.eq("username", username))
      .list().get(0);
  }

  public List<Spitter> findAll() {
    return (List<Spitter>) currentSession().createCriteria(Spitter.class).list(); 
  }
}
```

### 注册`PersistenceExceptionTranslationPostProcessor`

当我们直接使用Hibernate上下文的`Session`，而不是`HibernateTemplate`（不推荐）时，为了给不使用模板的Hibernate Repository添加异常转换功能，只需要在Spring应用上下文中添加一个`PersistenceExceptionTranslationPostProcessor` Bean：

```java
@Bean
public BeanPostProcessor persistenceTranslation() {
	return new PersistenceExceptionTranslationPostProcessor();
}
```

`PersistenceExceptionTranslationPostProcessor`是一个Bean后置处理器，它会在所有拥有`@Repository`标注的类上添加一个通知器（Advisor），这样就会捕获任何平台相关的异常，并以Spring的数据访问异常重新抛出。

## JPA

### 配置实体管理器工厂

# Spring Data

## Spring Data JDBC

## Spring Data JPA