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

```java
import net.sf.ehcache.CacheManager;
…
@Configuration
@EnableCaching
public class CachingConfig {
  @Bean
  public EhCacheCacheManager cacheManager(CacheManager cm) {
  	return new EhCacheCacheManager(cm);
  }
  @Bean
  public EhCacheManagerFactoryBean ehcache() {
  EhCacheManagerFactoryBean ehCacheFactoryBean =   new EhCacheManagerFactoryBean();
  ehCacheFactoryBean.setConfigLocation(new ClassPathResource("com/habuma/spittr/cache/ehcache.xml"));
  return ehCacheFactoryBean;
  }
}
```

注意：Spring和EhCache都定义有`CacheManager`类型。这里，将EhCache的`CacheManager`注入到Spring的`EhCacheCacheManager`（Spring的`CacheManager`实现）之中。

Spring提供了`EhCacheManagerFactoryBean`来生成EhCache的`CacheManager`，因此，也要把它注册成一个Bean。

EhCache自身的配置定义在一个XML文件中，这里是`ehcache.xml`：

```xml
<ehcache>
  <cache name="spittleCache"
         maxBytesLocalHeap="50m"
         timeToLiveSeconds="100">
  </cache>
</ehcache>
```

## 配置Redis缓存

Spring Data Redis提供了`RedisCacheManager`，它会与一个Redis服务器协作，并通过`RedisTemplate`将缓存条目存储到Redis中。

```java
@Configuration
@EnableCaching
public class CachingConfig {
  @Bean
  public CacheManager cacheManager(RedisTemplate redisTemplate) {
    return new RedisCacheManager(redisTemplate);
  }
  @Bean
  public JedisConnectionFactory redisConnectionFactory() {
    JedisConnectionFactory jedisConnectionFactory =
      new JedisConnectionFactory();
    jedisConnectionFactory.afterPropertiesSet();
    return jedisConnectionFactory;
  }
  @Bean
  public RedisTemplate<String, String> redisTemplate(RedisConnectionFactory redisCF) {
    RedisTemplate<String, String> redisTemplate = new RedisTemplate<String, String>();
    redisTemplate.setConnectionFactory(redisCF);
    redisTemplate.afterPropertiesSet();
    return redisTemplate;
  }
}
```

## 配置多个缓存管理器

可以使用`CompositeCacheManager`来配置多个缓存管理器，它会迭代这些缓存管理器，以查找之前所缓存的值。

```java
@Bean
public CacheManager cacheManager(
    net.sf.ehcache.CacheManager cm,
    javax.cache.CacheManager jcm) {
  CompositeCacheManager cacheManager = new CompositeCacheManager();
  List<CacheManager> managers = new ArrayList<CacheManager>();
  managers.add(new JCacheCacheManager(jcm));
  managers.add(new EhCacheCacheManager(cm));
  managers.add(new RedisCacheManager(redisTemplate()));
  cacheManager.setCacheManagers(managers);
  return cacheManager;
}
```

# 为方法添加标注以支持缓存

Spring提供的缓存标注：

| 标注        | 说明                                                         |
| ----------- | ------------------------------------------------------------ |
| @Cacheable  | 表明Spring在调用方法之前，首先应该在缓存中查找方法的返回值。如果这个值能够找到，就会返回缓存的值。否则，这个方法就会被调用，返回值会放到缓存之中。 |
| @CachePut   | 表示Spring应该将方法的返回值放到缓存中。在方法的调用前并不会检查缓存，方法始终都会被调用。 |
| @CacheEvict | 表示Spring应该在缓存中清除一个或多个条目。                   |
| @Caching    | 这是一个分组标注，能够同时应用多个其他的缓存标注。           |

## 填充缓存

`@Cacheable`和`@CachePut`都可填充缓存。它们的一些共有属性：

| 属性      | 类型     | 说明                                                         |
| --------- | -------- | ------------------------------------------------------------ |
| value     | String[] | 要使用的缓存名称。                                           |
| condition | String   | SpEL表达式，如果得到的值是`false`，就不会将缓存应用到方法调用上。 |
| key       | String   | SpEL表达式，用来计算自定义的缓存key。                        |
| unless    | String   | SpEL表达式，如果得到的值是`true`，返回值不会放到缓存之中。   |

```java
@Cacheable("spittleCache")  //缓存名称是spittleCache
public Spittle findOne(long id) {
  try {
    return jdbcTemplate.queryForObject(
      SELECT_SPITTLE_BY_ID,
      new SpittleRowMapper(),
      id);
  } catch (EmptyResultDataAccessException e) {
    return null;
  }
}
```

上面的程序，将`@Cacheable`放在实现类的方法上，这样，只有该实现类的方法才应用缓存。如果将`@Cacheable`放在接口的方法声明上，则所有实现该接口的类的重写方法都会应用缓存。

```java
@Cacheable("spittleCache")
Spittle findOne(long id);
```

### 自定义缓存key

默认的缓存key是基于方法的参数来确定的。上面程序中，缓存的key是传递到`findOne`方法的`id`参数。

可以使用`@Cacheable`和`@CachePut`标注的`key`属性来自定义缓存key：

```java
@CachePut(value="spittleCache", key="#result.id")
Spittle save(Spittle spittle);
```

Spring提供了多个用来定义缓存规则的SpEL扩展：

| 表达式              | 说明                                                         |
| ------------------- | ------------------------------------------------------------ |
| `#root.args`        | 传递给缓存方法的参数，形式为数组。                           |
| `#root.caches`      | 该方法执行时所对应的缓存，形式为数组。                       |
| `#root.target`      | 目标对象。                                                   |
| `#root.targetClass` | 目标对象的类，是`#root.target.class`的简写形式。             |
| `#root.method`      | 缓存方法。                                                   |
| `#root.methodName`  | 缓存方法的名称，是`#root.method.name`的简写形式。            |
| `#result`           | 方法调用的返回值（不能用在`@Cacheable`标注上）。             |
| `#参数名或索引`     | 任意的方法参数名（如`#argName`）或参数索引（如`#a0`或`#p0`）。 |

###  条件化缓存

`@Cacheable`和`@CachePut`提供了两个属性用以实现条件化缓存：`unless`和`condition`。

`unless`属性只能阻止将对象放进缓存，但是在这个方法调用的时候，依然会去缓存中进行查找，如果找到匹配的值，就会返回找到的值。

`condition`的表达式计算结果为`false`，则在这个方法调用的过程中，缓存是被禁用的。就是说，不会去缓存进行查找，同时返回值也不会放进缓存中。

```java
@Cacheable(value="spittleCache"
           unless="#result.message.contains('NoCache')"
           condition="#id >= 10")
Spittle findOne(long id);
```

另外，`unless`属性只有在缓存方法有返回值时才开始发挥作用。而`condition`肩负着在方法上禁用缓存的任务，因此它不能等到方法返回时再确定是否该关闭缓存。这意味着它的表达式必须要在进入方法时进行计算，所以我们不能通过`#result`引用返回值。

## 移除缓存条目

`@CacheEvict`并不会往缓存中添加任何东西。相反，如果带有`@CacheEvict`标注的方法被调用的话，那么会有一个或更多的条目会在缓存中移除。

> 注意：`@CacheEvict`可以应用在返回值为`void`的方法上，而`@Cacheable`和`@CachePut`只能用在返回值不是`void`的方法上。

```java
@CacheEvict("spittleCache")
void remove(long spittleId);
```

被删除条目的key与传递进来的`spittleId`参数的值相同。

`@CacheEvict`的属性：

| 属性             | 类型     | 说明                                                         |
| ---------------- | -------- | ------------------------------------------------------------ |
| value            | String[] | 要使用的缓存名称。                                           |
| key              | String   | SpEL表达式，用来计算自定义的缓存key。                        |
| condition        | String   | SpEL表达式，如果得到的值是`false`，则缓存不会应用到方法调用上。 |
| allEntries       | boolean  | 如果为`true`，特定缓存的所有条目都会被移除掉。               |
| beforeInvocation | boolean  | 如果为`true`，则在方法调用之前移除条目。如果为`false`（默认值），则在方法成功调用之后再移除条目。 |

# 使用XML声明缓存

如果你不希望在自己的源码中添加Spring的标注，或者你需要在没有源码的Bean上应用缓存功能。这时就需要使用XML的方式来声明缓存。

| 元素                        | 说明                                                         |
| --------------------------- | ------------------------------------------------------------ |
| `<cache:annotation-driven>` | 启用标注驱动的缓存。等同于Java配置中的`@EnableCaching`。     |
| `<cache:advice>`            | 定义缓存通知（advice）。结合`<aop:advisor>`，将通知应用到切点上。 |
| `<cache:caching>`           | 在缓存通知中，定义一组特定的缓存规则。                       |
| `<cache:cacheable>`         | 指明某个方法要进行缓存。等同于`@Cacheable`。                 |
| `<cache:cache-put>`         | 指明某个方法要填充缓存，但不会考虑缓存中是否已有配置的值。等同于`@CachePut`。 |
| `<cache:cache-evict>`       | 指明某个方法要从缓存中移除一个或多个条目。等同于`@CacheEvict`。 |

```java
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:cache="http://www.springframework.org/schema/cache"
    xmlns:aop="http://www.springframework.org/schema/aop"
    xsi:schemaLocation="http://www.springframework.org/schema/aop
      http://www.springframework.org/schema/aop/spring-aop.xsd
      http://www.springframework.org/schema/beans
      http://www.springframework.org/schema/beans/spring-beans.xsd
      http://www.springframework.org/schema/cache
      http://www.springframework.org/schema/cache/spring-cache.xsd">
  <aop:config>
  <aop:advisor advice-ref="cacheAdvice"
    pointcut="execution(* com.habuma.spittr.db.SpittleRepository.*(..))"/>
  </aop:config>
  <cache:advice id="cacheAdvice">
    <cache:caching>
      <cache:cacheable cache="spittleCache" method="findRecent" />
      <cache:cacheable cache="spittleCache" method="findOne" />
      <cache:cacheable cache="spittleCache" method="findBySpitterId" />
      <cache:cache-put cache="spittleCache" method="save" key="#result.id" />
      <cache:cache-evict cache="spittleCache" method="remove" />
    </cache:caching>
  </cache:advice>
  <bean id="cacheManager"
        class="org.springframework.cache.concurrent.ConcurrentMapCacheManager" />
</beans>
```

在`<cache:advice>`中可包含任意数量的`<cache:caching>`元素。

另外，如果缓存管理器的ID不是`cacheManager`的话，则还要在`<cache:advice>`中通过`cache-manager`属性显式指定。

如果`<cache:caching>`下的所有`<cache:cacheable>`、`<cache:cache-put>`、`<cache:cache-evict>`元素的`cache`、`condition`、`key`或`method`属性都引用相同的名字，则可以在`<cache:caching>`元素的对应属性上统一指定：

```xml
<cache:advice id="cacheAdvice">
  <cache:caching cache="spittleCache">
    <cache:cacheable method="findRecent" />
    <cache:cacheable method="findOne" />
    <cache:cacheable method="findBySpitterId" />
    <cache:cache-put method="save" key="#result.id" />
    <cache:cache-evict method="remove" />
  </cache:caching>
</cache:advice>
```

此外，`<cache:cacheable>`、`<cache:cache-put>`也有`unless`属性。`<cache:cache-evict>`有`all-entries`和`before-invocation`属性。