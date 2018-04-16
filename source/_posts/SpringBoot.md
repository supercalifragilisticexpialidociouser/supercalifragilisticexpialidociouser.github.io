---
title: SpringBoot
date: 2018-04-16 09:39:49
tags: [2.0.1]
---

Spring Boot简化了Spring应用的开发，不需要什么配置就可以运行Spring应用。

Spring Boot 要求 Java 8及以上版本。

Spring Boot可以直接将应用打包成一个可执行的jar，并且内嵌了Tomcat、Jetty和Undertow。因此，不再需要单独下载这些Servlet容器。

# 入门

## 创建项目

### POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<packaging>jar</packaging>

	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<parent>
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
		<dependency><!-- Web支持 -->
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
		</dependency>
	</dependencies>

	<build>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
			</plugin>
		</plugins>
	</build>
</project>
```

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

使用导入方式时，如果要覆盖spring boot中的依赖时，要将该依赖的定义放在`spring-boot-dependencies`之前。

### 启动类

启动类使得项目可以打包成可执行的Jar，并使用内嵌的Servlet容器（默认是Tomcat）来作为HTTP运行时。而不是打包成war，部署到外部Servlet容器中。

```java
package com.example.demo;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class DemoApplication {
	public static void main(String[] args) {
		SpringApplication.run(DemoApplication.class, args);
	}
}
```

> 建议将启动类放在项目的最高层次包中（例如本例中的`com.example.demo`），这样Spring Boot默认从启动类开始自动搜索其下所有层次的类。
>
> @SpringBootApplication相当于是下列标注的组合：
>
> - @Configuration：应用程序上下文配置类；
> - @EnableAutoConfiguration：自动配置；
> - @EnableWebMvc：通常不需要该标注。因为，只要类路径中包含Spring MVC，就会自动启用，并自动安装`DispatcherServlet`；
> - @ComponentScan：告诉Spring去扫描其他组件、配置和服务。

### 创建简单的控制器

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

在STS中，直接右击启动类，选择以`Java Application`方式或者`Spring Boot App`方式运行。

也可以手工打包成可执行Jar，然后以`java -jar`方式运行。例如：

```bash
$ java -jar target/demo-0.0.1-SNAPSHOT.jar
```

还可以使用Spring Boot插件来运行：

```bash
$ mvn spring-boot:run
```

然后，打开浏览器，访问：<http://localhost:8080/say>，就可以看到结果了。

可以按`Ctrl-C`关闭应用。

## 调试

与调试普通`Java Application`一样。

## 使用热部署

Spring Boot 提供了 spring-boot-devtools，它能在修改类或者配置文件（包括pom.xml）时，自动重新加载Spring Boot项目。这样就避免了手工重启项目。

要使用热部署只需要在`pom.xml`中添加如下依赖：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-devtools</artifactId>
  <optional>true</optional>
</dependency>
```

## 打包

如果使用Maven，则可以使用下列命令将项目打包成Jar：

```bash
$ mvn clean package
```

打包后，会在`target`目录下生成两个文件，例如：`demo-0.0.1-SNAPSHOT.jar`和`demo-0.0.1-SNAPSHOT.jar.original`。前者是可执行的jar。

## 常用工具

[Spring Initializr](https://start.spring.io/)：一个可以生成Spring Boot项目的在线生成器。

Spring Tool Suite（STS）：一个基于Eclipse的IDE，可以很方便地开发Spring Boot项目。

Spring Boot CLI：是Spring Boot 命令行工具，通过它也可以很方便地开发Spring Boot项目。

# Spring Boot CLI

下载并解压就可以使用了。可以将`bin`目录加到`PATH`环境变量中。

查看版本：`spring --version`。

Spring Boot CLI在`Bash`和`Zsh` Shell中，可以支持自动补全，只要按`Tab`键。

# MVC

## 引入依赖

要在Spring Boot中集成Spring MVC框架，只需要加入下面依赖：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

## Web应用的目录结构

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

## 控制器

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

### URL映射

`@RequestMapping`的属性：

- value：请求的URL路径，支持URL模板、正则表达式；
- method：HTTP请求方法，有GET、POST等；
- consumes：接受的媒体类型。对应HTTP的`Content-Type`；
- produces：响应的媒体类型。对应HTTP的`Accept`；
- params：请求参数；
- headers：请求的HTTP头。

#### 路径参数

```java
@RequestMapping(value="/get/{id}.json")
public @ResponseBody User getById(@PathVariable("id") Long id) {
  …
}
```

则访问路径是`/get/1.json`，时将调用`getById`方法，并且参数`id`的值为`1`。

#### Ant路径表达式

`*`匹配任意多个字符（除了路径分隔符”/“），`**`匹配任意路径，`?`匹配单个字符。

```java
@RequestMapping("/user/*.html")  //匹配：/user/1.html、/user/abc.html，但不匹配：/user/add/1.html
@RequestMapping("/**/1.html")  //匹配：/1.html、/user/1.html、/user/add/1.html
@RequestMapping("/user/?.html")  //匹配：/user/1.html，但不匹配：/user/abc.html
```

如果一个请求有多个`@RequestMapping`能够匹配，则更具体的匹配优先。另外，有通配符的低于无通配符的，有`**`的低于有`*`的。

#### 插值

在URL映射中，可以使用`${…}`的插值，来获取系统配置或环境变量：

```java
@RequestMapping("/${query.all}.json")
```

#### 请求方法

