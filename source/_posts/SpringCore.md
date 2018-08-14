---
title: SpringCore
date: 2018-06-01 21:57:20
tags:
---

# 依赖注入

`@Resource`标注是属于Java EE标准的，它默认按照名称进行装配，名称可以通过`name`属性进行指定。如果没有指定`name`属性，当标注写在字段上时，就默认取字段名进行查找；如果标注写在setter方法上，就默认取属性名进行装配。当找不到与名称匹配的bean时，才按照类型进行装配。但需要注意的是，`name`一旦指定，就只会按照名称进行装配。例如：

```java
@Resource(name="userRepository")
private UserRepository userRepository;
```

`@Autowired`标注属于Spring，默认按类型装配。默认情况下，要求注入对象必须存在，如果要允许`null`值，则要设置它的`required`属性为`false`，例如`@Autowired(required=false)`。可以借助`@Qualifier`标注实现按名称装配：

```java
@Autowired
@Qualifier("userRepository")
private UserRepository userRepository;
```

