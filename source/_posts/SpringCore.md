---
title: SpringCore
date: 2018-06-01 21:57:20
tags: [5.1.0]
---

# Spring框架

![Spring-framework](SpringCore/Spring-framework.png)

# IoC

IoC（Inversion of Control，控制反转）也称为DI（依赖注入）。DI能够让相互协作的软件组件保持**松耦合**。

IoC容器是Spring框架的核心，它使用DI管理构成应用的组件。Spring IoC容器可分成两种不同类型：

- `org.springframework.beans.factory.BeanFactory`：最简单的IoC容器。
- `org.springframework.context.ApplicationContext`：应用上下文，基于`BeanFactory`构建，并提供应用框架级别的服务。

在Spring中，构成应用程序主干并由Spring IoC容器管理的对象称为bean。 bean是一个由Spring IoC容器实例化，装配（Wiring）和管理的对象。除此之外，bean只是应用程序中众多对象之一。 Bean及其之间的依赖关系反映在容器使用的配置元数据中。

![](SpringCore/IoC-container.png)

## Bean的生命周期

![Bean](SpringCore/Bean.jpg)

1. Spring 对 Bean进行实例化；
2. Spring将值和Bean的引用注入到Bean对应的属性中；
3. 如果Bean实现了`BeanNameAware`接口，Spring将Bean的ID传递给`setBeanName`方法；
4. 如果Bean实现了`BeanFactoryAware`接口，Spring将调用`setBeanFactory`方法，将`BeanFactory`容器实例传入；
5. 如果Bean实现了`ApplicationContextAware`接口，Spring将调用`setApplicationContext`方法，将Bean所在的应用上下文的引用传入进来；
6. 如果Bean实现了`BeanPostProcessor`接口，Spring将调用它们的`postProcessBeforeInitialization`方法；
7. 如果Bean实现了`InitializingBean`接口，Spring将调用它们的`afterPropertiesSet`方法；
8. 如果Bean使用`init-method`声明了自定义的初始化方法，该方法也会被调用；
9. 如果Bean实现了`BeanPostProcessor`接口，Spring将调用它们的`postProcessAfterInitialization`方法；
10. 此时，Bean已经准备就绪，可以被应用程序使用了，它们将一直驻留在应用上下文中，直到该应用上下文被销毁；
11. 如果Bean实现了`DisposableBean`接口，Spring将调用它的`destroy`方法；
12. 如果Bean使用`destroy-method`声明了自定义的销毁方法，该方法会被调用。

## 装配Bean

Spring 能过配置元数据（Configuration Metadata）来装配Bean。

### 装配方式

Spring提供了三种装配Bean的方法：

- 在XML中进行显式装配；
- 在Java中进行显式装配；
- 隐式的Bean发现机制和自动装配。

这些装配方式可以任意互相搭配，比如：可以选择使用XML装配一些Bean，使用基于Java的配置来装配另一些Bean，而将剩余的Bean让Spring去自动发现。

XML和基于Java的装配方式更集中，且不会对代码造成侵入。自动装配和基于Java的配置具有类型安全等好处。

通常建议尽可能地使用自动装配机制，当必须要显式装配Bean的时候（比如，有些源码不是由你来维护的，而当你需要为这些代码装配Bean时），推荐使用类型安全并且比XML更加强大的基于Java的装配。最后，只有当你想要使用便利的XML命名空间，并且在基于Java的配置中没有同样的实现时，才应该使用XML的配置。

### 自动化装配

#### 创建可被发现的Bean

首先，创建Bean的接口：

```java
package soundsystem;
public interface CompactDisc {
  void play();
}
```

然后，创建Bean的实现类：

```java
package soundsystem;
import org.springframework.stereotype.Component;

@Component
public class SgtPeppers implements CompactDisc {
  private String title = "Sgt. Pepper's Lonely Hearts Club Band";  
  private String artist = "The Beatles";
  
  public void play() {
    System.out.println("Playing " + title + " by " + artist);
  }
}
```

实现类上要带有`@Component`等标注，这样Spring才会发现并为该类创建Bean。

#### 启用组件扫描

为了自动装配带有`@Component`等标注的Bean，需要启用组件扫描（默认是不启用的）。可以使用基于Java的配置或基于XML的配置来启用组件扫描。

基于Java的配置：

```java
package soundsystem;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration
@ComponentScan
public class CDPlayerConfig { 
}
```

`@ComponentScan`标注默认指定的基础包是被`@ComponentScan`标注的配置类所在的包，即它会扫描该基础包以及其下的任意层次的子包，查找带有`@Component`标注的类。

如果要扫描不同的包（例如配置类放在单独的包中），或要扫描多个基础包，则需要显式指定要扫描的基础包。

最简单指定基础包的方式是通过`@ComponentScan`的`value`属性：

```java
@ComponentScan("foo.bar")
```

另外，还可以使用`basePackages`或`basePackageClasses`属性来指定基础包。前者以字符串形式指定基础包，后者通过类或接口的class对象来指定，class对象所在的包即为基础包。而且，两者都可指定多个基础包：

```java
@ComponentScan(basePackages={"pkg1", "pkg2"})

@ComponentScan(basePackageClasses={Foo.class, Bar.class})
```

基于XML的配置：（src/resources/META-INF/spring/soundsystem.xml）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:c="http://www.springframework.org/schema/c"
  xmlns:p="http://www.springframework.org/schema/p"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

  <context:component-scan base-package="soundsystem" />

</beans>
```

#### 自动装配

在自动装配中，要将Bean与它的依赖装配在一起，需要借助标注`@Autowired`或`@Inject`（Java依赖注入规范）。

```java
package soundsystem;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
  private CompactDisc cd;

  @Autowired
  public CDPlayer(CompactDisc cd) {
    this.cd = cd;
  }

  public void play() {
    cd.play();
  }
}
```

`@Autowired`标注不仅能够用在构造器上，还能用在类的任何方法上（通常是属性的setter方法上）：

```java
@Autowired
public void setCompactDisc(CompactDisc cd) {
  this.cd = cd;
}

@Autowired
public void insertDisc(CompactDisc cd) {
  this.cd = cd;
}
```

`@Autowired`标注的`required`属性设置为`false`时，Spring会尝试执行自动装配，但是如果没有匹配的Bean，Spring将会让这个Bean处于未装配的状态。这时，如果在代码中没有进行null检查的话，这个处于未装配状态的属性有可能会出现`NullPointerException`异常。`required`属性的默认值是`true`，即如果没有匹配的Bean，那么在应用上下文创建的时候，Spring会抛出一个异常。

`@Autowired`标注和`@Inject`标注都是按类型进行装配的。而`@Resource`标注是属于Java EE标准的，它默认按照Bean的ID进行装配，ID可以通过`name`属性进行指定。如果没有指定`name`属性，当标注写在字段上时，就默认取字段名进行查找；如果标注写在setter方法上，就默认取属性名进行装配。当找不到与ID匹配的bean时，才按照类型进行装配。但需要注意的是，`name`一旦指定，就只会按照ID进行装配。例如：

```java
@Resource(name="userRepository")
private UserRepository userRepository;
```

### 基于Java的装配

#### 编写方法创建Bean

要在配置类中编写一个方法，该方法会创建所需类型的Bean，然后给这个方法加上`@Bean`标注：

```java
package soundsystem;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CDPlayerConfig {  
  @Bean
  public CompactDisc randomBeatlesCD() {
    int choice = (int) Math.floor(Math.random() * 4);
    if (choice == 0) {
      return new SgtPeppers();
    } else if (choice == 1) {
      return new WhiteAlbum();
    } else if (choice == 2) {
      return new HardDaysNight();
    } else {
      return new Revolver();
    }
  }
}
```

> 这里，配置类上不需要加上`@ComponentScan`标注。

标注`@Bean`的方法只会在首次请求时执行一次，以后每次请求都将被Spring拦截，并从应用上下文中返回相同的Bean。也就是说，默认情况下，Spring中的Bean都是**单例的**。

#### 编程装配

```java
package soundsystem;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CDPlayerConfig {
  @Bean
  public CompactDisc compactDisc() {
    return new SgtPeppers();
  }
  
  @Bean
  public CDPlayer cdPlayer(CompactDisc compactDisc) {
    return new CDPlayer(compactDisc);
  }
}
```

在这里，`cdPlayer`方法请求一个`CompactDisc`Bean作为参数。当Spring调用`cdPlayer`方法创建`CDPlayter`Bean时，它会自动装配一个`CompactDisc`到配置方法之中。这种方式不需要将`CompactDisc`声明到同一个配置类中，你可以将配置分散到多个配置类中。甚至不要求`CompactDisc`必须要在配置类中声明，也可是通过组件扫描自动发现或通过XML来配置。

下面还有一种方式也可以实现相同的装配效果：

```java
package soundsystem;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CDPlayerConfig {
  @Bean
  public CompactDisc compactDisc() {
    return new SgtPeppers();
  }
  
  @Bean
  public CDPlayer cdPlayer() {
    return new CDPlayer(compactDisc());
  }
}
```

这种方式通过“调用”`@Bean`方法的方式来装配Bean（实际上每次调用`@Bean`方法都将被Spring拦截，只在首次调用时真正执行方法，后面将从IoC容器中直接返回相同的Bean）。

这种方式要求Bean与它的依赖Bean必须在同一个配置类中声明。

### 基于XML的装配

#### 配置Bean

daos.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="accountDao"
        class="org.springframework.samples.jpetstore.dao.jpa.JpaAccountDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao" />

    <!-- more bean definitions for data access objects go here -->

</beans>
```

在基于XML的配置中，你不再需要直接手动创建Bean。当Spring发现`<bean>`元素时，它将会调用`class`属性指定的类的默认构造器来创建Bean。

#### 构造器注入

使用XML来配置构造器注入，可以使用`<constructor-arg>`元素或Spring 3.0引入的c-命名空间来配置。

使用`<constructor-arg>`元素配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean id="compactDisc" class="soundsystem.collections.BlankDisc">
    <constructor-arg value="Sgt. Pepper's Lonely Hearts Club Band" />
    <constructor-arg value="The Beatles" />
    <constructor-arg>
      <list>
        <value>Sgt. Pepper's Lonely Hearts Club Band</value>
        <value>With a Little Help from My Friends</value>
        <value>Lucy in the Sky with Diamonds</value>
        <value>Getting Better</value>
        <value>Fixing a Hole</value>
        <value>She's Leaving Home</value>
        <value>Being for the Benefit of Mr. Kite!</value>
        <value>Within You Without You</value>
        <value>When I'm Sixty-Four</value>
        <value>Lovely Rita</value>
        <value>Good Morning Good Morning</value>
        <value>Sgt. Pepper's Lonely Hearts Club Band (Reprise)</value>
        <value>A Day in the Life</value>
      </list>
    </constructor-arg>
  </bean>
        
  <bean id="cdPlayer" class="soundsystem.CDPlayer">
    <constructor-arg ref="compactDisc" />
  </bean>

</beans>
```

使用c-命名空间来配置：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:c="http://www.springframework.org/schema/c"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean id="compactDisc" class="soundsystem.BlankDisc"
        c:_0="Sgt. Pepper's Lonely Hearts Club Band" 
        c:_1="The Beatles" />
        
  <bean id="cdPlayer" class="soundsystem.CDPlayer"
        c:_-ref="compactDisc" />

</beans>
```

使用c-命名空间来声明构造器参数有两种方式：

- 通过构造器参数名：
  - `c:构造器参数名="字面量"`：例如，`c:foo="value"`。
  - `c:构造器参数名-ref="要注入的Bean的ID"`：例如，`c:bar-ref="beanID"`。
- 通过构造器参数的位置索引：
  - `c:_索引="字面量"`：例如，`c:_0="value"`。
  - `c:_索引-ref="要注入的Bean的ID"`：例如，`c:_1-ref="beanID"`。
  - 如果构造器只有一个参数，则索引可以省略。例如：`c:_="value"`。

> 注意：通过构造器参数名方式，需要在编译代码的时候，将调试符号（debug symbol）保存在类代码中。如果你优化构建过程，将调试符号移除掉，那么这种方式可能就无法正常执行了。

#### 属性注入

对于强依赖建议使用构造器注入，而对于可选性的依赖则建议使用属性注入。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xmlns:p="http://www.springframework.org/schema/p"
	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

  <bean id="compactDisc"
        class="soundsystem.properties.BlankDisc">
    <property name="title" value="Sgt. Pepper's Lonely Hearts Club Band" />
    <property name="artist" value="The Beatles" />
    <property name="tracks">
      <list>
        <value>Sgt. Pepper's Lonely Hearts Club Band</value>
        <value>With a Little Help from My Friends</value>
        <value>Lucy in the Sky with Diamonds</value>
        <value>Getting Better</value>
        <value>Fixing a Hole</value>
        <value>She's Leaving Home</value>
        <value>Being for the Benefit of Mr. Kite!</value>
        <value>Within You Without You</value>
        <value>When I'm Sixty-Four</value>
        <value>Lovely Rita</value>
        <value>Good Morning Good Morning</value>
        <value>Sgt. Pepper's Lonely Hearts Club Band (Reprise)</value>
        <value>A Day in the Life</value>
      </list>
    </property>
  </bean>

  <bean id="cdPlayer"
        class="soundsystem.properties.CDPlayer"
        p:compactDisc-ref="compactDisc" />

</beans>
```

Spring提供了简洁的p-命名空间，作为`<property>`元素的替代方案。

p-命名空间的命名约定与c-命名空间的类似。

#### 装配集合

如果装配的是集合，则只能使用`<constructor-arg>`元素和`<property>`元素来配置，而不能使用c-命名空间和p-命名空间来配置。

##### 列表

```xml
<constructor-arg>
	<list>
    <value>a list element followed by a reference</value>
  	<ref bean="myDataSource" />
    …
  </list>
</constructor-arg>
```

##### 集合

```xml
<property name="someSet">
  <set>
    <value>just some string</value>
    <ref bean="myDataSource" />
  </set>
</property>
```

##### 映射

```xml
<property name="someMap">
  <map>
    <entry key="an entry" value="just some string"/>
    <entry key ="a ref" value-ref="myDataSource"/>
  </map>
</property>
```

> 映射的键和值、集合的值都可以是下列元素：
>
> bean、ref、idref、list、set、map、props、value、null

##### java.util.Properties 

```xml
<!-- typed as a java.util.Properties -->
<property name="properties">
  <value>
    jdbc.driver.className=com.mysql.jdbc.Driver
    jdbc.url=jdbc:mysql://localhost:3306/mydb
  </value>
</property>
```

或者

```xml
<!-- results in a setAdminEmails(java.util.Properties) call -->
<property name="adminEmails">
  <props>
    <prop key="administrator">administrator@example.org</prop>
    <prop key="support">support@example.org</prop>
    <prop key="development">development@example.org</prop>
  </props>
</property>
```

#### 使用util-命名空间

| 元素                  | 描述                                                 |
| --------------------- | ---------------------------------------------------- |
| `<util:constant>`     | 引用某个类型的`public static`域，并将其暴露为Bean。  |
| `<util:list>`         | 创建一个`java.util.List`类型的Bean。                 |
| `<util:map>`          | 创建一个`java.util.Map`类型的Bean。                  |
| `<util:properties>`   | 创建一个`java.util.Properties`类型的Bean。           |
| `util:property-path>` | 引用一个Bean的属性（或内嵌属性），并将其暴露为Bean。 |
| `<util:set>`          | 创建一个`java.util.Set`类型的Bean。                  |

例如：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:p="http://www.springframework.org/schema/p"
  xmlns:util="http://www.springframework.org/schema/util"
  xsi:schemaLocation="http://www.springframework.org/schema/beans 
    http://www.springframework.org/schema/beans/spring-beans.xsd
    http://www.springframework.org/schema/util 
    http://www.springframework.org/schema/util/spring-util.xsd">

  <bean id="compactDisc"
        class="soundsystem.properties.BlankDisc"
        p:title="Sgt. Pepper's Lonely Hearts Club Band"
        p:artist="The Beatles"
        p:tracks-ref="trackList" />

  <util:list id="trackList">  
    <value>Sgt. Pepper's Lonely Hearts Club Band</value>
    <value>With a Little Help from My Friends</value>
    <value>Lucy in the Sky with Diamonds</value>
    <value>Getting Better</value>
    <value>Fixing a Hole</value>
    <value>She's Leaving Home</value>
    <value>Being for the Benefit of Mr. Kite!</value>
    <value>Within You Without You</value>
    <value>When I'm Sixty-Four</value>
    <value>Lovely Rita</value>
    <value>Good Morning Good Morning</value>
    <value>Sgt. Pepper's Lonely Hearts Club Band (Reprise)</value>
    <value>A Day in the Life</value>
  </util:list>

</beans>
```



#### 装配内嵌Bean

```xml
<bean id="outer" class="...">
  <!-- instead of using a reference to a target bean, simply define the target bean inline -->
  <property name="target">
    <bean class="com.example.Person"> <!-- this is the inner bean -->
      <property name="name" value="Fiona Apple"/>
      <property name="age" value="25"/>
    </bean>
  </property>
</bean>
```

#### 装配空值和空串

空值：

```xml
<constructor-arg><null/></constructor-arg>

<property name="email"><null/></property>
```

空串：

```xml
<constructor-arg value="" />

<property name="email" value="" />
```

#### `<idref>`元素

```xml
<bean id="theTargetBean" class="..."/>

<bean id="theClientBean" class="...">
    <property name="targetName">
        <idref bean="theTargetBean"/>
    </property>
</bean>
```

等价于：

```xml
<bean id="theTargetBean" class="..." />

<bean id="client" class="...">
    <property name="targetName" value="theTargetBean"/>
</bean>
```

### 配置导入

可以使用`@Import`标注将多个配置类组合在一起，还可以使用`@ImportResource`标注将XML配置文件导入配置类：

```java
@Configuration
@Import(abc.FooConfig.class)
@ImportResource("classpath:bar-config.xml")
public class MyConfig {
  
}
```

`@Import`标注和`@ImportResource`标注都可以接受多个参数：

```java
@Import({abc.FooConfig.class, xyz.BarConfig.class})
```

也可以将多个XML配置文件通过`<import>`组合到一个XML配置文件中：（app.xml）

```xml
<beans>
  <import resource="services.xml"/>
  <import resource="resources/messageSource.xml"/>
  <import resource="/resources/themeSource.xml"/>

  <bean id="bean1" class="..."/>
  <bean id="bean2" class="..."/>
</beans>
```

注意：`<import>`元素只能导入其他的XML配置文件，并不能导入配置类。但可以使用`<bean>`元素来将配置类导入XML配置文件中：

```xml
<bean class="abc.FooConfig" />
```

通过导入将多个配置类或XML配置文件组合在一起，在实例化应用上下文时，只需要传入最高层次的那一个配置类或XML配置文件即可。例如：

```java
ApplicationContext context = new ClassPathXmlApplicationContext("app.xml");
```

否则，就需要列出所有的配置类或XML配置文件：

```java
ApplicationContext context = new AnnotationConfigApplicationContext(abc.FooConfig.class, xyz.BarConfig.class);
```

### Bean的命名

每个Bean可以有一个或多个标识符，这些标识符在托管bean的容器中必须是唯一的。

在自动化装配中，通过将Bean标识符传递给标注`@Component`来为Bean显式命名：

```java
@Component("foo")
```

自动化装配中，默认的命名是首字母小写的类名。例如，类名为`FooBar`，则Bean的默认名为`fooBar`。

另外，也可以使用Java依赖注入规范中的`@Named`标注来代替`@Component`：

```java
@Named("foo")
```

在基于Java的配置中，通过标注`@Bean`的`name`属性为Bean显式命名：

```java
@Bean(name="foo")
```

在基于Java的配置中，Bean的默认ID是`@Bean`标注的方法名。例如，方法名为`fooBar`，则Bean的默认名为`fooBar`。

在基于XML的配置元数据中，使用`id`属性、`name`属性或两者来指定Bean标识符。`id`属性只能指定一个标识符，而`name`属性可以指定多个别名。别名之间使用逗号、分号或空白字符分隔。

在基于XML的配置中，Bean的默认ID是Bean的完全限定类名加`#计数`组成。例如：`abc.FooBar#0`。

### Spring Profile

Spring Profile能够在**运行时**根据环境决定该创建哪个Bean和不创建哪个Bean。因此，同一个部署单元（可能会是WAR文件）能够适用于所有的环境，并且不需要重新构建。

#### 配置Profile

##### 在Java中配置Profile

在Java配置中，使用`@Profile`标注来指定某个Bean属于哪一个Profile。`@Profile`可以标注在类级别，也可以标注在方法级别。

标注在类级别：

```java
@Configuration
@Profile("qa")
public class QAProfileConfig {
  @Bean(destroyMethod="close")
  public DataSource dataSource() {
    BasicDataSource dataSource = new BasicDataSource();
    dataSource.setUrl("jdbc:h2:tcp://dbserver/~/test");
    dataSource.setDriverClassName("org.h2.Driver");
    dataSource.setUsername("sa");
    dataSource.setPassword("password");
    dataSource.setInitialSize(20);
    dataSource.setMaxActive(30);
    return dataSource;
  }
}
```

标注在方法上：

```java
@Configuration
public class DataSourceConfig {
  @Bean(destroyMethod = "shutdown")
  @Profile("dev")
  public DataSource embeddedDataSource() {
    return new EmbeddedDatabaseBuilder()
        .setType(EmbeddedDatabaseType.H2)
        .addScript("classpath:schema.sql")
        .addScript("classpath:test-data.sql")
        .build();
  }

  @Bean
  @Profile("prod")
  public DataSource jndiDataSource() {
    JndiObjectFactoryBean jndiObjectFactoryBean = new JndiObjectFactoryBean();
    jndiObjectFactoryBean.setJndiName("jdbc/myDS");
    jndiObjectFactoryBean.setResourceRef(true);
    jndiObjectFactoryBean.setProxyInterface(javax.sql.DataSource.class);
    return (DataSource) jndiObjectFactoryBean.getObject();
  }
}
```

另外，`@Profile`可以同时指定多个Profile：

```java
@Profile({"dev", "test"})
```

`@Profile`还可以通过`!`来表示取反。例如：`@Profile("!test") `。

##### 在XML中配置Profile

在XML中通过`<beans>`元素的`profile`属性来配置Profile。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc"
       xsi:schemaLocation="
         http://www.springframework.org/schema/jdbc
         http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
         http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd"
       profile="dev">

  <jdbc:embedded-database id="dataSource" type="H2">
    <jdbc:script location="classpath:schema.sql" />
    <jdbc:script location="classpath:test-data.sql" />
  </jdbc:embedded-database>
  
</beans>
```

还可以在根`<beans>`元素中嵌套定义`<beans>`元素，而不是为每个环境都创建一个Profile XML文件。这能够将所有的Profile Bean定义放到同一个XML文件中：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:jdbc="http://www.springframework.org/schema/jdbc"
       xmlns:jee="http://www.springframework.org/schema/jee"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="
         http://www.springframework.org/schema/jee
         http://www.springframework.org/schema/jee/spring-jee.xsd
         http://www.springframework.org/schema/jdbc
         http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
         http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd">

  <beans profile="dev">
    <jdbc:embedded-database id="dataSource" type="H2">
      <jdbc:script location="classpath:schema.sql" />
      <jdbc:script location="classpath:test-data.sql" />
    </jdbc:embedded-database>
  </beans>
  
  <beans profile="qa">
  	<bean id="dataSource"
          class="org.apache.commons.dbcp.BasicDataSource"
          destroy-method="close"
          p:url="jdbc:h2:tcp://dbserver/~/test"
          p:driverClassName="org.h2.Driver"
          p:username="sa"
          p:password="password"
          p:initialSize="20"
          p:maxActive="30" />
  </beans>
  
  <beans profile="prod">
    <jee:jndi-lookup id="dataSource"
      lazy-init="true"
      jndi-name="jdbc/myDatabase"
      resource-ref="true"
      proxy-interface="javax.sql.DataSource" />
  </beans>
</beans>
```

#### 激活Profile

已经配置了Profile的Bean，只有当指定的Profile激活时，相应的Bean才会被创建。而没有指定任何Profile的Bean，总是会被创建，与激活哪个Profile没有关系。

Spring通过两个属性来确定哪个Profile处于激活状态：

- `spring.profiles.active`
- `spring.profiles.default`

如果设置了`spring.profiles.active`属性，则它的值就是被激活的Profile。否则，Spring会继续查找`spring.profiles.default`属性的值。如果两个属性均未设置，那就没有激活的Profile，因此只会创建那些没有定义在Profile中的Bean。

这两个属性都可以同时指定多个Profile，使用逗号分隔。

有多种方式来设置这两个属性：

- 作为`DispatcherServlet`的初始化参数：（web.xml）

  ```xml
  <servlet>
  	<servlet-name>appServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
    	<param-name>spring.profiles.default</param-name>
      <param-value>dev</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>
  ```

- 作为Web应用的上下文参数：（web.xml）

  ```xml
  <context-param>
  	<param-name>spring.profiles.default</param-name>
    <param-value>qa,demo</param-value>
  </context-param>
  ```

- 作为JNDI条目；

- 作为环境变量；

- 作为JVM的系统属性：

  ```bash
  -Dspring.profiles.active="profile1,profile2"
  ```

- 在集成测试类上，使用`@ActiveProfiles`标注设置。

  ```java
  public class DataSourceConfigTest {
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes=DataSourceConfig.class)
    @ActiveProfiles("dev")
    public static class DevDataSourceTest {
      @Autowired
      private DataSource dataSource;
      
      @Test
      public void shouldBeEmbeddedDatasource() {
        assertNotNull(dataSource);
        JdbcTemplate jdbc = new JdbcTemplate(dataSource);
        List<String> results = jdbc.query("select id, name from Things", new RowMapper<String>() {
          @Override
          public String mapRow(ResultSet rs, int rowNum) throws SQLException {
            return rs.getLong("id") + ":" + rs.getString("name");
          }
        });
        
        assertEquals(1, results.size());
        assertEquals("1:A", results.get(0));
      }
    }
  
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration(classes=DataSourceConfig.class)
    @ActiveProfiles("prod")
    public static class ProductionDataSourceTest {
      @Autowired
      private DataSource dataSource;
      
      @Test
      public void shouldBeEmbeddedDatasource() {
        // should be null, because there isn't a datasource configured in JNDI
        assertNull(dataSource);
      }
    }
    
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration("classpath:datasource-config.xml")
    @ActiveProfiles("dev")
    public static class DevDataSourceTest_XMLConfig {
      @Autowired
      private DataSource dataSource;
      
      @Test
      public void shouldBeEmbeddedDatasource() {
        assertNotNull(dataSource);
        JdbcTemplate jdbc = new JdbcTemplate(dataSource);
        List<String> results = jdbc.query("select id, name from Things", new RowMapper<String>() {
          @Override
          public String mapRow(ResultSet rs, int rowNum) throws SQLException {
            return rs.getLong("id") + ":" + rs.getString("name");
          }
        });
        
        assertEquals(1, results.size());
        assertEquals("1:A", results.get(0));
      }
    }
  
    @RunWith(SpringJUnit4ClassRunner.class)
    @ContextConfiguration("classpath:datasource-config.xml")
    @ActiveProfiles("prod")
    public static class ProductionDataSourceTest_XMLConfig {
      @Autowired(required=false)
      private DataSource dataSource;
      
      @Test
      public void shouldBeEmbeddedDatasource() {
        // should be null, because there isn't a datasource configured in JNDI
        assertNull(dataSource);
      }
    }
  }
  ```

另外，还可以通过硬编码方式激活Profile：

```java
AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext();
ctx.getEnvironment().setActiveProfiles("development");
ctx.register(SomeConfig.class, StandaloneDataConfig.class, JndiDataConfig.class);
ctx.refresh();
```

### 条件化的Bean

Spring 4.0引入了条件化的Bean机制，它可以自行设定条件来控制Bean的创建。它是比Profile机制更通用的机制。

例如，假设有一个名为`MagicBean`的类，我们希望只有设置了`magic`环境属性时，Spring才会实例化这个类。

首先，创建自定义条件：

```java
public class MagicExistsCondition implements Condition {
  @Override
  public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
    Environment env = context.getEnvironment();
    return env.containsProperty("magic");
  }
}
```

然后，条件化配置Bean：

```java
@Configuration
public class MagicConfig {
  @Bean
  @Conditional(MagicExistsCondition.class)
  public MagicBean magicBean() {
    return new MagicBean();
  }
}
```

当条件的`matches`方法返回`true`时才会创建`MagicBean`的实例。

### 处理自动装配的歧义性

在自动装配时，如果同时有多个Bean能够匹配，这时Spring就会抛出`NoUniqueBeanDefinitionException`异常。

Spring提供了两种方案来解决自动装配中的歧义性问题：你可以将可选的Bean中的某一个设置为首选（Primary），或者使用限定符（Qualifier）来帮助Spring将可选的Bean范围缩小到只有一个Bean。

#### 设置首选Bean

在Java配置中：

```java
@Component
@Primary
public class IceCream implements Dessert {…}
```

或者：

```java
@Bean
@Primary
public Dessert iceCream() {
  return new IceCream();
}
```

在XML配置中：

```xml
<bean id="iceCream"
      class="com.desserteater.IceCream"
      primary="true" />
```

为了使首选Bean有效，同一类型的Bean中，最多只能有一个Bean被设置为首选。但是，`@Primary`并不能限制设置多个首选Bean发生，它只是标示一个优先的可选方案，这也是设置首选Bean方案的局限性。

当遇到歧义性时，Spring将会使用首选的Bean。

#### 使用限定符

##### 为Bean设置限定符

所有的Bean，如果没有显式设置限定符，都会给定一个默认的限定符，它与Bean的ID相同。

可以使用`@Qualifier`标注为Bean显式设置限定符：

```java
@Component
@Qualifier("cold")
public class IceCream implements Dessert {…}
```

或者

```java
@Bean
@Qualifier("cold")
public Dessert iceCream() {
  return new IceCream();
}
```

使用为Bean显式设置限定符的好处是，可以减少与Bean的ID的耦合度。

为Bean设置限定符的最佳实践是为Bean选择特征性或描述性的术语作为限定符。

##### 使用限定符注入

```java
@Autowired
@Qualifier("cold")
public void setDessert(Dessert dessert) {
  this.dessert = dessert;
}
```

这样，就只会将带有`cold`限定符的Bean注入。

##### 自定义限定符标注

当多个Bean设置了相同的限定符时，就需要设置更多的限定符来区分它们。但是，很遗憾在Java中不允许同一条目上重复出现相同类型的多个标注。我们可以通过创建自定义的限定符标注来解决这个问题。

创建自定义限定符标注：

```java
@Target({ElementType.CONSTRUCTOR, ElementType.FIELD, ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Cold {}

@Target({ElementType.CONSTRUCTOR, ElementType.FIELD, ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Creamy {}

@Target({ElementType.CONSTRUCTOR, ElementType.FIELD, ElementType.METHOD, ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Qualifier
public @interface Fruity {}
```

使用自定义限定符标注：

```java
@Component
@Cold
@Creamy
public class IceCream implements Dessert {…}

@Component
@Cold
@Fruity
public class Popsicle implements Dessert {…}
```

则在注入点，可以使用下面方式将范围缩小到`IceCream`：

```java
@Autowired
@Cold
@Creamy
public void setDessert(Dessert dessert) {
  this.dessert = dessert;
}
```

### Bean的作用域

Spring Bean有多种作用域：

- 单例（`singleton`）：在整个应用中，只创建Bean的一个实例。
- 原型（`prototype`）：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的Bean实例。
- 会话（`session`）：在Web应用中，为每个会话创建一个Bean实例。
- 全局会话（`globalSession`）：类似于会话作用域，但仅在Portlet的Web应用中使用。
- 请求（`request`）：在Web应用中，为每个请求创建一个Bean实例。

在默认情况下，Spring应用上下文中所有Bean都是单例的。可以通过`@Scope`标注为Bean显式选择其他的作用域：

```java
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public class Notepad {…}
```

或者

```java
@Bean
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Notepad notepad() {
  return new Notepad();
}
```

在XML配置文件中，可以使用`<bean>`元素的`scope`属性来设置：

```xml
<bean id="notepad"
      class="com.myapp.Notepad"
      scope="prototype" />
```

会话和请求作用域只能在Web应用中使用，并且要在web.xml中做如下设置：

```xml
<listener>
	<listener-class>
  	org.springframework.web.context.request.RequestContextListener
  </listener-class>
</listener>
```



#### 作用域代理

在使用会话或请求作用域时，会遇到将会话或请求作用域的Bean注入到单例Bean中的问题。因为，单例Bean是在Spring应用上下文加载的时候创建的，而这时要注入的会话或请求作用域的Bean还不存在。而且，系统中将会有多个会话或请求作用域的Bean，而单例Bean只有一个。

Spring通过注入作用域代理，而不是直接注入会话或请求作用域Bean来解决这个问题。作用域代理会暴露与它代理的会话或请求作用域Bean相同的方法，并将调用委托给**当前会话或请求**所对应的那一个会话或请求作用域Bean。

![Scope Proxy](SpringCore/Scope-Proxy.png)

作用域代理有两种实现模式：

- 基于接口的JDK代理（目标Bean是个接口）；
- 基于具体类的CGLib代理（目标Bean是个具体类）。

作用域代理的实现模式用`@Scope`标注的`proxyMode`属性来设置：

- `proxyMode=ScopedProxyMode.INTERFACES`：基于接口的JDK代理；
- `proxyMode=ScopedProxyMode.TARGET_CLASS`：基于具体类的CGLib代理。

```java
@Bean
@Scope(value=WebApplicationContext.SCOPE_SESSION, 
       proxyMode=ScopedProxyMode.INTERFACES)
public ShoppingCart cart() {…}
```

`proxyMode`属性的默认值是`ScopedProxyMode.DEFAULT`，即不创建作用域代理。

在XML配置中，可以配置为：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:aop="http://www.springframework.org/schema/aop"
       xsi:schemaLocation="
         http://www.springframework.org/schema/aop
         http://www.springframework.org/schema/aop/spring-aop.xsd
         http://www.springframework.org/schema/beans
         http://www.springframework.org/schema/beans/spring-beans.xsd">
  <bean id="cart"
        class="com.myapp.ShoppingCart"
        scope="session" />
  <aop:scoped-proxy proxy-target-class="false" />
  </bean>
</beans>
```

`<aop:scoped-proxy>`的作用与`proxyMode`属性相同。

默认情况下，`proxy-target-class`的值是`true`，即使用CGLib创建目标类的代理。

## 应用上下文

Spring通过应用上下文（Application Context）装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。

常见的应用上下文实现：

- `AnnotationConfigApplicationContext`：从一个或多个基于Java的配置类中加载Spring应用上下文。
- `AnnotationConfigWebApplicationContext`：从一个或多个基于Java的配置类中加载Spring Web应用上下文。
- `ClassPathXmlApplicationContext`：从类路径下的一个或多个XML配置文件中加载上下文定义。
- `FileSystemXmlApplicationContext`：从文件系统下的一个或多个XML配置文件中加载上下文定义。
- `XmlWebApplicationContext`：从Web应用下的一个或多个XML配置文件中加载上下文定义。

### 应用上下文实例化

编程方式实例化：

```java
public static void main(String[] args) throws Exception {
	ApplicationContext context = new ClassPathXmlApplicationContext("services.xml", "daos.xml");
  // 获取Bean
  PetStoreService petStoreService = context.getBean("petStore", PetStoreService.class);
  // 使用petStoreService
List<String> userList = petStoreService.getUsernameList();
  context.close();
}
```

在Web应用中，可在`web.xml`中添加如下配置来自动实例化应用上下文：

```xml
<context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/daoContext.xml /WEB-INF/applicationContext.xml</param-value>
</context-param>

<listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
</listener>
```

上下文参数`contextConfigLocation`的默认值是`/WEB-INF/applicationContext.xml`。

上下文参数`contextConfigLocation`的值可以是逗号、分号或空白字符分隔的多个文件。还支持Ant样式的路径模式。例如：

- `/WEB-INF/*Context.xml`：匹配所有名称以`Context.xml`结尾且位于`WEB-INF`目录中的文件；
- `/WEB-INF/**/*Context.xml`：匹配`WEB-INF`的任何子目录中名称以`Context.xml`结尾的文件。

### 应用上下文事件



# 资源

## 使用外部属性文件

## 资源国际化

## 其他资源

### Resource接口

# 校验、数据绑定和类型转换



# SpEL

# AOP

DI能够让相互协作的软件组件保持松散耦合，而面向切面编程（aspect-oriented programming，AOP）允许你把遍布应用各处的功能分离出来形成**可重用**的组件。

可以把切面想象为覆盖在很多组件之上的一个外壳。应用是由那些实现各自业务功能的模块组成的。借助AOP，可以使用各种功能层去包裹核心业务层。这些层以声明的方式灵活地应用到系统中，你的核心应用甚至根本不知道它们的存在。

