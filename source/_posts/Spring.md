---
title: Spring
date: 2021-02-01 11:08:44
tags:
---

这个文档记录Spring的最佳实践。

# 开发环境

必需：安装JDK 8 或更新版本，当前推荐使用11。

可选：VSCode + Java Extension Pack + Spring Boot Extension Pack。

# 管理 Spring Boot 项目

创建项目：直接使用VSCode的扩展生成 Spring Boot 项目。

添加Spring Boot Starter：在VSCode中，在“pom.xml”上右击，出现“Add Starters...”菜单，可以调整项目依赖的Starters。

引导应用：引导类是使用`@SpringBootApplication`标注的类，并且它有一个`main`方法。它是通过可执行JAR来运行应用所必需的。

运行项目（开发）：`./mvnw spring-boot:run`，然后打开http://localhost:8080/xxx。在生产环境中，仍是使用`java -jar xxx.jar`方式运行。

调试：直接找到主类，右键点击，选择“Debug”。

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

