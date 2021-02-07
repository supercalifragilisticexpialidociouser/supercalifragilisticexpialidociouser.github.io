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

运行项目（开发）：`./mvnw spring-boot:run`，然后打开http://localhost:8080/xxx。在生产环境中，仍是使用`java -jar xxx.jar`方式运行。

调试：直接找到主类，右键点击，选择“Debug”。

# MVC

需要依赖：

```xml
<dependency>
	<groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

