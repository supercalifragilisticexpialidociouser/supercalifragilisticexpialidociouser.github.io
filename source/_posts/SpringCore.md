---
title: SpringCore
date: 2018-06-01 21:57:20
tags: [5.0.8]
---

# IoC

IoC（控制反转）也称为DI（依赖注入）。

在Spring中，构成应用程序主干并由Spring IoC容器管理的对象称为bean。 bean是一个由Spring IoC容器实例化，组装和管理的对象。除此之外，bean只是应用程序中众多对象之一。 Bean及其之间的依赖关系反映在容器使用的配置元数据中。

## 配置元数据

Spring 能过配置元数据（Configuration Metadata）来定义Bean。

配置元数据以XML，Java标注或Java代码等形式表示。

### 基于XML的元数据

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

# 校验

# SpEL

# AOP

DI能够让相互协作的软件组件保持松散耦合，而面向切面编程（aspect-oriented programming，AOP）允许你把遍布应用各处的功能分离出来形成可重用的组件。

可以把切面想象为覆盖在很多组件之上的一个外壳。应用是由那些实现各自业务功能的模块组成的。借助AOP，可以使用各种功能层去包裹核心业务层。这些层以声明的方式灵活地应用到系统中，你的核心应用甚至根本不知道它们的存在。

