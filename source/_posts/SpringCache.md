---
title: SpringCache
date: 2018-11-26 14:06:52
tags: [5.1.2]
---

尽管Spring自身并没有实现缓存解决方案，但是它对缓存功能提供了声明式的支持，能够与多种流行的缓存实现进行集成。

Spring对缓存的支持有两种方式：

- 注解驱动的缓存；
- XML声明的缓存。

# 启用缓存

```java
@Configuration
@EnableCaching
public class CachingConfig {
  …
}
```

或者：

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:cache="http://www.springframework.org/schema/cache"
       xsi:schemaLocation="
                           http://www.springframework.org/schema/beans
                           http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/cache
                           http://www.springframework.org/schema/cache/spring-cache.xsd">
  <cache:annotation-driven />
  …
</beans>
```

# 配置缓存管理器

Spring内置了许多缓存管理器实现：

- SimpleCacheManager
- NoOpCacheManager
- ConcurrentMapCacheManager
- CompositeCacheManager
- EhCacheCacheManager
- JCacheCacheManager：基于JCache（JSR-107）
- RedisCacheManager：来自于Spring Data Redis
- GemfireCacheManager：来自于Spring Data GemFire
- CaffeineCacheManager

## 配置EhCache缓存

## 配置Redis缓存

