---
title: SpringBoot
date: 2018-04-16 09:39:49
tags: [2.0.2]
---

# 简介

Spring Boot简化了Spring应用的开发，不需要什么配置就可以运行Spring应用。

Spring Boot项目可以直接被打包成一个可执行的jar包，并且内嵌了Tomcat、Jetty和Undertow支持。因此，不再需要单独下载这些Servlet容器。当然，也可以打包成传统的war包。

<!--more-->

## 系统要求

Spring Boot 2 要求 Java 8及以上版本。

## 常用工具

[Spring Initializr](https://start.spring.io/)：一个Spring Boot项目的在线生成器。

Spring Tool Suite（STS）：一个基于Eclipse的IDE，可以很方便地开发Spring Boot项目。

Spring Boot CLI：是Spring Boot 命令行工具，它可以运行Groovy脚本，通过它也可以很方便地开发Spring Boot项目。

构建工具：Maven、Gradle或Ant。

> 使用[Spring Initializr](https://start.spring.io/)或者STS生成的项目中，已经带有一个[Maven Wrapper](https://github.com/takari/maven-wrapper) （即项目根目录下的`mvnw`和`mvnw.cmd`文件）或 [Gradle Wrapper](http://gradle.org/docs/current/userguide/gradle_wrapper.html) 。Wrapper的用法与原来的Maven或Gradle一样。例如：原来Maven的`mvn clean install`，在Wrapper中则是`./mvnw clean install`。
>
> Wrapper的主要作用是保证所有构建这个项目的人，都使用同一版本的Maven或Gradle。当执行Wrapper命令时，如果发现当前用户的Maven或Gradle版本和期望的版本不一致，那么就下载期望的版本，然后用期望的版本来执行命令。 
>
> 在开发Spring Boot项目时，Wrapper不是必需使用的。完全可以使用原来的Maven或Gradle命令。

[JHipster](https://www.jhipster.tech/)：是一个生成、开发和部署Spring Boot + Angular/React的Web应用和Spring微服务的开发平台。 

# 入门

## 创建项目

创建一个普通的Java项目。

> 创建Spring Boot项目更方便的方式是使用STS IDE或者[Spring Initializr](https://start.spring.io/)在线生成项目。
>
> [Spring Initializr](https://start.spring.io/)除了在线使用外，也可以在命令行，通过`curl https://start.spring.io/...`命令来使用：
>
> ```bash
> $ curl https://start.spring.io/starter.zip\?name\=demo\&groupId\=com.example\&artifactId\=deroject+for+Spring+Boot\&packageName\=com.example.demo\&type\=maven-project\&packaging\=jar\&.0.2.RELEASE\&dependencies\=web --output demo.zip
> ```
>
> 

### 添加Spring Boot依赖

这里使用Maven来管理项目依赖。

`pom.xml`：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging><!-- 打包成jar包 -->

	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<parent><!-- 继承Spring Boot的parent POM -->
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.1.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>

	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
		<java.version>1.8</java.version>
	</properties>

	<dependencies>
		<dependency><!-- Web应用依赖，包括Spring MVC和Spring自己的Restful支持 -->
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency><!-- 测试依赖 -->
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin><!-- Spring Boot 插件。创建可执行JAR必需 -->
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

Spring Boot提供了大量的“Starters”，实得添加相关特性到项目变得非常容易。这里我们添加了两个“Starters”，分别用于提供开发Web应用支持和测试支持。

`spring-boot-starter-parent`也可以通过导入方式来使用，而不是继承Parent POM方式：

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <!-- Import dependency management from Spring Boot -->
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-dependencies</artifactId>
      <version>2.0.1.RELEASE</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

导入方式只会导入依赖管理，而不会导入插件管理。

> `spring-boot-starter-parent` 中`spring-boot-maven-plugin`包含了 `<executions>`，并绑定到 `repackage`目标。如果采用导入方式，则需要自己配置`spring-boot-maven-plugin`。

使用导入方式时，如果要覆盖spring boot中的依赖时，要将该依赖的定义放在`spring-boot-dependencies`之前。

### 启动类

启动类是使用`@SpringBootApplication`标注的类，并且它有一个`main`方法。

启动类使得项目可以打包成可执行的Jar，并使用内嵌的Servlet容器（默认是Tomcat）来作为HTTP运行时。而不是打包成war，部署到外部Servlet容器中。

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args); //启动应用，并将主组件和命令行参数传给它
	}
}
```

> 建议将启动类放在项目的最高层次包中（例如本例中的`com.example.demo`），这样Spring Boot默认从启动类开始自动搜索所在包及其下所有层次包中的类。
>
> 不建议将启动类放在默认包（即没有显式使用`package`声明）中，这样会导致扫描所有jar的所有类。
>
> `@SpringBootApplication`相当于是下列标注的组合：
>
> - `@Configuration`：应用程序配置类，用于替代传统基于XML的配置；
>
> - `@EnableAutoConfiguration`：基于依赖关系，自动配置Spring；
>
> - `@ComponentScan`：告诉Spring去哪里自动扫描其他组件、配置和服务，并将它们自动注册为Spring Beans。在默认情况下，这个标注将会扫描当前包以及其下任意深度的子包。如果没有使得这个标注，则必须使用`@Import`标注显式列出要扫描的组件：
>
>   ```java
>   @Configuration
>   @EnableAutoConfiguration
>   @Import({ MyConfig.class, MyAnotherConfig.class })
>   public class Application {
>   	public static void main(String[] args) {
>       SpringApplication.run(Application.class, args);
>   	}
>   }
>   ```

## 编码

这里编写个简单的控制器：

```java
package com.example.demo.hello;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

@Controller
public class HelloController {
	@RequestMapping("/say")
	@ResponseBody
	public String say() {
		return "Hello world!";
	}
}
```

> @ResponseBody 表示此方法返回的是文本而不是视图名称。

## 运行

在STS中，如果打包类型是jar，则可以直接右击启动类，选择以`Java Application`方式或者`Spring Boot App`方式运行。而不需要借助任何IDE特定的插件或扩展。

也可以使用Spring Boot插件来运行：

```bash
$ mvn spring-boot:run
```

在生产环境中，通常是打包成可执行Jar，然后以`java -jar`方式运行。例如：

```bash
$ java -jar target/demo-0.0.1-SNAPSHOT.jar
```

应用成功启动后，打开浏览器，访问：<http://localhost:8080/say>，就可以看到结果了。

可以按`Ctrl-C`关闭应用。

> 有时运行一个Web应用两次会出现端口占用情况，如果使得STS，可以使用它的`Relaunch`按钮来运行应用，而不是使用`Run`按钮来运行。`Relaunch`可以确保在运行应用之前，任何已经运行的实例被关闭。

## 调试

与调试普通`Java Application`一样。

### 远程调试

首先，在远程服务器上，以调试模式运行应用。例如：

```bash
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
       -jar target/demo-0.0.1-SNAPSHOT.jar
```

然后，在Eclipse中新建`Remote Java Application`，`Host`处输入远程服务器的IP，`Port`处输入上面的`8000`。

最后，象正常那样设置断点，点`Debug`按钮进行调试。

> 远程服务器上的应用应该与Eclipse中的项目基于完全相同的代码。



## 打包

如果使用Maven，则可以使用下列命令将项目打包成Jar：

```bash
$ mvn clean package
```

打包后，会在`target`目录下生成两个文件，例如：`demo-0.0.1-SNAPSHOT.jar`和`demo-0.0.1-SNAPSHOT.jar.original`。前者是可执行的jar，后者是未被Spring Boot重打包的原始JAR。

# 热部署

Spring Boot 提供了 spring-boot-devtools，它能在类路径中的任何文件（例如：类或者配置文件，包括pom.xml）被修改时，自动**热重启**Spring Boot项目，并触发浏览器刷新（即**热加载**，Live Reload）。

要使用热部署只需要在`pom.xml`中添加如下依赖：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-devtools</artifactId>
  <optional>true</optional><!--避免传递依赖到其他模块 -->
</dependency>
```

> devtools只适用于通过IDE或通过`mvn spring-boot:run`方式运行的情况。如果是通过`java -jar`方式运行可执行JAR时，则devtools自动被移除。也就是说，在打包生成的可执行JAR中将不会包含devtools。
>
> 如果需要热拔插，可以使用 [JRebel](https://zeroturnaround.com/software/jrebel/)。当使用JRebel时，热重启将被禁用，然而热加载仍可用。

Spring Boot支持的许多库使用缓存来改善性能。例如，模板引擎。然而，在开发阶段，缓存也会带来麻烦，例如它会阻止我们看到最新的改变。因此，devtools默认禁用缓存。

## 热重启

Spring Boot使用两个类加载器来实现热重启机制。不会改变的资源（例如第三方库）被加载进基础类加载器，而项目中正在开发的资源被加载到重启类加载器中。当热重启时，只有重启类加载器被抛弃，并重新创建一个新的重启类加载器。而基础类加载器保持不变。

如果需要指定哪些资源要加载到基础类加载器，哪些要加载到重启加载器，则需要创建一个属性文件：`META-INF/spring-devtools.properties` 。在这个属性文件中，以`restart.exclude` 为前缀的属性指定的资源是要被加载到基础类加载器中的，而以`restart.include`为前缀的属性指定的资源是则要被加载到重启类加载器中的：

```properties
restart.exclude.companycommonlibs=/mycorp-common-[\\w-]+\.jar
restart.include.projectcommon=/mycorp-myproj-[\\w-]+\.jar
```

所有类路径上（包括第三方Jar）的`META-INF/spring-devtools.properties` 都会被加载。

### 不需要重启的资源

有些资源，例如静态资源、视图模板等的修改，是不需要重启应用的。默认情况下，在`/META-INF/maven`、 `/META-INF/resources`、 `/resources`、 `/static`、`/public`或 `/templates`中的资源发生改变时，不会触发应用热重启，只会触发一个热加载。

也可以通过 `spring.devtools.restart.exclude` 来定制哪些资源发生改变时不需要热重启。例如，只有 `/static`、`/public`中的资源不需要热重启：

```properties
spring.devtools.restart.exclude=static/**,public/**
```

如果你希望保持默认情况，同时添加一些额外的不需要热重启，则需要使用 `spring.devtools.restart.additional-exclude`属性来配置。

### 监听非类路径上的资源

默认情况下，devtools只监听类路径上的资源的修改，并依此做出热重启决定。可以使用`spring.devtools.restart.additional-paths` 属性，来添加一些不在类路径上的路径，从而使得devtools可以监听这些路径上的资源的变化。

### 禁用重启

在`application.properties` 上配置 `spring.devtools.restart.enabled`属性为`false`，只会禁止devtools监听资源，但仍会初始化重启类加载器。

如果要完全禁用热重启功能，则必须在`SpringApplication.run(…)`调用之前，将系统属性`spring.devtools.restart.enabled` 设置为`false`：

```java
public static void main(String[] args) {
	System.setProperty("spring.devtools.restart.enabled", "false");
	SpringApplication.run(MyApp.class, args);
}
```

### 使用触发文件

如果使用IDE，会持续编译被修改的文件，你可能想只在特定的时间触发应用热重启。这时，可以使用触发文件。可以使用`spring.devtools.restart.trigger-file` 指定一个文件为触发文件，只有当这个文件发生修改时，才会触发应用热重启。

触发文件可以手工更新，也可以通过IDE插件来自动更新。

> 你可以将`spring.devtools.restart.trigger-file` 配置在全局配置中，这样，所有项目都有相同行为。

## 热加载

 `spring-boot-devtools`包含了一个内嵌的热加载服务器，它在监听的资源发生变化时，能触发浏览器进行刷新。

要禁用热加载，可以将`spring.devtools.livereload.enabled`属性设置为`false`。

> 一次只能运行一个热加载服务器。

## devtools的全局配置

devtools的全局配置文件位于：`~/.spring-boot-devtools.properties`。可以在全局配置文件中，配置一些本机上所有项目都适用的配置：

```properties
spring.devtools.reload.trigger-file=.reloadtrigger
```

## 远程支持

devtools不仅支持本地应用，也支持远程应用。

# 配置

Spring Boot偏爱基于Java的配置，而不是基于XML的配置。

标注有`@Configuration`的类就是Spring Boot的配置类。

不需要将所有配置全部集中在一个配置类中，可以将配置分散在多个配置类中。然后，通过`@ComponentScan`可以自动装配这些配置，也可以通过`@import`来自己指定要导入的其他配置类。

如果要导入基于XML的配置文件，则使用`@ImportResource`标注。

## 自动配置

将`@EnableAutoConfiguration`标注在配置类上，允许Spring Boot根据依赖关系，自动配置Spring应用。例如，如果hsqldb的JAR在你的类路径上，则Spring Boot会自动配置这个JAR与Spring集成。

自动配置是非侵入的，在任何时候，你都可以用自己的配置覆盖自动配置。

如果希望知道自动配置都配置了什么，可以带上`--debug`选项来启动应用。

`@EnableAutoConfiguration` 还可以禁止某些组件的自动配置：

```java
@Configuration
@EnableAutoConfiguration(exclude={DataSourceAutoConfiguration.class})
public class MyConfiguration {
}
```

如果要禁止自动配置的类不在类路径上，则要使用`excludeName` 代替`exclude`。

另外，也可以使用 `spring.autoconfigure.exclude`来配置禁止自动配置的组件。



> 由于`application.properties` 或 `application.yml`配置文件（也包括`application-xxx.properties`和`application-xxx.yml`）支持Spring风格的插值表达式（${…}），因此Maven filtering的插值表达式被改成使用`@…@`。可以通过`resource.delimiter`属性来自定义Maven插值表达式的定界符。



# SpringApplication

`SpringApplication`类提供了方便的方式来管理Spring Boot应用的生命周期。

## 启动应用

通常Spring Boot应用的`main`方法，会将启动应用的工作委托给`SpringApplication.run` 方法。例如：

```java
public static void main(String[] args) {
	SpringApplication.run(MySpringConfiguration.class, args);
}
```

### FailureAnalyzers

如果应用启动失败，可以注册一个`FailureAnalyzers`实现，以提供一个专用错误日志和一个修复问题的具体行为。

## 定制应用

## Web环境

`SpringApplication`会根据依赖关系自动创建合适的`ApplicationContext`：

- 如果类路径中存在Spring MVC，则使用`AnnotationConfigServletWebServerApplicationContext` ；
- 如果类路径中存在Spring WebFlux，则使用 `AnnotationConfigReactiveWebApplicationContext`；
- 否则，使用`AnnotationConfigApplicationContext` 。

也可以通过调用`setWebApplicationType(WebApplicationType)`方法来显式指定Web应用的类型，或者通过调用`setApplicationContextClass(…)`来对`ApplicationContext` 类型进行完全控制。

> 在JUnit单元测试中使用`SpringApplication` 时，经常这样调用：`setWebApplicationType(WebApplicationType.NONE)` 。

## 访问应用参数

可以通过注入一个`org.springframework.boot.ApplicationArguments` bean的方式，来访问传递给`SpringApplication.run(…)`的参数。

 `ApplicationArguments`接口既提供了原始的`String[]`参数，也可提供经过解析的option和non-option参数：

```java
@Component
public class MyBean {
	@Autowired
	public MyBean(ApplicationArguments args) {
		boolean debug = args.containsOption("debug");
		List<String> files = args.getNonOptionArgs();
		// if run with "--debug logfile.txt": debug=true, files=["logfile.txt"]
	}
}
```

Spring Boot也注册了一个 `CommandLinePropertySource`，使得可以将应用参数通过`@Value`注入字段。

## 应用事件和监听器

## ApplicationRunner和CommandLineRunner

如果需要在`SpringApplication.run`方法执行完成后执行一些特定代码，可以实现 `ApplicationRunner` 或 `CommandLineRunner` 接口。它们作用是一样的，区别在于提供应用参数的方式不同：

`CommandLineRunner` 以字符串数组的方式提供应用参数，而 `ApplicationRunner`以`ApplicationArguments`方式提供应用参数。

```java
@Component
public class MyBean implements CommandLineRunner {
	public void run(String... args) {
		// Do something...
	}
}
```

可以实现多个 `CommandLineRunner` 或 `ApplicationRunner` beans。如果要指定这些Beans的执行顺序，则可以实现`org.springframework.core.Ordered` 接口，或者使用`org.springframework.core.annotation.Order` 标注。

## 退出应用

## 启用管理特性

# Spring Boot CLI

## 安装

下载并解压就可以使用了。可以将`bin`目录加到`PATH`环境变量中。

查看版本：`spring --version`。

Spring Boot CLI在`Bash`和`Zsh` Shell中，可以支持自动补全，只要按`Tab`键。

# 日志集成

# Web集成

## MVC集成

### 引入依赖

要在Spring Boot中集成Spring MVC框架，只需要加入下面依赖：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

### Web应用的目录结构

视图模板文件默认位于`resources/templates`目录下。例如：

```java
@RequestMapping("/foo")
public String foo() {
  return "/admin/foo.btl";
}
```

上面代码中视图将会定位到`templates/admin/foo.ftl`。

视图模板文件中引用的静态资源文件，将默认放在`resources/static`目录下。假如，`foo.ftl`中有如下引用：

```html
<link href="/css/ztree.css" rel="stylesheet"/>
```

则Spring Boot将会定位到`static/css/ztree.css`。

### 控制器

Spring MVC的控制器不需要继承任何类或接口，只需要标注上`@Controller`即可。并且，使用`@RequestMapping`将HTTP请求映射到指定的方法。

`@RequestMapping`既可只作用在方法上，也可以同时作用在方法和类上：

```java
@Controller
@RequestMapping("/foo")
public class FooController {
  @RequestMapping("/bar")
  public String bar(Model model) {
    model.addAttribute("name", "Mary");
    return "/bar.ftl";
  }
}
```

则对`/foo/bar`的请求，将交由`FooController.bar`方法处理。

控制器方法返回值默认是视图的名称。如果希望返回内容本身，而不是视图名称，则需要在方法上加上`@ResponseBody`。这时，如果返回值是字符串类型，则直接返回这个字符串；否则，默认使用Jackson将返回值序列化为JSON字符串后输出。

#### URL映射

`@RequestMapping`的属性：

- value：请求的URL路径，支持URL模板、正则表达式；
- method：HTTP请求方法，有GET、POST等；
- consumes：接受的媒体类型。对应HTTP的`Content-Type`；
- produces：响应的媒体类型。对应HTTP的`Accept`；
- params：请求参数；
- headers：请求的HTTP头。

##### 路径参数

```java
@RequestMapping(value="/get/{id}.json")
public @ResponseBody User getById(@PathVariable("id") Long id) {
  …
}
```

则访问路径是`/get/1.json`，时将调用`getById`方法，并且参数`id`的值为`1`。

##### Ant路径表达式

`*`匹配任意多个字符（除了路径分隔符”/“），`**`匹配任意路径，`?`匹配单个字符。

```java
@RequestMapping("/user/*.html")  //匹配：/user/1.html、/user/abc.html，但不匹配：/user/add/1.html
@RequestMapping("/**/1.html")  //匹配：/1.html、/user/1.html、/user/add/1.html
@RequestMapping("/user/?.html")  //匹配：/user/1.html，但不匹配：/user/abc.html
```

如果一个请求有多个`@RequestMapping`能够匹配，则更具体的匹配优先。另外，有通配符的低于无通配符的，有`**`的低于有`*`的。

##### 插值

在URL映射中，可以使用`${…}`的插值，来获取系统配置或环境变量：

```java
@RequestMapping("/${query.all}.json")
```

##### 请求方法

## WebFlux集成

## Jersey（JAX-RS）集成

## 内嵌Sevlet容器

# 安全集成

# 关系数据库集成

# NoSQL集成

# 缓存集成

# 消息系统集成