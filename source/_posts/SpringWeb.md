---
title: SpringWeb
date: 2018-06-01 21:57:58
tags:
---

# Spring MVC

## DispatcherServlet

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