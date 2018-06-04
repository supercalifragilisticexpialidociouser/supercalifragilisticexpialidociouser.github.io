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

如果希望知道自动配置都配置了什么，可以带上`--debug`选项来启动应用。

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

16. 在`@Configuration`标注的类中，通过`@PropertySource`定义的应用属性。

17. 应用默认属性（通过`SpringApplication.setDefaultProperties`设定）。

### 应用属性文件

`SpringApplication`默认从下列位置加载应用属性文件（按优先级从高到低）：

1. 当前目录的`config`子目录（即`file:./config/`）；
2. 当前目录（`file:./`）；
3. 类路径中的`/config`包中（`classpath:/config`）；
4. 类路径的根中（`classpath:/`）。

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

#### PROFILE特定的应用属性文件

如果没有显式激活PROFILE，则默认激活`default` PROFILE，即加载`application-default.properties`或`application-default.yml`。

PROFILE特定的应用属性文件与标准应用属性文件（application.properties或application.yml）加载自相同的位置。

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

```java
@ConfigurationProperties(prefix = "another")
@Bean
public AnotherComponent anotherComponent() {
	...
}
```

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

未被显式标识的Profile的优先级比显式标识的Profile低，并且不管激活哪个Profile，未被显式标识的Profile中的应用属性**总会**被设置。这也是未被显式标识Profile与默认Profile不同的地方。

默认Profile（`default`）只在没有显式激活任何Profiles时，其中的应用属性才会被设置。

Spring profiles designated by using the `spring.profiles` element may optionally be negated by using the `!` character. If both negated and non-negated profiles are specified for a single document, at least one non-negated profile must match, and no negated profiles may match.

### 复杂类型属性的重写

当复杂类型属性出现在多个profiles中时，重写是以整个替换方式进行。

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

# Spring Boot CLI

## 安装

下载并解压就可以使用了。可以将`bin`目录加到`PATH`环境变量中。

查看版本：`spring --version`。

Spring Boot CLI在`Bash`和`Zsh` Shell中，可以支持自动补全，只要按`Tab`键。

# 日志集成

默认显示`INFO`日志消息。

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

### 静态内容

默认情况，Spring MVC的静态资源将位于类路径中的下列目录之一：

- /META-INF/resources/
- /resources/
- /static/
- /public/

视图模板文件默认位于`/resources/templates`目录下。例如：

```java
@RequestMapping("/foo")
public String foo() {
  return "/admin/foo.btl";  //视图路径也可以不以“/”开始，效果是一样的。return "admin/foo";
}
```

上面代码中视图将会定位到`/resources/templates/admin/foo.ftl`。

FreeMarker视图默认的后缀是`.ftl`，Thymeleaf视图默认的后缀是`.html`。默认后缀可以省略。

视图模板文件中引用的静态资源文件，将默认放在`/resources/static`目录下。假如，`foo.ftl`中有如下引用：

```html
<link href="/css/ztree.css" rel="stylesheet"/>
```

则Spring Boot将会定位到`/resources/static/css/ztree.css`。

资源默认是映射到`/**`路径，可以通过`spring.mvc.static-path-pattern`来自定义映射路径。例如：

```properties
spring.mvc.static-path-patter=/resources/**
```

则上例中的`/resources/static/css/ztree.css`将映射为`/resources/css/ztree.css`。



## WebFlux集成

## Jersey（JAX-RS）集成

## 内嵌Sevlet容器

# 安全集成

# 关系数据库集成

# NoSQL集成

# 缓存集成

# 消息系统集成