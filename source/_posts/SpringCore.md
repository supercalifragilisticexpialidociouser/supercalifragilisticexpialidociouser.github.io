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
  - `AnnotationConfigApplicationContext`：从一个或多个基于Java的配置类中加载Spring应用上下文。
  - `AnnotationConfigWebApplicationContext`：从一个或多个基于Java的配置类中加载Spring Web应用上下文。
  - `ClassPathXmlApplicationContext`：从类路径下的一个或多个XML配置文件中加载上下文定义。
  - `FileSystemXmlApplicationContext`：从文件系统下的一个或多个XML配置文件中加载上下文定义。
  - `XmlWebApplicationContext`：从Web应用下的一个或多个XML配置文件中加载上下文定义。

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

## 配置元数据

Spring 能过配置元数据（Configuration Metadata）来定义Bean。

### 配置方式

Spring提供了三种配置元数据的方法：

- 在XML中进行显式配置；
- 在Java中进行显式配置；
- 隐式的Bean发现机制和自动装配。

这些配置方式可以任意互相搭配，比如：可以选择使用XML装配一些Bean，使用基于Java的配置来装配另一些Bean，而将剩余的Bean让Spring去自动发现。

XML和基于Java的配置更集中，且不会对代码造成侵入。自动装配和基于Java的配置具有类型安全等好处。

通常建议尽可能地使用自动配置机制，当必须要显式配置Bean的时候（比如，有些源码不是由你来维护的，而当你需要为这些代码配置Bean时），推荐使用类型安全并且比XML更加强大的基于Java的配置。最后，只有当你想要使用便利的XML命名空间，并且在基于Java的配置中没有同样的实现时，才应该使用XML的配置。

#### 自动化装配

##### 创建可被发现的Bean

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

##### 启用组件扫描

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

`@ComponentScan`标注默认会扫描与配置类相同的包以及其下的所有子包，查找带有`@Component`标注的类。

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



#### 基于Java的装配

#### 基于XML的装配

services.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="petStore" class="org.springframework.samples.jpetstore.services.PetStoreServiceImpl">
        <property name="accountDao" ref="accountDao"/>
        <property name="itemDao" ref="itemDao"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for services go here -->

</beans>
```

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

    <bean id="itemDao" class="org.springframework.samples.jpetstore.dao.jpa.JpaItemDao">
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for data access objects go here -->

</beans>
```

### 定义Bean

#### Bean的命名

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

## 装载Bean

Spring通过应用上下文（Application Context）装载bean的定义并把它们组装起来。Spring应用上下文全权负责对象的创建和组装。

实例化Spring IoC容器：

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

常见的应用上下文实现：

- AnnotationConfigApplicationContext：从一个或多个基于Java的配置类中加载Spring应用上下文。
- AnnotationConfigWebApplicationContext：从一个或多个基于Java的配置类中加载Spring Web应用上下文。
- ClassPathXmlApplicationContext：从类路径下的一个或多个XML配置文件中加载上下文定义，把应用上下文的定义文件作为类资源。
- FileSystemXmlapplicationcontext：从文件系统下的一个或多个XML配置文件中加载上下文定义。
- XmlWebApplicationContext：从Web应用下的一个或多个XML配置文件中加载上下文定义。

## 导入

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

这样实例化XML配置元数据时，只需要传入这个XML配置文件即可：

```java
ApplicationContext context = new ClassPathXmlApplicationContext("app.xml");
```



`@Resource`标注是属于Java EE标准的，它默认按照名称进行装配，名称可以通过`name`属性进行指定。如果没有指定`name`属性，当标注写在字段上时，就默认取字段名进行查找；如果标注写在setter方法上，就默认取属性名进行装配。当找不到与名称匹配的bean时，才按照类型进行装配。但需要注意的是，`name`一旦指定，就只会按照名称进行装配。例如：

```java
@Resource(name="userRepository")
private UserRepository userRepository;
```

`@Autowired`标注属于Spring，默认按类型装配。默认情况下，要求注入对象必须存在，如果要允许`null`值，则要设置它的`required`属性为`false`，例如`@Autowired(required=false)`。可以借助`@Qualifier`标注实现按名称装配：

```java
@Autowired
@Qualifier("userRepository")
private UserRepository userRepository;
```

# 资源

# 校验、数据绑定和类型转换



# SpEL

# AOP

DI能够让相互协作的软件组件保持松散耦合，而面向切面编程（aspect-oriented programming，AOP）允许你把遍布应用各处的功能分离出来形成**可重用**的组件。

可以把切面想象为覆盖在很多组件之上的一个外壳。应用是由那些实现各自业务功能的模块组成的。借助AOP，可以使用各种功能层去包裹核心业务层。这些层以声明的方式灵活地应用到系统中，你的核心应用甚至根本不知道它们的存在。

