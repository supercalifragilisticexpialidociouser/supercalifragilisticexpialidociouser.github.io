---
title: SpringTesting
date: 2018-10-08 15:24:42
tags:
---



```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes=FooConfig.class)
public class CDPlayerTest {
}
```

`SpringJUnit4ClassRunner`在测试开始时自动创建Spring的应用上下文。

标注`@ContextConfiguration`告诉Spring应用上下文去哪里加载配置。