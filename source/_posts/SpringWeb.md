---
title: SpringWeb
date: 2018-06-01 21:57:58
tags: [5.1.2]
---

# Spring MVC

Spring Web MVC是构建在Servlet API上的Web框架。

## Spring MVC架构

![Spring MVC架构](SpringWeb/Spring-MVC-架构.png)

1. 客户端（如浏览器）发出一个HTTP请求，Web应用服务器接收到这个请求。如果匹配Spring MVC的前端控制器`DispatcherServlet`的URL映射模式，则Web容器将该请求转交给`DispatcherServlet`处理。

2. `DispatcherServlet`接收到这个请求后，将根据请求的信息（包括URL、HTTP方法、请求报文头、请求参数、Cookie等）及处理器映射（HandlerMapping，可看作路由控制器）的配置找到处理请求的处理器（Handler）。

   > 注意：在Spring MVC中并没有定义一个Handler接口，实际上，任何一个类都可以成为请求处理器。

3. 当`DispatcherServlet`找到对应当前请求的处理器后，通过`HandlerAdapter`对处理器进行封装，再以统一的适配器接口调用处理器。

4. 处理器完成业务逻辑的处理后（实际上，设计良好的控制器本身只处理很少甚至不处理工作，而是将业务逻辑委托给一个或多个服务对象进行处理），将返回一个`ModelAndView`给`DispatcherServlet`。`ModelAndView`包含了逻辑视图名和模型数据信息。

5. `DispatcherServlet`借由视图解析器（ViewResolver）来将逻辑视图名匹配为一个特定的视图实现（如JSP）。

6. `DispatcherServlet`将模型数据交付给上一步得到的视图，并由该视图进行渲染。

7. 渲染输出（如HTML、JSON、图片等）最终通过响应对象传递给客户端。

## 搭建Spring MVC

### 配置DispatcherServlet

传统配置DispatcherServlet的方式是在web.xml文件中进行配置。借助于Servlet 3规范和Spring 3.1的功能增强，现在更推荐在Java中配置DispatcherServlet。

#### 传统的web.xml配置

web.xml：

```xml
<web-app>
  <!-- 加载后端的中间层和数据层组件的应用上下文（根应用上下文） -->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <context-param><!-- ContextLoaderListener加载的Spring配置文件 -->
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/app-context.xml</param-value>
  </context-param>

  <!-- 加载Web组件的应用上下文 -->
  <servlet>
    <servlet-name>app</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <!-- 如果缺省init-param元素，则默认加载 /WEB-INF/Servlet名称-servlet.xml （这里是 /WEB-INF/app-servlet.xml）的Spring配置文件。这里将contextConfigLocation显式设置为空，表示不加载Web组件的应用上下文 -->
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value></param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>app</servlet-name>
    <url-pattern>/app/*</url-pattern>
  </servlet-mapping>
</web-app>
```

一个web.xml可以配置多个`DispatcherServlet`，通过不同的`<servlet-mapping>`配置，让每个`DispatcherServlet`处理不同的请求。

另外，还可以在web.xml中配置使用基于Java的配置，只需要告诉`ContextLoaderListener`和`DispatcherServlet`使用`AnnotationConfigWebApplicationContext`，它是`WebApplicationContext`的实现类，它会加载Java配置类，而不是使用XML配置。要实现这种配置，可以设置`contextClass`参数：

```xml
<web-app>
  <!-- 加载后端的中间层和数据层组件的应用上下文（根应用上下文） -->
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <context-param><!-- 指定根配置类 -->
    <param-name>contextConfigLocation</param-name>
    <param-value>foo.bar.RootConfig</param-value>
  </context-param>
  <context-param><!-- 使用Java配置类 -->
  	<param-name>contextClass</param-name>
    <param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
  </context-param>

  <!-- 加载Web组件的应用上下文 -->
  <servlet>
    <servlet-name>app</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param><!-- 指定DispatcherServlet配置类 -->
      <param-name>contextConfigLocation</param-name>
      <param-value>foo.bar.WebConfig</param-value>
    </init-param>
    <init-param><!-- 使用Java配置类 -->
    	<param-name>contextClass</param-name>
    	<param-value>org.springframework.web.context.support.AnnotationConfigWebApplicationContext</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>app</servlet-name>
    <url-pattern>/app/*</url-pattern>
  </servlet-mapping>
</web-app>
```



#### 使用Java配置

在Servlet 3环境中，容器会在类路径中查找实现了`javax.servlet.ServletContainerInitializer`接口的类，如果能发现的话，就会用它来配置Servlet容器上下文。

Spring提供了这个接口的实现——`SpringServletContainerInitializer`，这个类会查找实现`WebApplicationInitializer`接口的类，并将配置的任务交给它们来完成。因此，可通过实现`WebApplicationInitializer`接口来配置`DispatcherServlet`和Spring应用上下文。当部署到Servlet 3.0容器中时，容器会自动发现它，并用它来配置Servlet上下文。Spring应用上下文会位于应用程序的Servlet上下文之中：

```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {
  @Override
  public void onStartup(ServletContext servletCxt) {
    // Load Spring web application configuration
    AnnotationConfigWebApplicationContext ac = new AnnotationConfigWebApplicationContext();
    ac.register(AppConfig.class);
    ac.refresh();

    // Create and register the DispatcherServlet
    DispatcherServlet servlet = new DispatcherServlet(ac);
    ServletRegistration.Dynamic registration = servletCxt.addServlet("app", servlet);
    registration.setLoadOnStartup(1);
    registration.addMapping("/app/*");
  }
}
```

Spring 3.2引入了一个便利的`WebApplicationInitializer`基础实现，也就是`AbstractAnnotationConfigDispatcherServletInitializer` ，也可以扩展这个类来配置`DispatcherServlet`和Spring应用上下文：

```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
  @Override
  protected Class<?>[] getRootConfigClasses() {
    return new Class<?>[] { AppConfig.class };
  }

  @Override
  protected Class<?>[] getServletConfigClasses() {
    return null;
  }

  @Override
  protected String[] getServletMappings() {
    return new String[] { "/app/*" };
  }
}
```

##### 自定义DispatcherServlet配置

可以通过重写`customizeRegistration`方法（属于`AbstractDispatcherServletInitializer`类），利用它的参数`ServletRegistration.Dynamic`的方法来自定义`DispatcherServlet`配置。

例如，调用`setLoadOnStartup`方法设置`load-on-startup`优先级；调用`setInitParameter`方法设置初始化参数等。

### 上下文的层次结构

对于许多应用程序，拥有单个`WebApplicationContext`就足够。但Spring应用上下文之间可以设置为父子级关系，以实现更好的解耦。子上下文可以访问父上下文中的Bean，反之则不行。

在Spring MVC中，通常包含两个应用上下文，并且`DispatcherServlet`加载的Web层应用上下文，将作为`ContextLoaderListener`加载的后端应用上下文的子上下文。

![mvc-context-hierarchy](SpringWeb/mvc-context-hierarchy.png)

下面的示例配置了这种WebApplicationContext层次结构：

```java
public class MyWebAppInitializer extends AbstractAnnotationConfigDispatcherServletInitializer {
  @Override
  protected Class<?>[] getRootConfigClasses() {
    return new Class<?>[] { RootConfig.class };
  }

  @Override
  protected Class<?>[] getServletConfigClasses() {
    return new Class<?>[] { WebConfig.class };
  }

  @Override
  protected String[] getServletMappings() {
    return new String[] { "/app/*" };
  }
}
```

或者：

```java
public class MyWebAppInitializer implements WebApplicationInitializer {
  @Override
  public void onStartup(ServletContext container) {
    // Create the 'root' Spring application context
    AnnotationConfigWebApplicationContext rootContext =
      new AnnotationConfigWebApplicationContext();
    rootContext.register(RootConfig.class);

    // Manage the lifecycle of the root application context
    container.addListener(new ContextLoaderListener(rootContext));

    // Create the dispatcher servlet's Spring application context
    AnnotationConfigWebApplicationContext dispatcherContext =
      new AnnotationConfigWebApplicationContext();
    dispatcherContext.register(WebConfig.class);

    // Register and map the dispatcher servlet
    ServletRegistration.Dynamic dispatcher =
      container.addServlet("dispatcher", new DispatcherServlet(dispatcherContext));
    dispatcher.setLoadOnStartup(1);
    dispatcher.addMapping("/");
  }
 }
```

或者：

```xml
<web-app>
  <listener>
    <listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
  </listener>

  <context-param>
    <param-name>contextConfigLocation</param-name>
    <param-value>/WEB-INF/root-context.xml</param-value>
  </context-param>

  <servlet>
    <servlet-name>app</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
      <param-name>contextConfigLocation</param-name>
      <param-value>/WEB-INF/web-context.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>app</servlet-name>
    <url-pattern>/app/*</url-pattern>
  </servlet-mapping>
</web-app>
```

### MVC配置

#### 启用Spring MVC

在基于Java的配置中，可以在Servlet的应用上下文配置类中通过`@EnableWebMvc`标注来启用Spring MVC：

```java
@Configuration
@EnableWebMvc
public class WebConfig {
  …
}
```

在基于XML的配置中，可以使用下面配置启用标注驱动的Spring MVC：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:mvc="http://www.springframework.org/schema/mvc"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">

  <mvc:annotation-driven/>

</beans>
```

#### 启用组件扫描

```java
@Configuration
@EnableWebMvc
@ComponentScan("spitter.web")
public class WebConfig {
  …
}
```

#### AOP代理

在某些情况下，您需要在运行时使用AOP代理装饰控制器。例如，如果您选择在控制器上直接使用`@Transactional`注释。在这种情况下，对于控制器而言，我们建议使用基于类的代理。这通常是控制器的默认选择。但是，如果控制器必须实现不是Spring Context回调的接口（例如`InitializingBean`，`*Aware`等），则可能需要显式配置基于类的代理。例如，使用`<tx：annotation-driven />`，您可以更改为`<tx：annotation-driven proxy-target-class =“true”/>`。

### 根上下文配置

```java
package spittr.config;

…

@Configuration
@ComponentScan(
  basePackages={"spitter"},
  excludeFilters={
    @Filter(type=FilterType.ANNOTATION, value=EnableWebMvc.class)
  })
public class RootConfig {
  …
}
```

组件扫描时，过滤掉以`@EnableWebMvc`标注的类。

## 控制器

### 声明

Spring MVC的控制器不需要继承任何类或接口，只需要在控制器的方法上标注上`@RequestMapping`即可。`@RequestMapping`将HTTP请求映射到指定的方法。而`@Controller`标注主要是在自动装配时辅助组件扫描的。

`@RequestMapping`既可只作用在方法上，也可以同时作用在方法和类上：

```java
@Controller
@RequestMapping("/foo")
public class FooController {
  @RequestMapping("/bar")
  public String bar(Model model) {
    model.addAttribute("name", "Mary");
    return "/bar.ftl";
  }
}
```

上面代码对`/foo/bar`的请求，将交由`FooController.bar`方法处理。

控制器方法返回值默认是视图的名称，`DispatcherServlet`会要求视图解析器将这个逻辑视图名称解析为实际的视图。

如果希望返回内容本身，而不是视图名称，则需要在方法上加上`@ResponseBody`。`@ResponseBody`用于将控制器的方法返回值，通过相应的`HttpMessageConverter`转换为指定格式后，写入`Response`对象的body。默认情况下，如果返回值是字符串类型，则直接返回这个字符串；否则，默认使用Jackson将返回值序列化为JSON字符串后输出。

如果需要的是一个RESTful风格的控制器，则需要使用`@RestController`标注，它相当于`@Controller`+`@ResponseBody`，用于返回JSON格式数据。

### 请求映射

`@RequestMapping`的属性：

- value：请求的URL路径，支持URL模板、正则表达式。`value`属性能够接受一个`String`类型的数组：

  ```java
  @Controller
  @RequestMapping({"/", "/home"})
  public class HomeController {
    …
  }
  ```

- method：HTTP请求方法，有GET、POST等；

- consumes：接受的媒体类型。对应HTTP的`Content-Type`；

- produces：响应的媒体类型。对应HTTP的`Accept`；

- params：请求参数；

- headers：请求的HTTP头。

#### 指定HTTP方法

请求的HTTP方法可以通过`@RequestMapping`标注的`method`属性来指定，也可以使用如下便捷的标注来指定：

- `@GetMapping`
- `@PostMapping`
- `@PutMapping`
- `@DeleteMapping`
- `@PatchMapping`

```java
@RestController
@RequestMapping("/persons")
class PersonController {
  @GetMapping("/{id}")
  public Person getPerson(@PathVariable Long id) {
    // ...
  }

  @PostMapping
  @ResponseStatus(HttpStatus.CREATED)
  public void add(@RequestBody Person person) {
    // ...
  }
}
```

#### URL模式

##### 路径参数

```java
@RequestMapping(value="/get/{id}.json")
public @ResponseBody User getById(@PathVariable("id") Long id) {
  …
}
```

则访问路径是`/get/1.json`，时将调用`getById`方法，并且参数`id`的值为`1`。

> 标注中如果只设置一个`value`参数时，可以省略`value=`部分。例如：`@RequestMapping("/get/{id}.json")`。

同一个处理器方法允许有任意多个参数带有`@PathVariable`标注，并且可以在类和方法级别声明URI变量：

```java
@Controller
@RequestMapping("/owners/{ownerId}")
public class OwnerController {
  @GetMapping("/pets/{petId}")
  public Pet findPet(@PathVariable Long ownerId, @PathVariable Long petId) {
    // ...
  }
}
```

如果URI变量名称与方法参数相同，并且您的代码使用调试信息或Java 8上的`-parameters`编译器标志进行编译，则`@PathVariable`可以省略`value`属性。

URI变量自动转换为适当的类型，如果转换失败将引发`TypeMismatchException`。默认情况下支持简单类型（int，long，Date等），您可以注册对任何其他数据类型的支持。请参见“类型转换”和“数据绑定”。

##### Ant路径表达式

`*`匹配任意多个字符（除了路径分隔符”/“），`**`匹配任意多个字符（包括路径分隔符），`?`匹配单个字符。

```java
@RequestMapping("/user/*.html")  //匹配：/user/1.html、/user/abc.html，但不匹配：/user/add/1.html
@RequestMapping("/**/1.html")  //匹配：/1.html、/user/1.html、/user/add/1.html
@RequestMapping("/user/?.html")  //匹配：/user/1.html，但不匹配：/user/abc.html
```

如果一个请求有多个`@RequestMapping`能够匹配，则更具体的匹配优先。另外，有通配符的优先级低于无通配符的，有`**`的低于有`*`的。默认映射模式（`/**`）总是具有最低优先级。

##### 正则表达式

语法`{varName：regex}`声明一个URI变量，它的值必须匹配正则表达式`regex`：

```java
@GetMapping("/{name:[a-z-]+}-{version:\\d\\.\\d\\.\\d}{ext:\\.[a-z]+}")
public void handle(@PathVariable String version, @PathVariable String ext) {
  // ...
}
```

上面的正则表达式可以匹配类似于`/spring-web-3.0.5 .jar`的URL。

##### 占位符

URI路径模式还可以嵌入`$ {…}`占位符，这些占位符在启动时通过`PropertyPlaceHolderConfigurer`来解析本地、系统、环境和其他属性源。例如，您可以使用它来根据某些外部配置参数化基本URL：

```java
@RequestMapping("/${query.all}.json")
```

### 接受请求的输入

Spring MVC提供了下面几种方式将客户端的数据传送到控制器的处理器方法中：

- 查询参数（Query Parameter）：面向操作（控制）。
- 表单参数（Form Parameter）：面向数据。
- 路径变量（Path Variable）：面向资源，详见“路径参数”。

#### 查询参数

下面的处理器方法将匹配：`GET: /spittles?max=238900&count=50`

```java
@Controller
@RequestMapping("/spittles")
public class SpittleController {
  private static final String MAX_LONG_AS_STRING = "9223372036854775807";
  …
  @RequestMapping(method=RequestMethod.GET)
  public List<Spittle> spittles(
      @RequestParam(value="max", defaultValue=MAX_LONG_AS_STRING) long max,
      @RequestParam(value="count", defaultValue="20") int count) {
    return spittleRepository.findSpittles(max, count);
  }
  …
}
```

注意：`@RequestParam`的`value`和`defaultValue`属性都是`String`类型，但是当绑定到方法的参数时，会自动转换为相应类型。

#### 表单参数

控制器代码：

```java
@Controller
@RequestMapping("/spitter")
public class SpitterController {
  private SpitterRepository spitterRepository;

  @Autowired
  public SpitterController(SpitterRepository spitterRepository) {
    this.spitterRepository = spitterRepository;
  }
  
  //显示注册表单
  @RequestMapping(value="/register", method=GET)
  public String showRegistrationForm() {
    return "registerForm";
  }
  
  //处理注册表单
  @RequestMapping(value="/register", method=POST)
  public String processRegistration(Spitter spitter) {
    spitterRepository.save(spitter);
    return "redirect:/spitter/" + spitter.getUsername();
  }
  
  //显示个人信息
  @RequestMapping(value="/{username}", method=GET)
  public String showSpitterProfile(@PathVariable String username, Model model) {
    Spitter spitter = spitterRepository.findByUsername(username);
    model.addAttribute(spitter);
    return "profile";
  }
}
```

> `processRegistration`方法的`spitter`参数的属性将会使用POST请求中同名的参数进行填充。
>
> 在处理POST类型的请求时，在请求处理完成后，最好进行一下重定向，这样浏览器的刷新就不会重复提交表单了。
>
> 本例假定使用`InternalResourceViewResolver`视图解析器，它会将以`redirect:`为前缀的视图格式解析为重定向到指定视图。
>
> `InternalResourceViewResolver`视图解析器还会识别`forward:`前缀的视图格式，它将解析为将请求转发到指定视图。

registerForm.jsp：

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ page session="false" %>
<html>
  <head>
    <title>Spitter</title>
    <link rel="stylesheet" type="text/css" 
          href="<c:url value="/resources/style.css" />" >
  </head>
  <body>
    <h1>Register</h1>

    <form method="POST">
      First Name: <input type="text" name="firstName" /><br/>
      Last Name: <input type="text" name="lastName" /><br/>
      Email: <input type="email" name="email" /><br/>
      Username: <input type="text" name="username" /><br/>
      Password: <input type="password" name="password" /><br/>
      <input type="submit" value="Register" />
    </form>
  </body>
</html>
```

这里`<form>`元素中并没有设置`action`属性。在这种情况下，当表单提交时，它会提交到与展现当前视图时相同的URL路径上，本例中就是`/spitter/register`。

profile.jsp：

```jsp
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ page session="false" %>
<html>
  <head>
    <title>Spitter</title>
    <link rel="stylesheet" type="text/css" href="<c:url value="/resources/style.css" />" >
  </head>
  <body>
    <h1>Your Profile</h1>
    <c:out value="${spitter.username}" /><br/>
    <c:out value="${spitter.firstName}" /> <c:out value="${spitter.lastName}" /><br/>
    <c:out value="${spitter.email}" />
  </body>
</html>
```

##### 校验表单

从Spring 3.0开始，Spring MVC提供了对Java Validation API的支持。在Spring MVC中使用Java Validation API，并不需要什么额外的配置，只要保证在类路径下包含Java Validation API的实现（例如Hibernate Validator）。

Spitter.java：

```java
public class Spitter {
  private Long id;
  
  @NotNull
  @Size(min=5, max=16)
  private String username;

  @NotNull
  @Size(min=5, max=25)
  private String password;
  
  @NotNull
  @Size(min=2, max=30)
  private String firstName;

  @NotNull
  @Size(min=2, max=30)
  private String lastName;
  
  @NotNull
  @Email
  private String email;
  
  …
}
```

我们已经为`Spitter`添加了校验标注，接下来需要修改`processRegistration`方法来应用校验功能：

```java
@RequestMapping(value="/register", method=POST)
public String processRegistration(
  @Valid Spitter spitter, 
  Errors errors) {
  if (errors.hasErrors()) {
    return "registerForm";
  }

  spitterRepository.save(spitter);
  return "redirect:/spitter/" + spitter.getUsername();
}
```

`spitter`参数添加了`@Valid`标注，这会告知Spring，需要确保这个对象满足校验限制。

在`Spitter`类的属性上添加校验限制并不能阻止表单提交，不管校验有没有通过，`processRegistration`方法都会被调用。因此，我们要在`processRegistration`方法中处理校验的错误。

如果有校验出现错误，那么这些错误可以通过`Errors`对象进行访问。注意，`Errors`参数必须紧跟在带有`@Valid`标注的参数之后。

> 校验的更详细用法，请参见“SpringCore”的“校验”。

### 数据绑定

### 异常

### 控制器通知

## 模型

### 传递模型数据到视图中

Spring提供了多种方式将模型数据传递到视图中。

#### 通过Model参数

```java
@RequestMapping(method=RequestMethod.GET)
public String spittles(Model model) {
  model.addAttribute("spittleList", spittleRepository.findSpittles(Long.MAX_VALUE, 20));
  return "spittles";
}
```

这样，在视图中就可以通过`${spittles.属性名}`方式访问这些模型数据。

> 当视图是JSP时，模型数据实际上会作为请求属性放在请求（request）之中。
>
> `Model`参数的位置可任意。

#### 通过Map参数

如果你希望使用非Spring类型的话，可以用`java.util.Map`来代替`Model`：

```java
@RequestMapping(method=RequestMethod.GET)
public String spittles(Map model) {
  model.put("spittleList", spittleRepository.findSpittles(Long.MAX_VALUE, 20));
  return "spittles";
}
```

> `Map`参数的位置可任意。

#### 通过返回值

当处理器方法的返回值是除了`String`类型之外的对象时，该返回值将不会被当作是视图的逻辑名称，而是会被放到模型中：

```java
@Controller
@RequestMapping("/spittles")
public class SpittleController {
  …
  @RequestMapping(method=RequestMethod.GET)
  public List<Spittle> spittles() {
    return spittleRepository.findSpittles(Long.MAX_VALUE, 20);
  }
}
```

这里返回值是一个`List`对象，将被放到模型中，并且它在模型中的键名会根据其类型推断得出，本例中就是`spittleList`。而逻辑视图的名称将会根据请求路径推断得出，本例中就是`spittles`

## 视图

### 视图控制器

### 视图解析器

控制器只提供了逻辑视图名，而Spring视图解析器则负责根据逻辑视图名来确定使用哪一个视图实现来渲染模型。

Spring MVC定义了一个名为`ViewResolver`的接口，它大致如下：

```java
public interface ViewResolver {
  View resolveViewName(String viewName, Locale locale) throw Exception;
}
```

当给`resolveViewName`方法传递一个视图名和语言环境对象时，它会返回一个`View`实例。`View`也是一个接口：

```java
public interface View {
  String getContentType();
  void render(Map<String, ?>model, HttpServletRequest request, HttpServletResponse response) throw Exception;
}
```

`View`接口的任务就是接受模型以及Servlet的请求和响应对象，并将输出结果渲染到响应对象中。

当需要与具体视图技术整合时，我们实际上只需要实现上述两个接口。但是，Spring MVC已经为我们提供了许多内置的实现：

| 视图解析器                     | 说明                                                         |
| ------------------------------ | ------------------------------------------------------------ |
| BeanNameViewResolver           | 将视图解析为Spring应用上下文中的Bean，其中Bean的ID与视图名字相同。 |
| ContentNegotiatingViewResolver | 根据内容类型来解析视图，委托给另一个能够产生对应内容类型的视图解析器。 |
| FreeMarkerViewResolver         | 将视图解析为FreeMarker模板。                                 |
| InternalResourceViewResolver   | 将视图解析为Web应用的内部资源（一般为JSP）。                 |
| JasperReportsViewResolver      | 将视图解析为JasperReports定义。                              |
| ResourceBundleViewResolver     | 将视图解析为资源Bundle（一般为属性文件）。                   |
| TilesViewResolver              | 将视图解析为Apache Tile定义，其中tile ID与视图名相同。       |
| UrlBasedViewResolver           | 直接根据视图的名称解析视图，视图的名称会匹配一个物理视图的定义。 |
| VelocityLayoutViewResolver     | 将视图解析为Velocity布局，从不同的Velocity模板中组合页面。   |
| VelocityViewResolver           | 将视图解析为Velocity模板。                                   |
| XmlViewResolver                | 将视图解析为特定XML文件中的bean定义。类似于BeanNameViewResolver。 |
| XsltViewResolver               | 将视图解析为XSLT转换后的结果。                               |

### 视图技术

#### JSP

##### 配置视图解析器

基于Java的配置：

```java
@Configuration
@EnableWebMvc
@ComponentScan("spittr.web")
public class WebConfig extends WebMvcConfigurerAdapter {
  @Bean
  public ViewResolver viewResolver() {
    InternalResourceViewResolver resolver = new InternalResourceViewResolver();
    resolver.setPrefix("/WEB-INF/views/");
    resolver.setSuffix(".jsp");
    return resolver;
  }
  …
}
```

或者基于XML的配置：

```xml
<bean id="viewResolver"
      class="org.springframework.web.servlet.view.InternalResourceViewResolver"
      p:prefix="/WEB-INF/views/"
      p:suffix=".jsp" />
```

解析示例：

- 逻辑视图`home`将会解析为`/WEB-INF/views/home.jsp`。
  ![JSP视图解析图示](SpringWeb/JSP视图解析图示.png)
- 逻辑视图`productList`将会解析为`/WEB-INF/views/productList.jsp`。
- 逻辑视图`books/detail`将会解析为`/WEB-INF/views/books/detail.jsp`。

上面的配置最终会将逻辑视图名解析为`InternalResourceView`实例，这个实例会引用JSP文件。但是如果这些JSP使用JSTL标签来处理格式化和信息的话，那么我们会希望`InternalResourceViewResolver`将视图解析为`JstlView`。要让`InternalResourceViewResolver`将视图解析为`JstlView`，而不是`InternalResourceView`，只需要多设置个`viewClass`属性：

```java
@Bean
public ViewResolver viewResolver() {
  InternalResourceViewResolver resolver = new InternalResourceViewResolver();
  resolver.setPrefix("/WEB-INF/views/");
  resolver.setSuffix(".jsp");
  resolver.setViewClass(org.springframework.web.servlet.view.JstlView.class);
  return resolver;
}
```

或者：

```xml
<bean id="viewResolver"
      class="org.springframework.web.servlet.view.InternalResourceViewResolver"
      p:prefix="/WEB-INF/views/"
      p:suffix=".jsp"
      p:viewClass="org.springframework.web.servlet.view.JstlView" />
```

#### Thymeleaf

JSP作为视图技术是有很大缺点的：

- 缺乏良好的格式：JSP模板虽然采用HTML的形式，但又掺杂上了各种JSP标签库的标签，使其变得很混乱，在浏览器上展示的效果很难接近模板最终所渲染出来的效果。
- JSP规范与Servlet规范紧密耦合，只能在基于Servlet的Web应用之中使用。

Thymeleaf模板是原生的，不依赖于标签库。它通过自定义的命名空间，为标准的HTML标签集合添加Thymeleaf属性，从而能在接受原始HTML的地方进行编辑和渲染。它也没有与Servlet规范耦合，因此可以进入JSP无法涉足的领域。

##### 配置

为了在Spring中使用Thymeleaf，需要配置三个Bean：

- ThymeleafViewResolver：将逻辑视图名称解析为Thymeleaf模板；（Thymeleaf视图解析器不是Spring内置的）
- SpringTemplateEngine：处理模板并渲染结果；
- TemplateResolver：加载Thymeleaf模板。

```java
@Configuration
@EnableWebMvc
@ComponentScan("spittr.web")
public class WebConfig extends WebMvcConfigurerAdapter {
  @Bean
  public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
    ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
    viewResolver.setTemplateEngine(templateEngine);
    return viewResolver;
  }
  @Bean
  public SpringTemplateEngine templateEngine(TemplateResolver templateResolver) {
    SpringTemplateEngine templateEngine = new SpringTemplateEngine();
    templateEngine.setTemplateResolver(templateResolver);
    return templateEngine;
  }

  @Bean
  public TemplateResolver templateResolver() {
    TemplateResolver templateResolver = new ServletContextTemplateResolver();
    templateResolver.setPrefix("/WEB-INF/templates/");
    templateResolver.setSuffix(".html");
    templateResolver.setTemplateMode("HTML5");
    return templateResolver;
  }
    
  …
}
```

或者：

```xml
<bean id="viewResolver"
      class="org.thymeleaf.spring3.view.ThymeleafViewResolver"
      p:templateEngine-ref="templateEngine" />
<bean id="templateEngine"
      class="org.thymeleaf.spring3.SpringTemplateEngine"
      p:templateResolver-ref="templateResolver" />
<bean id="templateResolver"
      class="org.thymeleaf.templateresolver.ServletContextTemplateResolver"
      p:prefix="/WEB-INF/templates"
      p:suffix=".html"
      p:templateMode="HTML5" />
```



### 静态资源

### 主题

## *内容类型

默认情况下，Spring MVC执行`.*`后缀模式匹配，以便映射到`/person`的控制器也隐式映射到`/person.*`。这种机制主要是为了可以使用文件扩展名来指明响应的请求内容类型（而不是`Accept`头）。但是，使用文件扩展名来指定请求内容类型已经证明存在多种问题，可能会导致歧义。因此，使用`Accept`头应该是首选。

要完全禁用文件扩展名，必须同时设置以下两项：

- useSuffixPatternMatching(false)

- favorPathExtension(false)

基于Java的配置：

```java
@Configuration
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
  @Override
  public void configurePathMatch(PathMatchConfigurer configurer) {
    configurer.setUseSuffixPatternMatch(false);
    …
  }

  @Bean
  public UrlPathHelper urlPathHelper() {
    //...
  }

  @Bean
  public PathMatcher antPathMatcher() {
    //...
  }
}
```

基于XML配置：

```xml
<mvc:annotation-driven>
  <mvc:path-matching
    suffix-pattern="false"
    …/>
</mvc:annotation-driven>

<bean id="pathHelper" class="org.example.app.MyPathHelper"/>
<bean id="pathMatcher" class="org.example.app.MyPathMatcher"/>
```

基于URL的内容协商（content negotiation）仍然有用（例如，在浏览器中键入URL时）。为此，我们建议使用基于查询参数的策略来避免文件扩展名带来的大多数问题。或者，如果必须使用文件扩展名，请考虑通过`ContentNegotiationConfigurer`的`mediaTypes`属性将它们限制为显式注册的扩展名列表。

## 注册其他Servlet、Filter和Listener

### 基于Java的配置

基于Java的初始化器（Initializer）的一个好处是我们可以定义任意数量的初始化器类。因此，我们如果想往Web容器中注册其他组件，只需要创建一个新的初始化器就可以了。最简单的方式是就是实现Spring的`WebApplicationInitializer`接口。

注册Servlet：

```java
public class MyServletInitializer implements WebApplicationInitializer {
  @Override
  public void onStartup(ServletContext servletContext) throws ServletException {
    ServletRegistration.Dynamic myServlet = servletContext.addServlet("myServlet", MyServlet.class);
    myServlet.addMapping("/custom/**");
  }
}
```

注册Filter：

```java
@Override
public void onStartup(ServletContext servletContext) throws ServletException {
  FilterRegistration.Dynamic filter = servletContext.addFilter("myFilter", MyFilter.class);
  filter.addMappingForUrlPatterns(null, false, "/custom/**");
}
```

注册Filter并将其映射到`DispatcherServlet`，有一个快捷方式，就是重写`AbstractAnnotationConfigDispatcherServletInitializer`类的`getServletFilter`方法：

```java
@Override
protected Filter[] getServletFilters() {
  return new Filter[] {new MyFilter()};
}
```

`getServletFilter`方法返回的所有Filter都会映射到`DispatcherServlet`上。

### 基于web.xml的配置

参见“传统的web.xml配置。

## 处理Multipart数据

### 配置multipart解析器

`DispatcherServlet`并没有实现任何解析multipart请求数据的功能，它将该任务委托给了Spring中的`MultipartResolver`策略接口的实现。从Spring 3.1开始，Spring内置了两个`MultipartResolver`实现供我们选择：

- `CommonsMultipartResolver`：使用Jakarta Commons FileUpload解析multipart请求。
- `StandardServletMultipartResolver`：依赖于Servlet 3对multipart请求的支持（始于Spring 3.1）。

#### 使用Servlet 3.0解析multipart请求

首先，配置一个`StandardServletMultipartResolver` Bean：

```java
@Bean
public MultipartResolver multipartResolver() throws IOException {
  return new StandardServletMultipartResolver();
}
```

然后，有关multipart的配置（例如上传文件大小限制、临时写入目录的位置等）则在`DispatcherServlet`中配置。

如果初始化器实现了`WebApplicationInitializer`，则：

```java
public class MyWebApplicationInitializer implements WebApplicationInitializer {
  @Override
  public void onStartup(ServletContext servletCxt) {
    …
    DispatcherServlet ds = new DispatcherServlet();
    ServletRegistration.Dynamic registration = servletCxt.addServlet("app", ds);
    registration.setLoadOnStartup(1);
    registration.addMapping("/app/*");
    //设置临时写入路径
		registration.setMultipartConfig(new MultipartConfigElement("/tmp/uploads"));
  }
}
```

如果初始化器继承了`AnnotationConfigDispatcherServletInitializer`，则：

```java
@Override
protected void customizeRegistration(Dynamic registration) {
  registration.setMultipartConfig(new MultipartConfigElement("/tmp/uploads"));
}
```

`MultipartConfigElement`还有一个构造器，能接受如下参数：

- 临时写入目录；
- 上传文件的最大容量（以字节为单位），默认是没有限制的。
- 整个multipart请求的最大容量（以字节为单位），默认是没有限制的。（对part的个数，以及每个part的大小则不关心）。
- 在上传过程中，如果文件大小达到一个指定最大容量（以字节为单位）时，将会写入到临时文件路径中。默认值为0，即所有上传的文件都会写入到磁盘中。

```java
@Override
protected void customizeRegistration(Dynamic registration) {
  registration.setMultipartConfig(new MultipartConfigElement("/tmp/uploads", 2097152, 4194304, 0));
}
```

另外，也可以在web.xml中配置：

```xml
<servlet>
  <servlet-name>app</servlet-name>
  <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
  <load-on-startup>1</load-on-startup>
  <multipart-config>
  	<location>/tmp/uploads</location><!-- 必配选项 -->
    <max-file-size>2097152</max-file-size>
    <max-request-size>4194304</max-request-size>
  </multipart-config>
</servlet>
```

#### 配置Jakarta Commons FileUpload multipart解析器



## 语言环境

## 拦截器

## CORS

## 异步请求

## HTTP缓存

## REST客户端

## WebSockets

# Spring WebFlux