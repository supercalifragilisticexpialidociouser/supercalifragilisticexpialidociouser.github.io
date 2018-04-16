---
title: WildflySwarm
date: 2018-04-16 09:41:59
tags: [2018.4.1]
---

# 入门

## 创建项目

### POM

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>com.example</groupId>
  <artifactId>demo</artifactId>
  <name>WildFly Swarm Example</name>
  <version>1.0.0-SNAPSHOT</version>
  <packaging>war</packaging>

  <properties>
    <version.wildfly.swarm>2018.4.1</version.wildfly.swarm>
    <maven.compiler.source>1.8</maven.compiler.source>
    <maven.compiler.target>1.8</maven.compiler.target>
    <failOnMissingWebXml>false</failOnMissingWebXml>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
  </properties>

  <dependencyManagement>
    <dependencies>
      <dependency>
        <groupId>org.wildfly.swarm</groupId>
        <artifactId>bom-all</artifactId>
        <version>${version.wildfly.swarm}</version>
        <scope>import</scope>
        <type>pom</type>
      </dependency>
    </dependencies>
  </dependencyManagement>

  <build>
    <finalName>demo</finalName>
    <plugins>
      <plugin>
        <groupId>org.wildfly.swarm</groupId>
        <artifactId>wildfly-swarm-plugin</artifactId>
        <version>${version.wildfly.swarm}</version>
        <executions>
          <execution>
            <goals>
              <goal>package</goal>
            </goals>
          </execution>
        </executions>
      </plugin>
    </plugins>
  </build>

  <dependencies>
    <!-- Java EE 7 dependency -->
    <dependency>
      <groupId>javax</groupId>
      <artifactId>javaee-api</artifactId>
      <version>7.0</version>
      <scope>provided</scope>
    </dependency>
    <!-- WildFly Swarm Fractions -->
    <dependency>
      <groupId>org.wildfly.swarm</groupId>
      <artifactId>jaxrs</artifactId>
    </dependency>
  </dependencies>
</project>
```

### 启动类

可选。

### 创建Rest端点

```java
package com.example.demo.rest;


import javax.ws.rs.Path;
import javax.ws.rs.core.Response;
import javax.ws.rs.GET;
import javax.ws.rs.Produces;


@Path("/hello")
public class HelloWorldEndpoint {

	@GET
	@Produces("text/plain")
	public Response doGet() {
		return Response.ok("Hello from WildFly Swarm!").build();
	}
}
```

## 运行

直接右击启动类，选择以`Java Application`方式运行。

也可以手工打包成可执行Jar，然后以`java -jar`方式运行。例如：

```bash
$ java -jar target/demo-swarm.jar
```

还可以使用Wildfly-swarm插件来运行：

```bash
$ mvn wildfly-swarm:run
```

然后，打开浏览器，访问：<http://localhost:8080/hello>，就可以看到结果了。

可以按`Ctrl-C`或者执行`mvn wildfly-swarm:stop`关闭应用。

## 调试

## 使用热部署

## 打包

## 常用工具