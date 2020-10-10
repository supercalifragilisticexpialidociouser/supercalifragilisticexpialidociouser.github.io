---
title: Spring
date: 2020-09-21 21:46:48
tags: [5.2.9, 2.3.4]
---

# 简介

应用程序是由许多组件组成的。Spring的核心是提供了一个容器，通常称为Spring应用上下文，它们会创建和管理应用组件。这些组件也可以称为bean，会在Spring应用上下文中装配在一起，从而形成一个完整的应用程序。

指导Spring应用上下文将bean装配在一起的是Spring配置：自动配置、基于Java的配置和基于XML的配置。应该尽可能使用自动配置，只有在必要时才使用基于Java或基于XML的显式配置。

Spring Boot是Spring框架的扩展，提供了很多增强生产效率的方法，极大地改善了Spring的开发。

<!--more-->

# 起步

## 创建项目

创建Spring Boot项目，最常用的方式是使用Spring initializr。具体方式如下：

- 通过 https://start.spring.io/ 在线生成项目。

- 在命令行中使用`curl`命令，例如：

  ```bash
  $ curl https://start.spring.io/starter.zip \
         -d groupId=com.example \
         -d artifactId=demo \
         -d name=demo \
         -d packageName=com.example.demo \
         -d dependencies=web,data-jpa,devtools \
         -d javaVersion=11 \
         -o demo.zip （如果希望生成并解压到my-dir目录，则将这个选项改成 -d baseDir=my-dir | tar -xzvf -）
  ```

  参数选项的说明参见：https://github.com/spring-io/initializr/ 或者使用命令`curl https://start.spring.io`获取选项说明和依赖项列表。

  > 注：选项`jvmVersion`与`javaVersion`是等价的。

- 在命令行中使用Spring Boot CLI：是Spring Boot 命令行工具，它可以运行Groovy脚本，通过它也可以很方便地开发Spring Boot项目。
  使用[sdkman](https://sdkman.io)安装Spring Boot CLI：`sdk install springboot`。
  使用Spring Boot CLI生成项目：

  ```bash
  $ spring init --group-id=com.example \
                --artifact-id=demo \
                --name=demo \
                --package-name=com.example.demo \
                --dependencies=web,data-jpa,devtools \
                --javaVersion=11 \
  ```

  可以通过`spring init --list`命令来获取选项列表和依赖项列表。

- 使用 VSCode + Java Extension Pack + Spring Boot Extension Pack：轻量级开发环境。（本文使用的开发环境）

- 使用Spring Tool Suite（STS）：一个基于Eclipse的IDE，可以很方便地开发Spring Boot项目。

- 使用IntelliJ IDEA。

另外，也可以使用下列工具来创建Spring应用：

- [JHipster](https://www.jhipster.tech/)：是一个生成、开发和部署Spring Boot + Angular/React的Web应用和Spring微服务的开发平台。
- [Grails](https://grails.org)：基于Groovy。

## 项目结构

### Wrapper

使用[Spring Initializr](https://start.spring.io/)生成的项目中，已经带有一个[Maven Wrapper](https://github.com/takari/maven-wrapper) （即项目根目录下的`mvnw`和`mvnw.cmd`文件）或 [Gradle Wrapper](http://gradle.org/docs/current/userguide/gradle_wrapper.html) （即项目根目录下的`gradlew`和`gradlew.bat`）。Wrapper的用法与原来的Maven或Gradle一样。例如：原来Maven的`mvn clean install`，在Wrapper中则是`./mvnw clean install`。

Wrapper的主要作用是保证所有构建这个项目的人，都使用同一版本的Maven或Gradle。当执行Wrapper命令时，如果发现当前用户的Maven或Gradle版本和期望的版本不一致，那么就下载期望的版本，然后用期望的版本来执行命令。 

在开发Spring Boot项目时，Wrapper不是必需使用的。完全可以使用自己安装的Maven或Gradle命令。

### 项目定义

pom.xml：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.3.4.RELEASE</version>
		<relativePath/> <!-- lookup parent from repository -->
	</parent>
	<groupId>com.example</groupId>
	<artifactId>demo</artifactId>
	<version>0.0.1-SNAPSHOT</version>
	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<properties>
		<java.version>11</java.version>
	</properties>

	<dependencies>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-data-jpa</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-web</artifactId>
		</dependency>

		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-devtools</artifactId>
			<scope>runtime</scope>
			<optional>true</optional>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
			<scope>test</scope>
			<exclusions>
				<exclusion>
					<groupId>org.junit.vintage</groupId>
					<artifactId>junit-vintage-engine</artifactId>
				</exclusion>
			</exclusions>
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

#### 父POM

`spring-boot-starter-parent`也可以通过导入方式来使用，而不是继承Parent POM方式：

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

> `spring-boot-starter-parent` 中`spring-boot-maven-plugin`包含了 `<executions>`，并绑定到 `repackage`目标。如果采用导入方式，则需要自己配置`spring-boot-maven-plugin`。
>
> ```xml
> <plugin>
> <groupId>org.springframework.boot</groupId>
> <artifactId>spring-boot-maven-plugin</artifactId>
> <executions>
>  <execution>
>    <goals>
>      <goal>repackage</goal>
>    </goals>
>  </execution>
> </executions>
> <configuration>
>  <mainClass>${start-class}</mainClass>
>  <executable>true</executable>
>  <fork>true</fork>
>  <!-- Enable the line below to have remote debugging of your application on port 5005
>  <jvmArguments>-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005</jvmArguments>
>  -->
> </configuration>
> </plugin>
> ```

使用导入方式时，如果要覆盖spring boot中的依赖时，要将该依赖的定义放在`spring-boot-dependencies`之前。

#### Starter依赖

Spring Boot提供了大量的“Starters”，使得添加相关特性到项目变得非常容易。

在使用Spring initializr生成项目时，`spring-boot-starter-test`总是被添加到项目，不需要显式选择它。

#### Spring Boot 插件

`spring-boot-maven-plugin`插件提供了一些重要的功能：

- 一个用来运行项目的Maven目标：`spring-boot:run`；
- 确保所有依赖库都会包含在可执行的JAR文件中；
- 会在JAR文件中生成一个manifest文件，将引导类声明为可执行JAR的主类。

### 引导应用

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

- `@ComponentScan`：告诉Spring去哪里自动扫描其他组件、配置和服务，并将它们自动注册为Spring Beans。在默认情况下，这个标注将会扫描当前包以及其下任意深度的子包。如果没有使得这个标注，则必须使用`@Import`标注显式列出要扫描的组件：

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

在STS中，如果打包类型是jar，则可以直接右击启动类，选择以`Java Application`方式或者`Spring Boot App`方式运行。而不需要借助任何IDE特定的插件或扩展。

也可以使用Spring Boot插件来运行：

```bash
$ mvn spring-boot:run  （Maven）
$ gradle bootRun       （Gradle）
```

在生产环境中，通常是打包成可执行Jar，然后以`java -jar`方式运行。例如：

```bash
$ java -jar target/demo-0.0.1-SNAPSHOT.jar
```

应用成功启动后，打开浏览器，访问：<http://localhost:8080/say>，就可以看到结果了。

可以按`Ctrl-C` 停止应用。

> 有时运行一个Web应用两次会出现端口占用情况，如果使得STS，可以使用它的`Relaunch`按钮来运行应用，而不是使用`Run`按钮来运行。`Relaunch`可以确保在运行应用之前，任何已经运行的实例被关闭。

## 调试

### 本地调试

### 远程调试

## 测试

```java
package com.example.demo;

import org.junit.jupiter.api.Test;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;

@RunWith(SpringRunner.class)
@SpringBootTest
class DemoApplicationTests {

	@Test
	void contextLoads() {
	}

}
```

`@RunWith`是JUnit的注解，它会提供一个测试运行器来指导JUnit如何运行测试，以提供自定义的测试行为。`SpringRunner`当前实际上是`SpringJUnit4ClassRunner`的别名，主要为了移除对特定JUnit版本的关联。它会创建测试运行所需要的Spring应用上下文。

`@SpringBootTest`会告诉JUnit在启动测试时要添加上Spring Boot的功能。

## 打包

如果使用Maven，则可以使用下列命令将项目打包：

```bash
$ mvn clean package
```

打包后，会在`target`目录下生成两个文件，例如：`demo-0.0.1-SNAPSHOT.jar`和`demo-0.0.1-SNAPSHOT.jar.original`。前者是可执行的jar，后者是未被Spring Boot重打包的原始JAR。

Spring Initializr默认将应用打包成一个可执行的jar包，并且内嵌了Tomcat、Jetty和Undertow支持。因此，不再需要单独下载这些Servlet容器。这主要是为了更方便云部署。

当然，也可以将Spring Boot打包成传统的war包，部署到独立的Servlet容器中。只需在pom.xml中加上`<packaging>war</packaging>`，并且要包含一个Web初始化类。另外，这个war既可部署到支持Servlet 3.0的容器中，也可以直接使用`java -jar`命令来运行。