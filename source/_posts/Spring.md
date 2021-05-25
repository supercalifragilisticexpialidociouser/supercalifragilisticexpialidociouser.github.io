---
title: Spring
date: 2021-02-01 11:08:44
tags:
---

这个文档记录Spring的最佳实践。

# 开发环境

必需：安装JDK 8 或更新版本，当前推荐使用11。

可选：VSCode + Java Extension Pack + Spring Boot Extension Pack。

# 起步

## 创建项目

直接使用VSCode的扩展生成 Spring Boot 项目（实际上就是使用Spring initializr）。

> 在使用Spring initializr生成项目时，`spring-boot-starter-test`总是被添加到项目，不需要显式选择它。

## 添加依赖

在VSCode中，在“pom.xml”上右击，出现“Add Starters...”菜单，可以调整项目依赖的Starters。

### 继承或导入Spring Boot 的 POM

#### 继承方式

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.4.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

#### 导入方式

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

`spring-boot-starter-parent` 中`spring-boot-maven-plugin`包含了 `<executions>`，并绑定到 `repackage`目标。如果采用导入方式，则需要自己配置`spring-boot-maven-plugin`。

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <executions>
        <execution>
            <goals>
                <goal>repackage</goal>
            </goals>
        </execution>
    </executions>
    <configuration>
        <mainClass>${start-class}</mainClass>
        <executable>true</executable>
        <fork>true</fork>
        <!-- Enable the line below to have remote debugging of your application on port 5005
		<jvmArguments>-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005</jvmArguments>
		-->
    </configuration>
</plugin>
```

使用导入方式时，如果要覆盖spring boot中的依赖时，要将该依赖的定义放在`spring-boot-dependencies`之前。

### Spring Boot 插件

`spring-boot-maven-plugin`插件提供了一些重要的功能：

- 一个用来运行项目的Maven目标：`spring-boot:run`；
- 确保所有依赖库都会包含在可执行的JAR文件中；
- 会在JAR文件中生成一个manifest文件，将引导类声明为可执行JAR的主类。

## 引导应用

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

- `@EnableAutoConfiguration`：会将`spring-boot-autoconfigure.jar/META-INF/spring.factories`中`org.springframework.boot.autoconfigure.EnableAutoConfiguration`键对应的值里的所有配置类加载到IoC容器中。这些配置类都带有条件注解，从而达到自动装配目的；

- `@ComponentScan`：告诉Spring去哪里自动扫描其他组件、配置和服务，并将它们自动注册为Spring Beans。在默认情况下，这个标注将会扫描当前包以及其下任意深度的子包。

> 建议将启动类放在项目的最高层次包中（例如本例中的`com.example.demo`），这样Spring Boot默认从启动类开始自动搜索所在包及其下所有层次包中的类。
>
> 不建议将启动类放在默认包（即没有显式使用`package`声明）中，这样会导致扫描所有jar的所有类。

## 运行

运行项目（开发）：`./mvnw spring-boot:run`。

在生产环境中，仍是使用`java -jar demo-0.0.1-SNAPSHOT.jar`方式运行。

项目启动成功后，打开 http://localhost:8080。

可以按`Ctrl-C` 停止应用。

## 调试

直接找到引导类的`main`方法，右键点击，选择“Debug”。

如果需要远程调试，则可以以下面命令来运行应用：

```bash
$ java -Xdebug -Xrunjdwp:server=y,transport=dt_socket,address=8000,suspend=n \
       -jar target/demo-0.0.1-SNAPSHOT.jar
```

## 打包

如果使用Maven，则可以使用下列命令将项目打包：

```bash
$ mvnw clean package
```

> 需要将 `spring-boot-maven-plugin` 加入 pom.xml中。

打包后，会在`target`目录下生成两个文件，例如：`demo-0.0.1-SNAPSHOT.jar`和`demo-0.0.1-SNAPSHOT.jar.original`。前者是可执行的jar（包含您的已编译类以及代码需要运行的所有jar依赖），后者是未被Spring Boot重打包的原始JAR。

Spring Initializr默认将应用打包成一个可执行的jar包，并且内嵌了Tomcat、Jetty或Undertow支持。因此，不再需要单独下载这些Servlet容器。这主要是为了更方便云部署。

> 要解压这个可执行jar，可以使用命令：`jar xvf target/demo-0.0.1-SNAPSHOT.jar`。

当然，也可以将Spring Boot打包成传统的war包，部署到独立的Servlet容器中。只需在pom.xml中

1. 加上`<packaging>war</packaging>`；
2. 包含一个Web初始化类（通常扩展`SpringBootServletInitializer`类）。

另外，这个war既可部署到支持Servlet 3.0的容器中，也可以直接使用`java -jar`命令来运行。

# 配置

Spring 中有两种不同（但相关）的配置：

- bean 装配：声明在 Spring 应用上下文中创建哪些应用组件以及它们之间如何互相注入的配置。
- 属性注入：设置 Spring 应用上下文中 bean 的值的配置。

## 手动配置

Spring Boot偏爱基于Java的配置，而不是基于XML的配置。

标注有`@Configuration`的类就是Spring Boot的配置类。

不需要将所有配置全部集中在一个配置类中，可以将配置分散在多个配置类中。然后，通过`@ComponentScan`可以自动装配这些配置，也可以通过`@import`来自己指定要导入的其他配置类。

如果要导入基于XML的配置文件，则使用`@ImportResource`标注。

## 自动配置

不管是使用Java还是使用XML的显式配置，只有当Spring不能进行自动配置或需要覆盖默认配置的时候才是必要的。

将`@EnableAutoConfiguration`标注在配置类上，允许Spring Boot根据依赖关系，自动配置Spring应用。例如，如果hsqldb的JAR在你的类路径上，则Spring Boot会自动配置这个JAR与Spring集成。

自动配置是非侵入的，在任何时候，你都可以用自己的配置覆盖自动配置。

如果希望知道自动配置都配置了什么，可以启用“debug”模式或“trace”模式。

使用命令行选项：

```bash
$ java -jar springTest.jar --debug
$ java -jar springTest.jar --trace
```

使用应用属性：

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

Spring 应用属性可以来自多个属性源：（顺序靠前的应用属性优先）

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

Spring 的`Environment`负责从各个属性源拉取属性，并让 Spring 应用上下文中的 bean 可以使用它们。

### 应用属性文件

`SpringApplication`默认从下列位置加载应用属性文件（按优先级从高到低）：

1. 当前目录的`config`子目录（即`file:./config/`）；（外部属性文件）
2. 当前目录（`file:./`）；（外部属性文件）
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

#### 在应用属性中使用PlaceHolder

```properties
app.name=MyApp
app.description=${app.name} is a Spring Boot application
```

> 由于`application.properties` 或 `application.yml`配置文件（也包括`application-xxx.properties`和`application-xxx.yml`）支持PlaceHolder方式的插值表达式（${…}），这会与Maven filtering的插值表达式相冲突，因此如果要在配置文件中应用Maven Filter，则需要将Maven Filter插值表达式的定界符改成其他字符，例如：改成`@…@`。这可以通过Maven插件`maven-resource-plugin`的`delimiters`属性来配置。
>
> 注：Spring Boot的`spring-boot-starter-parent `的POM中已经通过`resource.delimiter`属性来将Maven Filter的插值表达式的定界符设置为`@`了。

#### 自定义属性

除了可以设置Spring预定义的应用属性外，也可以定义一些我们的自定义属性：

```properties
book.name=SpringCloudInAction
```

自定义属性与预定义属性一样，也可以使用`@Value`和`@ConfigurationProperties`注入Bean。

##### 声明应用属性元数据

在IDE中，对于自定义属性会出现警告信息，提示“Unknown property 'xxx'”。这是因为自定义属性缺少元数据。

为了创建自定义属性的元数据，需要在`src/main/resources/META-INF`下创建一个名叫`additional-spring-configuration-metadata.json`文件。例如，为一个名叫“taco.orders.page-size”的属性添加元数据：

```json
{
  "properties": [
    {
      "name": "taco.orders.page-size",
      "type": "java.lang.String",
      "description": "Sets the maximum number of orders to display in a list."
    }
  ]
}
```



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
> servers:
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
  private boolean enabled = false; //允许提供默认值。当应用属性没有配置值时，就使用默认值
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

也可以直接在`MyProperties`上使用`@Component`（`@Controller`、`@Service`等也是可以的），将它注册为一个Bean，而不需要在`@Configuration`类上使用`@EnableConfigurationProperties`标注了：

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

#### 应用属性名与Bean属性名的映射规则

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

#### 校验

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

#### @ConfigurationProperties 与 @Value 的比较

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

### Profiles

Spring Profiles提供了一种方式去将你的应用属性分成多个部分，并且使得它们只在某些环境中可用。

#### PROFILE特定的应用属性文件

PROFILE特定的应用属性文件只在该PROFILE被激活时，才会生效。

PROFILE特定的应用属性文件的命名规则：`application-PROFILE.properties`或`application-PROFILE.yml`。

如果没有显式激活PROFILE，则默认激活`default` PROFILE，即加载`application-default.properties`或`application-default.yml`。

PROFILE特定的应用属性文件与标准应用属性文件（application.properties或application.yml）加载自相同的位置。

#### 单个多Profiles的YAML文档

使用YAML作为应用属性文件时，除了可以像上面那样，为每个Profile分别创建一个独立的YAML文件外，也可以将多个Profiles创建在同一个YAML文件中。使用`---`来分割不同Profiles，并使用`spring.profiles`属性来标识每个Profiles：

```yaml
server:  #第一部分的属性相当于是定义在`application.yml`中，它们是所有profile通用的，或者如果当前激活的profile没有设置这些属性，它们就会作为默认值
	address: 192.168.1.100
---
spring:
  profiles: default  #使用spring.profiles来指定profile
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

#### 未被显式声明的Proflie和默认Profile

未被显式声明的Profile的优先级比显式声明的Profile低，并且不管激活哪个Profile，未被显式声明的Profile中的应用属性**总会**被设置。这也是未被显式声明Profile与默认Profile不同的地方。

默认Profile（`default`）只在没有显式激活任何Profiles时，其中的应用属性才会被设置。

Spring profiles designated by using the `spring.profiles` element may optionally be negated by using the `!` character. If both negated and non-negated profiles are specified for a single document, at least one non-negated profile must match, and no negated profiles may match.

#### 激活Profiles

可以通过`spring.profiles.active`应用属性来激活Profiles。（不推荐）

例如，可以在`application.properties`中配置它们：

```properties
spring.profiles.active=dev,hsqldb  #可以一次激活多个Profiles
```

也可以使用命令行参数`--spring.profiles.active=dev,hsqldb`，或者环境变量`export SPRING_PROFILES_ACTIVE=dev,hsqldb`。（推荐）

用`java -jar`运行应用时，使用`java -Dspring.profiles.active=dev -jar foo.jar`。

还可以使用`spring.profiles.include`应用属性（或者通过在`SpringApplication.run`运行之前，调用`SpringApplication.setAdditionalProfiles()`方法）来指定，当某个Profile激活时，也一起激活的Profiles。

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

另外，如果将Spring应用部署到Cloud Foundry中，将会自动激活一个名为`cloud`的profile。如果你的生产环境是Cloud Foundry，则可以将生产环境相关的属性放到`cloud`profile下。

#### 复杂类型属性的重写

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

#### @Profile

上面的配置profile，要么是通过文件名（例如：`application-PROFILE.yml`），要么是通过属性`spring.profiles`。而`@Profile`提供了通过标注来配置profile的方法，这样我们就可以只在特定profile激活时创建某些bean。

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

# Spring基础

## IoC

### 配置Bean

Spring 提供了三种配置Bean的方式：

- 通过XML配置
- 在Java配置类（带`@Configuration`注解的类）中配置
- 通过注解（例如：`@Component`、`@Service`、`@Repository`和`@Controller`等）配置

三种配置方式可以任意混合使用。通常建议尽可能地使用注解配置，当必须要显式装配Bean的时候（比如，有些源码不是由你来维护的，而当你需要为这些代码装配Bean时），推荐使用类型安全并且比XML更加强大的基于Java配置类的配置。最后，只有当你想要使用便利的XML命名空间，并且在基于Java配置类的配置中没有同样的实现时，才应该使用XML的配置。

#### 通过注解配置

当类被标注了`@Component`（通用组件）、`@Service`（服务）、`@Repository`（数据仓库）、`@Controller`（控制器）、`@Configuration`（配置）注解时，Spring容器会自动扫描（通过`@ComponentScan`注解实现），并将它们注册成受容器管理的Bean。

```java
@Component
public class SomeBean { … }
```

这种方式配置的Bean的默认名称是类名的首字母小写形式，即`someBean`。也可通过注解显式给Bean命名，例如：`@Component("myBean")`。（上面那些注解只能给Bean命名一个名字）

#### 通过Java配置类配置

这种方式是在Java配置类中，`@Bean`注解标注在方法上，则这些方法返回值就是一个Bean实例。

```java
package soundsystem;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class CDPlayerConfig {  
  @Bean
  public CompactDisc randomBeatlesCD() {
    int choice = (int) Math.floor(Math.random() * 4);
    if (choice == 0) {
      return new SgtPeppers();
    } else if (choice == 1) {
      return new WhiteAlbum();
    } else if (choice == 2) {
      return new HardDaysNight();
    } else {
      return new Revolver();
    }
  }
}
```

这种方式配置的Bean的默认名称是方法名，即`randomBeatlesCD`。也可通过注解显式给Bean命名，例如：`@Bean(name="myBean")`。每个Bean可以有一个或多个名字，这些名字在托管bean的容器中必须是唯一的。使用`@Bean`注解可以为Bean命名多个名字，例如：`@Bean({"b1", "b2"})`。

标注`@Bean`的方法只会在首次请求时执行一次，以后每次请求都将被Spring拦截，并从应用上下文中返回相同的Bean。也就是说，默认情况下，Spring中的Bean都是**单例的**。

### 装载Bean

配置好Bean后，还需要将这些Bean装载到Spring容器中。有两种方式装载Bean：

- 启用组件扫描
- 显式导入配置

#### 启用组件扫描

通过注释或配置类配置Bean，需要通过`@ComponentScan`注解来启用组件扫描（默认是不启用的）。

```java
@ComponentScan
public class Foo { … }
```

`@ComponentScan`默认扫描所标注的类（`Foo`）所在的包，以及其下的任意层次的子包，将所有带有`@Component`、`@Service`、`@Repository`、`@Controller`、`@Configuration`等注解的类注册成受容器管理的Bean。

可以通过`@ComponentScan`的`value`属性来显式指定要扫描的包（不扫描它们的子包）：

```java
@ComponentScan("foo.bar")
可以指定多个包：
@ComponentScan({"pkg1", "pkg2"})
```

另外，还可以使用`basePackages`或`basePackageClasses`属性来指定基础包（这种方式会扫描该基础包，以及其下的任意层次的子包）。前者以字符串形式指定基础包，后者通过类或接口的class对象来指定，class对象所在的包即为基础包。而且，两者都可指定多个基础包：

```java
@ComponentScan(basePackages={"pkg1", "pkg2"})

@ComponentScan(basePackageClasses={Foo.class, Bar.class})
```

#### 导入配置

如果没有使用`@ComponentScan`注解启用自动扫描组件，则必须使用`@Import`标注显式列出要装载的组件：

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

还可以使用`@ImportResource`标注将XML配置文件导入配置类：

```java
@Configuration
@Import(abc.FooConfig.class)
@ImportResource("classpath:bar-config.xml")
public class MyConfig {
	…  
}
```

导入一个配置类或XML配置，也会导入该配置类或XML配置导入的配置类或XML配置。因此，建议创建一个根配置类或根XML配置来导入其他顶级配置类或XML配置。这样在实例化应用上下文时，只需要传入根配置类或XML配置文件即可。例如：

```java
ApplicationContext context = new ClassPathXmlApplicationContext("app.xml");
```

否则，就需要列出所有的顶级配置类或XML配置文件：

```java
ApplicationContext context = new AnnotationConfigApplicationContext(abc.FooConfig.class, xyz.BarConfig.class);
```

### 依赖注入

#### 注解注入

在基于注解配置的Bean类中，要注入其他Bean，需要借助`@Autowired`注解。

##### 构造器注入（推荐）

```java
package soundsystem;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Component;

@Component
public class CDPlayer implements MediaPlayer {
  private CompactDisc cd;

  @Autowired
  public CDPlayer(CompactDisc cd) {
    this.cd = cd;
  }

  public void play() {
    cd.play();
  }
}
```

> 从Spring Framework 4.3开始，如果目标bean只定义了一个构造函数，则不再需要在该构造函数上使用`@Autowired`标注。但是，如果有几个构造函数可用，则必须标注一个构造函数，以便指导容器使用哪一个。

被标注的构造器不必是`public`的。

##### 属性注入

```java
public class MovieRecommender {
  private final CustomerPreferenceDao customerPreferenceDao;

  @Autowired
  private MovieCatalog movieCatalog;

  @Autowired
  public MovieRecommender(CustomerPreferenceDao customerPreferenceDao) {
    this.customerPreferenceDao = customerPreferenceDao;
  }

  // ...
}
```

您还可以通过将`@Autowired`应用于指定类型数组或集合（Collections）的字段或方法，则从`ApplicationContext`中将所有匹配指定类型的bean都装配到这个数组或集合中：

```java
@Autowired
private MovieCatalog[] movieCatalogs;

@Autowired
public void setMovieCatalogs(Set<MovieCatalog> movieCatalogs) {
  this.movieCatalogs = movieCatalogs;
}

@Autowired
public void setMovieCatalogs(Map<String, MovieCatalog> movieCatalogs) {
  this.movieCatalogs = movieCatalogs;
}
```

`Map`的键类型必须是`String`，它保存Bean的名称。

另外，在setter方法上标注`@Autowired`，与在属性上标注`@Autowired`效果是一样的。（setter注入实际上是一个方法注入）

```java
public class MovieRecommender {
   private final CustomerPreferenceDao customerPreferenceDao;
   private MovieCatalog movieCatalog;

   @Autowired
	public void setMovieCatalog(MovieCatalog movieCatalog) {
      this.movieCatalog = movieCatalog;
   }

   // ...
}
```

##### 方法注入

```java
@Autowired
public void setCompactDisc(CompactDisc cd) {
  this.cd = cd;
}

@Autowired
public void insertDisc(CompactDisc cd) {
  this.cd = cd;
}

@Autowired
public void prepare(MovieCatalog movieCatalog,
                    CustomerPreferenceDao customerPreferenceDao) {
  this.movieCatalog = movieCatalog;
  this.customerPreferenceDao = customerPreferenceDao;
}
```

方法的每个参数都将被注入（即每个参数都应该是已配置的Bean）。

#### 配置注入

标注`@Bean`注解的方法（简称`@Bean`方法）的每个参数都将被注入。

```java
@Configuration
public class JavaConfig {
	@Bean
   public AnotherService anotherService() { … }
   
   @Bean
   public JavaConfigInjectService javaConfigInjectService(AnotherService anotherService) { … }
}
```

在同一个配置类里，对`@Bean`方法的调用都被视为该Bean的注入：（实际上每次调用`@Bean`方法都将被Spring拦截，只在首次调用时真正执行方法，后面将从IoC容器中直接返回相同的Bean）

```java
@Configuration
public class JavaConfig {
	@Bean
   public AnotherService anotherService() { … }
   
   @Bean
   public JavaConfigInjectService javaConfigInjectService() {
      return new JavaConfigInjectService(anotherService());
   }
}
```

另外，注解配置的Bean可以直接注入给使用Java配置的Bean，反之亦然。

#### 注入规则

##### 默认注入规则

首先，通过 Bean 的名称来自动注入。当 Bean 名称不满足条件时，容器会根据 Bean 的类型进行自动注入。

##### `@Primary`

当全局有多个同类型的Bean时，容器将无法自动注入。这时，可通过`@Primary`注解来标记需要优先使用的Bean。

例如有两个同类型的Bean：

```java
@Bean
public AnotherService anotherService() { … }

@Bean
@Primary
public AnotherService primaryAnotherService() { … }
```

此时这两个Bean名称分别为 `anotherService` 和 `primaryAnotherService` 。如果在注入的地方不使用这两个名称，那么就会按照 Bean 的类型自动注入。因为 `primaryAnotherService` 注解了 `@Primary` ，所以使用 `primaryAnotherService` 这个 Bean 注入：

```java
@Component
public class UsePrimaryService {
   private AnotherService service;
   
   public UsePrimaryService(AnotherService service) { … }
   
   …
}
```

##### `@Qualifier`

可以使用 `@Qualifier` 注解直接指定需要使用哪个 Bean：

```java
@Component
public class UseQualifierService {
   private AnotherService service;
   
   public UseQualifierService(@Qualifier("anotherService") AnotherService service) { … }
   
   …
}
```

 `@Qualifier` 注解也可以标注在属性上：

```java
@Component
public class UseQualifierService {
   @Autowired
   @Qualifier("anotherService")
   private AnotherService service;
   
   …
}
```

### Bean的作用域

Spring Bean有多种作用域：

- 单例（`singleton`）：在整个应用中，只创建Bean的一个实例。
- 原型（`prototype`）：每次注入或者通过Spring应用上下文获取的时候，都会创建一个新的Bean实例。
- 会话（`session`）：在Web应用中，为每个会话创建一个Bean实例。
- 全局会话（`globalSession`）：类似于会话作用域，但仅在Portlet的Web应用中使用。
- 请求（`request`）：在Web应用中，为每个请求创建一个Bean实例。

在默认情况下，Spring应用上下文中所有Bean都是单例的。可以通过`@Scope`标注为Bean显式选择其他的作用域：

```java
@Component
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public class Notepad {…}
```

或者

```java
@Bean
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)
public Notepad notepad() {
  return new Notepad();
}
```

会话和请求作用域只能在Web应用中使用，并且要在web.xml中做如下设置：

```xml
<listener>
	<listener-class>
  	org.springframework.web.context.request.RequestContextListener
  </listener-class>
</listener>
```



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

## 内嵌Web服务器

对于Servlet栈的Spring Boot应用，`spring-boot-starter-web`默认内嵌Tomcat。另外，也可内嵌Jetty、Undertow。

对于反应栈（reactive）的Spring Boot应用， `spring-boot-starter-webflux`默认内嵌Netty。还可以内嵌Tomcat、Jetty、Undertow。

假设使用Undertow服务器：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <!-- Exclude the Tomcat dependency -->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>
<!-- Use Jetty instead -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-undertow</artifactId>
</dependency>
```

## 处理错误

### 服务端错误页

> 前端的错误页在前端项目中定义，这里只讨论服务端的错误页。

#### 静态错误页

在Nginx等代理服务器上配置，用于服务端应用无法访问时显示的错误页面。

#### 动态错误页

使用[Thymeleaf](https://www.thymeleaf.org/)模板定义，位于`src/main/resources/templates/error.html`。用于直接访问服务端应用（即绕开前端）时，显示服务端错误。

### API错误

使用 [Zalando’s Problem Spring Web library](https://github.com/zalando/problem-spring-web)框架处理Spring MVC REST响应错误（基于JSON）。

# 安全

Spring Security为[身份验证](https://docs.spring.io/spring-security/site/docs/5.4.5/reference/html5/#authentication)，授权和针对[常见漏洞的](https://docs.spring.io/spring-security/site/docs/5.4.5/reference/html5/#exploits)防护提供了全面的支持。

## 安装

pom.xml：

```xml
<dependencies>
    <!-- ... other dependency elements ... -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-security</artifactId>
    </dependency>
</dependencies>
```

