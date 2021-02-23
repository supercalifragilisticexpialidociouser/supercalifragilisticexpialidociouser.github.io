---
title: Spring
date: 2021-02-01 11:08:44
tags:
---

这个文档记录Spring的最佳实践。

# 开发环境

必需：安装JDK 8 或更新版本，当前推荐使用11。

可选：VSCode + Java Extension Pack + Spring Boot Extension Pack。

# 起步

## 创建项目

直接使用VSCode的扩展生成 Spring Boot 项目（实际上就是使用Spring initializr）。

> 在使用Spring initializr生成项目时，`spring-boot-starter-test`总是被添加到项目，不需要显式选择它。

## 添加依赖

在VSCode中，在“pom.xml”上右击，出现“Add Starters...”菜单，可以调整项目依赖的Starters。

### 继承或导入Spring Boot 的 POM

#### 继承方式

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.4.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

#### 导入方式

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <!-- Import dependency management from Spring Boot -->
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>2.3.4.RELEASE</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

导入方式只会导入依赖管理，而不会导入插件管理。

`spring-boot-starter-parent` 中`spring-boot-maven-plugin`包含了 `<executions>`，并绑定到 `repackage`目标。如果采用导入方式，则需要自己配置`spring-boot-maven-plugin`。

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <executions>
        <execution>
            <goals>
                <goal>repackage</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <mainClass>${start-class}</mainClass>
        <executable>true</executable>
        <fork>true</fork>
        <!-- Enable the line below to have remote debugging of your application on port 5005
		<jvmArguments>-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005</jvmArguments>
		-->
    </configuration>
</plugin>
```

使用导入方式时，如果要覆盖spring boot中的依赖时，要将该依赖的定义放在`spring-boot-dependencies`之前。

### Spring Boot 插件

`spring-boot-maven-plugin`插件提供了一些重要的功能：

- 一个用来运行项目的Maven目标：`spring-boot:run`；
- 确保所有依赖库都会包含在可执行的JAR文件中；
- 会在JAR文件中生成一个manifest文件，将引导类声明为可执行JAR的主类。

## 引导应用

引导类是使用`@SpringBootApplication`标注的类，并且它有一个`main`方法。它是通过可执行JAR来运行应用所必需的。

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {

	public static void main(String[] args) {
        //启动应用，并将主组件和命令行参数传给它
		SpringApplication.run(DemoApplication.class, args);
	}

}
```

`@SpringBootApplication`相当于是下列标注的组合：

- `@Configuration`：应用程序配置类，用于替代传统基于XML的配置；

- `@EnableAutoConfiguration`：基于依赖关系，自动配置Spring；

- `@ComponentScan`：告诉Spring去哪里自动扫描其他组件、配置和服务，并将它们自动注册为Spring Beans。在默认情况下，这个标注将会扫描当前包以及其下任意深度的子包。如果没有使用这个标注，则必须使用`@Import`标注显式列出要扫描的组件：

  ```java
  @Configuration
  @EnableAutoConfiguration
  @Import({ MyConfig.class, MyAnotherConfig.class })
  public class Application {
  	public static void main(String[] args) {
      SpringApplication.run(Application.class, args);
  	}
  }
  ```

> 建议将启动类放在项目的最高层次包中（例如本例中的`com.example.demo`），这样Spring Boot默认从启动类开始自动搜索所在包及其下所有层次包中的类。
>
> 不建议将启动类放在默认包（即没有显式使用`package`声明）中，这样会导致扫描所有jar的所有类。

## 运行

运行项目（开发）：`./mvnw spring-boot:run`。

在生产环境中，仍是使用`java -jar demo-0.0.1-SNAPSHOT.jar`方式运行。

项目启动成功后，打开 http://localhost:8080。

可以按`Ctrl-C` 停止应用。

## 调试

直接找到引导类的`main`方法，右键点击，选择“Debug”。

如果需要远程调试，则可以以下面命令来运行应用：

```bash
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
       -jar target/demo-0.0.1-SNAPSHOT.jar
```

## 打包

如果使用Maven，则可以使用下列命令将项目打包：

```bash
$ mvnw clean package
```

> 需要将 `spring-boot-maven-plugin` 加入 pom.xml中。

打包后，会在`target`目录下生成两个文件，例如：`demo-0.0.1-SNAPSHOT.jar`和`demo-0.0.1-SNAPSHOT.jar.original`。前者是可执行的jar（包含您的已编译类以及代码需要运行的所有jar依赖），后者是未被Spring Boot重打包的原始JAR。

Spring Initializr默认将应用打包成一个可执行的jar包，并且内嵌了Tomcat、Jetty或Undertow支持。因此，不再需要单独下载这些Servlet容器。这主要是为了更方便云部署。

> 要解压这个可执行jar，可以使用命令：`jar xvf target/demo-0.0.1-SNAPSHOT.jar`。

当然，也可以将Spring Boot打包成传统的war包，部署到独立的Servlet容器中。只需在pom.xml中

1. 加上`<packaging>war</packaging>`；
2. 包含一个Web初始化类（通常扩展`SpringBootServletInitializer`类）。

另外，这个war既可部署到支持Servlet 3.0的容器中，也可以直接使用`java -jar`命令来运行。

# 配置

Spring 中有两种不同（但相关）的配置：

- bean 装配：声明在 Spring 应用上下文中创建哪些应用组件以及它们之间如何互相注入的配置。
- 属性注入：设置 Spring 应用上下文中 bean 的值的配置。

## 手动配置

Spring Boot偏爱基于Java的配置，而不是基于XML的配置。

标注有`@Configuration`的类就是Spring Boot的配置类。

不需要将所有配置全部集中在一个配置类中，可以将配置分散在多个配置类中。然后，通过`@ComponentScan`可以自动装配这些配置，也可以通过`@import`来自己指定要导入的其他配置类。

如果要导入基于XML的配置文件，则使用`@ImportResource`标注。

## 自动配置

不管是使用Java还是使用XML的显式配置，只有当Spring不能进行自动配置的时候才是必要的。

将`@EnableAutoConfiguration`标注在配置类上，允许Spring Boot根据依赖关系，自动配置Spring应用。例如，如果hsqldb的JAR在你的类路径上，则Spring Boot会自动配置这个JAR与Spring集成。

自动配置是非侵入的，在任何时候，你都可以用自己的配置覆盖自动配置。

如果希望知道自动配置都配置了什么，可以启用“debug”模式或“trace”模式。

使用命令行选项：

```bash
$ java -jar springTest.jar --debug
$ java -jar springTest.jar --trace
```

使用应用属性：

```yaml
debug: true
trace: true
```

> 启用“debug”模式或“trace”模式后，核心`Logger`（包含嵌入式容器、hibernate、spring）会输出`DEBUG`或`TRACE`级别内容，但是你**自己应用的日志并不会输出为`DEBUG`或`TRACE`级别**。 

`@EnableAutoConfiguration` 还可以禁止某些组件的自动配置：

```java
@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
}
```

如果要禁止自动配置的类不在类路径上，则要使用`excludeName` 代替`exclude`。

另外，也可以使用 `spring.autoconfigure.exclude`属性来配置禁止自动配置的组件。

## 应用属性

Spring 应用属性可以来自多个属性源：（顺序靠前的应用属性优先）

1. Devtools全局配置的属性（位于`~/.spring-boot-devtools.properties`）。

2. 在测试代码中，`@TestPropertySource`指定的应用属性。

3. 在测试代码中，由`@SpringBootTest`标注的`properties`设定的属性。

4. 通过命令行参数指定的应用属性。（例如：`java -jar app.jar --PROPERTY_NAME="PROPERTY_VALUE"`）

5. 由`SPRING_APPLICATION_JSON`环境变量或`spring.application.json` 系统属性指定应用属性。
   例如，可通过如下方式设置`spring.application.json` 系统属性：

   ```bash
   $ java -Dspring.application.json='{"acme":{"name":"test"}}' -jar myapp.jar
   $ java -jar myapp.jar --spring.application.json='{"acme":{"name":"test"}}'
   ```

   这设置了应用属性`acme.name`的值为`test`。
   在UN*X shell中，环境变量也可以通过命令行来指定：

   ```bash
   $ SPRING_APPLICATION_JSON='{"acme":{"name":"test"}}' java -jar myapp.jar
   ```

6. 通过`ServletConfig` 初始参数设定的应用属性。

7. 通过`ServletContext` 初始参数设定的应用属性。

8. 来自`java:comp/env`的JNDI属性设定的应用属性。（例如：`java:comp/env/spring.application.json`、`java:comp/env/acme.name`等）

9. 通过Java系统属性（可以通过`System.getProperties()`获得）设定的应用属性。

10. 通过操作系统环境变量设定的应用属性。

11. 通过`random.*`配置的随机属性。（由`RandomValuePropertySource` 产生）
    例如：

    ```properties
    my.secret=${random.value}  #随机字符串
    my.number=${random.int}  #随机整数
    my.bignumber=${random.long}  #随机长整数
    my.uuid=${random.uuid}  #随机UUID
    my.number.less.than.ten=${random.int(10)}  #10以内（包含）的随机整数
    my.number.in.range=${random.int[1024,65536]}  #[1024..65536)之间的随机整数
    ```

    randon.int的语法格式：

    ```
    random.int OPEN VALUE [, MAX] CLOSE
    ```

    `OPEN`和`CLOSE`可以是任意字符，`VALUE`和`MAX`是整数。如果存在`MAX`，则`VALUE`是最小值（包含），`MAX`是最大值（不包含）。

12. 位于当前应用Jar包之外，针对不同PROFILE环境的应用属性文件（application-PROFILE.properties或application-PROFILE.yml）中指定的应用属性。

13. 位于当前应用Jar包之内，针对不同PROFILE环境的应用属性文件（application-PROFILE.properties或application-PROFILE.yml）中指定的应用属性。

14. 位于当前应用Jar包之外的应用属性文件（application.properties或application.yml）。

15. 位于当前应用Jar包之内的应用属性文件（application.properties或application.yml）。

16. 在`@Configuration`标注的类中，通过`@PropertySource`加载的应用属性。

17. 应用默认属性（通过`SpringApplication.setDefaultProperties`设定）。

> 另外，properties属性文件优先级高于相应的YAML文件。

Spring 的`Environment`负责从各个属性源拉取属性，并让 Spring 应用上下文中的 bean 可以使用它们。



# Web

Spring包含两个Web框架，它们可以混用，也可以单独使用：

- Spring Web MVC：基于Servlet API和Servlet容器，只能在Servlet容器上运行。
- Spring WebFlux：基于非堵塞的反应式Web框架，可在Netty、Undertow和Servlet 3.1+容器上运行。

> 传统的基于容器的应用是由Servlet容器根据`web.xml`配置来启动应用，并加载应用上下文配置。而Spring Boot应用则是根据Spring配置来启动自己以及内嵌的Servlet容器。

需要依赖：

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

## 内嵌Web服务器

对于Servlet栈的Spring Boot应用，`spring-boot-starter-web`默认内嵌Tomcat。另外，也可内嵌Jetty、Undertow。

对于反应栈（reactive）的Spring Boot应用， `spring-boot-starter-webflux`默认内嵌Netty。还可以内嵌Tomcat、Jetty、Undertow。

假设使用Undertow服务器：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <!-- Exclude the Tomcat dependency -->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!-- Use Jetty instead -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-undertow</artifactId>
</dependency>
```

## 处理错误

### 服务端错误页

> 前端的错误页在前端项目中定义，这里只讨论服务端的错误页。

#### 静态错误页

在Nginx等代理服务器上配置，用于服务端应用无法访问时显示的错误页面。

#### 动态错误页

使用[Thymeleaf](https://www.thymeleaf.org/)模板定义，位于`src/main/resources/templates/error.html`。用于直接访问服务端应用（即绕开前端）时，显示服务端错误。

### API错误

使用 [Zalando’s Problem Spring Web library](https://github.com/zalando/problem-spring-web)框架处理Spring MVC REST响应错误（基于JSON）。