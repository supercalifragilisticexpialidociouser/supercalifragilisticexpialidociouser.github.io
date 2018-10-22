---
title: SpringWeb
date: 2018-06-01 21:57:58
tags: [5.1.1]
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

## DispatcherServlet

### 配置DispatcherServlet

传统配置DispatcherServlet的方式是在web.xml文件中进行配置。借助于Servlet 3规范和Spring 3.1的功能增强，现在更推荐在Java中配置DispatcherServlet。

#### 传统的web.xml配置

web.xml：

```xml
<web-app>
  <!-- 加载后端的中间层和数据层组件的应用上下文 -->
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
    <!-- 默认加载 /WEB-INF/Servlet名称-servlet.xml （这里是 /WEB-INF/app-servlet.xml）的Spring配置文件 -->
    <load-on-startup>1</load-on-startup>
  </servlet>

  <servlet-mapping>
    <servlet-name>app</servlet-name>
    <url-pattern>/app/*</url-pattern>
  </servlet-mapping>
</web-app>
```

一个web.xml可以配置多个`DispatcherServlet`，通过不同的`<servlet-mapping>`配置，让每个`DispatcherServlet`处理不同的请求。

#### 使用Java配置

### 上下文的层次结构

在Spring MVC中，`DispatcherServlet`加载的Web层应用上下文，将作为`ContextLoaderListener`加载的后端应用上下文的子上下文。

## 过滤器

## 控制器

### 声明

Spring MVC的控制器不需要继承任何类或接口，只需要标注上`@Controller`即可。并且，使用`@RequestMapping`将HTTP请求映射到指定的方法。

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

则对`/foo/bar`的请求，将交由`FooController.bar`方法处理。

控制器方法返回值默认是视图的名称。如果希望返回内容本身，而不是视图名称，则需要在方法上加上`@ResponseBody`。`@ResponseBody`用于将控制器的方法返回值，通过相应的`HttpMessageConverter`转换为指定格式后，写入`Response`对象的body。默认情况下，如果返回值是字符串类型，则直接返回这个字符串；否则，默认使用Jackson将返回值序列化为JSON字符串后输出。

如果需要的是一个RESTful风格的控制器，则需要使用`@RestController`标注，它相当于`@Controller`+`@ResponseBody`，用于返回JSON格式数据。

### 请求映射

`@RequestMapping`的属性：

- value：请求的URL路径，支持URL模板、正则表达式；
- method：HTTP请求方法，有GET、POST等；
- consumes：接受的媒体类型。对应HTTP的`Content-Type`；
- produces：响应的媒体类型。对应HTTP的`Accept`；
- params：请求参数；
- headers：请求的HTTP头。

另外，根据请求方法的不同，还可以使用`@GetMapping`、`@PostMapping`、`@PutMapping`、`@DeleteMapping`和`@PatchMapping`等简便标注代替。

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

##### Ant路径表达式

`*`匹配任意多个字符（除了路径分隔符”/“），`**`匹配任意路径，`?`匹配单个字符。

```java
@RequestMapping("/user/*.html")  //匹配：/user/1.html、/user/abc.html，但不匹配：/user/add/1.html
@RequestMapping("/**/1.html")  //匹配：/1.html、/user/1.html、/user/add/1.html
@RequestMapping("/user/?.html")  //匹配：/user/1.html，但不匹配：/user/abc.html
```

如果一个请求有多个`@RequestMapping`能够匹配，则更具体的匹配优先。另外，有通配符的低于无通配符的，有`**`的低于有`*`的。

##### 插值

在URL映射中，可以使用`${…}`的插值（SpEL），来获取系统配置或环境变量：

```java
@RequestMapping("/${query.all}.json")
```

#### HTTP HEAD、OPTIONS

### 处理器方法

# Spring WebFlux