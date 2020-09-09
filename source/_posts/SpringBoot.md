---
title: SpringBoot
date: 2018-04-16 09:39:49
tags: [2.0.4]
---

# 简介

Spring Boot简化了Spring应用的开发，不需要什么配置就可以运行Spring应用。

Spring Boot项目可以直接被打包成一个可执行的jar包，并且内嵌了Tomcat、Jetty和Undertow支持。因此，不再需要单独下载这些Servlet容器。当然，也可以将Spring Boot打包成传统的war包，部署到独立的Servlet容器中。

<!--more-->

## 系统要求

Spring Boot 2 要求 Java 8及以上版本。

## 常用工具

[Spring Initializr](https://start.spring.io/)：一个Spring Boot项目的在线生成器。

Spring Boot CLI：是Spring Boot 命令行工具，它可以运行Groovy脚本，通过它也可以很方便地开发Spring Boot项目。

构建工具：Maven、Gradle或Ant。

> 使用[Spring Initializr](https://start.spring.io/)或者STS生成的项目中，已经带有一个[Maven Wrapper](https://github.com/takari/maven-wrapper) （即项目根目录下的`mvnw`和`mvnw.cmd`文件）或 [Gradle Wrapper](http://gradle.org/docs/current/userguide/gradle_wrapper.html) 。Wrapper的用法与原来的Maven或Gradle一样。例如：原来Maven的`mvn clean install`，在Wrapper中则是`./mvnw clean install`。
>
> Wrapper的主要作用是保证所有构建这个项目的人，都使用同一版本的Maven或Gradle。当执行Wrapper命令时，如果发现当前用户的Maven或Gradle版本和期望的版本不一致，那么就下载期望的版本，然后用期望的版本来执行命令。 
>
> 在开发Spring Boot项目时，Wrapper不是必需使用的。完全可以使用原来的Maven或Gradle命令。

VSCode + Java Extension Pack + Spring Boot Extension Pack：轻量级开发环境。

Spring Tool Suite（STS）：一个基于Eclipse的IDE，可以很方便地开发Spring Boot项目。

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
	<packaging>jar</packaging><!-- 打包成jar包。如果要打包成war，只需在这里改成“war”即可。而且这个war既可部署到支持Servlet 3.0的容器中，也可以使用“java -jar”命令来直接运行。 -->

	<name>demo</name>
	<description>Demo project for Spring Boot</description>

	<parent><!-- 继承Spring Boot的parent POM -->
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>2.0.3.RELEASE</version>
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
      <version>2.0.3.RELEASE</version>
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
>   <groupId>org.springframework.boot</groupId>
>   <artifactId>spring-boot-maven-plugin</artifactId>
>   <executions>
>     <execution>
>       <goals>
>         <goal>repackage</goal>
>       </goals>
>     </execution>
>   </executions>
>   <configuration>
>     <mainClass>${start-class}</mainClass>
>     <executable>true</executable>
>     <fork>true</fork>
>     <!-- Enable the line below to have remote debugging of your application on port 5005
>     <jvmArguments>-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005</jvmArguments>
>     -->
>   </configuration>
> </plugin>
> ```

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

## 编程

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

可以按`Ctrl-C` 停止应用。

> 有时运行一个Web应用两次会出现端口占用情况，如果使得STS，可以使用它的`Relaunch`按钮来运行应用，而不是使用`Run`按钮来运行。`Relaunch`可以确保在运行应用之前，任何已经运行的实例被关闭。

## 调试

与调试普通`Java Application`一样。

### 远程调试

首先，在远程服务器上，以调试模式运行应用。例如：

```bash
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
       -jar target/demo-0.0.1-SNAPSHOT.jar
```

> 如果是使用Maven来启动应用，则可以设置如下环境变量（例如在.zshrc中设置）：
>
> ```
> export MAVEN_OPTS="-Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n"
> ```

然后，在Eclipse中新建`Remote Java Application`，`Host`处输入远程服务器的IP，`Port`处输入上面的`8000`。

最后，象正常那样设置断点，点`Debug`按钮进行调试。

> 远程服务器上的应用应该与Eclipse中的项目基于完全相同的代码。

## 测试

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
public class HelloControllerTest {
  @Autowired
  private MockMvc mockMvc;
  
  @Test
  public void testSay() throws Exception {
    mockMvc.perform(MockMvcRequestBuilders.get("/say").accept(MediaType.APPLICATION_JSON))
      .andExpect(status().isOk())
      .andExpect(content().string(equalTo("Hello World!")));
  }
}
```

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
> devtools依赖于应用上下文的shutdown钩子，如果禁用了shutdown钩子（`SpringApplication.setRegisterShutdownHook(false)`），则devtools将不能正常工作。
>
> 如果需要热拔插（Hot Swapping），可以使用 [JRebel](https://zeroturnaround.com/software/jrebel/)。当使用JRebel时，热重启将被禁用，然而热加载仍可用。

Spring Boot支持的许多库使用缓存来改善性能。例如，模板引擎。然而，在开发阶段，缓存也会带来麻烦，例如它会阻止我们看到最新的改变。因此，devtools默认禁用缓存（是否应用缓存，通常是在`application.yml`中配置的，但devtools会自动应用一些合理的开发时配置。具体参见 [DevToolsPropertyDefaultsPostProcessor](https://github.com/spring-projects/spring-boot/tree/v2.0.2.RELEASE/spring-boot-project/spring-boot-devtools/src/main/java/org/springframework/boot/devtools/env/DevToolsPropertyDefaultsPostProcessor.java) ）。

## 热重启

Spring Boot使用两个类加载器来实现热重启（Automatic Restart）机制。不会改变的资源（例如第三方库）被加载进基础类加载器，而项目中正在开发的资源被加载到重启类加载器中。当热重启时，只有重启类加载器被抛弃，并重新创建一个新的重启类加载器。而基础类加载器保持不变。

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

devtools远程支持包括两部分：服务端（运行在远程服务器中的应用）和客户端（运行在IDE中的相同应用）。

远程应用通常是采用`java -jar`方式运行的，默认是不包含devtools。为了让devtools支持远程应用，你需要确保devtools被打包到jar包中，这可通过在`pom.xml`中配置`spring-boot-maven-plugin`实现：

```xml
<build>
	<plugins>
		<plugin>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-maven-plugin</artifactId>
			<configuration>
				<excludeDevtools>false</excludeDevtools>
			</configuration>
		</plugin>
	</plugins>
</build>
```

然后，需要设置一下 `spring.devtools.remote.secret` 属性：

```properties
spring.devtools.remote.secret=mysecret
```

这样，将应用部署到远程服务器后，就自动启用了devtools。

在IDE中运行的远程客户端需要将main class指定为`org.springframework.boot.devtools.RemoteSpringApplication` ，并在与运程服务端相同的类路径下用它来启动客户端。

例如，我们有一个应用`my-app`被部署到云上。在本地的Eclipse上可以作如下配置，启动`my-app`的远程客户端：

1. Select `Run Configurations…` from the `Run` menu.
2. Create a new `Java Application` “launch configuration”.
3. Browse for the `my-app` project.
4. Use `org.springframework.boot.devtools.RemoteSpringApplication` as the main class.
5. Add `https://myapp.cfapps.io` to the `Program arguments` (or whatever your remote URL is).

### 远程更新



# 配置

Spring Boot偏爱基于Java的配置，而不是基于XML的配置。

标注有`@Configuration`的类就是Spring Boot的配置类。

不需要将所有配置全部集中在一个配置类中，可以将配置分散在多个配置类中。然后，通过`@ComponentScan`可以自动装配这些配置，也可以通过`@import`来自己指定要导入的其他配置类。

如果要导入基于XML的配置文件，则使用`@ImportResource`标注。

## 自动配置

将`@EnableAutoConfiguration`标注在配置类上，允许Spring Boot根据依赖关系，自动配置Spring应用。例如，如果hsqldb的JAR在你的类路径上，则Spring Boot会自动配置这个JAR与Spring集成。

自动配置是非侵入的，在任何时候，你都可以用自己的配置覆盖自动配置。

如果希望知道自动配置都配置了什么，可以启用“debug”模式或“trace”模式。

命令行：

```bash
$ java -jar springTest.jar --debug
$ java -jar springTest.jar --trace
```

应用属性文件：

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

### 应用属性的加载顺序

Spring Boot按下列顺序加载应用属性，顺序靠前的应用属性优先：

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

### 应用属性文件

`SpringApplication`默认从下列位置加载应用属性文件（按优先级从高到低）：

1. 当前目录的`config`子目录（即`file:./config/`）；
2. 当前目录（`file:./`）；
3. 类路径中的`/config`包中（`classpath:/config`）；
4. 类路径的根中（`classpath:/`）。

> 当前目录是指执行`java -jar`命令时所在的目录。

如果不喜欢应用属性文件名的`application`部分，可以通过环境变量`SPRING_CONFIG_NAME`或系统属性`spring.config.name`来自己指定一个名字：

```bash
$ java -jar myproject.jar --spring.config.name=myproject
```

还可以通过环境变量`SPRING_CONFIG_LOCATION`或系统属性`spring.config.location`来自己指定应用属性文件的位置，它的值是一个逗号分隔的目录（必须以`/`结尾）或文件列表（靠后的优先级高）。如果是目录，将与`spring.config.name`一起组成应用属性文件的完整路径（应用属性文件既可以是特定`PROFILE`环境的，也可以是不特定的）。而如果是文件，则只当作是不特定PROFILE环境的应用属性文件（这时，任何PROFILE特定的应用属性文件都不可用）。

```bash
$ java -jar myproject.jar --spring.config.location=classpath:/default.properties,classpath:/override.properties
```

使用`spring.config.location`配置应用属性文件位置后，将不会再从默认位置加载应用属性位置。如果既从自定义位置加载，又可以从默认位置加载。则要使用环境变量`SPRING_CONFIG_ADDITIONAL-LOCATION`或系统属性`spring.config.additional-location`：

```bash
$ … --spring.config.additional-location=classpath:/custom-config/,file:./custom-config/
```

这时，将从下列位置加载（注意：自定义位置优先于默认位置）：

1. `file:./custom-config/`
2. `classpath:custom-config/`
3. `file:./config/`
4. `file:./`
5. `classpath:/config/`
6. `classpath:/`

#### @PropertySource

`@PropertySource`标注可以从自己指定的`.properties`文件中加载的应用属性。

foo/bar.properties：

```properties
demo.url = 1.2.3.4
demo.db = helloTest
```

BarProperties.java：

```java
@Configuration  
@PropertySource(value={"classpath:foo/bar.properties"}, ignoreResourceNotFound=false, encoding="UTF-8", name="bar.properties")
public class BarProperties {}
```

`ignoreResourceNotFound`表示指定属性文件不存在时是否报错，默认为`false`，即报错。

`value`值是需要加载的属性文件，可以一次性加载多个。

`name`值在Spring Boot环境中必须是唯一。

注意：`@PropertySource`标注只负责加载properties文件到Spring的`Environment`中，而将加载到的属性注入到Bean中仍需要使用`@Value`、`@ConfigurationProperties`标注 或`Environment`对象。两者可结合起来使用：

使用`@Value`：

```java
@Configuration  
@PropertySource("classpath:${my.placeholder:default/path}/bar.properties")
public class BarProperties {
  @Value("${demo.url}")  
  private String mongodbUrl;
  
  @Value("${demo.db}")  
  private String defaultDb;
}
```

使用`@ConfigurationProperties`：

```java
@Configuration  
@PropertySource({"classpath:foo/bar.properties", "file:/my/abc.properties"})
@ConfigurationProperties(prefix = "demo")
public class BarProperties {
  private String url;
  private String db;
  …
}
```

使用`Environment`：

```java
@Configuration  
@PropertySources({
  @PropertySource("classpath:foo/bar.properties"),
  @PropertySource("file:/my/abc.properties")
})
public class BarProperties {
  @Autowired
  private Environment env;
  
  @Bean
  public TestBean testBean() {
    TestBean testBean = new TestBean();
    testBean.setMongodbUrl(env.getProperty("demo.url"));
    testBean.setDefaultDb(env.getProperty("demo.db"));
    return testBean;
  }
}
```



#### 使用YAML格式的应用属性文件

应用属性文件既可以是传统的Java属性文件（.properties），也可以是YAML文件（.yml或.yaml），只要类路径中包含[SnakeYAML](http://www.snakeyaml.org/) （已经默认包含在`spring-boot-starter`中）。

Spring 两个方便的类来处理加载YAML文档，`YamlPropertiesFactoryBean` 将YAML加载为`Properties`，而`YamlMapFactoryBean` 将YAML加载为`Map`。

下列形式的YAML应用属性文件：

```yaml
environments:
	dev:
		url: http://dev.example.com
		name: Developer Setup
	prod:
		url: http://another.example.com
		name: My Cool App
```

将转化为等价的properties文件：

```
environments.dev.url=http://dev.example.com
environments.dev.name=Developer Setup
environments.prod.url=http://another.example.com
environments.prod.name=My Cool App
```

YAML的列表：

```yaml
my:
  servers:
    - dev.example.com
    - another.example.com
```

将转化为带索引的properties：

```
my.servers[0]=dev.example.com
my.servers[1]=another.example.com

# 或者使用单个逗号分隔的值
my.servers=dev.example.com,another.example.com
```

YAML应用属性文件的缺点是：不能使用`@PropertySource`标注加载。这种情况只能使用properties文件。

### 在应用属性中使用PlaceHolder

```properties
app.name=MyApp
app.description=${app.name} is a Spring Boot application
```

> 由于`application.properties` 或 `application.yml`配置文件（也包括`application-xxx.properties`和`application-xxx.yml`）支持PlaceHolder方式的插值表达式（${…}），因此Maven filtering的插值表达式被改成使用`@…@`。可以通过`resource.delimiter`属性来自定义Maven插值表达式的定界符。

### 自定义属性

除了可以设置Spring预定义的应用属性外，也可以定义一些我们的自定义属性：

```properties
book.name=SpringCloudInAction
```

自定义属性与预定义属性一样，也可以使用`@Value`和`@ConfigurationProperties`注入Bean。

### 获取应用属性

#### @Value

可以通过`@Value`将单个应用属性注入你的Bean中。

application.properties：

```properties
acme.name=test
```

MyBean.java：

```java
@Component
public class MyBean {
    @Value("${acme.name}")
    private String name;

    // ...
}
```

> 使用`@Value`只能注入单个值，而不能注入复合值（例如集合、对象）。但是，当复合值的元素是简单类型时，可以注入这些元素：
>
> application.yml：
>
> ```yaml
> my:
>   servers:
>     - dev.example.com
>     - another.example.com
> ```
>
> MyBean.java：
>
> ```java
> @Component
> public class MyBean {
>     @Value("${my.servers[1]}")
>     private String secondServer;
> 
>     // ...
> }
> ```
>
> 使用`@Value`注入的Bean字段可以不需要定义getters和setters。

#### @ConfigurationProperties

可以通过`@ConfigurationProperties`将多个应用属性注入到Bean中。

application.yml：

```yml
my:
  enabled: true
  remote-address: address.example.com
  security:
  	username: jo
  	password: 123456
    roles:
      - admin
      - user
```

Config.java：

```java
@ConfigurationProperties(prefix="my")
public class Config {
  private boolean enabled;
  private InetAddress remoteAddress;
  private final Security security = new Security(); //被显式初始化，可以不需要setter
  
  public boolean isEnabled() { ... }
  public void setEnabled(boolean enabled) { ... }
  
  public InetAddress getRemoteAddress() { ... }
	public void setRemoteAddress(InetAddress remoteAddress) { ... }

  public Security getSecurity() { ... }
  
  public static class Security {
		private String username;
		private String password;
		private List<String> roles = new ArrayList<>(Collections.singleton("USER")); //也可以使用“Set”集合类

		public String getUsername() { ... }
		public void setUsername(String username) { ... }
    
		public String getPassword() { ... }
		public void setPassword(String password) { ... }

		public List<String> getRoles() { ... }
		public void setRoles(List<String> roles) { ... }
	}
}
```

> 通常getters和setters是必须的，但setters在下列情况下可以省略：
>
> - 已经显式初始化的Maps。
>
> - 通过索引方式访问的集合或数组：
>
>   ```yaml
>   my:
>     servers:
>       - dev.example.com
>       - another.example.com
>   ```
>
>   或者：
>
>   ```properties
>   my.servers[0]=dev.example.com
>   my.servers[1]=another.example.com
>   ```
>
>   但是，集合或数组配置成逗号分隔的单个属性时，必须要有setter：
>
>   ```properties
>   my.servers=dev.example.com,another.example.com
>   ```
>
>   可以使用`@ConfigurationProperties `以集合或数组方式注入：
>
>   ```java
>   @ConfigurationProperties(prefix="my")
>   public class MyProperties {
>     private List<String> servers;
>   
>     public void setServers(List<String> ss) {
>       this.servers = ss;
>     }
>     
>     public List<String> getServers() {
>       return this.servers;
>     }
>   }
>   ```
>
>   如果使用`@Value`注入，只能将上述`my.servers`属性整个以`String`方式注入（可以不需要setter），而不能使用索引：
>
>   ```java
>   @Value("${my.servers}")
>   private String servers;
>   
>   //而不使用索引
>   //@Value("${my.servers[0]}")
>   //private String firstServers;
>   ```
>
> - 如果内嵌的POJO字段被显式初始化（象`Config.java`中的`Security`  字段那样），则可以不需要setter。

`@ConfigurationProperties`只是完成将应用属性注入到类中，但还不能将该类通过`@Autowired`注入给其他Bean。还需要在被`@Configuration`修饰的类上通过`@EnableConfigurationProperties`将该类注册为一个Bean：

```java
@Configuration
@EnableConfigurationProperties(MyProperties.class) //可以罗列多个类
public class MyConfiguration {
}
```

通过上面方式注册的`@ConfigurationProperties `的Bean，有一个形如`PREFIX-FULLY_QUALIFIED_NAME_OF_THEN_BEAN`名称。其中PREFIX是`@ConfigurationProperties `中的`prefix`参数。

例如：`my-jo.springboot.web.MyProperties`。如果`@ConfigurationProperties `没指定`prefix`，则为`jo.springboot.web.MyProperties`。

也可以直接在`MyProperties`上使用`@Component`，将它注册为一个Bean，而不需要在`@Configuration`类上使用`@EnableConfigurationProperties`标注了：

```java
@Component
@ConfigurationProperties(prefix="acme")
public class MyProperties {
	// ... 
}
```

`@ConfigurationProperties `除了可以标注在类上外，也可以标注在`@Bean public`的方法上，这在将应用属性绑定到不受自己控制的第三方组件时特别有用：

application.yml：

```yaml
server:  
  port: 8080  
  
spring:   
  redis:   
    dbIndex: 0  
    hostName: 192.168.58.133  
    password: nmamtf  
    port: 6379  
    timeout: 0  
    poolConfig:   
      maxIdle: 8  
      minIdle: 0  
      maxActive: 8  
      maxWait: -1  
```

RedisConfig.java：

```java
@Configuration    
@EnableAutoConfiguration  
public class RedisConfig {  
  @Bean    
  @ConfigurationProperties(prefix="spring.redis.poolConfig")    
  public JedisPoolConfig getRedisConfig(){    
    JedisPoolConfig config = new JedisPoolConfig();  
    return config;    
  }    

  @Bean    
  @ConfigurationProperties(prefix="spring.redis")    
  public JedisConnectionFactory getConnectionFactory(){    
    JedisConnectionFactory factory = new JedisConnectionFactory();    
    factory.setUsePool(true);  
    JedisPoolConfig config = getRedisConfig();    
    factory.setPoolConfig(config);    
    return factory;    
  }    

  @Bean    
  public RedisTemplate<?, ?> getRedisTemplate(){    
    RedisTemplate<?,?> template = new StringRedisTemplate(getConnectionFactory());    
    return template;    
  }       
}  
```

> 注意：其中`JedisConnectionFactory`中包含`dbIndex`、`hostName`、`password`、`port`、`timeout`、`poolConfig`几个成员变量。`JedisPoolConfig`包含`maxIdle`、`minIdle`、`maxActive`、`maxWait`几个成员变量。 

`@ConfigurationProperties`可以通过属性`ignoreInvalidFields`来决定是否忽略非法字段（默认为`false`，表示出现非法字段会报错），还可以通过属性`ignoreUnknownFields`来决定是否忽略未知字段（默认为`true`）。

##### 应用属性名与Bean属性名的映射规则

例如：

```java
@ConfigurationProperties(prefix="acme.my-project.person")
public class OwnerProperties {
	private String firstName;

	public String getFirstName() {
		return this.firstName;
	}

	public void setFirstName(String firstName) {
		this.firstName = firstName;
	}
}
```

则下面的应用属性名都可以被使用：

```properties
acme.my-project.person.first-name  #Kebab风格（推荐。小写，以“-”分隔）
acme.myProject.person.firstName    #标准驼峰风格
acme.my_project.person.first_name  #下划线风格
ACME_MYPROJECT_PERSON_FIRSTNAME    #大写风格
```

> `prefix`参数值只能使用Kebab风格。

不同属性源的映射规则：

| Property Source       | Simple                                                       | List                                                         |
| --------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Properties Files      | Camel case, kebab case, or underscore notation               | Standard list syntax using `[索引]` or comma-separated values |
| YAML Files            | Camel case, kebab case, or underscore notation               | Standard YAML list syntax or comma-separated values          |
| Environment Variables | Upper case format with underscore as the delimiter. `_` should not be used within a property name | Numeric values surrounded by underscores, such as `MY_ACME_1_OTHER 相当于 my.acme[1].other` |
| System properties     | Camel case, kebab case, or underscore notation               | Standard list syntax using `[ ]` or comma-separated values   |

##### 校验

Spring Boot总是会去校验`@ConfigurationProperties`  类，而不管它是否有标注`@Validated`。

```java
@ConfigurationProperties(prefix="acme")
@Validated
public class AcmeProperties {
	@NotNull
	private InetAddress remoteAddress;

	@Valid
	private final Security security = new Security();

	// ... getters and setters

	public static class Security {
		@NotEmpty
		public String username;

		// ... getters and setters

	}
}
```

>  `@Validated`也可以标注在`@Bean`方法上。

还可以自己实现一个`Validator`，并将它注册为名叫`configurationPropertiesValidator`的Bean。注册它的`@Bean`方法应该要声明为`static`。例如，参见： [property validation sample](https://github.com/spring-projects/spring-boot/tree/v2.0.2.RELEASE/spring-boot-samples/spring-boot-sample-property-validation) 。

##### @ConfigurationProperties 与 @Value 的比较

| Feature                                                      | `@ConfigurationProperties` | `@Value` |
| ------------------------------------------------------------ | -------------------------- | -------- |
| [Relaxed binding](https://docs.spring.io/spring-boot/docs/2.0.2.RELEASE/reference/htmlsingle/#boot-features-external-config-relaxed-binding) | Yes                        | No       |
| [Meta-data support](https://docs.spring.io/spring-boot/docs/2.0.2.RELEASE/reference/htmlsingle/#configuration-metadata) | Yes                        | No       |
| `SpEL` evaluation                                            | No                         | Yes      |

#### 通过`Environment`获取应用属性

参见“@PropertySource”。

### 属性转换

在将应用属性绑定到Bean中时，Spring Boot会尝试进行合适的类型转换。如果需要，也可以自定义类型转换。

#### 转换时间长度

Spring Boot可以将下列形式的应用属性自动转换为Bean的`java.time.Duration`  属性：

- 以`long`表示的时间长度（默认单位是毫秒，可以使用`@DurationUnit`来自己指定时间单位）。

- 标准的ISO-8601格式的时间长度（参见[`java.util.Duration`](https://docs.oracle.com/javase/8/docs/api//java/time/Duration.html#parse-java.lang.CharSequence-)），例如：

  ```
  "PT20.345S" -- parses as "20.345 seconds"
  "PT15M"     -- parses as "15 minutes" (where a minute is 60 seconds)
  "PT10H"     -- parses as "10 hours" (where an hour is 3600 seconds)
  "P2D"       -- parses as "2 days" (where a day is 24 hours or 86400 seconds)
  "P2DT3H4M"  -- parses as "2 days, 3 hours and 4 minutes"
  "P-6H3M"    -- parses as "-6 hours and +3 minutes"
  "-P6H3M"    -- parses as "-6 hours and -3 minutes"
  "-P-6H+3M"  -- parses as "+6 hours and -3 minutes"
  ```

- 带时间单位的格式（例如：`10s`表示10秒）。可以使用的时间单位有：

  + `ns`纳秒。
  + `ms`毫秒。
  + `s`秒。
  + `m`分钟。
  + `h`小时。
  + `d`天。

例如：

```java
@ConfigurationProperties("app.system")
public class AppSystemProperties {
	@DurationUnit(ChronoUnit.SECONDS)
	private Duration sessionTimeout = Duration.ofSeconds(30);
	private Duration readTimeout = Duration.ofMillis(1000);

	public Duration getSessionTimeout() {
		return this.sessionTimeout;
	}

	public void setSessionTimeout(Duration sessionTimeout) {
		this.sessionTimeout = sessionTimeout;
	}

	public Duration getReadTimeout() {
		return this.readTimeout;
	}

	public void setReadTimeout(Duration readTimeout) {
		this.readTimeout = readTimeout;
	}
}
```



#### 自定义转换

自定义转换可以通过提供一个`ConversionService` Bean（Bean名为`conversionService`）或者提供一个自定义的属性编辑器（通过`CustomEditorConfigurer` Bean）或者提供一个自定义的`Converters`（标注上`@ConfigurationPropertiesBinding` ）。

### 常用配置

#### HTTP端口

Spring Boot应用默认的HTTP端口号是`8080`，可以通过属性`server.port`或环境变量`SERVER_PORT`来自定义HTTP端口号。例如：

```properties
server.port=18080
```

#### SSL配置

```properties
server.port=8443
server.ssl.key-store=classpath:keystore.jks
server.ssl.key-store-password=secret
server.ssl.key-password=another-secret
```

## Profiles

Spring Profiles提供了一种方式去将你的应用属性分成多个部分，并且使得它们只在某些环境中可用。

### PROFILE特定的应用属性文件

PROFILE特定的应用属性文件只在该PROFILE被激活时，才会生效。

PROFILE特定的应用属性文件的命名规则：`application-PROFILE.properties`或`application-PROFILE.yml`。

如果没有显式激活PROFILE，则默认激活`default` PROFILE，即加载`application-default.properties`或`application-default.yml`。

PROFILE特定的应用属性文件与标准应用属性文件（application.properties或application.yml）加载自相同的位置。

### 单个多Profiles的YAML文档

使用YAML作为应用属性文件时，除了可以像上面那样，为每个Profile分别创建一个独立的YAML文件外，也可以将多个Profiles创建在同一个YAML文件中。使用`---`来分割不同Profiles，并使用`spring.profiles`属性来标识每个Profiles：

```
server:
	address: 192.168.1.100
---
spring:
  profiles: default
  security:
    user:
      password: weak
---
spring:
	profiles: development
server:
	address: 127.0.0.1
---
spring:
	profiles: production
server:
	address: 192.168.1.120
```

### 未被显式声明的Proflie和默认Profile

未被显式声明的Profile的优先级比显式声明的Profile低，并且不管激活哪个Profile，未被显式声明的Profile中的应用属性**总会**被设置。这也是未被显式声明Profile与默认Profile不同的地方。

默认Profile（`default`）只在没有显式激活任何Profiles时，其中的应用属性才会被设置。

Spring profiles designated by using the `spring.profiles` element may optionally be negated by using the `!` character. If both negated and non-negated profiles are specified for a single document, at least one non-negated profile must match, and no negated profiles may match.

### 激活Profiles

可以通过`spring.profiles.active`应用属性来激活Profiles。

例如，可以在`application.properties`中配置它们：

```properties
spring.profiles.active=dev,hsqldb  #可以一次激活多个Profiles
```

也可以使用命令行参数`--spring.profiles.active=dev,hsqldb`。

用`java -jar`运行应用时，使用`java -Dspring.profiles.active=dev -jar foo.jar`。

还可以使用`spring.profiles.include`应用属性（可者通过在`SpringApplication.run`运行之前，调用`SpringApplication.setAdditionalProfiles()`方法）来指定，当某个Profile激活时，也一起激活的Profiles。

```yaml
---
my.property: fromyamlfile
---
spring.profiles: prod
spring.profiles.include:
  - proddb
  - prodmq
```

这样，当设置`--spring.profiles.active=prod`激活`prod` profile时，也会一起激活`proddb`和`prodmy` profiles。

### 复杂类型属性的重写

当复杂类型属性出现在多个profiles中时，重写是以整体替换方式进行。

AcmeProperties.java：

```java
@ConfigurationProperties("acme")
public class AcmeProperties {
	private final List<MyPojo> list = new ArrayList<>();
  private final Map<String, MyPojo> map = new HashMap<>();

	public List<MyPojo> getList() {
		return this.list;
	}

  public Map<String, MyPojo> getMap() {
		return this.map;
	}
}
```

MyPojo.java：

```java
public class MyPojo {
  private String name;
  private String description;
  
  ... //getters和setters
}
```

application.yml：

```yaml
acme:
  list:
    - name: my name
      description: my description
  map:
    key1:
      name: my name 1
      description: my description 1
---
spring:
  profiles: dev
acme:
  list:
    - name: my another name
  map:
    key1:
      name: dev name 1
    key2:
      name: dev name 2
      description: dev description 2
```

如果`dev` profile没有被激活，这时`AcmeProperties.list`  只包含一个`name`为`my name`的`MyPojo`实例，`AcmeProperties.map`  包含一个键为`key1`、`name`为`my name 1`的`MyPojo`实例。如果`dev` profile被激活，这时`AcmeProperties.list`  仍只包含一个`name`为`my another name`的`MyPojo`实例，`AcmeProperties.map`  包含两个`MyPojo`实例，一个键为`key1`、`name`为`dev name 1`，另一个键为`key2`、`name`为`dev name 2`。

### @Profile

上面的配置profile，要么是通过文件名（例如：`application-PROFILE.yml`），要么是通过属性`spring.profiles`。而`@Profile`提供了通过标注来配置profile的方法。

任何`@Component`和`@Configuration`都可以被标注上`@Profile`，这样，它们就只能在该Profile被激活时，才会被加载。

```java
@Configuration
@Profile("production")
public class ProductionConfiguration {
	// ...
}
```

`@Profile`还可以标注在`@Bean`方法上：

```java
@Bean
@Profile({"dev", "test"})
public DataSource devDataSource() {…}
```

`@Profile`还可以通过`!`来表示取反。例如：`@Profile("!test") `。



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

### 定制Banner

略

### 定制SpringApplication

略

### Fluent Builder API

略

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

除了Spring框架事件外，`SpringApplication`也会发出一些额外的应用事件。

当应用启动时，应用事件按下列顺序依次发出：

1. An `ApplicationStartingEvent` is sent at the start of a run but before any processing, except for the registration of listeners and initializers.
2. An `ApplicationEnvironmentPreparedEvent` is sent when the `Environment` to be used in the context is known but before the context is created.
3. An `ApplicationPreparedEvent` is sent just before the refresh is started but after bean definitions have been loaded.
4. An `ApplicationStartedEvent` is sent after the context has been refreshed but before any application and command-line runners have been called.
5. An `ApplicationReadyEvent` is sent after any application and command-line runners have been called. It indicates that the application is ready to service requests.
6. An `ApplicationFailedEvent` is sent if there is an exception on startup.

由于有些事件是在`ApplicationContext`创建之前就发生了，因此，这些事件的监听器不能作为一个`@Bean`的方式被注册。而是，要是使用``SpringApplication.addListeners(…)` 或  `SpringApplicationBuilder.listeners(…)` 来注册。如果希望这些监听器被自动注册，而不管`ApplicationContext`是否已经创建，则可以添加`META-INF/spring.factories` 文件，然后使用`org.springframework.context.ApplicationListener` 配置你的监听器：

```properties
org.springframework.context.ApplicationListener=com.example.project.MyListener
```

Application events are sent by using Spring Framework’s event publishing mechanism. Part of this mechanism ensures that an event published to the listeners in a child context is also published to the listeners in any ancestor contexts. As a result of this, if your application uses a hierarchy of `SpringApplication` instances, a listener may receive multiple instances of the same type of application event.

To allow your listener to distinguish between an event for its context and an event for a descendant context, it should request that its application context is injected and then compare the injected context with the context of the event. The context can be injected by implementing `ApplicationContextAware` or, if the listener is a bean, by using `@Autowired`.

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

略

## 启用管理特性

略

# Actuator

Spring Boot Actuator为构建的应用提供一系列用于监控和管理的附加功能。

您可以选择使用HTTP端点或JMX来管理和监控您的应用程序。 

## 启用Actuator

要启用Acturator，只需要加入如下依赖：

```xml
<dependencies>
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-actuator</artifactId>
	</dependency>
</dependencies>
```

加入Actuator依赖后，启动应用，就会发现Actuator根据应用依赖和配置，为我们自动创建了许多用于监控和管理的端点。通过这些端点，我们可以实时获取应用的各项监控指标。

## Acturator端点

Acturator提供的内置端点可分成三类：

- 应用配置类：可获取应用程序中加载的应用配置、环境变量、自动化配置报告等。
- 度量指标类：获取应用程序运行过程中用于监控的度量指标，比如内存信息、线程池信息、HTTP请求统计等。
- 操作控制类：提供了对应用的关闭等操作功能。

技术无关的端点：

| ID               | Description                                                  | Enabled by default |
| ---------------- | ------------------------------------------------------------ | ------------------ |
| `auditevents`    | 公布当前应用程序的审计事件信息。                             | Yes                |
| `beans`          | 显示应用程序中所有Spring bean的完整列表。                    | Yes                |
| `conditions`     | Shows the conditions that were evaluated on configuration and auto-configuration classes and the reasons why they did or did not match. | Yes                |
| `configprops`    | Displays a collated list of all `@ConfigurationProperties`.  | Yes                |
| `env`            | Exposes properties from Spring’s `ConfigurableEnvironment`.  | Yes                |
| `flyway`         | Shows any Flyway database migrations that have been applied. | Yes                |
| `health`         | Shows application health information.                        | Yes                |
| `httptrace`      | Displays HTTP trace information (by default, the last 100 HTTP request-response exchanges). | Yes                |
| `info`           | Displays arbitrary application info.                         | Yes                |
| `loggers`        | Shows and modifies the configuration of loggers in the application. | Yes                |
| `liquibase`      | Shows any Liquibase database migrations that have been applied. | Yes                |
| `metrics`        | Shows ‘metrics’ information for the current application.     | Yes                |
| `mappings`       | Displays a collated list of all `@RequestMapping` paths.     | Yes                |
| `scheduledtasks` | Displays the scheduled tasks in your application.            | Yes                |
| `sessions`       | Allows retrieval and deletion of user sessions from a Spring Session-backed session store. Not available when using Spring Session’s support for reactive web applications. | Yes                |
| `shutdown`       | Lets the application be gracefully shutdown.                 | No                 |
| `threaddump`     | Performs a thread dump.                                      | Yes                |

> 上面的端点路径要加上`…/actuator/`。例如：`…/actuator/env`

Spring MVC、Spring WebFlux或Jersey等框架专用的端点：

| ID           | Description                                                  | Enabled by default |
| ------------ | ------------------------------------------------------------ | ------------------ |
| `heapdump`   | Returns a GZip compressed `hprof` heap dump file.            | Yes                |
| `jolokia`    | Exposes JMX beans over HTTP (when Jolokia is on the classpath, not available for WebFlux). | Yes                |
| `logfile`    | Returns the contents of the logfile (if `logging.file` or `logging.path` properties have been set). Supports the use of the HTTP `Range` header to retrieve part of the log file’s content. | Yes                |
| `prometheus` | Exposes metrics in a format that can be scraped by a Prometheus server. | Yes                |

### 启用或禁用端点

每个端点都可以启用或禁用。这控制着端点是否被创建，并且它的bean是否存在于应用程序上下文中。

默认情况下，除`shutdown` 以外的所有端点均已启用。 

可以通过`management.endpoint.端点ID.enabled`  属性来启用或禁用指定端点：

```properties
management.endpoint.shutdown.enabled=true
```

`management.endpoints.enabled-by-default`用于启用或禁用所有端点：

```properties
management.endpoints.enabled-by-default=false
management.endpoint.info.enabled=true
```

> 禁用的端点将从应用程序上下文中完全删除。如果您只想更改端点公布的技术，请改为使用`include`和`exclude`属性。 

### 公布端点

要远程访问端点，还必须通过JMX或HTTP进行公布。

通过HTTP公布的端点，将被映射到`/actuator/端点ID`的URL上。例如，`health`端点被映射到`/actuator/health`上。

默认情况下，只有`health`、`info`等几个端点被公布出来。如果要公布其他端点，需要进行如下配置：

```properties
management.endpoints.web.exposure.include=env,sessions
```

如果要公布所有端点，则使用：

```properties
management.endpoints.web.exposure.include=*
```



### 自定义端点

除了内置端点外，还允许您添加自己的端点。

# Spring Boot CLI

Spring Boot CLI本身就是启动器，所以不需要创建`Application`启动类。Maven或Gradle构建文件也不再需要，因为我们将会通过CLI运行未编译的Groovy文件。

## 安装

下载并解压就可以使用了。可以将`bin`目录加到`PATH`环境变量中。

查看版本：`spring --version`。

Spring Boot CLI在`Bash`和`Zsh` Shell中，可以支持自动补全，只要按`Tab`键。

# 部署

# 日志

## 引入依赖

Spring Boot内部日志都是使用 [Apache Commons Logging](https://commons.apache.org/logging) 作为通用日志接口（自己的日志可以选择Slf4j或Commons Logging），日志系统的具体实现，则留给用户选择。只要类路径中包含某个日志系统的库文件，该日志系统就会变得可用。

可以使用**系统属性**`org.springframework.boot.logging.LoggingSystem`  强制使用指定日志系统，它的值是`LoggingSystem`实现类的完全限定名。如果要完全禁用Spring Boot的日志配置，可以将该系统属性设置为`none`。

> 日志系统在`ApplicationContext` 创建之前被初始化，因此，通过`@PropertySource`标注加载的日志属性是无法控制（启用或禁用）日志系统的。唯一的方法是通过系统属性来控制日志系统。
>
> Java Util Logging 在“executable jar ”中会有问题，尽量不使用它。

### 引入Logback

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-logging</artifactId>
</dependency>
```

实际开发中我们不需要直接添加该依赖。 因为， Spring Boot 的 “Starters” 默认都采用Logback记录日志（你会发现`spring-boot-starter`其中包含了 `spring-boot-starter-logging`），并用`INFO`级别（包括`ERROR`、`WARN`和`INFO`日志）输出到控制台。 

这个Starter也会引入Slf4j，可以用来代替Common Logging来作为通用日志接口。

### 引入Log4j2

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter</artifactId>
  <exclusions>
    <exclusion>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-logging</artifactId>
    </exclusion>
  </exclusions>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-log4j2</artifactId>
</dependency>
```

这个Starter也会引入Slf4j，可以用来代替Common Logging来作为通用日志接口。

## 使用

```java
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.test.context.junit4.SpringRunner;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

@RunWith(SpringRunner.class)
@SpringBootTest
public class LoggerTests {
	private static final Logger logger = LoggerFactory.getLogger(LoggerTests.class);
  
	@Test
	public void test1() {
    logger.info("info...");
    logger.debug("debug...");
    logger.error("error...");
	}
}
```

## 日志输出格式

默认输出格式：

- Date and Time: Millisecond precision and easily sortable.
- Log Level: `ERROR`, `WARN`, `INFO`, `DEBUG`, or `TRACE`.
- Process ID.
- A `---` separator to distinguish the start of actual log messages.
- Thread name: Enclosed in square brackets (may be truncated for console output).
- Logger name: This is usually the source class name (often abbreviated).
- The log message.

```
2014-03-05 10:57:51.112  INFO 45469 --- [           main] org.apache.catalina.core.StandardEngine  : Starting Servlet Engine: Apache Tomcat/7.0.52
2014-03-05 10:57:51.253  INFO 45469 --- [ost-startStop-1] o.a.c.c.C.[Tomcat].[localhost].[/]       : Initializing Spring embedded WebApplicationContext

```



## 输出到文件

| `logging.file` | `logging.path` | Description                                               |
| -------------- | -------------- | --------------------------------------------------------- |
| *(none)*       | *(none)*       | 输出到控制台。                                            |
| `my.log`       | *(none)*       | 输出到 `my.log` 文件。指定的文件可以是绝对或相对路径。    |
| *(none)*       | `/var/log`     | 输出到 `/var/log/spring.log` 文件。可以是绝对或相对路径。 |

> 两者同时设置，则只有`logging.file`生效。

默认情况，当日志文件达到10MB时，会将日志文件归档，并产生新的日志文件。

日志文件大小限制可以通过`logging.file.max-size`属性设置。

归档的日志文件默认是永久保存，也可以通过`logging.file.max-history`属性来设置保存期限。

## 日志级别设置

可以通过如下形式的应用属性来设置日志级别：

```properties
logging.level.LOGGER_NAME=LEVEL
```

LEVEL可以取：`TRACE`、`DEBUG`、`INFO`、`WARN`、`ERROR`、`FATAL`或`OFF`。 

> Logback没有`FATAL`级别，它会被映射到`ERROR`。

根日志记录器可以使用`logging.level.root`来配置。

例如：

```properties
logging.level.root=WARN
logging.level.org.springframework.web=DEBUG
logging.level.org.hibernate=ERROR
```

或者：

```yaml
logging:
  level:
    root: WARN
    org.springframework.web: DEBUG
    org.hibernate: ERROR
```

## 日志应用属性与系统属性的对应

| Spring Environment                  | System Property                 | Comments                                                     |
| ----------------------------------- | ------------------------------- | ------------------------------------------------------------ |
| `logging.exception-conversion-word` | `LOG_EXCEPTION_CONVERSION_WORD` | The conversion word used when logging exceptions.            |
| `logging.file`                      | `LOG_FILE`                      | If defined, it is used in the default log configuration.     |
| `logging.file.max-size`             | `LOG_FILE_MAX_SIZE`             | Maximum log file size (if LOG_FILE enabled). (Only supported with the default Logback setup.) |
| `logging.file.max-history`          | `LOG_FILE_MAX_HISTORY`          | Maximum number of archive log files to keep (if LOG_FILE enabled). (Only supported with the default Logback setup.) |
| `logging.path`                      | `LOG_PATH`                      | If defined, it is used in the default log configuration.     |
| `logging.pattern.console`           | `CONSOLE_LOG_PATTERN`           | The log pattern to use on the console (stdout). (Only supported with the default Logback setup.) |
| `logging.pattern.dateformat`        | `LOG_DATEFORMAT_PATTERN`        | Appender pattern for log date format. (Only supported with the default Logback setup.) |
| `logging.pattern.file`              | `FILE_LOG_PATTERN`              | The log pattern to use in a file (if `LOG_FILE` is enabled). (Only supported with the default Logback setup.) |
| `logging.pattern.level`             | `LOG_LEVEL_PATTERN`             | The format to use when rendering the log level (default `%5p`). (Only supported with the default Logback setup.) |
| `PID`                               | `PID`                           | The current process ID (discovered if possible and when not already defined as an OS environment variable). |

## 使用日志系统自己的配置

 使用应用属性文件只能简单配置日志系统，在Spring Boot项目中，可以提供日志系统自己的配置文件。

这些配置文件默认放在类路径的根，默认各日志系统的配置文件名如下：

| Logging System          | Customization                                                |
| ----------------------- | ------------------------------------------------------------ |
| Logback                 | `logback-spring.xml`, `logback-spring.groovy`, `logback.xml`, or `logback.groovy` |
| Log4j2                  | `log4j2-spring.xml` or `log4j2.xml`                          |
| JDK (Java Util Logging) | `logging.properties`                                         |

> 推荐使用带`-spring`的配置文件名，否则，Spring可能无法完全控制日志的初始化。因为，那些日志系统的默认配置文件名（例如：`logback.xml`），会在应用上下文创建之前就加载了。而使用带`-spring`的配置文件名或由`logging.config` 指定的配置文件，则会在应用上下文创建过程中加载。
>

也可以通过应用属性`logging.config`来指定自己的日志配置文件：

```properties
logging.config=classpath:logging-config.xml
```

如果要在日志配置文件中使用占位符，要使用 [Spring Boot’s syntax](https://docs.spring.io/spring-boot/docs/2.0.2.RELEASE/reference/htmlsingle/#boot-features-external-config-placeholders-in-properties) ，而不要使用日志系统自己的语法。

使用Logback时，属性名与它的默认值之间的分隔符应该使用`:`，而不是使用`:-`。

### Logback扩展

Logback的配置文件的`<configuration>`元素中，可以使用`<springProfile>`来基于Spring profile来配置日志：

```xml
<configuration>
  <springProfile name="staging">
    <!-- configuration to be enabled when the "staging" profile is active -->
  </springProfile>

  <springProfile name="dev, staging">
    <!-- configuration to be enabled when the "dev" or "staging" profiles are active -->
  </springProfile>

  <springProfile name="!production">
    <!-- configuration to be enabled when the "production" profile is not active -->
  </springProfile>
</configuration>
```

> 要使用Spring的Logback扩展，日志配置文件名必须是带`-spring`的，例如：`logback-spring.xml`。

### 日志配置文件示例

#### Logback

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration
               xmlns="http://ch.qos.logback/xml/ns/logback"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xsi:schemaLocation="http://ch.qos.logback/xml/ns/logback https://raw.githubusercontent.com/enricopulatzo/logback-XSD/master/src/main/xsd/logback.xsd">
  <property name="LOG_HOME" value="tp/log"/>
  
  <!-- 输出到控制台 -->
  <appender name="consoleLog" class="ch.qos.logback.core.ConsoleAppender" >
    <!-- 输出的格式 -->
    <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50}:  %msg%n</pattern>
    </encoder>
  </appender>

  <!-- 输出到文件 -->
  <appender name="infoFileLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <filter class="ch.qos.logback.classic.filter.LevelFilter">
      <level>ERROR</level>
      <onMatch>DENY</onMatch>
      <onMismatch>ACCEPT</onMismatch>
    </filter>
    <!-- 配置滚动的策略 -->
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <!-- 日志名称的格式 -->
      <fileNamePattern>${LOG_HOME}/logback.info.%d{yyyy-MM-dd}.log</fileNamePattern>
      <!-- 保存的最长时间：天数 -->
      <MaxHistory>30</MaxHistory>
    </rollingPolicy>
    <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50}:  %msg%n</pattern>
    </encoder>
  </appender>
  
  <appender name="errorFileLog" class="ch.qos.logback.core.rolling.RollingFileAppender">
    <filter class="ch.qos.logback.classic.filter.ThresholdFilter">
      <level>ERROR</level>
    </filter>
    <rollingPolicy class="ch.qos.logback.core.rolling.TimeBasedRollingPolicy">
      <fileNamePattern>${LOG_HOME}/logback.error.%d{yyyy-MM-dd}.log</fileNamePattern>
      <MaxHistory>30</MaxHistory>
    </rollingPolicy>
    <encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
      <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50}:  %msg%n</pattern>
    </encoder>
  </appender>

  <!-- 各子日志的配置 -->
  <logger name="ws.log.logback.LogbackTest" additivity="false" level="DEBUG">
    <appender-ref ref="consoleLog" />
  </logger>
  
  <!-- root日志配置 -->
  <root level="info">
    <appender-ref ref="consoleLog" />
    <appender-ref ref="infoFileLog" />
    <appender-ref ref="errorFileLog" />
  </root>
</configuration>
```



# Web

## MVC

### 启用Spring MVC

要在Spring Boot中集成Spring MVC框架，只需要加入下面依赖：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

默认内嵌Tomcat。

### Spring MVC的自动配置

Spring Boot为Spring MVC提供了如下自动配置：

- 引入`ContentNegotiatingViewResolver` 和`BeanNameViewResolver`  beans。
- 静态资源支持（包括WebJars支持）。
- 自动注册 `Converter`、`GenericConverter`和`Formatter` beans。
- `HttpMessageConverters`  支持。
- 自动注册 `MessageCodesResolver` 。
- 静态`index.html`支持。
- 自制`Favicon`支持。
- 自动应用`ConfigurableWebBindingInitializer` bean。

如果你想保留Spring Boot MVC自动配置的特性，同时还想加一些MVC额外的配置（例如，拦截器、格式化器等），你可以添加自己的`@Configuration`类，并实现`WebMvcConfigurer` 接口，但是**不能**标注`@EnableWebMvc`。

如果你想完全控制Spring MVC，则你可以添加自己的`@Configuration`类，并标注上`@EnableWebMvc`。

### HttpMessageConverters

Spring MVC使用`HttpMessageConverter` 接口来转换HTTP请求和响应。字符串默认以`UTF-8`编码。

如果需要添加或重写转换器，则可以使用Spring Boot的`HttpMessageConverters` 类（注意：末尾多了个`s`）：

```java
import org.springframework.boot.autoconfigure.web.HttpMessageConverters;
import org.springframework.context.annotation.*;
import org.springframework.http.converter.*;

@Configuration
public class MyConfiguration {
	@Bean
	public HttpMessageConverters customConverters() {
		HttpMessageConverter<?> additional = ...
		HttpMessageConverter<?> another = ...
		return new HttpMessageConverters(additional, another);
	}
}
```

### 定制JSON的序列化和反序列化

如果使用Jackson去序列化和反序列化JSON数据，则可以通过实现自己的`JsonSerializer` 和`JsonDeserializer`  类来定制JSON的序列化和反序列化过程。

定制序列化器和反序列化器，通常通过 [registered with Jackson through a module](http://wiki.fasterxml.com/JacksonHowToCustomDeserializers) ，但是Spring Boot提供了一个`@JsonComponent` 标注来简化这项工作。

你能使用`@JsonComponent` 直接标注在`JsonSerializer` 和`JsonDeserializer` 实现上，也可以将它标注在一个包含序列化器和反序列化器内部类的类上：

```java
import java.io.*;
import com.fasterxml.jackson.core.*;
import com.fasterxml.jackson.databind.*;
import org.springframework.boot.jackson.*;

@JsonComponent
public class Example {
	public static class Serializer extends JsonSerializer<SomeObject> {
		// ...
	}

	public static class Deserializer extends JsonDeserializer<SomeObject> {
		// ...
	}
}
```

`@JsonComponent` 包含了`@Component`。

另外，Spring Boot还提供了[`JsonObjectSerializer`](https://github.com/spring-projects/spring-boot/tree/v2.0.2.RELEASE/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/jackson/JsonObjectSerializer.java) 和[`JsonObjectDeserializer`](https://github.com/spring-projects/spring-boot/tree/v2.0.2.RELEASE/spring-boot-project/spring-boot/src/main/java/org/springframework/boot/jackson/JsonObjectDeserializer.java)  基础实现类。

### ？MessageCodesResolver

Spring MVC有一个策略，用于从绑定的错误产生用来渲染错误信息的错误码：`MessageCodesResolver`。如果设置`spring.mvc.message-codes-resolver.format`属性为`PREFIX_ERROR_CODE`或`POSTFIX_ERROR_CODE`，Spring Boot会为你创建一个`MessageCodesResolver`（参见：[`DefaultMessageCodesResolver.Format`](https://docs.spring.io/spring/docs/5.0.6.RELEASE/javadoc-api/org/springframework/validation/DefaultMessageCodesResolver.Format.html)）。 

### 静态内容

#### 资源映射

默认情况，Spring MVC的静态资源将位于类路径或`ServletContext`的根中的下列路径之一：

- /META-INF/resources/
- /resources/（注意：`src/main/resources`是类路径的根，这里的`/resources/`应该位于它下面）
- /static/
- /public/

由Spring Boot自动配置的模板引擎，它的视图模板文件默认位于`classpath:/templates`目录下。例如：

```java
@RequestMapping("/foo")
public String foo() {
  return "/admin/foo.btl";  //视图路径也可以不以“/”开始，效果是一样的。return "admin/foo";
}
```

上面代码中视图将会定位到`src/main/resources/templates/admin/foo.ftl`。

FreeMarker视图默认的后缀是`.ftl`，Thymeleaf视图默认的后缀是`.html`。默认后缀可以省略。

视图模板文件中引用的静态资源文件，将默认放在`classpath:/static`目录下。假如，`foo.ftl`中有如下引用：

```html
<link href="/css/ztree.css" rel="stylesheet"/>
```

则Spring Boot将会定位到`src/main/resources/static/css/ztree.css`。

资源默认是映射到`/**`路径，可以通过`spring.mvc.static-path-pattern`来自定义映射路径。例如：

```properties
spring.mvc.static-path-pattern=/resources/**
```

则上例中的`src/main/resources/static/css/ztree.css`将映射为`/resources/css/ztree.css`。

可以通过`spring.resources.static-locations`  属性自定义静态资源的位置，这样默认位置将不可用：

```properties
spring.resources.static-locations=classpath:/foo/**,file:/bar/**
```

> ServletContext的根`/`自动加入`static-locations`中。
>
> `spring.mvc.static-path-pattern`定义的是访问路径，而`spring.resources.static-locations`定义的是存放的物理位置。

如果只是希望增加静态资源位置，而不是覆盖默认位置，默认位置仍有效，则可以提供自己的`WebMvcConfigurer` 配置类，并重写`addResourceHandlers`  方法，添加自己的资源映射：

```java
@Configuration
public class MyWebAppConfigurer 
  	extends WebMvcConfigurerAdapter {
  @Override
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
    registry.addResourceHandler("/myres/**")
      .addResourceLocations("classpath:/myres/", "file:/myres2/");
    super.addResourceHandlers(registry);
  }
}
```

> 如果我们的应用打包成jar形式，则不要使用`src/main/webapp`目录，它只在war形式的包中可用。

另外，任何映射到`/webjars/**`下的资源，都将从Webjars格式的jar中提取。例如：

```html
<script type="text/javascript" src="${pageContext.request.contextPath }/webjars/jquery/2.1.4/jquery.js"></script>
```

要实现上述对jQuery的访问，只需要添加jQuery的Webjars依赖：

```xml
<dependency>
  <groupId>org.webjars</groupId>
  <artifactId>jquery</artifactId>
  <version>2.1.4</version>
</dependency>
```

在实际开发中，可能会遇到升级版本号的情况，如果我们有100多个页面，几乎每个页面上都有按上面引入`jquery.js` 那么我们要把版本号更换为`3.0.0`，一个一个替换显然不是最好的办法。 如何来解决？按如下方法处理即可。

首先在`pom.xml` 中添加依赖：

```xml
<dependency>
  <groupId>org.webjars</groupId>
  <artifactId>webjars-locator-core</artifactId>
</dependency>
```

这样就可以忽略webjars的版本号了：

```html
<script type="text/javascript" src="${pageContext.request.contextPath }/webjars/jquery/jquery.js"></script>
```

#### 缓存问题

当我们的资源内容发生改变时，由于浏览器缓存，用户本地的资源还是旧资源。防止这种情况最常见的方法是对资源名进行哈希处理。

例如，只要配置如下属性：

```properties
spring.resources.chain.strategy.content.enabled=true
spring.resources.chain.strategy.content.paths=/**
```

则只要资源内容发生变化，它的名字将被哈希化：`<link href="/css/spring-2a2d595e6ed9a0b24f027f2b63b134d6.css"/>` 。

这种处理是由`ResourceUrlEncodingFilter`  来完成的，Spring  Boot自动为Thymeleaf和FreeMarker配置了该过滤器，而JSP要手动配置。

还有一种方式来解决缓存问题，就是在URL中加上版本号。这只需要配置如下：

```properties
spring.resources.chain.strategy.fixed.enabled=true
spring.resources.chain.strategy.fixed.paths=/js/lib/
spring.resources.chain.strategy.fixed.version=v12
```

这样，`/js/lib/`下的资源，将通过`/v12/js/lib/…`来访问。

以上两种解决缓存问题的方法可以同时使用。

### 欢迎页面

Spring Boot的欢迎页面既可以是静态的，也可以是模板化的。

Spring Boot首先在静态资源所在的位置处查找名为`index.html`的静态欢迎页面，如果没找到，则继续查找名为`index`的模板文件作为欢迎页面。

### 定制网站图标

Spring Boot自动从静态资源所在位置或类路径根下查找名为`favicon.ico`的文件作为网站图标。

### 路径匹配和内容协商机制

在HTTP请求映射时，Spring Boot默认禁用后缀模式匹配。例如，请求`GET /projects/spring-boot.json`  不会匹配映射`@GetMapping("/projects/spring-boot")` 。

以往使用这种后缀模式匹配的原因是，许多HTTP客户端并不发送合适的“Accept”请求头。

现在处理HTTP客户端不发送合适的“Accept”请求头的更好的方式是使用查询参数`format=…`。例如：请求`GET /projects/spring-boot?format=json` 可以匹配 `@GetMapping("/projects/spring-boot")`  。为了使用这种查询参数的方式，要做如下配置：

```properties
spring.mvc.contentnegotiation.favor-parameter=true

# We can change the parameter name, which is "format" by default:
# spring.mvc.contentnegotiation.parameter-name=myparam

# We can also register additional file extensions/media types with:（从而可以使用“format=markdown”查询参数，从而响应“text/markdown”类型内容）
spring.mvc.contentnegotiation.media-types.markdown=text/markdown
```

当然，Spring Boot仍然支持后缀模式，只需要做如下配置：

```properties
spring.mvc.contentnegotiation.favor-path-extension=true

# You can also restrict that feature to known extensions only
# spring.mvc.pathmatch.use-registered-suffix-pattern=true

# We can also register additional file extensions/media types with:（从而以“.adoc”为后缀的路径，将响应“text/asciidoc”类型内容）
# spring.mvc.contentnegotiation.media-types.adoc=text/asciidoc
```

### ？ConfigurableWebBindingInitializer

Spring MVC使用`WebBindingInitializer`为特定请求初始化`WebDataBinder`。 如果您创建自己的`ConfigurableWebBindingInitializer` Bean，Spring Boot会自动配置Spring MVC以使用它。 

### 模板引擎

Spring MVC支持各种模板引擎技术。其中，Spring Boot 对如下模板引擎提供了自动配置支持：

- [FreeMarker](https://freemarker.org/docs/)
- [Groovy](http://docs.groovy-lang.org/docs/next/html/documentation/template-engines.html#_the_markuptemplateengine)
- [Thymeleaf](http://www.thymeleaf.org/)
- [Mustache](https://mustache.github.io/)

> 尽管Spring Boot也支持JSP，但应该避免使用，因为它在内嵌Servlet容器中会有问题。

在Spring Boot 项目中，可通过添加下列依赖之一，来启用相应模板引擎：

```xml
<!-- Freemarker依赖 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-freemarker</artifactId>
</dependency>

<!-- Groovy模板引擎依赖 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-groovy-templates</artifactId>
</dependency>

<!-- Thymeleaf依赖 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>

<!-- Mustache依赖 -->
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-mustache</artifactId>
</dependency>
```



### 错误处理

默认情况下，Spring Boot使用`BasicErrorController`自动提供了两个`/error`映射用于处理所有错误，一个响应JSON（通过应用客户端），另一个响应HTML（通过浏览器）。

#### 定制错误视图

如果要定制响应的HTML视图（默认是一个“Whitelabel”错误视图），只需创建一个名为`error`的视图既可。`error`视图文件可以是静态HTML（放在静态资源目录中），也可以是模板文件。

默认错误视图映射的URL是`/error`，我们可以通过应用属性`error.path`来定制错误视图的URL。

Spring Boot还允许我们为指定的状态码提供定制的错误页面。只需将错误视图文件放到静态资源下的`error/`目录下，错误视图文件名必须是具体的状态码，或带有`x`掩码的状态码。

例如，为`404`状态码映射一个静态HTML文件，为所有`5xx`错误映射一个FreeMarker模板：

```
src/
 +- main/
     +- java/
     |   + <source code>
     +- resources/
         +- public/
             +- error/
             |   +- 404.html
             +- <other public assets>
         +- templates/
             +- error/
             |   +- 5xx.ftl
             +- <other templates>
```

对于更复杂的视图映射，你可以注册一个实现了`ErrorViewResolver`接口的Bean：

```java
public class MyErrorViewResolver implements ErrorViewResolver {
	@Override
	public ModelAndView resolveErrorView(HttpServletRequest request,
			HttpStatus status, Map<String, Object> model) {
		// Use the request or status to optionally return a ModelAndView
		return ...
	}
}
```

#### 定制错误视图内容

如果要定制错误视图所展示的内容，可以注册一个类型为`ErrorAttributes`  的Bean。该Bean为错误视图提供数据，并会继续沿用已存在的错误处理机制。

下面代码是`ErrorMvcAutoConfiguration` 为Spring Boot自动配置的`ErrorAttributes`  Bean：

```java
@Bean
@ConditionalOnMissingBean(value = ErrorAttributes.class, search = SearchStrategy.CURRENT)
public DefaultErrorAttributes errorAttributes() {
  return new DefaultErrorAttributes(
    this.serverProperties.getError().isIncludeException());
}
```

下面是自定义`ErrorAttributes`  Bean的例子：

```java
@Bean
public DefaultErrorAttributes errorAttributes() {
  return new DefaultErrorAttributes() {
    @Override
    public Map<String, Object> getErrorAttributes (RequestAttributes requestAttributes,
                                                   boolean includeStackTrace) {
      Map<String, Object> errorAttributes = super.getErrorAttributes(requestAttributes, includeStackTrace);
      errorAttributes.remove("error");
      errorAttributes.remove("exception");
      return errorAttributes;
    }
  };
}
```

另外，也可以定义一个标注`@ControllerAdvice`的类，它将为指定的控制器或指定的异常类型返回自定义的JSON文档。例如：

```java
@ControllerAdvice(basePackageClasses = AcmeController.class)
public class AcmeControllerAdvice extends ResponseEntityExceptionHandler {
	@ExceptionHandler(YourException.class)
	@ResponseBody
	ResponseEntity<?> handleControllerException(HttpServletRequest request, Throwable ex) {
		HttpStatus status = getStatus(request);
		return new ResponseEntity<>(new CustomErrorType(status.value(), ex.getMessage()), status);
	}

	private HttpStatus getStatus(HttpServletRequest request) {
		Integer statusCode = (Integer) request.getAttribute("javax.servlet.error.status_code");
		if (statusCode == null) {
			return HttpStatus.INTERNAL_SERVER_ERROR;
		}
		return HttpStatus.valueOf(statusCode);
	}
}
```

上例中，如果与 `AcmeController` 在同一包中的某个控制器抛出了`YourException`异常，则错误视图将收到`handleControllerException` 方法返回的JSON格式的`CustomErrorType`  数据，以替代`ErrorAttributes`  为错误视图提供数据。

#### 定制错误处理

如果要完全替换默认的行为，则可以注册一个自己的错误控制器（`@Controller` ），并且它要实现`ErrorController`接口或者直接扩展`BasicErrorController`类。

### Spring HATEOAS

超媒体是REST的一个重要方面。它允许您构建能够在很大程度上分离客户端和服务器的服务，并允许它们独立进化。为REST资源返回的*表现*（representations）不仅包含数据，还包含指向相关资源的链接。

使用Spring HATEOAS可以构建一个超媒体驱动的REST服务，这是一个API库，您可以使用它轻松创建指向Spring MVC控制器的链接，构建资源*表现*并控制它们如何呈现为支持的超媒体格式，例如HAL。

#### 添加Spring HATEOAS依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-hateoas</artifactId>
</dependency>
<dependency>
  <groupId>com.jayway.jsonpath</groupId>
  <artifactId>json-path</artifactId>
  <scope>test</scope>
</dependency>
```

#### 创建资源表现类

src/main/java/hello/Greeting.java：

```java
package hello;

import org.springframework.hateoas.ResourceSupport;

import com.fasterxml.jackson.annotation.JsonCreator;
import com.fasterxml.jackson.annotation.JsonProperty;

public class Greeting extends ResourceSupport {
  private final String content;

  @JsonCreator
  public Greeting(@JsonProperty("content") String content) {
    this.content = content;
  }

  public String getContent() {
    return content;
  }
}
```

`@JsonCreator`：指示了Jackson如何创建这个POJO实例。

`@JsonProperty` ：标示了哪些字段应该包含在生成的JSON中。

`ResourceSupport`类包含了*表现*模型的基本属性`_links`，它允许您添加Link的实例并确保它们正确地进行渲染。 

#### 创建资源控制器

src/main/java/hello/GreetingController.java ：

```java
package hello;

import static org.springframework.hateoas.mvc.ControllerLinkBuilder.*;

import org.springframework.http.HttpEntity;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;

@RestController
public class GreetingController {
  private static final String TEMPLATE = "Hello, %s!";

  @RequestMapping("/greeting")
  public HttpEntity<Greeting> greeting(
    	@RequestParam(value = "name", required = false, defaultValue = "World") String name) {
    Greeting greeting = new Greeting(String.format(TEMPLATE, name));
    greeting.add(linkTo(methodOn(GreetingController.class).greeting(name)).withSelfRel());

    return new ResponseEntity<>(greeting, HttpStatus.OK);
  }
}
```

`linkTo`和`methodOn`都是`ControllerLinkBuilder`上的静态方法，允许您在控制器上伪造一个方法调用。返回的`LinkBuilder`将检查控制器方法的映射注释，以准确建立该方法映射到的URI。 

调用`withSelfRel`将创建一个添加到`Greeting`表现模型的Link实例。 

#### 测试

访问： <http://localhost:8080/greeting> 

响应：

```json
{
  "content":"Hello, World!",
  "_links":{
    "self":{
      "href":"http://localhost:8080/greeting?name=World"
    }
  }
}
```

### CORS支持

从4.2开始，Spring MVC支持CORS。详见：《SpringWeb》

#### 控制器方法的CORS配置

为了在RESTful Web服务的响应中包含CORS访问控制标头，您只需向处理方法添加`@CrossOrigin`注释即可：

src/main/java/hello/GreetingController.java 

```java
@CrossOrigin(origins = "http://localhost:9000")
@GetMapping("/greeting")
public Greeting greeting(@RequestParam(required=false, defaultValue="World") String name) {
  System.out.println("==== in greeting ====");
  return new Greeting(counter.incrementAndGet(), String.format(template, name));
}
```

`@CrossOrigin`标注仅为此特定方法启用了跨域请求。默认情况下，它允许使用任意origins、任意请求头、`@RequestMapping`标注中指定的HTTP方法以及30分钟的`maxAge`。您可以通过指定其中一个标注属性的值来定制这些行为：origin、HTTP methods、allowedHeaders、exposedHeaders、allowCredentials或maxAge。在这个例子中，我们只允许`http:// localhost:9000`发送跨源请求。 

`@CrossOrigin`还可以标注在控制器类上，以便在此类的所有处理方法上启用CORS。 

#### 全局CORS配置

全局CORS是通过注册一个`WebMvcConfigurer` Bean来配置的。

src/main/java/hello/Application.java ：

```java
@Bean
public WebMvcConfigurer corsConfigurer() {
  return new WebMvcConfigurerAdapter() {
    @Override
    public void addCorsMappings(CorsRegistry registry) {
      registry.addMapping("/greeting-javaconfig").allowedOrigins("http://localhost:9000");
    }
  };
}
```

>  全局CORS配置可以与细粒度的`@CrossOrigin`配置结合使用。

## WebFlux

Spring WebFlux是Spring Framework 5.0中引入的新的反应式Web框架。与Spring MVC不同，它不需要Servlet API，完全异步、非阻塞和事件驱动，并通过[Reactor](https://projectreactor.io/) 项目（也提供了对RxJava的支持 ）实现 [Reactive Streams](http://www.reactive-streams.org/) 规范。 另外，Spring WebFlux也支持响应式的Websocket服务端开发。

Spring WebFlux有两种风格：函数式风格和基于标注的风格。基于标注的网络非常接近Spring MVC。

基于标注的风格：

```java
@RestController
@RequestMapping("/users")
public class MyRestController {
	@GetMapping("/{user}")
	public Mono<User> getUser(@PathVariable Long user) {
		// ...
	}

	@GetMapping("/{user}/customers")
	public Flux<Customer> getUserCustomers(@PathVariable Long user) {
		// ...
	}

	@DeleteMapping("/{user}")
	public Mono<User> deleteUser(@PathVariable Long user) {
		// ...
	}
}
```

基于函数式风格：

```java
// 创建路由
@Configuration
public class RoutingConfiguration {
	@Bean
	public RouterFunction<ServerResponse> monoRouterFunction(UserHandler userHandler) {
		return route(GET("/{user}").and(accept(APPLICATION_JSON)), userHandler::getUser)
				.andRoute(GET("/{user}/customers").and(accept(APPLICATION_JSON)), userHandler::getUserCustomers)
				.andRoute(DELETE("/{user}").and(accept(APPLICATION_JSON)), userHandler::deleteUser);
	}
}

// 创建WebFlux处理器
@Component
public class UserHandler {
	public Mono<ServerResponse> getUser(ServerRequest request) {
		// ...
	}

	public Mono<ServerResponse> getUserCustomers(ServerRequest request) {
		// ...
	}

	public Mono<ServerResponse> deleteUser(ServerRequest request) {
		// ...
	}
}
```

可以根据需要定义任意数量的`RouterFunction` Bean，以模块化路由器的定义。如果您需要应用优先级，则可以排序它们。 

### 启用WebFlux

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-webflux</artifactId>
</dependency>
<dependency>
  <groupId>io.projectreactor</groupId>
  <artifactId>reactor-test</artifactId>
  <scope>test</scope>
</dependency>
```

默认内嵌Netty。

> 在Spring Boot应用程序中同时添加spring-boot-starter-web和spring-boot-starter-webflux模块会导致Spring Boot自动配置Spring MVC，而不是WebFlux。这样做是因为许多Spring开发人员将spring-boot-starter-webflux添加到他们的Spring MVC应用程序中是为了使用反应性WebClient。
>
> 此外，您可以通过`SpringApplication.setWebApplicationType（WebApplicationType.REACTIVE）`来强制将应用程序类型设置为你选择的类型。 

### Spring WebFlux的自动配置

Spring Boot为Spring WebFlux提供了如下自动配置：

- 为HttpMessageReader和HttpMessageWriter实例配置编解码器；
- 支持提供静态资源，包括支持WebJars。

如果您想保留Spring Boot WebFlux自动配置的特性，并且想要添加其他WebFlux配置，则可以添加自己的标注有`@Configuration`，且类型为`WebFluxConfigurer`的类，但不能标注`@EnableWebFlux`。 

如果你想完全控制Spring WebFlux，则你可以添加自己的`@Configuration`类，并标注上`@EnableWebMvc`。

### 编解码器

Spring WebFlux使用HttpMessageReader和HttpMessageWriter接口来转换HTTP请求和响应。

Spring Boot通过使用`CodecCustomizer`实例来实现进一步的定制。 

如果您需要添加或自定义编解码器，则可以创建一个自定义CodecCustomizer组件，例如： 

```java
import org.springframework.boot.web.codec.CodecCustomizer;

@Configuration
public class MyConfiguration {
	@Bean
	public CodecCustomizer myCodecCustomizer() {
		return codecConfigurer -> {
			// ...
		}
	}
}
```

> 您还可以利用Spring Boot的自定义JSON序列化器和反序列化器。 

### 静态内容

默认情况下，Spring Boot将从类路径中的`/static`、`/public`、`/resources`或`/META-INF/resources`目录中提供静态内容。 

如果只是希望增加静态资源位置，而不是覆盖默认位置，默认位置仍有效，则可以提供自己的`WebFluxConfigurer` 配置类，并重写`addResourceHandlers`  方法，添加自己的资源映射。

资源默认是映射到`/**`路径，可以通过`spring.webflux.static-path-pattern`来自定义映射路径。例如：

```properties
spring.webflux.static-path-pattern=/resources/**
```

还可以使用`spring.resources.static-locations`来自定义静态资源位置。这样做会用设置的目录位置列表替换默认值。如果这样做，默认的欢迎页面（`index.html`）检测会切换到您的自定义位置。 

另外，任何映射到`/webjars/**`下的资源，都将从Webjars格式的jar中提取。

> Spring WebFlux应用程序不严格依赖于Servlet API，因此它们不能作为war文件部署，也不能使用`src/main/webapp`目录。 

### 模板引擎

同Spring MVC。

### 错误处理

WebFlux定制错误视图和内容方面与Spring MVC相同。不同在于定制错误处理方面。

要更改错误处理行为，可以实现`WebExceptionHandler`并注册一个该类型的bean。因为`WebExceptionHandler`是相当低级的，所以Spring Boot还提供了一个便利的`AbstractErrorWebExceptionHandler`，让您以WebFlux函数式处理错误，如下例所示： 

```java
public class CustomErrorWebExceptionHandler extends AbstractErrorWebExceptionHandler {
	// Define constructor here

	@Override
	protected RouterFunction<ServerResponse> getRoutingFunction(ErrorAttributes errorAttributes) {
		return RouterFunctions
				.route(aPredicate, aHandler)
				.andRoute(anotherPredicate, anotherHandler);
	}
}
```

> 您还可以直接继承`DefaultErrorWebExceptionHandler`并覆盖特定的方法。 

### WebFilter

Spring WebFlux提供了一个`WebFilter`接口，可以实现过滤HTTP请求-响应交换。

如果过滤器的顺序很重要，可以实现`Ordered`或使用`@Order` 标注。 Spring Boot可以为你自动配置WebFilter。当它这样做时，将使用下表中显示的顺序： 

| Web Filter                              | Order                            |
| --------------------------------------- | -------------------------------- |
| `MetricsWebFilter`                      | `Ordered.HIGHEST_PRECEDENCE + 1` |
| `WebFilterChainProxy` (Spring Security) | `-100`                           |
| `HttpTraceWebFilter`                    | `Ordered.LOWEST_PRECEDENCE - 10` |

## Jersey（JAX-RS）

## WebSocket

## Web服务

## 内嵌HTTP服务器

Spring Boot应用可以内嵌Tomcat、Jetty、Undertow或Netty HTTP服务器。

### Servlets、Filters和Listeners

在Spring Boot中使用Servlets、Filters和Listeners很简单：

1. 使用`@ServletComponentScan`标注启动类，基于标注自动发现Servlets、Filters和Listeners。

   > `@ServletComponentScan`标注只对内嵌Servlet容器起作用，对独立的容器没有作用。独立容器使用自身的发现机制。

2. 给Servlets实现类（实现`Servlet`接口）标注`@WebServlet`，给Filters实现类（实现`Filter`接口）标注`@WebFilter`，给Listeners实现类（实现相应监听器接口）标注`@WebListener`。

# 重试机制

当我们调用一个接口时，可能由于网络等原因造成第一次失败，再去尝试就成功了，这就是重试机制。

Spring-Retry框架可以将业务逻辑与重试逻辑解耦。

## 引入依赖

```xml
<dependency>
	<groupId>org.springframework.retry</groupId>
  <artifactId>spring-retry</artifactId>
</dependency>
<dependency>
	<groupId>org.aspectj</groupId>
  <artifactId>aspectjweaver</artifactId>
</dependency>
```

## 启用重试

通过给启动类标注上`@EnableRetry`来启用重试机制。

## 控制重试

```java
@Retryable(value={BusinessException.class}, maxAttempts=5, backoff=@Backoff(delay=5000, multiplier=2))
public AyUser findByNameAndPasswordRetry(String name, String password) {
  System.out.println("[findByNameAndPasswordRetry] 方法失败重试了！");
  throw new BusinessException();
}
```

`value`属性表示当出现哪些异常时触发重试。

`maxAttempts`表示最大重试次数（默认为3）。

`delay`表示重试的延迟时间。

`multiplier`表示上一次延迟时间是这一次的倍数。

# 安全

## 引入依赖



# 关系数据库

## 引入依赖

### 引入数据库驱动

MySQL：

```xml
<dependency>
  <groupId>mysql</groupId>
  <artifactId>mysql-connector-java</artifactId>
  <scope>runtime</scope>
</dependency>
```

H2：

```xml
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
  <scope>runtime</scope>
</dependency>
```

### 引入持久层框架

JDBC：

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```

Spring Data JPA：

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
	<artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
```

MyBatis：

```xml
<dependency>
  <groupId>org.mybatis.spring.boot</groupId>
  <artifactId>mybatis-spring-boot-starter</artifactId>
  <version>1.3.2</version>
</dependency>
```

## 配置数据库

### 嵌入式数据库

Spring Boot可以自动配置嵌入式H2、HSQL和Derby数据库。无需提供任何连接URL，只需要包含要使用的嵌入式数据库的相关依赖。 在这种情况下，无论您使用的应用程序上下文有多少，整个测试套件都会重用相同的数据库。如果要确保每个上下文都有单独的嵌入式数据库，只需将`spring.datasource.generate-unique-name` 设置为 `true` 。如果自己显式设置了连接嵌入式数据库的URL，则务必确保数据库自动关闭被禁用。也就是说，如果使用H2，则应使用`DB_CLOSE_ON_EXIT = FALSE`；如果使用HSQLDB，则应确保不使用`shutdown = true` 。禁用数据库的自动关闭使得可以在数据库关闭时进行Spring Boot控制，从而确保在不再需要访问数据库时发生 。

### 连接池

连接池实现的选择规则：

1. 如果HikariCP可用，总是选择它。 

   > 如果使用`spring-boot-starter-jdbc`或`spring-boot-starter-data-jpa` “starters”，则会自动获得对HikariCP的依赖。

2. 否则，如果Tomcat pooling DataSource可用，我们将使用它。 

3. 如果HikariCP和Tomcat pooling DataSource都不可用，并且[Commons DBCP2](https://commons.apache.org/proper/commons-dbcp/)可用，我们就会使用它。 

您可以完全绕过该算法，并通过设置`spring.datasource.type`属性指定要使用的连接池。始终可以手动配置其他连接池，如果您定义自己的DataSource bean，则不会进行自动配置 。

可以通过使用各自的前缀（`spring.datasource.hikari.*`、`spring.datasource.tomcat.*`和`spring.datasource.dbcp2.*`）来微调特定实现的设置。例如：

```properties
# Number of ms to wait before throwing an exception if no connection is available.
spring.datasource.tomcat.max-wait=10000

# Maximum number of active connections that can be allocated from this pool at the same time.
spring.datasource.tomcat.max-active=50

# Validate the connection before borrowing it from the pool.
spring.datasource.tomcat.test-on-borrow=true
```

### 配置数据源

数据源在属性文件中进行配置。

支持的选项，请参阅[DataSourceProperties](https://github.com/spring-projects/spring-boot/tree/v2.0.4.RELEASE/spring-boot-project/spring-boot-autoconfigure/src/main/java/org/springframework/boot/autoconfigure/jdbc/DataSourceProperties.java)。无论实际实施如何，这些都是标准选项。  

MySQL：

```properties
spring.datasource.url=jdbc:mysql://localhost/test
spring.datasource.username=dbuser
spring.datasource.password=dbpass
spring.datasource.driver-class-name=com.mysql.jdbc.Driver  # 可选，Spring Boot可以从url中推断出。
```

### 使用JNDI数据源

如果将Spring Boot应用程序部署到独立的应用服务器，则可能需要使用该应用服务器的内置功能来配置和管理数据源，并使用JNDI访问它。 

`spring.datasource.jndi-name`属性可用作`spring.datasource.url`、`spring.datasource.username`和`spring.datasource.password`属性的替代，以从特定JNDI位置访问DataSource。 例如：

```properties
spring.datasource.jndi-name=java:jboss/datasources/customers
```

### 配置MyBatis

```properties
mybatis.mapper-locations=classpath:/mappers/*Mapper.xml  #指定Mapper文件存放路径
mybatis.type-aliases-package=com.example.demo.dao   #指定DAO接口文件所在包
```



## 初始化数据库

在类路径根下（例如`src/main/resources`）下创建一个名为`schema.sql`的文件，并在里面编写创建数据库表的SQL语句，则当应用启动时，就会自动找到这个文件并进行数据加载。

## 使用JdbcTemplate

> 依赖于`spring-boot-starter-jdbc`。

Spring的`JdbcTemplate`和`NamedParameterJdbcTemplate`类是自动配置的，您可以将它们直接`@Autowired`到您自己的bean中，如下面的示例所示：

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.stereotype.Component;

@Component
public class MyBean {
	private final JdbcTemplate jdbcTemplate;

	@Autowired
	public MyBean(JdbcTemplate jdbcTemplate) {
		this.jdbcTemplate = jdbcTemplate;
	}

	// ...
}
```

您可以使用`spring.jdbc.template.*`属性自定义`JdbcTemplate`的某些属性，如以下示例所示： 

```properties
spring.jdbc.template.max-rows=500
```

## 使用Spring Data JPA

> 依赖于`spring-boot-starter-data-jpa`。

### 创建实体类

传统上，JPA“Entity”类在persistence.xml文件中指定。使用Spring Boot，此文件不是必需的，而是使用“实体扫描”。默认情况下，将搜索主配置类（使用`@EnableAutoConfiguration`或`@SpringBootApplication`标注）下面的所有包。 

任何使用`@Entity`、`@Embeddable`或`@MappedSuperclass`注释的类都被考虑为实体类。

```java
package hello;

import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;

@Entity
public class Customer {
  @Id
  @GeneratedValue(strategy=GenerationType.AUTO)
  private Long id;
  private String firstName;
  private String lastName;

  // no-args constructor required by JPA spec
  // this one is protected since it shouldn't be used directly
  protected Customer() {}

  public Customer(String firstName, String lastName) {
    this.firstName = firstName;
    this.lastName = lastName;
  }

  @Override
  public String toString() {
    return String.format(
      "Customer[id=%d, firstName='%s', lastName='%s']",
      id, firstName, lastName);
  }
  
  // getter和setter
}
```

### 创建Repository

Spring Data JPA中，Repository只需要创建为接口，在运行时，能够根据Repository接口自动创建Repository实现。 

```java
package hello;

import java.util.List;
import org.springframework.data.repository.CrudRepository;

public interface CustomerRepository extends CrudRepository<Customer, Long> {
  List<Customer> findByLastName(String lastName);
}
```

> 在使用JPA中，更经常的是扩展`JpaRepository`接口。

## 使用MyBatis

## 事务管理

在Spring Boot中已经默认对JPA、JDBC和MyBatis默认启用了事务，因此不需要显式使用`@EnableTransactionManagement`标注启用事务。

Spring既支持编程式事务管理，也支持声明式事务管理（推荐）。

Spring声明式事务管理提供了5种方式，其中基于标注的方式是目前比较流行的。使用基于标注的事务管理，只需将`@Transactional`标注在需要事务支持的类或方法上即可。

`@Transactional`通过`propagation`属性设置事务的传播行为，属性值可取：

- REQUIRED：如果当前没有事务，就新建一个事务；否则，就加入已有事务。
- SUPPORTS：加入当前事务；如果当前没有事务，就以非事务方式执行。
- MANDATORY：加入当前事务；如果当前没有事务，就抛出异常。
- REQUIRES_NEW：总是新建事务，如果当前存在事务，就把当前事务挂起。
- NOT_SUPPORTED：总是以非事务方式执行操作，如果当前存在事务，就把当前事务挂起。
- NEVER：总是以非事务方式执行操作，如果当前存在事务，就抛出异常。
- NESTED：如果当前存在事务，就在嵌套事务内执行；如果当前没有事务，就执行与`REQUIRED`类似的行为。

`@Transactional`通过`isolation`属性定义事务隔离级别，属性值可取：

- DEFAULT
- READ_UNCOMMITTED
- READ_COMMITTED
- REPEATABLE_READ
- SERIALIZABLE

还可以通过`timeout`属性设置事务过期时间，通过`readOnly`属性指定当前事务是否是只读事务，通过`rollbackFor`或`noRollbackFor`指定哪个或哪些异常可以或不可以引起事务回滚。

注意：Spring Data中提供的所有Repository接口都是没有标注`@Transactional`的，即没有进行事务控制的。只有实现类中（如`SimpleJapRepository`)中才有标注`@Transactional`。因此，为了进行事务控制，在自己定义的接口或类中，要标注上`@Transactional`。

# NoSQL

## Redis



## Hazelcast

## MongoDB

### 引入依赖

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb</artifactId>
</dependency>
```

或者

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-mongodb-reactive</artifactId>
</dependency>
```



# 校验

# 缓存

# 消息系统

# 异步调用

## 启用异步调用

在启动类上标注`@EnableAsync`启用异步调用。

## 编写异步调用方法

在方法上标注`@Async`，该方法就成了异步调用方法，该方法应该返回`Future`类型。

``` java
@Async
public Future<List<AyUser>> findAsynAll() {
  …
}
```



# 收发邮件

# 分布式事务

# 调度器

# Spring集成

# 测试