---
title: JHipster
date: 2019-01-16 14:34:09
tags: [6.0.1]
---

JHipster是一个开发平台，用于生成、开发和部署Spring Boot + Angular / React的Web应用程序和Spring微服务。

# 技术栈

## 前端

- [Angular](https://angular.io/) 或 [React](https://reactjs.org/) 或 Vue
- 响应式网页设计：[Twitter Bootstrap](https://getbootstrap.com/)
- [HTML5 Boilerplate](http://html5boilerplate.com/)
- 兼容现代浏览器（Chrome、FireFox、Microsoft Edge ......）
- 全面的国际化支持
- [Sass](https://www.npmjs.com/package/node-sass)
- Spring Websocket
- [Thymeleaf](http://www.thymeleaf.org/)模板引擎
- [NPM](https://www.npmjs.com/get-npm)
- [Webpack](https://webpack.js.org/)
- 测试： [Jest](https://facebook.github.io/jest/) 和 [Protractor](http://www.protractortest.org/)

## 服务端

- [Spring Boot](https://projects.spring.io/spring-boot/)
- 构建工具：[Maven](https://maven.apache.org/) 或 [Gradle](http://www.gradle.org/)
- [Spring Security](https://docs.spring.io/spring-security/site/index.html)
- “development”和“production” profiles
- [Spring MVC REST](https://spring.io/guides/gs/rest-service/) + [Jackson](https://github.com/FasterXML/jackson)
- Spring Websocket
- [Spring Data JPA](https://projects.spring.io/spring-data-jpa/) + Bean Validation
- 数据库更新：[Liquibase](http://www.liquibase.org/)
- [Elasticsearch](https://github.com/elastic/elasticsearch)
- [MongoDB](https://www.mongodb.org/) 和 [Couchbase](https://www.couchbase.com/)
- [Cassandra](https://cassandra.apache.org/)
- [Kafka](https://kafka.apache.org/)
- 监控：[Metrics](http://metrics.dropwizard.io/) 和 [the ELK Stack](https://www.elastic.co/products)
- 缓存：[ehcache](http://ehcache.org/)（本地缓存）, [hazelcast](http://www.hazelcast.com/) 或 [Infinispan](http://infinispan.org/)
- 优化的静态资源（gzip过滤器，HTTP缓存头）
- 日志管理：[Logback](http://logback.qos.ch/)
- 连接池：[HikariCP](https://github.com/brettwooldridge/HikariCP)
- 构建标准WAR文件或可执行JAR文件
- 完整的Docker和Docker Compose支持
- 支持所有主要云提供商：AWS，Cloud Foundry，Heroku，Kubernetes，OpenShift，Azure，Docker ......

## 微服务

- HTTP路由[Netflix Zuul](https://github.com/Netflix/zuul) 或 [Traefik](https://traefik.io/)
- 服务发现[Netflix Eureka](https://github.com/Netflix/eureka) 或 [HashiCorp Consul](https://www.consul.io/)

# 开发环境

## JHipster Online

[JHipster Online](https://start.jhipster.tech/)允许您轻松生成JHipster应用程序，而无需安装JHipster。

## 安装JHipster

1. 安装Java 11（实际上还是需要Java 8才能构建成功）
2. 安装Node.js，并将NPM更新到最新版本：`npm install -g npm`
3. 安装JHipster：`npm install -g generator-jhipster`
4. 如果要使用JHipster Marketplace，还要安装Yeoman：`npm install -g yo`
5.  如果要使用[JHipster upgrade sub-generator](https://www.jhipster.tech/upgrading-an-application/)，还要安装[Git](https://git-scm.com/)。也推荐安装可视化的Git客户端[SourceTree](https://www.sourcetreeapp.com/)

注意：构建工具Maven或Gradle可以不用手工安装，当执行`./mvnw`或`./gradlew`命令时，会自动安装。

## IDE准备

推荐使用Visual Studio Code。然后安装如下插件：

- Java Extension Pack
- Spring Initializr Java Support
- Spring Boot Tools

> 另外，也可以安装`Java IDE Pack`插件，它除了包含了上面所有插件外，还包含一些额外插件。

## 安装Docker及相关工具

主要安装：

- [Docker](https://docs.docker.com/installation/#installation)
- [Docker Compose](https://docs.docker.com/compose/install)

如果是在Windows或Mac OS下，建议安装[Docker Desktop](https://www.docker.com/products/docker-desktop)，它包括上述工具。

# 生成应用

```bash
$ mkdir myapplication
$ cd myapplication/
$ jhipster
```

然后，按照提示一步步操作。

# 开发应用

## CORS跨域

在JHipster中要启用跨域支持，需要：

1. 在`application-*.yml`或`application.yml`中添加如下配置：

   ```yaml
   jhipster:
     …
     cors:
       allowed-origins: "*"
       allowed-methods: '*'
       allowed-headers: "*"
       exposed-headers: 'Authorization,Link,X-Total-Count'
       allow-credentials: true
       max-age: 1800
   ```

2. 在`WebConfigurer.java`的`corsFilter()`方法中注册需要跨域的路径，默认已经注册了`/api/**`、`/management/**`和`/v2/api-docs`路径。

# 构建应用

可通过如下命令打包应用，并使用[Jib](https://github.com/GoogleContainerTools/jib)生成一个Docker镜像：

```bash
$ ./mvnw clean package -Pprod verify jib:dockerBuild
```

或者：

```bash
$ ./gradlew -Pprod bootWar jibDockerBuild
```

这会生成一个Docker镜像，镜像名在Linux上是应用名（例如`myapp`），而在Windows上是`jhipster`。



# 部署应用

## 直接运行应用（用于调试）

在项目根目录下运行：

```bash
$ ./mvnw
```

或者：

```bash
$ ./gradlew
```

应用启动完成后，在浏览器中打开：<http://localhost:8081>。

注意：在执行上述命令之前，应该先启动应用依赖的第三方服务，例如数据库等。

## 使用Docker Compose部署单个应用（本地部署）

JHipster生成项目时，也同时生成如下文件（位于文件夹`src/main/docker/`中）：

- 用于构建Docker镜像并在容器内运行应用程序的Dockerfile；
- 多个Docker Compose配置以帮助您使用第三方服务（例如数据库）运行应用程序。

通过执行如下Docker Compose命令：

```bash
$ docker-compose -f src/main/docker/app.yml up -d
```

将启动您的应用程序及其依赖的服务（数据库、搜索引擎、JHipster Registry ...）。

`-d`表示在后台运行。

要停止并移除所有容器，可以运行：

```bash
$ docker-compose -f src/main/docker/app.yml down
```

## 使用Docker Compose部署多个应用

首先，你所有应用必须位于同一个目录下（例如：`~/jee/`）。

然后：

```bash
$ cd ~/jee
$ mkdir docker-compose   （目录可随意）
$ cd docker-compose
$ jhipster docker-compose
```

子生成器将询问您希望在架构中使用哪些应用程序，以及是否要使用ELK或Prometheus设置监视。然后，就将生成全局Docker Compose配置。

你只需要运行：`docker-compose up`命令，就可以启动所有应用。

要停止并移除所有容器，可以运行：

```bash
$ docker-compose down
```

# 升级应用

# JDL

前面都是通过命令行来管理应用，JHipster还提供了一个JDL领域特定语言来简化应用管理。

JDL文件是一个文本文件，它的扩展名可以是`.jdl`或`.jh`。

可以使用在线的[JDL-Studio](https://start.jhipster.tech/jdl-studio/) 工具或使用VSCode的JHipster JDL插件来编写JDL文件。JDL文件名可随意取。

## 创建应用

```bash
$ jhipster import-jdl my_file.jdl
```

这会根据JDL文件中的`baseName`值，在当前目录下创建一个同名目录作为项目根目录，在它面生成项目。

## 更新实体

如果只是想更新发生改变的实体，则需要在项目根目录（假设为`foo`）下执行下面命令：

```bash
$ cd foo
$ jhipster import-jdl my_file.jdl
```

完成导入后，会自动提交代码。