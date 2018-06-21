---
title: SpringCloud
date: 2018-06-01 22:01:12
tags: [Finchley.RELEASE]
---

# 简介

Spring Cloud是建立在Spring Boot基础之上，并添加了一些系统中所有组件都会使用或偶尔需要的功能。 

> 如果由于“非法密钥大小”而导致异常，并且您使用Sun的JDK，则需要安装Java加密扩展（JCE）无限制强制管辖权策略文件。有关更多信息，请参阅以下链接：
>
> [Java 6 JCE](http://www.oracle.com/technetwork/java/javase/downloads/jce-6-download-429243.html)
>
> [Java 7 JCE](http://www.oracle.com/technetwork/java/javase/downloads/jce-7-download-432124.html)
>
> [Java 8 JCE](http://www.oracle.com/technetwork/java/javase/downloads/jce8-download-2133166.html)
>
> 将文件解压缩到您使用的JRE / JDK x64 / x86版本的`JDK_HOME/jre/lib/security`文件夹中。 

# 应用上下文服务：Spring Cloud Context

Spring Cloud Context为Spring云应用程序的`ApplicationContext`提供实用程序和特殊服务（bootstrap上下文、加密、刷新范围和环境端点）。 

Spring Cloud应用程序通过创建“bootstrap”上下文来运行，该上下文是主应用程序的父上下文。 它负责从外部源加载配置属性，并负责解密本地外部配置文件中的属性。 

默认情况下，引导属性（不是`bootstrap.properties`，而是在引导阶段加载的属性）以高优先级添加，因此它们不能被本地配置覆盖。 

通过引导上下文添加到应用程序的属性源通常是“远程的”（例如，来自Spring Cloud Config Server）。默认情况下，它们不能在本地覆盖。 如果您想覆盖这些远程属性，则远程属性源必须通过设置`spring.cloud.config.allowOverride = true`来授予其权限（无法在本地进行设置） 。一旦设置了该标志，两个更细粒度的设置将控制远程属性相对于系统属性和应用程序的本地配置的位置： 

- `spring.cloud.config.overrideNone=true`  ：从任何本地属性源均可覆盖 。
- `spring.cloud.config.overrideSystemProperties=false` ：只有系统属性、命令行参数和环境变量（但不是本地配置文件）可以覆盖远程设置。 

引导上下文的配置文件默认是`bootstrap.properties`或`bootstrap.yml`，而不是使用`application.yml`或`application.properties`，后者是主应用的配置文件。

例如：bootstrap.yml

```yaml
spring:
  application:
    name: foo
  cloud:
    config:
      uri: ${SPRING_CONFIG_URI:http://localhost:8888}
```

如果您的应用程序需要来自服务器的任何特定于应用程序的配置，那么设置`spring.application.name`（在bootstrap.yml或application.yml中）是一个不错的主意。 

引导上下文配置文件名可以通过 `spring.cloud.bootstrap.name` 来定制（默认是`bootstrap`）。引导上下文配置文件的位置可以通过`spring.cloud.bootstrap.location`  来定制（默认是空）。这些属性的行为与具有相同名称的`spring.config.*`变体类似。 

引导上下文配置文件也可以有Profiles，例如：`bootstrap-dev.properties`用于`dev` Profile。

您可以通过设置`spring.cloud.bootstrap.enabled = false`（例如，在系统属性中）完全禁用引导过程。 

## 应用上下文的层次结构

如果您从`SpringApplication`或`SpringApplicationBuilder`构建应用程序上下文，那么Bootstrap上下文将作为该上下文的父项添加。 Bootstrap上下文是您自己能够创建的最高级应用上下文的父级。 

Spring的一个特性是子级上下文继承了父级的属性源和配置文件，因此与没有使用Spring Cloud Config构建的相同上下文相比，“主”应用程序上下文包含了额外属性源。 额外的属性源：

- “bootstrap”：如果在Bootstrap上下文中找到任何`PropertySourceLocators`，并且它们具有非空属性，则可选的`CompositePropertySource`将呈现高优先级。 
- “applicationConfig”：`classpath:bootstrap.yml。`此外，如果Spring Profiles处于活动状态，则还包括相关文件。 `bootstrap.yml` （或`.properties`）用于配置Bootstrap上下文，它的优先级低于`application.yml `（或`.properties`），以及作为创建Spring Boot应用程序过程的常规部分添加到子项的任何其他属性源。 

> 注意：引导属性具有高优先级，但 `bootstrap.yml` （或`.properties`）中的属性优先级很低，常用于设置默认值。

您可以通过设置您创建的任何`ApplicationContext`的父上下文来扩展上下文层次结构。例如，通过使用它拥有的接口或`SpringApplicationBuilder`便捷方法（`parent()`、`child()`和`sibling()`）。

层次结构中的每个上下文都有自己的“bootstrap”属性源（可能是空的），以避免无意中将值从父级推到他们的后代。  

如果有配置服务器，则层次结构中的每个上下文也可以（原则上）具有不同的`spring.application.name`，并因此具有不同的远程属性源。 

正常情况下，Spring应用上下文行为规则应用于属性解析：来自子上下文的属性覆盖父类中的同名属性（基于名称和属性源名称）。 

# 公共抽象：Spring Cloud Commons

Spring Cloud Commons是一组用于不同Spring Cloud实现（如Spring Cloud Netflix和Spring Cloud Consul）的抽象和常用类。 

# 分布式配置中心：Spring Cloud Config

# 服务治理：Spring Cloud Eureka

Spring Cloud Eureka是基于Netflix Eureka做了二次封装，主要负责完成微服务架构中的服务治理功能（即微服务实例的自动化注册和发现）。

服务注册：每个服务单元（Eureka客户端）向注册中心登记自己提供的服务，并周期性地发送心跳来更新它有服务租约，未及时更新服务租约的服务被认为是不可用的，将从服务清单中剔除，从而达到排除故障服务的效果。

服务注册中心会维护类似下面的一个服务清单：

| 服务名 | 位置                                                       |
| ------ | ---------------------------------------------------------- |
| 服务A  | 192.168.0.100:8000, 192.168.0.101:8000                     |
| 服务B  | 192.168.0.100:9000, 192.168.0.101:9000, 192.168.0.102:9000 |

Eureka服务器没有后端存储，但注册表中的服务实例必须发送心跳信号以保持其注册是最新的（所以这可以在内存中完成）。客户端还有一个Eureka注册的内存缓存（因此他们不必每次请求注册服务都去注册中心）。 

服务发现：服务间的调用不再通过指定具体的实例地址来实现，而是通过服务名向注册中心查询服务的信息（并把它们缓存到本地并周期性地刷新服务状态），据此向服务的某个实例发起请求。

## 搭建服务注册中心（Eureka服务端）

### 引入依赖

pom.xml：

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-dependencies</artifactId>
      <version>Finchley.RELEASE</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
<dependencies>
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
  </dependency>
</dependencies>
```

### 启用服务注册中心

通过标注`@EnableEurekaServer`来启用服务注册中心：

```java
@SpringBootApplication
@EnableEurekaServer
public class Application {
  public static void main(String[] args) {
    new SpringApplicationBuilder(Application.class).web(true).run(args);
  }
}
```

### 配置注册中心

#### 独立模式

默认情况下，每个Eureka服务器也是Eureka客户端，并且需要（至少一个）服务URL来定位对等端。如果您没有提供该服务，该服务将运行并工作，但它会填满您的日志，并带来很多关于无法向对等端注册的噪音。 

因此，在独立模式下要禁止它将自己作为客户端注册，只需要配置如下应用属性：

```properties
server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false     #不向注册中心注册自己
    fetchRegistry: false          #不需要向注册中心查询服务
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

> 注意：在独立模式下，`serviceUrl`指向与本地实例相同的主机。 

#### 对等模式

通过运行多个Eureka注册中心实例并要求它们相互注册，Eureka可以变得更加灵活和可用。实际上，这是默认行为，所以您只需将一个有效的`serviceUrl`添加到对等体即可，如下例所示：

application.yml：

```yaml
---
spring:
  profiles: peer1
eureka:
  instance:
    hostname: peer1
  client:
    serviceUrl:
      defaultZone: http://peer2/eureka/

---
spring:
  profiles: peer2
eureka:
  instance:
    hostname: peer2
  client:
    serviceUrl:
      defaultZone: http://peer1/eureka/
```

如果您在知道自己的主机名的机器上运行（默认情况下，它使用`java.net.InetAddress`查找），则不需要`eureka.instance.hostname`。 

默认情况下，使用主机名来向注册中心注册。如果Java无法确定主机名，则将使用IP地址来注册。也可以显式设置使用IP地址来注册，只要设置`eureka.instance.preferIpAddress=true`（默认是`false`）。

您可以将多个对等端添加到系统，并且只要它们两两至少通过一个边相互连接，就可以在它们之间同步注册。

### 启动注册中心

以Spring Boot方式启动该注册中心。

访问Eureka注册中心主页： http://localhost:8761/、http://peer1、http://peer2

Eureka提供的HTTP端点在 `/eureka/*` 下。

## Eureka客户端

Eureka客户端使得Spring Boot应用可以向注册中心发布自己提供的服务。

### 引入依赖

pom.xml：

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-dependencies</artifactId>
      <version>Finchley.RELEASE</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
<dependencies>
  <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
  </dependency>
</dependencies>
```

### 启用DiscoveryClient

```java
@SpringBootApplication
@EnableDiscoveryClient
public class Application {
	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```

> `@EnableDiscoveryClient`不是必需的。只要在类路径上存在`DiscoveryClient`实现，Spring Boot应用程序就会向注册中心注册。 

### 配置Eureka客户端

application.properties：

```properties
spring.application.name=hello
eureka.client.serviceUrl.defaultZone=http://localhost:8761/eureka/
```

在对等模式下，defaultZone可以设置为：`eureka.client.serviceUrl.defaultZone=http://peer1/eureka/,http://peer2/eureka/`

### 注入DiscoveryClient

在控制器中注入`DiscoveryClient`对象：

```java
@RestController
public class HelloController {
  @Autowired
  private DiscoveryClient client;
  
  @RequestMapping(value="/hello", method=RequestMethod.GET)
  public String index() {
    …
  }
}
```



# 客户端负载均衡：Spring Cloud Ribbon

# 服务容错保护：Spring Cloud Hystrix

# 声明式服务调用：Spring Cloud Feign

# API网关：Spring Cloud Zuul

# 消息总线：Spring Cloud Bus

# 消息驱动：Spring Cloud Stream

# 分布式服务跟踪：Spring Cloud Sleuth