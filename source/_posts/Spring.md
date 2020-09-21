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
         -d dependencies=web,data-jpa \
         -d jvmVersion=11 \
         -o demo.zip
  ```

  参数的说明参见：https://github.com/spring-io/initializr/ 。

- 在命令行中使用Spring Boot CLI：是Spring Boot 命令行工具，它可以运行Groovy脚本，通过它也可以很方便地开发Spring Boot项目。
- 使用 VSCode + Java Extension Pack + Spring Boot Extension Pack：轻量级开发环境。
- 使用Spring Tool Suite（STS）：一个基于Eclipse的IDE，可以很方便地开发Spring Boot项目。
- 使用IntelliJ IDEA。

另外，也可以使用[JHipster](https://www.jhipster.tech/)：是一个生成、开发和部署Spring Boot + Angular/React的Web应用和Spring微服务的开发平台。

> Spring Boot项目可以直接被打包成一个可执行的jar包，并且内嵌了Tomcat、Jetty和Undertow支持。因此，不再需要单独下载这些Servlet容器。当然，也可以将Spring Boot打包成传统的war包，部署到独立的Servlet容器中。